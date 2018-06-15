---
title: Diagnozowanie wyjątków czasu wykonywania za pomocą usługi Azure Application Insights | Microsoft Docs
description: Samouczek omawiający znajdowanie i diagnozowanie wyjątków czasu wykonywania w aplikacji za pomocą usługi Azure Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 115611c5d4eeffb0f0600dd0a792ee9f80247e36
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2018
ms.locfileid: "27998053"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Znajdowanie i diagnozowanie wyjątków czasu wykonywania za pomocą usługi Azure Application Insights

Usługa Azure Application Insights gromadzi dane telemetryczne z Twojej aplikacji, aby identyfikować i diagnozować wyjątki czasu wykonywania.  W tym samouczku przedstawiono ten proces dla Twojej aplikacji.  Omawiane kwestie:

> [!div class="checklist"]
> * Modyfikowanie projektu w celu włączenia śledzenia wyjątków
> * Identyfikowanie wyjątków dla różnych składników aplikacji
> * Wyświetlanie szczegółów wyjątku
> * Pobieranie migawki wyjątku do programu Visual Studio w celu debugowania
> * Analizowanie szczegółów nieudanych żądań przy użyciu języka zapytań
> * Tworzenie nowego elementu roboczego w celu poprawienia błędnego kodu


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z następującymi pakietami roboczymi:
    - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    - Tworzenie aplikacji na platformie Azure
- Pobierz i zainstaluj rozszerzenie [Visual Studio Snapshot Debugger](http://aka.ms/snapshotdebugger).
- Włącz rozszerzenie [Visual Studio Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger).
- Wdróż aplikację .NET na platformie Azure i [włącz zestaw Application Insights SDK](app-insights-asp-net.md). 
- Samouczek śledzi identyfikowanie wyjątków w Twojej aplikacji, dlatego zmodyfikuj kod w środowisku deweloperskim lub testowym, aby wygenerować wyjątek. 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Analizowanie błędów
Usługa Application Insights zbiera wszelkie błędy z Twojej aplikacji i pozwala wyświetlać ich częstotliwość dla różnych operacji, aby pomóc w skoncentrowaniu wysiłków na tych z nich, które niosą za sobą największe skutki.  Następnie można przechodzić do szczegółów tych błędów, aby odkryć ich główną przyczynę.   

1. Wybierz pozycję **Application Insights**, a następnie swoją subskrypcję.  
2. Aby otworzyć panel **Błędy**, wybierz pozycję **Błędy** z menu **Zbadaj** albo kliknij wykres **Żądania zakończone niepowodzeniem**.

    ![Żądania zakończone niepowodzeniem](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

3. Panel **Żądania zakończone niepowodzeniem** pokazuje liczbę żądań zakończonych niepowodzeniem oraz liczbę użytkowników, których to dotyczy, dla każdej operacji aplikacji.  Sortując te informacje według użytkowników, można znaleźć te błędy, które mają największy wpływ na użytkowników.  W tym przykładzie dobrymi kandydatami do przyjrzenia się bliżej są operacje **GET Employees/Create** i **GET Customers/Details** z powodu ich dużej liczby błędów i dotkniętych użytkowników.  Po wybraniu operacji na prawym panelu pokazywane są dalsze informacje na temat tej operacji.

    ![Panel Żądania zakończone niepowodzeniem](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

4. Zmniejsz przedział czasu, aby przybliżyć okres, w którym współczynnik błędów wykazuje największy wzrost.

    ![Okno Żądania zakończone niepowodzeniem](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

5. Kliknij pozycję **Wyświetl szczegóły**, aby zobaczyć szczegóły operacji.  Obejmują one wykres Gantta z dwoma błędnymi zależnościami, które w sumie wymagają niemal pół sekundy na ukończenie.  Więcej na temat analizowania problemów z wydajnością możesz się dowiedzieć, kończąc samouczek [Find and diagnose performance issues with Azure Application Insights (Znajdowanie i diagnozowanie problemów z wydajnością w usłudze Azure Application Insights)](app-insights-tutorial-performance.md).

    ![Szczegóły żądań zakończonych niepowodzeniem](media/app-insights-tutorial-runtime-exceptions/failed-requests-details.png)

6. W szczegółach operacji widać również wyjątek FormatException, który wydaje się być przyczyną błędu.  Kliknij wyjątek lub licznik **Najważniejsze 3 typy wyjątków**, aby wyświetlić jego szczegóły.  Zobaczysz, że przyczyną jest nieprawidłowy kod pocztowy.

    ![Szczegóły wyjątku](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)

> [!NOTE]
Włącz [środowisko podglądu](app-insights-previews.md) o nazwie „Ujednolicone szczegóły: diagnostyka transakcji E2E”, aby zobaczyć wszystkie powiązane dane telemetryczne po stronie serwera, takie jak żądania, zależności, wyjątki, ślady, zdarzenia itp. w pojedynczym widoku pełnoekranowym. 

Na włączonym podglądzie możesz sprawdzić czas potrzebny do wywołania zależności wraz z wszelkimi błędami i wyjątkami w ujednoliconym środowisku. W przypadku transakcji między składnikami wykres Gantta wraz z okienkiem szczegółów mogą pomóc w szybkim wykryciu składnika, zależności lub wyjątku będącego główną przyczyną problemu. Dolną sekcję można rozwinąć, aby zobaczyć sekwencję czasową wszelkich śladów lub zdarzeń zebranych dla wybranego komponentu i operacji. [Dowiedz się więcej na temat nowego środowiska](app-insights-transaction-diagnostics.md)  

![Diagnostyka transakcji](media/app-insights-tutorial-runtime-exceptions/e2e-transaction-preview.png)

## <a name="identify-failing-code"></a>Identyfikowanie błędnego kodu
Rozszerzenie Snapshot Debugger zbiera migawki najczęściej występujących wyjątków w Twojej aplikacji, aby pomóc Ci w diagnozowaniu głównej przyczyny problemu w środowisku produkcyjnym.  Migawki debugowania można wyświetlić w portalu, aby zobaczyć stos wywołań i sprawdzić zmienne w każdej ramce tego stosu. Następnie można debugować kod źródłowy, pobierając migawkę i otwierając ją w programie Visual Studio 2017.

1. We właściwościach wyjątku kliknij pozycję **Otwórz migawkę debugowania**.
2. Zostanie otwarty panel **Migawka debugowania** ze stosem wywołań dla żądania.  Kliknij dowolną metodę, aby wyświetlić wartości wszystkich zmiennych lokalnych w momencie żądania.  Zaczynając od pierwszej metody w tym przykładzie, widać zmienne lokalne bez wartości.

    ![Migawka debugowania](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. Pierwsze wywołanie z prawidłowymi wartościami to **ValidZipCode** i widać, że w kodzie pocztowym podano litery, których nie można przekształcić na liczbę całkowitą.  Wygląda to na błąd w kodzie, który trzeba poprawić.

    ![Migawka debugowania](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. Aby pobrać tę migawkę do programu Visual Studio, w którym będzie można znaleźć faktyczny kod do poprawienia, kliknij pozycję **Pobierz migawkę**.
6. Migawka zostanie załadowana do programu Visual Studio.
7. Teraz możesz uruchomić sesję debugowania w programie Visual Studio, która szybko zidentyfikuje wiersz kodu będący przyczyną wyjątku.

    ![Wyjątek w kodzie](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Korzystanie z danych analizy
Wszystkie dane zbierane przez usługę Application Insights są przechowywane w usłudze Azure Log Analytics, która oferuje rozbudowany język zapytań umożliwiający analizowanie danych na różne sposoby.  Możemy użyć tych danych do przeanalizowania żądań, które spowodowały badany wyjątek. 

8. Kliknij informacje CodeLens nad kodem, aby wyświetlić dane telemetryczne dostarczane przez usługę Application Insights.

    ![Kod](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Kliknij pozycję **Analiza wpływu**, aby otworzyć okno Application Insights — analiza.  Jest ono wypełnione kilkoma zapytaniami udostępniającymi szczegóły żądań zakończonych niepowodzeniem, takie jak użytkownicy, przeglądarki i regiony dotknięte niepowodzeniem.<br><br>![Analiza](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Dodawanie elementu roboczego
Jeśli usługa Application Insights zostanie połączona z systemem śledzenia, takim jak Visual Studio Team Services lub GitHub, element roboczy można utworzyć bezpośrednio z usługi Application Insights.

1. Wróć do panelu **Właściwości wyjątku** w usłudze Application Insights.
2. Kliknij pozycję **Nowy element roboczy**.
3. Zostanie otwarty panel **Nowy element roboczy** z już wypełnionymi informacjami na temat wyjątku.  Przed jego zapisaniem możesz dodać dowolne informacje dodatkowe.

    ![Nowy element roboczy](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak identyfikować wyjątki czasu wykonywania, przejdź do następnego samouczka, aby dowiedzieć się, jak identyfikować i diagnozować problemy z wydajnością.

> [!div class="nextstepaction"]
> [Identyfikowanie problemów z wydajnością](app-insights-tutorial-performance.md)