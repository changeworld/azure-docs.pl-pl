---
title: Wprowadzenie do rejestracji połączonej — usługa Azure Active Directory
description: Włącz połączone uwierzytelnianie wieloskładnikowe usługi Azure AD i samoobsługową rejestrację resetowania haseł (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e22871114f260417e8a1514546c88ec4541064a4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309745"
---
# <a name="enable-combined-security-information-registration-preview"></a>Włączanie połączonej rejestracji informacji o zabezpieczeniach (wersja zapoznawcza)

Przed włączeniem nowego środowiska zapoznaj się z artykułem [Połączono rejestrację informacji o zabezpieczeniach (wersja zapoznawcza),](concept-registration-mfa-sspr-combined.md) aby upewnić się, że rozumiesz funkcjonalność i efekty tej funkcji.

![Połączone doświadczenie rejestracji informacji o zabezpieczeniach](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Połączone rejestracje informacji o zabezpieczeniach dla uwierzytelniania wieloskładnikowego platformy Azure i samoobsługowego resetowania hasła usługi Azure Active Directory (Azure AD) to publiczna funkcja w wersji zapoznawczej usługi Azure AD. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Organizacje, które włączyły poprzednią wersję zapoznawczą do rejestrowania informacji zabezpieczających i zarządzania nimi, powinny wykonać poniższe czynności, aby włączyć ulepszone środowisko w wersji zapoznawczej. W przypadku organizacji, które nie dokonają zmiany, 8 października 2019 r. firma Microsoft przełączy użytkowników poprzedniej wersji zapoznawczej do rejestrowania informacji zabezpieczających i zarządzania nimi na ulepszone środowisko. 
> 
> Jeśli nie włączono żadnej wersji wersji podglądu, twoja organizacja nie będzie miała wpływu.

## <a name="enable-combined-registration"></a>Włączanie rejestracji łączonej

Wykonaj następujące kroki, aby włączyć rejestrację połączoną:

1. Zaloguj się do witryny Azure portal jako administrator użytkownika lub administrator globalny.
2. Przejdź do ustawień**użytkownika** >  **usługi Azure Active Directory** > Zarządzanie**ustawieniami podglądu funkcji użytkownika**.
3. W obszarze **Użytkownicy można używać funkcji w wersji zapoznawczej do rejestrowania informacji zabezpieczających i zarządzania**nimi wybierz opcję włączenia dla **wybranej** grupy użytkowników lub dla **wszystkich** użytkowników.

   ![Włącz połączone środowisko podglądu informacji zabezpieczających dla wszystkich użytkowników](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> Począwszy od marca 2019 r. opcje połączeń telefonicznych nie będą dostępne dla użytkowników uwierzytelniania wieloskładnikowego i sspr w bezpłatnych/próbnych dzierżawach usługi Azure AD. Zmiana ta nie ma wpływu na wiadomości SMS. Opcje połączeń telefonicznych będą nadal dostępne dla użytkowników płatnych dzierżaw usługi Azure AD.

> [!NOTE]
> Po włączeniu rejestracji połączonej użytkownicy, którzy zarejestrują lub potwierdzą swój numer telefonu lub aplikację mobilną za pośrednictwem nowego środowiska, mogą używać ich do uwierzytelniania wieloskładnikowego i samookreślenia, jeśli te metody są włączone w zasadach uwierzytelniania wieloskładnikowego i samookapła. Jeśli następnie wyłączysz to środowisko, użytkownicy, którzy `https://aka.ms/ssprsetup` przejdą do poprzedniej strony rejestracji samookapła, będą musieli wykonać uwierzytelnianie wieloskładnikowe, zanim będą mogli uzyskać dostęp do strony.

Jeśli w programie Internet Explorer skonfigurowano listę przypisania witryny do stref, następujące witryny muszą znajdować się w tej samej strefie:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Zasady dostępu warunkowego dla połączonej rejestracji

Zabezpieczanie, kiedy i jak użytkownicy rejestrują się w celu autoryzacji wieloskładnikowej platformy Azure i samoobsługowego resetowania hasła jest teraz możliwe dzięki działaniom użytkownika w zasadach dostępu warunkowego. Ta funkcja podglądu jest dostępna dla organizacji, które włączyły [połączoną wersję zapoznawczą rejestracji](../authentication/concept-registration-mfa-sspr-combined.md). Ta funkcja może być włączona w organizacjach, w których użytkownicy mają rejestrować się w usłudze Azure Multi-Factor Authentication i SSPR z centralnej lokalizacji, takiej jak zaufana lokalizacja sieciowa podczas dołączania do działu kadr. Aby uzyskać więcej informacji na temat tworzenia zaufanych lokalizacji w programie Dostęp warunkowy, zobacz artykuł [Jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Tworzenie zasad wymagających rejestracji z zaufanej lokalizacji

Poniższe zasady mają zastosowanie do wszystkich wybranych użytkowników, którzy próbują zarejestrować się przy użyciu połączonego środowiska rejestracji i blokują dostęp, chyba że łączą się z lokalizacji oznaczonej jako zaufana sieć.

![Tworzenie zasad urzędu certyfikacji w celu kontrolowania rejestracji informacji zabezpieczających](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. W **witrynie Azure portal**przejdź do usługi **Azure Active Directory** > **Security** > **Conditional Access**
1. Wybierz pozycję **Nowe zasady**
1. W nazwie wprowadź nazwę dla tej zasady. Na przykład **rejestracja połączonych informacji zabezpieczających w zaufanych sieciach**
1. W obszarze **Przydziały**kliknij pozycję **Użytkownicy i grupy**i wybierz użytkowników i grupy, do których ma się odnosić ta zasada

   > [!WARNING]
   > Użytkownicy muszą być włączeni dla [połączonej wersji zapoznawczej rejestracji](../authentication/howto-registration-mfa-sspr-combined.md).

1. W obszarze **Aplikacje lub akcje w chmurze**wybierz pozycję **Akcje użytkownika**, zaznacz pozycję **Zarejestruj informacje o zabezpieczeniach (wersja zapoznawcza)**
1. W **warunkach** > **lokalizacje**
   1. Konfigurowanie **tak**
   1. Uwzględnij **dowolną lokalizację**
   1. Wyklucz **wszystkie zaufane lokalizacje**
   1. Kliknij **gotowe** na lemieszu Lokalizacje
   1. Kliknij **gotowe** na ostrze Warunki
1. W obszarze **Kontrola** > **Grant** dostępu
   1. Kliknij **pozycję Blokuj dostęp**
   1. Następnie kliknij przycisk **Zaznacz**
1. Ustaw **włącz zasadę** **na Włączone**
1. Następnie kliknij przycisk **Utwórz**

## <a name="next-steps"></a>Następne kroki

[Wymuszanie na użytkownikach ponownych rejestracji metod uwierzytelniania](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

[Dostępne metody uwierzytelniania wieloskładnikowego i samookapła](concept-authentication-methods.md)

[Konfigurowanie samoobsługowego resetowania hasła](howto-sspr-deployment.md)

[Konfigurowanie usługi Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Rozwiązywanie problemów z rejestracją połączonych informacji zabezpieczających](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy?](../conditional-access/location-condition.md)
