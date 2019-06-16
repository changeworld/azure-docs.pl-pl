---
title: B2B współpracy interfejsu API i dostosowywanie — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Współpraca B2B usługi Azure Active Directory wspiera relacje między firmami, umożliwiając partnerom biznesowym selektywne uzyskiwanie dostępu do Twoich aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0369988bc6f6503f9940e6aabccb91ab843d63f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65811881"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Dostosowywanie i współpracy usługi Azure Active Directory B2B interfejsu API

Mieliśmy wielu klientów, powiedz nam, że chce dostosować proces zaproszenie w sposób, który najlepiej sprawdza się w swoich organizacjach. Za pomocą naszego interfejsu API możesz to zrobić tylko. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Możliwości zaproszenia do interfejsu API

Interfejs API oferuje następujące możliwości:

1. Zaproś użytkownika zewnętrznego za pomocą *wszelkie* adres e-mail.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Dostosowywanie, którego użytkownicy z zawarciem po zaakceptowaniu zaproszenia.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Wybierz do wysyłania wiadomości e-mail standardowa zaproszenia przez nas

    ```
    "sendInvitationMessage": true
    ```

   komunikat do adresata, które można dostosowywać

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Możliwość DW: osób, które chcesz zachować w pętli o tym współpracownika zaproszenie.

5. Lub całkowicie dostosować Twoje zaproszenie i przepływ pracy dołączania, wybierając pozycję nie wysłać powiadomienia za pośrednictwem usługi Azure AD.

    ```
    "sendInvitationMessage": false
    ```

   W takim przypadku należy wrócić adres URL realizacji z interfejsu API, które można osadzić w szablon wiadomości e-mail, wiadomości Błyskawicznych lub innej metody dystrybucji wybranego.

6. Ponadto jeśli jesteś administratorem, możesz zaprosić użytkownika jako członka.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modelu autoryzacji

Interfejs API mogą być uruchamiane w następujących trybach autoryzacji:

### <a name="app--user-mode"></a>Aplikacja i trybu użytkownika

W tym trybie, kto korzysta na potrzeby interfejsu API musi mieć uprawnienia można utworzyć zaproszenia B2B.

### <a name="app-only-mode"></a>Tryb tylko do aplikacji

W kontekście tylko aplikacji Aplikacja musi zakresu User.Invite.All związane z zaproszeniem została wykonana pomyślnie.

Aby uzyskać więcej informacji można znaleźć: https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

Aby dodać, a następnie łatwo zaprosić użytkowników zewnętrznych dla organizacji, można użyć programu PowerShell. Utwórz zaproszenia przy użyciu polecenia cmdlet:

```powershell
New-AzureADMSInvitation
```

Możesz użyć następujących opcji:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Stan zaproszenia

Po użytkownik zewnętrzny wysłać zaproszenie, można użyć **Get-AzureADUser** polecenia cmdlet, aby zobaczyć, jeśli została zaakceptowana go. Następujące właściwości Get-AzureADUser są wypełniane, gdy użytkownik zewnętrzny jest wysyłana wiadomość z zaproszeniem:

* **UserState** wskazuje, czy to zaproszenie **PendingAcceptance** lub **zaakceptowano**.
* **UserStateChangedOn** zawiera sygnaturę czasową, aby najnowsze zmiany **UserState** właściwości.

Możesz użyć **filtru** opcję, aby przefiltrować wyniki według **UserState**. W poniższym przykładzie pokazano sposób filtrowania wyników do wyświetlenia tylko tych użytkowników, którzy mają oczekujące zaproszenie. W przykładzie pokazano również **Format-Lista** opcja, która umożliwia określenie właściwości do wyświetlenia. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Upewnij się, że masz najnowszą wersję modułu AzureAD PowerShell lub module AzureADPreview programu PowerShell. 

## <a name="see-also"></a>Zobacz także

Zapoznaj się z odwołanie zaproszenia interfejsu API w [ https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation ](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

## <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Elementy wiadomości e-mail z zaproszeniem współpracy B2B](invitation-email-elements.md)
- [Realizacja zaproszenia współpracy B2B](redemption-experience.md)
- [Dodają użytkowników we współpracy B2B bez zaproszenia](add-user-without-invite.md)