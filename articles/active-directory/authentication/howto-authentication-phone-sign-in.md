---
title: Bez hasła Zaloguj się przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza) — usługi Azure Active Directory
description: Zaloguj się do usługi Azure AD przy użyciu aplikacji Microsoft Authenticator, bez użycia hasła (publiczna wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47e8541b82a1cd38f07684508a96b9789df20e92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359205"
---
# <a name="password-less-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Logowanie telefonem bez hasła przy użyciu aplikacji Microsoft Authenticator (publiczna wersja zapoznawcza)

Aplikacja Microsoft Authenticator może służyć do logowania się do dowolnego konta usługi Azure AD bez użycia hasła. Podobne do technologii [Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby umożliwić poświadczeń użytkownika, który jest powiązany z urządzeniem i używa biometryczne lub numer PIN.

![Przykład w przeglądarce logowaniu pytaniem do użytkownika zatwierdzić logowanie](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Zamiast zobaczyć monit o podanie hasła, po wprowadzeniu nazwy użytkownika, osoba, która ma włączone logowanie za pomocą telefonu w aplikacji Microsoft Authenticator, zostanie wyświetlony komunikat z informacją o mogą wybrać liczbę w swojej aplikacji. W aplikacji użytkownik musi być zgodna z liczbą, wybierz Zatwierdź, a następnie podać swoje numery PIN lub biometrycznych, następnie uwierzytelniania zostanie ukończone.

## <a name="enable-my-users"></a>Włączanie użytkowników

W publicznej wersji zapoznawczej administrator musi najpierw dodać zasad za pomocą programu powershell, aby zezwolić na korzystanie z poświadczeń w dzierżawie. Zapoznaj się z sekcją "Znane problemy" przed wykonaniem tego kroku.

### <a name="tenant-prerequisites"></a>Wymagania wstępne dotyczące dzierżawy

* Usługa Azure Active Directory
* Użytkownicy końcowi włączone dla usługi Azure Multi-Factor Authentication
* Użytkownicy mogą rejestrować swoje urządzenia

### <a name="steps-to-enable"></a>Kroki, aby włączyć

1. Upewnij się, że masz najnowszą wersję publicznej wersji zapoznawczej usługi Azure Active Directory w wersji 2 modułu programu PowerShell. Możesz też chcieć odinstalowanie i ponowne zainstalowanie, aby to sprawdzić, wykonując następujące polecenia:

    ```powershell
    Uninstall-Module -Name AzureADPreview
    Install-Module -Name AzureADPreview
    ```

2. Uwierzytelnianie w dzierżawie usługi Azure AD, aby użyć modułu programu PowerShell usługi Azure AD w wersji 2. Konto używane musi być Administrator zabezpieczeń lub Administrator globalny.

    ```powershell
    Connect-AzureAD
    ```

3. Utwórz zasady uwierzytelniania logowania:

    ```powershell
    New-AzureADPolicy -Type AuthenticatorAppSignInPolicy -Definition '{"AuthenticatorAppSignInPolicy":{"Enabled":true}}' -isOrganizationDefault $true -DisplayName AuthenticatorAppSignIn
    ```

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Jak Moi użytkownicy końcowi włączyć logowanie za pomocą telefonu?

W publicznej wersji zapoznawczej nie istnieje żaden sposób wymusić użytkowników do utworzenia lub używania tego nowego poświadczenia. Użytkownik końcowy tylko wystąpi logowania bez hasła, gdy administrator włączył swojej dzierżawy, a użytkownik zaktualizował swoich aplikacji Microsoft Authenticator, aby włączyć logowanie za pomocą telefonu.

> [!NOTE]
> Ta funkcja została w aplikacji od marca 2017 r., więc ma możliwość, że gdy jest włączona dla dzierżawy, mogą napotkać użytkownicy ten przepływ natychmiast. Należy pamiętać i przygotowanie użytkowników do tej zmiany.
>

1. Rejestrowanie w usłudze Azure Multi-Factor Authentication
1. Najnowsza wersja programu Microsoft Authenticator zainstalowanych na urządzeniach z systemem iOS 8.0 lub nowszej lub system Android 6.0 lub nowszej.
1. Konto służbowe lub szkolne za pomocą powiadomień push dodana do aplikacji. Dokumentacja użytkownika znajduje się w temacie [ https://aka.ms/authappstart ](https://aka.ms/authappstart).

Po użytkownik ma konto usługi MFA za pomocą powiadomień push w aplikacji Microsoft Authenticator, ich wykonaj kroki opisane w artykule [Zaloguj się przy użyciu telefonu, nie hasła](../user-help/microsoft-authenticator-app-phone-signin-faq.md) do ukończenia rejestracji logowania telefonu.

## <a name="known-issues"></a>Znane problemy

### <a name="ad-fs-integration"></a>Integracja z usługą AD FS

Po użytkownik włączył poświadczenia bez hasła Microsoft Authenticator, zawsze będzie domyślnie wysłanie powiadomienia do zatwierdzenia uwierzytelniania dla tego użytkownika. Tę logikę uniemożliwia użytkownikom w dzierżawie hybrydowego być kierowany do usług AD FS dla weryfikacji logowania bez użytkownika, wykonanie dodatkowych czynności kliknij pozycję "Zamiast tego użyj hasła." Ten proces będzie również pominąć wszystkie zasady dostępu warunkowego w środowisku lokalnym i przepływów uwierzytelniania przekazywanego. Wyjątek do tego procesu jest, jeśli login_hint jest określony, użytkownik będzie automatycznie przekazana dalej do usług AD FS, a Pomiń opcję, aby użyć poświadczeń bez hasła.

### <a name="azure-mfa-server"></a>Serwer usługi Azure MFA

Użytkownicy końcowi, którzy są włączone dla usługi MFA w organizacji na lokalnym serwerze Azure MFA można nadal tworzyć i używać poświadczeń logowania pojedynczego telefonu bez hasła. Jeśli użytkownik podejmie próbę uaktualnienia wiele instalacji (5 i nowsze) programu Microsoft Authenticator z poświadczeniami, ta zmiana może spowodować wystąpienie błędu.  

### <a name="device-registration"></a>Rejestracja urządzenia

To jeden z warunków wstępnych, aby utworzyć to poświadczenie nowych, silnych, że urządzenia, w którym znajduje się jest zarejestrowany w ramach dzierżawy usługi Azure AD, do indywidualnego użytkownika. Ze względu na ograniczenia rejestracji urządzeń urządzenie może być rejestrowane tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe lub szkolne w aplikacji Microsoft Authenticator można włączyć dla logowanie za pomocą telefonu.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o rejestracji urządzenia](../devices/overview.md#getting-devices-under-the-control-of-azure-ad)

[Więcej informacji na temat usługi Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
