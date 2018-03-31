---
title: Interfejs użytkownika usługi Azure Mobile Engagement — segmenty
description: Dowiedz się, jak utworzyć i zarządzanie segmentami użytkowników w celu identyfikowania wzorców użycia za pomocą usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6a4f2205-4a3c-406e-a04f-5e6f2a36653f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 53c5b1b672a0d7212a3d298c3e411a9cc6ac0807
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Tworzenie i zarządzanie segmentami użytkowników w celu identyfikowania wzorców użycia
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

W tym artykule opisano **segmentów** karcie **Mobile Engagement** portalu. Możesz użyć **Mobile Engagement** portalu do monitorowania i zarządzania aplikacjami mobilnymi.

Sekcja segmentów interfejsu użytkownika umożliwia pracę na podzielenie na podstawie inaczej i analizy, który można uzyskać z aplikacji i można również uzyskać dostęp za pośrednictwem interfejsu API segmentów użytkowników. Segmenty najpierw są obliczane 24 godziny po ich tworzenia i są one przeliczane co 24 godziny na podstawie najnowszych informacji analizy. Po obliczeniu segment on wyświetla "Od dnia na dzień historii" wykres każdego dnia.

> [!NOTE]
> Wiele sekcji **Mobile Engagement** zawierają interfejsu użytkownika portalu **Pokaż Pomoc** przycisku. Naciśnij ten przycisk, aby uzyskać dodatkowe informacje kontekstowe o sekcji.
> 
> 

## <a name="create-segments"></a>Tworzyć segmenty
Można utworzyć na podstawie kryteriów maksymalnie 10 w danym okresie się do 60 dni w przeszłości z sekcji analytics segment. Na przykład można utworzyć segment oparte na osoby, które mają wyświetlać określonych stron lub wyszukiwane określonej zawartości w Twojej aplikacji w ciągu ostatnich 10 dni. Te informacje są dostępne w sekcji analytics. Tak można go utworzyć segment, a następnie ponowne skonfigurowanie powiadomienie wypychane pod kątem tego podzbioru użytkowników można uzyskać je, aby wrócić do aplikacji. 

> [!NOTE]
> Po obliczeniu segmentu nie może być edytowany; można tylko sklonować (skopiowanych) lub zniszczony (usunięty). W tej samej aplikacji (z tej samej AppID) można sklonować segmentu i można sklonować również do innych aplikacji (z różnych AppID). 

 ![segments1][35] 

## <a name="examples-segments"></a>Przykłady segmentów
 ![segments2][36]

Segmenty umożliwiają segmentu użytkowników końcowych w aplikacji.
Segmentacji użytkowników jest ważne strategii marketingowej. Usługa Azure Mobile Engagement pozwala na pobieranie danych historycznych i tworzyć segmenty niestandardowych. To narzędzie zaawansowane umożliwia więcej informacji na temat obsługi klientów w aplikacji. Można łatwo analizować segmentów i używać segmentów jako miejsca docelowe wypychania.
Typowe przypadek użycia jest chcesz wysłać powiadomienie do zachęcić użytkowników końcowych do klasyfikowania aplikacji w magazynie wypychania. Zamiast wysyła powiadomienie do wszystkich użytkowników końcowych, można utworzyć segment, który określa tylko użytkownicy użyty aplikacji codziennie w ciągu ostatniego miesiąca i miały dużą interfejs użytkownika. Podczas wysyłania powiadomień wypychanych mniej, wysokiej docelowej, możesz uzyskać lepsze ROI.

 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Segmentów, które można tworzyć oparte na elementy główne usługi Azure Mobile Engagement:
* Zdarzenie: utworzyć segment tego cele jednego określonego zdarzenia aplikacji przypada więcej niż dwa razy w tygodniu. 
* Sesji: utworzenia segment użytkowników, którzy używali aplikacji więcej niż 5 razy w ostatnim tygodniu.
* Działania: Utwórz segment użytkowników, którzy używali jedną stronę lub zawartość większa lub mniejsza niż 10 czas ostatniego miesiąca.
* Zadania: Utwórz segment użytkowników, które zostały wykonane zadania więcej niż dwa razy dziennie.
* Awarii: segmentem wszystkich użytkowników, których awarii więcej niż 10 razy w ostatnim tygodniu. (Ten segment Przeprosiny lub nawet papieru wartościowego można push)!
* Błąd: segmentem wszystkich użytkowników, których wystąpił błąd więcej niż 100 razy ostatnich 3 dni.
* Informacje o aplikacji: utworzyć segment przeznaczonego dla niestandardowych informacji o aplikacji, które wystąpiły w ciągu ostatnich 25 dni.
  
  ![segments4][38]

Aby użyć segmentu w optymalny sposób, musisz przeprowadzić dostosowane integracji zestawu SDK w aplikacji z planem znakowania tagów "Informacje o aplikacji".
Następnie przejdź do strony głównej interfejsu, wybierz aplikację i kliknij w sekcji "Segmentów".

1. Wybierz sekcję "Segmentów".
2. Kliknij przycisk "nowy segment" przycisk, aby utworzyć nowy segment.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Rzeczywiste życia przykład: Tworzenie prostego segmentu, na podstawie informacji "Session"
Segmentem wszystkich użytkowników końcowych, którzy używali aplikacji w co najmniej 50 razy w ciągu ostatniego tygodnia. Z tego miejsca Znajdź tylko użytkownicy końcowi, którzy poświęcony co najmniej 30 sekund w aplikacji na sesję. Będzie to wyświetlenie wszystkich użytkowników końcowych, którzy mają pozytywne doświadczenia w aplikacji. Następnie segmentu utworzone może zostać wykorzystane do zmuszenia powiadomienie dla tych użytkowników końcowych, aby zadać je, aby oceniać aplikację w magazynie.

 ![segments5][39]

1. Nadaj nazwę segmentu, aby szybko znaleźć na liście "Segmentu".
2. Kliknij przycisk "Utwórz".
   
   ![segments6][40]

Wybierz sesji.

 ![segments7][41]

1. Wybierz okres "Ostatniego tygodnia".
2. Kliknij przycisk Dalej.
   
   ![segments8][42]
3. Wybierz Operator, który jest odpowiedni na liście: =; ≥, ≤.
4. Wprowadź liczbę, ma.
5. Wybierz wystąpienie ma. 
6. Kliknij przycisk Dalej.
   W tym przykładzie jest ustawiona, więc w ostatnim tygodniu pasujących użytkowników, których dokonano co najmniej 50 sesji.
   
   ![segments9][43]

W przypadku segmentacji sesji można długości sesji jako kryterium.

1. Wybierz Operator, z listy.
2. Podaj długości sesji.
3. Kliknij przycisk Dalej.
   W tym przykładzie mówi czy przez wszystkie sesje który zostały podzielone w sekcji wystąpienie, wybierz użytkowników, które poświęcony ponad 30 sekund na sesję.
   
   ![segments10][44]

Nazwa kryterium Aby pobrać go w pełną lejka, a następnie kliknij przycisk Zakończ.

 ![segments11][45]

Jeśli ukończono konfigurowanie kryterium, pojawi się w segmencie lejka.
Ponieważ segment jest oparta na dane analityczne, segmentów są obliczane raz dziennie.
W tym przykładzie 47,7% całkowitej użytkownicy końcowi zgodny z kryterium. Powinny one być użytkowników, którzy miały dobrej środowisko i będzie może zapewnić wyższy klasyfikacji, jeśli wypychanie ich powiadomienie z prośbą do klasyfikowania aplikacji w sklepie.

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

