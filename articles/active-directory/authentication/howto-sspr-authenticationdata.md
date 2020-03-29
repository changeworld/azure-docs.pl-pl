---
title: Wymagania dotyczące danych sspr usługi Azure AD — usługa Azure Active Directory
description: Wymagania dotyczące danych dotyczących samoobsługowego resetowania hasła usługi Azure AD i sposobu ich spełnienia
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a14338e552250ac63c344365099a16f20616ea9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74964036"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Wdrażanie resetowania hasła bez konieczności rejestracji użytkownika końcowego

Aby wdrożyć samoobsługowe resetowanie haseł usługi Azure Active Directory (Azure AD), dane uwierzytelniania muszą być obecne. Niektóre organizacje same wprowadziły swoje dane uwierzytelnienia. Inne organizacje wolą synchronizować dane, które już istnieją w usłudze Active Directory. Te zsynchronizowane dane są udostępniane usługom Azure AD i wiele SSPR bez konieczności interakcji z użytkownikiem, jeśli spełniasz następujące wymagania:

* Prawidłowe formatowanie danych w katalogu lokalnym.
* Skonfiguruj [usługę Azure AD Connect przy użyciu ustawień ekspresowych](../hybrid/how-to-connect-install-express.md).

Aby działać poprawnie, numery telefonów muszą być w formacie *+CountryCode PhoneNumber*, na przykład +1 4255551234.

> [!NOTE]
> Między kodem kraju a numerem telefonu musi być miejsce.
>
> Resetowanie hasła nie obsługuje rozszerzeń telefonu. Nawet w formacie +1 4255551234X12345 rozszerzenia są usuwane przed najechaniem połączenia.

## <a name="fields-populated"></a>Pola wypełnione

Jeśli używasz ustawień domyślnych w usłudze Azure AD Connect, są wykonane następujące mapowania:

| Lokalna usługa Active Directory | Azure AD |
| --- | --- |
| Telephonenumber | Telefon służbowy |
| telefon komórkowy | Telefon komórkowy |

Po zweryfikowaniu przez użytkownika numeru telefonu komórkowego pole *Telefon* w obszarze **Informacje kontaktowe uwierzytelniania** w usłudze Azure AD jest również wypełniane tym numerem.

## <a name="authentication-contact-info"></a>Informacje kontaktowe uwierzytelniania

Na stronie **Metody uwierzytelniania** dla użytkownika usługi Azure AD w witrynie Azure portal administrator globalny może ręcznie ustawić informacje kontaktowe uwierzytelniania, jak pokazano na poniższym przykładowym zrzucie ekranu:

![Informacje kontaktowe uwierzytelniania użytkownika w usłudze Azure AD][Contact]

* Jeśli pole **Telefon** jest wypełnione, a **telefon komórkowy** jest włączony w zasadach samoobserwowania haseł, użytkownik widzi ten numer na stronie rejestracji resetowania hasła i podczas przepływu pracy resetowania hasła.
* Pole **Telefon alternatywny** nie jest używane do resetowania hasła.
* Jeśli pole **Poczta e-mail** jest wypełniane, a **poczta e-mail** jest włączona w zasadach sspr, użytkownik widzi tę wiadomość e-mail na stronie rejestracji resetowania hasła i podczas przepływu pracy resetowania hasła.
* Jeśli pole **Alternatywny adres e-mail** jest wypełnione, a w zasadach SSPR jest włączona opcja **Poczta e-mail,** użytkownik nie **zobaczy** tej wiadomości e-mail na stronie rejestracji resetowania hasła, ale zobaczy ją podczas przepływu pracy resetowania hasła.

## <a name="security-questions-and-answers"></a>Pytania zabezpieczające i odpowiedzi

Pytania zabezpieczające i odpowiedzi są bezpiecznie przechowywane w dzierżawie usługi Azure AD i są dostępne tylko dla użytkowników za pośrednictwem [portalu rejestracji sspr.](https://aka.ms/ssprsetup) Administratorzy nie widzą, nie ustawiają ani nie modyfikują zawartości pytań i odpowiedzi innych użytkowników.

## <a name="what-happens-when-a-user-registers"></a>Co się stanie, gdy użytkownik zarejestruje się

Gdy użytkownik się zarejestruje, strona rejestracji ustawia następujące pola:

* **Telefon uwierzytelniający**
* **Uwierzytelnianie e-mail**
* **Pytania i odpowiedzi zabezpieczające**

Jeśli podałeś wartość **dla telefonu komórkowego** lub **alternatywnej poczty e-mail,** użytkownicy mogą natychmiast użyć tych wartości, aby zresetować swoje hasła, nawet jeśli nie zarejestrowali się w usłudze. Ponadto użytkownicy widzą te wartości podczas rejestracji po raz pierwszy i mogą je modyfikować, jeśli chcą. Po pomyślnym zarejestrowaniu te wartości są zachowywane odpowiednio w polach **Telefon uwierzytelniania** i **Uwierzytelnianie e-mail.**

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Ustawianie i odczytywanie danych uwierzytelniania za pośrednictwem programu PowerShell

Za pomocą programu PowerShell można ustawić następujące pola:

* **Alternatywny adres e-mail**
* **Telefon komórkowy**
* **Telefon pakietu Office**: można ustawić tylko wtedy, gdy nie synchronizujesz z katalogiem lokalnym

### <a name="use-powershell-version-1"></a>Korzystanie z programu PowerShell w wersji 1

Aby rozpocząć, należy [pobrać i zainstalować moduł programu Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Po zainstalowaniu można użyć kroków, które należy wykonać, aby skonfigurować każde pole.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Ustawianie danych uwierzytelniania w programie PowerShell w wersji 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Odczytywanie danych uwierzytelniania w programie PowerShell w wersji 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Odczytywanie opcji uwierzytelniania telefonu i uwierzytelniania wiadomości e-mail

Aby odczytać wiadomość **e-mail** z **telefonem uwierzytelnianym** i uwierzytelnianiem podczas korzystania z programu PowerShell w wersji 1, należy użyć następujących poleceń:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Korzystanie z programu PowerShell w wersji 2

Aby rozpocząć, musisz [pobrać i zainstalować moduł programu PowerShell w usłudze Azure AD w wersji 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Po zainstalowaniu można użyć kroków, które należy wykonać, aby skonfigurować każde pole.

Aby szybko zainstalować z najnowszych wersji programu PowerShell, które obsługują install-module, uruchom następujące polecenia. (Pierwszy wiersz sprawdza, czy moduł jest już zainstalowany).

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Ustawianie danych uwierzytelniania w programie PowerShell w wersji 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Odczytywanie danych uwierzytelniania w programie PowerShell w wersji 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Następne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś jest zepsute. Jak rozwiązać problem z łatem SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Administratorzy globalni mogą modyfikować informacje kontaktowe uwierzytelniania użytkownika"
