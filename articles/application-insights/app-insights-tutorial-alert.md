---
title: Wysyłanie alertów z usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Samouczek do wysyłania alertów w odpowiedzi na błędy w aplikacji przy użyciu usługi Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 39e2f136e30ebb6dcfc003c435382f3384af1052
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2017
ms.locfileid: "23947228"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitor i alert na kondycji aplikacji z usługą Azure Application Insights

Azure Application Insights umożliwia monitorowanie aplikacji i wysyłania alertów, gdy jest ona niedostępna, u których występują błędy, lub niewystarczający problemy z wydajnością.  Ten samouczek przeprowadza użytkownika przez proces tworzenia testów, aby sprawdzić ciągłej dostępności aplikacji i wysyłanie różnego rodzaju alertów w odpowiedzi na wykrytych problemów.  Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz test dostępności, aby sprawdzić stale odpowiedzi aplikacji
> * Wysyłania wiadomości e-mail do administratorów, gdy wystąpi problem
> * Tworzenie alertów w oparciu metryki wydajności 
> * Użyj aplikacji logiki, aby wysłać dane telemetryczne podsumowane zgodnie z harmonogramem.


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z następującymi pakietami roboczymi:
    - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    - Tworzenie aplikacji na platformie Azure
    - Wdrażanie aplikacji .NET na platformie Azure i [włączyć zestaw SDK usługi Application Insights](app-insights-asp-net.md). 


## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do portalu Azure pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Utwórz test dostępności
Badania dostępności w usłudze Application Insights umożliwiają automatycznego testowania aplikacji z różnych lokalizacji na całym świecie.   W tym samouczku będzie wykonywać proste test, aby upewnić się, że aplikacja jest dostępna.  Można również utworzyć pełny przewodnik, aby przetestować jego działanie szczegółowe. 

1. Wybierz **usługi Application Insights** , a następnie wybierz subskrypcję.  
1. Wybierz **dostępności** w obszarze **zbadaj** menu, a następnie kliknij przycisk **Dodaj test**.
 
    ![Dodaj test dostępności](media/app-insights-tutorial-alert/add-test.png)

2. Wpisz nazwę testu i pozostawić innych wartości domyślnych.  Żąda strony głównej aplikacji co 5 minut z 5 różne lokalizacje geograficzne. 
3. Wybierz **alerty** otworzyć **alerty** panelu, w którym można zdefiniować szczegóły dotyczące sposobu reagowania, jeśli test zakończy się niepowodzeniem. Typ adresu e-mail do wysyłania, gdy spełnione jest kryteria alertu.  Opcjonalnie można wpisać adres elementu webhook do wywołania po spełnieniu kryteriów alertu.

    ![Tworzenie testu](media/app-insights-tutorial-alert/create-test.png)
 
4. Wróć do panelu testu, a po kilku minutach powinna być widoczna wyników testu dostępności.  Kliknij nazwę testu, aby wyświetlić szczegóły w każdej lokalizacji.  Wykres punktowy pokazuje powodzenie i czasu trwania poszczególnych testów.

    ![Szczegóły testu](media/app-insights-tutorial-alert/test-details.png)

5.  Użytkownik może przejść do szczegółów szczegóły każdego z badań, klikając jej kropką w wykres punktowy.  W poniższym przykładzie pokazano szczegóły żądania nie powiodło się.

    ![Wynik testu](media/app-insights-tutorial-alert/test-result.png)
  
6. Jeśli są spełnione kryteria alertu, podobny do przedstawionego poniżej wiadomości e-mail są wysyłane do adresu określonego.

    ![Alert poczty](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Utwórz alert na podstawie metryk
Oprócz wysyłania alertów z test dostępności, można utworzyć alertu z wszystkie metryki wydajności, które są zbierane dla aplikacji.

2. Wybierz **alerty** z **Konfiguruj** menu.  Spowoduje to otwarcie panelu alerty Azure.  Mogą istnieć inne reguły alertów konfiguracji innych usług w tym miejscu.
3. Kliknij przycisk **Dodaj alert metryki**.  Spowoduje to otwarcie panelu, aby utworzyć nową regułę alertu.

    ![Dodawanie metryki alertu](media/app-insights-tutorial-alert/add-metric-alert.png)

4. Wpisz w **nazwa** alertu reguły, a następnie wybierz aplikację na liście rozwijanej dla **zasobów**.
5. Wybierz **Metryka** próbki.  Wykres zostanie wyświetlony wskazujący wartość tego żądania w ciągu ostatnich 24 godzin.  To ułatwia ustawienie warunku metryki.

    ![Dodaj regułę alertów](media/app-insights-tutorial-alert/add-alert-01.png)

6. Określ **warunku** i **próg** alertu. Jest to liczba razy Metryka musi zostać przekroczona alertu ma zostać utworzony. 
6. W obszarze **powiadomienia za pośrednictwem** Sprawdź **E-mail właściciele, współautorzy i czytelnicy** pole, aby wysyłać wiadomości do tych użytkowników, gdy warunek alertu jest spełniony i Dodaj adres e-mail żadnych dodatkowych adresatów.  Można również określić elementu webhook lub aplikacji logiki tutaj uruchamiana, gdy warunek jest spełniony.  Te można zmniejszyć wykryty problem lub 

    ![Dodaj regułę alertów](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Aktywne wysyłanie informacji
Alerty są tworzone w reakcji na zestaw określone w aplikacji i zwykle zarezerwować alerty krytyczne warunki, które wymagają natychmiastowej uwagi.  Aktywne można otrzymać informacje o aplikacji z aplikacji logiki, która jest uruchamiana automatycznie, zgodnie z harmonogramem.  Na przykład można mieć wiadomości e-mail wysyłane do administratorów codziennie o informacje podsumowujące, które wymaga dalszych oceny.

Aby uzyskać więcej informacji na temat tworzenia aplikacji logiki z usługą Application Insights, zobacz [zautomatyzować usługi Application Insights przetwarza przy użyciu aplikacji logiki](automate-with-logic-apps.md)

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już jak alert po wystąpieniu problemów, należy przejść do następny samouczek, aby dowiedzieć się, jak analizować, jak użytkownicy są interakcji z aplikacją.

> [!div class="nextstepaction"]
> [Zrozumienie użytkowników](app-insights-tutorial-users.md)