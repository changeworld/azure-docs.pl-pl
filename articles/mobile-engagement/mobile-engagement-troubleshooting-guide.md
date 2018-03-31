---
title: Przewodniki Rozwiązywanie problemów z usługą Azure Mobile Engagement
description: Podręcznik rozwiązywania problemów dotyczących usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 31134a29-a513-4e5e-b626-f6cf6fe04769
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 1242b2b796821856561a82d4a585b26693aadadd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Mobile Engagement — przewodnik rozwiązywania problemów
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

## <a name="introduction"></a>Wprowadzenie
Następujący przewodnik rozwiązywania problemów pomoże poznać głównych przyczyn niektóre często spotykanych problemów i będzie umożliwiają rozwiązywanie problemów z samodzielnie. 

## <a name="general"></a>Ogólne
Ogólnie rzecz biorąc zawsze upewnij się, że:

1. Upewnij się, że przejściu wszystkich kroków wymaganych do integracji, zgodnie z opisem w naszym [samouczki dotyczące rozpoczynania pracy](mobile-engagement-windows-store-dotnet-get-started.md)
2. Używasz najnowszej wersji platformy zestawów SDK. 
3. Test na rzeczywistego urządzenia i emulatora, ponieważ niektóre problemy są specyficzne dla emulatora tylko. 
4. Czy nie osiągnięto żadnych limitów/limity z usługi Mobile Engagement opisanych [tutaj](../azure-subscription-service-limits.md)
5. Jeśli nie masz możliwość połączenia z zapleczem usługi Mobile Engagement lub oglądanie nie ładowanych danych stale następnie upewnij się, że nie ma żadnych zdarzeń usługi trwającą sprawdzając [tutaj](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problemy z "Monitora"
### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Nie widzę urządzenie pojawia się na karcie Monitor
Karta Monitor zawiera urządzeń podłączonych do platformy Mobile Engagement w czasie rzeczywistym. Debugowania na emulator i urządzeń, należy zobacz co najmniej jedną sesję w tym miejscu. Jeśli aplikacja została dystrybuowana, będą widzieć miernika aktywne sesje odzwierciedlają urządzeń, które są połączone z platformy w czasie rzeczywistym. 

Jeśli nie widzisz urządzenia na karcie Monitor jest prawdopodobnie problem w integracji zestawu SDK. Niektóre typowe kroki podczas rozwiązywania problemów związanych z są następujące:

1. Upewnij się, że używasz prawidłowych parametrów połączenia w aplikacji mobilnej i jest z sekcji kluczy zestawu SDK i nie sekcję klucze interfejsu API. Parametry połączenia aplikacji mobilnej łączy się z wystąpieniem aplikacji usługi Mobile Engagement, w którym Twoje urządzenie będzie widoczne na karcie Monitor. 
2. Dla platformy systemu Windows — Jeśli strona zastępuje `OnNavigatedTo` metoda, upewnij się, że wywołanie `base.OnNavigatedTo(e)`.
3. Jeśli usługa Mobile Engagement są integracji istniejących aplikacji mobilnej, można także zapewnić, że nie brakuje żadnych kroków analizując kroki integracji zaawansowane [tutaj](mobile-engagement-windows-store-integrate-engagement.md)
4. Upewnij się, co najmniej jednego ekranu/działania są wysyłane przez zastąpienie strony z EngagementActivity w zależności od platformy działają zgodnie z opisem w [samouczki dotyczące rozpoczynania pracy](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>Widzę karcie Monitor przedstawiający sesji nawet gdy zostały odłączone lub zamknięty Moja aplikacja / emulatora.
Jeśli jesteś jedyną podłączone do platformy w tym momencie i używasz emulatora aby otworzyć aplikację następnie prawdopodobnie jest to spowodowane Osobliwości emulatora. Ogólnie rzecz biorąc należy się upewnić, że możesz wrócić do ekranie głównym na emulatorze na pomyślnie rozłączyć sesji aplikacji. Ponadto na platformie systemu Windows podczas debugowania w programie Visual Studio, konieczne może być upewnij się, że w programie Visual Studio, przejdziesz do **zdarzenia cyklu życia** paska menu i kliknij pozycję **zawieszenia** naprawdę zamknąć sesji. Zobacz [Samouczek Windows](mobile-engagement-windows-store-dotnet-get-started.md) szczegółowe informacje. 

## <a name="analytics-issues"></a>Problemy z "Analytics"
### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>I nie zwróciła żadnych danych / odświeżenia danych na karcie analityka
Dane analityczne są przeliczane regularnie i może potrwać maksymalnie 24 godziny na odświeżenie nie powiedzie się. Te dane nie są w czasie rzeczywistym i zobaczysz, że są one odświeżane w ramach tego 24-godzinnym przedziale czasu.
Upewnij się, mimo że wysyłasz działanie lub co najmniej jednego ekranu do zaplecza platformy przez albo zastępowanie co najmniej jednej strony z `EngagementActivity` lub wywoływania `SendActivity` explcitly. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Na karcie Analytics widzę niepoprawnie przechwyconych daty i godziny dla urządzenia
Okres czasu w celu wykonania analizy opiera się na datę z ustawienia urządzenia użytkowników. Dlatego Sprawdź, czy urządzenie ma Data prawidłowo ustawione. 

## <a name="segment-issues"></a>Problemy z "Segmentu"
### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Utworzono segment i jest wyświetlana jako nieaktywna lub nie są wyświetlane wszystkie dane
Tworzenie segmentu nie jest w czasie rzeczywistym w tej chwili. W tym samym czasie jest obliczana jako dane analityczne jest agregowana w związku z czym może potrwać maksymalnie 24 godziny. Należy sprawdzić ponownie później, ale w tym samym czasie należy upewnić się, czy aplikacji mobilnych w rzeczywistości wysyłania danych, na podstawie której są tworzące segmentów. Na przykład Jeśli zdarzenie powiedzieć "foo" nie jest wysyłane przez wszystkich urządzeń przenośnych, a następnie byłoby żadnych danych segmentu dla segmentu utworzony o nazwie EventName = foo jako kryterium. Należy także sprawdzić, czy integracji zestawu SDK, aby upewnić się, aplikacja mobilna poprawnie wysyłania danych. 

## <a name="reach-or-push-notifications-issues"></a>Problemy z "Zasięg" lub powiadomień wypychanych
### <a name="my-push-messages-are-not-being-delivered"></a>Nie są dostarczane wiadomości wypychanych
1. Spróbuj wysłać powiadomienia do urządzenia testu, aby najpierw upewnij się, że wszystkie składniki - aplikacji mobilnej, zestaw SDK i usługi są prawidłowo podłączone i mogła dostarczać powiadomienia wypychane. 
2. Zawsze wysyłaj najprostszym "powiadomienie poza aplikacji" najpierw za pośrednictwem kampanii, które nie zostało zaplanowane i ani dowolnego kryterium odbiorców określony. To jest ponownie potwierdzenia powiadomień połączenie działa poprawnie. 
3. Jeśli masz problemy w dostarczaniu powiadomienia w aplikacji następnie jest dobrym pierwszy krok w celu spróbuj najpierw wysłać powiadomienie poza aplikacji. 
4. Upewnij się, czy "natywnych powiadomień wypychanych" został poprawnie skonfigurowany dla aplikacji mobilnej. W zależności od platformy albo obejmuje się kluczy (Android i Windows) lub certyfikatów (iOS). Zobacz [interfejsu użytkownika — ustawienia](mobile-engagement-user-interface-settings.md)
5. Poza aplikacją powiadomienia można również zablokowany przez użytkownika za pomocą przenośnego systemu operacyjnego więc upewnij się, że nie jest to. 
6. Upewnij się, że nie konfigurujesz *Ignoruj odbiorców, powiadomienia wypychane będą wysyłane do użytkowników za pośrednictwem interfejsu API* opcji **kampanii** sekcji Reach kampanii, ponieważ zapewni to, że powiadomienia wypychane mogą być jedynie wysyłane za pośrednictwem interfejsów API. 
7. Upewnij się, zarówno urządzenia, połączenie za pośrednictwem sieci Wi-Fi i telefon sieci operator wyeliminować połączenia sieciowego w postaci źródłem problemów z testowania kampanii wypychania.
8. Upewnij się, system daty/godziny na urządzeniu/emulatorze jest poprawna, ponieważ wszystkie urządzenia zsynchronizowane zakłóca również możliwość wypychanie powiadomień usługi dostarczenia powiadomienia. 

Więcej platformy Rozwiązywanie problemów z instrukcjami poniżej:

1. **iOS** 
   
   * Upewnij się, że certyfikaty są prawidłowe i niewygasłe dla powiadomień wypychanych systemu iOS. 
   * Upewnij się, poprawne skonfigurowanie *produkcji* certyfikatu w aplikacji usługi Mobile Engagement. 
   * Upewnij się, że testowania na *prawdziwe, fizyczne urządzenie.* Symulatora systemu iOS nie można przetworzyć wiadomości wypychanych.
   * Upewnij się, że identyfikator pakietu został poprawnie skonfigurowany w aplikacji mobilnej. Zapoznaj się z instrukcjami [tutaj](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
   * Podczas testowania, przy użyciu dystrybucji "Ad Hoc" w profilu inicjowania obsługi administracyjnej przenośnych. Nie można otrzymać powiadomienie, jeśli aplikacja jest skompilowana przy użyciu "Debug"
2. **Android**
   
   * Upewnij się, że określono poprawny numer projektu w pliku AndroidManifest.xml aplikację mobilną, której następuje znak \n. 
     
           <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
   * Upewnij się, czy nie są brakujące lub nieprawidłowo skonfigurowane wszystkie uprawnienia w pliku manifestu systemu Android 
   * Sprawdź, czy numer projektu dodawanego do aplikacji klienta jest z tego samego konta, w którym masz klucz serwera GCM Server. Wszelkie niezgodność między tymi dwoma uniemożliwi Twojej wypchnięć przejście. 
   * W przypadku otrzymania systemu powiadomień, ale nie w aplikacji następnie przejrzyj [Określanie ikony dla powiadomień sekcji](mobile-engagement-android-get-started.md) jako prawdopodobnie nie określisz ikony jest poprawna w pliku manifestu systemu Android. 
   * Jeśli wysyłania powiadomień BigPicture, upewnij się, że jeśli masz serwery obrazów zewnętrznych, a następnie muszą mieć możliwość obsługi protokołu HTTP "GET" i "HEAD".
3. **Windows**
   
   * Upewnij się, że aplikacja ma skojarzone z prawidłową aplikację ze Sklepu Windows. W programie Visual Studio — będzie musiał kliknij prawym przyciskiem myszy projekt i wybierz opcję "Kojarzenie aplikacji z magazynu" i wybierz aplikację, której został utworzony w Sklepie Windows. Ta aplikacja ze Sklepu Windows powinna być taka sama, co z którym masz poświadczenia natywnych powiadomień wypychanych, aby skonfigurować w portalu usługi Mobile Engagement.
   * W przypadku otrzymania powiadomienia wypychane w poziomie aplikacji, ale powiadomienia nie w aplikacji przy użyciu `EngagementOverlay` integracji następnie upewnij się, Brak elementu głównego siatki na stronie. EngagementOverlay używa pierwszego elementu "Siatki" znajdzie się w pliku xaml, aby dodać dwie sieci web wyświetla na stronie. Jeśli chcesz zlokalizować, gdy zostanie ustawiona widoki sieci web, można zdefiniować siatki o nazwie "EngagementGrid", tak jak to jednak, należy upewnić się, że istnieje wystarczająca wysokość i szerokość dla dwóch kolejnych sieci web widoków, która wyświetli powiadomienie i następujące powiadomienie jako powiadomienie w aplikacji:
     
           <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>Po utworzeniu powiadomień wypychanych/anonsu/kampanii i nawet po ona wysłana do mnie powiadomienie, jest wyświetlany jako "Aktywny". Co to znaczy?
**Kampanii** utworzony w usłudze Mobile Engagement jest wywoływana, ponieważ jest długo działające znaczenie powiadomień wypychanych, jak połączyć nowych urządzeń do usługi mobile engagement platformy, zostaną automatycznie wysłane powiadomienia. Możesz Skonfiguruj, ile spełniają one kryterium, które można ustawić w kampanii. To nie jest plikiem zrzut pojedyncze powiadomienia Instalatora. Konieczne będzie ręczne kliknij **Zakończ** przycisk, aby zakończyć kampanię nie go wysyłanie dalszych powiadomienia. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>Po utworzeniu kampanii wypychania i otrzymuję powiadomień pomyślnie jednak zawsze, gdy I otwarcie aplikacji, pojawia się powiadomienie tego samego nawet wtedy, gdy użytkownik miał których wykonano akcje go przed?
Może nastąpić podczas testowania, a jeśli używasz emulatory lub niektórych testów framework, takich jak TestFlight. Co dzieje się w tym miejscu jest na wszystkie aplikacje uruchomione wystąpienie jest uzyskiwanie nowego DeviceID i wysyłania go do naszego wewnętrznej bazy danych, które powoduje platformy Mobile Engagement można traktować go jako nowe urządzenie i wysyłania powiadomienia. 

## <a name="getting-support"></a>Uzyskiwanie pomocy technicznej
Jeśli nie możesz rozwiązać ten problem samodzielnie możesz wykonywać następujące czynności:

1. Wyszukaj problemu w istniejących wątków na StackOverflow forum i [MSDN forum](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) i jeśli nie następnie zadać pytanie. 
2. Jeśli okaże się, brak następnie dodać/głos dla żądania w funkcji naszych [forum usługi UserVoice](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Jeśli masz Microsoft obsługują otwarte zdarzenie obsługi podając następujące informacje: 
   * Identyfikator subskrypcji platformy Azure
   * Platforma (np. systemy iOS, Android itp.)
   * Identyfikator aplikacji
   * Identyfikator kampanii (w przypadku problemów powiadomienia wypychane)
   * Identyfikator urządzenia
   * Wersja zestawu SDK usługi Mobile Engagement (np. v2.1.0 zestawu SDK systemu Android)
   * Szczegóły błędu z dokładną komunikat i scenariusza

