---
title: Wysyłanie alertów z usługi Azure Application Insights | Microsoft Docs
description: Samouczek dotyczący wysyłania alertów w odpowiedzi na błędy w aplikacji za pomocą usługi Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 300f0ddc8b738b5fd8578ed0b33cc15000c1098a
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101789"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorowanie kondycji aplikacji i wysyłanie powiązanych alertów za pomocą usługi Azure Application Insights

Usługa Azure Application Insights umożliwia monitorowanie aplikacji i wysyłania alertów, gdy aplikacja jest niedostępna lub występują w niej błędy bądź problemy z wydajnością.  Ten samouczek przeprowadzi Cię przez proces tworzenia testów stale sprawdzających dostępność aplikacji i wysyłających różnego rodzaju alerty w odpowiedzi na wykryte problemy.  Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie testu dostępności stale sprawdzającego odpowiedź aplikacji
> * Wysyłanie wiadomości e-mail do administratorów, gdy występuje problem
> * Tworzenie alertów na podstawie metryk wydajności 
> * Korzystanie z aplikacji logiki do wysyłania podsumowania danych telemetrycznych zgodnie z harmonogramem.


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z następującymi pakietami roboczymi:
    - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    - Tworzenie aplikacji na platformie Azure
    - Wdróż aplikację .NET na platformie Azure i [włącz zestaw Application Insights SDK](../../azure-monitor/app/asp-net.md). 


## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Tworzenie testu dostępności
Testy dostępności w usłudze Application Insights umożliwiają automatyczne testowanie aplikacji z różnych lokalizacji na całym świecie.   W tym samouczku wykonasz prosty test, aby się upewnić, że aplikacja jest dostępna.  Możesz również utworzyć szczegółowy przewodnik, aby dokładnie przetestować działanie aplikacji. 

1. Wybierz pozycję **Application Insights**, a następnie wybierz swoją subskrypcję.  
1. Wybierz pozycję **Dostępność** w menu **Zbadaj**, a następnie kliknij przycisk **Dodaj test**.
 
    ![Dodawanie testu dostępności](media/tutorial-alert/add-test.png)

2. Wpisz nazwę testu i pozostaw inne wartości domyślne.  Spowoduje to wysyłanie co 5 minut żądania strony głównej aplikacji z 5 różnych lokalizacji geograficznych. 
3. Wybierz pozycję **Alerty**, aby otworzyć panel **Alerty**, na którym można zdefiniować szczegóły dotyczące sposobu reagowania w przypadku niepowodzenia testu. Wpisz adres e-mail, na który zostanie wysłana wiadomość, gdy zostaną spełnione kryteria alertu.  Opcjonalnie można wpisać adres elementu webhook, który ma zostać wywołany po spełnieniu kryteriów alertu.

    ![Tworzenie testu](media/tutorial-alert/create-test.png)
 
4. Wróć do panelu testów. Po kilku minutach powinny zacząć się pojawiać wyniki testu dostępności.  Kliknij nazwę testu, aby wyświetlić szczegółowe informacje z poszczególnych lokalizacji.  Na wykresie punktowym wyświetlane są informacje, czy test zakończył się powodzeniem, oraz czas trwania poszczególnych testów.

    ![Szczegóły testu](media/tutorial-alert/test-details.png)

5.  Kliknięcie kropki na wykresie punktowym umożliwia przejście do szczegółów konkretnego testu.  W poniższym przykładzie przedstawiono szczegółowe informacje dotyczące żądania zakończonego niepowodzeniem.

    ![Wynik testu](media/tutorial-alert/test-result.png)
  
6. Jeśli zostaną spełnione kryteria alertu, na podany adres jest wysyłana wiadomość e-mail podobna do przedstawionej poniżej.

    ![Wiadomość e-mail z alertem](media/tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Tworzenie alertu na podstawie metryki
Oprócz wysyłania alertów z testu dostępności można utworzyć alert na podstawie dowolnych metryk wydajności zbieranych na potrzeby aplikacji.

2. Wybierz pozycję **Alerty** z menu **Konfiguruj**.  Spowoduje to otwarcie panelu alertów platformy Azure.  Mogą tu być wyświetlane inne reguły alertów skonfigurowane na potrzeby innych usług.
3. Kliknij pozycję **Dodaj alert metryki**.  Spowoduje to otwarcie panelu umożliwiającego utworzenie nowej reguły alertu.

    ![Dodawanie alertu dotyczącego metryki](media/tutorial-alert/add-metric-alert.png)

4. W polu **Nazwa** wpisz nazwę reguły alertu i z listy rozwijanej **Zasób** wybierz swoją aplikację.
5. Wybierz **metrykę** do próbkowania.  Zostanie wyświetlony wykres wskazujący wartość tego żądania w ciągu ostatnich 24 godzin.  Ułatwia to ustawienie warunku metryki.

    ![Dodawanie reguły alertu](media/tutorial-alert/add-alert-01.png)

6. Określ **Warunek** i **Próg** alertu. Jest to liczba wskazująca, ile razy musi nastąpić przekroczenie metryki, aby został utworzony alert. 
6. W obszarze **Metoda powiadomienia** zaznacz pole **Właściciele, współautorzy i czytelnicy poczty e-mail**, aby do tych użytkowników została wysłana wiadomość e-mail, kiedy zostanie spełniony warunek alertu, i dodaj adresy e-mail ewentualnych dodatkowych odbiorców.  W tym miejscu możesz również określić element webhook lub aplikację logiki, które będą uruchamiane, gdy warunek jest spełniony.  Za pomocą tych elementów można podjąć próbę ograniczenia wykrytego problemu. 

    ![Dodawanie reguły alertu](media/tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Proaktywne wysyłanie informacji
Alerty są tworzone w odpowiedzi na określony zestaw problemów zidentyfikowanych w aplikacji. Alerty są zwykle używane w przypadku krytycznych warunków wymagających natychmiastowej uwagi.  Informacje o aplikacji można otrzymywać w sposób proaktywny za pomocą aplikacji logiki uruchamianej automatycznie zgodnie z harmonogramem.  Codziennie może być na przykład wysyłana wiadomość e-mail do administratorów z informacjami podsumowującymi wymagającymi dalszej oceny.

Aby uzyskać więcej informacji na temat tworzenia aplikacji logiki za pomocą usługi Application Insights, zobacz [Automate Application Insights processes by using Logic Apps](../../azure-monitor/app/automate-with-logic-apps.md) (Automatyzacja procesów usługi Application Insights za pomocą aplikacji logiki)

## <a name="next-steps"></a>Następne kroki
Po poznaniu sposobów tworzenia alertów w odpowiedzi na problemy przejdź do następnego samouczka, aby się dowiedzieć, jak analizować sposób, w jaki użytkownicy wchodzą w interakcje z Twoją aplikacją.

> [!div class="nextstepaction"]
> [Interpretacja zachowań użytkowników](../../azure-monitor/learn/tutorial-users.md)