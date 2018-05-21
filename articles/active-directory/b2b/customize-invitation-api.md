---
title: Dostosowywanie i Azure Active Directory B2B współpracy interfejsu API | Dokumentacja firmy Microsoft
description: Współpraca B2B usługi Azure Active Directory wspiera relacje między firmami, umożliwiając partnerom biznesowym selektywne uzyskiwanie dostępu do Twoich aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/11/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: e53bac5bca199fe08d53f232416a1c6432148f34
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Dostosowywanie i Azure Active Directory B2B współpracy interfejsu API

Firma Microsoft zakończonej wielu klientów, powiedz nam, że chcą dostosowania procesu zaproszenia w taki sposób, który najlepiej pasuje do organizacji. O interfejsie API możesz to zrobić tylko. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Możliwości zaproszenia do interfejsu API
Interfejs API oferuje następujące możliwości:

1. Zaproś użytkownika zewnętrznego z *żadnych* adres e-mail.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Dostosowywanie miejscu użytkownikom przejście po zaakceptowaniu zaproszenia ich.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Wysyłanie poczty standardowe zaproszenia przez nas

    ```
    "sendInvitationMessage": true
    ```

  komunikat do adresata, który można dostosować

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. I wybierz opcję DW: osób, które mają być zachowane w pętli o tym współpracownika zaproszenie.

5. Lub całkowicie dostosować, wybierając pozycję nie wysłać powiadomienia za pomocą usługi Azure AD Twojego zaproszenia i przepływ pracy dołączania.

    ```
    "sendInvitationMessage": false
    ```

  W takim przypadku można odzyskać realizacji adres URL z interfejsu API, który można osadzić w szablonu wiadomości e-mail, wiadomości Błyskawicznych lub innej metody dystrybucji wybranych przez użytkownika.

6. Ponadto jeśli jesteś administratorem, można zaprosić użytkowników jako elementu członkowskiego.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modelu autoryzacji
Interfejs API mogą być uruchamiane w następujących trybach autoryzacji:

### <a name="app--user-mode"></a>Aplikacja + trybu użytkownika
W tym trybie, kto korzysta na potrzeby interfejsu API uprawnienia można utworzyć B2B zaproszeń do skorzystania z.

### <a name="app-only-mode"></a>Tryb tylko do aplikacji
W kontekście tylko aplikacji Aplikacja musi zakres User.Invite.All zaproszenia powiodło się.

Aby uzyskać więcej informacji można znaleźć: https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Obecnie istnieje możliwość dodania i łatwo zaprosić użytkowników zewnętrznych do organizacji przy użyciu programu PowerShell. Tworzenie zaproszenia, za pomocą polecenia cmdlet:

```
New-AzureADMSInvitation
```

Można użyć następujących opcji:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Możesz również sprawdzić się w dokumentacji interfejsu API zaproszenia [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Elementy współpracy B2B zaproszenie](invitation-email-elements.md)
- [Realizacji zaproszenia współpracy B2B](redemption-experience.md)
- [Dodawanie użytkowników współpracy B2B bez zaproszenia](add-user-without-invite.md)

