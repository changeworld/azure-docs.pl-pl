---
title: Wprowadzenie do usługi Azure Mobile Engagement dla aplikacji sieci Web | Microsoft Docs
description: Dowiedz się, jak używać usługi Azure Mobile Engagement z funkcją analizy i powiadomieniami wypychanymi na potrzeby aplikacji sieci Web.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 04afe53a-4caf-4c80-bd75-20cc630cd75c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: hero-article
ms.date: 06/01/2016
ms.author: piyushjo
ms.openlocfilehash: bb7a0e61b6d29c292642fd950aed7617de9b340d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Wprowadzenie do usługi Azure Mobile Engagement dla aplikacji sieci Web
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

W tym temacie pokazano, jak używać usługi Azure Mobile Engagement, aby zrozumieć Twój sposób użycia aplikacji sieci Web.

> [!NOTE]
> Usługa Azure Mobile Engagement zostanie wycofana w marcu 2018 r. i jest obecnie dostępna wyłącznie dla dotychczasowych klientów. Aby uzyskać więcej informacji, zobacz [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Dla tego samouczka wymagane są następujące elementy:

* Visual Studio 2015 lub dowolny inny edytor
* [Zestaw SDK sieci Web](http://aka.ms/P7b453)

Ten zestaw SDK sieci Web jest w wersji Preview i w tej chwili obsługuje tylko analizę, natomiast nie obsługuje jeszcze wysyłania powiadomień wypychanych w przeglądarce ani w aplikacji. 

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).
> 
> 

## <a name="setup-mobile-engagement-for-your-web-app"></a>Konfigurowanie usługi Mobile Engagement dla aplikacji sieci Web
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
Ten samouczek przedstawia „podstawową integrację”, tj. minimalny zestaw wymagany do zbierania danych.

Utworzymy podstawową aplikację sieci Web w programie Visual Studio, aby zademonstrować integrację, ale kolejne czynności można również wykonać za pomocą dowolnej aplikacji sieci Web utworzonej poza programem Visual Studio. 

### <a name="create-a-new-web-app"></a>Tworzenie nowej aplikacji sieci Web
W następujących krokach założono korzystanie z programu Visual Studio 2015, chociaż kroki są podobne także w przypadku wcześniejszych wersji programu Visual Studio. 

1. Uruchom program Visual Studio i na ekranie **Strona główna** wybierz pozycję **Nowy projekt**.
2. W oknie podręcznym wybierz pozycję **Sieć Web** -> **Aplikacja sieci Web ASP.Net**. Wypełnij pola **Nazwa****Lokalizacja** i **Nazwa rozwiązania**, a następnie kliknij przycisk **OK**.
3. W oknie podręcznym **Wybierz szablon** wybierz pozycję **Pusty** w obszarze **Szablony ASP.Net 4.5** i kliknij przycisk **OK**. 

Został utworzony nowy pusty projekt aplikacji sieci Web, z którym zostanie zintegrowany zestaw SDK sieci Web usługi Azure Mobile Engagement.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
1. Utwórz w swoim rozwiązaniu nowy folder o nazwie **javascript** i dodaj do niego plik JS zestawu SDK sieci Web **azure-engagement.js**. 
2. Do tego folderu javascript dodaj nowy plik o nazwie **main.js** zawierający poniższy kod. Nie zapomnij zaktualizować parametrów połączenia. Ten obiekt `azureEngagement` umożliwi uzyskiwanie dostępu do metod zestawu SDK sieci Web. 
   
        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };
   
    ![Program Visual Studio z plikami js][1]

## <a name="enable-real-time-monitoring"></a>Włączanie monitorowania w czasie rzeczywistym
Aby rozpocząć wysyłanie danych i upewnić się, że użytkownicy są aktywni, konieczne jest wysłanie co najmniej jednego działania do zaplecza usługi Mobile Engagement. Działanie w kontekście aplikacji sieci Web to strona sieci Web. 

1. Utwórz w swoim rozwiązaniu nową stronę o nazwie **home.html** i ustaw ją jako stronę początkową dla aplikacji sieci Web. 
2. Dołącz dwa skrypty JavaScript, które dodaliśmy wcześniej na tej stronie, przez dodanie poniższego kodu w tagu treści. 
   
        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>
3. Zaktualizuj tag treści, aby wywołać metodę `startActivity` obiektu EngagementAgent.
   
        <body onload="engagement.agent.startActivity('Home')">
4. Twoja strona **home.html** będzie wyglądać tak:
   
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

## <a name="connect-app-with-real-time-monitoring"></a>Łączenie aplikacji z funkcją monitorowania w czasie rzeczywistym
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

  ![][2]

## <a name="extend-analytics"></a>Poszerzanie analizy
Oto wszystkie metody dostępne obecnie w zestawie SDK sieci Web, których można używać do analizy:

1. Działania/strony sieci Web:
   
        engagement.agent.startActivity(name);
        engagement.agent.endActivity();
2. Zdarzenia
   
        engagement.agent.sendEvent(name, extras);
3. Błędy
   
        engagement.agent.sendError(name, extras);
4. Zadania
   
        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

