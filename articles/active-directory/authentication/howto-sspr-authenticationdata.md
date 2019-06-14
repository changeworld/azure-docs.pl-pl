---
title: Azure AD SSPR wymagania dotyczące danych — Azure Active Directory
description: Dane dotyczące haseł usługi Azure AD resetowania oraz sposób ich spełnienia
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a0d7edb6c7faafcad55e827c2d9e3d2eeea40f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60358047"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Wdrażanie resetowania haseł bez wymagania rejestracji użytkowników końcowych

Aby wdrożyć usługi Azure Active Directory (Azure AD) samoobsługowego resetowania haseł (SSPR), dane uwierzytelniania musi być obecny. Niektóre organizacje mają użytkownikom, podaj swoje dane uwierzytelniania, samodzielnie. Jednak wiele organizacji chce synchronizować z danymi, która już istnieje w usłudze Active Directory. Zsynchronizowane dane będą dostępne dla usługi Azure AD i funkcji samoobsługowego resetowania HASEŁ, bez konieczności interakcji z użytkownikiem, jeśli użytkownik:

* Poprawnie formatowanie danych w Twoim katalogu w środowisku lokalnym.
* Konfigurowanie [program Azure AD Connect przy użyciu ustawień ekspresowych](../hybrid/how-to-connect-install-express.md).

Aby działać poprawnie, numerów telefonów musi być w formacie *+ CountryCode PhoneNumber*, na przykład 4255551234 + 1.

> [!NOTE]
> Musi istnieć odstęp między kod kraju i numer telefonu.
>
> Resetowanie hasła nie obsługuje wewnętrzne numery telefonów. Nawet w formacie 12345 4255551234 + 1 X rozszerzenia zostaną usunięte przed wykonaniem wywołania.

## <a name="fields-populated"></a>Pól

Jeśli używasz domyślne ustawienia Azure AD Connect zostały wprowadzone następujące mapowania:

| Lokalna usługa Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | Telefon biurowy |
| Telefon komórkowy | Telefon komórkowy |

Gdy użytkownik zweryfikuje numeru telefonu komórkowego, pole telefonu w obszarze informacje kontaktowe uwierzytelniania w usłudze Azure AD również zostaną wypełnione przy użyciu tego numeru.

## <a name="authentication-contact-info"></a>Informacje kontaktowe uwierzytelniania

Administrator globalny, można ręcznie ustawić informacje kontaktowe uwierzytelniania użytkownika wyświetlane w poniższy zrzut ekranu.

![Uwierzytelnianie skontaktuj się z pomocą informacji dotyczących użytkownika w usłudze Azure AD][Contact]

Jeśli pole telefonu jest wypełniana i telefonu komórkowego jest włączony w zasadach funkcji samoobsługowego resetowania HASEŁ, użytkownik będzie widział przepływ pracy resetowania numeru strony rejestracji resetowania hasła i podczas hasło.

Pole alternatywny numer telefonu nie jest używane do resetowania hasła.

Jeśli jest wypełniana pola adresu E-mail i wiadomości E-mail jest włączona w zasadach funkcji samoobsługowego resetowania HASEŁ, użytkownik zobaczy, Wyślij wiadomość e-mail na stronie rejestracji resetowania hasła i podczas hasło przepływ pracy resetowania.

Jeśli wypełnianie pola alternatywny adres e-mail i wiadomości E-mail jest włączona w zasadach funkcji samoobsługowego resetowania HASEŁ, użytkownik zostanie **nie** zauważyć, że strony rejestracji resetowania poczty e-mail na hasło, lecz zostanie wyświetlony, zresetuj go podczas hasło przepływu pracy.

## <a name="security-questions-and-answers"></a>Pytania zabezpieczające i odpowiedzi

Pytania zabezpieczające i odpowiedzi są bezpiecznie przechowywane w dzierżawie usługi Azure AD i są dostępne dla użytkowników za pośrednictwem jedynie [portal rejestracji SSPR](https://aka.ms/ssprsetup). Administratorzy nie zobaczą, ustawić lub zmodyfikować zawartość innym użytkownikom pytań i odpowiedzi.

## <a name="what-happens-when-a-user-registers"></a>Co się stanie po użytkownik rejestruje

Gdy użytkownik rejestruje, na stronie rejestracji ustawia następujące pola:

* **Numer telefonu uwierzytelniania**
* **Adres E-mail uwierzytelniania**
* **Pytania zabezpieczające i odpowiedzi**

Jeśli podano wartość **telefon komórkowy** lub **alternatywny adres e-mail**, użytkownicy mogą natychmiast używać tych wartości resetowania swoich haseł, nawet jeśli ich nie zostały zarejestrowane dla usługi. Ponadto użytkownicy widzą tych wartości, gdy po raz pierwszy zarejestrować i mogą ich modyfikować, jeśli chcą. Po ich pomyślnie zarejestrować, te wartości zostaną utrwalone w **numer telefonu uwierzytelniania** i **adres E-mail uwierzytelniania** pola, odpowiednio.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Ustawianie i odczytywanie danych uwierzytelniania za pomocą programu PowerShell

Za pomocą programu PowerShell można ustawić następujące pola:

* **Alternatywny adres e-mail**
* **Telefon komórkowy**
* **Telefon biurowy**: Można ustawić tylko jeśli one nie przeprowadza synchronizacji z katalogiem lokalnym

### <a name="use-powershell-version-1"></a>Za pomocą programu PowerShell w wersji 1

Aby rozpocząć pracę, musisz [Pobierz i zainstaluj moduł programu Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Po użytkownik jest zainstalowany, należy użyć czynności, które należy wykonać, aby skonfigurować każdego pola.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Ustaw dane uwierzytelniania przy użyciu programu PowerShell w wersji 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Odczytywanie danych uwierzytelniania przy użyciu programu PowerShell w wersji 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Przeczytaj następujące opcje numer telefonu uwierzytelniania i adres E-mail uwierzytelniania

Aby przeczytać **numer telefonu uwierzytelniania** i **adres E-mail uwierzytelniania** korzystając z programu PowerShell w wersji 1, użyj następujących poleceń:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Za pomocą programu PowerShell w wersji 2

Aby rozpocząć pracę, musisz [Pobierz i zainstaluj moduł programu PowerShell w wersji 2 usługi Azure AD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Po użytkownik jest zainstalowany, należy użyć czynności, które należy wykonać, aby skonfigurować każdego pola.

Aby szybko zainstalować z najnowszych wersji programu PowerShell, które obsługują Install-Module, uruchom następujące polecenia. (Pierwszy wiersz sprawdza, czy moduł jest już zainstalowany.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Ustaw dane uwierzytelniania przy użyciu programu PowerShell w wersji 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Odczytywanie danych uwierzytelniania przy użyciu programu PowerShell w wersji 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Administratorzy globalni mogą modyfikować informacje kontaktowe uwierzytelniania użytkownika"
