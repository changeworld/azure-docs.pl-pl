---
title: Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza) — Azure Active Directory
description: Logowanie do usługi Azure AD przy użyciu aplikacji Microsoft Authenticator bez użycia hasła (publiczna wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c464874708c7b93ec5620cc9ae253912ce1a4790
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357143"
---
# <a name="passwordless-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Logowanie za pomocą telefonu bez hasła przy użyciu aplikacji Microsoft Authenticator (publiczna wersja zapoznawcza)

Aplikacja Microsoft Authenticator może służyć do logowania się do dowolnego konta usługi Azure AD bez użycia hasła. Podobnie jak w przypadku technologii [Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby umożliwić poświadczenie użytkownika powiązanego z urządzeniem i używa biometrycznych lub PIN.

![Przykład logowania do przeglądarki z prośbą do użytkownika o zatwierdzenie logowania](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Zamiast wyświetlać monit o hasło po wprowadzeniu nazwy użytkownika, osoba, która włączyła logowanie za pomocą telefonu w aplikacji Microsoft Authenticator zobaczy komunikat informujący o naciśnięciu numeru w swojej aplikacji. W aplikacji użytkownik musi dopasować liczbę, wybrać pozycję Zatwierdź, a następnie podać numer PIN lub biometryczny, a następnie zostanie wykonane uwierzytelnianie.

## <a name="enable-my-users"></a>Włącz moich użytkowników

### <a name="tenant-prerequisites"></a>Wymagania wstępne dotyczące dzierżawców

* Usługa Azure Active Directory
* Użytkownicy końcowi włączeni do uwierzytelniania wieloskładnikowego systemu Azure
* Użytkownicy mogą rejestrować swoje urządzenia

### <a name="steps-to-enable"></a>Kroki umożliwiające włączenie

Wykonaj kroki opisane w artykule [Włączanie logowania bezhasła w usłudze Azure AD](howto-authentication-passwordless-enable.md#enable-new-passwordless-authentication-methods), aby włączyć metody uwierzytelniania bezhasło w katalogu.

> [!NOTE]
> Jeśli funkcja ta została wcześniej włączona dla dzierżawy przy użyciu skryptu programu PowerShell, ustawienie nowych zasad dla użytkowników i grup zastępuje istniejące zasady dla dzierżawy. 
>

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Jak użytkownicy końcowi włączają logowanie za telefon?

W publicznej wersji zapoznawczej nie ma możliwości wymuszania użytkownikom tworzenia lub używania tego nowego poświadczenia. Użytkownik końcowy będzie napotykał logowanie bezhasła, gdy administrator włączył swoją dzierżawę, a użytkownik zaktualizował Microsoft Authenticator aplikację, aby włączyć logowanie za pomocą telefonu.

> [!NOTE]
> Ta funkcja była w aplikacji od marca 2017, dlatego istnieje możliwość, że gdy zasady są włączone dla dzierżawy, użytkownicy mogą natychmiast napotkać ten przepływ. Weź pod uwagę i przygotuj użytkowników do tej zmiany.
>

1. Rejestrowanie w usłudze Azure wieloskładnikowe Authentication
1. Najnowsza wersja Microsoft Authenticator zainstalowana na urządzeniach z systemem iOS 8,0 lub nowszym albo systemem Android 6,0 lub nowszym.
1. Konto służbowe z powiadomieniami wypychanymi zostało dodane do aplikacji. Dokumentację użytkownika końcowego można znaleźć pod adresem [https://aka.ms/authappstart](https://aka.ms/authappstart).

Gdy użytkownik ma konto usługi MFA z powiadomieniami wypychanymi skonfigurowanymi w aplikacji Microsoft Authenticator, mogą wykonać kroki opisane w artykule zalogować się [przy użyciu telefonu, a nie hasła](../user-help/microsoft-authenticator-app-phone-signin-faq.md) do ukończenia rejestracji na telefonie.

## <a name="known-issues"></a>Znane problemy

### <a name="ad-fs-integration"></a>Integracja AD FS

Jeśli użytkownik włączył Microsoft Authenticator poświadczenie bezhasło, uwierzytelnianie dla tego użytkownika zawsze będzie domyślnie wysyłać powiadomienie o zatwierdzeniu. Ta logika uniemożliwia kierowanie do usług AD FS użytkowników w dzierżawie hybrydowej w celu weryfikacji logowania bez podejmowania dodatkowych kroków przez użytkownika w celu kliknięcia przycisku "Użyj hasła zamiast". Ten proces spowoduje również ominięcie wszelkich lokalnych zasad dostępu warunkowego i przepływów uwierzytelniania przekazywanego. Wyjątkiem od tego procesu jest login_hint, użytkownik zostanie przesunięty w przód do AD FS i zostanie pominięty przy użyciu poświadczeń bezhasłem.

### <a name="azure-mfa-server"></a>Serwer usługi Azure MFA

Użytkownicy końcowi korzystający z usług MFA za pomocą lokalnego serwera usługi Azure MFA w organizacji mogą nadal tworzyć i używać jednego poświadczenia logowania jednokrotnego bez hasła. Jeśli użytkownik próbuje uaktualnić wiele instalacji (5 +) Microsoft Authenticator przy użyciu poświadczenia, ta zmiana może spowodować wystąpienie błędu.  

### <a name="device-registration"></a>Rejestracja urządzenia

Jedno z wymagań wstępnych dotyczących tworzenia nowego, silnego poświadczenia polega na tym, że urządzenie, na którym się znajduje, jest zarejestrowane w ramach dzierżawy usługi Azure AD, do pojedynczego użytkownika. Ze względu na ograniczenia rejestracji urządzeń urządzenie może być rejestrowane tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe w aplikacji Microsoft Authenticator może być włączone do logowania za pomocą telefonu.

## <a name="next-steps"></a>Kolejne kroki

[Co to jest bezhasło?](concept-authentication-passwordless.md)

[Informacje o rejestracji urządzeń](../devices/overview.md#getting-devices-in-azure-ad)

[Dowiedz się więcej o usłudze Azure wieloskładnikowe Authentication](../authentication/howto-mfa-getstarted.md)
