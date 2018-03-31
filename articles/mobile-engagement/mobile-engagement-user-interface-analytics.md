---
title: Interfejs użytkownika usługi Azure Mobile Engagement — analiza
description: Dowiedz się, jak i analizowanie danych historycznych dotyczących aplikacji za pomocą usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6b2533ac-b8ec-4e35-872c-d563895bdc0c
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fceae1ffff40fc525170121181e21726fe2bd3f7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-analyze-historical-data-about-your-application"></a>Jak analizować dane historyczne dotyczące aplikacji
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

W tym artykule opisano **ANALYTICS** karcie **Mobile Engagement** portalu. Możesz użyć **Mobile Engagement** portalu do monitorowania i zarządzania aplikacjami mobilnymi. Należy pamiętać, że można uruchomić przy użyciu portalu należy najpierw utworzyć **usługi Azure Mobile Engagement** konta.

Sekcja analizy interfejsu użytkownika zawiera zagregowane informacje na temat aplikacji na podstawie danych historycznych, która jest aktualizowana co 24 godziny. Informacje są wyświetlane na różnych pulpity nawigacyjne składa się z wykresy kołowe/paska/linii siatki i mapy. Dane można również pobrać jako pliki CSV. Większość tych informacji jest dostępnych w czasie rzeczywistym w sekcji monitorowanie interfejsu użytkownika i mogą również uzyskiwać z interfejsu API usługi Analytics.

> [!NOTE]
> Wiele sekcji **Mobile Engagement** zawierają interfejsu użytkownika portalu **Pokaż Pomoc** przycisku. Naciśnij ten przycisk, aby uzyskać dodatkowe informacje kontekstowe o sekcji.

## <a name="standard-and-custom-analytics"></a>Standardowe i niestandardowe analityka
Usługa Azure Mobile Engagement udostępnia zestaw podstawowa, standardowa analityczne informacji o aplikacjach, które mogą zostać wyświetlone na wykresie zaraz po zintegrowaniu aplikacji przy użyciu zestawu SDK. Usługa Azure Mobile Engagement umożliwia również zbieranie dalszej analizy niestandardowych informacji dotyczących zachowania użytkowników końcowych. Można to zrobić przez utworzenie planu tagu "Znaczniki niestandardowe (app-info)", utworzone na podstawie **ustawienia** tak, aby zebrać dodatkowe dane usługi Azure Mobile Engagement dla Ciebie.

## <a name="analytics"></a>Analiza
* Pulpit nawigacyjny: Zawiera ogólne informacje o nowych i uaktywnia użytkowników i ich trendów.
* Użytkownicy: Użytkownicy są identyfikowani na podstawie identyfikatora urządzenia: ten identyfikator jest unikatowy dla każdego urządzenia (jeden nowy użytkownik to w rzeczywistości jedno nowe urządzenie). Użytkownik jest uznawany co nowego w danym przedziale czasu, jeśli wykonał swoją pierwszą sesję w tym przedziale czasu. Użytkownik jest uznawany za zachowanego, jeśli wykonał co najmniej jedną sesję w ciągu ostatnich siedmiu dni. Aktywni użytkownicy są użytkownikami, wprowadzone w co najmniej jedną sesję w danym okresie. Można sortować według, co miesiąc, co tydzień, codziennie lub co godzinę okresów. Wszystkie wykresy wyglądać podobnie, ale pozwala Filtruj według różnych funkcji, takich jak wersja aplikacji, a następnie sortowania przez pewien czas. Standardowe informacje zebrane przez integrowania zestawu SDK obejmuje następujące elementy: aktywni użytkownicy, nowy użytkownik, liczbę sesji, długość każdej sesji, informacje techniczne dotyczące kraju, zmienne lokalne, lokalizacji, operator języka, urządzeń, oprogramowania układowego, sieci (Wi-Fi) , wersje aplikacji i zestawu SDK, używanych przez klientów. Informacje te można wyświetlić w czasie rzeczywistym z sekcji monitora.

> [!NOTE]
> Przedział czasu opiera się na datę z ustawień urządzeń użytkowników, dlatego użytkownik, którego telefon ma niepoprawnie ustawione Data mogą pojawiać się w przedziale czasu niewłaściwy.

* Przechowywania: Użytkownik jest uznawany za zachowanego w danym przedziale czasu, jeśli wykonał swoją pierwszą sesję w tym przedziale czasu. Przedziały czasu, w których zliczane są zachowani użytkownicy (i nowi użytkownicy) można zmienić na godziny, dni, tygodnie lub miesiące. Analizowanie przechowywania użytkowników jest oparty na stado. Kohorty to zbiór wszyscy nowi użytkownicy wykryto dla danego okresu (tj., zbiór użytkowników wykonywania ich pierwszej sesji podczas tego okresu). Używamy stado 1-dniowego, 2-dniowego, 4-dniowego, 7-dniowe lub 1-miesięcznego. Biorąc pod uwagę kohorty, każdego dnia 1, 2-dniowego, 4-dniowego, 7-dniowe, lub 1-miesięcznego, Azure Mobile Engagement oblicza zbiór wszystkich użytkowników, którzy należą do kohorty i są nadal aktywne (tj., zbiór użytkowników, którzy wykonali co najmniej jedną sesję w okresie). Ten zbiór użytkowników, nosi nazwę wersji kohorty. (Usługa azure Mobile Engagement można stwierdzić, ile użytkownicy nadal używają aplikacji, ale tylko magazynu określonych platform pomagają stwierdzić, ile użytkowników odinstalowywania programu iTunes app — na przykład GooglePlay, Sklep Windows, itp.).
* Sesje: Jedno użycie aplikacji przez użytkownika. Sesje są generowane na podstawie sekwencji działań wykonywanych przez użytkowników (działanie jest zwykle skojarzone z użyciem jednego ekranu aplikacji, a to może się różnić w zależności od sposobu został zintegrowany zestaw SDK aplikacji). Użytkownik naraz może wykonywać tylko jedno działanie: sesja rozpoczyna się, gdy użytkownik rozpoczyna swoje pierwsze działanie i zatrzymywana, gdy użytkownik kończy swoje ostatnie działanie. Jeśli użytkownik pozostaje więcej niż kilka sekund, bez wykonywania żadnych działań, sekwencja działań jest podzielony na dwie różne sesje.
* Działania: Nazwy każdego ekranu w aplikacji oraz długość czasu użytkownicy spędzają na każdym ekranie. Działania są niestandardowych opcji analityczne odpowiadające tagi "app-info" skonfigurowanych dla aplikacji:
* Ścieżka użytkownika: Pokazuje, jak użytkownicy nawigują między działaniami aplikacji (ekranami). Poruszając suwakiem, możesz dostosować poziom szczegółów. Niebieskie węzły reprezentują działania aplikacji. Ich rozmiar jest proporcjonalny do czasu, jaki użytkownik poświęcił na ich. Białe węzły reprezentują rozpoczęcie i zakończenie sesji. Czerwone węzły reprezentują awarie (crash). Połączenia reprezentują przejścia między działaniami aplikacji (lub między działaniami i awariami). Kliknij węzeł lub połączenie, aby wyświetlić etykietkę zawierającą więcej informacji na temat danych: czas spędzony na konkretnym ekranie, liczbę przejść i procent przejść z działania źródłowego do działania docelowego. (---60%---> B oznacza, że użytkownicy z działania A przechodzą do działania B 60% czasu.) Wykres można reorganizować dowolnie wyjaśnienia jego położenie jest zapisywane po każdej dokonanej zmianie. Możesz wyświetlić lub ukryć awarie (crash), aby rozjaśnić wykres.
* Zdarzenia: Określone akcje wykonywane przez użytkownika w aplikacji. Rozkład zdarzeń jest wyświetlany jako liczba zdarzeń na użytkownika na sesji. Zdarzenie reprezentuje natychmiastową akcję, na przykład kliknięcie przycisku lub odebranie powiadomienia. (Znaczenie zdarzeń jest zależna od jak zestaw SDK został zintegrowany z aplikacją). Zdarzenie może wystąpić podczas sesji, zadania lub być autonomiczne.
* Zadania: Podobne do zdarzeń, z wyjątkiem skupić się na długość akcji. Na przykład zadania można zorientować się informacje techniczne na temat jak długo trwa zawartości obciążenia lub wywołanie usługi sieci web. Można także wyświetlać, jak długo trwa użytkownika do wypełniania formularza, Utwórz konto lub dokonać zakupu. Zadanie reprezentuje czas trwania zadania, na przykład, czas trwania zadania pobierania lub czas wyświetlania banera na ekranie. (Znaczenie zadania Job jest zależna od jak zestaw SDK został zintegrowany z aplikacją). Zadania są zwykle skojarzone z zadaniami w tle, które są wykonywane poza zakresem sesji (tzn. bez żadnego działania użytkownika).
* Technicals: Informacji technicznych o urządzeniach użytkowników aplikacji można śledzić, takich jak ustawienia regionalne, operatora, sieci, urządzenia, oprogramowania układowego i ekranu rozmiar urządzeń użytkowników i wersji aplikacji i wersja zestawu SDK programu używana w aplikacji.
* Błędy: Informacje techniczne błędy wewnątrz aplikacji, które nie powodują awarię aplikacji. (Error) reprezentuje natychmiastowy problem, na przykład awaria sieci lub zła manipulacja. (Znaczenie zdarzeń jest zależna od jak zestaw SDK został zintegrowany z aplikacją). Wystąpił błąd może wystąpić podczas sesji, zadania lub być autonomiczne.
* Awarie: Informacje o błędach, które spowodować awarię aplikacji. Awaria jest nieoczekiwany warunek, w którym aplikacja przestaje wykonywać oczekiwane funkcje i musi zostać zatrzymana. Awaria jest zwykle spowodowana błędem w aplikacji.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Uzyskiwanie dostępu do przechowywania Przegląd
![Analytics3][12]

Omówienie przechowywania dzieli się na środku w kilka kart, każdy przeglądu przez okres przechowywania. Okres przechowywania 2-dniowego jest widoczna w przykładzie. Inne karty Pokaż okresów przechowywania 4-dniowego i 7 dni.

## <a name="understanding-the-retention-overview-cards"></a>Opis karty przechowywania — omówienie
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Każda karta składa się z 3 główne części:
1. 1: kohorty i okresu
2. 2-4: przechowywania dla bieżącego okresu
3. 5: wykres przebiegu w czasie historii

### <a name="here-is-detailed-information-about-each-element"></a>Poniżej przedstawiono szczegółowe informacje o każdym elemencie:
1. Kohorty i okres: typ kohorty daje tego nagłówka. W tym miejscu "2-dniowego okresu" oznacza, że zajmiemy zachowania użytkowników ponad 2 dni, użytkowników, które dotarły w okresie 2 dni oraz tego, czy łączą z następujących bloków 2 dni. W powyższym przykładzie uwzględnia działania użytkowników między 21 i 22 listopada.
2. Daje to współczynnik przechowywania za pośrednictwem 21 i 22 listopada dla użytkowników w 19 i 20 listopada. W tym miejscu było 1 aktywnego użytkownika między 21 i 22, za pośrednictwem 3, które były nowych użytkowników między 19th i 20.
3. Ten wskaźnik visual zapewnia te same informacje jak powyżej zaprezentowana graficznie. (Trzeci okręgu jest od liczby 33%). Kolor zapewnia dodatkowe informacje: zielony oznacza ta liczba rośnie z poprzednich obliczeń. Żółty oznacza stabilny, a czerwony oznacza zmniejszenie.
4. To ustawienie określa wartości używane do obliczania.
5. Jest to wykres przebiegu w czasie historii wartości przechowywania. Umożliwia on zobaczyć wartości w przeszłości, aby jak ewoluował szerokie widok.

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
