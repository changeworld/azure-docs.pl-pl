---
title: Wprowadzenie do połączonej rejestracji usługi Azure AD SSPR i uwierzytelniania wieloskładnikowego (wersja zapoznawcza) — Azure Active Directory
description: Włączanie połączonego uwierzytelniania wieloskładnikowego usługi Azure AD i samoobsługowego rejestrowania funkcji resetowania haseł (wersja zapoznawcza)
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
ms.openlocfilehash: 9fc30c3273528b8cbc08681950e0bd5f03ec7890
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69561277"
---
# <a name="enable-combined-security-information-registration-preview"></a>Włącz rejestrację połączonych informacji o zabezpieczeniach (wersja zapoznawcza)

Przed włączeniem nowego środowiska zapoznaj się z artykułem [łączenie informacji o zabezpieczeniach (wersja zapoznawcza)](concept-registration-mfa-sspr-combined.md) w celu zapoznania się z funkcjami i efektami tej funkcji.

![Ulepszone środowisko rejestracji informacji o zabezpieczeniach](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Rejestracja informacji o zabezpieczeniach dla usługi Azure MFA Authentication i usługi Azure Active Directory (Azure AD) samoobsługowego resetowania hasła jest publiczną funkcją w wersji zapoznawczej usługi Azure AD. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Organizacje, które obsługują poprzednią wersję zapoznawczą rejestrowania i zarządzania informacjami o zabezpieczeniach, powinny wykonać poniższe kroki, aby włączyć Ulepszone środowisko w wersji zapoznawczej. W przypadku organizacji, które nie przełączają się na 25 września 2019, firma Microsoft przełączy użytkowników z poprzednią wersją zapoznawczą w celu zarejestrowania i zarządzania informacjami o zabezpieczeniach w udoskonalonym środowisku. 
> 
> Jeśli nie została włączona żadna wersja wersji zapoznawczej, nie wpłynie to na Twoją organizację.

## <a name="enable-combined-registration"></a>Włączanie rejestracji łączonej

Wykonaj następujące kroki, aby włączyć rejestrację połączoną:

1. Zaloguj się do Azure Portal jako administrator użytkownika lub Administrator globalny.
2. Przejdź do pozycji **Azure Active Directory** > **Ustawienia** > użytkownika**Zarządzaj ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.
3. W obszarze **Użytkownicy mogą korzystać z funkcji w wersji zapoznawczej na potrzeby rejestrowania i zarządzania informacjami o zabezpieczeniach — odświeżanie**, Wybieranie opcji do włączenia dla **wybranej** grupy użytkowników lub dla **wszystkich** użytkowników.

   ![Włącz środowisko wersji zapoznawczej połączonej informacji zabezpieczeń dla wszystkich użytkowników](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Od marca 2019 opcje połączeń telefonicznych nie będą dostępne dla usługi uwierzytelnianie wieloskładnikowe i SSPR użytkowników w bezpłatnych/bezpłatnych dzierżawach Azure AD. Ta zmiana nie wpłynie na wiadomości SMS. Opcje połączenia telefonicznego będą nadal dostępne dla użytkowników z płatnymi dzierżawami usługi Azure AD.

> [!NOTE]
> Po włączeniu łączenia się użytkowników, którzy rejestrują lub potwierdzają swój numer telefonu lub aplikację mobilną za pomocą nowego środowiska, mogą używać ich do uwierzytelniania wieloskładnikowego i SSPR, jeśli te metody są włączone w usłudze uwierzytelnianie wieloskładnikowe i SSPR jazd. Jeśli wyłączysz to środowisko, użytkownicy, którzy przejdą do poprzedniej strony rejestracji SSPR `https://aka.ms/ssprsetup` w programie, będą musieli przeprowadzić uwierzytelnianie wieloskładnikowe, aby uzyskać dostęp do strony.

Jeśli skonfigurowano listę przypisywania lokacji do strefy w programie Internet Explorer, następujące Lokacje muszą znajdować się w tej samej strefie:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Zasady dostępu warunkowego dla połączonej rejestracji

Zabezpieczanie, kiedy i jak użytkownicy rejestrują się do usługi Azure MFA Authentication i samoobsługowego resetowania hasła jest teraz możliwy w przypadku akcji użytkownika w zasadach dostępu warunkowego. Ta funkcja w wersji zapoznawczej jest dostępna dla organizacji, które włączyły [Podgląd rejestracji połączonej](../authentication/concept-registration-mfa-sspr-combined.md). Ta funkcjonalność może być włączona w organizacjach, w których użytkownicy mogą rejestrować się w usłudze Azure MFA Authentication i SSPR z centralnej lokalizacji, takiej jak Zaufane lokalizacje sieciowe podczas dołączania do usługi kadr. Aby uzyskać więcej informacji na temat tworzenia zaufanych lokalizacji w dostępie warunkowym, zobacz artykuł [jaki jest warunek lokalizacji w Azure Active Directory dostęp warunkowy?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Utwórz zasady, aby wymagać rejestracji z zaufanej lokalizacji

Poniższe zasady mają zastosowanie do wszystkich wybranych użytkowników, którzy próbują zarejestrować się przy użyciu połączonego środowiska rejestracji i blokują dostęp, chyba że nawiązują połączenie z lokalizacji oznaczonej jako zaufane sieci.

![Tworzenie zasad urzędu certyfikacji w celu kontrolowania rejestracji informacji zabezpieczających](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. W **Azure Portal**przejdź do **Azure Active Directory** > **dostęp warunkowy**
1. Wybierz pozycję **Nowe zasady**
1. W polu Nazwa wprowadź nazwę dla tych zasad. Przykładowa **rejestracja informacji o zabezpieczeniach w zaufanych sieciach**
1. W obszarze **przypisania**kliknij pozycję **Użytkownicy i grupy**, a następnie wybierz użytkowników i grupy, do których te zasady mają być stosowane

   > [!WARNING]
   > Użytkownicy muszą mieć włączoną funkcję [Podgląd rejestracji połączonej](../authentication/howto-registration-mfa-sspr-combined.md).

1. W obszarze **aplikacje lub akcje w chmurze**wybierz pozycję **akcje użytkownika**, a następnie sprawdź pozycję **zarejestruj informacje zabezpieczające (wersja zapoznawcza)**
1. W obszarze **warunki** > **lokalizacji**
   1. Skonfiguruj **tak**
   1. Uwzględnij **dowolną lokalizację**
   1. Wyklucz **wszystkie Zaufane lokalizacje**
   1. Kliknij przycisk **gotowe** w bloku lokalizacje
   1. Kliknij przycisk **gotowe** w bloku warunki
1. W obszarze **Kontrola** > dostępu**Udziel**
   1. Kliknij pozycję **Blokuj dostęp**
   1. Następnie kliknij pozycję **Wybierz** .
1. Ustaw opcję **Włącz zasady** na wartość **włączone**
1. Następnie kliknij pozycję **Utwórz** .

## <a name="next-steps"></a>Następne kroki

[Dostępne metody uwierzytelniania wieloskładnikowego i SSPR](concept-authentication-methods.md)

[Konfigurowanie samoobsługowego resetowania hasła](howto-sspr-deployment.md)

[Konfigurowanie uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-getstarted.md)

[Rozwiązywanie problemów z rejestracją połączonych informacji zabezpieczających](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Jaki jest warunek lokalizacji w Azure Active Directory dostęp warunkowy?](../conditional-access/location-condition.md)
