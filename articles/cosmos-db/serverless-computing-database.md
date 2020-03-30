---
title: Przetwarzanie bezserwerowych baz danych za pomocą usługi Azure Cosmos DB i usług Azure Functions
description: Dowiedz się, jak usługi Azure Cosmos DB i usługi Azure Functions mogą być używane razem do tworzenia aplikacji obliczeniowych opartych na zdarzeniach bez użycia serwera.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 70545020899b69508a4cedb0fd7cf5495c847104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462449"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Przetwarzanie bezserwerowych baz danych przy użyciu usługi Azure Cosmos DB i usługi Azure Functions

Komputery bezserwerowe to możliwość skupienia się na poszczególnych elementach logiki, które są powtarzalne i bezstanowe. Te elementy nie wymagają zarządzania infrastrukturą i zużywają zasoby tylko przez sekundy lub milisekundy, dla których są uruchamiane. Sednem ruchu obliczeniowego bez serwera są funkcje, które są udostępniane w ekosystemie platformy Azure przez [usługę Azure Functions.](https://azure.microsoft.com/services/functions) Aby dowiedzieć się więcej o innych środowiskach wykonywania bezserwerowych na platformie Azure, zobacz [bezserwerową stronę na platformie Azure.](https://azure.microsoft.com/solutions/serverless/) 

Dzięki natywnej integracji między [usługą Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) i usługi Azure Functions można tworzyć wyzwalacze bazy danych, powiązania wejściowe i powiązania danych wyjściowych bezpośrednio z konta usługi Azure Cosmos DB. Korzystając z usługi Azure Functions i usługi Azure Cosmos DB, można tworzyć i wdrażać aplikacje bezserwerowe oparte na zdarzeniach z małym opóźnieniem dostępu do bogatych danych dla globalnej bazy użytkowników.

## <a name="overview"></a>Omówienie

Usługi Azure Cosmos DB i usługi Azure Functions umożliwiają integrację baz danych i aplikacji bezserwerowych w następujący sposób:

* Utwórz **wyzwalacz usługi Azure Functions sterowany**przez zdarzenia dla usługi Cosmos DB . Ten wyzwalacz opiera się na strumieniach [strumieniowych zmian,](change-feed.md) aby monitorować kontener usługi Azure Cosmos pod kątem zmian. Po wprowadzeniu jakichkolwiek zmian w kontenerze strumień kanału informacyjnego zmian jest wysyłany do wyzwalacza, który wywołuje funkcję platformy Azure.
* Alternatywnie należy powiązać funkcję platformy Azure z kontenerem usługi Azure Cosmos przy użyciu **powiązania wejściowego.** Powiązania wejściowe odczytywać dane z kontenera, gdy funkcja jest wykonywana.
* Powiąż funkcję z kontenerem usługi Azure Cosmos przy użyciu **powiązania danych wyjściowych.** Powiązania wyjściowe zapisują dane do kontenera po zakończeniu funkcji.

> [!NOTE]
> Obecnie wyzwalacz usługi Azure Functions, powiązania wejściowe i powiązania danych wyjściowych dla usługi Cosmos DB są obsługiwane tylko do użytku z interfejsem API SQL. Dla wszystkich innych interfejsów API bazy danych usługi Azure Cosmos, należy uzyskać dostęp do bazy danych z funkcji przy użyciu klienta statycznego dla interfejsu API.


Poniższy diagram ilustruje każdą z tych trzech integracji: 

![Jak integruje się usługa Azure Cosmos DB i usługi Azure Functions](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Wyzwalacz usług Azure Functions, powiązanie danych wejściowych i danych wyjściowych dla usługi Azure Cosmos DB może być używane w następujących kombinacjach:

* Wyzwalacz funkcji platformy Azure dla usługi Cosmos DB może służyć z powiązaniem danych wyjściowych do innego kontenera usługi Azure Cosmos. Po funkcji wykonuje akcję na element w zestawieniu zmian można zapisać go do innego kontenera (zapisywanie go do tego samego kontenera, z którego pochodzi skutecznie utworzyć pętlę cykliczną). Lub można użyć wyzwalacza usługi Azure Functions dla usługi Cosmos DB, aby skutecznie migrować wszystkie zmienione elementy z jednego kontenera do innego kontenera, przy użyciu powiązania danych wyjściowych.
* Powiązania wejściowe i powiązania danych wyjściowych dla usługi Azure Cosmos DB mogą być używane w tej samej funkcji platformy Azure. Działa to dobrze w przypadkach, gdy chcesz znaleźć pewne dane z powiązaniem wejściowym, zmodyfikować je w usłudze Azure Function, a następnie zapisać go w tym samym kontenerze lub innym kontenerze, po modyfikacji.
* Powiązanie danych wejściowych do kontenera usługi Azure Cosmos może być używane w tej samej funkcji co wyzwalacz usługi Azure Functions dla usługi Cosmos DB i może być również używane z powiązaniem danych wyjściowych lub bez niego. Można użyć tej kombinacji, aby zastosować aktualne informacje o wymianie walut (wciągnięte z powiązaniem wejściowym do kontenera wymiany) do źródła nowych zamówień w usłudze koszyka zakupów. Zaktualizowana suma koszyka zakupów, z zastosowaniem bieżącego przeliczania walut, może być zapisywana w trzecim kontenerze przy użyciu powiązania danych wyjściowych.

## <a name="use-cases"></a>Przypadki zastosowań

Poniższe przypadki użycia pokazują kilka sposobów, w jaki można w pełni wykorzystać dane usługi Azure Cosmos DB — łącząc dane z usługą Azure Functions opartą na zdarzeniach.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Przypadek użycia IoT — powiązanie wyzwalania i danych wyjściowych usług Azure Functions dla usługi Cosmos DB

W implementacjach IoT można wywołać funkcję, gdy lampka kontrolna silnika jest wyświetlana w podłączonym samochodzie.

**Realizacja:** Używanie powiązania wyzwalania i danych wyjściowych usługi Azure Functions dla usługi Cosmos DB

1. **Wyzwalacz usługi Azure Functions dla usługi Cosmos DB** służy do wyzwalania zdarzeń związanych z alertami samochodowymi, takich jak lampka kontrolna silnika przychodząca w podłączonym samochodzie.
2. Gdy pojawi się lampka aparatu wyboru, dane czujnika są wysyłane do usługi Azure Cosmos DB.
3. Usługa Azure Cosmos DB tworzy lub aktualizuje nowe dokumenty danych czujnika, a następnie te zmiany są przesyłane strumieniowo do wyzwalacza usługi Azure Functions dla usługi Cosmos DB.
4. Wyzwalacz jest wywoływany przy każdej zmianie danych do zbierania danych czujnika, ponieważ wszystkie zmiany są przesyłane strumieniowo za pośrednictwem źródła danych zmian.
5. Warunek progowy jest używany w funkcji do wysyłania danych czujnika do działu gwarancyjnego.
6. Jeśli temperatura jest również powyżej określonej wartości, alert jest również wysyłany do właściciela.
7. **Powiązanie danych wyjściowych** dla funkcji aktualizuje rekord samochodu w innym kontenerze usługi Azure Cosmos do przechowywania informacji o zdarzeniu aparatu kontroli.

Na poniższej ilustracji przedstawiono kod napisany w witrynie Azure portal dla tego wyzwalacza.

![Tworzenie wyzwalacza usługi Azure Functions dla usługi Cosmos DB w witrynie Azure portal](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Przypadek użycia finansowego — wyzwalacz czasu i powiązanie wejściowe

W implementacjach finansowych można wywołać funkcję, gdy saldo konta bankowego spadnie poniżej określonej kwoty.

**Realizacja:** Wyzwalacz czasomierza z powiązaniem wejściowym usługi Azure Cosmos DB

1. Za pomocą [wyzwalacza czasomierza](../azure-functions/functions-bindings-timer.md)można pobrać informacje o saldzie konta bankowego przechowywane w kontenerze usługi Azure Cosmos w odstępach czasu przy użyciu **powiązania wejściowego.**
2. Jeśli saldo jest poniżej progu niskiego salda ustawionego przez użytkownika, wykonaj akcję z funkcji platformy Azure.
3. Powiązanie danych wyjściowych może być [integracją SendGrid,](../azure-functions/functions-bindings-sendgrid.md) która wysyła wiadomość e-mail z konta usługi na adresy e-mail zidentyfikowane dla każdego konta o niskim saldzie.

Poniższe obrazy pokazują kod w witrynie Azure portal dla tego scenariusza.

![Index.js plik wyzwalacza czasomierza dla scenariusza finansowego](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Plik Run.csx dla wyzwalacza czasomierza dla scenariusza finansowego](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Przypadek użycia gier — powiązanie wyzwalania i danych wyjściowych usługi Azure Functions dla usługi Cosmos DB 

W grach po utworzeniu nowego użytkownika można wyszukiwać innych użytkowników, którzy mogą je znać za pomocą [interfejsu API Gremlin usługi Azure Cosmos DB](graph-introduction.md). Następnie można zapisać wyniki w [Azure Cosmos DB bazy danych SQL] dla łatwego pobierania.

**Realizacja:** Używanie powiązania wyzwalania i danych wyjściowych usługi Azure Functions dla usługi Cosmos DB

1. Za pomocą [bazy danych wykresu](graph-introduction.md) usługi Azure Cosmos DB do przechowywania wszystkich użytkowników, można utworzyć nową funkcję z wyzwalacza usługi Azure Functions dla usługi Cosmos DB. 
2. Za każdym razem, gdy zostanie wstawiony nowy użytkownik, funkcja jest wywoływana, a następnie wynik jest przechowywany przy użyciu **powiązania wyjściowego**.
3. Funkcja wysyła zapytanie do bazy danych wykresu, aby wyszukać wszystkich użytkowników, którzy są bezpośrednio związane z nowym użytkownikiem i zwraca ten zestaw danych do funkcji.
4. Te dane są następnie przechowywane w usłudze Azure Cosmos DB, które następnie można łatwo pobrać przez dowolną aplikację frontonu, która pokazuje nowego użytkownika ich połączonych znajomych.

### <a name="retail-use-case---multiple-functions"></a>Przypadek użycia detalicznego — wiele funkcji

W implementacjach sieci sprzedaży, gdy użytkownik dodaje element do koszyka, masz teraz możliwość tworzenia i wywoływania funkcji dla opcjonalnych składników potoku biznesowego.

**Realizacja:** Wiele wyzwalaczy funkcji platformy Azure dla usługi Cosmos DB nasłuchiwanie jednego kontenera

1. Można utworzyć wiele funkcji platformy Azure, dodając wyzwalacze usługi Azure Functions dla usługi Cosmos DB do każdego — wszystkie z nich nasłuchują tego samego kanału danych koszyka zakupów. Należy zauważyć, że gdy wiele funkcji słuchać tego samego źródła danych o zmianie, nowa kolekcja dzierżawy jest wymagana dla każdej funkcji. Aby uzyskać więcej informacji na temat kolekcji dzierżaw, zobacz [Opis biblioteki procesora kanału informacyjnego zmiany](change-feed-processor.md).
2. Za każdym razem, gdy nowy element jest dodawany do koszyka użytkowników, każda funkcja jest niezależnie wywoływana przez źródło danych ze zmiany z kontenera koszyka.
   * Jedna funkcja może używać zawartości bieżącego koszyka, aby zmienić wyświetlanie innych elementów, które użytkownik może być zainteresowany.
   * Inna funkcja może aktualizować sumy zapasów.
   * Inna funkcja może wysyłać informacje o klientach dla niektórych produktów do działu marketingu, który wysyła im listonosza promocyjnego. 

     Każdy dział można utworzyć usługi Azure Functions for Cosmos DB, nasłuchiwanie kanału informacyjnego zmiany i upewnij się, że nie będzie opóźniać zdarzenia przetwarzania zamówień krytycznych w procesie.

We wszystkich tych przypadkach użycia, ponieważ funkcja została oddzielona od siebie aplikacji, nie trzeba rozkręcać nowych wystąpień aplikacji przez cały czas. Zamiast tego usługa Azure Functions uruchamia poszczególne funkcje w celu ukończenia dyskretnych procesów w razie potrzeby.

## <a name="tooling"></a>Narzędzia

Natywna integracja między usługą Azure Cosmos DB i usługi Azure Functions jest dostępna w witrynie Azure portal i w programie Visual Studio 2019.

* W portalu usług Azure Functions można utworzyć wyzwalacz. Aby uzyskać instrukcje szybkiego startu, zobacz [Tworzenie wyzwalacza usługi Azure Functions dla usługi Cosmos DB w witrynie Azure portal.](https://aka.ms/cosmosdbtriggerportalfunc)
* W portalu usługi Azure Cosmos DB można dodać wyzwalacz usługi Azure Functions dla usługi Cosmos DB do istniejącej aplikacji funkcji platformy Azure w tej samej grupie zasobów.
* W programie Visual Studio 2019 można utworzyć wyzwalacz przy użyciu [narzędzi funkcji platformy Azure:](../azure-functions/functions-develop-vs.md)

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Dlaczego warto wybrać integrację usługi Azure Functions dla komputerów bezserwerowych?

Usługa Azure Functions umożliwia tworzenie skalowalnych jednostek pracy lub zwięzłych fragmentów logiki, które można uruchamiać na żądanie, bez inicjowania obsługi administracyjnej lub zarządzania infrastrukturą. Korzystając z usługi Azure Functions, nie trzeba tworzyć pełnowymiarową aplikację, aby reagować na zmiany w bazie danych usługi Azure Cosmos, można utworzyć małe funkcje wielokrotnego użytku dla określonych zadań. Ponadto można również użyć danych usługi Azure Cosmos DB jako danych wejściowych lub wyjściowych do funkcji platformy Azure w odpowiedzi na zdarzenie, takie jak żądania HTTP lub wyzwalacz czasowy.

Usługa Azure Cosmos DB jest zalecaną bazą danych dla architektury obliczeniowej bezserwerowej z następujących powodów:

* **Natychmiastowy dostęp do wszystkich danych:** masz szczegółowy dostęp do każdej wartości przechowywane, ponieważ usługa Azure Cosmos DB [automatycznie indeksuje](index-policy.md) wszystkie dane domyślnie i udostępnia te indeksy natychmiast. Oznacza to, że można stale wysyłać zapytania, aktualizować i dodawać nowe elementy do bazy danych i mieć natychmiastowy dostęp za pośrednictwem usługi Azure Functions.

* **Bez schematu**. Usługa Azure Cosmos DB jest bez schematów — dzięki czemu jest unikatowo w stanie obsłużyć wszelkie dane wyjściowe z funkcji platformy Azure. To podejście "obsłużyć wszystko" ułatwia tworzenie różnych funkcji, które wszystkie dane wyjściowe do usługi Azure Cosmos DB.

* **Skalowalna przepustowość**. Przepływność można skalować w górę iw dół natychmiast w usłudze Azure Cosmos DB. Jeśli masz setki lub tysiące funkcji kwerendy i zapisywania do tego samego kontenera, można skalować w górę [ru/s](request-units.md) do obsługi obciążenia. Wszystkie funkcje mogą działać równolegle przy użyciu przydzielonych ru/s, a twoje dane są gwarantowane, aby były [spójne.](consistency-levels.md)

* **Replikacja globalna**. Można replikować dane usługi Azure Cosmos DB [na całym świecie,](distribute-data-globally.md) aby zmniejszyć opóźnienia, geolokalizacji danych najbliżej miejsca, w którym znajdują się użytkownicy. Podobnie jak w przypadku wszystkich zapytań usługi Azure Cosmos DB, dane z wyzwalaczy opartych na zdarzeniach są odczytywane dane z usługi Azure Cosmos DB najbliżej użytkownika.

Jeśli chcesz zintegrować się z usługą Azure Functions do przechowywania danych i nie potrzebujesz głębokiego indeksowania lub jeśli chcesz przechowywać załączniki i pliki multimedialne, [wyzwalacz usługi Azure Blob Storage](../azure-functions/functions-bindings-storage-blob.md) może być lepszym rozwiązaniem.

Zalety funkcji platformy Azure: 

* **Sterowane zdarzeniami**. Usługi Azure Functions są oparte na zdarzeniach i mogą nasłuchiwać źródła danych zmian z usługi Azure Cosmos DB. Oznacza to, że nie musisz tworzyć logiki słuchania, po prostu zwracaj uwagę na zmiany, których słuchasz. 

* **Bez ograniczeń**. Funkcje są wykonywane równolegle, a usługa obraca się tyle, ile potrzebujesz. Można ustawić parametry.

* **Dobry do szybkich zadań**. Usługa uruchamia nowe wystąpienia funkcji za każdym razem, gdy zdarzenie jest uruchamiane i zamyka je natychmiast po zakończeniu funkcji. Płacisz tylko za czas działania funkcji.

Jeśli nie masz pewności, czy przepływ, aplikacje logiki, usługi Azure Functions lub WebJobs są najlepsze dla twojej implementacji, zobacz [Wybieranie między przepływem, aplikacjami logiki, funkcjami i webjobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Następne kroki

Teraz połączmy usługę Azure Cosmos DB i usługi Azure Functions w celu rzeczywistego: 

* [Tworzenie wyzwalacza usługi Azure Functions dla usługi Cosmos DB w witrynie Azure portal](https://aka.ms/cosmosdbtriggerportalfunc)
* [Tworzenie wyzwalacza HTTP usługi Azure Functions przy użyciu powiązania danych wejściowych usługi Azure Cosmos DB](https://aka.ms/cosmosdbinputbind)
* [Powiązania i wyzwalacze usługi Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md)