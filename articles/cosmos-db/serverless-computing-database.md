---
title: Przetwarzanie bazy danych bez użycia serwera — usługi Azure Functions i Azure Cosmos DB
description: Dowiedz się, jak usługa Azure Cosmos DB i Azure Functions może służyć razem do utworzenia sterowanych zdarzeniami — aplikacje niekorzystające z serwera przetwarzania.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: 54de2d2f9b5691a47ff56891185c7655661092dd
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833595"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Bezserwerowa baza danych obliczeń z użyciem usługi Azure Cosmos DB i Azure Functions

Operacje obliczeniowe jest możliwość skupić się na poszczególnych elementach logiki, które są powtarzalne i bezstanowych. Te elementy wymagają nie zarządzania infrastrukturą i ich używanie zasobów tylko w przypadku sekund, czyli milisekund, ich uruchamiania dla. W samym sercu przepływu obliczeniowych bez użycia serwera są funkcje, które są udostępniane w ekosystemie platformy Azure przez [usługi Azure Functions](https://azure.microsoft.com/services/functions). Aby dowiedzieć się więcej o innych środowisk wykonywania bezserwerowego Azure widzą [Praca bezserwerowa na platformie Azure](https://azure.microsoft.com/solutions/serverless/) strony. 

Za pomocą natywna integracja pomiędzy usługą [usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) i usługi Azure Functions, można utworzyć wyzwalacze bazy danych, powiązania danych wejściowych i powiązania danych wyjściowych bezpośrednio z konta usługi Azure Cosmos DB. Za pomocą usługi Azure Functions i Azure Cosmos DB, można tworzyć i wdrażać oparte na zdarzeniach aplikacje niewymagające użycia serwera, z małymi opóźnieniami dostępem do zaawansowanych danych dla globalnej bazy użytkowników.

## <a name="overview"></a>Przegląd

Usługa Azure Cosmos DB i Azure Functions umożliwiają integrowanie baz danych i aplikacji bez użycia serwera w następujący sposób:

* Utwórz oparte na zdarzeniach **wyzwalacza usługi Azure Cosmos DB** w funkcji platformy Azure. Ten wyzwalacz, który opiera się na [zestawienia zmian](change-feed.md) strumienie do monitorowania zmian kontenera usługi Azure Cosmos DB. Gdy zmiany zostaną wprowadzone do kontenera, zestawienia strumienia zmian jest wysyłany do wyzwalacza, który wywołuje funkcję platformy Azure.
* Alternatywnie powiązać funkcję platformy Azure w kontenerze usługi Azure Cosmos DB za pomocą **powiązania danych wejściowych**. Powiązania danych wejściowych odczytywać dane z kontenera, gdy funkcja była wykonywana.
* Powiązywanie kontenera usługi Azure Cosmos DB przy użyciu funkcji **powiązania danych wyjściowych**. Powiązania danych wyjściowych zapisu danych do kontenera, po zakończeniu funkcji.

> [!NOTE]
> Obecnie są obsługiwane do użytku z interfejsu API SQL wyzwalacza usługi Azure Cosmos DB, powiązania danych wejściowych i powiązania danych wyjściowych. Dla wszystkich innych usługi Azure Cosmos DB interfejsów API należy dostęp do bazy danych ze swojej funkcji przy użyciu statycznych klienta dla interfejsu API.


Poniższy diagram ilustruje każdą z tych trzech integracji: 

![Jak zintegrować usługę Azure Cosmos DB i Azure Functions](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Wyzwalacz usługi Azure Cosmos DB, powiązania danych wejściowych i powiązania danych wyjściowych może służyć w następujących kombinacji:
* Wyzwalacz usługi Azure Cosmos DB może służyć za pomocą powiązania danych wyjściowych do innego kontenera usługi Azure Cosmos DB. Po funkcji wykonuje jakąś akcję na element kanału informacyjnego zmian można go zapisać w innym kontenerze (zapisywania, że do tego samego kontenera, z której pochodzi takie ustawienie spowodowałoby skutecznie powstanie pętli cyklicznego). Alternatywnie można użyć wyzwalacza usługi Azure Cosmos DB skutecznie migrować wszystkie zmienione elementy z jednego kontenera do innego kontenera przy użyciu powiązania danych wyjściowych.
* Powiązania danych wejściowych i powiązania danych wyjściowych usługi Azure Cosmos DB może służyć w tej samej funkcji platformy Azure. To działa dobrze w przypadkach, gdy chcesz znaleźć określone dane przy użyciu powiązania danych wejściowych, zmodyfikować go w funkcji platformy Azure i zapisz go na tym samym kontenerze lub innego kontenera po modyfikacji.
* Powiązanie danych wejściowych do kontenera usługi Azure Cosmos DB mogą być używane w taką samą funkcję jak wyzwalacz usługi Azure Cosmos DB i może być używany z lub bez powiązania danych wyjściowych usługi również. Można użyć tej kombinacji do zastosowania aktualne waluty wymiany informacji (pobierane przy użyciu powiązania danych wejściowych do kontenera programu exchange) zestawienia zmian nowych zamówień w usłudze koszyka zakupów. Zaktualizowano zakupów łącznie koszyka, przy użyciu bieżącego konwersja stosowany, można zapisać w trzeci kontenera za pomocą powiązania danych wyjściowych.

## <a name="use-cases"></a>Przypadki zastosowań

Następujące przypadki użycia pokazują na kilka sposobów, które można wykorzystać dane usługi Azure Cosmos DB — łącząc dane do usługi Azure Functions oparte na zdarzeniach.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT przypadek użycia — wyzwalacz usługi Azure Cosmos DB i powiązania danych wyjściowych

W implementacji IoT można wywołać funkcji podczas wyboru światła aparat są wyświetlane w samochód połączony.

**Implementacja:** Użyj wyzwalacza usługi Azure Cosmos DB i powiązania danych wyjściowych

1. **Wyzwalacza usługi Azure Cosmos DB** służy do wyzwalania zdarzenia związane z alertami samochód, takie jak światło aparatu wyboru mieszczących się w samochód połączony.
2. Po przejściu do trybu wyboru aparatu światła, dane czujników są wysyłane do usługi Azure Cosmos DB.
3. Usługa Azure Cosmos DB tworzy lub aktualizuje nowe dokumenty dane czujników, a następnie zmiany te są przesyłane strumieniowo do wyzwalacza usługi Azure Cosmos DB.
4. Wyzwalacz jest wywoływana dla każdej zmiany danych, do zbierania danych czujników, ponieważ wszystkie zmiany są przesyłane strumieniowo za pomocą zestawienia zmian.
5. Warunkiem progu jest używany w funkcji do wysyłania danych z czujników do działu gwarancji.
6. Jeśli temperatura jest również przez określoną wartość, alert również są wysyłane do właściciela.
7. **Powiązania danych wyjściowych** dla funkcji aktualizacji rekordu samochodów w innym kontenerze usługi Azure Cosmos DB do przechowywania informacji o zdarzeniu aparatu wyboru.

Na poniższej ilustracji przedstawiono kod napisany w witrynie Azure portal dla tego wyzwalacza.

![Tworzenie wyzwalacza usługi Azure Cosmos DB w witrynie Azure portal](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Finansowe przypadek użycia — wyzwalacza czasomierza i powiązania danych wejściowych

W implementacji finansowych można wywoływać funkcji, gdy saldo konta bankowego znajduje się w pewnym.

**Implementacja:** Powiązania danych wejściowych wyzwalacza czasomierza, za pomocą usługi Azure Cosmos DB

1. Za pomocą [wyzwalacza czasomierza](../azure-functions/functions-bindings-timer.md), możesz pobrać informacje saldo konta bankowego, które są przechowywane w kontenerze usługi Azure Cosmos DB w określonych interwałach przy użyciu **powiązania danych wejściowych**.
2. Jeśli saldo jest poniżej wartości progowej niskim saldzie ustawiony przez użytkownika, następnie monitowanie akcję z funkcji platformy Azure.
3. Powiązania danych wyjściowych może być [integracji usługi SendGrid](../azure-functions/functions-bindings-sendgrid.md) wysyłającą wiadomość e-mail z konta usługi na adresy e-mail dla każdego konta o niskim saldzie.

Na poniższych ilustracjach przedstawiono kod w witrynie Azure portal, w tym scenariuszu.

![Plik Index.js wyzwalacza czasomierza finansowych scenariusza](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![W pliku Run.csx wyzwalacza czasomierza finansowych scenariusza](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Gier przypadek użycia — wyzwalacz usługi Azure Cosmos DB i powiązania danych wyjściowych

W gry, po utworzeniu nowego użytkownika można wyszukiwać innych użytkowników, którzy mogą znać je za pomocą [interfejsu API usługi Azure Cosmos DB Gremlin](graph-introduction.md). Następnie możesz zapisywać wyniki [SQL usługi Azure Cosmos DB database] ułatwia ich odnalezienie.

**Implementacja:** Użyj wyzwalacza usługi Azure Cosmos DB i powiązania danych wyjściowych

1. Za pomocą usługi Azure Cosmos DB [bazy danych grafów](graph-introduction.md) do przechowywania wszystkich użytkowników, można utworzyć nową funkcję przy użyciu wyzwalacza usługi Azure Cosmos DB. 
2. Zawsze, gdy nowy użytkownik zostanie wstawiony, funkcja jest wywoływana i następnie wynik jest przechowywany przy użyciu **powiązania danych wyjściowych**.
3. Funkcja wysyła zapytanie do bazy danych programu graph, aby wyszukać wszystkich użytkowników, którzy są bezpośrednio związane z nowego użytkownika i zwraca ten zestaw danych do funkcji.
4. Te dane są następnie przechowywane w usługi Azure Cosmos DB, w której następnie można łatwo pobrać przez aplikację zewnętrzną, pokazujący swoim znajomym, połączonych nowego użytkownika.

### <a name="retail-use-case---multiple-functions"></a>Przypadek użycia handlu detalicznego — wiele funkcji

W implementacji sprzedaży detalicznej gdy użytkownik dodaje element do koszyka masz teraz elastyczność tworzenia i wywoływać funkcje dla firm opcjonalne składniki potoku.

**Implementacja:** Wiele wyzwalaczy usługi Azure Cosmos DB nasłuchiwanie jeden kontener

1. Wiele funkcji platformy Azure można utworzyć przez dodanie Wyzwalacze usługi Azure Cosmos DB do każdego — które nasłuchują do tej samej zmienić źródło danych koszyka zakupów. Należy pamiętać, że gdy wiele funkcji słuchać takie same zestawienia zmian Nowa kolekcja dzierżawy jest wymagana dla każdej funkcji. Aby uzyskać więcej informacji o kolekcjach dzierżawy, zobacz [informacje o bibliotece procesora zestawienia zmian](change-feed-processor.md).
2. Zawsze, gdy nowy element zostanie dodany do użytkowników, koszyk, każda funkcja niezależnie zostanie wywołany przez źródło z kontenera koszyka zakupów zmian.
   * Jedna funkcja może używać zawartość koszyka bieżącej, aby zmienić sposób wyświetlania innych elementów, które użytkownik może być zainteresowany.
   * Inna funkcja może być aktualizowana sumy spisu.
   * Inną funkcję może wysłać informacje o kliencie w przypadku niektórych produktów do działu marketingu, który wysyła je masowo promocyjne wiadomości e-mail. 

     Dział można utworzyć wyzwalacza usługi Azure Cosmos DB, nasłuchuje zestawienia zmian i upewnij się, że nie będą one opóźnienie krytyczne kolejność przetwarzania zdarzeń w procesie.

We wszystkich tych zastosowań, ponieważ funkcja ma odłączone samej aplikacji, nie trzeba uruchamiaj nowe wystąpienia aplikacji przez cały czas. Zamiast tego usługa Azure Functions uruchamia poszczególnych funkcji, aby zakończyć procesów dyskretnych zgodnie z potrzebami.

## <a name="tooling"></a>Narzędzia

Natywna integracja pomiędzy usługą Azure Cosmos DB i Azure Functions jest dostępne w witrynie Azure portal i programu Visual Studio 2019 r.

* W portalu usługi Azure Functions można utworzyć wyzwalacza usługi Azure Cosmos DB. Przewodnik Szybki Start instrukcje można znaleźć [Tworzenie wyzwalacza usługi Azure Cosmos DB w witrynie Azure portal](https://aka.ms/cosmosdbtriggerportalfunc).
* W portalu usługi Azure Cosmos DB można dodać wyzwalacza usługi Azure Cosmos DB do istniejącej aplikacji funkcji platformy Azure w tej samej grupie zasobów.
* W programie Visual Studio 2019 r, można utworzyć wyzwalacza usługi Azure Cosmos DB przy użyciu [narzędzi usługi Azure Functions](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Dlaczego warto wybrać integracji usługi Azure Functions do obliczeń bez użycia serwera?

Usługa Azure Functions zapewnia możliwość tworzenia skalowalnej jednostki pracy lub zwięzłe rodzajów logiki, który można uruchomić na żądanie bez inicjowania obsługi infrastruktury i zarządzania nią. Za pomocą usługi Azure Functions, nie ma konieczności tworzenia pełnej aplikacji do reagowania na zmiany w bazie danych Azure Cosmos DB, tworzenia małych funkcji wielokrotnego użytku do wykonywania określonych zadań. Ponadto umożliwia także danych usługi Azure Cosmos DB jako dane wejściowe i wyjściowe do funkcji platformy Azure w odpowiedzi na zdarzenia, takie jak HTTP żądania lub przekroczono limit czasu wyzwalacza.

Usługa Azure Cosmos DB jest zalecane bazy danych dla bezserwerowej architektury przetwarzania z następujących powodów:

* **Natychmiastowy dostęp do wszystkich danych**: Masz szczegółowe dostęp do każdej wartości, które zostały zapisane, ponieważ usługi Azure Cosmos DB [automatycznie indeksuje](index-policy.md) wszystkich danych domyślnie i sprawia, że te indeksy jest natychmiast dostępna. Oznacza to, że jesteś w stanie stale zapytania, zaktualizować i dodawania nowych elementów do bazy danych i natychmiast uzyskać dostęp za pośrednictwem usługi Azure Functions.

* **Ze schematów**. Usługa Azure Cosmos DB nie korzysta ze schematów -, więc jest jednoznacznie może obsługiwać żadnych danymi wyjściowymi funkcji platformy Azure. To podejście "obsługujące" sprawia, że bardzo proste tworzyć różne funkcje, które wszystkie dane wyjściowe do usługi Azure Cosmos DB.

* **Skalowalna przepływność**. Przepływności można skalować w górę i w dół natychmiast w usłudze Azure Cosmos DB. W przypadku setek lub tysięcy funkcje zapytań i zapisu do tego samego kontenera, możesz skalować w górę swoje [jednostek RU/s](request-units.md) do obsługi obciążenia. Wszystkie funkcje mogą działać równolegle za pomocą usługi przydzielonych jednostek RU/s i danych musi być [spójne](consistency-levels.md).

* **Globalnej replikacji**. Można replikować dane w usłudze Azure Cosmos DB [na całym świecie](distribute-data-globally.md) do zmniejszenia opóźnień, geolokalizację swoje dane, które są najbliżej gdzie są Twoi użytkownicy. Jako za pomocą wszystkich zapytań usługi Azure Cosmos DB, dane z wyzwalacze sterowane zdarzeniami jest odczytywać dane z usługi Azure Cosmos DB najbliżej użytkownika.

Jeśli chcą zintegrować z usługą Azure Functions do przechowywania danych i nie ma potrzeby głębokiego indeksowania lub jeśli chcesz przechować załączników i pliki multimedialne, [wyzwalacz usługi Azure Blob Storage](../azure-functions/functions-bindings-storage-blob.md) może być lepszym rozwiązaniem.

Korzyści wynikające z usługi Azure Functions: 

* **Event-driven**. Usługa Azure Functions są oparte na zdarzeniach i może nasłuchiwać na zmianę źródła danych z usługi Azure Cosmos DB. Oznacza to, nie trzeba tworzyć nasłuchiwania logiki, możesz po prostu śledzą na zmiany, które one nasłuchiwanie. 

* **Brak ograniczeń**. Funkcje wykonywane w równoległych i usługa obroty się tak dużo, ilu potrzebujesz. Należy ustawić parametry.

* **Rozwiązanie dobre dla szybkich zadań**. Uruchamia usługę nowych wystąpień funkcji zawsze wtedy, gdy zdarzenie wyzwala i zamyka je jak najszybciej po zakończeniu funkcji. Płacisz tylko za czas wykonywania funkcji.

Jeśli nie masz pewności, czy usługi Flow, Logic Apps, Azure Functions lub zadania Webjob są najlepsze w przypadku implementacji, zobacz [wybór między usługi Flow, Logic Apps, Functions i WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz Połączmy rzeczywistym z usługi Azure Cosmos DB i Azure Functions: 

* [Tworzenie wyzwalacza usługi Azure Cosmos DB w witrynie Azure portal](https://aka.ms/cosmosdbtriggerportalfunc)
* [Tworzenie wyzwalacza HTTP funkcje platformy Azure przy użyciu powiązania danych wejściowych usługi Azure Cosmos DB](https://aka.ms/cosmosdbinputbind)
* [Azure Cosmos DB, powiązania i wyzwalacze](../azure-functions/functions-bindings-cosmosdb.md)


 



