---
title: Aplikacja demonstracyjna usługi Azure Mobile Engagement | Dokumentacja firmy Microsoft
description: W tym artykule opisano, skąd pobrać, jak używać i korzyści wynikające ze stosowania aplikacji demonstracyjnej usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: b74afcadc52973a4d824afddc0abff6ac9f71d17
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-demo-app"></a>Aplikacja demonstracyjna usługi Azure Mobile Engagement
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Firma Microsoft już opublikowana aplikacja demonstracyjna usługi Azure Mobile Engagement dla **iOS**, **Android**, i **Windows** platformy, aby ułatwić znajdowanie przydatne zasoby i Dowiedz się więcej o Mobile Zaangażowania.

Aplikacja ułatwia:

* Łatwo łącza przydatne do usługi Mobile Engagement zasobów, takich jak pliki wideo, dokumentacji forum pomocy technicznej i gdzie można wywołać funkcji żądania.
* Środowisko próbki powiadomień, które są obsługiwane przez usługi Mobile Engagement i wykorzystać je we własnych aplikacji dla urządzeń przenośnych.
* Implementacja odwołanie umożliwia badanie sposobu implementacji usługi Mobile Engagement do własnych aplikacji. Aby dowiedzieć się do:
  
  * Zbierać dane analityczne.
  * Wdrożenie, takie jak zaawansowanych scenariuszy powiadomień typów *pełnoekranowy międzysegmentowe* lub *wyskakujących*.
  * Implementuje ankiet i ankiety.
  * Implementowanie scenariuszy dyskretnej wypychania danych i wypychania.   

## <a name="app-installation"></a>Instalacja aplikacji
Ta aplikacja jest dostępna w następujących sklepów z aplikacjami:

* **Uniwersalnych systemu Windows aplikacja demonstracyjna**:
  
  * Pobierz aplikację na [Sklepu Windows](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
  * Aplikacja został opracowany jako aplikacji uniwersalnych systemu Windows 10. Kod źródłowy jest dostępny na [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).
* **iOS demonstracją aplikacji**:
  
  * Pobierz aplikację na [sklepu Apple](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
  * Aplikacja został opracowany w systemu iOS w języku Swift. Kod źródłowy jest dostępny na [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).
* **Aplikacja demonstracyjna android**:
  
  * Pobierz aplikację na [sklepu Google Play](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
  * Kod źródłowy jest dostępny na [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Aplikacja demonstracyjna uniwersalnych systemu Windows][1]

![iOS demonstracją aplikacji][2]
![aplikacja demonstracyjna systemu Android][3]

## <a name="usage"></a>Sposób użycia
Możesz użyć tej aplikacji, w następujący sposób:

**Pobierz aplikację na urządzeniu z aplikacji sklepu łączy (starszych):**

> [!IMPORTANT]
> Nie potrzebujesz konta platformy Azure lub muszą nawiązać zaplecze aplikacji. Aplikacja działa niezależnie.
> 
> 

* Po utworzeniu aplikacji na urządzeniu, można przejść za pośrednictwem łączy w menu po lewej stronie, aby znaleźć przydatne zasoby dotyczące usługi Mobile Engagement.
* Dodaliśmy [źródło danych RSS usługi](https://aka.ms/azmerssfeed) do tej aplikacji, aby zawsze jest zaktualizowano o najnowsze aktualizacje produktu.
* Można również przejść przez przykładowe scenariusze powiadomień występować typ powiadomień, które są obsługiwane przez usługi Mobile Engagement dla każdej platformy. Te powiadomienia mogą wystąpić lokalnie — czyli, możesz kliknąć przyciski na ekranach pokazanie środowiska powiadomień do wysyłania powiadomień z platformy Mobile Engagement.

![Menu aplikacji dla systemu Windows][4]

![Menu aplikacji dla systemu iOS][5]
![menu aplikacji dla systemu Android][6]

**Pobieranie kodu źródłowego z usługi GitHub łączy (starszych):**

* Po pobraniu kodu źródłowego, można go otworzyć w środowisku programistycznym odpowiednich--XCode dla systemu iOS, Android Studio dla systemów Android i Visual Studio dla systemu Windows.
* Następnie należy wykonać naszych [podstawowe kroki integracji zestawu SDK](mobile-engagement-windows-store-dotnet-get-started.md) , dzięki czemu możesz połączyć tę aplikację z własne wystąpienie zaplecza usługi Mobile Engagement.
  * Należy skonfigurować parametry połączenia w aplikacji.
  * Należy również skonfigurować platformę powiadomień wypychanych dla aplikacji.
* Można zauważyć, że aplikacja jest instrumentowane przy użyciu usługi Mobile Engagement. W związku z tym po otwarciu aplikacji po podłączeniu go do wewnętrznej będzie widoczne w sesji użytkownika, działania, zdarzeń i tak dalej **Monitor** kartę.
* Można również wysyłać powiadomienia do tej aplikacji z własnego wystąpienia usługi Mobile Engagement, zamiast lokalnego powiadomienia.
  
  * Tutaj Dodaj urządzenia jako urządzenia testu przy użyciu **pobranie Identyfikatora urządzenia** element menu w aplikacji. Zapewnia to identyfikator urządzenia, które następnie zarejestrować jako urządzenia testu z wystąpieniem platformy zaplecza.
    
    ![Identyfikator urządzenia w systemie Windows][7]
    
    ![Identyfikator urządzenia w systemie iOS][8]
    ![identyfikator urządzenia w systemie Android][9]

## <a name="key-features-of-the-demo-app"></a>Najważniejsze funkcje aplikacja demonstracyjna
* Jak wspomniano wcześniej, z tą aplikacją, masz kluczowych zasobów dla usługi Mobile Engagement w dłoni. W menu po lewej stronie, można przejść za pośrednictwem łącza.
* Może wystąpić poza aplikacji powiadomień dla każdej platformy. Te powiadomienia mogą być dostarczane jako **tylko powiadomienie**, gdzie kliknięcie powiadomienia po prostu Otwiera ekran natywnych aplikacji (przy użyciu **bezpośrednich połączeń**)--lub jako **sieci Web Zawiadomienie**, gdzie mogą dostarczać dodatkowych zawartość HTML z zaplecza usługi Mobile Engagement będzie wyświetlana po kliknięciu powiadomienia.
  
    ![Powiadomienia w poziomie aplikacji][29]
* W systemach iOS należy zamknąć aplikację, aby wyświetlić powiadomienia wypychane poza aplikacji lub systemu. Można przyjrzeć się tutaj implementację dodawania **przycisków akcji**, takich jak te, które są dodawane do tego powiadomienia poza aplikacji dla *opinii* i *udziału* (, aby Użytkownik może zająć akcji bezpośrednio w powiadomień, się).
  
    ![Powiadomienia poza aplikacji w systemie iOS][11] ![Wyświetl powiadomienie poza aplikacji w systemie iOS][14]
* Opcje, które są obsługiwane w systemie Android, dodawania wielowierszowy tekst (**tekst Big**) lub obraz powiadomienia (**szerszej**) do powiadomienia, wraz z programem **przycisków akcji**(jak obsługiwane przez system iOS).
  
    ![Powiadomienia poza aplikacji w systemie Android][12] ![Wyświetl powiadomienie poza aplikacji w systemie Android][15]
* W systemie Windows 10 można zobaczyć wygląd powiadomienia na komputerze. To powiadomienie również zostaną wyświetlone w systemie Windows 10 **Centrum powiadomień**. Nie obsługuje dodawania **przycisków akcji** w tej chwili w zestawie Windows SDK.
  
    ![Powiadomienia poza aplikacji w systemie Windows][10] ![Wyświetl poza aplikacji w systemie Windows][13]
* Może wystąpić powiadomień "w aplikacji" domyślna dla każdej platformy. To środowisko dwuetapowej gdzie **powiadomień** okno jest wyświetlane jako pierwsze. Po kliknięciu go uruchomi pełny ekran **anonsu**wyświetlane na poniższym zrzucie ekranu. Zawartość tego anonsu pochodzi z wystąpieniem zaplecza usługi Mobile Engagement. Zestaw SDK zawiera szablony powiadomień i anonsów. Możesz można łatwo dostosować je, jak pokazano w tym aplikacja demonstracyjna, z uwzględnieniem kolorowanie i logo firmy Microsoft.  
  
    ![Powiadomienia w aplikacji w systemie Windows][16]
  
    ![Powiadomienia w aplikacji w systemie iOS][17]  ![Powiadomienia w aplikacji w systemie Android][18]
  
    **iOS**, **Android**
* Można również użyć **kategorii** funkcji usługi Mobile Engagement, aby dostosować ten domyślny. W aplikacji demonstracyjnej firma Microsoft została przedstawiona dwa podstawowe sposoby zmienić obsługi powiadomień. Należy pamiętać, że funkcja kategorii nie jest jeszcze obsługiwana w zestawie Windows SDK.
  
    **Pełnoekranowy międzysegmentowe:**
  
    ![Powiadomienie w aplikacji - międzysegmentowe kategorii][30]
  
    ![Kategoria międzysegmentowe w systemie iOS][21]     ![Kategoria międzysegmentowe w systemie Android][22]
  
    **Wyskakujące powiadomienie:**
  
    ![Powiadomienie w aplikacji - kategorii wyskakujących][31]
  
    ![Wyskakujące powiadomienia w systemie iOS][19]    ![Wyskakujące powiadomienia w systemie Android][20]

**iOS**, **Android**

* Usługa Mobile Engagement obsługuje również specjalistyczną odmianą powiadomienie w aplikacji o nazwie **sond**. Dzięki temu można wysyłać do użytkowników aplikacji segmentu szybkie ankiet. Możesz dodać pytania i opcje dla każdego pytania, tak jak na poniższym zrzucie ekranu. Będzie to następnie Pobierz wyświetlany jako powiadomienie w aplikacji użytkownika aplikacji.   
  
    ![Powiadomienia sondowania][32]
  
    ![Ankieta w systemie Windows][26]
  
    ![Badanie w systemie iOS][27]   ![Ankieta w systemie Android][28]

**iOS**, **Android**

* Usługa Mobile Engagement obsługuje również wysyłanie dyskretnej **wypychania danych** powiadomienia. Z te powiadomienia można wysyłać dane z usługą (np. danych JSON w poniższym przykładzie), który może obsługiwać w aplikacji i niektóre reakcję. Przykładem jest sposób Zmieniamy cen elementu selektywnie przy użyciu powiadomień wypychanych danych.
  
    ![Powiadomienia wypychane danych][33]
  
    ![Powiadomienia wypychane danych w systemie Windows][23]
  
    ![Powiadomienia wypychane danych w systemie iOS][24]  ![Powiadomienia wypychane danych w systemie Android][25]

**iOS**, **Android**

> [!NOTE]
> Szczegółowe instrukcje krok po kroku dla każdego powiadomienia te można wyświetlić, klikając **kliknij tutaj, aby uzyskać instrukcje dotyczące sposobu wysyłania powiadomień z platformy Mobile Engagement** na wszystkie próbki powiadomienia ekranu.
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
