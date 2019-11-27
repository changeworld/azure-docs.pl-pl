---
title: Dostosuj funkcję samoobsługowego resetowania haseł — Azure Active Directory
description: Opcje dostosowywania dla funkcji samoobsługowego resetowania haseł w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dfd035f73ea529ddb55bac6ce601185fda51a4d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381936"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Dostosowywanie funkcji usługi Azure AD do samoobsługowego resetowania hasła

Specjaliści IT, którzy chcą wdrożyć funkcję samoobsługowego resetowania hasła (SSPR) w usłudze Azure Active Directory (Azure AD), mogą dostosować środowisko w celu dopasowania ich do potrzeb użytkowników.

## <a name="customize-the-contact-your-administrator-link"></a>Dostosuj link "Skontaktuj się z administratorem"

Użytkownicy samoobsługowego resetowania hasła mają dostępne dla nich link "Skontaktuj się z administratorem" w portalu resetowania haseł. Jeśli użytkownik wybierze ten link, wykona jedną z dwóch czynności:

* Jeśli został pozostawiony w stanie domyślnym:
   * Wiadomość e-mail jest wysyłana do administratorów i prosi o zapewnienie pomocy przy zmianie hasła użytkownika. Zapoznaj się z [przykładową pocztą e-mail](#sample-email) poniżej.
* Jeśli są dostosowane:
   * Wysyła użytkownika do strony sieci Web lub adresu e-mail określonego przez administratora w celu uzyskania pomocy.

> [!TIP]
> W przypadku dostosowania tego ustawienia zalecamy ustawienie tej opcji dla użytkowników, którzy już znają obsługę

> [!WARNING]
> Jeśli to ustawienie zostanie dostosowane przy użyciu adresu e-mail i konta wymagającego zresetowania hasła, użytkownik może nie być w stanie uzyskać pomocy.

### <a name="sample-email"></a>Przykładowa wiadomość e-mail

![Przykładowe żądanie zresetowania wiadomości e-mail wysłanej do administratora][Contact]

Kontaktowy adres e-mail jest wysyłany do następujących adresatów w następującej kolejności:

1. Jeśli przypisano rolę **administratora hasła** , Administratorzy z tą rolą są powiadamiani.
2. Jeśli nie przypisano żadnych administratorów haseł, zostaną powiadomieni Administratorzy z rolą **administratora użytkownika** .
3. Jeśli żadna z poprzednich ról nie zostanie przypisana, **administratorzy globalni** są powiadamiani.

We wszystkich przypadkach powiadamia się maksymalnie 100 adresatów.

Aby dowiedzieć się więcej o różnych rolach administratorów i sposobach ich przypisywania, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Wyłącz wiadomości e-mail "Skontaktuj się z administratorem"

Jeśli organizacja nie chce powiadamiać administratorów o żądaniach resetowania haseł, można włączyć następującą konfigurację:

* Włącz Samoobsługowe resetowanie haseł dla wszystkich użytkowników końcowych. Ta opcja służy do **resetowania hasła** > **Właściwości**. Jeśli nie chcesz, aby użytkownicy zresetują swoje hasła, możesz ograniczyć dostęp do pustej grupy. *Nie zalecamy tej opcji.*
* Dostosuj link do pomocy technicznej, aby podać internetowy adres URL lub mailto: adres, za pomocą którego użytkownicy mogą uzyskać pomoc. Ta opcja służy do **resetowania hasła** > **dostosowania** > **e-mail lub adres URL niestandardowego działu pomocy technicznej**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Dostosowywanie strony logowania AD FS SSPR

Administratorzy Active Directory Federation Services (AD FS) mogą dodać łącze do strony logowania, korzystając ze wskazówek dostępnych w artykule [Dodawanie opisu strony logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) .

Aby dodać łącze do strony logowania AD FS, użyj następującego polecenia na serwerze AD FS. Użytkownicy mogą korzystać z tej strony, aby wprowadzić przepływ pracy SSPR.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Dostosowywanie strony logowania i wyglądu i działania panelu dostępu

Możesz dostosować stronę logowania. Możesz dodać logo, które pojawia się wraz z obrazem, który pasuje do marki firmy.

Wybrane grafiki są wyświetlane w następujących okolicznościach:

* Gdy użytkownik wprowadzi swoją nazwę użytkownika
* Jeśli użytkownik uzyskuje dostęp do dostosowanego adresu URL:
   * Przekazując parametr `whr` do strony resetowania hasła, na przykład `https://login.microsoftonline.com/?whr=contoso.com`
   * Przekazując parametr `username` do strony resetowania hasła, na przykład `https://login.microsoftonline.com/?username=admin@contoso.com`

Szczegółowe informacje na temat konfigurowania znakowania firmowego w artykule [Dodawanie znakowania firmowego do strony logowania w usłudze Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Nazwa katalogu

Można zmienić atrybut nazwy katalogu w obszarze **Azure Active Directory** **Właściwości** > . Można wyświetlić przyjazną nazwę organizacji, która jest widoczna w portalu i w zautomatyzowanym komunikacie. Ta opcja jest najbardziej widoczna w zautomatyzowanych wiadomościach e-mail w następujących formularzach:

* Przyjazna nazwa w wiadomości e-mail, na przykład "Firma Microsoft w imieniu firmy CONTOSO demonstracyjna"
* Wiersz tematu w wiadomości e-mail, na przykład "kod weryfikacyjny e-mail konta demonstracji CONTOSO"

## <a name="next-steps"></a>Następne kroki

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
* [Myślę, że coś jest zerwane. Jak mogę Rozwiązywanie problemów z SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Skontaktuj się z administratorem, aby uzyskać pomoc dotyczącą resetowania przykładu wiadomości e-mail z hasłem"
