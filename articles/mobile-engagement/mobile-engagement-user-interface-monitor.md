---
title: Interfejs użytkownika usługi Azure Mobile Engagement — Monitor
description: Informacje o sposobie monitorowania w czasie rzeczywistym danych aplikacji przy użyciu usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: b91ad89a-b89d-4377-abb0-cc2d16a2836d
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b370a5db1acee37ce234f5ec3f004be20719cfea
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-monitor-real-time-data-about-your-application"></a>Jak monitorować dane w czasie rzeczywistym dotyczące Twojej aplikacji
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

W tym artykule opisano **MONITOR** karcie **Mobile Engagement** portalu. Możesz użyć **Mobile Engagement** portalu do monitorowania i zarządzania aplikacjami mobilnymi. Należy pamiętać, że można uruchomić przy użyciu portalu należy najpierw utworzyć **usługi Azure Mobile Engagement** konta. 

Monitor część interfejsu użytkownika analiz w czasie rzeczywistym informacje i pozwala ustawić alerty po osiągnięciu progów większość tych samych informacji, która jest dostępna w przeszłości [ANALYTICS](mobile-engagement-user-interface-analytics.md) część interfejsu użytkownika. Zobacz **słownik** w sekcji [pojęcia](http://go.microsoft.com/fwlink/?LinkId=525555) tematu definicje terminów i skrótów, analizy i monitorowanie (takie jak następujące: aktywnego użytkownika, nowy użytkownik zachowane użytkowników, sesji, ścieżki użytkownika Wykres, mapy użytkowników, adresy URL śledzenia, trendów, działania, zdarzenie, zadania, błąd, dodatkowe informacje, awarii i App-info).

> [!NOTE]
> Wiele sekcji **Mobile Engagement** zawierają interfejsu użytkownika portalu **Pokaż Pomoc** przycisku. Naciśnij ten przycisk, aby uzyskać dodatkowe informacje kontekstowe o sekcji.
> 
> 

## <a name="monitor---sessions-jobs-events-errors-and-crashes"></a>Monitor — sesji, zadania zdarzeń, błędów i awarii (Crash)
Widać, ilu użytkowników są obecnie w sesji i w określonym ekrany lub wykonanie określonych czynności. Można wyświetlić rozdzielonych sesji, zadania zdarzeń, błędów i awarii aktywności użytkownika. Możesz wyświetlić informacje o bieżącej i są wyświetlane dane z ostatniego godziny, dnia lub tygodnia. Można wyświetlić wszystkich danych w każdej kategorii lub sortowania określonej sesji, zadania, zdarzeń, błędów i awarii.  Monitorowanie na żywo jest przydatne do wykorzystania podczas zdarzenia, takie jak kampanii wypychania, jeśli istnieje uptick w akcji prawo po wysłaniu powiadomienia wypychane.

![Monitor1][14]  

## <a name="troubleshooting-with-monitor---events---details"></a>Rozwiązywanie problemów z monitora - zdarzenia — szczegóły
Generowanie zdarzeń w aplikacji z urządzenia testu i znalezieniem w szczegółach monitora - zdarzenia - jest najprostszym sposoby, aby znaleźć identyfikator urządzenia dla urządzenia testu i upewnij się, że integracji usługi Azure Mobile Engagement analizy, monitorowanie, i Segmenty współpracuje z aplikacji. Po utworzeniu Identyfikatora urządzenia urządzenia testu, można dodać go do urządzeń testów w "Moje urządzenia — konto". Jeśli nie można wygenerować zdarzeń, upewnij się, że usługi Azure Mobile Engagement jest poprawnie zintegrowane w aplikacji systemu Android/iOS/Web/Windows/Windows Phone przy użyciu zestawu SDK.

Aby uzyskać więcej informacji, zobacz: [dokumentacji zestawu SDK][Link 5]

![Monitor 2][15]  

## <a name="troubleshooting-with-monitor---crashes---details"></a>Rozwiązywanie problemów z monitorem - awarii — szczegóły
Możesz przejrzeć informacje o awariach aplikacji z szczegółów Monitora - awarii — w celu określenia przyczyny awarii aplikacji. Należy również wyszukać znane problemy z każdej wersji zestawu SDK w informacjach o wersji dla każdej wersji zestawu SDK dla systemu Android/iOS/Web/Windows/Windows Phone.

Aby uzyskać więcej informacji, zobacz: [dokumentacji zestawu SDK — informacje o wersji][Link 5]

![Monitor3][16]

## <a name="monitor---alerts"></a>Monitor — alerty
Można również określić warunków alertów, które będą automatycznie wysyłane do użytkownika za pośrednictwem poczty e-mail lub wiadomości błyskawicznych. (Wszystkie usługi zgodne z protokołem XMPP, takich jak GTalk firmy Google lub iChat firmy Apple są obsługiwane). Alerty są oparte na wykrywanie wstępnie zdefiniowane wartości progowej większości (>) i mniejszości (<) określoną liczbę sesji, zadania, zdarzeń, błędów lub awarii (Crash) na sekundę, minuty lub godzinę. Alerty można monitorować wszystkie działania danego typu lub po prostu monitorowanie określonego działania zadania, zdarzenia lub błąd. 

Można również określić minimalna częstotliwość wykrywania, który jest minimalna ilość musi upłynąć między dwoma powiadomieniami w ramach tego samego alertu upewnić się, że po wyzwoleniu alertu nigdy nie otrzymasz więcej niż 1 powiadomienia na interwał określony w minutach.

![Monitor4][17]

## <a name="see-also"></a>Zobacz także
* [Pojęcia][Link 6]
* [Usługa Przewodnik rozwiązywania problemów][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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
