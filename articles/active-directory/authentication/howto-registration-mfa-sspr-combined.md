---
title: Rozpoczynanie pracy z usługą połączone rejestracji dla usługi Azure AD SSPR oraz uwierzytelnianie wieloskładnikowe (wersja zapoznawcza) — usługi Azure Active Directory
description: Włącz uwierzytelnianie wieloskładnikowe AD systemu Azure w połączeniu i Samoobsługowe resetowanie haseł rejestracji (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280574"
---
# <a name="enable-combined-security-information-registration-preview"></a>Włącz połączone zabezpieczeń informacji o rejestracji (wersja zapoznawcza)

Przed włączeniem nowego środowiska pracy, zapoznaj się z artykułem [rejestracji informacje zabezpieczeń (wersja zapoznawcza) w połączeniu](concept-registration-mfa-sspr-combined.md) zapewnienie poznać funkcje i wpływ tej funkcji.

![Środowisko rejestracji rozszerzone informacje zabezpieczeń połączone](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Połączone zabezpieczeń informacji o rejestracji dla usługi Azure Multi-Factor Authentication i resetowania hasła usługi Azure Active Directory (Azure AD) to funkcja publicznej wersji zapoznawczej usługi Azure AD. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Włączanie rejestracji łączonej

Wykonaj następujące kroki, aby włączyć rejestrację połączone:

1. Zaloguj się do witryny Azure portal jako administrator globalny lub administrator użytkowników.
2. Przejdź do **usługi Azure Active Directory** > **ustawienia użytkownika** > **Zarządzanie ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.
3. W obszarze **użytkownicy mogą korzystać z funkcji rejestrowania i zarządzanie informacje zabezpieczające w wersji zapoznawczej — odświeżanie**, włączenia dla **wybrane** grupy użytkowników lub dla **wszystkich** użytkowników.

   ![Włącz środowisko zabezpieczeń połączone informacje o wersji zapoznawczej dla wszystkich użytkowników](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Począwszy od marca 2019 opcji połączenia telefonicznego nie będzie dostępna dla uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ użytkowników w dzierżawach bezpłatnej/wersji próbnej usługi Azure AD. Ta zmiana nie dotyczy wiadomości SMS. Opcje połączenia telefonicznego będą dostępne dla użytkowników w płatną dzierżaw usługi Azure AD.

> [!NOTE]
> Po włączeniu rejestracji połączone, użytkownicy, którzy rejestrowanie lub Potwierdzanie numeru telefonu lub aplikacji mobilnej za pomocą nowego środowiska można ich używać uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ, jeśli te metody są włączone w uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ zasady. Jeśli wyłączysz następnie tego środowiska, użytkownicy, którzy przejdź do poprzedniej rejestracji SSPR strony w `https:/aka.ms/ssprsetup` będą musieli wykonywać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do strony.

Jeśli strefa Lista przypisywanie witryn do zostały skonfigurowane w programie Internet Explorer, następujące witryny muszą znajdować się w tej samej strefy:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Kolejne kroki

[Dostępne metody uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ](concept-authentication-methods.md)

[Konfigurowanie samoobsługowego resetowania hasła](howto-sspr-deployment.md)

[Konfigurowanie usługi Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Rozwiązywanie problemów z połączone zabezpieczeń informacji o rejestracji](howto-registration-mfa-sspr-combined-troubleshoot.md)