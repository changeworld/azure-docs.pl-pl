---
title: Rozpoczynanie pracy z usługą połączone rejestracji dla usługi Azure AD SSPR oraz uwierzytelnianie wieloskładnikowe (wersja zapoznawcza)
description: Włącz uwierzytelnianie wieloskładnikowe AD systemu Azure w połączeniu i Samoobsługowe resetowanie haseł rejestracji (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ead303257b0b5a4b56803abe57a0101b8f031c0
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56589055"
---
# <a name="enable-combined-security-information-registration-preview"></a>Włącz połączone zabezpieczeń informacji o rejestracji (wersja zapoznawcza)

Przed włączeniem nowego środowiska pracy, zapoznaj się z artykułem [rejestracji informacje zabezpieczeń (wersja zapoznawcza) w połączeniu](concept-registration-mfa-sspr-combined.md) zapewnienie poznać funkcje i wpływ tej funkcji.

![Środowisko rejestracji rozszerzone informacje zabezpieczeń połączone zażądać więcej informacji podczas logowania. Rejestrowanie aplikacji Microsoft Authenticator jako pierwsza metoda pokazano w przykładzie.](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Połączone zabezpieczeń informacji o rejestracji dla usługi Azure Multi-Factor Authentication i Azure AD samoobsługowego resetowania haseł to funkcja publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="enable-combined-registration"></a>Włączanie rejestracji łączonej

Wykonaj poniższe kroki, aby włączyć rejestrację połączone:

1. Zaloguj się do witryny Azure portal jako administrator globalny lub administrator użytkowników.
2. Przejdź do **usługi Azure Active Directory** > **ustawienia użytkownika** > **Zarządzanie ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.
3. W obszarze **użytkownicy mogą korzystać z funkcji rejestrowania i zarządzanie informacje zabezpieczające w wersji zapoznawczej — odświeżanie**, włączenia dla **wybrane** grupy użytkowników lub dla **wszystkich** użytkowników.

![Włącz środowisko zabezpieczeń połączone informacje o wersji zapoznawczej dla wszystkich użytkowników w portalu usługi Azure AD](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!NOTE]
> Po włączeniu połączone rejestracji użytkowników, którzy zarejestrować lub upewnij się, że ich numer telefonu lub aplikacji mobilnej za pomocą nowego środowiska można ich używać uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ, jeśli te metody są włączone w zasadach uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ. Jeśli wyłączysz następnie tego środowiska, użytkownicy, którzy przejdź do poprzedniej rejestracji SSPR strony w [https:/aka.ms/ssprsetup](https:/aka.ms/ssprsetup) będą musieli wykonywać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do strony.

## <a name="next-steps"></a>Kolejne kroki

[Dostępne metody uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ](concept-authentication-methods.md)

[Konfigurowanie samoobsługowego resetowania haseł](howto-sspr-deployment.md)

[Konfigurowanie usługi Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Rozwiązywanie problemów z połączone zabezpieczeń informacji o rejestracji](howto-registration-mfa-sspr-combined-troubleshoot.md)