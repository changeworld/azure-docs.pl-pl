---
title: Wdrażanie stacji roboczych zarządzanych przez platformę Azure — Azure Active Directory
description: Dowiedz się, jak wdrażać bezpieczne, zarządzane stacje robocze platformy Azure, aby zmniejszyć ryzyko naruszenia z powodu nieodpowiedniej konfiguracji lub złamania zabezpieczeń.
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
ms.openlocfilehash: 90687d0229d3ad74c287bb4aff4885dc26932e40
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227273"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Wdrażanie bezpiecznej, zarządzanej na platformie Azure stacji roboczej

Teraz, gdy [rozumiesz bezpieczne stacje robocze](concept-azure-managed-workstation.md), zaczniesz rozpocząć proces wdrażania. Te wskazówki umożliwiają użycie zdefiniowanych profilów w celu zapewnienia bezpieczniejszej stacji roboczej od początku.

![Wdrożenie zabezpieczonej stacji roboczej](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Musisz wybrać profil, aby można było wdrożyć rozwiązanie. Można użyć wielu profilów jednocześnie we wdrożeniu i przypisać je do tagów lub grup.
> [!NOTE]
> Zastosuj dowolne profile zgodnie z wymaganiami. Możesz przenieść do innego profilu, przypisując go w usłudze Intune.

| Profil | Małe | Rozszerzone | Wysoka | Wyspecjalizowany | Secure | Izolowane |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Użytkownik w usłudze Azure AD | Tak | Yes | Yes | Yes | Yes | Tak |
| Usługa Intune — zarządzana | Tak | Yes | Yes | Yes | Yes | Tak |
| Urządzenie — zarejestrowano usługę Azure AD | Tak |  |  |  |  | |   |
| Urządzenie — przyłączone do usługi Azure AD |   | Yes | Yes | Yes | Yes | Yes |
| Zastosowano linię bazową zabezpieczeń usługi Intune |   | Tak <br> Usprawnion | Yes <br> (HighSecurity) | Tak <br> (NCSC) | Yes <br> Secure |  Nie dotyczy |
| Sprzęt spełnia bezpieczne standardy systemu Windows 10 |   | Yes | Yes | Yes | Yes | Tak |
| Usługa Microsoft Defender ATP została włączona |   | Tak  | Yes | Yes | Yes | Tak |
| Usuwanie praw administratora |   |   | Tak  | Yes | Yes | Tak |
| Wdrażanie przy użyciu programu Microsoft autopilotaż |   |   | Yes  | Yes | Yes | Tak |
| Aplikacje zainstalowane tylko przez usługę Intune |   |   |   | Tak | Yes |Tak |
| Adresy URL ograniczone do zatwierdzonej listy |   |   |   | Tak | Yes |Yes |
| Zablokowany przez Internet (przychodzący/wychodzący) |   |   |   |  |  |Tak |

## <a name="license-requirements"></a>Wymagania licencyjne

Pojęcia omówione w tym przewodniku założono, że masz Microsoft 365 Enterprise E5 lub równoważną jednostkę SKU. Niektóre zalecenia zawarte w tym przewodniku można zaimplementować przy użyciu mniejszych jednostek SKU. Aby uzyskać więcej informacji, zobacz [Microsoft 365 Enterprise Licencjonowanie](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

W celu zautomatyzowania aprowizacji licencji należy rozważyć [Licencjonowanie oparte na grupach](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) dla użytkowników.

## <a name="azure-active-directory-configuration"></a>Konfiguracja Azure Active Directory

Azure Active Directory (Azure AD) zarządza użytkownikami, grupami i urządzeniami dla stacji roboczych administratora. Należy włączyć usługi tożsamości i funkcje przy użyciu [konta administratora](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Podczas tworzenia konta administratora zabezpieczonej stacji roboczej należy uwidocznić konto na bieżącej stacji roboczej. Upewnij się, że używasz znanego bezpiecznego urządzenia, aby wykonać tę konfigurację początkową i wszystkie konfiguracje globalne. Aby zmniejszyć narażenie na ataki podczas pierwszego środowiska, należy wziąć pod uwagę [wskazówki, aby zapobiec infekcjom złośliwego oprogramowania](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

Należy również wymagać uwierzytelniania wieloskładnikowego, co najmniej dla administratorów. Zobacz [wdrażanie usługi MFA opartej na chmurze](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) w celu uzyskania wskazówek dotyczących implementacji.

### <a name="azure-ad-users-and-groups"></a>Użytkownicy i grupy usługi Azure AD

1. W Azure Portal przejdź do **Azure Active Directory** > **użytkowników** > **nowy użytkownik**.
1. Aby utworzyć administratora urządzenia, wykonaj czynności opisane w [samouczku Tworzenie użytkownika](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Wejść
   * **Nazwa** — administrator bezpiecznego stacji roboczej
   * **Nazwa użytkownika** - `secure-ws-admin@identityitpro.com`
   *  - **Administrator z ograniczoną** rolą katalogu i wybierz rolę **administratora usługi Intune** .
1. Wybierz pozycję **Utwórz**.

Następnie utworzysz dwie grupy: Użytkownicy stacji roboczej i urządzenia stacji roboczej.

W Azure Portal przejdź do **Azure Active Directory** > **grup** > **Nowa grupa**.

1. W przypadku grupy Użytkownicy stacji roboczej możesz chcieć skonfigurować [Licencjonowanie oparte na grupach](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) w celu zautomatyzowania aprowizacji licencji dla użytkowników.
1. Dla grupy Użytkownicy stacji roboczej wpisz:
   * **Typ grupy** — zabezpieczenia
   * **Nazwa grupy** — bezpieczna użytkownicy stacji roboczej
   * Przypisany **Typ członkostwa**
1. Dodaj użytkownika administratora bezpiecznego stacji roboczej:`secure-ws-admin@identityitpro.com`
1. Można dodać innych użytkowników, którzy będą zarządzać bezpiecznymi stacjami roboczymi.
1. Wybierz pozycję **Utwórz**.

1. Dla grupy urządzenia stacji roboczej wpisz:
   * **Typ grupy** — zabezpieczenia
   * **Nazwa grupy** — bezpieczne stacje robocze
   * Przypisany **Typ członkostwa**
1. Wybierz pozycję **Utwórz**.

### <a name="azure-ad-device-configuration"></a>Konfiguracja urządzenia usługi Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Określanie osób, które mogą dołączać urządzenia do usługi Azure AD

Skonfiguruj ustawienia urządzeń w Active Directory, aby umożliwić grupie zabezpieczeń administracyjnych dołączanie urządzeń do domeny. Aby skonfigurować to ustawienie na podstawie Azure Portal:

1. Przejdź do pozycji **Azure Active Directory** > **Devices** > **Ustawienia urządzenia**.
1. Wybierz opcję **wybrane** w obszarze **Użytkownicy mogą dołączać urządzenia do usługi Azure AD**, a następnie wybierz grupę "Bezpieczna użytkownicy stacji roboczych".

#### <a name="removal-of-local-admin-rights"></a>Usuwanie praw administratora lokalnego

Ta metoda wymaga, aby użytkownicy adresów VIP, DevOps i stacji roboczych na poziomie bezpiecznym nie mieli uprawnień administratora na swoich komputerach. Aby skonfigurować to ustawienie na podstawie Azure Portal:

1. Przejdź do pozycji **Azure Active Directory** > **Devices** > **Ustawienia urządzenia**.
1. Wybierz pozycję **Brak** w obszarze **dodatkowi Administratorzy lokalni na urządzeniach dołączonych do usługi Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Wymagaj uwierzytelniania wieloskładnikowego w celu dołączania urządzeń

Aby bardziej zwiększyć proces dołączania urządzeń do usługi Azure AD:

1. Przejdź do pozycji **Azure Active Directory** > **Devices** > **Ustawienia urządzenia**.
1. Wybierz pozycję **tak** w obszarze **Wymagaj uwierzytelniania wieloskładnikowego, aby dołączać urządzenia**.
1. Wybierz pozycję **Zapisz**.

#### <a name="configure-mdm"></a>Konfigurowanie zarządzania urządzeniami przenośnymi

Z Azure Portal:

1. Przejdź do**Microsoft Intune** **Azure Active Directory** > **Mobility (MDM i mam)**  > .
1. Zmień ustawienie **zakresu użytkownika MDM** na **wszystkie**.
1. Wybierz pozycję **Zapisz**.

Te kroki umożliwiają zarządzanie dowolnym urządzeniem za pomocą usługi Intune. Aby uzyskać więcej informacji, [zobacz Przewodnik Szybki Start dotyczący usługi Intune: Konfigurowanie automatycznej rejestracji urządzeń](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)z systemem Windows 10. W przyszłości utworzysz konfigurację i zasady zgodności usługi Intune.

#### <a name="azure-ad-conditional-access"></a>Dostęp warunkowy Azure AD

Dostęp warunkowy usługi Azure AD może pomóc w ograniczeniu uprzywilejowanych zadań administracyjnych do zgodnych urządzeń. Wstępnie zdefiniowane elementy członkowskie grupy **bezpiecznych użytkowników stacji roboczej** są wymagane do przeprowadzenia uwierzytelniania wieloskładnikowego podczas logowania do aplikacji w chmurze. Najlepszym rozwiązaniem jest wykluczenie kont dostępu awaryjnego z zasad. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Aby skonfigurować dostęp warunkowy z Azure Portal:

1. Przejdź do pozycji **Azure Active Directory** > **dostęp** > warunkowy**nowe zasady**.
1. Wejść
   * **Nazwa** — zasady wymagane przez bezpieczne urządzenie
   * Przypisania
     * **Użytkownicy i grupy**
       * Dołącz — **Użytkownicy i grupy** — wybierz grupę **Użytkownicy grupy bezpiecznych stacji roboczych** utworzoną wcześniej.
       * Wykluczanie — **Użytkownicy i grupy** — wybierz konta dostępu awaryjnego w organizacji.
     * **Aplikacje w chmurze** — Dołącz **wszystkie aplikacje w chmurze**.
    * Kontrola dostępu
      * **Udziel** — wybierz przycisk radiowy **Udziel dostępu** .
        * **Wymagaj uwierzytelniania**wieloskładnikowego.
        * **Wymagaj, aby urządzenie było oznaczone jako zgodne**.
        * Dla wielu kontrolek — **Wymagaj wszystkich zaznaczonych kontrolek**.
    * Włącz **zasady.**

Istnieje możliwość utworzenia zasad, które blokują kraje, w których użytkownicy nie mają dostępu do zasobów firmy. Aby uzyskać więcej informacji na temat zasad dostępu warunkowego opartego na lokalizacji IP, zobacz [warunek lokalizacji w Azure Active Directory dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Konfiguracja usługi Intune

### <a name="configure-enrollment-status"></a>Konfigurowanie stanu rejestracji

Ważne jest, aby upewnić się, że bezpieczna stacja robocza jest zaufanym czystym urządzeniem. Podczas kupowania nowych urządzeń można określić, że są one fabrycznie ustawione na [system Windows 10 Pro w trybie S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), które ograniczają narażenie na luki w zabezpieczeniach podczas zarządzania łańcuchem dostaw. Po otrzymaniu urządzenia od dostawcy możesz użyć programu autopilotażu, aby zmienić go z trybu S. Poniższe wskazówki zawierają szczegółowe informacje dotyczące stosowania procesu transformacji.

Aby zapewnić, że urządzenia są w pełni skonfigurowane przed użyciem, usługa Intune udostępnia metodę **blokowania użycia urządzenia do momentu zainstalowania wszystkich aplikacji i profilów**.

Z **Azure Portal**:
1. Przejdź do pozycji **Microsoft Intune** > **rejestracji** > urządzeń Rejestracja**systemu Windows** > rejestrowanie**ustawień** **domyślnych** > ustawienia > strony.
1. Ustaw opcję **Pokaż postęp instalacji profilu aplikacji** na **wartość tak**.
1. Ustaw opcję **Blokuj użycie urządzenia, dopóki wszystkie aplikacje i profile nie zostaną zainstalowane** na **tak**.

### <a name="create-an-autopilot-deployment-profile"></a>Tworzenie profilu wdrożenia programu pilotażowego

Po utworzeniu grupy urządzeń należy utworzyć profil wdrożenia, aby skonfigurować urządzenia autopilotażowe.

W usłudze Intune w Azure Portal:

1. Wybierz pozycję **Rejestrowanie** > urządzenia**Profile** > wdrożeniaRejestracjasystemuWindowsUtwórz > **profil**.
1. Wejść
   * Nazwa — **bezpieczny profil wdrożenia stacji roboczej**.
   * Opis — **wdrażanie bezpiecznych stacji roboczych**.
   * Ustaw wartość **tak** **dla opcji Konwertuj wszystkie urządzenia skierowane na** pozycję autopilotaż. To ustawienie gwarantuje, że wszystkie urządzenia na liście są zarejestrowane w usłudze wdrożenia autopilotażu. Zezwalaj na przetwarzanie rejestracji przez 48 godzin.
1. Wybierz opcję **Dalej**.
   * W obszarze **Tryb wdrożenia**wybierz opcję **samodzielne wdrażanie (wersja zapoznawcza)** . Urządzenia z tym profilem są skojarzone z użytkownikiem, który zarejestrował urządzenie. Poświadczenia użytkownika są wymagane do zarejestrowania urządzenia. Należy pamiętać, że wdrożenie urządzenia w trybie samodzielnego **wdrażania** umożliwi wdrożenie laptopów w modelu udostępnionym. Przypisanie użytkownika nie zostanie wykonane, dopóki urządzenie nie zostanie przypisane do użytkownika po raz pierwszy. W związku z tym wszelkie zasady użytkownika, takie jak funkcja BitLocker, nie będą włączane do momentu ukończenia przypisania użytkownika. Aby uzyskać więcej informacji na temat sposobu logowania się na zabezpieczonym urządzeniu, zobacz [wybrane profile](https://docs.microsoft.com/intune/device-profile-assign).
   * Pole **Dołącz do usługi Azure AD jako** powinno zawierać przyłączone do usługi **Azure AD** i być wyszarzone.
   * Wybierz swój Langugage (region), typ konta użytkownika **Standard**. 
1. Wybierz opcję **Dalej**.
   * Wybierz tag zakresu, jeśli został wstępnie skonfigurowany.
1. Wybierz opcję **Dalej**.
1. Wybierz pozycję **przypisania** > **Przypisz do** > **wybranych grup**. W obszarze **Wybierz grupy do**dołączenia wybierz pozycję **bezpieczna użytkownicy stacji roboczej**.
1. Wybierz opcję **Dalej**.
1. Wybierz pozycję **Utwórz**, aby utworzyć profil. Profil wdrożenia programu pilotażowego jest teraz dostępny do przypisywania do urządzeń.

Rejestracja urządzeń w programie autopilotaż zapewnia różne środowisko użytkownika w oparciu o typ i rolę urządzenia. W naszym przykładowym wdrożeniu przedstawiono model, w którym są wdrażane zbiorczo dane zabezpieczone i można je udostępnić, ale gdy jest używany po raz pierwszy, urządzenie jest przypisane do użytkownika. Aby uzyskać więcej informacji, zobacz [rejestracja urządzeń](https://docs.microsoft.com/intune/device-enrollment)autopilotażowego usługi Intune.

### <a name="configure-windows-update"></a>Konfigurowanie Windows Update

Zachowywanie aktualności systemu Windows 10 jest jedną z najważniejszych rzeczy, które można wykonać. Aby zapewnić obsługę systemu Windows w bezpiecznym stanie, należy wdrożyć pierścień aktualizacji w celu zarządzania tym, w jaki sposób aktualizacje są stosowane do stacji roboczych. 

W tych wskazówkach zaleca się utworzenie nowego pierścienia aktualizacji i zmianę następujących ustawień domyślnych:

W witrynie Azure Portal:

1. Przejdź do **Microsoft Intune** > **aktualizacje** > oprogramowania —**pierścienie aktualizacji systemu Windows 10**.
1. Wejść
   * Nazwa — **aktualizacje stacji roboczej zarządzanej przez platformę Azure**
   * Kanał obsługi — **niejawny tester systemu Windows — Fast**
   * Odroczenie aktualizacji jakości (dni) — **3**
   * Okres odroczenia aktualizacji funkcji (w dniach) — **3**
   * Zachowanie automatycznej aktualizacji — **automatyczne instalowanie i ponowne uruchamianie bez kontroli użytkownika końcowego**
   * Zablokuj użytkownikowi możliwość wstrzymywania aktualizacji systemu Windows — **blok**
   * Wymagaj zatwierdzenia użytkownika w celu ponownego uruchomienia poza godzinami pracy — **wymagane**
   * Zezwalaj użytkownikowi na ponowne uruchomienie (Zaangażuj ponownie) — **wymagane**
   * Przejście użytkowników do ponownego uruchomienia po ponownym uruchomieniu (dni) — **3**
   * Odłożenie przypomnienia o ponownym uruchomieniu (dni) — **3**
   * Ustaw termin dla oczekujących ponownych uruchomień (w dniach) — **3**

1. Wybierz pozycję **Utwórz**.
1. Na karcie **przypisania** Dodaj grupę **bezpiecznych stacji roboczych** .

Aby uzyskać dodatkowe informacje na temat zasad Windows Update, zobacz [Policy CSP-Update](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integracja usługi Windows Defender ATP w usłudze Intune

Usługa Windows Defender ATP i Microsoft Intune współdziałają ze sobą, aby zapobiec naruszeniom zabezpieczeń. Mogą również ograniczyć wpływ naruszeń. Funkcje ATP zapewniają wykrywanie zagrożeń w czasie rzeczywistym oraz umożliwiają przeprowadzanie obszernej inspekcji i rejestrowania urządzeń końcowych.

Aby skonfigurować integrację usługi Windows Defender ATP i usługi Intune, przejdź do Azure Portal.

1. Przejdź do **Microsoft Intune** > **zgodności** > urządzeń z usługą**Windows Defender ATP**.
1. W kroku 1 w obszarze **Konfigurowanie programu Windows Defender ATP**wybierz pozycję **Połącz usługę Windows Defender atp, aby Microsoft Intune w Security Center Windows Defender**.
1. W Security Center Windows Defender:
   1. Wybierz pozycję **Ustawienia** > **Zaawansowane funkcje**.
   1. W obszarze **połączenie Microsoft Intune**wybierz pozycję **włączone**.
   1. Wybierz pozycję **Zapisz preferencje**.
1. Po nawiązaniu połączenia Wróć do usługi Intune i wybierz pozycję **Odśwież** w górnej części.
1. Ustaw dla opcji **Połącz urządzenia z systemem Windows w wersji 10.0.15063 lub nowszej, aby włączyć usługę Windows Defender ATP** .
1. Wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji, zobacz Usługa [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Zakończenie ograniczania funkcjonalności profilu stacji roboczej

Aby pomyślnie ukończyć wzmacnianie rozwiązania, Pobierz i wykonaj odpowiedni skrypt. Znajdź linki do pobrania dla żądanego **poziomu profilu**:

| Profil | Lokalizacja pobierania | Nazwa pliku |
| --- | --- | --- |
| Niski poziom zabezpieczeń | ND |  ND |
| Większe bezpieczeństwo | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Wysoki poziom zabezpieczeń  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Wyspecjalizowany | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline. ps1 |
| Specjalna zgodność * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10 (1803). ps1 |
| Secure | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*Wyspecjalizowana zgodność to skrypt, który wymusza wyspecjalizowaną konfigurację podaną w NCSC Windows10 SecurityBaseline.

Po pomyślnym wykonaniu skryptu można wprowadzać aktualizacje profilów i zasad w usłudze Intune. Skrypty rozszerzonego i bezpiecznego profilu tworzą zasady i profile, ale należy je przypisać do grupy **bezpiecznych stacji roboczych** .

* Tutaj znajdują się informacje o profilach konfiguracji urządzeń w usłudze Intune tworzonych przez skrypty: **Azure Portal** **Microsoft Intune**profile konfiguracjiurządzeń. >  >  > 
* Tutaj można znaleźć zasady zgodności urządzeń w usłudze Intune utworzone przez skrypty: **Azure Portal** **Microsoft Intune**zasady zgodnościurządzeń. >  >  > 

Aby zapoznać się ze zmianami wprowadzonymi przez skrypty, można wyeksportować profile. W ten sposób można określić dodatkowe zabezpieczenia, które mogą być wymagane, jak opisano w dokumentacji SECCON.

Uruchom skrypt `DeviceConfiguration_Export.ps1` eksportu danych usługi Intune z [repozytorium DeviceConfiguration GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) , aby wyeksportować wszystkie bieżące profile usługi Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Dodatkowe konfiguracje i Ograniczanie funkcjonalności do rozważenia

Postępując zgodnie ze wskazówkami w tym miejscu, wdrożono bezpieczną stację roboczą. Należy jednak również rozważyć dodatkowe kontrole. Na przykład:

* Ograniczanie dostępu do przeglądarek alternatywnych
* Zezwalaj na wychodzące HTTP
* Blokuj Wybieranie witryn sieci Web
* Ustawianie uprawnień do uruchamiania niestandardowych skryptów programu PowerShell

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Ustawianie reguł w dostawcy usługi konfiguracji zapory (CSP)

Możesz wprowadzić dodatkowe zmiany w zakresie zarządzania regułami ruchu przychodzącego i wychodzącego zgodnie z potrzebami dla dozwolonych i zablokowanych punktów końcowych. W miarę zwiększania funkcjonalności bezpiecznej stacji roboczej można zmniejszyć ograniczenie, które odmówi cały ruch przychodzący i wychodzący. Możesz dodać dozwolone Lokacje wychodzące w celu uwzględnienia wspólnych i zaufanych witryn sieci Web. Aby uzyskać więcej informacji, zobacz [usługa konfiguracji zapory](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Domyślnie ograniczone zalecenia:

* Odrzuć wszystkie przychodzące
* Odrzuć wszystkie wychodzące

Projekt Spamhaus zachowuje [listę zablokowanych domen (DWUrzędnych)](https://www.spamhaus.org/dbl/): dobry zasób do użycia jako punkt wyjścia do blokowania lokacji.

### <a name="manage-local-applications"></a>Zarządzanie aplikacjami lokalnymi

Bezpieczna stacja robocza przechodzi do naprawdę zaostrzonego stanu podczas usuwania aplikacji lokalnych, w tym aplikacji biurowych. W tym miejscu możesz dodać program Chrome jako domyślną przeglądarkę i użyć usługi Intune w celu ograniczenia możliwości modyfikowania przeglądarki i jej wtyczek przez użytkownika.

#### <a name="deploy-applications-using-intune"></a>Wdrażanie aplikacji przy użyciu usługi Intune

W niektórych sytuacjach aplikacje takie jak przeglądarka Google Chrome są wymagane na zabezpieczonej stacji roboczej. Poniższy przykład zawiera instrukcje dotyczące instalowania programu Chrome na urządzeniach z **bezpiecznymi stacjami roboczymi**grupy zabezpieczeń.

1. Pobierz pakiet Chrome Instalatora w trybie offline [dla systemu Windows 64-bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Wyodrębnij pliki i zanotuj lokalizację `GoogleChromeStandaloneEnterprise64.msi` pliku.
1. W **Azure Portal** przejdź do **Microsoft Intune** > **aplikacje** > klienckie > aplikacje**Dodaj**.
1. W obszarze **Typ aplikacji**wybierz **pozycję**branżowe.
1. W obszarze **plik pakietu aplikacji**wybierz `GoogleChromeStandaloneEnterprise64.msi` plik z wyodrębnionej lokalizacji i wybierz **przycisk OK**.
1. W obszarze **Informacje o aplikacji**Podaj opis i wydawcę. Kliknij przycisk **OK**.
1. Wybierz pozycję **Dodaj**.
1. Na karcie **przypisania** wybierz pozycję **dostępne dla zarejestrowanych urządzeń** w obszarze **Typ przypisania**.
1. W obszarze **dołączone grupy**Dodaj grupę **bezpiecznych stacji roboczych** .
1. Wybierz przycisk **OK**, a następnie wybierz pozycję **Zapisz**.

Aby uzyskać więcej wskazówek dotyczących konfigurowania ustawień programu Chrome, zobacz [Zarządzanie przeglądarką Chrome przy użyciu Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurowanie portalu firmy dla aplikacji niestandardowych

W trybie zabezpieczonym instalacja aplikacji jest ograniczona do portalu firmy usługi Intune. Jednak zainstalowanie portalu wymaga dostępu do Microsoft Store. W zabezpieczonym rozwiązaniu Portal firmy można udostępnić wszystkim urządzeniom w trybie offline.

Kopia [Portal firmy](https://docs.microsoft.com/Intune/store-apps-company-portal-app) zarządzana przez usługę Intune zapewnia dostęp do dodatkowych narzędzi, które można wypchnąć do użytkowników bezpiecznych stacji roboczych.

Może być konieczne zainstalowanie aplikacji systemu Windows 32-bit lub innych aplikacji, których wdrożenie wymaga specjalnych przygotowań. W takich przypadkach [Narzędzie przygotowywania zawartości Win32 firmy Microsoft](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) może dostarczyć gotowy do użycia `.intunewin` plik formatu na potrzeby instalacji.

### <a name="use-powershell-to-create-custom-settings"></a>Tworzenie ustawień niestandardowych przy użyciu programu PowerShell

Możesz również użyć programu PowerShell, aby zwiększyć możliwości zarządzania hostami. Ten przykładowy skrypt konfiguruje domyślne tło na hoście. Jest to możliwość dostępna również za pomocą Azure Portal i profilów. Przykładowy skrypt służy tylko do zilustrowania funkcjonalności programu PowerShell.

Może być konieczne skonfigurowanie niektórych niestandardowych kontrolek i ustawień na bezpiecznych stacjach roboczych. Ten przykład zmienia tło stacji roboczej za pomocą programu PowerShell w celu łatwego identyfikowania urządzenia jako gotowej do użycia, bezpiecznej stacji roboczej.

Skrypt [SetDesktopBackground. ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) z centrum skryptów firmy Microsoft umożliwia systemowi Windows załadowanie tego [bezpłatnego, ogólnego obrazu tła](https://i.imgur.com/OAJ28zO.png) podczas uruchamiania.

1. Pobierz skrypt na urządzenie lokalne.
1. Zaktualizuj customerXXXX i lokalizację pobierania obrazu tła. W naszym przykładzie zamienimy customerXXXX na tło.  
1. Przejdź > do **Azure Portal** > **Microsoft Intune** > **skrypt**programuPowerShellKonfiguracjaurządzenia. > 
1. Podaj **nazwę** skryptu i określ **lokalizację skryptu**.
1. Wybierz pozycję **Konfiguruj**.
   1. Ustaw opcję **Uruchom ten skrypt przy użyciu poświadczeń zalogowanych** na **wartość tak**.
   1. Kliknij przycisk **OK**.
1. Wybierz pozycję **Utwórz**.
1. Wybierz pozycję **przypisania** > **Wybierz pozycję grupy**.
   1. Dodaj do grupy zabezpieczeń **bezpieczne stacje robocze**.
   1. Wybierz pozycję **Zapisz**.

## <a name="enroll-and-validate-your-first-device"></a>Rejestrowanie i weryfikowanie pierwszego urządzenia

1. Aby zarejestrować urządzenie, potrzebne są następujące informacje:
   * **Numer seryjny** — znaleziono w obudowie urządzenia.
   * **Identyfikator produktu systemu Windows** — znajduje się w obszarze **system** >  **— informacje** z menu Ustawienia systemu Windows.
   * Można uruchomić [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) , aby pobrać plik skrótu CSV ze wszystkimi wymaganymi informacjami na potrzeby rejestracji urządzeń.
   
     Uruchom `Get-WindowsAutoPilotInfo – outputfile device1.csv` , aby uzyskać informacje wyjściowe w postaci pliku CSV, który można zaimportować do usługi Intune.

     > [!NOTE]
     > Skrypt wymaga podniesionych uprawnień. Jest on uruchamiany jako element zdalny ze znakiem. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` Polecenie umożliwia poprawne działanie skryptu.

   * Te informacje można zebrać, logując się do urządzenia z systemem Windows 10 w wersji 1809 lub nowszej. Odsprzedawca sprzętu może również dostarczyć te informacje.
1. W **Azure Portal**przejdź do pozycji **Microsoft Intune** > **Rejestrowanie** > urządzenia**rejestracja** > systemu Windows**urządzenia — zarządzanie urządzeniami z systemem Windows**.
1. Wybierz pozycję **Importuj** i wybierz plik CSV.
1. Dodaj urządzenie do grupy zabezpieczeń **bezpieczne stacje robocze** .
1. Na urządzeniu z systemem Windows 10, które chcesz skonfigurować, przejdź do pozycji >  **Ustawienia systemu Windows** **Aktualizacja & zabezpieczenia** >  **.**
   1. Wybierz pozycję **Rozpocznij** w obszarze **Zresetuj ten komputer**.
   1. Postępuj zgodnie z monitami, aby zresetować i zmienić konfigurację urządzenia przy użyciu skonfigurowanych profilów i zasad zgodności.

Po skonfigurowaniu urządzenia wykonaj przegląd i sprawdź konfigurację. Przed kontynuowaniem wdrażania upewnij się, że pierwsze urządzenie jest skonfigurowane prawidłowo.

## <a name="assign-and-monitor"></a>Przypisywanie i monitorowanie

Aby przypisać urządzenia i użytkowników, należy zmapować [wybrane profile](https://docs.microsoft.com/intune/device-profile-assign) do grupy zabezpieczeń. Wszyscy nowi użytkownicy, którzy wymagają uprawnień do usługi, muszą zostać dodani również do grupy zabezpieczeń.

Profile można monitorować przy użyciu [monitorowania profilu usługi Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Informacje o [usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/index).
