---
title: Dostawcy usługi Azure MFA — Azure Active Directory
description: Kiedy należy używać dostawcy uwierzytelniania z usługą Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d4b89f7416847e01cad8cb4f9bc52248d09170d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382002"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Kiedy używać dostawcy usługi Azure Multi-Factor Authentication

Weryfikacja dwuetapowa jest domyślnie dostępna dla administratorów globalnych, którzy zarządzają użytkownikami usług Azure Active Directory i Office 365. Jeśli jednak chcesz korzystać z [funkcji zaawansowanych](howto-mfa-mfasettings.md), musisz kupić pełną wersję usługi Azure Multi-Factor Authentication (MFA).

Dostawca usługi Azure MFA jest używany do korzystania z funkcji udostępnionych przez usługę Azure Multi-Factor Authentication dla użytkowników, którzy **nie mają licencji**.

> [!NOTE]
> Obowiązujące 1 września, 2018 nowych dostawców uwierzytelniania nie można już tworzyć. Istniejący dostawcy uwierzytelniania mogą nadal być używane i aktualizowane, ale migracja nie jest już możliwa. Uwierzytelnianie wieloskładnikowe będzie nadal dostępne jako funkcja w Azure AD — wersja Premium licencji.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Ostrzeżenia związane z zestawem SDK usługi Azure MFA

Należy zauważyć, że zestaw SDK jest przestarzały i będzie kontynuował pracę dopiero po 14 listopada 2018. Po tym czasie wywołania do zestawu SDK będą kończyć się niepowodzeniem.

## <a name="what-is-an-mfa-provider"></a>Co to jest dostawca usługi MFA?

Istnieją dwa typy dostawców uwierzytelniania, a różnica polega na tym, że opłaty są naliczane za subskrypcję platformy Azure. W modelu opartym na uwierzytelnianiu jest uwzględniana liczba uwierzytelnień w dzierżawie w ciągu miesiąca. Ta opcja jest przydatna, jeśli masz pewną liczbę użytkowników, którzy uwierzytelniają się tylko czasami. W modelu opartym na użytkownikach jest uwzględniana liczba osób w dzierżawie, które w danym miesiącu korzystają z weryfikacji dwuetapowej. Ta opcja jest przydatna, jeśli masz użytkowników z licencjami, ale musisz objąć uwierzytelnianiem wieloskładnikowym dodatkowych użytkowników, którzy nie mają licencji.

## <a name="manage-your-mfa-provider"></a>Zarządzanie dostawcą usługi MFA

Po utworzeniu dostawcy usługi MFA nie możesz zmienić modelu użycia (na włączonego użytkownika lub na uwierzytelnienie).

W przypadku zakupionej wystarczającej liczby licencji na pokrycie wszystkich użytkowników włączonych dla usługi MFA można całkowicie usunąć dostawcę usługi MFA.

Jeśli dostawca usługi MFA nie jest połączony z dzierżawą usługi Azure AD lub łączysz nowego dostawcę usługi MFA z inną dzierżawą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są przenoszone. Ponadto istniejące serwery usługi Azure MFA muszą zostać ponownie aktywowane przy użyciu poświadczeń aktywacji wygenerowanych przez dostawcę usługi MFA. Ponowne aktywowanie serwerów usługi MFA w celu połączenia ich z dostawcą usługi MFA nie ma wpływu na połączenia telefoniczne i wiadomości SMS, ale powiadomienia aplikacji mobilnej przestaną działać dla wszystkich użytkowników, dopóki nie aktywują aplikacji mobilnej.

### <a name="removing-an-authentication-provider"></a>Usuwanie dostawcy uwierzytelniania

> [!CAUTION]
> Nie ma potwierdzenia podczas usuwania dostawcy uwierzytelniania. Wybranie pozycji **Usuń** jest procesem trwałym.

Dostawców uwierzytelniania można znaleźć w **Azure Portal** > **Azure Active Directory** > **dostawca** > **MFA** . Kliknij wyświetlone dostawcy, aby wyświetlić szczegóły i konfiguracje skojarzone z tym dostawcą.

Przed usunięciem dostawcy uwierzytelniania Zwróć uwagę na wszelkie dostosowane ustawienia skonfigurowane w dostawcy. Zdecyduj, które ustawienia należy zmigrować do ustawień ogólnych usługi MFA od dostawcy, i wykonaj migrację tych ustawień. 

Serwery usługi Azure MFA połączone z dostawcami będą musiały zostać ponownie aktywowane przy użyciu poświadczeń wygenerowanych w obszarze **Azure Portal** > **Azure Active Directory** > **Ustawienia serwera** **MFA** > . Przed ponownym uaktywnieniem należy usunąć następujące pliki z katalogu `\Program Files\Multi-Factor Authentication Server\Data\` na serwerach usługi Azure MFA w Twoim środowisku:

- caCert
- zarchiwizowan
- groupCACert
- groupKey
- groupName
- Element LicenseKey
- pkey

![Usuwanie dostawcy uwierzytelniania z Azure Portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Po potwierdzeniu, że wszystkie ustawienia zostały poddane migracji, możesz przejść do **Azure Portal** >  ** > Azure Active Directory** > **dostawców** usługi **MFA** i wybrać wielokropek **...** i wybrać pozycję **Usuń**.

> [!WARNING]
> Usunięcie dostawcy uwierzytelniania spowoduje usunięcie wszelkich informacji o raportowaniu skojarzonych z tym dostawcą. Możesz chcieć zapisać raporty aktywności przed usunięciem dostawcy.

> [!NOTE]
> Użytkownicy ze starszymi wersjami aplikacji Microsoft Authenticator i serwera usługi Azure MFA mogą wymagać ponownego zarejestrowania aplikacji.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie ustawień usługi Multi-Factor Authentication](howto-mfa-mfasettings.md)
