---
title: Usługi Azure Mobile Engagement przewodnik - API rozwiązywania problemów
description: Rozwiązywanie problemów z przewodniki dotyczące usługi Azure Mobile Engagement — interfejsów API
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
ms.openlocfilehash: cda11c8d2f1d147a807083c5479d2b2cda65462c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-api-issues"></a>Przewodnik rozwiązywania problemów interfejsu API
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Poniżej przedstawiono możliwe problemy, które mogą się pojawić w sposób administratorzy interakcji z usługi Azure Mobile Engagement za pośrednictwem interfejsów API.

## <a name="syntax-issues"></a>Składnia problemów
### <a name="issue"></a>Problem
* Błędy składniowe za pomocą interfejsu API (lub nieoczekiwane zachowanie).

### <a name="causes"></a>Powoduje, że
* Składnia problemy:
  * Upewnij się sprawdzić składnię interfejsu API, używasz, aby upewnić się, że ta opcja jest dostępna.
  * Typowym problemem z użyciem interfejsu API jest mylenie interfejsu API Reach i interfejsu API Push (większość zadań powinno odbywać się przy użyciu interfejsu API Reach zamiast interfejsu API Push). 
  * Inny typowe problemy z integracji zestawu SDK i użycie interfejsu API jest mylenie klucza zestawu SDK i klucz interfejsu API.
  * Skrypty łączących się za pośrednictwem interfejsów API należy wysyłać dane co najmniej 10 minut lub połączenie zostanie limit czasu (typowe szczególnie w przypadku skryptów Monitor API nasłuchu danych). Aby zapobiec przekroczeń limitu czasu, mieć skrypt wysłać polecenie ping protokołu XMPP co 10 minut do podtrzymywania sesji na serwerze.

### <a name="see-also"></a>Zobacz także
* [Dokumentacja interfejsu API][Link 4]
* [Informacje o protokołem XMPP](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Nie można wykonać tę samą akcję dostępne w Interfejsie użytkownika usługi Azure Mobile Engagement za pomocą interfejsu API
### <a name="issue"></a>Problem
* Akcja, która działa w interfejsie użytkownika usługi Azure Mobile Engagement nie działa z interfejsu API powiązane usługi Azure Mobile Engagement.

### <a name="causes"></a>Powoduje, że
* Potwierdzenie, że tę samą akcję można wykonać w interfejsie użytkownika usługi Azure Mobile Engagement pokazuje poprawnie zintegrowane tej funkcji usługi Azure Mobile Engagement przy użyciu zestawu SDK.

### <a name="see-also"></a>Zobacz także
* [Dokumentacja interfejsu użytkownika][Link 1]

## <a name="error-messages"></a>Komunikaty o błędach
### <a name="issue"></a>Problem
* Kody błędów przy użyciu interfejsu API, wyświetlane w czasie wykonywania, lub w dziennikach.

### <a name="causes"></a>Powoduje, że
* Poniżej przedstawiono listę złożonego wspólnej numery kodów stanu interfejsu API dla odwołania i wstępne rozwiązywanie problemów z:
  
        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Zobacz także
* [Dokumentacja interfejsu API — szczegóły błędów w każdej interfejsu API][Link 4]

## <a name="silent-failures"></a>Błędy w trybie dyskretnym
### <a name="issue"></a>Problem
* Interfejs API akcja zakończy się niepowodzeniem z komunikatem o błędzie wyświetlany w czasie wykonywania, lub w dziennikach.

### <a name="causes"></a>Powoduje, że
* Wiele elementów zostanie wyłączona w Interfejsie użytkownika usługi Azure Mobile Engagement, jeśli nie są poprawnie, zintegrowane, ale spowoduje niepowodzenie w trybie dyskretnym z interfejsu API, więc Pamiętaj, aby przetestować funkcji z interfejsu użytkownika, aby sprawdzić, czy działa.
* Usługa Azure Mobile Engagement i wiele zaawansowanych funkcji usługi Azure Mobile Engagement, którego próbujesz użyć, muszą być indywidualnie zintegrowane aplikacji przy użyciu zestawu SDK jako oddzielne kroki przed ich użyciem.

### <a name="see-also"></a>Zobacz także
* [Przewodnik rozwiązywania problemów - SDK][Link 25]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
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

