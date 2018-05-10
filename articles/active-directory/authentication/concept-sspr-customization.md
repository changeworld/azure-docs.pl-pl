---
title: Samoobsługowego resetowania hasła dostosowania — Azure Active Directory
description: Zresetuj opcje dostosowywania dla usługi Azure AD samoobsługi hasła
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: ffd12d03dffb5deafc8605cc7352bd71d588d235
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Dostosowywanie funkcji usługi Azure AD dla samoobsługowego resetowania hasła

Informatycy, którzy mają zostać wdrożone samoobsługowego resetowania hasła (SSPR) w usłudze Azure Active directory (Azure AD) można dostosować środowisko odpowiada potrzebom użytkownika.

## <a name="customize-the-contact-your-administrator-link"></a>Dostosowywanie łącze "Skontaktuj się z administratorem"

Nawet jeśli nie włączono funkcji SSPR, użytkownicy nadal mają łącze "Skontaktuj się z administratorem" hasło portal resetowania. Jeśli użytkownik wybierze ten link jej albo:
   * Wiadomości e-mail z administratorami w Twojej organizacji i zapyta, aby uzyskać pomoc w odniesieniu do zmiany hasła użytkownika. 
   * Wysyła użytkowników na adres URL określony dla pomocy. 

Zalecane ustawienie tego kontaktu inny, takie jak adres e-mail lub witryny sieci Web, który użytkownicy już używać dla pytań.

![Skontaktuj się z][Contact]

Kontaktowy adres e-mail są wysyłane do następujących adresatów w następującej kolejności:

1. Jeśli **hasło administratora** rola jest przypisywana, administratorów z tej roli są powiadomienia.
2. Jeśli hasło administratorów nie ma przypisanych, następnie Administratorzy z **użytkownika administratora** są powiadamiani o roli.
3. Jeśli żaden z poprzednich ról nie ma przypisanych, a następnie **Administratorzy globalni** wyświetlone odpowiednie powiadomienie.

We wszystkich przypadkach są powiadamiani o maksymalnie 100 adresatów.

Aby dowiedzieć się więcej o rolach administratora różnych i jak przypisać je, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](../active-directory-assign-admin-roles-azure-portal.md).

### <a name="disable-contact-your-administrator-emails"></a>Wyłączanie "Skontaktuj się z administratorem" wiadomości e-mail

Jeśli Twoja organizacja nie ma być powiadamiany żądań resetowania administratorów o hasło, możesz włączyć następującej konfiguracji:

* Włącz samoobsługowego resetowania haseł dla wszystkich użytkowników końcowych. Ta opcja jest w obszarze **resetowania hasła** > **właściwości**.
  
  Jeśli nie chcesz resetować swoje hasła użytkowników można zakresu dostępu do pustej grupy. *Nie zalecamy tej opcji.*
* Dostosowywanie link pomocy technicznej, aby podać adres URL sieci web lub mailto: adres, który użytkownicy mogą używać, aby uzyskać pomoc. Ta opcja jest w obszarze **resetowania hasła** > **dostosowywania** > **niestandardowe pomoc techniczna e-mail lub adres URL**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Dostosowywanie strony logowania usług AD FS dla usługi SSPR

Administratorzy usługi Active Directory Federation Services (AD FS) można dodać łącza do ich strony logowania przy użyciu wskazówek w [dodawanie opisu strony logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) artykułu.

Aby dodać łącze do strony logowania usług AD FS, wpisz następujące polecenie na serwerze usług AD FS. Użytkownicy mogą ta strona umożliwia wprowadź SSPR przepływu pracy.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Dostosowywanie logowania dostępu i stronę panelu Wygląd i działanie

Można dostosować stronę logowania. Można dodać logo, które pojawia się wraz z obrazem spełniającym znakowaniu firmy.

Wybrane grafiki są przedstawione w następujących okolicznościach:

* Gdy użytkownik wprowadzi swoją nazwę użytkownika
* Jeśli użytkownik uzyskuje dostęp do niestandardowych adresu URL:
    * Przez przekazanie *godz. pracy* parametr hasło zresetować strony, tak samo, jak "https://login.microsoftonline.com/?whr=contoso.com"
    * Przez przekazanie *username* parametr hasło zresetować strony, tak samo, jak "https://login.microsoftonline.com/?username=admin@contoso.com"

Wyszukiwanie szczegółowych informacji na temat konfigurowania firmowe w artykule [dodać znakowanie firmowe do strony logowania w usłudze Azure AD](../customize-branding.md).

### <a name="directory-name"></a>Nazwa katalogu

Można zmienić atrybutu nazwy katalogu, w obszarze **usługi Azure Active Directory** > **właściwości**. Można wyświetlić organizacji przyjazną nazwę, która jest widoczna w portalu i automatyczne komunikacji. Ta opcja jest najbardziej widoczne w zautomatyzowane wiadomości e-mail, formularzy, które należy wykonać:

* Przyjazna nazwa w wiadomości e-mail, na przykład "Microsoft imieniu pokaz firmy CONTOSO"
* Wiersz tematu wiadomości e-mail, na przykład "CONTOSO demonstracyjna konta e-mail kod weryfikacyjny"

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Skontaktuj się z administratorem, aby uzyskać pomoc zresetowanie Twojego hasła przykład poczty e-mail"
