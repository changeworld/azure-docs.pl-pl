---
title: Interfejs API i dostosowywania współpracy B2B — usługa Azure Active Directory
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
ms.openlocfilehash: 3a090ee3f9588ff6bff01e12db469bf04407a7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263467"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Interfejs API współpracy i dostosowywania usługi Azure Active Directory B2B

Wielu klientów mówi nam, że chcą dostosować proces zaproszenia w sposób, który najlepiej odpowiada ich organizacjom. Dzięki naszemu API możesz to zrobić. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Możliwości interfejsu API zaproszenia

Interfejs API oferuje następujące możliwości:

1. Zaproś użytkownika zewnętrznego z *dowolnym* adresem e-mail.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Dostosuj miejsce, w którym użytkownicy mają wylądować po zaakceptowaniu zaproszenia.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Wybierz, aby wysłać standardową wiadomość z zaproszeniem za pośrednictwem nas

    ```
    "sendInvitationMessage": true
    ```

   z wiadomością do adresata, którą można dostosować

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. I wybierz cc: osoby, które chcesz zachować w pętli o zaproszeniu tego współpracownika.

5. Możesz też całkowicie dostosować przepływ pracy zaproszenia i dołączania, wybierając opcję niesyłania powiadomień za pośrednictwem usługi Azure AD.

    ```
    "sendInvitationMessage": false
    ```

   W takim przypadku można odzyskać adres URL realizacji z interfejsu API, który można osadzić w szablonie wiadomości e-mail, wiadomości błyskawicznych lub innej metody dystrybucji do wyboru.

6. Na koniec, jeśli jesteś administratorem, możesz zaprosić użytkownika jako członka.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Model autoryzacji

Interfejs API można uruchomić w następujących trybach autoryzacji:

### <a name="app--user-mode"></a>Aplikacja + tryb użytkownika

W tym trybie niezależnie od tego, kto korzysta z interfejsu API, musi mieć uprawnienia do tworzenia zaproszeń B2B.

### <a name="app-only-mode"></a>Tryb tylko aplikacji

W kontekście tylko aplikacji aplikacja potrzebuje user.Invite.All zakres zaproszenia do pomyślnego.

Aby uzyskać więcej informacji, zobacz:https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

Za pomocą programu PowerShell można łatwo dodawać i zapraszać użytkowników zewnętrznych do organizacji. Utwórz zaproszenie przy użyciu polecenia cmdlet:

```powershell
New-AzureADMSInvitation
```

Możesz użyć następujących opcji:

* -ZaproszonyUserWydajnajem
* -InvitedUserEmailAddress
* -WyślijZaproszeSe
* -ZaproszonyUserMessageInfo

### <a name="invitation-status"></a>Stan zaproszenia

Po wysłaniu zewnętrznego użytkownika zaproszenie, można użyć **get-AzureADUser** polecenia cmdlet, aby sprawdzić, czy zostały zaakceptowane. Następujące właściwości Get-AzureADUser są wypełniane, gdy użytkownik zewnętrzny jest wysyłany zaproszenie:

* **UserState** wskazuje, czy zaproszenie jest **PendingAcceptance** czy **Zaakceptowane**.
* **UserStateChangedOn** pokazuje sygnaturę czasowa dla najnowszej zmiany **właściwości UserState.**

Za pomocą opcji **Filtruj** można filtrować wyniki według **stanu użytkownika**. W poniższym przykładzie pokazano, jak filtrować wyniki, aby wyświetlić tylko użytkowników, którzy mają oczekujące zaproszenie. W przykładzie przedstawiono również opcję **Format-List,** która umożliwia określenie właściwości do wyświetlenia. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Upewnij się, że masz najnowszą wersję modułu AzureAD PowerShell lub modułu AzureADPreview PowerShell. 

## <a name="see-also"></a>Zobacz też

Zapoznaj się z odwołaniem do interfejsu API zaproszenia w pliku [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Elementy wiadomości e-mail z zaproszeniem do współpracy B2B](invitation-email-elements.md)
- [Wykup zaproszenia do współpracy B2B](redemption-experience.md)
- [Dodawanie użytkowników współpracy B2B bez zaproszenia](add-user-without-invite.md)