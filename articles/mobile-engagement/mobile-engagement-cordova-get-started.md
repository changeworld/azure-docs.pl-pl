---
title: Rozpoczynanie pracy z usługą Azure Mobile Engagement dla oprogramowania Cordova/Phonegap
description: Dowiedz się, jak używać usługi Azure Mobile Engagement z funkcją analizy i powiadomieniami wypychanymi dla aplikacji Cordova/Phonegap.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: abe0ad6117cc2feb72ebe788d07b50e8f145174c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Rozpoczynanie pracy z usługą Azure Mobile Engagement dla oprogramowania Cordova/Phonegap
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

W tym temacie przedstawiono sposób użycia usługi Azure Mobile Engagement umożliwiający zbieranie informacji o użyciu aplikacji i wysyłanie powiadomień wypychanych do segmentowanych użytkowników aplikacji mobilnych opracowanych za pomocą oprogramowania Cordova.

W tym samouczku utworzymy pustą aplikację Cordova za pomocą komputera Mac, a następnie zintegrujemy zestaw Mobile Engagement SDK. Rozwiązanie będzie zbierać dane analityczne i odbierać powiadomienia wypychane za pomocą systemu Apple Push Notification System (APNS) dla systemów iOS oraz usługi Google Cloud Messaging (GCM) dla systemów Android. Wdrożymy aplikację na urządzeniu z systemem iOS lub Android w celu przetestowania rozwiązania. 

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

Dla tego samouczka wymagane są następujące elementy:

* Środowisko XCode, które możesz zainstalować ze sklepu Mac App Store (w przypadku wdrożenia w systemie iOS)
* [Zestaw SDK dla systemu Android oraz emulator](http://developer.android.com/sdk/installing/index.html) (w przypadku wdrożenia w systemie Android)
* Certyfikat powiadomień wypychanych (.p12), który można uzyskać z Centrum deweloperów firmy Apple dla rozwiązań systemu APNS
* Numer GCM projektu, który można uzyskać z konsoli dla deweloperów Google dla usługi GCM
* [Wtyczka Mobile Engagement Cordova](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Kod źródłowy i plik ReadMe dla wtyczki Cordova można znaleźć w serwisie [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova)
> 
> 

## <a id="setup-azme"></a>Konfigurowanie usługi Mobile Engagement dla aplikacji Cordova
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
Ten samouczek przedstawia „podstawową integrację”, tj. minimalny zestaw wymagany do zbierania danych i wysyłania powiadomień wypychanych. 

Aby zademonstrować integrację, utworzymy podstawową aplikację za pomocą systemu Cordova:

### <a name="create-a-new-cordova-project"></a>Tworzenie nowego projektu Cordova
1. Uruchom okno *Terminal* na komputerze Mac i wpisz następujące polecenie, które utworzy nowy projekt Cordova przy użyciu szablonu domyślnego. Upewnij się, że profil publikowania, którego możesz użyć do wdrożenia aplikacji systemu iOS, używa frazy „com.mycompany.myapp” jako identyfikatora aplikacji. 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Wykonaj następujące czynności, aby skonfigurować projekt dla systemu **iOS** i uruchomić go w narzędziu iOS Simulator:
   
        $ cordova platform add ios 
        $ cordova run ios
3. Wykonaj następujące czynności, aby skonfigurować projekt dla systemu **Android** i uruchomić go w emulatorze systemu Android. Upewnij się, że ustawienia emulatora Android SDK uwzględniają cel w formie interfejsów API firmy Google (Google Inc.) z ustawieniem CPU/ABI jako menedżerem ARM interfejsów API firmy Google.  
   
        $ cordova platform add android
        $ cordova run android
4. Dodaj wtyczkę konsoli Cordova. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
1. Zainstaluj wtyczkę Azure Mobile Engagement Cordova, podając wartości zmiennych w celu skonfigurowania wtyczki:
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Android Reach Icon* (Ikona zasięgu systemu Android): musi być nazwą zasobu bez rozszerzenia i prefiksu drawable (np. mynotificationicon), a plik ikony musi być skopiowany do projektu systemu Android (platforms/android/res/drawable)

*iOS Reach Icon* (Ikona zasięgu systemu iOS): musi być nazwą zasobu z rozszerzeniem (np. mynotificationicon.png), a plik ikony musi być dodany do projektu systemu iOS za pomocą środowiska XCode (przy użyciu menu Dodaj pliki)

## <a id="monitor"></a>Włączanie monitorowania w czasie rzeczywistym
1. W projekcie Cordova edytuj plik **www/js/index.js**, aby dodać wywołanie usługi Mobile Engagement i zadeklarować nowe działanie po odebraniu zdarzenia *deviceReady*.
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Uruchom aplikację:
   
   * **W przypadku systemu iOS**
     
       W oknie `Terminal` uruchom aplikację w nowym wystąpieniu narzędzia Simulator, wykonując następujące polecenie:
     
           cordova run ios
   * **W przypadku systemu Android**
     
       W oknie `Terminal` uruchom aplikację w nowym wystąpieniu emulatora, wykonując następujące polecenie:
     
           cordova run android
3. W dziennikach konsoli możesz zobaczyć następujące informacje:
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Łączenie aplikacji z funkcją monitorowania w czasie rzeczywistym
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Włączanie powiadomień wypychanych i funkcji komunikatów w aplikacji
Usługa Mobile Engagement umożliwia wchodzenie w interakcję z użytkownikami przy użyciu powiadomień wypychanych i komunikatów w aplikacji w kontekście kampanii. Ten moduł w portalu Mobile Engagement ma nazwę REACH.
Poniższe sekcje umożliwią skonfigurowanie aplikacji do ich odbierania.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Konfigurowanie poświadczeń wypychania dla usługi Mobile Engagement
Aby zezwolić usłudze Mobile Engagement na wysyłanie powiadomień wypychanych w Twoim imieniu, musisz przyznać jej dostęp do certyfikatu Apple iOS lub klucza interfejsu API serwera GCM Server. 

1. Przejdź do portalu Mobile Engagement. Upewnij się, że jesteś w aplikacji, której używamy dla celów tego projektu, a następnie naciśnij przycisk **Engage** (Włącz) widoczny u dołu:
   
    ![][1]
2. System przeniesie Cię do strony ustawień w portalu Engagement. Kliknij sekcję **Native Push** (Natywne powiadomienia wypychane):
   
    ![][2]
3. Skonfiguruj certyfikat systemu iOS/klucz interfejsu API serwera GCM Server
   
    **[iOS]**
   
    a. Wybierz element .p12, prześlij go i wpisz hasło:
   
    ![][3]
   
    **[Android]**
   
    a. Kliknij ikonę edycji przed **kluczem interfejsu API** w sekcji Ustawienia GCM, a następnie wklej klucz serwera GCM Server w wyświetlonym oknie i kliknij przycisk **OK**. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Włączanie powiadomień wypychanych w aplikacji Cordova
Edytuj plik **www/js/index.js**, aby dodać wywołanie do usługi Mobile Engagement i zażądać powiadomień wypychanych oraz zadeklarować mechanizm obsługi:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Uruchamianie aplikacji
**[iOS]**

1. Użyjemy środowiska XCode do skompilowania i wdrożenia aplikacji na urządzeniu, aby przetestować powiadomienia wypychane, ponieważ system iOS umożliwia tylko wysyłanie powiadomień wypychanych do rzeczywistego urządzenia. Przejdź do lokalizacji, w której utworzono projekt Cordova, a następnie przejdź do folderu **...\platforms\ios**. Otwórz plik natywny .xcodeproj w środowisku XCode. 
2. Skompiluj i wdróż aplikację Cordova na urządzeniu z systemem iOS, korzystając z konta, które ma profil aprowizacji zawierający certyfikat przesłany do portalu Mobile Engagement oraz identyfikator aplikacji odpowiadający identyfikatorowi podanemu podczas tworzenia aplikacji Cordova. Możesz wyewidencjonować identyfikator *Bundle identifier* w pliku **Resources\*-info.plist** w środowisku XCode, aby zapewnić zgodność identyfikatorów. 
3. Zobaczysz na urządzeniu standardowe wyskakujące okno systemu iOS z informacją o tym, że aplikacja prosi o uprawnienie do wysyłania powiadomień. Przyznaj uprawnienie. 

**[Android]**

Możesz użyć emulatora do uruchomienia aplikacji systemu Android, ponieważ powiadomienia GCM są obsługiwane w emulatorze systemu Android. 

    cordova run android

## <a id="send"></a>Wysyłanie powiadomienia do aplikacji
Teraz utworzymy prostą kampanię powiadomień wypychanych, która wyśle powiadomienie wypychane do aplikacji uruchomionej na urządzeniu:

1. Przejdź do karty **Reach** (Zasięg) w portalu Mobile Engagement.
2. Kliknij przycisk **New Announcement** (Nowy anons), aby utworzyć kampanię powiadomień wypychanych.
   
    ![][6]
3. Podaj dane wejściowe, aby utworzyć kampanię dla systemu **[Android]**.
   
   * Podaj **nazwę** kampanii. 
   * Dla ustawienia **Delivery Type** (Typ dostawy) ustaw *System notification* (Powiadomienie systemowe) *Simple* (Proste).
   * Wybierz **Delivery time** (Czas dostawy) jako *„Any Time”* (W dowolnym momencie).
   * Podaj **tytuł** powiadomienia. Będzie to pierwszy wiersz powiadomienia wypychanego.
   * Wpisz **komunikat** powiadomienia, który będzie jego główną treścią. 
     
     ![][11]
4. Podaj dane wejściowe, aby utworzyć kampanię dla systemu **[iOS]**.
   
   * Podaj **nazwę** kampanii. 
   * Wybierz **Delivery time** (Czas dostawy) jako *„Out of app only”* (Tylko poza aplikacją).
   * Podaj **tytuł** powiadomienia. Będzie to pierwszy wiersz powiadomienia wypychanego.
   * Wpisz **komunikat** powiadomienia, który będzie jego główną treścią. 
     
     ![][12]
5. Przewiń w dół i w sekcji zawartości wybierz opcję **Notification only** (Tylko powiadomienie).
   
    ![][8]
6. [Opcjonalnie] Możesz też podać adres URL akcji. Upewnij się, że adres wykorzystuje schemat adresów URL podany podczas konfigurowania zmiennej **AZME\_REDIRECT\_URL** wtyczki, np. *myapp://test*.  
7. Na tym kończy się konfigurowanie najbardziej podstawowej kampanii. Teraz przewiń ponownie w dół i kliknij przycisk **Create** (Utwórz), aby zapisać kampanię.
8. Na koniec **aktywuj** swoją kampanię.
   
    ![][10]
9. W ramach kampanii na urządzeniu lub w emulatorze powinno pojawić się powiadomienie wypychane. 

## <a id="next-steps"></a>Następne kroki
[Przegląd wszystkich metod dostępnych przy użyciu zestawu Cordova Mobile Engagement SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

