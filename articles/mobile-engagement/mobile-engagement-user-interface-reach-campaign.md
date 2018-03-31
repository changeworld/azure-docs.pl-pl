---
title: Interfejs użytkownika usługi Azure Mobile Engagement - Reach kampanii
description: Laern sposób tworzenia i zarządzania nimi powiadomień wypychanych kampanie, za pomocą usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2fe124a2-a86f-4136-81ba-a9d298ec798a
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9d457a8d5f68f70f6bf7a5f086b5e1d65bd1ea95
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Jak utworzyć i zarządzać kampanii obejmujących wysyłanie powiadomień wypychanych
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Utwórz nową kampanię wypychania formułą złożonych, podając wszystkie informacje potrzebne do wysyłania powiadomień wypychanych umożliwia Reach część interfejsu użytkownika. Opcje kampanii wypychania się nieco różnić w zależności od typów cztery kampanii: anonsów, ankiety, dane wypychane i Kafelki (tylko Windows Phone).

### <a name="option-applies-to"></a>Opcja ma zastosowanie do:
* Języki: Wszystkie (anonse, ankiety, wypychania danych, Kafelki)
* Kampania: Wszystkie (anonse, ankiety, wypychania danych, Kafelki)
* Powiadomienie: Anonse, ankiety
* Zawartość: Unikatowy dla każdego typu kampanii
* Grupy odbiorców: Wszystkie (anonse, ankiety, wypychania danych, Kafelki)
* Przedział czasu: Kafelki anonse, ankiety,
* Testowanie: Wszystkie (anonse, ankiety, wypychania danych, Kafelki)

![Reach-Campaign1][20]

## <a name="languages"></a>Języki
Menu rozwijane języków umożliwia wysyłanie inna wersja programu wypychania do urządzenia, które są skonfigurowane do korzystania z różnych języków. Domyślnie wszystkie urządzenia będą otrzymywać tego samego wypychania niezależnie od języka, jakie są one skonfigurowane do korzystania. Użytkownicy ze swoimi urządzeniami, ustawić inny język otrzymają wersję językową domyślne powiadomienia Wypychanego. Wiele opcji kampanii wypychania zezwala na określanie alternatywnej zawartości dla każdego z dodatkowych języków, którą wybierzesz. 

![Reach-Campaign2][21]

### <a name="language-differences-apply-to"></a>Różnice języka dotyczą:
* Języki: Unikatowy języków można zaznaczyć oprócz język domyślny
* Kampania: Takie Same dla wszystkich języków
* Powiadomienie: Unikatowy dla każdego języka oprócz język domyślny
* Zawartość: Unikatowy dla każdego języka oprócz język domyślny
* Grupy odbiorców: Mogą być filtrowane według kryterium oddzielne języka
* Przedział czasu: tej samej dla wszystkich języków
* Testowanie: Mogą być wysyłane do każdego z języków naraz

### <a name="supported-languages"></a>Obsługiwane języki:
* Arabski (ar) 
* Bułgarski (bg) 
* Katalońskim (ca) 
* Chiński (zh) 
* Chorwacki (hr) 
* Czech (CS) 
* Duński (da) 
* Holenderski (Holandia) 
* Angielski (en) 
* Fiński (fi) 
* Francuski (fr) 
* Niemiecki (de) 
* Grecki (el) 
* Hebrajski (osoba) 
* Hindi (hi) 
* Węgierski (hu) 
* Indonezyjski (id) 
* Włoski (go) 
* Japoński (ja) 
* Koreański (ko) 
* Łotewski (lv) 
* Litewski (lt) 
* Malajski (macrolanguage) (ms) 
* Norwegian Bokmål (nb) 
* Polski (pl) 
* Portugalski (pt) 
* Rumuński (ro) 
* Rosyjski (ru) 
* Serbski (sr) 
* Słowacki (sk) 
* Słoweński (sl) 
* Hiszpański (es) 
* Szwedzki (sv) 
* Tagalski (tl) 
* Tajski (th) 
* Turecki (tr) 
* Ukraiński (Zjednoczone Królestwo) 
* Wietnamski (IV) 

## <a name="campaign"></a>Kampanii
Sekcja kampanii można ustawić nazwą i kategorią kampanii również tak, jakby planujesz Ignoruj odbiorców części kampanii wypychania i zamiast tego Wyślij kampanii za pomocą interfejsu API Reach (i niektóre elementy z niskim poziomem Push interfejsu API). Kategorie można przy użyciu szablonu niestandardowego powiadomień do sterowania powiadomienia w aplikacji na podstawie wstępnie zdefiniowanych ustawień. Można pobrać listy istniejących "kategorie" za pośrednictwem interfejsu API Reach.

> [!WARNING]
> Jeśli używasz opcji "Ignoruj odbiorców, wypychane będą wysyłane do użytkowników za pośrednictwem interfejsu API" w sekcji "Kampanii" kampanii Reach kampanii nie będzie automatycznie wysyłać, należy wysłać go ręcznie za pośrednictwem interfejsu API Reach.

![Reach-Campaign3][22]

### <a name="option-applies-to"></a>Opcja ma zastosowanie do:
* Nazwa: wszystkie
* Kategoria: Anonse, ankiety
* Ignoruj odbiorców, będą wysyłane powiadomienia wypychane do użytkowników za pośrednictwem interfejsu API: wszystkie

## <a name="notification"></a>Powiadomienia
Sekcja powiadomienia podstawowe ustawienia dla Twojego tym wypychania: tytuł powiadomienia Wypychanego, wiadomości, obrazu w aplikacji lub jeśli jest możliwe do odrzucenia. Wiele ustawień powiadomień są specyficzne dla platformy urządzenia. Można wybrać, czy Twoje wypychane będą wysyłane "w aplikacji" lub "poza aplikacją" lub oba. (Należy pamiętać, że użytkownicy mogą "opt w" lub "Wypisz" "poza aplikacji" wypchnięcia na System operacyjny poziomu na ich urządzeniach i usługi Azure Mobile Engagement nie będzie można zastąpić to ustawienie. Również należy pamiętać, że obsługuje interfejsu API Reach "w aplikacji" i wypchnięcia "poza aplikacji". Interfejs API wypychania może służyć do obsługi wypchnięć "poza aplikacją" zbyt.) Wypchnięcia można dostosować za pomocą obrazów lub zawartość HTML, w tym linków bezpośrednich do konsolidacji poza aplikacji lub w innej lokalizacji w aplikacji (zestaw SDK systemu Android 2.1.0 lub nowsze kategorii konwersji wymagane). Możesz zmienić identyfikator ikony lub z systemem iOS i wysyłania zawartości tekstu lub w sieci web (podręcznego łączem html zawartości, adres URL w innej lokalizacji wewnątrz lub na zewnątrz aplikacji). Można również dzwonek urządzenia z systemem Android lub też z wypychania. (Należy pamiętać, że konieczne będzie poprawny plik pierścienia lub Włącz wibrację urządzenia manifestu uprawnień zestawu SDK dla systemu Android.) Nie ma żadnych branży standardowe dla systemu Android "szerszej" rozmiary, od rozmiaru ekranu różnią się na każdym urządzeniu, ale obrazy 400 x 100 pracować z prawie każdego rozmiaru ekranu.

### <a name="delivery-types"></a>Typy dostarczania:
* Tylko poza aplikacją: powiadomienia są dostarczane, gdy użytkownik nie używa aplikacji.
* Poza tylko powiadomienie aplikacji wymaga certyfikatu od firmy Apple lub Google (certyfikat usługi APNS lub GCM).
* W aplikacji tylko: powiadomienie jest wyświetlane tylko wtedy, gdy aplikacja jest uruchomiona.
* Powiadomienia używa systemu dostarczania Capptain do nawiązania kontaktu z użytkownikiem. Można dostosować pełni visual układu/wyświetlania Twojego wypychania.
* Zawsze: Ta opcja zapewnia wysyłanie powiadomień, które każda aplikacja jest uruchomiona lub nie.

![Reach-Campaign4][23]

### <a name="option-applies-to"></a>Opcja ma zastosowanie do:
* Powiadomienie: Anonse, ankiety

## <a name="content"></a>Zawartość
Sekcja zawartości można użyć do zmiany zawartości anonse, ankiety, dane wypychane i Kafelki (tylko Windows Phone). Ustawienie zawartości kampanie wypychania jest specyficzne dla typu kampanii. 

### <a name="see-also"></a>Zobacz także
* [Dokumentacja interfejsu użytkownika — osiągnąć — wypychania zawartości][Link 29]

![Reach-Campaign5][24]

## <a name="audience"></a>Grupy odbiorców
W sekcji odbiorców umożliwia definiowanie standardowe listę elementów, aby ograniczyć kampanii limity kampanii na podstawie niestandardowych kryteriów. Standardowy zestaw opcji, aby ograniczyć odbiorców umożliwia Wypychanie do nowej lub starej użytkowników czy tylko użytkowników natywnych powiadomień wypychanych. Można również ustawić limit przydziału, aby ograniczyć liczbę użytkowników, którzy będą otrzymywać powiadomienia wypychanego. Można ręcznie edytować wyrażenie filtrowania kampanii uwzględnienie co najmniej jednego kryterium użytkowników docelowych. Można ręcznie wpisz wyrażenie odbiorców. Takie wyrażenie musi jawnie zdefiniować relację między kryteriami. Kryterium jest opisane przez identyfikator, który musi rozpoczynać się wielką literą i nie może zawierać spacji. Relacja między kryteriami może być oznaczone przy użyciu "i", "lub" operatory "nie", a także "(",")". Przykład: "Criterion1 lub (Criterion1 i nie Criterion2)".

> [!NOTE]
> Wiele osób uwzględnione w kampanii po stronie serwera przeznaczonych dla skanowania może być powolne, zwłaszcza, jeśli próby uruchomienia wielu kampanii w tym samym czasie.

* Jeśli to możliwe tylko należy uruchomić jeden kampanii.
* Co najwyżej tylko należy uruchomić cztery kampanii.
* Wypychanie tylko do aktywnych użytkowników (pole wyboru "Uwzględnij tylko użytkowników osiągalnych przy użyciu natywnych powiadomień wypychanych" i "Uwzględnij tylko aktywnych użytkowników"), aby tylko użytkownikom, którzy nadal mieć zainstalowanej aplikacji i korzystać z niego będzie musiał skanowania.
  Po zdefiniowaniu odbiorców służy przycisk Symulacja Aby dowiedzieć się, ile użytkownicy otrzymają ten wypychania. Spowoduje to obliczanie liczby znanych użytkowników potencjalnie docelowe w ramach tych odbiorców (jest to wartość szacowana na podstawie losowej próbki użytkowników). Należy pamiętać, że użytkownicy, którzy odinstalowali aplikację będą również należeli do tych odbiorców, ale nie można nawiązać połączenia.

### <a name="see-also"></a>Zobacz także
* [Nowe kryterium wypychania - Reach — dokumentacja interfejsu użytkownika][Link 28]

![Reach-Campaign6][25]

### <a name="edit-expression"></a>Edytowanie wyrażeń
![Reach-Campaign7][26]

### <a name="limit-your-audience-option-applies-to"></a>Limit Twojego odbiorców opcja ma zastosowanie do:
* Uwzględnij tylko podzestaw użytkowników: wszystkie (anonse, ankiety, wypycha dane, Kafelki)
* Uwzględnij tylko starych użytkowników: wszystkie (anonse, ankiety, wypycha dane, Kafelki)
* Uwzględnij tylko nowych użytkowników: wszystkie (anonse, ankiety, wypycha dane, Kafelki)
* Uwzględnij tylko bezczynnych użytkowników: Kafelki anonse, ankiety,
* Uwzględnij tylko aktywnych użytkowników: wszystkie (anonse, ankiety, wypycha dane, Kafelki)
* Uwzględnij tylko użytkowników osiągalnych przy użyciu natywnych powiadomień wypychanych: anonse, ankiety

## <a name="time-frame"></a>Przedział czasu
Sekcja okresie gdy powiadomienia wypychane będą wysyłane lub przedział czasu można pozostawić pole puste, aby natychmiast rozpocząć kampanii. Należy pamiętać, że przy użyciu strefy czasowej użytkownicy końcowi mogą zaczynają kampanii dzień wcześniej, niż oczekiwano dla użytkowników końcowych w Azji oraz wysyłanie małych partii wypchnięć aż wszystkich stref czasowych w świecie odpowiada przedział czasu dla kampanii. Przy użyciu strefy czasowej użytkownicy końcowi mogą powodować opóźnienia w kampaniach ponieważ czas na żądanie z telefonu przed rozpoczęciem powiadomienia wypychanego.

> [!NOTE]
> Kampanie, bez daty zakończenia może buforować wypchnięć lokalnie i nadal wyświetlane po ręcznie pełną kampanii. Aby uniknąć tego zachowania, określonej godziny zakończenia kampanii.

### <a name="see-also"></a>Zobacz także
* [Osiągnąć — jak Tos — Planowanie][Link 3] 

![Reach-Campaign8][27]

### <a name="settings-apply-to"></a>Ustawienia mają zastosowanie do:
* Przedział czasu: Kafelki anonse, ankiety,

## <a name="test"></a>Testowanie
Sekcja testu służy do wysyłania tego wypychania do urządzenia test przed zapisaniem kampanii. Jeśli skonfigurowano wszelkie niestandardowe języków dla kampanii, można przetestować powiadomienia wypychanego w każdego języka. Możesz skonfigurować urządzenia testu z "Moje konto".

> [!NOTE]
> Wypchnięcia nie po stronie serwera, które dane są rejestrowane, korzystając z przycisku do "test", tylko rejestrowane są dane dla kampanie wypychania prawdziwe.

### <a name="see-also"></a>Zobacz także
* [Dokumentacja interfejsu użytkownika — Moje konto][Link 14]

![Reach-Campaign9][28]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

