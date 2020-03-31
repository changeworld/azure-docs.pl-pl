---
title: Wdrażanie stacji roboczych zarządzanych przez platformę Azure — usługa Azure Active Directory
description: Dowiedz się, jak wdrożyć bezpieczne, zarządzane przez platformę Azure stacje robocze, aby zmniejszyć ryzyko naruszenia z powodu błędnej konfiguracji lub naruszenia zabezpieczeń.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672601"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Wdrażanie bezpiecznej stacji roboczej zarządzanej przez platformę Azure

Teraz, gdy [znasz bezpieczne stacje robocze,](concept-azure-managed-workstation.md)nadszedł czas, aby rozpocząć proces wdrażania. Dzięki tym wskazówkom można użyć zdefiniowanych profili, aby utworzyć stację roboczą, która jest bezpieczniejsza od samego początku.

![Wdrożenie bezpiecznej stacji roboczej](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Wybierz profil przed wdrożeniem rozwiązania. Można używać wielu profili jednocześnie we wdrożeniu i przypisywać je za pomocą tagów lub grup.

> [!NOTE]
> Zastosuj dowolny profil zgodnie z wymaganiami. Można przenieść do innego profilu, przypisując go w usłudze Microsoft Intune.

| Profil | Małe | Rozszerzone | Wysoka | Wyspecjalizowany | Zabezpieczone | Izolowany |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Użytkownik w usłudze Azure AD | Tak | Tak | Tak | Tak | Tak | Tak |
| Zarządzane przez usługę Intune | Tak | Tak | Tak | Tak | Tak | Tak |
| Urządzenie — zarejestrowana w usłudze Azure AD | Tak |  |  |  |  | |   |
| Urządzenie — przyłączona do usługi Azure AD |   | Tak | Tak | Tak | Tak | Tak |
| Zastosowana linia bazowa zabezpieczeń usługi Intune |   | Tak <br> (Ulepszone) | Tak <br> (Wysoki poziom bezpieczeństwa) | Tak <br> (NCSC) | Tak <br> (Zabezpieczone) | Nie dotyczy |
| Sprzęt spełnia bezpieczne standardy systemu Windows 10 |   | Tak | Tak | Tak | Tak | Tak |
| Microsoft Defender ATP włączony |   | Tak  | Tak | Tak | Tak | Tak |
| Usuwanie praw administratora |   |   | Tak  | Tak | Tak | Tak |
| Wdrażanie przy użyciu programu Microsoft Autopilot |   |   | Tak  | Tak | Tak | Tak |
| Aplikacje zainstalowane tylko przez usługę Intune |   |   |   | Tak | Tak |Tak |
| Adresy URL ograniczone do zatwierdzonej listy |   |   |   | Tak | Tak |Tak |
| Zablokowany Internet (przychodzący/wychodzący) |   |   |   |  |  |Tak |

> [!NOTE]
> W bezpiecznych **urządzeniach prowadzących** do stacji roboczej będą przypisywane profile i zasady. Użytkownicy nie będą mieli zasad stosowanych bezpośrednio do nich, co pozwala na udostępnianie urządzeń (urządzenia udostępnione) w życie. Jeśli bezpieczna stacja robocza nie jest współużytkowana we wdrożeniu lub potrzebne są zasady poszczególnych użytkowników, przypisanie profilów zasad użytkownika można przypisać do użytkownika i urządzenia. 

## <a name="license-requirements"></a>Wymagania licencyjne

Pojęcia opisane w tym przewodniku zakładają, że masz Microsoft 365 Enterprise E5 lub równoważną jednostkę SKU. Niektóre z zaleceń zawartych w tym przewodniku można zaimplementować za pomocą niższych jednostek SKU. Aby uzyskać więcej informacji, zobacz [Licencjonowanie usługi Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Aby zautomatyzować inicjowanie obsługi administracyjnej licencji, należy wziąć pod uwagę [licencjonowanie oparte na grupach](../users-groups-roles/licensing-groups-assign.md) dla użytkowników.

## <a name="azure-active-directory-configuration"></a>Konfiguracja usługi Azure Active Directory

Usługa Azure Active Directory (Azure AD) zarządza użytkownikami, grupami i urządzeniami dla stacji roboczych administratora. Włącz usługi tożsamości i funkcje za pomocą [konta administratora](../users-groups-roles/directory-assign-admin-roles.md).

Podczas tworzenia zabezpieczonego konta administratora stacji roboczej można narazić konto na bieżącą stację roboczą. Upewnij się, że używasz znanego bezpiecznego urządzenia do tej początkowej konfiguracji i całej konfiguracji globalnej. Aby zmniejszyć ekspozycję na atak po raz pierwszy, należy rozważyć przestrzeganie [wskazówek, aby zapobiec infekcjom złośliwym oprogramowaniem](/windows/security/threat-protection/intelligence/prevent-malware-infection).

Wymagaj uwierzytelniania wieloskładnikowego, przynajmniej dla administratorów. Zobacz [Wdrażanie usługi MFA opartej na chmurze, aby](../authentication/howto-mfa-getstarted.md) uzyskać wskazówki dotyczące implementacji.

### <a name="azure-ad-users-and-groups"></a>Użytkownicy i grupy usługi Azure AD

1. Z witryny Azure Portal przejdź do **usługi Azure Active Directory** > **Users** > **New user**.
1. Utwórz administratora urządzenia, wykonując czynności opisane w [samouczku tworzenia użytkownika](/Intune/quickstart-create-user).
1. Wprowadź:

   * **Nazwa** — Administrator bezpiecznej stacji roboczej
   * **Nazwa użytkownika** - `secure-ws-admin@identityitpro.com`
   * **Rola** - katalogu**Ograniczona administrator** i wybierz rolę Administrator **usługi Intune.**

1. Wybierz **pozycję Utwórz**.

Następnie utworzysz dwie grupy: użytkowników stacji roboczych i urządzenia stacji roboczej.

Z witryny Azure Portal przejdź do nowej grupy usługi Azure Active **Directory** > **Groups** > **.**

1. W przypadku grupy użytkownicy stacji roboczych można skonfigurować [licencjonowanie oparte na grupach](../users-groups-roles/licensing-groups-assign.md) w celu automatyzacji inicjowania obsługi administracyjnej licencji użytkownikom.
1. W grupie Użytkownicy stacji roboczej wprowadź:

   * **Typ grupy** — zabezpieczenia
   * **Nazwa grupy** — bezpieczni użytkownicy stacji roboczej
   * **Typ członkostwa** — przypisany

1. Dodaj bezpiecznego użytkownika administratora stacji roboczej:`secure-ws-admin@identityitpro.com`
1. Można dodać innych użytkowników, którzy będą zarządzać bezpiecznymi stacjami roboczymi.
1. Wybierz **pozycję Utwórz**.
1. W przypadku grupy urządzeń stacji roboczych wprowadź:

   * **Typ grupy** — zabezpieczenia
   * **Nazwa grupy** — bezpieczne stacje robocze
   * **Typ członkostwa** — przypisany

1. Wybierz **pozycję Utwórz**.

### <a name="azure-ad-device-configuration"></a>Konfiguracja urządzenia usługi Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Określanie, kto może łączyć urządzenia z usługą Azure AD

Skonfiguruj ustawienia urządzeń w usłudze Active Directory, aby zezwolić grupie zabezpieczeń administracyjnych na łączenie urządzeń z domeną. Aby skonfigurować to ustawienie z witryny Azure portal:

1. Przejdź do ustawień**urządzenia****urządzeń** > usługi Azure **Active Directory** > .
1. Wybierz **pozycję Wybrano** w obszarze **Użytkownicy mogą dołączać urządzenia do usługi Azure AD,** a następnie wybierz grupę "Bezpieczni użytkownicy stacji roboczej".

#### <a name="removal-of-local-admin-rights"></a>Usuwanie lokalnych praw administratora

Ta metoda wymaga, aby użytkownicy stacji roboczych VIP, DevOps i bezpiecznych stacji roboczych nie mieli żadnych praw administratora na swoich komputerach. Aby skonfigurować to ustawienie z witryny Azure portal:

1. Przejdź do ustawień**urządzenia****urządzeń** > usługi Azure **Active Directory** > .
1. Wybierz **pozycję Brak** w obszarze Dodatkowe **administratorzy lokalni na urządzeniach przyłączonych do usługi Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Wymagaj uwierzytelniania wieloskładnikowego do łączenia urządzeń

Aby jeszcze bardziej wzmocnić proces łączenia urządzeń z usługą Azure AD:

1. Przejdź do ustawień**urządzenia****urządzeń** > usługi Azure **Active Directory** > .
1. Wybierz **opcję Tak** w obszarze **Wymagaj wieloskładnikowej auth do łączenia urządzeń**.
1. Wybierz **pozycję Zapisz**.

#### <a name="configure-mobile-device-management"></a>Konfigurowanie zarządzania urządzeniami przenośnymi

Z witryny Azure portal:

1. Przejdź do **usługi Azure Active Directory** > Mobility **(MDM i MAM)** > **Usługi Microsoft Intune**.
1. Zmień ustawienie **zakresu użytkownika MDM** na **Wszystkie**.
1. Wybierz **pozycję Zapisz**.

Te kroki umożliwiają zarządzanie dowolnym urządzeniem za pomocą usługi Intune. Aby uzyskać więcej informacji, zobacz [Szybki start usługi Intune: Konfigurowanie automatycznej rejestracji dla urządzeń z systemem Windows 10](/Intune/quickstart-setup-auto-enrollment). Zasady konfiguracji i zgodności usługi Intune można utworzyć w przyszłym kroku.

#### <a name="azure-ad-conditional-access"></a>Dostęp warunkowy usługi Azure AD

Dostęp warunkowy usługi Azure AD może pomóc ograniczyć uprzywilejowane zadania administracyjne do zgodnych urządzeń. Wstępnie zdefiniowani członkowie grupy **Użytkownicy bezpiecznych stacji roboczych** są zobowiązani do wykonywania uwierzytelniania wieloskładnikowego podczas logowania się do aplikacji w chmurze. Najlepszym rozwiązaniem jest wykluczenie kont dostępu awaryjnego z zasad. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md).

## <a name="intune-configuration"></a>Konfiguracja usługi Intune

### <a name="configure-enrollment-status"></a>Konfigurowanie stanu rejestracji

Ważne jest, aby upewnić się, że bezpieczna stacja robocza jest zaufanym czystym urządzeniem. Kupując nowe urządzenia, możesz nalegać, aby były one fabrycznie ustawione na [Windows 10 Pro w trybie S mode,](/Windows/deployment/Windows-10-pro-in-s-mode)co ogranicza narażenie na luki w zabezpieczeniach podczas zarządzania łańcuchem dostaw. Po otrzymaniu urządzenia od dostawcy możesz użyć autopilota, aby zmienić je w trybie S mode. Poniższe wskazówki zawiera szczegółowe informacje na temat stosowania procesu transformacji.

Aby upewnić się, że urządzenia są w pełni skonfigurowane przed użyciem, usługa Intune umożliwia **blokowanie używania urządzenia do momentu zainstalowania wszystkich aplikacji i profili.**

Z **portalu Azure:**

1. Przejdź do ustawień**domyślnych** > strony rejestracji na urządzeniu **usługi Microsoft Intune** > , ustawienia**domyślne** > **Settings****strony** > **rejestracji systemu** > Windows .
1. Ustaw **pokaż postęp instalacji profilu aplikacji** na **Tak**.
1. Ustaw **blokowanie użycia urządzenia, dopóki wszystkie aplikacje i profile nie zostaną zainstalowane** na **Tak**.

### <a name="create-an-autopilot-deployment-profile"></a>Tworzenie profilu wdrażania rozwiązania Autopilot

Po utworzeniu grupy urządzeń należy utworzyć profil wdrożenia, aby skonfigurować urządzenia Autopilot.

W usłudze Intune w witrynie Azure portal:

1. Wybierz **pozycję Profile** > **wdrażania** > rejestracji**rejestracji** > urządzeń systemu Windows**Utwórz profil .**
1. Wprowadź:

   * Nazwa — **profil wdrożenia bezpiecznej stacji roboczej**.
   * Opis - **Wdrażanie bezpiecznych stacji roboczych**.
   * Ustaw pozycję **Konwertuj wszystkie urządzenia docelowe na rozwiązanie Autopilot** na **Tak**. To ustawienie gwarantuje, że wszystkie urządzenia na liście zostaną zarejestrowane przy użyciu usługi wdrażania rozwiązania Autopilot. Przetwarzanie rejestracji może potrwać do 48 godzin.

1. Wybierz **pozycję Dalej**.

   * W **trybie wdrażania**wybierz opcję **Samorozmieszczanie (wersja zapoznawcza).** Urządzenia z tym profilem są skojarzone z użytkownikiem, który zarejestruje urządzenie. Poświadczenia użytkownika są wymagane do rejestracji urządzenia. Należy pamiętać, że wdrażanie urządzenia w trybie **samodzielnego wdrażania** umożliwia wdrażanie laptopów w modelu udostępnionym. Żadne przypisanie użytkownika nie nastąpi, dopóki urządzenie nie zostanie przypisane do użytkownika po raz pierwszy. W rezultacie wszystkie zasady użytkownika, takie jak Funkcje BitLocker, nie zostaną włączone, dopóki przypisanie użytkownika nie zostanie zakończone. Aby uzyskać więcej informacji na temat logowania się do zabezpieczonego urządzenia, zobacz [wybrane profile](/intune/device-profile-assign).
   * Pole **Dołącz do usługi Azure AD jako** powinno być wyświetlane **przyłączone usługi Azure AD** i być wyszarzone.
   * Wybierz swój język (region), typ konta użytkownika **standardowy**. 

1. Wybierz **pozycję Dalej**.

   * Wybierz znacznik zakresu, jeśli został wstępnie skonfigurowany.

1. Wybierz **pozycję Dalej**.
1. Wybierz **pozycję Przydziały** > **przypisywane do** > **wybranych grup**. W **obszarze Wybierz grupy do uwzględnienia**wybierz pozycję Bezpieczne **stacje robocze**.
1. Wybierz **pozycję Dalej**.
1. Wybierz pozycję **Utwórz**, aby utworzyć profil. Profil wdrażania rozwiązania Autopilot jest teraz dostępny do przypisania do urządzeń.

Rejestracja urządzenia w programie Autopilot zapewnia inne środowisko użytkownika na podstawie typu i roli urządzenia. W naszym przykładzie wdrożenia ilustrujemy model, w którym zabezpieczone urządzenia są wdrażane zbiorczo i mogą być udostępniane, ale gdy jest używane po raz pierwszy, urządzenie jest przypisane do użytkownika. Aby uzyskać więcej informacji, zobacz [Rejestracja urządzenia autopilota usługi Intune](/intune/device-enrollment).

### <a name="configure-windows-update"></a>Konfigurowanie usługi Windows Update

Aktualizowanie systemu Windows 10 jest jedną z najważniejszych rzeczy, które możesz zrobić. Aby utrzymać system Windows w bezpiecznym stanie, należy wdrożyć pierścień aktualizacji, aby zarządzać tempem, w które aktualizacje są stosowane do stacji roboczych. 

W tym wytycznych zaleca się utworzenie nowego pierścienia aktualizacji i zmianę następujących ustawień domyślnych:

W witrynie Azure Portal:

1. Przejdź do **programu Microsoft Intune** > **Software updates** > **Windows 10 Update Rings**.
1. Wprowadź:

   * Nazwa — **aktualizacje stacji roboczych zarządzanej przez platformę Azure**
   * Kanał obsługi - **Niejawny tester systemu Windows - Szybki**
   * Odroczenie aktualizacji jakości (dni) - **3**
   * Okres odroczenia aktualizacji funkcji (dni) - **3**
   * Zachowanie automatycznej aktualizacji - **Automatyczna instalacja i ponowne uruchomienie bez kontroli użytkownika końcowego**
   * Blokowanie użytkownikowi wstrzymywania aktualizacji systemu Windows — **blokowanie**
   * Wymagaj zgody użytkownika, aby ponownie uruchomić poza godzinami pracy - **Wymagane**
   * Zezwalaj użytkownikowi na ponowne uruchomienie (włączone ponowne uruchomienie) - **Wymagane**
   * Przejście użytkowników do włączonego ponownego uruchomienia po automatycznym ponownym uruchomieniu (dni) - **3**
   * Drzemka włączone przypomnienie o ponownym uruchomieniu (dni) - **3**
   * Ustawianie terminu oczekiwania na ponowne uruchomienie (dni) — **3**

1. Wybierz **pozycję Utwórz**.
1. Na karcie **Przydziały** dodaj grupę **Bezpieczne stacje robocze.**

Aby uzyskać więcej informacji na temat zasad usługi Windows Update, zobacz [CSP zasad — Aktualizacja](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integracja z usłudze Windows Defender ATP Intune

Usługi Windows Defender ATP i usługa Microsoft Intune współpracują ze sobą, aby zapobiegać naruszeniom zabezpieczeń. Mogą one również ograniczyć wpływ naruszeń. Funkcje NARZĘDZIA ATP umożliwiają wykrywanie zagrożeń w czasie rzeczywistym, a także umożliwiają szeroko zakrojoną inspekcję i rejestrowanie urządzeń końcowych.

Aby skonfigurować integrację usługi Windows Defender ATP i usługi Intune, przejdź do witryny Azure portal.

1. Przejdź do zgodności usługi Windows**Defender ATP****z usłudze** >  **Microsoft Intune** > .
1. W kroku 1 w obszarze **Konfigurowanie usługi Windows Defender ATP**wybierz pozycję **Połącz usługę Windows Defender ATP z usłudze Microsoft Intune w Centrum zabezpieczeń usługi Windows Defender**.
1. W usłudze Windows Defender Security Center:

   1. Wybierz **pozycję Ustawienia** > **zaawansowane funkcje**.
   1. W przypadku **połączenia usługi Microsoft Intune**wybierz pozycję **On**.
   1. Wybierz pozycję **Zapisz preferencje**.

1. Po nawiązaniu połączenia wróć do usługi Intune i wybierz **pozycję Odśwież** u góry.
1. Ustaw pozycję **Połącz urządzenia systemu Windows w wersji 10.0.15063 lub nowszej z usługą Windows Defender ATP** na **Wł.**
1. Wybierz **pozycję Zapisz**.

Aby uzyskać więcej informacji, zobacz [Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Wykańczanie hartowania profilu stacji roboczej

Aby pomyślnie zakończyć hartowanie rozwiązania, pobierz i wykonaj odpowiedni skrypt. Znajdź linki do pobrania dla żądanego **poziomu profilu:**

| Profil | Lokalizacja pobierania | Pod nazwą |
| --- | --- | --- |
| Niskie bezpieczeństwo | Nie dotyczy | Nie dotyczy |
| Zwiększone zabezpieczenia | https://aka.ms/securedworkstationgit | Ulepszona stacja robocza-Windows10-(1809).ps1 |
| Wysokie bezpieczeństwo | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Wyspecjalizowany | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC — Windows10 (1803) SecurityBaseline.ps1 |
| Specjalistyczna zgodność* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Zabezpieczone | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*Specialized Compliance to skrypt wymuszacy konfigurację Specialized dostępną w ncsc Windows10 SecurityBaseline.

Po pomyślnym wykonaniu skryptu można wprowadzać aktualizacje profilów i zasad w usłudze Intune. Skrypty profilów rozszerzonych i bezpiecznych tworzą zasady i profile, ale należy przypisać te zasady do grupy urządzeń **bezpiecznych stacji roboczych.**

* Tutaj można znaleźć profile konfiguracji urządzenia usługi Intune utworzone przez skrypty:**Profile****konfiguracji** > urządzenia usługi **Azure portal** > **Microsoft Intune** > .
* Tutaj można znaleźć zasady zgodności urządzeń usługi Intune utworzone przez skrypty:**Zasady****zgodności** > urządzeń usługi **Azure portal** > **Microsoft Intune** > .

Aby przejrzeć zmiany wprowadzone przez skrypty, można wyeksportować profile. W ten sposób można określić dodatkowe utwardzanie, które może być wymagane zgodnie z opisem w [dokumentacji SECCON](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Uruchom skrypt `DeviceConfiguration_Export.ps1` eksportu danych usługi Intune z [repozytorium DeviceConfiguration GiuHub,](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) aby wyeksportować wszystkie bieżące profile usługi Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Dodatkowe konfiguracje i utwardzanie do rozważenia

Postępował zgodnie ze wskazówkami w tym miejscu, wdrożono bezpieczną stację roboczą. Należy jednak również wziąć pod uwagę dodatkowe formanty. Przykład:

* ograniczenie dostępu do alternatywnych przeglądarek
* zezwalanie na wychodzący protokół HTTP
* blokowanie wybranych stron internetowych
* ustawianie uprawnień do uruchamiania niestandardowych skryptów programu PowerShell

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Ustawianie reguł u dostawcy usług konfiguracji zapory (CSP)

W razie potrzeby dla dozwolonych i zablokowanych punktów końcowych można wprowadzić dodatkowe zmiany w zarządzaniu regułami przychodzącymi i wychodzącymi. W miarę dalszego wzmacniania bezpiecznej stacji roboczej można poluzować ograniczenie, które odrzuca cały ruch przychodzący i wychodzący. Możesz dodać dozwolone witryny wychodzące, aby zawierały wspólne i zaufane witryny sieci Web. Aby uzyskać więcej informacji, zobacz [Usługa konfiguracji zapory](/Windows/client-management/mdm/firewall-csp).

Restrykcyjne zarządzanie ruchem adresów URL obejmuje:

* Odmów całego ruchu przychodzącego — ustaw w skrypcie profilu zabezpieczonej stacji roboczej.
* Odmów wszystkich wychodzących z wyjątkiem wybranych usług platformy Azure i microsoft, w tym usługi Azure Cloud Shell i możliwość zezwalania na resetowanie hasła platformy Azure.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Jeśli chcesz zapewnić większą szczegółowość reguł blokowania, możesz odwołać się do Projektu Spamhaus, który utrzymuje [listę zablokowanych domen (DBL)](https://www.spamhaus.org/dbl/): dobry zasób do wykorzystania jako zaawansowany zestaw reguł do wdrożenia do blokowania witryn.

### <a name="manage-local-applications"></a>Zarządzanie aplikacjami lokalnymi

Bezpieczna stacja robocza przechodzi do stanu prawdziwie utwardzonego po usunięciu aplikacji lokalnych, w tym aplikacji zwiększających produktywność. W tym miejscu możesz dodać Chrome jako domyślną przeglądarkę i użyć usługi Intune, aby ograniczyć możliwość modyfikowania przeglądarki i jej wtyczek.

#### <a name="deploy-applications-using-intune"></a>Wdrażanie aplikacji przy użyciu usługi Intune

W niektórych sytuacjach aplikacje takie jak przeglądarka Google Chrome są wymagane na zabezpieczonej stacji roboczej. Poniższy przykład zawiera instrukcje dotyczące instalowania Chrome na urządzeniach z grupy zabezpieczeń **Secure Workstations**.

1. Pobierz pakiet Chrome instalatora trybu offline [dla systemu Windows 64-bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Wyodrębnij pliki i zanotuj `GoogleChromeStandaloneEnterprise64.msi` lokalizację pliku.
1. W **portalu Azure** przejdź do**aplikacji klienckich** >  **usługi Microsoft Intune** > **Dodaj****.** > 
1. W obszarze **Typ aplikacji**wybierz pozycję **Linia biznesowa**.
1. W obszarze Plik pakietu `GoogleChromeStandaloneEnterprise64.msi` **aplikacji**wybierz plik z wyodrębniona lokalizacja i wybierz przycisk **OK**.
1. W obszarze **Informacje o aplikacji**podaj opis i wydawcę. Kliknij przycisk **OK**.
1. Wybierz pozycję **Dodaj**.
1. Na karcie **Przydziały** wybierz pozycję **Dostępne dla zarejestrowanych urządzeń** w obszarze Typ **przydziału**.
1. W **obszarze Uwzględnione grupy**dodaj grupę Bezpieczne **stacje robocze.**
1. Wybierz **przycisk OK**, a następnie wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat konfigurowania ustawień Chrome, zobacz [Zarządzanie przeglądarką Chrome za pomocą usługi Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurowanie portalu firmy dla aplikacji niestandardowych

W trybie zabezpieczonym instalacja aplikacji jest ograniczona do portalu firmy usługi Intune. Jednak zainstalowanie portalu wymaga dostępu do sklepu Microsoft Store. W zabezpieczonym rozwiązaniu możesz udostępnić portal firmy wszystkim urządzeniom w trybie offline.

Zarządzana przez usługę Intune kopia [portalu firmy](/Intune/store-apps-company-portal-app) zapewnia dostęp na żądanie do dodatkowych narzędzi, które można przesunąć w dół do użytkowników zabezpieczonych stacji roboczych.

Może być konieczne zainstalowanie aplikacji 32-bitowych systemu Windows lub innych aplikacji, których wdrożenie wymaga specjalnych przygotowań. W takich przypadkach [narzędzie do przygotowywania zawartości microsoft win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) może dostarczyć gotowy do użycia `.intunewin` plik formatu do instalacji.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Dostęp warunkowy zezwala jedynie na dostęp do witryny Azure portal tylko dla zabezpieczonych stacji roboczych

Usługa Azure AD oferuje możliwość zarządzania i ograniczania, kto i co może uzyskać dostęp do portalu zarządzania w chmurze platformy Azure. Włączenie [dostępu warunkowego](../conditional-access/overview.md) zapewni, że tylko bezpieczna stacja robocza może zarządzać zasobami lub zmieniać je. Istotne jest, aby podczas wdrażania tej funkcji, należy wziąć pod uwagę, jeśli funkcja [dostępu awaryjnego](../users-groups-roles/directory-emergency-access.md) może lub powinna być używana tylko w skrajnych przypadkach i konta zarządzanego za pomocą zasad.

> [!NOTE]
> Należy utworzyć grupę użytkowników i dołączyć użytkownika awaryjnego, który może pominąć zasady dostępu warunkowego. W naszym przykładzie mamy grupę zabezpieczeń o nazwie **Emergency BreakGlass**

1. Przejdź do **witryny Azure portal** > **Microsoft Intune** > **Conditional Access — Zasady** > **nowe zasady**.
1. Podaj **nazwę** zasad.
1. Wybieranie **użytkowników i grup** > **Wybierz użytkowników i grupy** 
1. Wybierz **pozycję Uwzględnij** > **role katalogu** > Wybierz role > administratora usługi globalnej, administratora ról uprzywilejowanych, administratora uwierzytelniania z uprawnieniami, administratora zabezpieczeń, administratora zgodności, administratora dostępu warunkowego, administratora aplikacji, administratora aplikacji w chmurze, administratora usługi Intune
1. Wybierz **pozycję Wyklucz** > Wybierz pozycję **Użytkownicy i grupy** > Wybierz wybierz wybranych **wykluczonych użytkowników** > Wybierz grupę **Rozłamu awaryjnego.**
1. Wybierz **aplikacje lub akcje w chmurze** > Wybierz **wszystkie aplikacje w chmurze**
1. Wybierz **warunki** > Wybierz **platformy urządzeń,** > wybierz konfigurację **Tak** > wybierz wybierz platformy **wybierz urządzenia** Wybierz **Windows**
1. Wybierz **pozycję Formanty dostępu** > wybierz pozycję **Przyznaj dostęp** **tak** > wybierz **pozycję Wymagaj, aby urządzenie było oznaczone jako zgodne**. 
1. Wybierz **włącz zasady** > **włączone**
 
Ten zestaw zasad zapewni, że administratorzy muszą używać zgodnego urządzenia z systemem Windows, które jest ustawione przez usługę Intune i WDATP. 

Organizacje powinny również rozważyć blokowanie starszych protokołów uwierzytelniania w swoich środowiskach. Istnieje wiele sposobów wykonania tego zadania, aby uzyskać więcej informacji na temat blokowania starszych protokołów uwierzytelniania, zobacz artykuł [Jak: Blokowanie starszego uwierzytelniania w usłudze Azure AD za pomocą dostępu warunkowego](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Tworzenie ustawień niestandardowych za pomocą programu PowerShell

Program PowerShell można również użyć do rozszerzenia możliwości zarządzania hostami. Ten przykładowy skrypt konfiguruje domyślne tło na hoście. Jest to funkcja, która jest również dostępna za pośrednictwem witryny Azure portal i profile. Przykładowy skrypt służy tylko do zilustrowania funkcji programu PowerShell.

Może być konieczne skonfigurowanie niektórych niestandardowych formantów i ustawień na bezpiecznych stacjach roboczych. W tym przykładzie zmienia tło stacji roboczej przy użyciu możliwości programu Powershell łatwo zidentyfikować urządzenie jako gotowe do użycia, bezpiecznestację roboczą.

Skrypt [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) z Centrum skryptów firmy Microsoft umożliwia systemowi Windows załadowanie tego [bezpłatnego, ogólnego obrazu tła](https://i.imgur.com/OAJ28zO.png) podczas uruchamiania.

1. Pobierz skrypt na urządzenie lokalne.
1. Zaktualizuj customerXXXX i lokalizację pobierania obrazu tła. W naszym przykładzie zastępujemy customerXXXX na tła.
1. Przejdź do **witryny Azure portal** > **Microsoft Intune** > **Device configuration** > **PowerShell scripts** > **Add**.
1. Podaj **nazwę** skryptu i określ **lokalizację skryptu**.
1. Wybierz pozycję **Konfiguruj**.
   1. Ustaw **uruchom ten skrypt przy użyciu zalogowanych poświadczeń na** **Tak**.
   1. Kliknij przycisk **OK**.
1. Wybierz **pozycję Utwórz**.
1. Wybierz **przydziały** > **Wybierz grupy**.
   1. Dodaj grupę zabezpieczeń **Bezpieczne stacje robocze**.
   1. Wybierz **pozycję Zapisz**.

## <a name="enroll-and-validate-your-first-device"></a>Rejestrowanie i sprawdzanie poprawności pierwszego urządzenia

1. Aby zarejestrować urządzenie, potrzebne są następujące informacje:
   * **Numer seryjny** - znaleziony na obudowie urządzenia.
   * **Identyfikator produktu Windows** — znaleziono w obszarze**Informacje o** **systemie** > z menu Ustawienia systemu Windows.
   * Można uruchomić [get-WindowsAutoPilotInfo,](https://aka.ms/Autopilotshell) aby uzyskać plik skrótu CSV ze wszystkimi wymaganymi informacjami do rejestracji urządzenia.
   
     Uruchom, `Get-WindowsAutoPilotInfo – outputfile device1.csv` aby wyprowadzić informacje jako plik CSV, który można zaimportować do usługi Intune.

     > [!NOTE]
     > Skrypt wymaga podwyższonych praw. Działa jako zdalnie podpisane. Polecenie `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` umożliwia poprawne działanie skryptu.

   * Informacje te można zebrać, logując się na urządzeniu z systemem Windows 10 w wersji 1809 lub nowszej. Sprzedawca sprzętu może również podać te informacje.
1. W **witrynie Azure portal**przejdź do**witryny** > Rejestracji urządzeń usługi **Microsoft Intune** > **Urządzenia z systemem** > Windows **— Zarządzanie urządzeniami z systemem Windows Autopilot**.
1. Wybierz **pozycję Importuj** i wybierz plik CSV.
1. Dodaj urządzenie do grupy zabezpieczeń **Bezpieczne stacje robocze.**
1. Na urządzeniu z systemem Windows 10, które chcesz skonfigurować, przejdź do **witryny Windows Settings** > **Update & Security** > **Recovery**.
   1. Wybierz **pozycję Wprowadzenie w** obszarze **Resetowanie tego komputera**.
   1. Postępuj zgodnie z instrukcjami, aby zresetować i ponownie skonfigurować urządzenie ze skonfigurowanym profilem i zasadami zgodności.

Po skonfigurowaniu urządzenia wykonaj przegląd i sprawdź konfigurację. Przed kontynuowaniem wdrażania upewnij się, że pierwsze urządzenie jest poprawnie skonfigurowane.

## <a name="assign-devices"></a>Przypisywanie urządzeń

Aby przypisać urządzenia i użytkowników, należy zamapować [wybrane profile](/intune/device-profile-assign) do grupy zabezpieczeń. Wszyscy nowi użytkownicy, którzy wymagają uprawnień do usługi, muszą również zostać dodana do grupy zabezpieczeń.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Używanie usługi Sentinel i Windows Defender ATP do monitorowania incydentów związanych z zabezpieczeniami i reagowania na nie

Monitorowanie wdrożenia bezpiecznej stacji roboczej można osiągnąć, włączając program [Sentinel] i wykorzystując [zarządzanie zagrożeniami i lukami w zabezpieczeniach](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) Wskazówki nie zapewniają wyczerpującego polowania na zagrożenia, ale zapewniają zdrowy rozsądek w monitorowaniu potencjalnych incydentów związanych z bezpieczeństwem i reagowania na nie.

Firma **Azure Sentinel** użyjemy do: 

* Zbieranie danych z usługi Intune, witryny Azure portal i usługi Azure AD w celu monitorowania użytkowników i urządzeń
* Pomoc w badaniu podejrzanej aktywności konfiguracji użytkownika i urządzenia
* I napęd możliwość badania zabezpieczeń za pomocą WDATP

Monitorowanie wartownika wymaga skonfigurowania łączników do źródeł danych, takich jak usługa Azure AD.

1. W **witrynie Azure portal**przejdź do **usługi Azure Sentinel (Preview)** > Wybierz **dodaj**
1. W obszarze **Wybieranie obszaru roboczego do dodania do usługi Azure Sentinel** Select **Utwórz nowy obszar roboczy**
1. Wprowadź:
   * **Obszar roboczy analizy dzienników** — "Bezpieczne monitorowanie stacji roboczej"
   * **Subskrypcja** — wybierz aktywną subskrypcję
   * **Grupa zasobów** — wybierz ** Utwórz nowy** > bezpieczną stację roboczą RG > **Ok**
   * **Lokalizacja** — wybierz lokalizację, która jest geograficznie najlepiej nadaje się do wdrożenia
   * **Warstwa cenowa** — wybierz **na GB (2018)**
1. Wybierz **ok**.

Następnie połączymy dostępne bezpieczne źródła danych stacji roboczej z monitoringiem.

1. W **witrynie Azure portal**przejdź do **obszaru roboczego Azure Sentinel** > wybierz obszar roboczy Monitorowania **bezpiecznej stacji roboczej**
1. Wybieranie **łączników danych**
1. Wybierz **pozycję Azure Active Directory** > Open Connector Page > Po przejrzeniu warunek wstępny. Przejdź do konfiguracji i wybierz **pozycję Połącz** dla dzienników logowania usługi Azure AD, a także dzienników inspekcji usługi Azure AD.
1. Wybierz **pozycję Aktywność platformy Azure** > strona otwierania łącznika > po przejrzeniu warunek wstępny. Przejdź do konfigurowania dzienników aktywności platformy Azure > wybierz subskrypcję > wybierz **połącz**

Ponieważ dane są zbierane przez sentinel, będziesz mógł obserwować aktywność, wybierając **witrynę Azure portal**, przejdź do **przeglądu usługi Azure Sentinel** 

Użyjemy **usługi Windows Defender ATP (WDATP)** do:

* Ciągłe obserwowanie i monitorowanie luk w zabezpieczeniach i błędnych konfiguracji
* Wykorzystaj WDATP do priorytetowego określania priorytetów dynamicznych zagrożeń w środowisku naturalnym
* Skorelacja luk w zabezpieczeniach z alertami o wykrywaniu i reagowaniu na punkty końcowe (EDR)
* Identyfikowanie luk w zabezpieczeniach na poziomie komputera podczas badania za pomocą pulpitu nawigacyjnego
* Wypychanie korygowania do usługi Intune

Skonfiguruj [pulpit nawigacyjny narzędzia Defender ATP](https://securitycenter.windows.com/machines). Korzystanie ze wskazówek na [temat pulpitu nawigacyjnego zarządzania lukami w zabezpieczeniach threat &](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Monitorowanie aktywności aplikacji przy użyciu programu Microsoft Monitoring Agent (MMA)
Począwszy od specjalistycznej stacji roboczej, blokada aplikacji jest włączona do monitorowania aktywności aplikacji na stacji roboczej. Aby monitorowanie było zintegrowane z obszarem roboczym usługi Log Analytics, należy przestrzegać agenta i konfiguracji MMA. 

> [!NOTE]
> Profil stacji roboczej Specialized zawiera zasady monitorowania zasad ograniczeń oprogramowania. Wdrożenie zasad jest wymagane do monitorowania aktywności aplikacji na kliencie

Wdrażanie agenta programu MMA za pomocą skryptu programu PowerShell usługi Intune

1. Pobierz skrypt konfiguracji [na urządzenie lokalne](https://aka.ms/securedworkstationgit).
1. Aktualizowanie **parametrów, $WorkSpaceID** i **$WorkSpaceKey**
1. Przejdź do **witryny Azure portal** > **Microsoft Intune** > **Device configuration** > **PowerShell scripts** > **Add**.
1. Podaj **nazwę** skryptu i określ **lokalizację skryptu**.
1. Wybierz pozycję **Konfiguruj**.
   1. Ustaw **uruchom ten skrypt przy użyciu zalogowanych poświadczeń na** **Tak**.
   1. Kliknij przycisk **OK**.
1. Wybierz **pozycję Utwórz**.
1. Wybierz **przydziały** > **Wybierz grupy**.
   1. Dodaj grupę zabezpieczeń **Bezpieczne stacje robocze**.
   1. Wybierz **pozycję Zapisz**.

Następnie musisz skonfigurować log analytics, aby otrzymywać nowe dzienniki
1. W **witrynie Azure portal**przejdź do **obszaru roboczego usługi Log Analytics** > Select — "Bezpieczne monitorowanie stacji roboczej"
1. Wybieranie **ustawień** > zaawansowanych**Dzienniki zdarzeń systemu Windows** **dane** > 
1. W **zbieraj zdarzenia z następujących dzienników zdarzeń** 
1. Wprowadź:
   * "Microsoft-Windows-AppLocker/EXE i DLL" > Odznacz **informacyjny**
   * "Microsoft-Windows-AppLocker/MSI i skrypt" > odznaczenie **informacji**
   * "Microsoft-Windows-AppLocker/Packaged app-Deployment" > Odznacz **informacje**
   * 'Microsoft-Windows-AppLocker/Packaged app-Execution" > Odznacz **informacyjny**
1. Wybierz pozycję **Zapisz**.

Rejestrowanie aplikacji będzie dostępne w wybranym obszarze roboczym usługi Log Analytics.

## <a name="monitoring"></a>Monitorowanie

* Dowiedz się, jak [wykrywać zagrożenia za pomocą usługi Azure Sentinel](/azure/sentinel/tutorial-detect-threats)
* [Badanie incydentów za pomocą usługi Azure Sentinel](/azure/sentinel/tutorial-investigate-cases)
* [Konfigurowanie automatycznych odpowiedzi na zagrożenia w usłudze Azure Sentinel](/azure/sentinel/tutorial-respond-threats-playbook)
* Dowiedz się, jak przejrzeć [wynik ekspozycji](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Przejrzyj [zalecenie dotyczące zabezpieczeń](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Zarządzanie [korygowaniami](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) zabezpieczeń
* Zarządzanie [wykrywaniem i reagowaniem punktów końcowych](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Monitoruj profile za pomocą [monitorowania profilu usługi Intune](/intune/device-profile-monitor).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [usłudze Microsoft Intune](/intune/index).
* Poznaj [usługę Azure AD](../index.yml).
* Praca z [zaawansowaną ochroną przed zagrożeniami w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Odkryj [wartownika platformy Azure](/azure/sentinel/)
