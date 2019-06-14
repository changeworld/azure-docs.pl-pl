---
title: Dostosowywanie usługi Azure AD samoobsługowego resetowania haseł — Azure Active Directory
description: Resetuj opcje dostosowywania dla haseł usługi Azure AD
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
ms.openlocfilehash: d38d93a1c9716cc3a71d904b7b1a46fb8b1c2ee0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60415667"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Dostosowywanie funkcji usługi Azure AD dla samoobsługowego resetowania haseł

Informatycy, którzy mają zostać wdrożone samoobsługowego resetowania haseł (SSPR) w usłudze Azure Active directory (Azure AD) można dostosować środowisko aby odpowiednio do potrzeb ich użytkowników.

## <a name="customize-the-contact-your-administrator-link"></a>Dostosuj link "Skontaktuj się z administratorem"

Nawet jeśli nie włączono funkcji samoobsługowego resetowania HASEŁ, użytkownicy nadal mają link "Skontaktuj się z administratorem" hasła portal resetowania. Jeśli użytkownik wybierze ten link go albo:

* Wysłanie wiadomości e-mail administratorów i pyta, aby uzyskać pomoc podczas zmieniania hasła użytkownika.
* Wysyła użytkowników do adresu URL, który należy określić w celu uzyskania pomocy.

Zaleca się Ustaw ten kontakt na coś adres e-mail lub witrynę sieci Web, które użytkownicy już używane w przypadku pytań dotyczących pomocy technicznej.

![Przykładowe żądanie, aby zresetować wysyłanie wiadomości e-mail do administratora][Contact]

Kontaktowy adres e-mail są wysyłane do następujących adresatów w następującej kolejności:

1. Jeśli **administrator haseł** rola jest przypisywana, są powiadamiani, Administratorzy, z tą rolą.
2. Jeśli administratorzy nie hasła są przypisywane, następnie administratorom **administrator użytkowników** powiadomienie o roli.
3. Jeśli żaden z poprzednich role są przydzielane, a następnie **Administratorzy globalni** są powiadamiani.

We wszystkich przypadkach są powiadamiani, maksymalnie 100 adresatów.

Aby dowiedzieć się więcej o rolach innego administratora i przypisywania ich, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Wyłącz "Skontaktuj się z administratorem" wiadomości e-mail

Jeśli Twoja organizacja nie chce otrzymywać powiadomienia, żądań usługi resetowania administratorów o hasło, aby umożliwić następującej konfiguracji:

* Włącz samoobsługowe resetowanie haseł dla wszystkich użytkowników końcowych. Ta opcja jest w obszarze **resetowania hasła** > **właściwości**. Jeśli nie chcesz, aby użytkownikom Resetowanie swoich haseł, możesz ograniczyć dostęp do pustej grupy. *Ta opcja nie jest zalecane.*
* Dostosuj link pomocy technicznej, aby podać adres URL sieci web lub mailto: adres, z którego użytkownicy mogą uzyskać pomoc. Ta opcja jest w obszarze **resetowania hasła** > **dostosowywania** > **niestandardowe pomocy technicznej w wiadomości e-mail lub adres URL**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Dostosowywanie strony logowania usług AD FS dla funkcji samoobsługowego resetowania HASEŁ

Administratorów usługi Active Directory Federation Services (AD FS) można dodać link do ich strony logowania, korzystając z informacji przedstawionych w [opisu strony logowania Dodaj](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) artykułu.

Aby dodać łącze do strony logowania usług AD FS, wpisz następujące polecenie na serwerze usług AD FS. Użytkownicy mogą ta strona umożliwia wprowadź przepływu pracy funkcji samoobsługowego resetowania HASEŁ.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Dostosowywanie logowania strony i dostęp do panelu wyglądu i działania

Można dostosować stronę logowania. Możesz dodać logo, który pojawia się wraz z obrazu, który pasuje do oznaczenie marką firmy.

Grafiki, które wybierzesz są wyświetlane w następujących okolicznościach:

* Po użytkownik musi wprowadzić swoją nazwę użytkownika
* Jeśli użytkownik uzyskuje dostęp do dostosowany adres URL:
   * Przekazując `whr` parametr hasła resetowania strony, takie jak `https://login.microsoftonline.com/?whr=contoso.com`
   * Przekazując `username` parametr hasła resetowania strony, takie jak `https://login.microsoftonline.com/?username=admin@contoso.com`

Informacje dotyczące sposobu konfigurowania firmowe w artykule [dodać znakowanie firmowe do strony logowania w usłudze Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Nazwa katalogu

Możesz zmienić atrybut nazwy katalogu, w obszarze **usługi Azure Active Directory** > **właściwości**. Możesz wyświetlić przyjazną nazwą w danej organizacji jest widoczny w portalu, a w przypadku zautomatyzowanych komunikacji. Ta opcja jest najbardziej widoczne w zautomatyzowane wiadomości e-mail w formularzach, które należy wykonać:

* Przyjazna nazwa w wiadomości e-mail, na przykład "Microsoft w imieniu pokaz firmy CONTOSO"
* Wiersz tematu wiadomości e-mail, na przykład "CONTOSO pokaz kod weryfikacyjny e-mail konta"

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Skontaktuj się z administratorem, aby uzyskać pomoc, resetowanie hasła przykładu poczty e-mail"
