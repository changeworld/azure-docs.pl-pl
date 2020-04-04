---
title: Logowanie się bez hasła do systemu Windows — usługa Azure Active Directory
description: Dowiedz się, jak włączyć bezosztywne logowanie się klucza zabezpieczeń do usługi Azure Active Directory przy użyciu kluczy zabezpieczeń FIDO2 (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b8f5d6aaa96c24eb37eb78d237a489f1d25293c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653983"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Włącz bezoszowe logowanie się klucza zabezpieczeń do urządzeń z systemem Windows 10 za pomocą usługi Azure Active Directory (wersja zapoznawcza)

Ten dokument koncentruje się na włączaniu uwierzytelniania bez hasła opartego na kluczu zabezpieczeń FIDO2 na urządzeniach z systemem Windows 10. Na końcu tego artykułu będzie można zalogować się do usługi Azure AD i hybrydowej usługi Azure AD przyłączone do systemu Windows 10 z konta usługi Azure AD przy użyciu klucza zabezpieczeń FIDO2.

|     |
| --- |
| Klucze zabezpieczeń FIDO2 są publiczną funkcją podglądu usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Wymagania

| Typ urządzenia | Dołączone do usługi Azure AD | hybrydowym dołączonym do usługi Azure AD. |
| --- | --- | --- |
| [Uwierzytelnianie wieloskładnikowe platformy Azure](howto-mfa-getstarted.md) | X | X |
| [Połączona wersja zapoznawcza rejestracji informacji o zabezpieczeniach](concept-registration-mfa-sspr-combined.md) | X | X |
| Kompatybilne [klucze zabezpieczeń FIDO2](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| Funkcja WebAuthN wymaga systemu Windows 10 w wersji 1809 lub nowszej | X | X |
| [Urządzenia przyłączone do usługi Azure AD](../devices/concept-azure-ad-join.md) wymagają systemu Windows 10 w wersji 1903 lub nowszej | X |   |
| [Urządzenia połączone z usługą Azure AD](../devices/concept-azure-ad-join-hybrid.md) wymagają kompilacji niejawnego systemu Windows 10 18945 lub nowszego |   | X |
| W pełni poprawione kontrolery domeny systemu Windows Server 2016/2019. |   | X |
| [Usługa Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) w wersji 1.4.32.0 lub nowszej |   | X |
| [Usługa Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (opcjonalnie) | X | X |
| Pakiet inicjowania obsługi administracyjnej (opcjonalnie) | X | X |
| Zasady grupy (opcjonalnie) |   | X |

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Następujące scenariusze nie są obsługiwane:

- Wdrożenie przyłączonych do domeny Usług domenowych Active Directory (AD DS) systemu Windows Server (tylko urządzenia lokalne).
- Scenariusze RDP, VDI i Citrix przy użyciu klucza zabezpieczeń.
- S/MIME przy użyciu klucza zabezpieczeń.
- "Uruchom jako" przy użyciu klucza zabezpieczeń.
- Zaloguj się do serwera przy użyciu klucza zabezpieczeń.
- Jeśli nie używasz klucza zabezpieczeń do logowania się na urządzeniu w trybie online, nie możesz go używać do logowania się lub odblokowywania w trybie offline.
- Logowanie lub odblokowywanie urządzenia z systemem Windows 10 za pomocą klucza zabezpieczeń zawierającego wiele kont usługi Azure AD. W tym scenariuszu wykorzystuje ostatnie konto dodane do klucza zabezpieczeń. WebAuthN pozwala użytkownikom wybrać konto, którego chcą używać.
- Odblokuj urządzenie z systemem Windows 10 w wersji 1809. Aby uzyskać najlepsze wrażenia, użyj systemu Windows 10 w wersji 1903 lub nowszej.

## <a name="prepare-devices-for-preview"></a>Przygotowywanie urządzeń do podglądu

Urządzenia przyłączone do usługi Azure AD, które są pilotażowe podczas podglądu funkcji z musi uruchomić system Windows 10 w wersji 1809 lub nowszej. Najlepsze środowisko jest w systemie Windows 10 w wersji 1903 lub nowszej.

Hybrydowe urządzenia przyłączone do usługi Azure AD muszą działać w kompilacji niejawnego programu testów systemu Windows 10 18945 lub nowszej.

## <a name="enable-security-keys-for-windows-sign-in"></a>Włączanie kluczy zabezpieczeń dla logowania się do systemu Windows

Organizacje mogą użyć co najmniej jednej z następujących metod, aby umożliwić korzystanie z kluczy zabezpieczeń dla logowania systemu Windows na podstawie wymagań organizacji:

- [Włącz za pomocą usługi Intune](#enable-with-intune)
- [Ukierunkowane wdrożenie usługi Intune](#targeted-intune-deployment)
- [Włącz za pomocą pakietu inicjowania obsługi administracyjnej](#enable-with-a-provisioning-package)
- [Włącz za pomocą zasad grupy (tylko urządzenia przyłączone do usługi Azure Azure)](#enable-with-group-policy)

> [!IMPORTANT]
> Organizacje z **hybrydowymi urządzeniami przyłączającymi do usługi Azure AD** muszą **również** wykonać kroki opisane w artykule [Włącz uwierzytelnianie FIDO2 do zasobów lokalnych,](howto-authentication-passwordless-security-key-on-premises.md) zanim zacznie działać uwierzytelnianie klucza zabezpieczeń FIDO2 systemu Windows 10.
>
> Organizacje z **urządzeniami przyłączającymi do usługi Azure AD** muszą to zrobić, zanim ich urządzenia będą mogły uwierzytelniać się w zasobach lokalnych przy użyciu kluczy zabezpieczeń FIDO2.

### <a name="enable-with-intune"></a>Włącz za pomocą usługi Intune

Aby włączyć korzystanie z kluczy zabezpieczeń przy użyciu usługi Intune, wykonaj następujące czynności:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do rejestracji**urządzenia** >  **usługi Microsoft Intune Rejestracja** > **systemu Windows** > **Hello dla właściwości biznesowych** > **Properties**.
1. W obszarze **Ustawienia**ustaw **pozycję Używanie kluczy zabezpieczeń do logowania** się na **Włączone**.

Konfiguracja kluczy zabezpieczeń logowania nie zależy od konfigurowania funkcji Windows Hello dla firm.

### <a name="targeted-intune-deployment"></a>Ukierunkowane wdrożenie usługi Intune

Aby kierować reklamy na określone grupy urządzeń, aby włączyć dostawcę poświadczeń, użyj następujących ustawień niestandardowych za pośrednictwem usługi Intune:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do**profilów** > **konfiguracji** > urządzenia **usługi Microsoft Intune** > **Utwórz profil**.
1. Skonfiguruj nowy profil z następującymi ustawieniami:
   - Nazwa: Klucze zabezpieczeń logowania do systemu Windows
   - Opis: umożliwia korzystanie z kluczy zabezpieczeń FIDO podczas logowania do systemu Windows
   - Platforma: Windows 10 i nowsze
   - Typ profilu: Niestandardowy
   - Niestandardowe ustawienia identyfikatora URI OMA:
      - Nazwa: Włączanie kluczy zabezpieczeń FIDO dla logowania się do systemu Windows
      - OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Typ danych: Całkowita
      - Wartość: 1
1. Te zasady można przypisać do określonych użytkowników, urządzeń lub grup. Aby uzyskać więcej informacji, zobacz [Przypisywanie profilów użytkowników i urządzeń w usłudze Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Tworzenie zasad konfiguracji niestandardowej usługi Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Włącz za pomocą pakietu inicjowania obsługi administracyjnej

W przypadku urządzeń, które nie są zarządzane przez usługę Intune, można zainstalować pakiet inicjowania obsługi administracyjnej, aby włączyć te funkcje. Aplikację Projektant konfiguracji systemu Windows można zainstalować ze [sklepu Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22). Wykonaj następujące kroki, aby utworzyć pakiet inicjowania obsługi administracyjnej:

1. Uruchom Projektanta konfiguracji systemu Windows.
1. Wybierz **pozycję Plik** > **nowy projekt**.
1. Nadaj projektowi nazwę i zanotuj ścieżkę, w której tworzony jest projekt, a następnie wybierz pozycję **Dalej**.
1. Pozostaw *pakiet inicjowania obsługi administracyjnej* wybrany jako **wybrany przepływ pracy projektu** i wybierz przycisk **Dalej**.
1. Wybierz *pozycję Wszystkie wersje pulpitu systemu Windows* w obszarze Wybierz **ustawienia, które mają być wyświetlane i konfigurowane,** a następnie wybierz pozycję **Dalej**.
1. Wybierz **pozycję Zakończ**.
1. W nowo utworzonym projekcie przejdź do ustawień >  **środowiska uruchomieniowego****WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Ustaw **funkcję UseSecurityKeyForSignIn** *na Włączoną*.
1. Wybierz pakiet **eksportu** > **aprowizacji**
1. Pozostaw wartości domyślne w oknie **Kompilacja** w obszarze **Opis pakietu inicjowania obsługi administracyjnej**, a następnie wybierz przycisk **Dalej**.
1. Pozostaw wartości domyślne w oknie **Kompilacja** w obszarze **Wybierz szczegóły zabezpieczeń dla pakietu inicjowania obsługi administracyjnej** i wybierz pozycję **Dalej**.
1. Zanotuj lub zmień ścieżkę w oknach **kompilacji** w obszarze **Wybierz miejsce zapisania pakietu inicjowania obsługi administracyjnej** i wybierz pozycję **Dalej**.
1. Wybierz **pozycję Kompilacja** na stronie **pakietu inicjowania obsługi administracyjnej.**
1. Zapisz dwa utworzone pliki *(ppkg* i *cat)* w miejscu, w którym możesz zastosować je później na maszynach.
1. Aby zastosować utworzony pakiet inicjowania obsługi [administracyjnej, zobacz Stosowanie pakietu inicjowania obsługi administracyjnej](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Urządzenia z systemem Windows 10 w wersji 1809 muszą również włączyć tryb współdzielonego komputera (*EnableSharedPCMode*). Aby uzyskać więcej informacji na temat włączania tej funkcji, zobacz [Konfigurowanie udostępnionego lub gościnnego komputera w systemie Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Włącz za pomocą zasad grupy

W przypadku **hybrydowych urządzeń przyłączonych do usługi Azure AD**organizacje mogą skonfigurować następujące ustawienie zasad grupy, aby włączyć logowanie klucza zabezpieczeń FIDO. To ustawienie można znaleźć w obszarze **Konfiguracja konfiguracji** > komputera**Szablony administracyjne** > **Logowanie** > **systemowe** > **Włącz logowanie klucza zabezpieczeń:**

- Ustawienie tej zasady na **Włączone** umożliwia użytkownikom logowanie się przy za pomocą kluczy zabezpieczeń.
- Ustawienie tej zasady **na Wyłączone** lub **Nieskonfigurowane** uniemożliwia użytkownikom logowanie się przy za pomocą kluczy zabezpieczeń.

To ustawienie zasad grupy wymaga `credentialprovider.admx` zaktualizowanej wersji szablonu zasad grupy. Ten nowy szablon jest dostępny z następną wersją systemu Windows Server i windows 10 20H1. To ustawienie można zarządzać za pomocą urządzenia z jedną z tych nowszych wersji systemu Windows lub centralnie, postępujących zgodnie ze wskazówkami zawartymi w temacie pomocy technicznej [Jak utworzyć szablony administracyjne zasad grupy i zarządzać nim w systemie Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Logowanie się za pomocą klucza zabezpieczeń FIDO2

W poniższym przykładzie użytkownik o nazwie Bala Sandhu już aprowizował swój klucz zabezpieczeń FIDO2, wykonując czynności opisane w poprzednim artykule [Włącz logowanie się bez hasła](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). W przypadku hybrydowych urządzeń przyłączonych do usługi Azure AD upewnij się, że włączono również [logowanie bez hasła do zasobów lokalnych.](howto-authentication-passwordless-security-key-on-premises.md) Bala może wybrać dostawcę poświadczeń klucza zabezpieczeń z ekranu blokady systemu Windows 10 i wstawić klucz zabezpieczeń, aby zalogować się do systemu Windows.

![Logowanie klucza zabezpieczeń na ekranie blokady systemu Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Zarządzanie kluczem zabezpieczeń biometrycznym, PIN lub resetowaniem klucza zabezpieczeń

* Windows 10 w wersji 1903 lub nowszej
   * Użytkownicy mogą otwierać **ustawienia systemu Windows** na urządzeniu > klucz**zabezpieczeń** **konta** > 
   * Użytkownicy mogą zmieniać kod PIN, aktualizować dane biometryczne lub resetować klucz zabezpieczeń

## <a name="troubleshooting-and-feedback"></a>Rozwiązywanie problemów i opinie

Jeśli chcesz udostępnić opinię lub napotkasz problemy podczas wyświetlania podglądu tej funkcji, udostępnij za pośrednictwem aplikacji Centrum opinii systemu Windows, wykonując następujące czynności:

1. Uruchom **Centrum opinii** i upewnij się, że się zalogowałeś.
1. Prześlij opinię w następującej kategorii:
   - Kategoria: Bezpieczeństwo i prywatność
   - Podkategoria: FIDO
1. Aby przechwycić dzienniki, użyj **opcji,** aby ponownie utworzyć problem

## <a name="next-steps"></a>Następne kroki

[Włączanie dostępu do zasobów lokalnych dla usługi Azure AD i urządzeń przyłączonych do usługi Azure AD](howto-authentication-passwordless-security-key-on-premises.md)

[Dowiedz się więcej o rejestracji urządzenia](../devices/overview.md)

[Dowiedz się więcej o uwierzytelnianiu wieloskładnikowym platformy Azure](../authentication/howto-mfa-getstarted.md)
