---
title: Wysyłanie alertów z usługi Azure Application Insights | Microsoft Docs
description: Samouczek dotyczący wysyłania alertów w odpowiedzi na błędy w aplikacji za pomocą usługi Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 05285a177827cd0dd1e0e39e779a395ccfdfc0cd
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578768"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorowanie kondycji aplikacji i wysyłanie powiązanych alertów za pomocą usługi Azure Application Insights

Usługa Azure Application Insights umożliwia monitorowanie aplikacji i wysyłania alertów, gdy aplikacja jest niedostępna lub występują w niej błędy bądź problemy z wydajnością.  Ten samouczek przeprowadzi Cię przez proces tworzenia testów, aby stale sprawdzać dostępność aplikacji.

Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie testu dostępności stale sprawdzającego odpowiedź aplikacji
> * Wysyłanie wiadomości e-mail do administratorów, gdy występuje problem

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

Tworzenie [zasób usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Tworzenie testu dostępności

Testy dostępności w usłudze Application Insights umożliwiają automatyczne testowanie aplikacji z różnych lokalizacji na całym świecie.   W tym samouczku wykonasz test adresu url, aby upewnić się, że aplikacja sieci web jest dostępna.  Możesz również utworzyć szczegółowy przewodnik, aby dokładnie przetestować działanie aplikacji. 

1. Wybierz pozycję **Application Insights**, a następnie wybierz swoją subskrypcję.  

2. Wybierz **dostępności** w obszarze **zbadaj** menu, a następnie kliknij przycisk **Utwórz test**.

    ![Dodawanie testu dostępności](media/tutorial-alert/add-test-001.png)

3. Wpisz nazwę testu i pozostaw inne wartości domyślne.  Zaznacz to pole wyboru wyzwoli żądania dla adresu url aplikacji co 5 minut od pięciu różnych lokalizacjach geograficznych.

4. Wybierz **alerty** otworzyć **alerty** listy rozwijanej, w którym można zdefiniować szczegóły dotyczące sposobu reagowania, jeśli test zakończy się niepowodzeniem. Wybierz **niemal w czasie rzeczywistym** i ustawić stan na **włączone.**

    Wpisz adres e-mail, na który zostanie wysłana wiadomość, gdy zostaną spełnione kryteria alertu.  Opcjonalnie można wpisać adres elementu webhook, który ma zostać wywołany po spełnieniu kryteriów alertu.

    ![Tworzenie testu](media/tutorial-alert/create-test-001.png)

5. Wróć do panelu testu, wybierz wielokropek, a następnie Edytuj alert, aby wprowadzić konfiguracji alertu niemal w czasie rzeczywistym.

    ![Edytuj alert](media/tutorial-alert/edit-alert-001.png)

6. Ustawianie lokalizacji z błędami większa lub równa 3. Tworzenie [grupy akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) do skonfigurowania, który pobiera powiadomienia w przypadku włamania się próg alertu.

    ![Zapisywanie alertu interfejsu użytkownika](media/tutorial-alert/save-alert-001.png)

7. Po skonfigurowaniu alert, kliknij nazwę testu, aby wyświetlić szczegółowe informacje z każdej lokalizacji. Testy mogą być wyświetlane w obu wiersza wykres punktowy wykres format i wizualizacji sukcesów/niepowodzeń w zakresie w danym momencie.

    ![Szczegóły testu](media/tutorial-alert/test-details-001.png)

8. Możesz przejść do szczegółów dowolnego testu, klikając jej kropkę na wykresie punktowym. Spowoduje to uruchomienie widoku szczegółów transakcji end-to-end. W poniższym przykładzie przedstawiono szczegółowe informacje dotyczące żądania zakończonego niepowodzeniem.

    ![Wynik testu](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Kolejne kroki

Po poznaniu sposobów tworzenia alertów w odpowiedzi na problemy przejdź do następnego samouczka, aby się dowiedzieć, jak analizować sposób, w jaki użytkownicy wchodzą w interakcje z Twoją aplikacją.

> [!div class="nextstepaction"]
> [Interpretacja zachowań użytkowników](../../azure-monitor/learn/tutorial-users.md)