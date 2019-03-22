---
title: Rozpoczynanie pracy z usługą połączone rejestracji dla usługi Azure AD SSPR oraz uwierzytelnianie wieloskładnikowe (wersja zapoznawcza)
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
ms.openlocfilehash: 2a6896e2b9633b8de679e8d14a7957dc0e3229e7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226729"
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

> [!IMPORTANT]
> Począwszy od marca 2019 opcji połączenia telefonicznego nie będą dostępne dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ użytkowników w dzierżawach bezpłatnej/wersji próbnej usługi Azure AD. Ta zmiana nie wpływa na wiadomości SMS. Połączenie telefoniczne będą nadal dostępne dla użytkowników w płatną dzierżaw usługi Azure AD. Ta zmiana ma wpływ tylko na dzierżaw bezpłatnej/wersji próbnej usługi Azure AD.

> [!NOTE]
> Po włączeniu połączone rejestracji użytkowników, którzy zarejestrować lub upewnij się, że ich numer telefonu lub aplikacji mobilnej za pomocą nowego środowiska można ich używać uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ, jeśli te metody są włączone w zasadach uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ. Jeśli wyłączysz następnie tego środowiska, użytkownicy, którzy przejdź do poprzedniej rejestracji SSPR strony w `https:/aka.ms/ssprsetup` będą musieli wykonywać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do strony.

Jeśli lista witryn do stref przypisania zostały skonfigurowane w programie Internet Explorer następujące witryny muszą być w tej samej strefie:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Kolejne kroki

[Dostępne metody uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ](concept-authentication-methods.md)

[Konfigurowanie samoobsługowego resetowania haseł](howto-sspr-deployment.md)

[Konfigurowanie usługi Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Rozwiązywanie problemów z połączone zabezpieczeń informacji o rejestracji](howto-registration-mfa-sspr-combined-troubleshoot.md)