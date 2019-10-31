---
title: Włącz logowanie przy użyciu klucza zabezpieczeń bezhasło dla usługi Azure AD (wersja zapoznawcza) — Azure Active Directory
description: Włącz logowanie za pomocą klucza zabezpieczeń bezhasła do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2 (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5758b1fbb9d311219e3dc4dd483691f6c9d80c1
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172171"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Włącz klucz zabezpieczeń bezhasłem Zaloguj się na urządzeniach z systemem Windows 10 (wersja zapoznawcza)

Ten dokument koncentruje się na włączaniu uwierzytelniania bezhaseł opartego na kluczu zabezpieczeń FIDO2 na urządzeniach z systemem Windows 10. Na końcu tego artykułu będzie można zalogować się do aplikacji sieci Web i urządzeń z systemem Windows 10 dołączonych do usługi Azure AD za pomocą konta usługi Azure AD przy użyciu klucza zabezpieczeń FIDO2.

|     |
| --- |
| Klucze zabezpieczeń FIDO2 są publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure|
|     |

## <a name="requirements"></a>Wymagania

| Typ urządzenia | Przyłączone do usługi Azure AD | Dołączona hybrydowa usługa Azure AD |
| --- | --- | --- |
| [Multi-Factor Authentication platformy Azure](howto-mfa-getstarted.md) | X | X |
| [Wersja zapoznawcza rejestracji informacji o zabezpieczeniach](concept-registration-mfa-sspr-combined.md) | X | X |
| Zgodne [FIDO2 klucze zabezpieczeń](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN wymaga systemu Windows 10 w wersji 1809 lub nowszej | X | X |
| [Urządzenia przyłączone do usługi Azure AD](../devices/concept-azure-ad-join.md) wymagają systemu Windows 10 w wersji 1809 lub nowszej | X |   |
| [Hybrydowe urządzenia dołączone do usługi Azure AD](../devices/concept-azure-ad-join-hybrid.md) wymagają kompilacji niejawnego programu testów systemu Windows 10 18945 lub nowszej |   | X |
| W pełni poprawione kontrolery domeny systemu Windows Server 2016/2019. |   | X |
| Uaktualnij do najnowszej wersji [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (opcjonalnie) | X | X |
| Pakiet aprowizacji (opcjonalnie) | X | X |
| Zasady grupy (opcjonalnie) |   | X |

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

- Systemy Windows Server Active Directory Domain Services (AD DS) przyłączone do domeny (urządzenia lokalne) nie są **obsługiwane**.
- Scenariusze dotyczące protokołu RDP, infrastruktury VDI i Citrix **nie są obsługiwane** przy użyciu klucza zabezpieczeń.
- Użycie protokołu S/MIME **nie jest obsługiwane** przy użyciu klucza zabezpieczeń.
- Użycie opcji "Uruchom jako" **nie jest obsługiwane** przy użyciu klucza zabezpieczeń.
- Logowanie się do serwera przy użyciu klucza zabezpieczeń **nie jest obsługiwane**.
- Jeśli klucz zabezpieczeń nie został użyty do zalogowania się na urządzeniu w trybie online, nie będzie można go użyć do zalogowania się lub odblokowania w trybie offline.

## <a name="prepare-devices-for-preview"></a>Przygotuj urządzenia do wersji zapoznawczej

Urządzenia przyłączone do usługi Azure AD, które będą używane do pilotażu, muszą mieć uruchomiony system Windows 10 w wersji 1809 lub nowszej. Najlepszym rozwiązaniem jest system Windows 10 w wersji 1903 lub nowszej.

Hybrydowe urządzenia dołączone do usługi Azure AD, z którymi będziesz się testować, muszą mieć uruchomiony system Windows 10 w wersji 18945 lub nowszej.

## <a name="enable-security-keys-for-windows-sign-in"></a>Włącz klucze zabezpieczeń logowania systemu Windows

Organizacje mogą wybrać jedną lub więcej z poniższych metod, aby umożliwić korzystanie z kluczy zabezpieczeń logowania systemu Windows zgodnie z wymaganiami organizacji.

- [Włącz w usłudze Intune](#enable-with-intune)
   - [Planowane wdrożenie usługi Intune](#targeted-intune-deployment)
- [Włącz z pakietem aprowizacji](#enable-with-a-provisioning-package)
- [Włącz z zasady grupy (tylko urządzenia dołączone do hybrydowej usługi Azure AD)](#enable-with-group-policy)

> [!IMPORTANT]
> Organizacje z **urządzeniami dołączonymi do hybrydowej usługi Azure AD** muszą **również** wykonać kroki opisane w tym artykule, [Aby włączyć uwierzytelnianie FIDO2 w zasobach lokalnych](howto-authentication-passwordless-security-key-on-premises.md) przed rozpoczęciem uwierzytelniania klucza zabezpieczeń w systemie Windows 10 FIDO2.
>
> Organizacje z **urządzeniami przyłączonymi do usługi Azure AD** muszą to zrobić, zanim będą mogły być uwierzytelniane w zasobach lokalnych przy użyciu kluczy zabezpieczeń FIDO2.

### <a name="enable-with-intune"></a>Włącz w usłudze Intune

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Przejdź do **Microsoft Intune** > **rejestracji urządzeń** > **Rejestracja systemu Windows** > **Właściwości**usługi **Windows Hello dla firm** > .
1. W obszarze **Ustawienia** Ustaw **Użyj kluczy zabezpieczeń do logowania** do **włączenia**.

Konfiguracja kluczy zabezpieczeń dla logowania nie zależy od konfigurowania funkcji Windows Hello dla firm.

#### <a name="targeted-intune-deployment"></a>Planowane wdrożenie usługi Intune

Aby włączyć dostawcę poświadczeń dla określonych grup urządzeń, użyj następujących ustawień niestandardowych za pośrednictwem usługi Intune.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Przejdź do **Microsoft Intune** > **Konfiguracja urządzenia** > **Profile** > **Utwórz profil**.
1. Skonfiguruj nowy profil przy użyciu następujących ustawień
   1. Name: klucze zabezpieczeń logowania systemu Windows
   1. Opis: włącza klucze zabezpieczeń FIDO, które mają być używane podczas logowania do systemu Windows
   1. Platforma: system Windows 10 i nowsze
   1. Typ profilu: niestandardowy
   1. Niestandardowe ustawienia OMA-URI:
      1. Nazwa: Włącz klucze zabezpieczeń FIDO dla logowania do systemu Windows
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Typ danych: liczba całkowita
      1. Wartość: 1
1. Te zasady mogą być przypisane do określonych użytkowników, urządzeń lub grup. Więcej informacji można znaleźć w artykule [przypisywanie profilów użytkowników i urządzeń w Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Tworzenie zasad niestandardowej konfiguracji urządzeń w usłudze Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Włącz z pakietem aprowizacji

W przypadku urządzeń, które nie są zarządzane przez usługę Intune, można zainstalować pakiet aprowizacji, aby włączyć tę funkcję. Aplikację Windows Configuration Designer można zainstalować z poziomu [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Uruchom projektanta konfiguracji systemu Windows.
1. Wybierz pozycję **plik** > **Nowy projekt**.
1. Nadaj projektowi nazwę i Zanotuj ścieżkę, w której został utworzony projekt.
1. Wybierz opcję **Dalej**.
1. Pozostaw wybrany **pakiet aprowizacji** jako **przepływ pracy wybranego projektu** i wybierz pozycję **dalej**.
1. Wybierz **wszystkie wersje pulpitu systemu Windows** w obszarze **Wybierz ustawienia do wyświetlenia i skonfigurowania** , a następnie wybierz pozycję **dalej**.
1. Wybierz pozycję **Finish** (Zakończ).
1. W nowo utworzonym projekcie przejdź do **ustawień środowiska uruchomieniowego** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Ustaw **UseSecurityKeyForSignIn** na **włączone**.
1. Wybierz pozycję **eksportuj** > **pakiet aprowizacji**
1. Pozostaw wartości domyślne w oknie **kompilacja** w obszarze **opisz pakiet aprowizacji** i wybierz pozycję **dalej**.
1. Pozostaw wartości domyślne w oknie **kompilacja** w obszarze **Wybierz szczegóły zabezpieczeń dla pakietu aprowizacji** i wybierz pozycję **dalej**.
1. Zanotuj lub zmień ścieżkę w oknach **kompilacji** w obszarze **Wybierz lokalizację, w której ma zostać zapisany pakiet aprowizacji** , a następnie wybierz pozycję **dalej**.
1. Wybierz pozycję **kompilacja** na stronie **Kompiluj pakiet aprowizacji** .
1. Zapisz dwa pliki utworzone (ppkg i Cat) w lokalizacji, w której można je później zastosować do maszyn.
1. Postępuj zgodnie ze wskazówkami zawartymi w artykule [Zastosuj pakiet aprowizacji](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package), aby zastosować utworzony pakiet aprowizacji.

> [!NOTE]
> W przypadku urządzeń z systemem Windows 10 w wersji 1809 należy również włączyć tryb Shared PC (EnableSharedPCMode). Informacje na temat włączania tej funkcjonalność można znaleźć w artykule, [skonfigurować komputer współużytkowany lub Gościa z systemem Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Włącz z zasady grupy

W przypadku **hybrydowych urządzeń przyłączonych do usługi Azure AD** można skonfigurować następujące ustawienie zasady grupy, aby włączyć logowanie przy użyciu klucza zabezpieczeń Fido.

Ustawienie to można znaleźć w obszarze **Konfiguracja komputera** > **Szablony administracyjne** > **system** > **Logon** > **włączyć logowanie przy użyciu klucza zabezpieczeń**.

- Ustawienie tych zasad na **włączone** umożliwi użytkownikom logowanie się przy użyciu kluczy zabezpieczeń.
- Ustawienie tych zasad na **wyłączone** lub **Nieskonfigurowane** uniemożliwi użytkownikom logowanie się przy użyciu kluczy zabezpieczeń.

To ustawienie zasady grupy wymaga zaktualizowanej wersji szablonu `credentialprovider.admx` zasady grupy. Ten nowy szablon jest dostępny w następnej wersji systemu Windows Server i z systemem Windows 10 20H1. To ustawienie może być zarządzane przy użyciu urządzenia z jedną z tych nowszych wersji systemu Windows lub centralnie, postępując zgodnie ze wskazówkami w temacie dotyczącym pomocy technicznej, [jak utworzyć magazyn centralny dla zasady grupy Szablony administracyjne w systemie Windows i zarządzać](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)nim.

## <a name="sign-in-with-fido2-security-key"></a>Zaloguj się przy użyciu klucza zabezpieczeń FIDO2

W poniższym przykładzie w przypadku użytkownika Bala Sandhu został już zainicjowany klucz zabezpieczeń FIDO2 za pomocą kroków opisanych w poprzednim artykule, [Włącz opcję Zaloguj klucz zabezpieczeń](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Bala może wybrać dostawcę poświadczeń klucza zabezpieczeń z ekranu blokady systemu Windows 10 i wstawić klucz zabezpieczeń, aby zalogować się do systemu Windows.

![Logowanie przy użyciu klucza zabezpieczeń na ekranie blokady systemu Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Zarządzaj kluczem zabezpieczeń biometrycznym, numerem PIN lub resetowaniem klucza zabezpieczeń

* Windows 10 w wersji 1903 lub nowszej
   * Użytkownicy mogą otwierać **Ustawienia systemu Windows** na swoich urządzeniach > **konta** > **klucz zabezpieczeń**
   * Użytkownicy mogą zmieniać swój kod PIN, aktualizować biometria lub resetować swój klucz zabezpieczeń

## <a name="troubleshooting-and-feedback"></a>Rozwiązywanie problemów i opinie

Jeśli chcesz udostępnić opinię lub napotkać problemy podczas korzystania z tej funkcji, Udostępnij aplikację centrum opinii o systemie Windows.

1. Uruchom **centrum opinii** i upewnij się, że użytkownik jest zalogowany.
1. Prześlij opinię poniżej następującej kategoryzacji:
   1. Kategoria: zabezpieczenia i prywatność
   1. Podkategoria: FIDO
1. Aby przechwytywać dzienniki, użyj opcji: **Utwórz ponownie mój problem**

## <a name="next-steps"></a>Następne kroki

[Zapewnianie dostępu do zasobów lokalnych w usłudze Azure AD i hybrydowych urządzeniach z usługą Azure AD](howto-authentication-passwordless-security-key-on-premises.md)

[Dowiedz się więcej o rejestrowaniu urządzeń](../devices/overview.md)

[Dowiedz się więcej o usłudze Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
