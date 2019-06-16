---
title: Rozpoczynanie pracy z usługą połączone rejestracji dla usługi Azure AD SSPR oraz uwierzytelnianie wieloskładnikowe (wersja zapoznawcza) — usługi Azure Active Directory
description: Włącz uwierzytelnianie wieloskładnikowe AD systemu Azure w połączeniu i Samoobsługowe resetowanie haseł rejestracji (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d006bd36cc8f8c84fb13bae43702a3e472f8876a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113294"
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
> Po włączeniu rejestracji połączone, użytkownicy, którzy rejestrowanie lub Potwierdzanie numeru telefonu lub aplikacji mobilnej za pomocą nowego środowiska można ich używać uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ, jeśli te metody są włączone w uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ zasady. Jeśli wyłączysz następnie tego środowiska, użytkownicy, którzy przejdź do poprzedniej rejestracji SSPR strony w `https://aka.ms/ssprsetup` będą musieli wykonywać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do strony.

Jeśli strefa Lista przypisywanie witryn do zostały skonfigurowane w programie Internet Explorer, następujące witryny muszą znajdować się w tej samej strefy:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Zasady dostępu warunkowego dla rejestracji połączone

Zabezpieczanie, kiedy i jak rejestrowanie użytkowników na potrzeby usługi Azure Multi-Factor Authentication i samoobsługowego resetowania hasła jest możliwe z akcjami użytkownika zasad dostępu warunkowego. Tę funkcję wersji zapoznawczej jest dostępna dla organizacji, którzy włączyli [rejestracji (wersja zapoznawcza) w połączeniu](../authentication/concept-registration-mfa-sspr-combined.md). Ta funkcja może być włączona w organizacjach, w którym mają być użytkownikom na rejestrowanie dla usługi Azure Multi-Factor Authentication i samoobsługowego resetowania HASEŁ z centralnej lokalizacji, takich jak zaufanej lokalizacji sieciowej zestawu dokumentacji podczas dołączania HR. Aby uzyskać więcej informacji na temat tworzenia zaufanych lokalizacji w funkcji dostępu warunkowego, zobacz artykuł [co to jest warunek lokalizacji w usłudze Azure Active Directory dostępu warunkowego?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Utwórz zasady, które wymagają rejestracji z zaufanej lokalizacji

Następujące zasady mają zastosowanie do wszystkich wybranych użytkowników, który próbuje zarejestrować się przy użyciu rejestracji połączone środowisko i blokuje dostęp, chyba że jest nawiązywane z lokalizacji oznaczone jako zaufaną siecią.

![Tworzenie zasad dostępu Warunkowego do kontrolowania zabezpieczeń informacji o rejestracji](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. W **witryny Azure portal**, przejdź do **usługi Azure Active Directory** > **dostępu warunkowego**
1. Wybierz pozycję **Nowe zasady**
1. W polu Nazwa wprowadź nazwę dla tych zasad. Na przykład **połączone zabezpieczeń informacji o rejestracji w zaufanych sieciach**
1. W obszarze **przypisania**, kliknij przycisk **użytkowników i grup**, a następnie wybierz użytkowników i grupy mają te zasady mają dotyczyć

   > [!WARNING]
   > Użytkownicy, należy włączyć [rejestracji (wersja zapoznawcza) w połączeniu](../authentication/howto-registration-mfa-sspr-combined.md).

1. W obszarze **w chmurze aplikacji lub akcje**, wybierz opcję **akcje użytkownika**, sprawdź **zarejestrowanie informacji o zabezpieczeniach (wersja zapoznawcza)**
1. W obszarze **warunki** > **lokalizacje**
   1. Konfigurowanie **tak**
   1. Obejmują **dowolnego miejsca**
   1. Wyklucz **wszystkie zaufane lokalizacje**
   1. Kliknij przycisk **gotowe** bloku lokalizacje
   1. Kliknij przycisk **gotowe** bloku warunków
1. W obszarze **kontrole dostępu** > **przydział**
   1. Kliknij przycisk **blokują dostęp**
   1. Następnie kliknij przycisk **wybierz**
1. Ustaw **Włącz zasady** do **na**
1. Następnie kliknij przycisk **Create**

## <a name="next-steps"></a>Kolejne kroki

[Dostępne metody uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ](concept-authentication-methods.md)

[Konfigurowanie samoobsługowego resetowania haseł](howto-sspr-deployment.md)

[Konfigurowanie usługi Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Rozwiązywanie problemów z połączone zabezpieczeń informacji o rejestracji](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Co to jest warunek lokalizacji w usłudze Azure Active Directory dostępu warunkowego?](../conditional-access/location-condition.md)
