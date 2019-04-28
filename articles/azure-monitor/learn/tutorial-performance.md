---
title: Diagnozowanie problemów z wydajnością za pomocą usługi Azure Application Insights | Microsoft Docs
description: Samouczek omawiający znajdowanie i diagnozowanie problemów z wydajnością aplikacji za pomocą usługi Azure Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 7eae71411a1a3772dbdbaa289a32cbc69fca0e5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61365081"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Znajdowanie i diagnozowanie problemów z wydajnością za pomocą usługi Azure Application Insights

Usługa Azure Application Insights gromadzi dane telemetryczne z Twojej aplikacji, aby pomóc w analizie jej działania i wydajności.  Tymi informacjami można się posłużyć przy identyfikowaniu stwierdzonych problemów albo identyfikowaniu usprawnień do aplikacji, które miałyby największy wpływ na użytkowników.  Ten samouczek przedstawia proces analizowania wydajności składników serwera aplikacji i perspektywy klienta.  Omawiane kwestie:

> [!div class="checklist"]
> * Identyfikowanie wydajności operacji po stronie serwera
> * Analizowanie operacji serwera w celu określenia głównej przyczyny niskiej wydajności
> * Identyfikowanie najwolniejszych operacji po stronie klienta
> * Analizowanie szczegółów wyświetleń stron przy użyciu języka zapytań


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z następującymi pakietami roboczymi:
    - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    - Tworzenie aplikacji na platformie Azure
- Wdróż aplikację .NET na platformie Azure i [włącz zestaw Application Insights SDK](../../azure-monitor/app/asp-net.md).
- [Włącz profiler usługi Application Insights](../../azure-monitor/app/profiler.md#installation) dla swojej aplikacji.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identyfikowanie wolnych operacji serwera
Usługa Application Insights zbiera informacje o wydajności różnych operacji w aplikacji. Identyfikując operacje o najdłuższym czasie trwania, możesz zdiagnozować potencjalne problemy albo najlepiej ukierunkować trwające prace programistyczne w celu podniesienia ogólnej wydajności aplikacji.

1. Wybierz pozycję **Application Insights**, a następnie wybierz swoją subskrypcję.  
1. Aby otworzyć panel **Wydajność**, wybierz pozycję **Wydajność** z menu **Zbadaj** albo kliknij wykres**Czas odpowiedzi serwera**.

    ![Wydajność](media/tutorial-performance/performance.png)

2. Panel **Wydajność** przedstawia wydajność i średni czas trwania każdej operacji dla aplikacji.  Te informacje mogą posłużyć do zidentyfikowania tych operacji, które mają największy wpływ na użytkowników. W tym przykładzie dobrymi kandydatami do przyjrzenia się bliżej są operacje **GET Customers/Details** i **GET Home/Index** z powodu ich względnie długiego czasu trwania i względnie dużej liczby wywołań.  Inne operacje mogą mieć dłuższy czas trwania, ale są rzadko wywoływane, więc efekt ich poprawienia będzie niewielki.  

    ![Panel Wydajność](media/tutorial-performance/performance-blade.png)

3. Wykres przedstawia obecnie średni czas trwania wybranych operacji w czasie. Możesz przełączyć się na 95. percentyl, aby znaleźć problemy z wydajnością. Dodaj operacje, które Cię interesują, przypinając je do wykresu.  To pokazuje, że istnieją wzrosty wartości warte zbadania.  Wyizoluj je jeszcze bardziej, zmniejszając przedział czasu wykresu.

    ![Przypinanie wartości](media/tutorial-performance/pin-operations.png)

4.  Panel wydajności po prawej stronie pokazuje rozkład czasów trwania różnych żądań dla wybranej operacji.  Zmniejsz okno, aby rozpocząć w okolicy 95. percentyla. Z karty ze szczegółowymi informacjami „3 najważniejsze zależności” można szybko odczytać, że zależności zewnętrzne najprawdopodobniej wpływają na spowolnienie transakcji.  Kliknij przycisk z liczbą próbek, aby wyświetlić listę próbek. Następnie możesz wybrać dowolną próbkę, aby wyświetlić szczegóły transakcji.

    ![Rozkład czasów trwania](media/tutorial-performance/duration-distribution.png)

5.  Na pierwszy rzut oka zobaczysz, że wywołanie tabeli Fabrikamaccount Azure Table najbardziej wpływa na całkowity czas trwania transakcji. Zobaczysz też, że wyjątek spowodował jego niepowodzenia. Możesz kliknąć dowolną pozycję na liście, aby wyświetlić jej szczegóły po prawej stronie. [Dowiedz się więcej o środowisku diagnostyki transakcji](../../azure-monitor/app/transaction-diagnostics.md)

    ![Szczegóły operacji](media/tutorial-performance/operation-details.png)
    

6.  Narzędzie **Profiler** pomaga zagłębić się w diagnostykę na poziomie kodu, wyświetlając rzeczywisty kod uruchamiany dla operacji i czas wymagany przez każdy z kroków. Niektóre operacje mogą nie mieć śladu, ponieważ profiler jest uruchamiany okresowo.  Z upływem czasu coraz więcej operacji powinno mieć ślady.  Aby uruchomić profiler dla operacji, kliknij pozycję **Ślady narzędzia Profiler**.
5.  Ślad pokazuje indywidualne zdarzenia dla każdej operacji, więc można zdiagnozować główną przyczynę obecnego czasu trwania całej operacji.  Kliknij jeden z przykładów u góry, które mają najdłuższy czas trwania.
6.  Kliknij pozycję **Pokaż ścieżkę aktywną**, aby wyróżnić konkretną ścieżkę zdarzeń, która najbardziej przyczynia się do całkowitego czasu trwania operacji.  W tym przykładzie widać, że najwolniejsze wywołanie pochodzi z metody *FabrikamFiberAzureStorage.GetStorageTableData*. Częścią zabierającą najwięcej czasu jest metoda *CloudTable.CreateIfNotExist*. Jeśli ten wiersz kodu jest wywoływany po każdym wywołaniu funkcji, niepotrzebnie używane będą wywołanie sieciowe i zasób procesora CPU. Najlepszym sposobem poprawienia kodu jest umieszczenie tego wiersza w jakiejś metodzie startowej, która jest wykonywana tylko raz. 

    ![Szczegóły profilera](media/tutorial-performance/profiler-details.png)

7.  Obszar **Porada dotycząca wydajności** u góry ekranu potwierdza ocenę, że nadmierny czas trwania wynika z oczekiwania.  Kliknij link **oczekiwanie**, aby otworzyć dokumentację omawiającą interpretowanie różnych typów zdarzeń.

    ![Porada dotycząca wydajności](media/tutorial-performance/performance-tip.png)

8.  W celu dalszej analizy możesz kliknąć pozycję **Pobierz ślad etl**, aby pobrać ślad do programu Visual Studio.

## <a name="use-analytics-data-for-server"></a>Korzystanie z danych analizy dla serwera
Funkcja Application Insights — analiza udostępnia zaawansowany język zapytań, który pozwala analizować wszystkie dane zebrane przez usługę Application Insights.  Możesz jej używać do wykonywania głębokiej analizy danych żądań i wydajności.

1. Wróć do panelu szczegółów operacji i kliknij przycisk Analiza.

    ![Przycisk Analiza](media/tutorial-performance/server-analytics-button.png)

2. Funkcja Application Insights — analiza jest otwierana z zapytaniem dla każdego z widoków w panelu.  Zapytania te można uruchomić w proponowanej formie lub dostosować do własnych wymagań.  Pierwsze zapytanie pokazuje czas trwania operacji w miarę upływu czasu.

    ![Analiza](media/tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identyfikowanie wolnych operacji klienta
Oprócz identyfikowania procesów serwera do zoptymalizowania, usługa Application Insights może analizować perspektywę przeglądarek klienta.  To może pomóc zidentyfikować potencjalne ulepszenia do składników klienta, a nawet zidentyfikować problemy z różnymi przeglądarkami i lokalizacjami.

1. Wybierz pozycję **Przeglądarka** z menu **Zbadaj**, aby otworzyć podsumowanie informacji o przeglądarce.  Ma ono postać wizualnego podsumowania różnych danych telemetrycznych dla Twojej aplikacji z perspektywy przeglądarki.

    ![Podsumowanie informacji o przeglądarce](media/tutorial-performance/browser-summary.png)

2.  Przewiń ekran do pozycji **Które strony działają najwolniej?**.  Spowoduje to pokazanie listy stron w Twojej aplikacji, których załadowanie przez klienta trwało najdłużej.  Te informacje mogą posłużyć do nadania wyższego priorytetu stronom, które mają największy wpływ na użytkownika.
3.  Kliknij jedną ze stron, aby otworzyć panel **Wyświetlenie strony**.  W tym przykładzie strona **/FabrikamProd** charakteryzuje się nadmiernym średnim czasem trwania.  Panel **Wyświetlenie strony** zawiera szczegółowe informacje o tej stronie, w tym podział na zakresy różnych czasów trwania.

    ![Wyświetlenie strony](media/tutorial-performance/page-view.png)

4.  Kliknij najdłuższy czas trwania, aby sprawdzić szczegóły tych żądań.  Następnie kliknij pojedyncze żądanie, aby wyświetlić szczegółowe informacje o kliencie żądającym strony, w tym o typie przeglądarki i lokalizacji.  Te informacje mogą pomóc w określeniu, czy występują problemy z wydajnością powiązane z konkretnymi typami klientów.

    ![Szczegóły żądania](media/tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Korzystanie z danych analizy dla klienta
Tak jak w przypadku zbieranych danych dotyczących wydajności serwera, usługa Application Insights udostępnia wszystkie dane klienta do szczegółowej analizy za pomocą narzędzia Analiza.

1. Wróć do podsumowania informacji o przeglądarce i kliknij ikonę Analiza.

    ![Ikona Analiza](media/tutorial-performance/client-analytics-icon.png)

2. Funkcja Application Insights — analiza jest otwierana z zapytaniem dla każdego z widoków w panelu. Pierwsze zapytanie pokazuje czas trwania dla różnych wyświetleń stron w miarę upływu czasu.

    ![Analiza](media/tutorial-performance/client-analytics.png)

3.  Inteligentna diagnostyka to element funkcji Application Insights — analiza identyfikujący unikatowe wzorce w danych.  Po kliknięciu na wykresie liniowym kropki funkcji Inteligentna diagnostyka uruchamiane jest to samo zapytanie, ale bez rekordów będących przyczyną anomalii.  Szczegółowe informacje o tych rekordach są pokazywane w sekcji komentarza zapytania, więc można zidentyfikować właściwości tych wyświetleń strony, które powodują za długie czasy trwania.

    ![Inteligentna diagnostyka](media/tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy już wiesz, jak identyfikować wyjątki czasu wykonywania, przejdź do następnego samouczka, aby dowiedzieć się, jak tworzyć alerty w odpowiedzi na błędy.

> [!div class="nextstepaction"]
> [Alerty dotyczące kondycji aplikacji](../../azure-monitor/learn/tutorial-alert.md)
