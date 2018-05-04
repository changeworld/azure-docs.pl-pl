---
title: Samoobsługowego resetowania hasła zasad — Azure Active Directory
description: Azure AD samoobsługowego resetowania hasła opcji zasad
services: active-directory
keywords: Zarządzanie hasłami w usłudze Active directory, zarządzanie hasłami, usługi Azure AD samodzielnego resetowania hasła usługi
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: b8079c5dffd4d426d62267e77f226d2dda183665
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Ograniczenia w usłudze Azure Active Directory i zasad haseł

W tym artykule opisano zasady haseł i wymagania dotyczące złożoności skojarzone z kont użytkowników przechowywanych w dzierżawie usługi Azure Active Directory (Azure AD).

## <a name="administrator-password-policy-differences"></a>Różnice zasad hasła administratora

Microsoft wymusza silne domyślne *dwa bramy* zasady dla każdej roli administrator usługi Azure resetowania hasła. 

Za pomocą zasad dwa bramy administratorzy nie mają możliwość używania pytania zabezpieczające.

 Zasady bramy dwa wymaga dwóch rodzajów danych uwierzytelniania, takich jak adres e-mail *i* numeru telefonu. Zasady dwa bramy ma zastosowanie w następujących okolicznościach:

* Dotyczy wszystkich następujących ról administratora platformy Azure:
  * Administrator pomocy technicznej
  * Administrator pomocy technicznej usługi
  * Administrator rozliczeń
  * Pomoc techniczna dla partnerów (warstwa 1)
  * Pomoc techniczna dla partnerów (warstwa 2)
  * Administrator usługi Exchange
  * Administrator usługi Lync
  * Administrator konta użytkownika
  * Zapisywanie katalogów
  * Administrator globalny lub administrator firmy
  * Administrator usługi programu SharePoint
  * Administrator do spraw zgodności
  * Administrator aplikacji
  * Administrator zabezpieczeń
  * Administrator roli uprzywilejowanej
  * Administratora usługi Microsoft Intune
  * Administrator usługi serwera proxy aplikacji
  * Administrator programu CRM usługi
  * Administrator usługi Power BI
  
* Po upływie 30 dni w subskrypcji wersji próbnej

  lub

* Domen niestandardowych jest obecny, np. contoso.com

  lub

* Azure AD Connect synchronizuje tożsamości z katalogu lokalnego

### <a name="exceptions"></a>Wyjątki
Zasady co brama wymaga jednego elementu danych uwierzytelniania, takie jak adres e-mail *lub* numer telefonu. Zasady jednej bramy ma zastosowanie w następujących okolicznościach:

* Jest w ciągu 30 dni subskrypcji wersji próbnej

  lub

* Domen niestandardowych nie jest obecny (*. onmicrosoft.com) 

  i 

  Azure AD Connect nie jest synchronizowanie tożsamości


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName zasad, które są stosowane do wszystkich kont użytkowników

Każdego konta użytkownika, który musi się zalogować do usługi Azure AD musi mieć wartość atrybutu unikatowego użytkownika głównej nazwy (UPN) skojarzonych z jego konta. W poniższej tabeli przedstawiono zasady, które dotyczą zarówno lokalnych kont użytkownika usługi Active Directory, które są synchronizowane z chmurą i do kont użytkowników tylko w chmurze:

| Właściwość | Wymagania dotyczące UserPrincipalName |
| --- | --- |
| Znaki są dozwolone |<ul> <li>A-Z</li> <li>- z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Znaki nie są dozwolone |<ul> <li>Wszelkie "\@ \" znak, który nie jest oddzielając nazwy użytkownika w domenie.</li> <li>Nie może zawierać znaku kropki "." bezpośrednio przed "\@ \" symbol</li></ul> |
| Ograniczenia długości |<ul> <li>Całkowita długość nie może przekraczać 113 znaków</li><li>Może istnieć maksymalnie 64 znaki, przed "\@ \" symbol</li><li>Może istnieć maksymalnie 48 znaków po "\@ \" symbol</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Zasady haseł, które są stosowane tylko do kont użytkowników w chmurze

W poniższej tabeli opisano ustawienia zasad dostępne hasła, które można zastosować do kont użytkowników, które są tworzone i zarządzane w usłudze Azure AD:

| Właściwość | Wymagania |
| --- | --- |
| Znaki są dozwolone |<ul><li>A-Z</li><li>- z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Znaki nie są dozwolone |<ul><li>Znaki Unicode.</li><li>Spacje.</li><li> Tylko silne hasła</li></ul> |
| Ograniczenia haseł |<ul><li>Co najmniej 8 znaków i maksymalnie 16 znaków.</li><li>Silne hasła tylko: wymaga trzech spośród czterech następujących:<ul><li>Małe litery.</li><li>Wielkie litery.</li><li>Cyfry (0 – 9).</li><li>Symbole (patrz poprzednie ograniczeń hasła).</li></ul></li></ul> |
| Okres wygasania haseł |<ul><li>Wartość domyślna: **90** dni.</li><li>Wartość jest można skonfigurować przy użyciu `Set-MsolPasswordPolicy` polecenia cmdlet programu Azure Active Directory modułu dla Windows PowerShell.</li></ul> |
| Powiadomienie o wygaśnięciu hasła |<ul><li>Wartość domyślna: **14** dni (do wygaśnięcia hasła).</li><li>Wartość jest można skonfigurować przy użyciu `Set-MsolPasswordPolicy` polecenia cmdlet.</li></ul> |
| Wygaśnięcia hasła |<ul><li>Wartość domyślna: **false** dni (wskazuje, że wygaśnięcie hasła jest włączona).</li><li>Wartość można skonfigurować dla poszczególnych kont użytkowników przy użyciu `Set-MsolUser` polecenia cmdlet.</li></ul> |
| Historia zmian hasła |Hasło ostatnio *nie* można użyć ponownie, gdy użytkownik zmieni hasło. |
| Historia resetowania hasła | Hasło ostatnio *można* można użyć ponownie, gdy użytkownik resetuje zapomniane hasło. |
| Blokada konta |Po 10 nieudanych prób logowania z nieprawidłowego hasła użytkownik jest zablokowany przez jedną minutę. Dodatkowo niepoprawne prób zalogowania blokady użytkownika zwiększenia okresów czasu. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Ustawianie zasad wygasania haseł w usłudze Azure AD

Administrator globalny dla usługi w chmurze firmy Microsoft można użyć modułu Microsoft Azure AD dla programu Windows PowerShell można ustawić hasła użytkownika nie były wygaszane. Użyciu polecenia cmdlet programu Windows PowerShell można również usunąć nigdy nie wygasa konfiguracji lub aby wyświetlić użytkowników, których hasła są ustawione nigdy nie wygasa. 

Niniejsze wytyczne mają zastosowanie do innych dostawców, takich jak usługi Intune i Office 365, które również zależą od usługi Azure AD dla tożsamości i usługi katalogowe. Wygaśnięcie hasła jest tylko część zasad, które można zmienić.

> [!NOTE]
> Można skonfigurować tylko haseł dla kont użytkowników, które nie są synchronizowane przez synchronizacji katalogów nie wygaśnie. Aby uzyskać więcej informacji o synchronizacji katalogów, zobacz [AD z usługą Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Ustawianie lub Sprawdzanie zasad haseł przy użyciu programu PowerShell

Aby rozpocząć pracę, musisz [Pobierz i zainstaluj moduł programu PowerShell usługi Azure AD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Po jest zainstalowany, możesz użyć następujące kroki do skonfigurowania każdego pola.

### <a name="how-to-check-the-expiration-policy-for-a-password"></a>Jak sprawdzić zasady wygasania hasła
1. Nawiązać środowiska Windows PowerShell przy użyciu poświadczeń administratora firmy.
2. Wykonaj jedną z następujących poleceń:

   * Aby sprawdzić, jeśli ustawiono pojedynczego użytkownika, hasło nigdy nie wygasa, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN (na przykład *aprilr@contoso.onmicrosoft.com*) lub identyfikator użytkownika, aby sprawdzić użytkownika: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Aby wyświetlić **hasło nigdy nie wygasa** ustawienie dla wszystkich użytkowników, uruchom następujące polecenie cmdlet: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Ustaw hasło wygaśnie

1. Nawiązać środowiska Windows PowerShell przy użyciu poświadczeń administratora firmy.
2. Wykonaj jedną z następujących poleceń:

   * Aby ustawić hasło jednego użytkownika tak, aby hasło wygaśnie, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN lub identyfikator użytkownika, użytkownik: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Aby ustawić hasła wszystkich użytkowników w organizacji, co umożliwi wygasną, użyj następującego polecenia cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Ustaw hasło nigdy nie wygasa

1. Nawiązać środowiska Windows PowerShell przy użyciu poświadczeń administratora firmy.
2. Wykonaj jedną z następujących poleceń:

   * Aby ustawić hasło nigdy nie wygasa dla wielu użytkowników, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN lub identyfikator użytkownika, użytkownik: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Aby ustawić hasła wszystkich użytkowników w organizacji nigdy nie wygasa, uruchom następujące polecenie cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > Ustaw hasła `-PasswordNeverExpires $true` nadal wieku na podstawie `pwdLastSet` atrybutu. Ustaw hasła użytkowników nigdy nie wygasa, przejdź 90 dni wygaśnięcia hasła. Na podstawie `pwdLastSet` atrybutów, w przypadku zmiany ważności do `-PasswordNeverExpires $false`, wszystkie hasła, które mają `pwdLastSet` starsze niż 90 dni wymagają od użytkownika je zmienić przy następnym zalogowaniu. Ta zmiana może mieć wpływ na wielu użytkowników. 

## <a name="next-steps"></a>Kolejne kroki

Poniższe artykuły zawierają dodatkowe informacje na temat resetowania za pośrednictwem usługi Azure AD:

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md).
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md).
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)
