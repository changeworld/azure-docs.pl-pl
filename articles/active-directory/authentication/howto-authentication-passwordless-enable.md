---
title: Konfigurowanie logowania bez hasła usługi Azure Active Directory (wersja zapoznawcza)
description: Logowanie bez hasła w usłudze Azure AD przy użyciu kluczy zabezpieczeń FIDO2 lub aplikacji Microsoft Authenticator (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ba2545467ebfbd032408aeee25b82b92a628f2a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712074"
---
# <a name="enable-passwordless-sign-in-for-azure-ad-preview"></a>Włącz logowanie bez hasła w usłudze Azure AD (wersja zapoznawcza)

## <a name="requirements"></a>Wymagania

* Azure Multi-Factor Authentication
* Połączone rejestracji (wersja zapoznawcza)
* FIDO2 zabezpieczeń klucza (wersja zapoznawcza) wymaga zgodne kluczy zabezpieczeń FIDO2
* WebAuthN wymaga programu Microsoft Edge w systemie Windows 10 w wersji 1809 lub wyższej
* Oparte na Windows logowanie wymaga usługi Azure AD FIDO2 przyłączone do systemu Windows 10 w wersji 1809 lub wyższej

## <a name="prepare-devices-for-preview"></a>Przygotowywanie urządzeń (wersja zapoznawcza)

Urządzenia, które można będzie mieć pilotażowe wdrożenie musi działać system Windows 10 w wersji 1809 lub nowszej. Najlepsze środowisko jest w systemie Windows 10 w wersji 1903 lub nowszej.

## <a name="enable-security-keys-for-windows-sign-in"></a>Zaloguj się do niego Włącz kluczy zabezpieczeń dla Windows

Organizacje mogą określić użyć jednego lub więcej z poniższych metod, aby umożliwić korzystanie z kluczy zabezpieczeń dla Windows Zaloguj się w.

### <a name="enable-credential-provider-via-intune"></a>Włącz dostawcę poświadczeń za pomocą usługi Intune

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Microsoft Intune** > **rejestracji urządzeń** > **rejestracji Windows** > **Windows Hello dla firm** > **właściwości**.
1. W obszarze **ustawienia** ustaw **używać kluczy zabezpieczeń na potrzeby logowania w** do **włączone**.

Konfiguracja kluczy zabezpieczeń podczas logowania, nie jest zależny od konfigurowania Windows Hello dla firm.

#### <a name="enable-targeted-intune-deployment"></a>Włącz docelowych wdrożenia usługi Intune

Aby skierować je do grup urządzeń w celu włączenia dostawcy poświadczeń, użyj następujących ustawień niestandardowych za pomocą usługi Intune. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Microsoft Intune** > **konfiguracji urządzenia** > **profile** > **Utwórz profil**.
1. Skonfiguruj nowy profil z następującymi ustawieniami
   1. Nazwa: Klucze zabezpieczeń podczas logowania Windows
   1. Opis: Włącza FIDO kluczy zabezpieczeń, które będą używane w Windows Sign In
   1. Platforma: Windows 10 i nowsze
   1. Typ platformy: Niestandardowy
   1. Ustawienia niestandardowe OMA-URI:
      1. Nazwa: Włącz kluczy zabezpieczeń FIDO dla Windows Sign In
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Typ danych: Liczba całkowita
      1. Wartość: 1 
1. Te zasady można przypisać do określonych użytkowników, urządzeń lub grup. Więcej informacji można znaleźć w artykule [Przypisywanie profili użytkowników i urządzeń w programie Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Tworzenie zasad konfiguracji urządzeń niestandardowych usługi Intune](./media/howto-authentication-passwordless-enable/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Włącz dostawcę poświadczeń za pośrednictwem pakietu aprowizacyjnego

W przypadku urządzeń niezarządzanych przez usługę Intune można zainstalować pakietu aprowizacyjnego do włączenia funkcji. Można zainstalować aplikacji Windows Configuration Designer ze [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Uruchom program Windows Configuration Designer.
1. Wybierz **pliku** > **nowy projekt**.
1. Nadaj projektowi nazwę i zwróć uwagę na ścieżkę, w którym projekt zostanie utworzona.
1. Wybierz opcję **Dalej**.
1. Pozostaw **pakiet aprowizacji** wybrany jako **przepływu pracy projektu wybrane** i wybierz **dalej**.
1. Wybierz **wersje pulpitu Windows wszystkich** w obszarze **wybierz ustawienia, które można wyświetlić i skonfigurować** i wybierz **dalej**.
1. Wybierz pozycję **Finish** (Zakończ).
1. W nowo utworzonego projektu, przejdź do **ustawień środowiska uruchomieniowego** > **WindowsHelloForBusiness** > **SecurityKeys**  >  **UseSecurityKeyForSignIn**.
1. Ustaw **UseSecurityKeyForSignIn** do **włączone**.
1. Wybierz **wyeksportować** > **pakietów aprowizacji**
1. Pozostaw wartości domyślne w **kompilacji** okna w obszarze **opisują pakiet aprowizacyjny** i wybierz **dalej**.
1. Pozostaw wartości domyślne w **kompilacji** okna w obszarze **Wybierz szczegóły zabezpieczeń dotyczące pakietu aprowizacyjnego** i wybierz **dalej**.
1. Zwróć uwagę na lub zmienić ścieżkę w **kompilacji** systemu windows w obszarze **wybierz miejsce zapisania pakietu aprowizacyjnego** i wybierz **dalej**.
1. Wybierz **kompilacji** na **Tworzenie pakietu aprowizacyjnego** strony.
1. Zapisz dwa pliki, utworzony (ppkg i cat) do lokalizacji, gdzie można je zastosować do maszyn później.
1. Postępuj zgodnie ze wskazówkami w artykule [zastosować pakiet aprowizacyjny](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package), aby zastosować pakiet aprowizacyjny został utworzony.

## <a name="obtain-fido2-security-keys"></a>Uzyskiwanie kluczy zabezpieczeń FIDO2

Zobacz sekcję klucze zabezpieczeń FIDO2, w artykule [co to jest bez hasła?](concept-authentication-passwordless.md) Aby uzyskać więcej informacji na temat obsługiwanych kluczy i producentów.

> [!NOTE]
> Jeśli zakupu i planowane jest użycie kluczy zabezpieczeń NFC na podstawie konieczne będzie obsługiwane czytnika NFC.

## <a name="enable-passwordless-authentication-methods"></a>Włącz metod uwierzytelniania bez hasła

### <a name="enable-the-combined-registration-experience"></a>Włącz środowisko rejestracji połączone

Funkcje rejestracji dla kluczy zabezpieczeń FIDO2 polegają na połączone rejestracji w wersji zapoznawczej. Wykonaj poniższe kroki, aby włączyć wersję zapoznawczą połączone rejestracji.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do **usługi Azure Active Directory** > **ustawień użytkownika**
   1. Kliknij pozycję **Zarządzanie ustawieniami funkcji w wersji zapoznawczej panelu dostępu**
   1. W obszarze **użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania nimi zabezpieczające — rozszerzone**.
      1. Wybierz **wybrane** i wybierz grupę użytkowników, którzy będą uczestniczyć w wersji zapoznawczej.
      1. Lub wybierz **wszystkich** można włączyć dla wszystkich użytkowników w katalogu.
1. Kliknij polecenie **Zapisz**.

### <a name="enable-new-passwordless-authentication-methods"></a>Włączanie nowych metod uwierzytelniania bez hasła

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do **usługi Azure Active Directory** > **metod uwierzytelniania** > **zasady metody uwierzytelniania (wersja zapoznawcza)**
1. W każdym **metoda**, wybierz następujące opcje
   1. **Włącz** — Yes lub No
   1. **Docelowy** -wszystkich użytkowników lub Wybieranie: użytkownicy
1. **Zapisz** każdej metody

> [!WARNING]
> FIDO2, "Klucz zasad ograniczeń" nie będą działać jeszcze. Ta funkcja będzie dostępna przed ogólnie dostępne, nie zmieniaj tych zasad z domyślnej.

> [!NOTE]
> Nie ma potrzeby korzystania z obu metod bez hasła (Jeśli chcesz tylko jedna metoda bez hasła w wersji zapoznawczej, można włączyć tylko tej metody). Firma Microsoft zachęca wypróbować obie metody, ponieważ mają swoje własne korzyści.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Rejestracja użytkownika i zarządzanie kluczami zabezpieczeń FIDO2

1. Przejdź na stronę [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Zaloguj się w Jeśli jeszcze nie
1. Kliknij przycisk **informacje zabezpieczające**
   1. Jeśli użytkownik ma już co najmniej jedną metodę uwierzytelniania wieloskładnikowego Azure zarejestrowany, można od razu zarejestrować FIDO2 klucza zabezpieczeń.
   1. Jeśli nie mają co najmniej jedną metodę uwierzytelniania wieloskładnikowego Azure zarejestrowany, należy dodać jeden.
1. Dodaj klucz zabezpieczeń FIDO2, klikając **Dodaj metodę** i wybierając pozycję **klucz zabezpieczeń**
1. Wybierz **urządzenia USB** lub **NFC urządzenia**
1. Mieć klucz gotowe i wybierz **dalej**
1. Pole będzie pojawiają się i poprosić o Tworzenie/podania numeru PIN dla klucza zabezpieczeń, a następnie albo wykonaj gest wymagane związane z kluczem biometryczne lub w ogóle.
1. Będziesz zwrócony do środowiska rejestracji połączone i monit o podanie nazwę opisową dla Twojego tokenu, aby można było zidentyfikować który z nich w przypadku wielu. Kliknij przycisk **Dalej**.
1. Kliknij przycisk **gotowe** do ukończenia procesu

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Zarządzanie kluczem zabezpieczeń biometrycznych, numer PIN, lub zresetuj klucz zabezpieczeń

* Windows 10 w wersji 1809
   * Pomocnik oprogramowania od dostawcy klucza zabezpieczeń jest wymagany
* Windows 10 w wersji 1903 lub nowszej
   * Użytkownicy mogą otwierać **ustawienia Windows** na swoim urządzeniu > **kont** > **klucz zabezpieczeń**
   * Użytkowników można zmienić kod PIN, zaktualizuj dane biometryczne lub zresetować swój klucz zabezpieczeń

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Rejestracja użytkownika i zarządzania aplikacji Microsoft Authenticator

Aby skonfigurować aplikację Microsoft Authenticator dla logowania przez telefon, zgodnie z wytycznymi w artykule [Zaloguj się do konta przy użyciu aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credentials"></a>Zaloguj się przy użyciu poświadczeń bez hasła

### <a name="sign-in-at-the-lock-screen"></a>Zaloguj się na ekranie blokady

W przykładzie poniżej użytkownik Bala Sandhu aprowizowano już ich FIDO2 klucza zabezpieczeń. Bala wybrać dostawcę poświadczenia klucza zabezpieczeń z ekranu blokady systemu Windows 10 i Wstaw klucz zabezpieczeń, aby zalogować się do Windows.

![Klucz zabezpieczeń Zaloguj się na ekranie blokady systemu Windows 10](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Zaloguj się w sieci web

W przykładzie poniżej użytkownik ma już zainicjowano obsługę administracyjną ich FIDO2 klucza zabezpieczeń. Użytkownik może wybrać do logowania się w sieci web przy użyciu ich klucza zabezpieczeń FIDO2 wewnątrz przeglądarki Microsoft Edge w systemie Windows 10 w wersji 1809 lub nowszej.

![Znak kluczy zabezpieczeń w programie Microsoft Edge](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-edge-sign-in.png)

Informacje o logowanie się przy użyciu aplikacji Microsoft Authenticator na ten temat można znaleźć w artykule [Zaloguj się do konta przy użyciu aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="known-issues"></a>Znane problemy

### <a name="fido2-security-keys"></a>FIDO2 kluczy zabezpieczeń

#### <a name="security-key-provisioning"></a>Zabezpieczenia klucza aprowizacji

Administrator aprowizację i anulowanie obsługi kluczy zabezpieczeń nie jest dostępna w publicznej wersji zapoznawczej.

#### <a name="hybrid-azure-ad-join"></a>Dołączenie hybrydowe do usługi Azure AD

Użytkownicy opierając się na WIA logowania jednokrotnego, które używają poświadczeń zarządzanych, takich jak FIDO2 kluczy zabezpieczeń lub bez hasła Zaloguj się przy użyciu aplikacji Microsoft Authenticator koniecznych do hybrydowego przyłączenia w systemie Windows 10 do uzyskania korzyści wynikające z rejestracji Jednokrotnej. Jednak działają tylko dla usługi Azure Active Directory przyłączone maszyn teraz kluczy zabezpieczeń. Zaleca się, że tylko wypróbować FIDO2 kluczy zabezpieczeń dla Windows ekranu blokady na czystych maszynach Azure Active Directory przyłączony. To ograniczenie nie ma zastosowania dla sieci web.

#### <a name="upn-changes"></a>Zmiany nazwy UPN

Pracujemy nad na obsłudze funkcja, która umożliwia zmiana nazwy UPN hybrydowego AADJ i AADJ urządzenia. Zmienia nazwę UPN użytkownika, nie można zmodyfikować FIDO2 kluczy zabezpieczeń do konta w tym. Dlatego jest to jedyny próbę zresetowania urządzenia, a użytkownik będzie musiał ponownie zarejestrować.

### <a name="authenticator-app"></a>Aplikacja Authenticator

#### <a name="ad-fs-integration"></a>Integracja z usługą AD FS

Po użytkownik włączył poświadczenia bez hasła Microsoft Authenticator, zawsze będzie domyślnie wysłanie powiadomienia do zatwierdzenia uwierzytelniania dla tego użytkownika. Tę logikę uniemożliwia użytkownikom w dzierżawie hybrydowego być kierowany do usług AD FS dla weryfikację logowania bez użytkownika, wykonanie dodatkowych czynności kliknij pozycję "Zamiast tego użyj hasła." Ten proces będzie również pominąć wszystkie zasady dostępu warunkowego w środowisku lokalnym i przepływów uwierzytelniania przekazywanego. Wyjątkiem od tego procesu jest, jeśli login_hint jest określony, użytkownik będzie automatycznie przekazana dalej do usług AD FS, a Pomiń opcję, aby użyć poświadczeń bez hasła.

#### <a name="azure-mfa-server"></a>Serwer usługi Azure MFA

Użytkownicy końcowi, którzy są włączone dla usługi MFA w organizacji na lokalnym serwerze Azure MFA nadal można tworzyć i używać znaku pojedynczego telefonu bez hasła w poświadczeniach. Jeśli użytkownik podejmie próbę uaktualnienia wiele instalacji (5 i nowsze) programu Microsoft Authenticator z poświadczeniami, ta zmiana może spowodować wystąpienie błędu.  

#### <a name="device-registration"></a>Rejestracja urządzenia

To jeden z warunków wstępnych, aby utworzyć to poświadczenie nowych, silnych, że urządzenia, w którym znajduje się jest zarejestrowany w ramach dzierżawy usługi Azure AD, do indywidualnego użytkownika. Ze względu na ograniczenia rejestracji urządzeń urządzenie może być rejestrowane tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe lub szkolne w aplikacji Microsoft Authenticator może zostać włączona dla logowania przez telefon.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o rejestracji urządzenia](../devices/overview.md)

[Więcej informacji na temat usługi Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
