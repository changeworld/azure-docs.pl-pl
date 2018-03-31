---
title: Interfejs użytkownika usługi Azure Mobile Engagement - Reach porady
description: Przegląd interfejsu użytkownika dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0c536d378023ee4a9675bb2d67262fb485c8052b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Jak rozpocząć pracę przy użyciu i zarządzanie wypchnięcia dotrzeć do użytkowników końcowych
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Po użyciu zestawu SDK jest w pełni zintegrowana w swojej aplikacji, możesz rozpocząć pracę przy użyciu sekcji Reach interfejsu użytkownika powiadomień wypychanych do użytkowników aplikacji.  

## <a name="do-your-first-push-notification-campaign"></a>Czy pierwszy kampanię powiadomień wypychanych
* Upewnij się, że zasięg jest zintegrowana z aplikacji przy użyciu zestawu SDK. 
* Wybierz aplikację

![First1][1]

* Przejdź do sekcji "Reach" i kliknij przycisk "nowy anons"

![First2][2]

* Utwórz nową kampanię i nadaj jej nazwę
  
![First3][3]

* Wybierz sposób powiadomienie ma zostać dostarczony, w aplikacji tylko

![First4][4]

* Tworzenie wiadomości, którą chcesz dystrybuować

![First5][5]

* Tytuł mogą zapisywać na powiadomienia (opcjonalnie).
* Zapisanie zawartości wiadomości wypychanych.
* Możesz przekazać obraz. Należy pamiętać, że rozmiar pliku nie może przekraczać 32 768 bajtów.
* Istnieje również możliwość wybrania dalsze opcje, ale dla zespołu w tym samouczku, należy sprawdzić, które później.
* Wybierz typ zawartości tylko jako powiadomienie

![First6][6]

* Utworzyć kampanię powiadomień wypychanych i zostanie wyświetlony na liście kampanii.

![First7][7]

## <a name="test-your-push-notification-campaign"></a>Testowanie kampanię powiadomień wypychanych
![Test1][8]

* Zarejestruj urządzenie.
* Kliknij pole wyboru urządzenia, które chcesz dystrybuować.
* Kliknij przycisk "Test", aby wysłać powiadomienia wypychanego na urządzeniu.

![Test2][9]

* Aktywowania kampanii

![Test3][10]

* Teraz, po utworzeniu kampanii wystarczy aktywować go w celu powiadomienia, aby zostać przeniesiony do użytkowników.

## <a name="send-personalized-pushes"></a>Wysyłanie spersonalizowanych Wypchnięć
* W tym przykładzie tworzy wypychania, w którym kod niestandardowy rabatów jest wprowadzany do powiadomień wypychanych.

![Personalize1][11]

Personalizacja działa przez zastąpienie znacznika przez z tagu informacje o aplikacji tak, będzie konieczne upewnij się, że użytkownik ma odpowiednie app-info najpierw zdefiniowane. W tym przykładzie docelowe użytkownicy będą mieć tag informacje o aplikacji o nazwie rebate_code zdefiniowane.
Jak widać powyżej zawartości powiadomienia wypychanego obejmuje $ znacznika {rebate_code}, które będzie wskazywać, że ma zostać zastąpione przez rzeczywistej zawartości tagu informacje o aplikacji.

> [!WARNING]
> Tag informacje o aplikacji nie jest zdefiniowany dla użytkownika, użytkownik nie będą otrzymywać powiadomienia wypychanego.

* Wynik

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Można dodatkowo spersonalizować tekst powiadomienia
![Personalize3][13]

* W tym tytuł powiadomienia
* I treści wiadomości.
* Wybierz typ anonsu (tekstu, widoku lub widok sieci Web)

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Treść anonsu może być również spersonalizowanych z:
* Adres URL akcji powinna chcesz dostosować strony docelowej
* Tytuł,
* Treść wiadomości.

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Rozróżnianie Your Push Notification (w lub poza aplikacją)
* Wybierz typ powiadomienia push, wybierz aplikację, przejdź do sekcji "Osiągnąć", wybierz lub utworzysz kampanii wypychania i przejdź do sekcji "Powiadomienia".
* Kliknij na "dostarczanie tryb".
* Kliknij pole wyboru "Ogranicz działania", jeśli chcesz, aby powiadomienia występuje na określonych działaniami (ekranami).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Dostawy "Tylko poza aplikacją"
![Differentiate2][16]

"Tylko poza aplikacją" dostawy zapewnia powiadomień wypychanych, gdy aplikacja zostanie zamknięta. To powiadomienie wypychane standardowa.
Po wybraniu "tylko poza aplikacją", musi już podano certyfikatów od platformy, która tworzy aplikację na (APN lub GCM).

### <a name="see-also"></a>Zobacz także
* [Certyfikatów Apple Push Notification Service —](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging — certyfikat](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>"w aplikacji tylko" dostawy
![Differentiate3][17]

"W aplikacji tylko" dostawy zawiera powiadomień wypychanych, gdy aplikacja jest uruchomiona.
Dla tego powiadomienia nie musisz przejść za pomocą systemu APNS i usługi GCM.
System dostarczania w aplikacji można użyć do użytkowników końcowych.
Można w pełni dostosować powiadomienia i zdecydować, w którym działanie (ekran) powiadomienia będą wyświetlane.

### <a name="anytime-delivery-mode"></a>"W każdej chwili" dostawy
Można wybrać dostawy "W każdej chwili", zapewnia, że do osiągnięcia użytkownika końcowego, czy aplikacja jest uruchomiona lub nie.
Po wybraniu opcji "W każdej chwili" musi już podano certyfikatów od platformy, która tworzy aplikację (APN lub GCM). 

## <a name="schedule-a-push-campaign"></a>Harmonogram kampanii wypychania
### <a name="plan-to-start-a-campaign"></a>Zamierzasz uruchomić kampanii
![Shedule1][18]

Jest 21 marca z i możesz mieć anons, aby i być to planowane dla 22 z marca o północy. Nie masz pozostanie przed interfejs w celu wypychania! Można zaplanować z wyprzedzeniem dokładne minutę, który będą wysyłane powiadomienia.

* Usuń zaznaczenie "None" wyboru i wybierz czas rozpoczęcia 
* Wybierz datę i godzinę, o których chcesz uruchomić kampanii wypychania.

### <a name="plan-to-end-a-campaign"></a>Planowanie zakończenia kampanii
![Shedule2][19]

Ma kampanii można zatrzymać na 25 z marca godzinie 3:00, ale wiesz, że użytkownik nie będzie to zrobić.
Nie masz pozostanie przed interfejs służący do wypychania! Można zaplanować z wyprzedzeniem dokładne minutę kampania zostanie zatrzymana.

* Kliknij na "Brak" wyboru lub wybierz czas zakończenia
* Wybierz datę i godzinę, o których chcesz zakończyć kampanię wypychania.

### <a name="end-a-campaign-manually"></a>Ręcznie zakończenia kampanii
![Shedule3][20]

Domyślnie "None" są zaznaczone pola wyboru.
Oznacza to, że kampania rozpocznie się natychmiast Aktywuj ją w sekcji reach i zakończy się, gdy przestanie w sekcji reach.

> [!NOTE]
> Kampanie utworzone bez daty zakończenia przechowywania naciśnięcie lokalnie na urządzeniu i pokazać przy następnym otwarciu aplikacji, nawet jeśli ręcznie zakończenia kampanii.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Ulepszanie powiadomienie wypychane z widoku tekstu
### <a name="what-is-a-text-view"></a>Co to jest widoku tekstu?
![TextView1][21]

Widok tekstu jest okno podręczne z zawartością tekstu. Są one wyświetlane po użytkownik końcowy kliknie powiadomienie wypychane.
Widok tekstu umożliwia prezentowanie więcej zawartości do użytkownika końcowego. Dotyczy to również możliwość przedstawienia wywołanie akcji, takich jak przechodzenie do strony aplikacji, przekierowanie do sklepu, otwarcie strony sieci web, wysyłanie wiadomości e-mail, rozpoczynania wyszukiwania z lokalizacją geograficzną itp...

### <a name="example-text-view"></a>Przykład: Wyświetlanie tekstu
* W sekcji "Reach" utworzyć kampanię powiadomień wypychanych i nadaj nazwę kampanii

![TextView2][22]

* Wpisz komunikat, który będzie wyświetlany na powiadomienia.
* Wybierz typ zawartości "text" anonsu

![TextView3][23]

> [!NOTE]
> Po naciśnięciu widoku tekstu, zawsze pochodzi z powiadomienie najpierw. 

* Zdefiniuj tekst (po dokonaniu wyboru anons zawartości tekstowej, pojawi się podsekcja możliwość definiowania tekst, który ma być wyświetlana.)

![TextView4][24]

* Wpisz tytuł, który będzie wyświetlany w górnej części wiadomości.
* Wpisz głównego zawartość widoku tekstu.
* Zapisu zawartości, który będzie wyświetlany na przycisku akcji (przycisku akcji umożliwia aplikacji, aby wprowadzić określonej akcji, takich jak otwieranie stron aplikacji, przekierowanie do sklepu z aplikacjami lub dowolnych źródeł, które można podać).
* Zapisu zawartości, który będzie wyświetlany na przycisku zakończenia (klikając przycisk Zakończ, widoku tekstu znikną.)
* Utworzyć kampanię powiadomień wypychanych i pojawi się na liście kampanii.

![TextView5][25]

* Aktywować kampanię powiadomień wypychanych do wysyłania widoku tekstu dla użytkowników.

![TextView6][26]

* Wynik

![TextView7][27]

* Użytkownik otrzymuje powiadomienie i kliknij go.
* Wyświetl tekst jest wyświetlany jako okno podręczne, co pozwala na interakcję z nią.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Ulepszanie powiadomienie wypychane z widoku sieci Web
### <a name="what-is-a-web-view"></a>Co to jest widok sieci Web?
![WebView1][28]

Widok sieci web jest okno podręczne z zawartością sieci web. Są one wyświetlane, gdy użytkownik końcowy kliknie powiadomienie wypychane.
Widok sieci Web pozwala poszerzyć interakcję z użytkownikami końcowymi.
Dotyczy to również możliwość przedstawienia wywołanie akcji, takich jak przekierowanie do sklepu z aplikacjami, otwarcie strony sieci web, wysyłanie wiadomości e-mail, rozpoczynania wyszukiwania lokalizacją geograficzną, itp...

### <a name="example-web-view"></a>Przykład: Widoku sieci Web
* Utworzyć kampanię powiadomień wypychanych w sekcji "Reach" i nadaj nazwę kampanii.

![WebView2][29]

* Wpisz komunikat, który będzie wyświetlany na powiadomienia.
* Wybierz typ zawartości anonsu jako "web".

![WebView3][30]

### <a name="about-announcement-types"></a>O typach anonsu:
* Tylko powiadomienie: jest proste powiadomień w wersji standard. Oznacza to, że jeśli użytkownik go kliknie, bez dodatkowego widoku będą wyświetlane, ale zostanie przeprowadzona tylko akcję skojarzoną do niego.
* Tekst anonsu: to powiadomienie, które angażujący użytkownikowi przyjrzeć widoku tekstu.
* Sieci Web anonsu: to powiadomienie, które angażujący użytkownikowi przyjrzeć widoku sieci web.
  Wybierz zawartość "Anonsu Web".

> [!NOTE]
> Po naciśnięciu widoku sieci web, zawsze pochodzi z powiadomienie najpierw.

* Zdefiniuj zawartość sieci web (po dokonaniu wyboru zawartości sieci web anonsu, pojawi się podsekcję możliwość definiowania zawartość widoku sieci web, które mają być wyświetlane.)

![WebView4][31]

* Wpisz tytuł, który będzie wyświetlany w górnej części komunikatu (opcjonalnie).
* Wpisz tutaj swój kod HTML.
* Kliknij źródło edycji przycisk Tryb przełącznika edition i zobacz, jak wygląda.
* Zapisu zawartości, który będzie wyświetlany na przycisku akcji (przycisku akcji umożliwia aplikacji, aby wprowadzić określonej akcji, takich jak otwieranie stron aplikacji, przekierowanie do sklepu lub dowolny rodzaj źródeł, które można podać).
* Zapisu zawartości, który będzie wyświetlany na przycisku zakończenia (klikając przycisk Zakończ, widoku sieci web zostanie usunięty).
* Wynik

![WebView5][32]

* Użytkownik otrzyma powiadomienie i kliknij go.
* Wyświetl tekst jest wyświetlany jako okno podręczne, co pozwala na interakcję z nią.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md

