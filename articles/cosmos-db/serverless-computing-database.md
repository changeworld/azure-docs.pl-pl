---
title: Obliczanie bez serwera bazy danych — usługi Azure Functions i bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, bazy danych rozwiązania Cosmos Azure i usługi Azure Functions możliwości korzystania ze sobą do tworzenia sterowane zdarzeniami niekorzystającą przetwarzania danych aplikacji.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: mimig
ms.openlocfilehash: 39155a2407efa0cc13236a1bbbbcd292daac86b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-serverless-database-computing-using-azure-functions"></a>Azure rozwiązania Cosmos bazy danych: Za pomocą usługi Azure Functions obliczeniowych bez serwera bazy danych

Obliczanie niekorzystającą jest możliwość skupić się na poszczególnych części logiki, które są powtarzalnych i bezstanowe. Te elementy wymagają bez infrastruktury zarządzania i ich używać tylko w przypadku sekund lub milisekund, ich uruchamiania dla zasobów. Fundament niekorzystającą przepływu obliczeniowych są funkcje, które są udostępniane w ekosystemie Azure przez [usługi Azure Functions](https://azure.microsoft.com/services/functions).

Z natywna integracja pomiędzy usługą [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db) i usługi Azure Functions można utworzyć wyzwalaczy bazy danych, powiązania wejściowe i powiązania danych wyjściowych bezpośrednio z Twojego konta bazy danych Azure rozwiązania Cosmos. Przy użyciu usługi Azure Functions i bazy danych rozwiązania Cosmos platformy Azure, możesz tworzenie i wdrażanie sterowane zdarzeniami niekorzystającą aplikacji, o małych opóźnieniach dostępu do sformatowanego danych globalnych bazę użytkowników.

## <a name="overview"></a>Przegląd

Bazę danych rozwiązania Cosmos systemu Azure i usługi Azure Functions umożliwiają integrowanie baz danych i pliki aplikacji w następujący sposób:

* Utwórz sterowane zdarzeniami **wyzwalacza bazy danych Azure rozwiązania Cosmos** w funkcji platformy Azure. Zależy od tego wyzwalacza [zmienić źródła strumieniowego](change-feed.md) strumieni do monitorowania kontener zmiany z bazy danych Azure rozwiązania Cosmos. Podczas wprowadzania żadnych zmian do kontenera, zmiany źródła danych strumienia są wysyłane do wyzwalacz, który wywołuje funkcję platformy Azure.
* Alternatywnie powiązać funkcji platformy Azure z kolekcji bazy danych rozwiązania Cosmos Azure przy użyciu **wejściowych powiązania**. Powiązania wejściowe odczytać danych z kontenera, jeśli funkcja wykonuje.
* Powiązać kolekcji bazy danych rozwiązania Cosmos Azure przy użyciu funkcji **powiązania wyjściowego**. Powiązania danych wyjściowych zapisu danych do kontenera, po zakończeniu działania funkcji.

> [!NOTE]
> W tej chwili wyzwalacz usługi Azure Cosmos DB, powiązania danych wejściowych i powiązania danych wyjściowych współpracują tylko z kontami interfejsu API SQL i interfejsu API programu Graph.

Poniższy diagram ilustruje każdą z tych trzech integracji: 

![Sposób integracji bazy danych rozwiązania Cosmos Azure i usługi Azure Functions](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

W następujących kombinacji można wyzwalacza bazy danych Azure rozwiązania Cosmos, powiązania wejściowego i powiązania danych wyjściowych:
* Wyzwalacz bazy danych rozwiązania Cosmos Azure może służyć wraz z powiązaniem danych wyjściowych do innego kontenera Azure DB rozwiązania Cosmos. Po funkcji wykonuje akcję elementu w źródle danych zmiany może zapisywać innego kontenera (pisania go do tego samego kontenera, z której pochodzi skutecznie spowoduje utworzenie pętli cyklicznej). Lub wyzwalacz bazy danych Azure rozwiązania Cosmos umożliwia efektywne migrację wszystkie zmienione elementy z jednego kontenera do innego kontenera podczas korzystania z wiązania danych wyjściowych.
* W tej samej funkcji platformy Azure można powiązania wejściowe i powiązania danych wyjściowych dla bazy danych Azure rozwiązania Cosmos. To działa dobrze w przypadkach, gdy chcesz znaleźć niektórych danych z powiązania wejściowego, zmodyfikuj go w funkcji platformy Azure, a następnie zapisz go na tym samym kontenerze lub innego kontenera po modyfikacji.
* Powiązania wejściowego do kontenera Azure DB rozwiązania Cosmos można użyć w tej samej funkcji jako wyzwalacz bazy danych Azure rozwiązania Cosmos i mogą być używane z lub bez wyjścia również powiązania. Ta kombinacja do stosowania aktualne waluty wymiany informacji (pobierane za pomocą powiązania wejściowego do kontenera programu exchange) można użyć do zmiany źródła nowych zamówień w usługą koszyka zakupów. Zaktualizowano łączną koszyka zakupów, z bieżącej konwersja zastosowane, mogą być zapisywane na trzeci kontener używanie powiązania danych wyjściowych.

## <a name="use-cases"></a>Przypadki zastosowań

Następujące przypadki użycia pokazują na kilka sposobów. Aby zapewnić większość danych bazy danych Azure rozwiązania Cosmos — łączenie danych z funkcjami usługi Azure sterowanego zdarzeniami.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT przypadkach - DB rozwiązania Cosmos Azure wyzwalacza i powiązania wyjściowego

W implementacjach IoT można wywołania funkcji, gdy światła aparat wyboru jest wyświetlany w połączonych samochodów.

**Implementacja:** wyzwalacz bazy danych Azure rozwiązania Cosmos i powiązania danych wyjściowych

1. **Wyzwalacza bazy danych Azure rozwiązania Cosmos** służy do wyzwalania zdarzenia związane z alertami samochodu, takie jak światło aparat wyboru przychodzących połączonych samochodu.
2. Jeśli pochodzi światła aparat wyboru, dane czujników są wysyłane do bazy danych Azure rozwiązania Cosmos.
3. Azure DB rozwiązania Cosmos tworzy lub aktualizuje nowych dokumentów danych czujnika, a następnie zmiany te są przesyłane strumieniowo do bazy danych Azure rozwiązania Cosmos wyzwalacza.
4. Wyzwalacz jest wywoływana dla każdej zmiany danych, do zbierania danych czujnika, ponieważ wszystkie zmiany są przesyłane strumieniowo za pośrednictwem zmiany źródła danych.
5. Warunek progu jest używany w funkcji do wysyłania danych czujnika do działu gwarancji.
6. Jeśli temperatura jest również przez określoną wartość, alert zostanie także wysłana do właściciela.
7. **Powiązania wyjściowego** w funkcji aktualizacji rekordu samochodu w innej kolekcji bazy danych rozwiązania Cosmos Azure do przechowywania informacji o zdarzeniu aparat wyboru.

Poniższa ilustracja przedstawia przykładowy kod napisany w portalu Azure dla tego wyzwalacza.

![Utwórz wyzwalacz bazy danych Azure rozwiązania Cosmos w portalu Azure](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Finansowe przypadek - wyzwalacza czasomierza użycia i dane wejściowe powiązania

W implementacjach finansowych gdy saldo konta bankowego podlega pewnym można wywołać funkcji.

**Implementacja:** powiązania danych wejściowych wyzwalacza bazującego na czasomierzu z bazy danych Azure rozwiązania Cosmos

1. Przy użyciu [wyzwalacza czasomierza](../azure-functions/functions-bindings-timer.md), mogą pobierać informacje saldo konta bankowego, które są przechowywane w kontenerze bazy danych Azure rozwiązania Cosmos w określonych interwałach przy użyciu **wejściowych powiązania**.
2. Jeśli saldo jest poniżej progu niski bilans określonego przez użytkownika, następnie wykonać kolejne czynności z akcją z funkcji platformy Azure.
3. Powiązanie danych wyjściowych może być [integracji SendGrid](../azure-functions/functions-bindings-sendgrid.md) który wysyła wiadomość e-mail z konta usługi na adresy e-mail dla każdego z tych kont niski.

Na poniższych ilustracjach przedstawiono kod w portalu Azure w tym scenariuszu.

![Plik Index.js dla wyzwalacza bazującego na czasomierzu finansowych scenariusza](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Plik Run.csx dla wyzwalacza bazującego na czasomierzu finansowych scenariusza](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Gier przypadkach - DB rozwiązania Cosmos Azure wyzwalacza i powiązania wyjściowego

W gier, podczas tworzenia nowego użytkownika można wyszukiwać innych użytkowników, którzy mogą znać je za pomocą [interfejsu API programu Graph usługi Azure rozwiązania Cosmos DB](graph-introduction.md). [Azure rozwiązania Cosmos bazy danych SQL database] ułatwia ich odnalezienie może następnie zapisać wyniki.

**Implementacja:** wyzwalacz bazy danych Azure rozwiązania Cosmos i powiązania danych wyjściowych

1. Przy użyciu bazy danych Azure rozwiązania Cosmos [bazy danych wykresu](graph-introduction.md) do przechowywania wszystkich użytkowników, można utworzyć nową funkcję z wyzwalaczem bazy danych Azure rozwiązania Cosmos. 
2. Zawsze, gdy nowy użytkownik zostanie wstawiony, funkcja jest wywoływana i następnie wynik jest przechowywany przy użyciu **powiązania wyjściowego**.
3. Funkcja wysyła zapytanie do bazy danych wykresu, aby wyszukać wszystkich użytkowników, którzy są bezpośrednio związane z nowego użytkownika i zwraca ten zestaw danych do funkcji.
4. Dane te są następnie przechowywane w usłudze Azure DB rozwiązania Cosmos, które następnie mogą być w łatwy sposób pobierany przez aplikację zewnętrzną, pokazujący swoich znajomych połączonych nowego użytkownika.

### <a name="retail-use-case---multiple-functions"></a>Przypadek użycia detalicznej — wiele funkcji

W implementacji sprzedaży detalicznej gdy użytkownik dodaje element do koszyka masz teraz elastyczność tworzenia i wywoływać funkcje dla składników opcjonalnych biznesowych.

**Implementacja:** wyzwalaczy wielu DB rozwiązania Cosmos Azure nasłuchiwanie jednej kolekcji

1. Wiele funkcji Azure można utworzyć przez dodanie źródła danych koszyka zakupów zmienić wyzwalacze bazy danych Azure rozwiązania Cosmos do każdego — które nasłuchiwanie na takie same. Należy pamiętać, że po wielu funkcji nasłuchiwanie na takie same zmienić źródło nowej kolekcji dzierżawy jest wymagany dla każdej funkcji. Aby uzyskać więcej informacji o kolekcjach dzierżawy, zobacz [opis biblioteki procesora źródła danych zmian](change-feed.md#understand-cf).
2. Zawsze, gdy nowy element jest dodawany do użytkowników, koszyka, każda funkcja niezależnie jest wywoływany przez zmiany źródła danych z kontenera koszyka zakupów.
    * Jedna funkcja może używać zawartości bieżący koszyk można zmienić wyświetlanie innych elementów, które mogą być zainteresowani użytkownika.
    * Inną funkcję może zaktualizować sumy spisu.
    * Inną funkcję może wysłać informacje o kliencie dla niektórych produktów do działu marketingu, który wysyła je promocyjna program obsługi poczty. 

    Dział można utworzyć wyzwalacza bazy danych Azure rozwiązania Cosmos przez nasłuchiwanie zmian źródła danych i upewnij się, że nie będą one opóźnienie krytyczne kolejność przetwarzania zdarzeń w procesie.

We wszystkich tych zastosowań, ponieważ funkcja jest całkowicie niezależna aplikacja, nie trzeba pokrętła się nowych wystąpień aplikacji cały czas. Zamiast tego należy się poszczególne funkcje do ukończenia osobne procesy zgodnie z potrzebami obraca usługi Azure Functions.

## <a name="tooling"></a>Narzędzia

Natywna integracja pomiędzy usługą bazy danych rozwiązania Cosmos Azure i usługi Azure Functions jest dostępne w portalu Azure i programu Visual Studio 2017 r.
* W portalu Azure Functions można utworzyć wyzwalacza bazy danych Azure rozwiązania Cosmos. Szybki Start instrukcje, zobacz [utworzyć wyzwalacza bazy danych Azure rozwiązania Cosmos w portalu Azure](https://aka.ms/cosmosdbtriggerportalfunc) ![utworzyć wyzwalacza bazy danych Azure rozwiązania Cosmos w portalu Azure Functions](./media/serverless-computing-database/azure-function-cosmos-db-trigger.png) 
* W portalu Azure Functions można również dodać do innych typów wyzwalaczy powiązania wejściowe bazy danych Azure rozwiązania Cosmos i powiązania danych wyjściowych. Szybki Start instrukcje, zobacz [przechowywania danych niestrukturalnych przy użyciu usługi Azure Functions i DB rozwiązania Cosmos](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md).
    ![Utwórz wyzwalacz bazy danych Azure rozwiązania Cosmos w portalu Azure Functions](./media/serverless-computing-database/function-portal-input-binding.png)
*   W portalu Azure DB rozwiązania Cosmos wyzwalacz bazy danych Azure rozwiązania Cosmos można dodać do istniejącej aplikacji funkcji platformy Azure w tej samej grupie zasobów.
    ![Utwórz wyzwalacz bazy danych Azure rozwiązania Cosmos w portalu Azure Functions](./media/serverless-computing-database/cosmos-db-portal.png)
* W programie Visual Studio 2017 r można utworzyć wyzwalacza bazy danych rozwiązania Cosmos Azure przy użyciu zintegrowanego szablonu:

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]


## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Dlaczego integracji usługi Azure Functions dla przetwarzania niekorzystającą?

Środowisko Azure Functions zapewnia możliwość tworzenia skalowalnej jednostki pracy lub krótkie fragmenty logiki, które można uruchomić na żądanie, bez obsługi lub zarządzania infrastrukturą. Za pomocą usługi Azure Functions, nie trzeba tworzyć pełnej wersji aplikacji do reagowania na zmiany w bazie danych bazy danych rozwiązania Cosmos platformy Azure, możesz utworzyć małe funkcje wielokrotnego użytku do wykonywania określonych zadań. Ponadto umożliwia także danych DB rozwiązania Cosmos Azure jako wejście lub wyjście do funkcji platformy Azure w odpowiedzi na zdarzenie, takich jak HTTP żądania lub wyzwalacza czasu.

Azure DB rozwiązania Cosmos jest zalecane bazy danych dla architektury obliczeniowych niekorzystającą z następujących powodów:

* **Natychmiastowy dostęp do wszystkich danych**: szczegółowego dostęp do każdej wartości zapisane, ponieważ bazy danych Azure rozwiązania Cosmos [automatycznie indeksuje](indexing-policies.md) wszystkie dane domyślnie i natychmiast udostępnia te indeksy. Oznacza to, że możliwe jest stale zapytania, zaktualizować i dodawania nowych elementów do bazy danych i mają stały dostęp za pośrednictwem usługi Azure Functions.

* **Schematu**. Azure DB rozwiązania Cosmos jest schematu -, dlatego jest unikatowo może obsługiwać żadnych danymi wyjściowymi funkcji platformy Azure. Takie podejście "obsługi niczego" sprawia, że bezpośrednie, aby utworzyć różne funkcje, które wszystkie dane wyjściowe do bazy danych Azure rozwiązania Cosmos.

* **Skalowalne przepływność**. Przepływność można skalować w górę i w dół natychmiast w usłudze Azure DB rozwiązania Cosmos. Jeśli masz setek lub tysięcy funkcji podczas badania i zapisywanie do tej samej kolekcji, można zwiększać z [RU/s](request-units.md) obsłużyć obciążenia. Wszystkie funkcje mogą pracować z użyciem Twojego przydzielone RU/s i danych jest gwarancji [spójne](consistency-levels.md).

* **Globalne replikacji**. Można replikować danych bazy danych Azure rozwiązania Cosmos [na całym świecie](distribute-data-globally.md) do zmniejszenia opóźnień, lokalizowanie geograficznie najbliżej gdzie znajdują się użytkownicy danych. Jako z wszystkich zapytań bazy danych Azure rozwiązania Cosmos, danych z wyzwalaczy sterowane zdarzeniami jest odczytywać dane z bazy danych rozwiązania Cosmos platformy Azure, które są najbardziej zbliżony do użytkownika.

Jeśli rozważasz usługę do integracji z usługi Azure Functions do przechowywania danych i nie ma potrzeby indeksowania głębokie lub należy przechowywać załączników i plików multimedialnych [wyzwalacza z magazynu obiektów Blob Azure](../azure-functions/functions-bindings-storage-blob.md) może być lepszym rozwiązaniem.

Zalety funkcji platformy Azure: 

* **Event-driven**. Funkcje platformy Azure są sterowane zdarzeniami i wykrywać do zmiany źródła danych z bazy danych usługi Azure rozwiązania Cosmos. Oznacza to, nie trzeba tworzyć logikę nasłuchiwania, możesz po prostu uważać na zmiany, które jest nasłuchiwanie. 

* **Brak ograniczeń**. Funkcje wykonywanie równoległe i usłudze obroty w górę tyle, zgodnie z potrzebami. Należy ustawić parametry.

* **Ułatwia szybkie zadania**. Usługa się nowych wystąpień funkcji obraca zawsze, gdy zdarzenie wyzwala i zamyka je zaraz po zakończeniu działania funkcji. Płacisz tylko za czas funkcji są uruchomione.

Jeśli nie masz pewności, czy są najlepsze w przypadku implementacji przepływu, Logic Apps, usługi Azure Functions lub zadań Webjob, zobacz [wybór między przepływu, Logic Apps, funkcje i zadań Webjob](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz załóżmy połączenia bazy danych rozwiązania Cosmos Azure i usługi Azure Functions rzeczywistym: 

* [Utwórz wyzwalacz bazy danych Azure rozwiązania Cosmos w portalu Azure](https://aka.ms/cosmosdbtriggerportalfunc)
* [Utwórz wyzwalacz Azure funkcje HTTP z powiązaniem wejściowych bazy danych Azure rozwiązania Cosmos](https://aka.ms/cosmosdbinputbind)
* [Przechowywania danych niestrukturalnych przy użyciu usługi Azure Functions i DB rozwiązania Cosmos](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)
* [Azure DB rozwiązania Cosmos powiązania i wyzwalaczy](../azure-functions/functions-bindings-cosmosdb.md)


 



