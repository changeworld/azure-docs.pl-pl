---
title: Wdrożenia na stacjach roboczych zarządzanych platformy Azure — usłudze Azure Active Directory
description: Dowiedz się, jak wdrożyć bezpieczne, zarządzane usługi Azure stacjach roboczych, aby zmniejszyć ryzyko naruszenia zabezpieczeń z powodu błędnej konfiguracji lub kompromisowych rozwiązań.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51d8bbc8b8be9679fbf024d7c51de53c430dc493
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550485"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Wdróż bezpieczne, zarządzane usługi Azure stacji roboczej

Teraz, jak [zrozumieć bezpiecznego stacje robocze](concept-azure-managed-workstation.md), nadszedł czas, aby rozpocząć proces wdrażania. Z tego przewodnika użyjesz zdefiniowane profile do utworzenia stacji roboczej, która zapewnia większe bezpieczeństwo od samego początku.

![Wdrażanie bezpiecznej stacji roboczej](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Należy wybrać profil, zanim będzie można wdrożyć rozwiązanie. Można używać wielu profilów jednocześnie w ramach wdrożenia i przypisać je z tagami lub grup.
> [!NOTE]
> Zastosuj wszelkie profilów, odpowiednio do potrzeb wymagań. Można przenieść do innego profilu, przypisując go w usłudze Intune.

| Profil | Małe | Rozszerzone | Wysoka | Wyspecjalizowany | Zabezpieczone | Izolowane |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Użytkownik w usłudze Azure AD | Tak | Yes | Yes | Yes | Yes | Tak |
| Zarządzane przez usługę Intune | Tak | Yes | Yes | Yes | Yes | Tak |
| Urządzenia — zarejestrowana z usługi Azure AD | Tak |  |  |  |  | |   |
| Urządzenia — przyłączony Azure AD |   | Tak | Yes | Yes | Yes | Tak |
| Usługa Intune linii bazowej zabezpieczeń stosowane |   | Tak <br> (Rozszerzony) | Tak <br> (HighSecurity) | Yes <br> (NCSC) | Tak <br> (Zabezpieczone) |  Nie dotyczy |
| Sprzęt spełnia bezpiecznego systemu Windows 10 i standardy |   | Tak | Yes | Yes | Yes | Yes |
| Włączony program Microsoft Defender ATP |   | Tak  | Yes | Yes | Yes | Tak |
| Usuwanie uprawnień administratora |   |   | Tak  | Yes | Yes | Yes |
| Wdrażanie przy użyciu Microsoft Autopilot |   |   | Yes  | Yes | Yes | Yes |
| Aplikacje zainstalowane tylko przez usługę Intune |   |   |   | Tak | Yes |Yes |
| Ograniczone do listy zatwierdzonych adresów URL |   |   |   | Tak | Yes |Tak |
| Internet zablokowane (dla ruchu przychodzącego/wychodzącego) |   |   |   |  |  |Tak |

## <a name="license-requirements"></a>Wymagania licencyjne

Pojęcia opisane w tym przewodniku przyjęto założenie, że masz Microsoft 365 Enterprise E5 lub równoważne jednostki SKU. Niektóre zalecenia przedstawione w tym przewodniku można zaimplementować przy użyciu niższe jednostki SKU. Aby uzyskać więcej informacji, zobacz [Licencjonowanie rozwiązania Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Aby zautomatyzować licencji inicjowania obsługi administracyjnej, należy wziąć pod uwagę [Licencjonowanie na podstawie grupy](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) dla użytkowników.

## <a name="azure-active-directory-configuration"></a>Konfiguracja usługi Active Directory systemu Azure

Azure Active Directory (Azure AD) zarządza użytkowników, grup i urządzeń dla stacji roboczych z administratorem. Należy włączyć usługi zarządzania tożsamościami i funkcji przy użyciu [konta administratora](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Podczas tworzenia konta administratora zabezpieczonej stacji roboczej, należy udostępnić konta na bieżącej stacji roboczej. Upewnij się, że za pomocą znanych, bezpiecznych urządzeń tej początkowej konfiguracji i wszystkie konfiguracje globalne. Aby zmniejszyć podatność na zagrożenia środowiska po raz pierwszy, należy wziąć pod uwagę następujące [ze wskazówkami, aby zapobiec przed infekcjami złośliwym oprogramowaniem](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

Trzeba również wymagać uwierzytelniania wieloskładnikowego, przynajmniej dla administratorów. Zobacz [wdrażanie oparte na chmurze usługa MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) uzyskać wytyczne dotyczące implementacji.

### <a name="azure-ad-users-and-groups"></a>Użytkownicy usługi Azure AD i grup

1. W witrynie Azure portal, przejdź do **usługi Azure Active Directory** > **użytkowników** > **nowego użytkownika**.
1. Utwórz administratora urządzenia, wykonując kroki opisane w [utworzyć użytkownika w ramach samouczka](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Wprowadź:
   * **Nazwa** — zabezpieczanie stacji roboczej administratora
   * **Nazwa użytkownika** - `secure-ws-admin@identityitpro.com`
   * **Rola katalogu** - **ograniczony administrator** i wybierz **administratora usługi Intune** roli.
1. Wybierz pozycję **Utwórz**.

Następnie należy utworzyć dwie grupy: użytkownicy stacji roboczej i urządzeń stacji roboczej.

W witrynie Azure portal, przejdź do **usługi Azure Active Directory** > **grup** > **nową grupę**.

1. Dla grupy Użytkownicy stacji roboczej, warto skonfigurować [Licencjonowanie na podstawie grupy](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) Aby zautomatyzować aprowizację licencji do użytkowników.
1. Dla grupy Użytkownicy stacji roboczej wprowadź:
   * **Typ grupy** — zabezpieczenia
   * **Nazwa grupy** — zabezpieczanie użytkownicy stacji roboczej
   * **Typ członkostwa** — przypisane
1. Dodaj użytkownika administratora bezpiecznego stacji roboczej: `secure-ws-admin@identityitpro.com`
1. Możesz dodać innych użytkowników, zarządzania bezpiecznym stacjach roboczych.
1. Wybierz pozycję **Utwórz**.

1. Dla grupy urządzeń stacji roboczej wprowadź:
   * **Typ grupy** — zabezpieczenia
   * **Nazwa grupy** — zabezpieczanie stacji roboczych
   * **Typ członkostwa** — przypisane
1. Wybierz pozycję **Utwórz**.

### <a name="azure-ad-device-configuration"></a>Konfiguracja urządzenia w usłudze Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Określ, kto może dołączać urządzenia do usługi Azure AD

Konfigurowanie urządzeń ustawienie w usłudze Active Directory, aby zezwolić na grupy zabezpieczeń administracyjnych przyłączać urządzenia do Twojej domeny. Aby skonfigurować to ustawienie w witrynie Azure portal:

1. Przejdź do **usługi Azure Active Directory** > **urządzeń** > **ustawienia urządzenia**.
1. Wybierz **wybrane** w obszarze **użytkownicy mogą dołączać urządzania do usługi Azure AD**, a następnie wybierz grupę "Użytkownicy stacji roboczej zabezpieczenia".

#### <a name="removal-of-local-admin-rights"></a>Usunięcie prawa administratora lokalnego

Ta metoda wymaga, czy użytkownicy adresów VIP, metodyki DevOps i stacje robocze zabezpieczenia na poziomie mieć żadnych praw administratora na swoich komputerach. Aby skonfigurować to ustawienie w witrynie Azure portal:

1. Przejdź do **usługi Azure Active Directory** > **urządzeń** > **ustawienia urządzenia**.
1. Wybierz **Brak** w obszarze **Dodatkowi administratorzy lokalni w usłudze Azure AD urządzenia przyłączone do**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Wymagaj uwierzytelniania wieloskładnikowego do dołączania urządzeń

Aby jeszcze bardziej wzmocnić procesu przyłączania urządzenia do usługi Azure AD:

1. Przejdź do **usługi Azure Active Directory** > **urządzeń** > **ustawienia urządzenia**.
1. Wybierz **tak** w obszarze **Wymagaj uwierzytelniania wieloskładnikowego do dołączania urządzeń**.
1. Wybierz pozycję **Zapisz**.

#### <a name="configure-mdm"></a>Konfigurowanie zarządzania urządzeniami Przenośnymi

W witrynie Azure portal:

1. Przejdź do **usługi Azure Active Directory** > **mobilność (Zarządzanie urządzeniami Przenośnymi i zarządzania aplikacjami Mobilnymi)**  > **usługi Microsoft Intune**.
1. Zmiana **zakres użytkownika oprogramowania MDM** ustawienie **wszystkich**.
1. Wybierz pozycję **Zapisz**.

Poniższe kroki umożliwiają zarządzanie dowolnym urządzeniu za pomocą usługi Intune. Aby uzyskać więcej informacji, zobacz [szybkiego startu usługi Intune: Konfigurowanie automatycznej rejestracji urządzeń z systemem Windows 10](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Zasady konfiguracji i zgodności usługi Intune są tworzone w przyszłym kroku.

#### <a name="azure-ad-conditional-access"></a>Dostęp warunkowy Azure AD

Azure AD warunkowego dostępu można ograniczyć zadania wymagające uprawnień administracyjnych do zgodnych urządzeń. Wstępnie zdefiniowane elementy członkowskie **Secure użytkownicy stacji roboczej** grupy są wymagane do wykonywania uwierzytelniania wieloskładnikowego podczas logowania do aplikacji w chmurze. Najlepszym rozwiązaniem jest wyłączenie kont dostępu awaryjnego z zasad. Aby uzyskać więcej informacji, zobacz [zarządzania kont dostępu awaryjnego w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Aby skonfigurować dostęp warunkowy w witrynie Azure portal:

1. Przejdź do **usługi Azure Active Directory** > **dostępu warunkowego** > **nowe zasady**.
1. Wprowadź:
   * **Nazwa** — bezpieczne urządzenie wymagane zasady
   * Przypisania
     * **Użytkownicy i grupy**
       * Obejmują - **użytkowników i grup** — wybierz tę opcję **Secure użytkownicy stacji roboczej** utworzoną wcześniej grupę.
       * Wyklucz - **użytkowników i grup** — Wybieranie kont dostępu awaryjnego Twojej organizacji.
     * **Aplikacje w chmurze** — obejmują **wszystkie aplikacje w chmurze**.
    * Kontrola dostępu
      * **Udziel** — wybierz tę opcję **udzielić dostępu** przycisku radiowego.
        * **Wymagaj uwierzytelniania wieloskładnikowego**.
        * **Wymagaj, aby urządzenie było oznaczone jako zgodne**.
        * W przypadku wielu kontrolek — **Wymagaj wszystkich wybranych kontrolek**.
    * Włącz zasady — **na**.

Masz możliwość utworzenia zasad, które blokują krajach, w których użytkownicy nie będzie dostępu do zasobów firmy. Aby uzyskać więcej informacji na temat zasad dostępu warunkowego na podstawie lokalizacji adresu IP, zobacz [warunek lokalizacji w usłudze Azure Active Directory dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Konfiguracja usługi Intune

### <a name="configure-enrollment-status"></a>Skonfiguruj stan rejestracji

Koniecznie upewnij się, że bezpieczne stacji roboczej zaufanych czystym urządzeniu. Przy zakupie nowych urządzeń, mogą nalegać, że są one fabryki równa [Windows 10 Pro w trybie S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), co ogranicza narażenia na luki w zabezpieczeniach podczas zarządzania łańcuchem dostaw. Po otrzymaniu urządzenia z dostawcą, można użyć rozwiązania Autopilot można go zmienić z trybu S. Poniższe wskazówki zawiera szczegółowe informacje na temat stosowania proces przekształcania.

Aby upewnić się, że urządzenia są w pełni skonfigurowane przed użyciem, usługa Intune udostępnia środki do **zablokować użycie urządzenia, dopóki nie zostaną zainstalowane wszystkie aplikacje i profile**.

Z **witryny Azure portal**:
1. Przejdź do **Microsoft Intune** > **rejestracji urządzeń** > **rejestracji Windows** > **stan rejestracji Strona** > **domyślne** > **ustawienia**.
1. Ustaw **wyświetlany postęp instalacji profilu aplikacji** do **tak**.
1. Ustaw **zablokować użycie urządzenia, dopóki nie zostaną zainstalowane wszystkie aplikacje i profile** do **tak**.

### <a name="create-an-autopilot-deployment-profile"></a>Tworzenie profilu wdrażania rozwiązania Autopilot

Po utworzeniu grupy urządzeń, należy utworzyć profil wdrożenia do skonfigurowania urządzeń z rozwiązaniem Autopilot.

W usłudze Intune w witrynie Azure portal:

1. Wybierz **rejestracji urządzeń** > **rejestracji Windows** > **profile wdrażania** > **Utwórz profil** .
1. Wprowadź:
   * Nazwa — **Secure profilu wdrażania stacji roboczej**.
   * Opis — **wdrożenia z stacje robocze bezpiecznego**.
   * Ustaw **przekonwertować wszystkie objęte nimi urządzenia rozwiązania Autopilot** do **tak**. To ustawienie gwarantuje, że wszystkie urządzenia na liście Zarejestruj się przy użyciu usługi wdrażania rozwiązania Autopilot. Zezwalaj na rejestrację, które mają być przetwarzane w ciągu 48 godzin.
1. Wybierz opcję **Dalej**.
   * Aby uzyskać **tryb wdrożenia**, wybierz **własnym wdrażania (wersja zapoznawcza)** . Urządzenia z tym profilem są skojarzone z użytkownikiem, który zarejestrował urządzenie. Poświadczenia użytkownika są wymagane do zarejestrowania urządzenia.
   * **Dołączania do usługi Azure AD jako** pola powinny być widoczne **przyłączony Azure AD** i być wyszarzone.
   * Wybierz swoje Langugage (Region), typ konta dla użytkownika **standardowa**. 
1. Wybierz opcję **Dalej**.
   * Wybierz tag zakresu, jeśli zostały wstępnie skonfigurowane jedną.
1. Wybierz opcję **Dalej**.
1. Wybierz **przypisania** > **przypisać** > **wybrane grupy**. W **wybierz grupy do uwzględnienia**, wybierz **Secure użytkownicy stacji roboczej**.
1. Wybierz opcję **Dalej**.
1. Wybierz pozycję **Utwórz**, aby utworzyć profil. Profil wdrażania rozwiązania Autopilot jest teraz dostępny do przypisania do urządzenia.

### <a name="configure-windows-update"></a>Konfigurowanie Windows Update

Aktualizowanie systemu Windows 10 jest jednym z najważniejszych czynników, które można zrobić. Aby zachować Windows w stanie bezpiecznym, wdrażanie pierścienia aktualizacji, aby zarządzać tempie zastosowaniu aktualizacji na stacjach roboczych. 

Niniejsze wskazówki zaleca się, Utwórz nowy pierścień aktualizacji, a następnie zmienić następujące ustawienia domyślne:

W witrynie Azure Portal:

1. Przejdź do **Microsoft Intune** > **aktualizacji oprogramowania** > **pierścienie aktualizacji systemu Windows 10**.
1. Wprowadź:
   * Nazwa — **usługi Azure managed aktualizacje stacji roboczej**
   * Kanał - obsługi **Windows Insider - Fast**
   * Odroczenie aktualizacji dotyczącej jakości aktualizacji (dni) - **3**
   * Okres odroczenia aktualizacji funkcji (dni) - **3**
   * Zachowanie automatycznych aktualizacji - **automatycznie Zainstaluj i uruchom ponownie bez kontroli użytkownika końcowego**
   * Zablokuj użytkownikom możliwość Wstrzymywanie aktualizacji Windows - **bloku**
   * Wymagaj zatwierdzenia użytkownika o ponowne uruchomienie poza godzinami pracy — **wymagane**
   * Zezwalaj użytkownikowi na ponowne uruchomienie (zaangażowanych jest ponowne uruchomienie) - **wymagane**
   * Przeniesienie użytkowników do ponownego uruchomienia zaangażowani po automatycznego uruchamiania ponownego dla zaplanowanych (dni) - **3**
   * Odłóż zaangażowani ponownego uruchomienia monitów (dni) - **3**
   * Ustaw termin ostateczny oczekujące ponowne uruchomienie (w dniach) - **3**

1. Wybierz pozycję **Utwórz**.
1. Na **przypisania** kartę, należy dodać **bezpieczny z stacje robocze** grupy.

Aby uzyskać dodatkowe informacje na temat zasady usługi Windows Update, zobacz [zasad dostawcy usług Kryptograficznych — aktualizacja](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integracja z systemem Windows Defender ATP w usłudze Intune

Windows Defender ATP i Microsoft Intune współpracują ze sobą w celu zapobiegania naruszeniom zabezpieczeń. Można również ograniczyć wpływ naruszeń. Możliwości zaawansowanej ochrony przed zagrożeniami umożliwiają wykrywanie zagrożeń w czasie rzeczywistym także umożliwić rozbudowane inspekcji i rejestrowania urządzeń punkt końcowy.

Aby skonfigurować integrację usługi Windows Defender ATP i usługi Intune, przejdź do witryny Azure portal.

1. Przejdź do **Microsoft Intune** > **zgodność urządzenia** > **usługi Windows Defender ATP**.
1. W kroku 1 w sekcji **konfigurowania usługi Windows Defender ATP**, wybierz opcję **połączyć usługi Windows Defender ATP usługą Microsoft Intune w usłudze Windows Defender Security Center**.
1. W usłudze Windows Defender Security Center:
   1. Wybierz **ustawienia** > **zaawansowane funkcje**.
   1. Aby uzyskać **połączenia Microsoft Intune**, wybierz **na**.
   1. Wybierz **Zapisz preferencje**.
1. Po nawiązaniu połączenia zwróć się do usługi Intune i wybierz pozycję **Odśwież** u góry.
1. Ustaw **Windows Połącz urządzenia, wersja 10.0.15063 lub nowszym do usługi Windows Defender ATP** do **na**.
1. Wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji, zobacz [zaawansowanej ochrony przed zagrożeniami programu Windows Defender](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Ukończ Tworzenie profilu stacji roboczej zaostrzanie poziomu zabezpieczeń

Aby pomyślnie wykonać ograniczania funkcjonalności rozwiązania, Pobierz i uruchom odpowiedni skrypt. Znajdź łącza pobierania, aby uzyskać odpowiednie **profilu poziom**:

| Profil | Lokalizacja pobierania | Nazwa pliku |
| --- | --- | --- |
| Niski poziom zabezpieczeń | ND |  ND |
| Większe bezpieczeństwo | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Wysoki poziom zabezpieczeń  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Wyspecjalizowany | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Wyspecjalizowane zgodności * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Zabezpieczone | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* Wyspecjalizowane zgodności to skrypt, który wymusza podana w NCSC Windows10 SecurityBaseline Konfiguracja specjalne.

Po skryptu zostało wykonane pomyślnie, można wprowadzić aktualizacje, profile i zasady w usłudze Intune. Skrypty do profilów rozszerzony i bezpiecznego Tworzenie zasad i profilów dla Ciebie, ale należy przypisać zasady do Twojej **bezpieczny z stacje robocze** grupy.

* Poniżej przedstawiono, gdzie można znaleźć profile konfiguracji urządzeń usługi Intune utworzone przez skrypty: **Witryna Azure portal** > **Microsoft Intune** > **konfiguracji urządzenia** > **profile**.
* Poniżej przedstawiono, gdzie można znaleźć urządzeń w usłudze Intune zasady zgodności utworzone przez skrypty: **Witryna Azure portal** > **Microsoft Intune** > **zgodność urządzenia** > **zasady**.

Aby przejrzeć zmiany wprowadzone przez skrypty, możesz wyeksportować profile. W ten sposób można określić dodatkowe ograniczenie funkcjonalności, które mogą być wymagane, co zostało opisane w dokumentacji SECCON.

Uruchom skrypt eksportu danych usługi Intune `DeviceConfiguration_Export.ps1` z [repozytorium GiuHub Konfiguracja urządzenia](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) Aby wyeksportować wszystkie bieżące profilów usługi Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Dodatkowe konfiguracje i zaostrzanie poziomu zabezpieczeń, które należy rozważyć

Postępując zgodnie ze wskazówkami zawartymi w tym miejscu, udało Ci się wdrożyć bezpieczne stacji roboczej. Jednak należy również rozważyć dodatkowe kontrole. Na przykład:

* Ograniczanie dostępu do alternatywnego przeglądarek
* Zezwalaj na HTTP ruchu wychodzącego
* Blokuj wybierz witryn sieci Web
* Ustaw uprawnienia do uruchamiania niestandardowych skryptów środowiska PowerShell

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Ustawianie reguł w zapory dostawca usługi konfiguracji (CSP)

Zgodnie z potrzebami dla punktów końcowych dozwolonych i zablokowanych, może wprowadzić dodatkowe zmiany do zarządzania reguły ruchu przychodzącego i wychodzącego. W miarę postępu wzmacniania zabezpieczeń stacji roboczej bezpiecznego można Poluzuj ograniczeń, która zakazuje całego ruchu przychodzącego i wychodzącego. Można dodać dozwolonych witryn ruchu wychodzącego obejmują typowe i zaufanych witryn sieci Web. Aby uzyskać więcej informacji, zobacz [zapory usługi konfiguracji](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Domyślne ograniczone zalecenia są:

* Odmów wszystkie przychodzące
* Odmów wszystkie wychodzące

Przechowuje projekt Spamhaus [domeny blok listy ((podwójna) kartka)](https://www.spamhaus.org/dbl/): odpowiedni zasób do użycia jako punkt początkowy dla blokowanie witryn.

### <a name="manage-local-applications"></a>Zarządzanie aplikacjami lokalne

Bezpieczne stacji roboczej przenosi się do naprawdę ze wzmocnionymi zabezpieczeniami stanu, po usunięciu lokalne aplikacje, w tym aplikacji biurowych. W tym miejscu Dodaj Chrome jako domyślnej przeglądarki i ograniczyć możliwość modyfikowania przeglądarki i jego wtyczek użytkownika przy użyciu usługi Intune.

#### <a name="deploy-applications-using-intune"></a>Wdrażanie aplikacji przy użyciu usługi Intune

W niektórych sytuacjach na zabezpieczonej stacji roboczej są wymagane aplikacje, takie jak przeglądarki Google Chrome. W poniższym przykładzie przedstawiono instrukcje dotyczące instalacji dla programu Chrome na urządzeniach w grupie zabezpieczeń **bezpieczny z stacje robocze**.

1. Pobierz Instalatora w trybie offline [pakietu dla programu Chrome dla Windows 64‑bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Wyodrębnij pliki i zanotuj lokalizację `GoogleChromeStandaloneEnterprise64.msi` pliku.
1. W **witryny Azure portal** wskaż **Microsoft Intune** > **aplikacje klienckie** > **aplikacje**  >  **Dodaj**.
1. W obszarze **typ aplikacji**, wybierz **Line-of-business**.
1. W obszarze **plik pakietu aplikacji**, wybierz opcję `GoogleChromeStandaloneEnterprise64.msi` pliku z lokalizacji wyodrębnionych i wybierz **OK**.
1. W obszarze **informacje o aplikacji**, podaj opis i wydawcy. Kliknij przycisk **OK**.
1. Wybierz pozycję **Dodaj**.
1. Na **przypisania** zaznacz **dostępne dla zarejestrowanych urządzeń** w obszarze **typ przypisania**.
1. W obszarze **objęte grupy**, Dodaj **bezpieczny z stacje robocze** grupy.
1. Wybierz **OK**, a następnie wybierz pozycję **Zapisz**.

Aby uzyskać więcej wskazówek na temat konfigurowania ustawień dla programu Chrome, zobacz [Zarządzanie przeglądarki Chrome w usłudze Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurowanie aplikacji portal firmy dla aplikacji niestandardowych

W trybie zabezpieczonym instalacja aplikacji jest ograniczony do aplikacji portal firmy usługi Intune. Jednakże instalując aplikację portal wymaga dostępu do Microsoft Store. W rozwiązaniu do zabezpieczonej możesz udostępnić portal firmy na wszystkich urządzeniach przy użyciu trybu offline.

Zarządzane przez usługę Intune kopię [aplikacji Portal firmy](https://docs.microsoft.com/Intune/store-apps-company-portal-app) zapewnia dostęp na żądanie do dodatkowe narzędzia, które możesz wypychać w dół do użytkowników zabezpieczonej stacjach roboczych.

Użytkownik może być konieczne zainstalowanie aplikacji Windows 32-bitowych lub innych aplikacji, których wdrożenie wymaga specjalnych przygotowań. W takich przypadkach [narzędzie przygotowywania zawartości win32 Microsoft](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) zapewnia gotowe do użycia `.intunewin` pliku formatu do instalacji.

### <a name="use-powershell-to-create-custom-settings"></a>Aby utworzyć ustawienia niestandardowe przy użyciu programu PowerShell

Program PowerShell umożliwia również rozszerzyć możliwości zarządzania hostem. Ten przykładowy skrypt konfiguruje domyślne tło na hoście. Jest to funkcja, który jest również dostępna za pośrednictwem witryny Azure portal i profilów. Przykładowy skrypt służy tylko w celu zilustrowania funkcje programu PowerShell.

Może być konieczne skonfigurować niektóre formanty niestandardowe i ustawienia na bezpieczne stacjach roboczych. W tym przykładzie zmienia tło stacji roboczej za pomocą programu Powershell możliwości można łatwo zidentyfikować urządzenie jako gotowe do użycia, bezpieczna stacji roboczej.

[SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) skrypt z witryny Microsoft Scripting Center umożliwia Windows załadować ten [obraz tła bezpłatnej, ogólny](https://i.imgur.com/OAJ28zO.png) na uruchomieniu.

1. Pobierz skrypt na urządzenie lokalne.
1. Zaktualizuj customerXXXX i lokalizacji pobierania obrazu tła. W tym przykładzie firma Microsoft Zastąp customerXXXX do tła.  
1. Przejdź do **witryny Azure portal** > **Microsoft Intune** > **konfiguracji urządzenia** > **programu PowerShell skrypty** > **Dodaj**.
1. Podaj **nazwa** skryptu i określ **lokalizację skryptu**.
1. Wybierz pozycję **Konfiguruj**.
   1. Ustaw **Uruchom ten skrypt, używając poświadczeń zalogowanego** do **tak**.
   1. Kliknij przycisk **OK**.
1. Wybierz pozycję **Utwórz**.
1. Wybierz **przypisania** > **wybierz grupy**.
   1. Dodaj grupę zabezpieczeń **bezpieczny z stacje robocze**.
   1. Wybierz pozycję **Zapisz**.

## <a name="enroll-and-validate-your-first-device"></a>Rejestrowanie i zweryfikować pierwszego urządzenia

1. Aby zarejestrować urządzenie, potrzebne są następujące informacje:
   * **Numer seryjny** — na obudowę urządzenia.
   * **Identyfikator produktu Windows** — znaleziono w obszarze **systemu** > **o** z poziomu menu ustawień Windows.
   * Możesz uruchomić [Get WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) można pobrać plik CSV zawierający wyznaczania wartości skrótu w przypadku wszystkich wymaganych informacji dotyczących rejestracji urządzeń.
   
     Uruchom `Get-WindowsAutoPilotInfo – outputfile device1.csv` w danych wyjściowych informacji jako plik CSV, który można zaimportować w usłudze Intune.

     > [!NOTE]
     > Ten skrypt wymaga podwyższonym poziomem uprawnień. Działa jako zdalny podpisany. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` Polecenie umożliwia poprawne uruchomienie skryptu.

   * Te informacje można zebrać, logując się do systemu Windows 10 w wersji 1809 lub nowszej urządzeń. Odsprzedawcy sprzętu można też podać te informacje.
1. W **witryny Azure portal**, przejdź do **Microsoft Intune** > **rejestracji urządzeń** > **rejestracji Windows**  >  **Urządzeń — Zarządzanie rozwiązania Windows Autopilot urządzeń**.
1. Wybierz **importu** i wybierz plik CSV.
1. Dodaj urządzenie do **bezpieczny z stacje robocze** grupy zabezpieczeń.
1. Na urządzeniu Windows 10, chcesz skonfigurować, przejdź do **ustawienia Windows** > **aktualizacja i zabezpieczenia** > **odzyskiwania**.
   1. Wybierz **wprowadzenie** w obszarze **zresetować tego komputera**.
   1. Postępuj zgodnie z monitami, aby zresetować i ponownie skonfigurować urządzenia z zasadami dotyczącymi profilu i zgodności, skonfigurowane.

Po skonfigurowaniu urządzenia zakończenia przeglądu i Sprawdź konfigurację. Upewnij się, że pierwsze urządzenie jest poprawnie skonfigurowany, przed kontynuowaniem wdrożenia.

## <a name="assign-and-monitor"></a>Przypisywanie i monitorowanie

Aby przypisać urządzeń i użytkowników, należy zamapować [wybrane profile](https://docs.microsoft.com/intune/device-profile-assign) dla swojej grupy zabezpieczeń. Wszyscy nowi użytkownicy, którzy muszą mieć uprawnienia do usługi, należy dodać do grupy zabezpieczeń, a także.

Można monitorować profile z użyciem [monitorowania profilu usługi Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Zrozumienie [usługi Azure AD](https://docs.microsoft.com/azure/active-directory/index).
