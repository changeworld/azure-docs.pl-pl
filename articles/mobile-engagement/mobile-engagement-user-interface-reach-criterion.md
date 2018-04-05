---
title: Interfejs użytkownika usługi Azure Mobile Engagement — kryterium Reach
description: Dowiedz się, jak używać kryteriów strategicznych do wysyłania kampanie wypychania do wybranego podzbioru użytkowników przy użyciu usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a4ed03a0-55b1-4dd8-b0bd-c475005afb66
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 2adf473c6acea0f128eb14e2616748ff29d5d762
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Jak używać kryteriów strategicznych do wysyłania kampanie wypychania do wybranego podzbioru użytkowników
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

Elementów docelowych odbiorców według określonych kryteriów przy użyciu przycisku "Nowe kryterium" jest jednym z najbardziej zaawansowanych pojęcia związane z usługi Azure Mobile Engagement pomaga odpowiednich wysyłania powiadomień wypychanych że klienci będą odpowiadać zamiast wszyscy spamem. Można ograniczyć na podstawie standardowego kryteriów odbiorców i symulować wypchnięciu do określenia, ile osób będą otrzymywać powiadomienia.

**Zobacz też:**

* [Nową kampanię wypychania dokumentacji - Reach - interfejsu użytkownika][Link 27]

## <a name="audience-criteria-can-include"></a>Kryteriów odbiorców narzędzia mogą obejmować:
* ** Technicals: ** celem może być oparte na te same informacje techniczne można sprawdzić w sekcji Analytics i monitora. **Zobacz też:** [dokumentacji interfejsu użytkownika — Analytics][Link 15], [dokumentacji interfejsu użytkownika — Monitor][Link 16]
* **Lokalizacja:** aplikacji, które używają "Raportowanie lokalizacji czasu rzeczywistego" z Grodzenia umożliwia lokalizacja geograficzna jako kryterium docelowych odbiorców z lokalizacji GPS. Wywołanie "Lokalizacji opóźnieniem raportowania" również docelowych odbiorców z lokalizacji telefonu komórkowego ("Raportowanie lokalizacji czasu rzeczywistego" i "Opóźnieniem raportowanie lokalizacji obszaru" musi być aktywowany z zestawu SDK). **Zobacz też:** [dokumentacji zestawu SDK - iOS - integracji][Link 5], [dokumentacji zestawu SDK - Android - integracji][Link 5]
* **Opinie reach:** można kierować na ich podstawie reach powiadomienia za pośrednictwem opinii reach anonse, ankiety i wypychanie danych odbiorcy. Pozwala to lepszą docelowych odbiorców po dwóch lub trzech osiągnąć kampanie, niż można po raz pierwszy. On również można odfiltrować użytkowników, którzy już odebrał powiadomienie o podobnych zawartości, ustawiając kampanii na nie wysyłane do użytkowników, którzy otrzymali już określonej kampanii poprzedniej. Można nawet wyklucz użytkowników, który są uwzględniane określonej kampanii, które są nadal aktywne odbieranie nowych Wypchnięć. **Zobacz też:** [zawartości przekazywanej - Reach — dokumentacja interfejsu użytkownika][Link 29]
* **Instalacja śledzenia:** można śledzić informacje, na podstawie której użytkownicy zainstalowanych aplikacji. **Zobacz też:** [dokumentacji interfejsu użytkownika — ustawienia][Link 20]
* **Profil użytkownika:** możesz docelowym na podstawie informacji użytkownika standardowego i możesz docelowym oparte na informacji niestandardowych aplikacji, które zostały utworzone. Dotyczy to również użytkowników, którzy są aktualnie zalogowany i użytkowników, którzy mają odpowiedzi na pytania określonych, które zwróciły je ustawić w aplikacji zamiast podobnie jak ich reakcja na poprzednich kampanii. Wszystkie informacje aplikacji zdefiniowane pokazu aplikacji się na tej liście.
* Segmenty: Można również docelowym oparte na segmenty, które zostały utworzone na podstawie określonego użytkownika zachowania zawierający wiele kryteriów. Wszystkie segmenty zdefiniowane dla aplikacji wyświetlane na tej liście. **Zobacz też:** [dokumentacji interfejsu użytkownika — segmenty][Link 18]
* **Informacje o aplikacji:** znaczniki informacje o aplikacji niestandardowe można tworzyć w lokalizacji "Ustawienia", do śledzenia zachowania użytkownika. **Zobacz też:** [dokumentacji interfejsu użytkownika — ustawienia][Link 20]

## <a name="example"></a>Przykład:
Jeśli chcesz wypychać powiadomienia tylko do podzbioru użytkownicy wykonali akcję zakupu w aplikacji.

1. Przejdź do strony ustawień aplikacji, wybierz z menu "App-info" i wybierz pozycję "Nowe informacje o aplikacji"
2. Zarejestruj nowe informacje o aplikacji logiczną o nazwie "inAppPurchase"
3. Zwiększyć bezpieczeństwo aplikacji, ustawianie tych informacji o aplikacji "true", gdy użytkownik wykonuje pomyślnie zakupu w aplikacji (przy użyciu sendAppInfo ("inAppPurchase",...) funkcja)
4. Jeśli nie chcesz to zrobić z aplikacji, należy go z poziomu zaplecza przy użyciu interfejsu API urządzenia)
5. Następnie wystarczy utworzyć anonsu, przy użyciu kryterium ograniczanie odbiorców dla użytkowników mających "inAppPurchase" wartość "true")

> [!NOTE]
> Docelowy, na podstawie kryteriów innych niż tagi informacje o aplikacji wymaga usługi Azure Mobile Engagement zebrać informacje z urządzeń użytkowników przed wysłaniem powiadomienia wypychanego w związku z czym może powodować opóźnienia. Wypycha konfigurację złożonych wypychania opcje (np. aktualizowanie identyfikatory) również może opóźniać. Korzystanie z interfejsu API wypychania kampanii "zrzut jeden" jest bezwzględnym najszybszy sposób wypychania w usłudze Azure Mobile Engagement. Przy użyciu tylko tagi informacje o aplikacji jako kryterium wypychanej kampanii Reach (niezależnie od interfejsu API Reach lub interfejsu użytkownika) jest najszybszy sposób dalej, ponieważ tagi informacje o aplikacji są przechowywane po stronie serwera. Przy użyciu innych kryteriów określania wartości docelowej w ramach kampanii wypychania jest metoda push najbardziej elastycznego, ale najwolniejsze, ponieważ usługi Azure Mobile Engagement ma zapytanie urządzenia w celu wysyłania kampanii.

![Reach-Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Opcje kryterium mają zastosowanie do:
* **Technicals**     
* Nazwa oprogramowania układowego: nazwy oprogramowania układowego
* Wersja oprogramowania układowego: wersja oprogramowania układowego
* Model urządzenia: model urządzenia
* Producent urządzenia: producent urządzenia
* Wersja aplikacji: wersja aplikacji
* Nazwa operatora: Nazwa operatora, niezdefiniowana
* Operator kraju: operator kraju, niezdefiniowana
* Typ sieci: typ sieci
* Ustawienia regionalne: ustawień regionalnych
* Rozmiar ekranu: rozmiaru ekranu
* **Lokalizacja**      
* Ostatnia znana obszaru: Miejscowość województwo kraj, Region,
* Grodzenia czasu rzeczywistego: Lista z liczba punktów POI (nazwa, akcje), cykliczne POI (nazwa, szerokości geograficznej, geograficzne, Radius metry)
* **Osiągnąć opinii**     
* Opinie anonsu: anonsu, opinie
* Sondowanie opinii: sondowania, opinie
* Sondowanie odpowiedzi opinii: sondowanie opinii odpowiedzi, pytanie, wybór
* Opinie wypychania danych: wypychane dane, opinii
* **Zainstaluj śledzenia**     
* Magazyn: Magazyn, niezdefiniowana
* Źródło: Źródło, niezdefiniowana
* **Profil użytkownika**     
* Płci: niezdefiniowana męskiego lub gniazdo,
* Data urodzenia: operator, data niezdefiniowana
* Opcjonalnych: PRAWDA lub FAŁSZ, niezdefiniowane
* **Informacje o aplikacji**      
* Ciąg: Ciąg, niezdefiniowana
* Data: operator, data niezdefiniowana
* Całkowitą: operator, numer niezdefiniowana
* Wartość logiczna: niezdefiniowana wartość PRAWDA lub FAŁSZ,
* **Segment**    
* Nazwa segmenty (z listy rozwijanej) wykluczeń (użytkownicy docelowi, którzy nie są częścią tego segmentu).

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

