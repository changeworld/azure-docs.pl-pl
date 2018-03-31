---
title: Usługi Azure Mobile Engagement przewodnik - SDK rozwiązywania problemów
description: Rozwiązywanie problemów integracji zestawu SDK usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: de265cf1-2f88-43ef-8616-156ada5be7b6
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 1f617b3c3b221a38b63be6f61f7553b390cf2f7c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Przewodnik rozwiązywania problemów integracji zestawu SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Poniżej przedstawiono możliwe problemy, które mogą się pojawić w sposób integruje aplikacji usługi Azure Mobile Engagement.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>Problemy z zestawu SDK odnalezione awaria w innym miejscu aplikacji
### <a name="issue"></a>Problem
* Błąd zbierania danych interfejsu użytkownika (w analizy, monitorowanie, segmentacji lub pulpity).
* Wypychanie błędów (Wypchnięć nie działają w aplikacji poza aplikacji lub obie).
* Zaawansowane błędy funkcji (śledzenia, używanie funkcji Geolokalizacji lub platformy, którą określonych Wypchnięć nie działają).
* Błędy interfejsu API (interfejsy API niepowodzenie często trybie cichym bez komunikaty o błędach).
* Awarie usługi (Brak usługi Azure Mobile Engagement działa w przypadku aplikacji).

### <a name="causes"></a>Powoduje, że
* Błąd w aplikacji (np. Błąd zbierania danych interfejsu użytkownika, Niepowodzenie wypychania, Niepowodzenie zaawansowanych funkcji, błąd interfejsu API, awarii aplikacji lub awaria usługi jawnego) spowoduje odnalezienie większości problemów, które muszą zostać rozpoznane z zestawem Azure Mobile Engagement SDK .  
* Jeśli w aplikacji przed nigdy nie działał poszczególnych funkcji usługi Azure Mobile Engagement, należy ukończyć integracji. 
* Jeśli poszczególnych funkcji usługi Azure Mobile Engagement pracy i zatrzymanie, może być konieczne uaktualnienie do ostatniej wersji z zestawem Azure Mobile Engagement SDK. Należy pamiętać, że jest inna wersja zestawem Azure Mobile Engagement SDK dla każdej platformy obsługiwane przez usługi Azure Mobile Engagement (Android, iOS, Windows i Windows Phone).

#### <a name="sdk-integration"></a>Integracja z zestawem SDK
* Usługa Azure Mobile Engagement prawidłowo zintegrowane w zestawie SDK (Analytics).
* Łączność prawidłowo zintegrowany zestaw SDK (w aplikacji i poza wypchnięcia aplikacji).
* Certyfikat wygasły lub niepoprawne vs produkcyjną. Deweloperów (tylko iOS).
* GCM lub ADM prawidłowo zintegrowany zestaw SDK (Android tylko — usługa określonych wypchnięcia).
* Śledzenie prawidłowo zintegrowane w zestawie SDK (magazyn instalacji śledzenia).
* Opóźnieniem lokalizacji lub lokalizacji GPS prawidłowo zintegrowane w zestawie SDK (docelowych według lokalizacji geograficznej).

**Zobacz też:**

* [Dokumentacja zestawu SDK — przewodniki integracji][Link 5] 
* [Przewodnik rozwiązywania problemów - wypychania][Link 23]

#### <a name="sdk-upgrade"></a>Uaktualnianie zestawu SDK
* Należy uaktualnić zestaw SDK, aby rozwiązać problemy ze starszymi wersjami SDK (często powiązane do nowszej wersji systemu operacyjnego urządzenia).
* Odinstaluj wszystkie wcześniejsze wersje aplikacji z urządzenia, a następnie zainstaluj ponownie najnowszą wersję aplikacji, ponownie zarejestrować identyfikator urządzenia z usługi Azure Mobile Engagement interfejsu użytkownika aby upewnić się, że urządzenie jest używana najnowsza wersja aplikacji.

**Zobacz też:**

* [Dokumentacja zestawu SDK — informacje o wersji](http://go.microsoft.com/fwlink/?LinkId= 525554) 
* [Dokumentacja zestawu SDK — przewodniki uaktualnienia](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>Zestaw SDK innych
* Błędy w manifeście aplikacji "AndroidManifest.xml" może spowodować usługi Azure Mobile Engagement nie będą działać (tylko Android).
* Typowym problemem z integracji zestawu SDK i użycia interfejsu API jest mylenie klucza zestawu SDK i klucz interfejsu API.

**Zobacz też:**

* [Pojęcia — słownik][Link 6]

## <a name="advanced-coding-issues"></a>Zaawansowane kodowania problemów
### <a name="issue"></a>Problem
* Określony kod platformy niepowiązane bezpośrednio z usługi Azure Mobile Engagement mogą powodować problemy w systemie iOS, Android i Windows Phone.

### <a name="causes"></a>Powoduje, że
* Wiele zaawansowanych kodowania problemy z usługi Azure Mobile Engagement są spowodowane przez niepoprawnie napisane platformy kod niepowiązane bezpośrednio z usługi Azure Mobile Engagement. Należy zapoznać się z dokumentacją specyficzną dla używanej platformy, które tworzysz dla oprócz dokumentacji usługi Azure Mobile Engagement (Android, iOS, sieci Web, Windows i Windows Phone).
* Konfigurowanie prawidłowo "kategorie", uniemożliwia łączenie z powiadomienie do innej lokalizacji wewnątrz lub na zewnątrz aplikacji (tylko Android). 
* To ustawienie nie zostanie "UIKit.framework" "opcjonalne" w kodzie systemu iOS pokazuje "Błąd: nie odnaleziono symbolu" i/lub ulega awarii na starszych urządzeń z systemem iOS (tylko iOS).
* Wygasłe certyfikaty lub prawidłowo przy użyciu wersji Programistycznych lub produkcyjną CERT przyczyny problemów wypychania (tylko iOS).
* Istnieją pewne ograniczenia wbudowane w platformę Azure Mobile Engagement nie może kontrolować (tak jak wypycha działania programu system center dla aplikacji w systemach Android i iOS).
* Usługa Azure Mobile Engagement publikuje pełną listę pakietów wewnętrzny używany przez usługi Azure Mobile Engagement dla systemów iOS i Android odwołania. Należy pamiętać, że niektóre funkcje usługi Azure Mobile Engagement są specyficzne dla platformy (Android, iOS, sieci Web, Windows i Windows Phone).

### <a name="see-also"></a>Zobacz także
* [Przewodnik rozwiązywania problemów - wypychania][Link 23] 
* [Dokumentacja zestawu SDK — informacje o wersji][Link 5]
* [Dokumentacja zestawu SDK — przewodniki uaktualnienia][Link 5]

## <a name="application-crashes"></a>Awarii aplikacji
### <a name="issue"></a>Problem
* Aplikacja przestaje działać na urządzeniu użytkownika końcowego.

### <a name="causes"></a>Powoduje, że
* Informacje o awarii można wyświetlić w *interfejsu użytkownika analizy* lub *analizy interfejsu API*
* Można znaleźć Identyfikatora urządzenia urządzenia testu i wykonać tę samą akcję, który spowodował awarię aplikacji dla użytkownika końcowego ułatwić identyfikację przyczyny awarii sieci.
* Znane problemy z zestawem Azure Mobile Engagement SDK, które aplikacje awarii są czasami rozwiązać przez uaktualnienie do najnowszej wersji zestawu SDK. Upewnij się, że podczas badania awarii (Crash), sprawdź informacje o wersji dotyczące platformy.

### <a name="see-also"></a>Zobacz także
* [Dokumentacja zestawu SDK — informacje o wersji][Link 5]
* [Dokumentacja zestawu SDK — przewodniki uaktualnienia][Link 5]

## <a name="app-store-upload-failures"></a>Aplikacji sklepu przekazywania błędów
### <a name="issue"></a>Problem
* Błędy związane z przekazaniem najnowszą wersję aplikacji do sklepu z aplikacjami systemu Windows firmy Apple lub Google.

### <a name="causes"></a>Powoduje, że
* Aplikacja przechowuje czasami blok aplikacje z niektórych z włączonymi funkcjami (np. sklepu Apple zapobiega użyciu IDFV aplikacji w magazynie i magazynu GooglePlay uniemożliwia udostępnianie informacji o aplikacji między aplikacjami). 
* Upewnij się, sprawdź informacje o wersji dotyczące platformy i bieżącej wersji zestawu SDK, jeśli masz trudności przekazywania aplikacji do sklepu.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
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

