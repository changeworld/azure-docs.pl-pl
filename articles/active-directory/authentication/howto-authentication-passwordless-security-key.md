---
title: Włączenie klucza zabezpieczeń bezhasłem w usłudze Azure AD (wersja zapoznawcza) — Azure Active Directory
description: Włącz klucz zabezpieczeń bezhasłem Zaloguj się do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2 (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a063891339a46366490447b7c7a7a1a14fd81be6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828919"
---
# <a name="enable-passwordless-security-key-sign-in-for-azure-ad-preview"></a>Włączenie klucza zabezpieczeń bezhasłem w usłudze Azure AD (wersja zapoznawcza)

## <a name="requirements"></a>Wymagania

* Azure Multi-Factor Authentication
* Podgląd połączonej rejestracji z włączonymi użytkownikami dla SSPR
* Wersja zapoznawcza klucza zabezpieczeń FIDO2 wymaga zgodnych kluczy zabezpieczeń FIDO2
* Pakiet WebAuthN wymaga przeglądarki Microsoft Edge w systemie Windows 10 w wersji 1809 lub nowszej
* Logowanie systemu Windows opartego na FIDO2 wymaga usługi Azure AD przyłączonej do systemu Windows 10 w wersji 1809 lub nowszej

## <a name="prepare-devices-for-preview"></a>Przygotuj urządzenia do wersji zapoznawczej

Urządzenia, na których będziesz się pilotażować, muszą mieć uruchomiony system Windows 10 w wersji 1809 lub nowszej. Najlepszym rozwiązaniem jest system Windows 10 w wersji 1903 lub nowszej.

## <a name="enable-security-keys-for-windows-sign-in"></a>Włącz klucze zabezpieczeń logowania systemu Windows

Organizacje mogą wybrać jedną lub więcej z poniższych metod, aby umożliwić korzystanie z kluczy zabezpieczeń dla logowania systemu Windows.

### <a name="enable-credential-provider-via-intune"></a>Włączanie dostawcy poświadczeń za pośrednictwem usługi Intune

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Microsoft Intune** >  > **rejestracji** > urządzeń RejestracjaWindows > Windows**Hello dla firm**właściwości.
1. W obszarze **Ustawienia** Ustaw **Użyj kluczy zabezpieczeń do logowania** do **włączenia**.

Konfiguracja kluczy zabezpieczeń do logowania nie zależy od konfigurowania funkcji Windows Hello dla firm.

#### <a name="enable-targeted-intune-deployment"></a>Włącz planowane wdrożenie usługi Intune

Aby włączyć dostawcę poświadczeń dla określonych grup urządzeń, użyj następujących ustawień niestandardowych za pośrednictwem usługi Intune. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Microsoft Intune** >  > **profil**konfiguracji > urządzeń**Utwórz profil**.
1. Skonfiguruj nowy profil przy użyciu następujących ustawień
   1. Nazwa: Klucze zabezpieczeń logowania systemu Windows
   1. Opis: Włącza klucze zabezpieczeń FIDO, które mają być używane podczas logowania do systemu Windows
   1. Platforma: System Windows 10 i nowsze
   1. Typ platformy: Niestandardowa
   1. Niestandardowe ustawienia OMA-URI:
      1. Nazwa: Włącz klucze zabezpieczeń FIDO dla logowania do systemu Windows
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Typ danych: Liczba całkowita
      1. Wartość: 1 
1. Te zasady mogą być przypisane do określonych użytkowników, urządzeń lub grup. Więcej informacji można znaleźć w artykule [przypisywanie profilów użytkowników i urządzeń w Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Tworzenie zasad niestandardowej konfiguracji urządzeń w usłudze Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Włącz dostawcę poświadczeń za pomocą pakietu aprowizacji

W przypadku urządzeń, które nie są zarządzane przez usługę Intune, można zainstalować pakiet aprowizacji, aby włączyć tę funkcję. Aplikację Windows Configuration Designer można zainstalować z poziomu [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Uruchom projektanta konfiguracji systemu Windows.
1. Wybierz pozycję **plik** > **Nowy projekt**.
1. Nadaj projektowi nazwę i Zanotuj ścieżkę, w której został utworzony projekt.
1. Wybierz opcję **Dalej**.
1. Pozostaw wybrany **pakiet aprowizacji** jako **przepływ pracy wybranego projektu** i wybierz pozycję **dalej**.
1. Wybierz **wszystkie wersje pulpitu systemu Windows** w obszarze **Wybierz ustawienia do wyświetlenia i skonfigurowania** , a następnie wybierz pozycję **dalej**.
1. Wybierz pozycję **Finish** (Zakończ).
1. W nowo utworzonym projekcie przejdź do >  **ustawień środowiska uruchomieniowego** **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Ustaw **UseSecurityKeyForSignIn** na **włączone**.
1. Wybierz**pakiet aprowizacji** **eksportu** > 
1. Pozostaw wartości domyślne w oknie **kompilacja** w obszarze **opisz pakiet aprowizacji** i wybierz pozycję **dalej**.
1. Pozostaw wartości domyślne w oknie **kompilacja** w obszarze **Wybierz szczegóły zabezpieczeń dla pakietu aprowizacji** i wybierz pozycję **dalej**.
1. Zanotuj lub zmień ścieżkę w oknach **kompilacji** w obszarze **Wybierz lokalizację, w której ma zostać zapisany pakiet aprowizacji** , a następnie wybierz pozycję **dalej**.
1. Wybierz pozycję **kompilacja** na stronie **Kompiluj pakiet aprowizacji** .
1. Zapisz dwa pliki utworzone (ppkg i Cat) w lokalizacji, w której można je później zastosować do maszyn.
1. Postępuj zgodnie ze wskazówkami zawartymi w artykule [Zastosuj pakiet aprowizacji](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package), aby zastosować utworzony pakiet aprowizacji.

## <a name="obtain-fido2-security-keys"></a>Uzyskaj klucze zabezpieczeń FIDO2

Zapoznaj się z sekcją FIDO2 Security Keys w artykule [co to jest hasło?](concept-authentication-passwordless.md) , aby uzyskać więcej informacji na temat obsługiwanych kluczy i producentów.

> [!NOTE]
> Jeśli kupisz i planujesz korzystanie z kluczy zabezpieczeń opartych na NFC, musisz mieć obsługiwanego czytnika NFC.

## <a name="enable-passwordless-authentication-method"></a>Włącz metodę uwierzytelniania bezhasła

### <a name="enable-the-combined-registration-experience"></a>Włącz połączone środowisko rejestracji

Funkcje rejestracji dla metod uwierzytelniania bezhaseł polegają na połączonej wersji zapoznawczej rejestracji. Wykonaj kroki opisane w artykule [Włączanie rejestracji informacji o zabezpieczeniach połączonych (wersja zapoznawcza)](howto-registration-mfa-sspr-combined.md), aby włączyć podgląd rejestracji połączonej.

### <a name="enable-new-passwordless-authentication-method"></a>Włącz nową metodę uwierzytelniania bezhasło

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do **Azure Active Directory** >  > metoduwierzytelniania > zabezpieczeń**Metoda uwierzytelniania (wersja zapoznawcza)**
1. W każdej **metodzie**wybierz poniższe opcje.
   1. **Włącz** — tak lub nie
   1. **Cel** — wszyscy użytkownicy lub wybrani użytkownicy
1. **Zapisz** każdą metodę

> [!WARNING]
> Zasady ograniczeń kluczy FIDO2 nie działają jeszcze. Ta funkcja będzie dostępna przed ogólną dostępnością, nie należy zmieniać domyślnych zasad.

> [!NOTE]
> Nie musisz korzystać z obu metod bez hasła (Jeśli chcesz przejrzeć tylko jedną metodę bez hasła, możesz włączyć tylko tę metodę). Zachęcamy do wypróbowania obu metod, ponieważ oba mają swoje korzyści.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Rejestracja użytkownika i zarządzanie kluczami zabezpieczeń FIDO2

1. Przejdź na stronę [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Zaloguj się, jeśli jeszcze nie
1. Kliknij pozycję **informacje zabezpieczające**
   1. Jeśli użytkownik ma już zarejestrowana co najmniej jedną metodę uwierzytelniania wieloskładnikowego platformy Azure, może natychmiast zarejestrować klucz zabezpieczeń FIDO2.
   1. Jeśli nie zarejestrowano co najmniej jednej metody usługi Azure MFA, należy dodać ją.
1. Dodaj klucz zabezpieczeń FIDO2, klikając pozycję **Dodaj metodę** i wybierając pozycję **klucz zabezpieczeń** .
1. Wybierz **urządzenie USB** lub **urządzenie NFC**
1. Przygotuj swój klucz, a następnie wybierz pozycję **dalej** .
1. Zostanie wyświetlone okno z poproszeniem o utworzenie/wprowadzenie numeru PIN dla klucza zabezpieczeń, a następnie przeprowadzenie wymaganego gestu dla klucza.
1. Nastąpi powrót do połączonego środowiska rejestracji i poproszenie o podanie zrozumiałej nazwy dla tokenu, aby można było określić, która z nich ma być wiele. Kliknij przycisk **Dalej**.
1. Kliknij pozycję **gotowe** , aby zakończyć proces

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Zarządzaj kluczem zabezpieczeń biometrycznym, numerem PIN lub resetowaniem klucza zabezpieczeń

* Windows 10 w wersji 1809
   * Wymagane jest oprogramowanie pomocnika od dostawcy klucza zabezpieczeń
* Windows 10 w wersji 1903 lub nowszej
   * Użytkownicy mogą otwierać **Ustawienia systemu Windows** na swoim**kluczu zabezpieczeń** urządzeń > **konta** > 
   * Użytkownicy mogą zmieniać swój kod PIN, aktualizować biometria lub resetować swój klucz zabezpieczeń

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Rejestracja użytkownika i zarządzanie aplikacją Microsoft Authenticator

Aby skonfigurować Microsoft Authenticator aplikacji do logowania za pomocą telefonu, postępuj zgodnie ze wskazówkami zawartymi w artykule [Logowanie do kont przy użyciu aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credential"></a>Zaloguj się przy użyciu poświadczeń bez hasła

### <a name="sign-in-at-the-lock-screen"></a>Zaloguj się na ekranie blokady

W poniższym przykładzie w przypadku użytkownika Bala Sandhu został już zainicjowany klucz zabezpieczeń FIDO2. Bala może wybrać dostawcę poświadczeń klucza zabezpieczeń z ekranu blokady systemu Windows 10 i wstawić klucz zabezpieczeń, aby zalogować się do systemu Windows.

![Logowanie przy użyciu klucza zabezpieczeń na ekranie blokady systemu Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Zaloguj się w sieci Web

W przykładzie poniżej Użytkownik zainicjowano już swój klucz zabezpieczeń FIDO2. Użytkownik może zdecydować się na zalogowanie się w sieci Web przy użyciu klucza zabezpieczeń FIDO2 w przeglądarce Microsoft Edge w systemie Windows 10 w wersji 1809 lub nowszej.

![Znak zabezpieczeń w przeglądarce Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="known-issues"></a>Znane problemy

### <a name="security-key-provisioning"></a>Inicjowanie obsługi kluczy zabezpieczeń

W publicznej wersji zapoznawczej nie jest dostępna obsługa administracyjna i dezaktywowanie kluczy zabezpieczeń przez administratora.

### <a name="hybrid-azure-ad-join"></a>Dołączenie do hybrydowej usługi Azure AD

Użytkownicy korzystający z logowania jednokrotnego WIA korzystający z poświadczeń zarządzanych, takich jak FIDO2 klucze zabezpieczeń lub logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator muszą przyłączać hybrydowo do systemu Windows 10, aby uzyskać korzyści z logowania jednokrotnego. Jednak klucze zabezpieczeń działają tylko dla Azure Active Directory przyłączonych maszyn. Zalecamy wypróbowanie tylko kluczy zabezpieczeń FIDO2 dla ekranu blokady systemu Windows na czystej Azure Active Directory przyłączonych maszyn. To ograniczenie nie ma zastosowania do sieci Web.

### <a name="upn-changes"></a>Zmiany nazw UPN

Pracujemy nad obsługą funkcji, która umożliwia zmianę nazwy UPN na urządzeniach hybrydowych AADJ i AADJ. W przypadku zmiany nazwy UPN użytkownika nie można już modyfikować kluczy zabezpieczeń FIDO2 dla tego konta. Dlatego jedyną metodą jest zresetowanie urządzenia i konieczność jego ponownego zarejestrowania.

## <a name="next-steps"></a>Następne kroki

[Informacje o rejestracji urządzeń](../devices/overview.md)

[Dowiedz się więcej o usłudze Azure wieloskładnikowe Authentication](../authentication/howto-mfa-getstarted.md)
