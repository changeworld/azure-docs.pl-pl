---
title: Dostosowywanie samoobsługowego resetowania hasła — usługa Azure Active Directory
description: Opcje dostosowywania samoobsługowego resetowania hasła usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979457"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Dostosowywanie funkcji usługi Azure AD do samodzielnego resetowania hasła

Informatycy, którzy chcą wdrożyć samoobsługowe resetowanie haseł (SSPR) w usłudze Azure Active Directory (Azure AD) mogą dostosować środowisko do potrzeb użytkowników.

## <a name="customize-the-contact-your-administrator-link"></a>Dostosowywanie linku "Skontaktuj się z administratorem"

Użytkownicy samoobsługowego resetowania hasła mają link "Skontaktuj się z administratorem" dostępny w portalu resetowania hasła. Jeśli użytkownik wybierze to łącze, wykona jedną z dwóch czynności:

* W przypadku pozostawienia w stanie domyślnym:
   * Wiadomość e-mail jest wysyłana do administratorów i prosi ich o pomoc w zmianie hasła użytkownika. Zobacz [przykładową wiadomość e-mail](#sample-email) poniżej.
* Jeśli dostosowano:
   * Wysyła użytkownika na stronę sieci Web lub adres e-mail określony przez administratora w celu uzyskania pomocy.

> [!TIP]
> Jeśli dostosujesz to, zalecamy ustawienie tego na coś, co użytkownicy są już zaznajomieni z pomocą techniczną

> [!WARNING]
> Jeśli dostosujesz to ustawienie za pomocą adresu e-mail i konta, które wymaga zresetowania hasła, użytkownik może nie być w stanie poprosić o pomoc.

### <a name="sample-email"></a>Przykładowa wiadomość e-mail

![Przykładowe żądanie zresetowania wiadomości e-mail wysłanej do administratora][Contact]

Wiadomość e-mail kontaktowa jest wysyłana do następujących adresatów w następującej kolejności:

1. Jeśli przypisana jest rola **administratora pomocy technicznej** lub rola **administratora haseł,** administratorzy z tymi rolami zostaną powiadomieni.
1. Jeśli nie przypisano żadnych administratorów pomocy technicznej ani administratora haseł, administratorzy z rolą **administratora użytkownika** zostaną powiadomieni.
1. Jeśli żadna z poprzednich ról nie zostanie przypisana, **administratorzy globalni** zostaną powiadomieni.

We wszystkich przypadkach zgłasza się maksymalnie 100 odbiorców.

Aby dowiedzieć się więcej o różnych rolach administratora i sposobie ich [przypisywania, zobacz Przypisywanie ról administratora w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Wyłączanie wiadomości e-mail "Skontaktuj się z administratorem"

Jeśli organizacja nie chce powiadamiać administratorów o żądaniach resetowania hasła, można włączyć następującą konfigurację:

* Włącz samoobsługowe resetowanie hasła dla wszystkich użytkowników końcowych. Ta opcja znajduje się w obszarze Właściwości **resetowania** > **hasła**. Jeśli nie chcesz, aby użytkownicy resetowali własne hasła, możesz mieć dostęp do pustej grupy. *Nie zalecamy tej opcji.*
* Dostosuj łącze pomocy technicznej, aby podać internetowy adres URL lub adres mailto: używany przez użytkowników do uzyskania pomocy. Ta opcja znajduje się w obszarze Adres**e-mail lub adres URL niestandardowego pomocy technicznej** **do dostosowywania** > **hasła** > .

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Dostosowywanie strony logowania usług AD FS dla sspru

Administratorzy usług federacyjnych Active Directory (AD FS) mogą dodawać łącze do strony logowania, korzystając ze wskazówek zawartych w artykule [Dodaj opis strony logowania.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)

Aby dodać łącze do strony logowania usług AD FS, użyj następującego polecenia na serwerze usług AD FS. Użytkownicy mogą użyć tej strony, aby wprowadzić przepływ pracy SSPR.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Dostosowywanie wyglądu i wyglądu strony logowania oraz panelu dostępu

Stronę logowania można dostosować. Możesz dodać logo, które pojawi się wraz z obrazem, który pasuje do marki twojej firmy.

Wybrana grafika jest wyświetlana w następujących okolicznościach:

* Po wprowadzeniu nazwy użytkownika
* Jeśli użytkownik uzyskuje dostęp do dostosowanego adresu URL:
   * Przekazując `whr` parametr do strony resetowania hasła, np.`https://login.microsoftonline.com/?whr=contoso.com`
   * Przekazując `username` parametr do strony resetowania hasła, np.`https://login.microsoftonline.com/?username=admin@contoso.com`

Szczegółowe informacje na temat konfigurowania znakowania firmy znajdują się w artykule [Dodawanie znakowania firmy do strony logowania w usłudze Azure AD.](../fundamentals/customize-branding.md)

### <a name="directory-name"></a>Nazwa katalogu

Atrybut nazwy katalogu można zmienić w obszarze Właściwości **usługi Azure Active Directory** > **Properties**. Można wyświetlić przyjazną nazwę organizacji widoczną w portalu i w zautomatyzowanej komunikacji. Ta opcja jest najbardziej widoczna w automatycznych wiadomościach e-mail w kolejnych formularzach:

* Przyjazna nazwa w wiadomości e-mail, na przykład "Microsoft w imieniu wersji demonstracyjnej CONTOSO"
* Wiersz tematu w wiadomości e-mail, na przykład "Kod weryfikacyjny konta demonstracyjnego CONTOSO"

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
* [Myślę, że coś jest zepsute. Jak rozwiązać problem z łatem SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Skontaktuj się z administratorem, aby uzyskać pomoc dotyczącą resetowania przykładu adresu e-mail z hasłem"
