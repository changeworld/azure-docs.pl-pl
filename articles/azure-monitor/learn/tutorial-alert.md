---
title: Wysyłanie alertów z usługi Azure Application Insights | Microsoft Docs
description: Samouczek dotyczący wysyłania alertów w odpowiedzi na błędy w aplikacji za pomocą usługi Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 7195de1cf58e5dd2e1d0b49b309f3afc718cca92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77656266"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorowanie kondycji aplikacji i wysyłanie powiązanych alertów za pomocą usługi Azure Application Insights

Usługa Azure Application Insights umożliwia monitorowanie aplikacji i wysyłania alertów, gdy aplikacja jest niedostępna lub występują w niej błędy bądź problemy z wydajnością.  W tym samouczku można przejść przez proces tworzenia testów, aby stale sprawdzać dostępność aplikacji.

Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie testu dostępności stale sprawdzającego odpowiedź aplikacji
> * Wysyłanie wiadomości e-mail do administratorów, gdy występuje problem

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

Tworzenie [zasobu usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Tworzenie testu dostępności

Testy dostępności w usłudze Application Insights umożliwiają automatyczne testowanie aplikacji z różnych lokalizacji na całym świecie.   W tym samouczku wykonasz test adresu URL, aby upewnić się, że aplikacja sieci web jest dostępna.  Możesz również utworzyć szczegółowy przewodnik, aby dokładnie przetestować działanie aplikacji. 

1. Wybierz pozycję **Application Insights**, a następnie wybierz swoją subskrypcję.  

2. Wybierz **pozycję Dostępność** w menu **Badanie,** a następnie kliknij polecenie **Utwórz test**.

    ![Dodawanie testu dostępności](media/tutorial-alert/add-test-001.png)

3. Wpisz nazwę testu i pozostaw inne wartości domyślne.  Ten wybór spowoduje wyzwolenie żądań dla adresu URL aplikacji co 5 minut z pięciu różnych lokalizacji geograficznych.

4. Wybierz **alerty,** aby otworzyć listy rozwijanej **Alerty,** gdzie można zdefiniować szczegóły dotyczące reagowania, jeśli test zakończy się niepowodzeniem. Wybierz **pozycję Prawie w czasie rzeczywistym** i ustaw stan na **Włączone.**

    Wpisz adres e-mail, na który zostanie wysłana wiadomość, gdy zostaną spełnione kryteria alertu.  Opcjonalnie można wpisać adres elementu webhook, który ma zostać wywołany po spełnieniu kryteriów alertu.

    ![Tworzenie testu](media/tutorial-alert/create-test-001.png)

5. Wróć do panelu testowego, wybierz elipsy i edytuj alert, aby wprowadzić konfigurację alertu w czasie zbliżonym do rzeczywistego.

    ![Edytuj alert](media/tutorial-alert/edit-alert-001.png)

6. Ustaw lokalizacje, których nie powiodło się, na większą lub równą 3. Utwórz [grupę akcji,](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) aby skonfigurować, kto otrzymuje powiadomienia o przekroczenie progu alertu.

    ![Zapisz interfejs użytkownika alertu](media/tutorial-alert/save-alert-001.png)

7. Po skonfigurowaniu alertu kliknij nazwę testu, aby wyświetlić szczegóły z każdej lokalizacji. Testy można przeglądać zarówno na wykresie liniowym, jak i w formacie wykresu punktowego, aby wizualizować powodzenie/błędy dla danego zakresu czasu.

    ![Szczegóły testu](media/tutorial-alert/test-details-001.png)

8. Możesz przejść do szczegółów dowolnego testu, klikając jego kropkę na wykresie punktowym. Spowoduje to uruchomienie widoku szczegółów transakcji end-to-end. W poniższym przykładzie przedstawiono szczegółowe informacje dotyczące żądania zakończonego niepowodzeniem.

    ![Wynik testu](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Następne kroki

Po poznaniu sposobów tworzenia alertów w odpowiedzi na problemy przejdź do następnego samouczka, aby się dowiedzieć, jak analizować sposób, w jaki użytkownicy wchodzą w interakcje z Twoją aplikacją.

> [!div class="nextstepaction"]
> [Zrozumienie zachowań użytkowników](../../azure-monitor/learn/tutorial-users.md)
