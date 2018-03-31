---
title: Interfejs użytkownika usługi Azure Mobile Engagement — strona główna
description: Dowiedz się, jak zarządzać istniejącej aplikacji i projektów przy użyciu usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: aff578d2-40f6-43e4-b0ea-7d2674cb28a1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c0379e51a30927af88367fc687d8f8ce66eb827f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-your-existing-application-and-projects"></a>Jak zarządzać istniejącej aplikacji i projekty
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

W tym artykule opisano **Home** strony **Mobile Engagement** portalu. Możesz użyć **Mobile Engagement** portalu do monitorowania i zarządzania aplikacjami mobilnymi. Należy pamiętać, że można uruchomić przy użyciu portalu należy najpierw utworzyć **usługi Azure Mobile Engagement** konta. 

Aby uzyskać dostęp do strony głównej, kliknij przycisk **macierzystego** w lewym górnym rogu strony. Zawiera listę wszystkich aplikacji, które są częścią wybranej kolekcji. Na tej stronie tylko Zobacz szybki przegląd aplikacji.

Strona główna zawiera także wszystkie projekty zawierające dowolnej aplikacji, która jest na Twoim koncie. Należy pamiętać, że każdy mogą uzyskiwać dostęp do interfejsu użytkownika strony głównej przez utworzenie konta, ale należy przyznać uprawnienia do innych użytkowników w kolejności ich dostęp do niestandardowych aplikacji w **Moje projekty**.

Można również wyświetlić Wykres porównawczy dla wybranych aplikacji. Lub wybierz, aby wyświetlić Wykres porównawczy dla wybranej aplikacji w projekcie.

![Home1][0]

## <a name="my-applications"></a>Moje aplikacje
Szybki przegląd aplikacji służy do wybierania aplikacji, które chcesz otworzyć, aby wyświetlić opcje szczegółowe wstążki. Kliknij nazwę aplikacji, aby powrócić do ostatnio odwiedzonych lokalizacji wstążki w aplikacji lub kliknij ikonę narzędzi, aby przejść bezpośrednio do strony "Ustawienia" aplikacji. Możesz wyszukiwania, filtrować i sortować informacji wyświetlanych w tabelach aplikacji. Można również przeciągnij i upuść nagłówki kolumn, aby zmienić kolejność.

Między innymi zawiera omówienie aplikacji:

* **Nowe trend użytkowników**: ewolucja nowych użytkowników w ciągu ostatnich dwóch tygodni.
* **Aktywni użytkownicy**: liczba aktywnych użytkowników w ciągu ostatnich 30 dni.
* **Aktywni użytkownicy trend**: ewolucja aktywnych użytkowników w ciągu ostatnich dwóch tygodni.
* **Sesje**: sesja stanowi jedno użycie aplikacji przez użytkownika, od momentu rozpoczęcia korzystania, dopóki zatrzymuje użytkownika użytkownika.
* **Trendy sesji**: ewolucja sesji w ciągu ostatnich dwóch tygodni.

Po kliknięciu aplikacji, można uruchomić monitorowanie i zarządzanie aplikacjami za pomocą interfejsu użytkownika. Na przykład:    

* [Monitorowanie w czasie rzeczywistym danych aplikacji](mobile-engagement-user-interface-monitor.md)
* [Analizowanie danych historycznych dotyczących aplikacji](mobile-engagement-user-interface-analytics.md)
* [Tworzenie i zarządzanie segmentami użytkowników w celu identyfikowania wzorców użycia](mobile-engagement-user-interface-segments.md)
* [Docieranie do użytkowników aplikacji za pomocą powiadomień wypychanych](mobile-engagement-user-interface-reach.md)

## <a name="my-projects"></a>Moje projekty
Projekty służy do grupy aplikacji i nadaj uprawnienia innym użytkownikom dostęp do Twojej aplikacji. Należy nadać uprawnienia do innych użytkowników, podając adres e-mail. **Nowy projekt** przycisk służy do tworzenia nowego projektu tylko wprowadzając "name" i "opis" nowego projektu. Po utworzeniu projektu, należy kliknąć nazwę projektu, aby edytować nazwę i opis produktu i wybrać wszystkie aplikacje, które mają być wyświetlane w tym projekcie.

![Home6][60]

Role obejmują:

* **Podgląd**: A Podgląd jest użytkownik, który je tylko przeglądać aplikacje skojarzone z projektem. Przeglądarka analytics monitorować dane i dostępne przyjrzyj wyniki Reach. Przeglądarka nie zmienia żadnych informacji ani zarządzać aplikacjami lub użytkowników. Przeglądarka nie można utworzyć lub zmienić stan kampanii Reach.
* **Deweloper**: A deweloper jest użytkownik, który może robić wszystko to przeglądarka można zrobić, a także zarządzania aplikacjami. Deweloper może włączyć wyłączenie aplikacji, zmienić informacje o aplikacji (np. pakietu i podpis) i tworzyć kampanie Zasięgowe. Projektant nie może zarządzać użytkownikami.
* **Administrator**: Administrator jest użytkownik, który może robić wszystko dewelopera można zrobić, a także zarządzanie użytkownikami. Administrator można zaprosić użytkowników do dołączenia do projektu, można zmienić role użytkownika i można zmieniać informacji projektu. W obszarze "ustawienia" można również ustawić uprawnienia na poziomie aplikacji.

Kliknij projekt, aby wyświetlić wszystkie aplikacje, które są częścią tego projektu. Na poniższej ilustracji przedstawiono porównanie dla wybranych aplikacji.

![Home2][3]

## <a name="see-also"></a>Zobacz także
* [Pojęcia][Link 6]
* [Usługa Przewodnik rozwiązywania problemów][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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
