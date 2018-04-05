---
title: Wprowadzenie do usługi Azure Mobile Engagement na potrzeby wdrożenia aplikacji platformy Unity na urządzeniu z systemem iOS
description: Dowiedz się, jak używać usługi Azure Mobile Engagement z funkcją analizy i powiadomieniami wypychanymi na potrzeby aplikacji platformy Unity wdrażanych na urządzeniach z systemem iOS.
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8270a56daf8e69eede5b3d14b4ff88e0911da11a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Wprowadzenie do usługi Azure Mobile Engagement na potrzeby wdrożenia aplikacji platformy Unity na urządzeniu z systemem iOS
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

W tym temacie pokazano, jak za pomocą usługi Azure Mobile Engagement można określić sposób użycia aplikacji oraz wysyłać powiadomienia wypychane do segmentowanych użytkowników aplikacji platformy Unity podczas wdrażania na urządzeniu z systemem iOS.
W tym samouczku jako punkt startowy został użyty klasyczny samouczek Roll a Ball dla platformy Unity. Należy postępować zgodnie z krokami w tym [samouczku](mobile-engagement-unity-roll-a-ball.md) przed kontynuowaniem integracji usługi Mobile Engagement, która została przedstawiona w samouczku poniżej. 

Dla tego samouczka wymagane są następujące elementy:

* [Edytor platformy Unity](http://unity3d.com/get-unity)
* [Zestaw SDK platformy Unity usługi Mobile Engagement](https://aka.ms/azmeunitysdk)
* Edytor XCode

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Konfigurowanie usługi Mobile Engagement dla aplikacji systemu iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
### <a name="import-the-unity-package"></a>Importowanie pakietu Unity
1. Pobierz [pakiet Unity usługi Mobile Engagement](https://aka.ms/azmeunitysdk) i zapisz go na komputerze lokalnym. 
2. Wybierz pozycję **Assets -> Import Package -> Custom Package** (Zasoby -> Importuj pakiet -> Pakiet niestandardowy), a następnie wybierz pakiet, który został pobrany w kroku powyżej. 
   
    ![][70] 
3. Upewnij się, że są zaznaczone wszystkie pliki, a następnie kliknij przycisk **Import** (Importuj). 
   
    ![][71] 
4. Gdy import zakończy się pomyślnie, w projekcie będą widoczne zaimportowane pliki zestawu SDK.  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>Aktualizowanie pliku skryptu EngagementConfiguration
1. Otwórz pliku skryptu **EngagementConfiguration** z folderu zestawu SDK i zaktualizuj zmienną **IOS\_CONNECTION\_STRING** parametrami połączenia uzyskanymi wcześniej z witryny Azure Portal.  
   
    ![][73]
2. Zapisz plik. 

### <a name="configure-the-app-for-basic-tracking"></a>Konfigurowanie aplikacji na potrzeby śledzenia podstawowego
1. Otwórz do edycji skrypt **PlayerController** dołączony do obiektu Player. 
2. Dodaj następującą instrukcję using:
   
        using Microsoft.Azure.Engagement.Unity;
3. Dodaj następujący kod do metody `Start()`:
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Wdrażanie i uruchamianie aplikacji
1. Podłącz urządzenie z systemem iOS do komputera. 
2. Wybierz pozycję **File -> Build Settings** (Plik -> Ustawienia kompilacji). 
   
    ![][40]
3. Wybierz pozycję **iOS**, a następnie kliknij pozycję **Switch Platform** (Przełącz platformę).
   
    ![][41]
   
    ![][42]
4. Kliknij pozycję **Player settings** (Ustawienia odtwarzacza) i podaj prawidłowy identyfikator pakietu. 
   
    ![][53]
5. Na koniec kliknij pozycję **Build And Run** (Kompiluj i uruchom).
   
    ![][54]
6. Może pojawić się monit o podanie nazwy folderu do przechowywania pakietu systemu iOS. 
   
    ![][43]
7. Jeśli wszystko odbędzie się poprawnie, projekt zostanie skompilowany, a następnie konieczne będzie otworzenie go w aplikacji XCode. 
8. Upewnij się, że **identyfikator pakietu** w projekcie jest prawidłowy.  
   
    ![][75]
9. Teraz uruchom aplikację w edytorze XCode, dzięki czemu pakiet zostanie wdrożony na podłączonym urządzeniu i na telefonie będzie widoczna Twoja gra na platformie Unity. 

## <a id="monitor"></a>Łączenie aplikacji z funkcją monitorowania w czasie rzeczywistym
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Włączanie powiadomień wypychanych i funkcji komunikatów w aplikacji
Usługa Mobile Engagement umożliwia wchodzenie w interakcję z użytkownikami i modułem REACH przy użyciu powiadomień wypychanych i komunikatów w aplikacji w kontekście kampanii. Ten moduł w portalu Mobile Engagement ma nazwę REACH.
Nie ma konieczności dodatkowego konfigurowania aplikacji w celu otrzymywania powiadomień — została ona już skonfigurowana.

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
