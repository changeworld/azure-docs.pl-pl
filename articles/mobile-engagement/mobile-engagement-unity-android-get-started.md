---
title: Wprowadzenie do usługi Azure Mobile Engagement na potrzeby wdrożenia aplikacji platformy Unity na urządzeniu z systemem Android
description: Dowiedz się, jak używać usługi Azure Mobile Engagement z funkcją analizy i powiadomieniami wypychanymi na potrzeby aplikacji platformy Unity wdrażanych na urządzeniach z systemem iOS.
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d5f0ef79-be00-4cec-97a5-a0b2fdaa380e
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: cd126fc656fc00667c4c09298409f920ab8160fc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>Wprowadzenie do usługi Azure Mobile Engagement na potrzeby wdrożenia aplikacji platformy Unity na urządzeniu z systemem Android
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

W tym temacie pokazano, w jaki sposób za pomocą usługi Azure Mobile Engagement można określić sposób użycia aplikacji oraz wysyłać powiadomienia wypychane do segmentowanych użytkowników aplikacji platformy Unity podczas wdrażania w urządzeniu z systemem Android.
W tym samouczku jako punkt startowy został użyty klasyczny samouczek Roll a Ball dla platformy Unity. Należy postępować zgodnie z krokami w tym [samouczku](mobile-engagement-unity-roll-a-ball.md) przed kontynuowaniem integracji usługi Mobile Engagement, która została przedstawiona w samouczku poniżej. 

Dla tego samouczka wymagane są następujące elementy:

* [Edytor platformy Unity](http://unity3d.com/get-unity)
* [Zestaw SDK platformy Unity usługi Mobile Engagement](https://aka.ms/azmeunitysdk)
* Zestaw SDK systemu Google Android

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).
> 
> 

## <a id="setup-azme"></a>Konfigurowanie usługi Mobile Engagement dla aplikacji systemu Android
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
1. Otwórz pliku skryptu **EngagementConfiguration** z folderu zestawu SDK, a następnie zaktualizuj zmienną **ANDROID\_CONNECTION\_STRING** parametrami połączenia uzyskanymi wcześniej z witryny Azure Portal.  
   
    ![][73]
2. Zapisz plik. 
3. Wykonaj polecenie **File -> Engagement -> Generate Android Manifest** (Plik -> Engagement -> Generowanie manifestu systemu Android). Jest to wtyczka dodana przez zestaw SDK usługi Mobile Engagement i kliknięcie jej spowoduje automatyczne zaktualizowanie ustawień projektu. 
   
    ![][74]

> [!IMPORTANT]
> Upewnij się, że to polecenie będzie wykonywane za każdym razem, gdy plik **EngagementConfiguration** zostanie zaktualizowany — w przeciwnym razie zmiany nie zostaną odzwierciedlone w aplikacji. 
> 
> 

### <a name="configure-the-app-for-basic-tracking"></a>Konfigurowanie aplikacji na potrzeby śledzenia podstawowego
1. Otwórz do edycji skrypt **PlayerController** dołączony do obiektu Player. 
2. Dodaj następującą instrukcję using:
   
        using Microsoft.Azure.Engagement.Unity;
3. Dodaj następujący kod do metody `Start()`:
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Wdrażanie i uruchamianie aplikacji
Przed próbą wdrożenia tej aplikacji platformy Unity na urządzeniu upewnij się, że na komputerze jest zainstalowany zestaw SDK systemu Android. 

1. Podłącz urządzenie z systemem Android do swojego komputera. 
2. Wybierz pozycję **File -> Build Settings** (Plik -> Ustawienia kompilacji). 
   
    ![][40]
3. Wybierz pozycję **Android** a następnie kliknij pozycję **Switch Platform** (Przełącz platformę).
   
    ![][51]
   
    ![][52]
4. Kliknij pozycję **Player settings** (Ustawienia odtwarzacza) i podaj prawidłowy identyfikator pakietu. 
   
    ![][53]
5. Na koniec kliknij pozycję **Build And Run** (Kompiluj i uruchom).
   
    ![][54]
6. Może pojawić się monit o podanie nazwy folderu do przechowywania pakietu systemu Android. 
7. Jeśli wszystko odbędzie się poprawnie, pakiet zostanie wdrożony na podłączonym urządzeniu i na telefonie będzie widoczna Twoja gra na platformie Unity. 

## <a id="monitor"></a>Łączenie aplikacji z funkcją monitorowania w czasie rzeczywistym
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Włączanie powiadomień wypychanych i funkcji komunikatów w aplikacji
[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

### <a name="update-the-engagementconfiguration"></a>Aktualizowanie pliku skryptu EngagementConfiguration
1. Otwórz plik skryptu **EngagementConfiguration** z folderu zestawu SDK i zaktualizuj zmienną **ANDROID\_GOOGLE\_NUMBER** **numerem projektu Google** uzyskanym wcześniej z portalu Google Cloud Developer. Jest to wartość ciągu, więc upewnij się, że została ujęta w cudzysłów. 
   
    ![][75]
2. Zapisz plik. 
3. Wykonaj polecenie **File -> Engagement -> Generate Android Manifest** (Plik -> Engagement -> Generowanie manifestu systemu Android). Jest to wtyczka dodana przez zestaw SDK usługi Mobile Engagement i kliknięcie jej spowoduje automatyczne zaktualizowanie ustawień projektu. 
   
    ![][74]

### <a name="configure-the-app-to-receive-notifications"></a>Konfigurowanie aplikacji na potrzeby otrzymywania powiadomień
1. Otwórz do edycji skrypt **PlayerController** dołączony do obiektu Player. 
2. Dodaj następujący kod do metody `Start()`:
   
        EngagementReachAgent.Initialize();
3. Teraz, gdy aplikacja jest zaktualizowana, wdróż i uruchom aplikację na urządzeniu zgodnie z poniższymi instrukcjami. 

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png
