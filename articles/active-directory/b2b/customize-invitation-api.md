---
title: Dostosowywanie i współpracy usługi Azure Active Directory B2B interfejsu API | Dokumentacja firmy Microsoft
description: Współpraca B2B usługi Azure Active Directory wspiera relacje między firmami, umożliwiając partnerom biznesowym selektywne uzyskiwanie dostępu do Twoich aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: a41eadaecc203f9371da3eee05367a4f77747253
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644961"
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

Aby uzyskać więcej informacji można znaleźć: https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Teraz istnieje możliwość dodawania i łatwo zaprosić użytkowników zewnętrznych dla organizacji przy użyciu programu PowerShell. Utwórz zaproszenia przy użyciu polecenia cmdlet:

```
New-AzureADMSInvitation
```

Można użyć następujących opcji:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Możesz również sprawdzić dokumentacja interfejsu API zaproszenie w [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Elementy wiadomości e-mail z zaproszeniem współpracy B2B](invitation-email-elements.md)
- [Realizacja zaproszenia współpracy B2B](redemption-experience.md)
- [Dodają użytkowników we współpracy B2B bez zaproszenia](add-user-without-invite.md)

