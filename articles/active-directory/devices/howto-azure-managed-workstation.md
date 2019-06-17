---
title: Wdrażanie usługi Azure managed stacje robocze — usługi Azure Active Directory
description: Dowiedz się, jak wdrożyć bezpieczne usługi Azure managed stacjach roboczych zmniejszyć ryzyko naruszenia zabezpieczeń z powodu błędnej konfiguracji lub kompromisowych rozwiązań
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
ms.openlocfilehash: ae7c823b9aea262556081354a108ac9509a284ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110682"
---
# <a name="deploy-a-secure-workstation"></a>Wdrażanie bezpiecznej stacji roboczej

Teraz, gdy już rozumiesz [Dlaczego ważne jest zabezpieczanie dostępu do stacji roboczej?](concept-azure-managed-workstation.md) nadszedł czas, aby rozpocząć proces wdrożenia przy użyciu dostępnych narzędzi. Niniejsze wskazówki użyje zdefiniowane profile do tworzenia stacji roboczej, która zapewnia większe bezpieczeństwo od samego początku.

![Wdrażanie bezpiecznej stacji roboczej](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Przed wdrożeniem rozwiązania, należy wybrać profil, który będzie używany. Należy pamiętać, że możesz wykorzystać wybranych profilów i przenieść do innego, przypisywanie profilu w usłudze Intune, zgodnie z wymaganiami. Wiele profilów można jednocześnie używany we wdrożeniu i przypisać przy użyciu przydziałów znacznika.

| Profil | Małe | Rozszerzone | Wysoka | Wyspecjalizowany | Zabezpieczone | Izolowane |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Użytkownik w usłudze Azure AD | Tak | Yes | Yes | Yes | Yes | Yes |
| Zarządzane przez usługę Intune | Tak | Yes | Yes | Yes | Yes | Yes |
| Urządzenia zarejestrowane w usłudze Azure AD | Tak |  |  |  |  | |   |
| Urządzenia przyłączone do usługi Azure AD |   | Tak | Yes | Yes | Yes | Tak |
| Usługa Intune linii bazowej zabezpieczeń stosowane |   | Yes <br> (Rozszerzony) | Tak <br> (HighSecurity) | Tak <br> (NCSC) | Tak <br> (Zabezpieczone) |  Nie dotyczy |
| Sprzęt spełnia bezpiecznego systemu Windows 10 i standardy |   | Tak | Yes | Yes | Yes | Tak |
| Włączony program Microsoft Defender ATP |   | Tak  | Yes | Yes | Yes | Tak |
| Usuwanie uprawnień administratora |   |   | Yes  | Yes | Yes | Yes |
| Wdrażanie przy użyciu Microsoft Autopilot |   |   | Tak  | Yes | Yes | Tak |
| Aplikacje zainstalowane tylko przez usługę Intune |   |   |   | Yes | Yes |Tak |
| Ograniczone do listy zatwierdzonych tylko adresów URL |   |   |   | Tak | Yes |Tak |
| Internet (ruchu przychodzącego/wychodzącego zablokowane) |   |   |   |  |  |Tak |

## <a name="license-requirements"></a>Wymagania licencyjne

Pojęcia opisane w tym przewodniku zakłada Microsoft 365 Enterprise E5 lub równoważne jednostki SKU. Niektóre zalecenia przedstawione w tym przewodniku można zaimplementować przy użyciu niższe jednostki SKU. Dodatkowe informacje można znaleźć na [Licencjonowanie rozwiązania Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Chcesz skonfigurować [Licencjonowanie na podstawie grupy](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) dla użytkowników zautomatyzować aprowizację licencji.

## <a name="azure-active-directory-configuration"></a>Konfiguracja usługi Active Directory systemu Azure

Konfigurowanie katalogu usługi Azure Active Directory (Azure AD), które będą zarządzać użytkownikami, grup i urządzeń dla stacji roboczych użytkownika administratora wymaga włączenia usługi zarządzania tożsamościami i funkcji przy użyciu [konta administratora](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Podczas tworzenia konta administratora zabezpieczonej stacji roboczej wyświetlasz konta na bieżącej stacji roboczej. Zalecane jest, czy ten wstępną konfigurację i wszystkie globalnej konfiguracji ze znanych, bezpiecznych urządzeń. Można rozważyć wskazówki dotyczące [zapobiegać infekcjom złośliwego oprogramowania](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection) pozwala zredukować ryzyko ujawnienia pierwsze doświadczenie po raz pierwszy od ataku.

Organizacje powinny wymagać stosowania uwierzytelniania wieloskładnikowego, przynajmniej dla administratorów. Zobacz [wdrażanie oparte na chmurze usługa MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) uzyskać wytyczne dotyczące implementacji.

### <a name="azure-ad-users-and-groups"></a>Użytkownicy usługi Azure AD i grup

W witrynie Azure portal, przejdź do **usługi Azure Active Directory** > **użytkowników** > **nowego użytkownika**. [Tworzenie użytkownika zabezpieczanie stacji roboczej](https://docs.microsoft.com/Intune/quickstart-create-user), kto będzie administratorem urządzenia.

* **Nazwa** — zabezpieczanie stacji roboczej administratora
* **Nazwa użytkownika** - secure-ws-admin@identityitpro.com
* **Rola katalogu** - **ograniczony administrator** i wybierz następującą rolę administracyjne
   * **Administrator usługi Intune**
* **Tworzenie**

Utworzymy dwie grupy: jeden dla użytkowników z stacje robocze i jeden dla stacji roboczych, samodzielnie. W witrynie Azure portal, przejdź do **usługi Azure Active Directory** > **grup** > **nowej grupy**

Pierwsza grupa użytkowników stacji roboczej. Chcesz skonfigurować [Licencjonowanie na podstawie grupy](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) dla użytkowników w tej grupie, aby zautomatyzować aprowizację licencji do użytkowników.

* **Typ grupy** — zabezpieczenia
* **Nazwa grupy** — zabezpieczanie użytkownicy stacji roboczej
* **Typ członkostwa** — przypisane
* Dodaj użytkownika administratora bezpiecznego stacji roboczej do grupy
   * secure-ws-admin@identityitpro.com
* Można dodać wszystkich użytkowników, bezpieczne stacji roboczych do grupy zarządzania
* **Tworzenie**

Druga grupa dla urządzeń stacji roboczej.

* **Typ grupy** — zabezpieczenia
* **Nazwa grupy** — zabezpieczanie stacji roboczych
* **Typ członkostwa** — przypisane
* **Tworzenie**

### <a name="azure-ad-device-configuration"></a>Konfiguracja urządzenia w usłudze Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Określ, kto może dołączać urządzenia do usługi Azure AD

Konfigurowanie urządzeń ustawienie w usłudze Active Directory, aby zezwolić na grupy zabezpieczeń administracyjnych przyłączać urządzenia do Twojej domeny. Aby skonfigurować to ustawienie w witrynie Azure portal, przejdź do **usługi Azure Active Directory** > **urządzeń** > **ustawienia urządzenia**. Wybierz **wybrane** w obszarze **użytkownicy mogą dołączać urządzania do usługi Azure AD** i wybierz grupę "Użytkownicy stacji roboczej zabezpieczenia".

#### <a name="removal-of-local-admin-rights"></a>Usunięcie prawa administratora lokalnego

Jako część wdrożenia użytkownicy stacje robocze, adresów VIP, metodyki DevOps i bezpieczne stacji roboczych z poziomu mają nie prawa administratora na swoich komputerach. Aby skonfigurować to ustawienie w witrynie Azure portal, przejdź do **usługi Azure Active Directory** > **urządzeń** > **ustawienia urządzenia**. Wybierz **Brak** w obszarze **Dodatkowi administratorzy lokalni w usłudze Azure AD urządzenia przyłączone do**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Wymagaj uwierzytelniania wieloskładnikowego do dołączania urządzeń

Dalsze wzmacnianie procesu przyłączania urządzenia do usługi Azure AD, przejdź do **usługi Azure Active Directory** > **urządzeń** > **ustawienia urządzenia** Wybierz **tak** w obszarze **Wymagaj uwierzytelniania wieloskładnikowego do dołączania urządzeń** wybierz **Zapisz**.

#### <a name="configure-mdm"></a>Konfigurowanie zarządzania urządzeniami Przenośnymi

W witrynie Azure portal, przejdź do **usługi Azure Active Directory** > **mobilność (Zarządzanie urządzeniami Przenośnymi i zarządzania aplikacjami Mobilnymi)**  > **Microsoft Intune**. Zmień ustawienie **zakres użytkownika oprogramowania MDM** do **wszystkich** i wybierz polecenie **Zapisz** dopuszcza możemy dowolnego urządzenia, które mają być zarządzane przez usługę Intune, w tym scenariuszu. Więcej informacji można znaleźć w artykule [szybkiego startu usługi Intune: Konfigurowanie automatycznej rejestracji urządzeń z systemem Windows 10](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Zasady konfiguracji i zgodności usługi Intune zostanie utworzona w przyszłym kroku.

#### <a name="azure-ad-conditional-access"></a>Dostęp warunkowy Azure AD

Azure AD warunkowego dostępu może pomóc zachować uprzywilejowanych wykonywania zadań administracyjnych na zgodnych urządzeniach. Użytkownicy, gdy zdefiniowaliśmy jako elementy członkowskie **Secure użytkownicy stacji roboczej** grupy będą musieli wykonywać uwierzytelnianie wieloskładnikowe podczas logowania do aplikacji w chmurze. Firma Microsoft postępuj zgodnie z najlepszych rozwiązań i wykluczyć naszych kont dostępu awaryjnego. Dodatkowe informacje można znaleźć w artykule [zarządzania kont dostępu awaryjnego w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

Aby skonfigurować dostęp warunkowy w witrynie Azure portal, przejdź do **usługi Azure Active Directory** > **dostępu warunkowego** > **nowe zasady**.

* **Nazwa** — bezpieczne urządzenie wymagane zasady
* Przypisania
   * **Użytkownicy i grupy**
      * Obejmują - **użytkowników i grup** — wybierz tę opcję **Secure użytkownicy stacji roboczej** utworzoną wcześniej grupę
      * Wyklucz - **użytkowników i grup** — Wybieranie kont dostępu awaryjnego Twojej organizacji
   * **Aplikacje w chmurze**
      * Obejmują - **wszystkie aplikacje w chmurze**
* Kontrola dostępu
   * **Udziel** — wybierz tę opcję **udzielić dostępu** przycisku radiowego
      * **Wymagaj uwierzytelniania wieloskładnikowego**
      * **Wymagaj, aby urządzenie było oznaczone jako zgodne**
      * W przypadku wielu kontrolek — **Wymagaj wszystkich wybranych kontrolek**
* Włącz zasady — **na**

Organizacje, można opcjonalnie utworzyć zasady krajach bloku, w których użytkownicy nie będzie dostępu do zasobów firmy. Więcej informacji na temat zasad dostępu warunkowego na podstawie lokalizacji IP można znaleźć w artykule [co to jest warunek lokalizacji w usłudze Azure Active Directory dostępu warunkowego?](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)

## <a name="intune-configuration"></a>Konfiguracja usługi Intune

### <a name="configure-enrollment-status"></a>Skonfiguruj stan rejestracji

Zgodnie z opisem w zarządzania łańcuchem dostaw, zapewnia bezpieczne stacji roboczej jest zaufany czystym urządzeniu zalecamy, aby przy zakupie nowych urządzeń, urządzenia być fabryki równa [Windows 10 Pro w trybie S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), co ogranicza narażenia na działanie i luk w zabezpieczeniach podczas zarządzania łańcuchem dostaw. Gdy urządzenie zostanie odebrany od dostawcy, urządzenie zostanie usunięte z trybu S za pomocą rozwiązania Autopilot. Poniższe wskazówki zawiera szczegółowe informacje na temat stosowania proces przekształcania.

Chcemy upewnić się, że urządzenia są w pełni skonfigurowane przed użyciem. Usługa Intune udostępnia środki do **zablokować użycie urządzenia, dopóki nie zostaną zainstalowane wszystkie aplikacje i profile**. 

1. Z **witryny Azure portal** przejdź do:
1. **Microsoft Intune** > **rejestracji urządzeń** > **rejestracji Windows** > **Strona stanu rejestracji(wersjazapoznawcza)**  >  **Domyślne** > **ustawienia**.
1. Ustaw **wyświetlany postęp instalacji profilu aplikacji** do **tak**.
1. Ustaw **zablokować użycie urządzenia, dopóki nie zostaną zainstalowane wszystkie aplikacje i profile** do **tak**.

### <a name="create-an-autopilot-deployment-profile"></a>Tworzenie profilu wdrażania rozwiązania Autopilot

Po utworzeniu grupy urządzeń, należy utworzyć profil wdrażania, dzięki czemu można skonfigurować urządzenia rozwiązania Autopilot.

1. W usłudze Intune w witrynie Azure portal, wybierz **rejestracji urządzeń** > **rejestracji Windows** > **profile wdrażania**  >   **Utwórz profil**.
1. Dla nazwy wprowadź **Secure profilu wdrażania stacji roboczej**. Aby uzyskać opis, wpisz **wdrożenia z stacje robocze bezpiecznego**.
1. Zestaw przekonwertować wszystkie objęte nimi urządzenia rozwiązania Autopilot Yes (tak). To ustawienie gwarantuje, że wszystkie urządzenia na liście Zarejestruj się przy użyciu usługi wdrażania rozwiązania Autopilot.  Zezwalaj na rejestrację, które mają być przetwarzane w ciągu 48 godzin.
1. Tryb wdrożenia, można wybrać **własnym wdrażania (wersja zapoznawcza)** . Urządzenia z tym profilem są skojarzone z użytkownikiem, rejestrowanie samego urządzenia. Poświadczenia użytkownika są wymagane do zarejestrowania urządzenia.
1. Sprzężenia do usługi Azure AD jako pole **przyłączony Azure AD** powinien być wybrana i wyszarzone.
1. Wybierz środowisko Out-of-box (OOBE), skonfigurować opcja i pozostaw inne wartość domyślną, a następnie wybierz **Ok**:
   1. Typ konta dla użytkownika: **Standardowa**
1. Wybierz pozycję **Utwórz**, aby utworzyć profil. Profil wdrażania rozwiązania Autopilot jest teraz dostępny do przypisania do urządzenia.
1. Wybierz **przypisania** > **przypisać** > **wybrane grupy**
   1. **Wybierz grupy do uwzględnienia** — zabezpieczanie użytkownicy stacji roboczej

### <a name="configure-windows-update"></a>Konfigurowanie Windows Update

Jedną z najważniejszych czynności, które organizacja może wykonać jest aktualne systemu Windows 10. W celu obsługi systemu Windows 10 w stanie bezpiecznym, wdrożymy pierścień aktualizacji, aby zarządzać tempie, w którym aktualizacje są stosowane do stacji roboczych. Tej konfiguracji można znaleźć w **witryny Azure portal** > **Microsoft Intune** > **aktualizacji oprogramowania**  >  **Pierścienie aktualizacji systemu Windows 10**.

Firma Microsoft będzie **Utwórz** nowy pierścień aktualizacji z następującymi ustawieniami zmieniła się z wartości domyślne.

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

Kliknij przycisk **Utwórz** następnie na **przypisania** Dodaj kartę **bezpieczny z stacje robocze** grupy jako dołączonej grupy.

Dodatkowe informacje na temat zasady usługi Windows Update można znaleźć w [zasad dostawcy usług Kryptograficznych — aktualizacja](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>Integracja z systemem Windows Defender ATP w usłudze Intune

Windows Defender ATP Microsoft Intune współpracują ze sobą w celu zapobiegania naruszeniom zabezpieczeń i ograniczyć wpływ naruszeń. Funkcje zapewni wykrywania w czasie rzeczywistym. Zaawansowanej ochrony przed zagrożeniami udostępni również naszych wdrożenia rozbudowane inspekcji i rejestrowania urządzeń z punktu końcowego.

Aby skonfigurować integrację Windows Defender ATP w usłudze Intune w witrynie Azure portal, przejdź do **Microsoft Intune** > **zgodność urządzenia** > **usługi Windows Defender ATP** .

1. W kroku 1 w sekcji **konfigurowania usługi Windows Defender ATP**, kliknij przycisk **połączyć usługi Windows Defender ATP usługą Microsoft Intune w usłudze Windows Defender Security Center**.
1. W usłudze Windows Defender Security Center:
   1. Wybierz **ustawienia** > **zaawansowane funkcje**
   1. Aby uzyskać **połączenia Microsoft Intune**, wybierz **na**
   1. Wybierz **Zapisz preferencje**
1. Po nawiązaniu połączenia zwróć się do usługi Intune i kliknij przycisk **Odśwież** u góry.
1. Ustaw **Windows Połącz urządzenia, wersja 10.0.15063 lub nowszym do usługi Windows Defender ATP** do **na**.
1. **Zapisz**

Dodatkowe informacje można znaleźć w artykule [zaawansowanej ochrony przed zagrożeniami programu Windows Defender](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="completing-hardening-of-the-workstation-profile"></a>Kończenie zaostrzanie poziomu zabezpieczeń stacji roboczej profilu

Do pomyślnego ukończenia ograniczania funkcjonalności rozwiązania, należy pobrać i uruchomienia skryptu, w oparciu o żądane **profilu poziom** z poniższej tabeli.

| Profil | Lokalizacja pobierania | Nazwa pliku |
| --- | --- | --- |
| Niski poziom zabezpieczeń | ND |  ND |
| Większe bezpieczeństwo | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Wysoki poziom zabezpieczeń  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Wyspecjalizowany | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Wyspecjalizowane zgodności * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Zabezpieczone | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

Wyspecjalizowane zgodności * to skrypt, który wymusza podana w NCSC Windows10 SecurityBaseline Konfiguracja specjalne.

Po zaznaczonego skryptu zostało wykonane pomyślnie, można nawiązać aktualizacji zasad i profilów w programie Microsoft Intune. Skrypty do profilów rozszerzony i bezpiecznego Tworzenie zasad i profilów można, ale możesz przypisać zasady aby Twoje **bezpieczny z stacje robocze** grupy.

* Profile konfiguracji urządzeń usługi Intune utworzone przez skrypty można znaleźć w **witryny Azure portal** > **Microsoft Intune** > **konfiguracji urządzenia**  >  **Profile**.
* Zasady zgodności urządzeń usługi Intune utworzone przez skrypty można znaleźć w **witryny Azure portal** > **Microsoft Intune** > **zgodność urządzenia**  >  **Zasady**.

Dokonanie przeglądu zmian można również wyeksportować profile i stosowania zmian do pliku eksportu, zgodnie z opisem w SECCON dokumentacji oparty na oraz dodatkowe ograniczenie funkcjonalności, która jest wymagana.

Uruchamianie danych usługi Intune eksportowania skryptu `DeviceConfiguration_Export.ps1` z [repozytorium GiuHub Konfiguracja urządzenia](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) zapewni bieżącego eksportu wszystkich istniejących profilów usługi Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Dodatkowe konfiguracje i zaostrzanie poziomu zabezpieczeń, które należy rozważyć

Ze wskazówkami podanymi włączył zabezpieczonej stacji roboczej, dodatkowe elementy sterujące należy również rozważyć, takich jak dostęp do alternatywnego przeglądarek, wychodzących HTTP dozwolonych i zablokowanych witryn sieci Web i możliwość uruchamiania niestandardowego skryptu programu PowerShell.

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>Restrykcyjne zasady ruchu przychodzącego i wychodzącego w dostawcy usługi konfiguracji zapory (CSP)

Dodatkowe Zarządzanie reguły ruchu przychodzącego i wychodzącego może zostać zaktualizowana w celu uwzględnienia punktów końcowych dozwolonych i zablokowanych. Kontynuując wzmacniania zabezpieczeń stacji roboczej bezpieczny, firma Microsoft Przesuń ograniczenie Odmów wszystkie przychodzące i wychodzące jako domyślny, a dodanie witryn dozwolonych dla ruchu wychodzącego odzwierciedlić typowe i zaufanych witryn sieci web. Dodatkowe informacje o konfiguracji dostawcy usług konfiguracji zapory można znaleźć w artykule [zapory dostawcy usług Kryptograficznych](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Domyślne ograniczone zalecenia są:

* Odmów wszystkie przychodzące
* Odmów wszystkie wychodzące

Projekt Spamhaus prowadzi listę dobre, której organizacje mogą używać jako punktu wyjścia dla blokowanie witryn, znane jako [listy bloku ((podwójna, domeny) kartka)](https://www.spamhaus.org/dbl/).

### <a name="managing-local-applications"></a>Zarządzanie aplikacjami lokalne

Usuwanie lokalnych aplikacji, w tym usuwania aplikacji biurowych zostanie przesunięty w bezpiecznej stacji roboczej do stanu naprawdę ze wzmocnionymi zabezpieczeniami. W tym przykładzie firma Microsoft będzie dodać Chrome jako domyślnej przeglądarki i ograniczyć możliwość modyfikowania przeglądarce, w tym wtyczki za pomocą usługi Intune.

#### <a name="deploy-applications-using-intune"></a>Wdrażanie aplikacji przy użyciu usługi Intune

W niektórych sytuacjach na zabezpieczonej stacji roboczej są wymagane aplikacje, takie jak przeglądarki Google Chrome. W poniższym przykładzie przedstawiono instrukcje dotyczące instalacji dla programu Chrome na urządzeniach w grupie zabezpieczeń **bezpieczny z stacje robocze** utworzone wcześniej.

1. Pobierz Instalatora w trybie offline [pakietu dla programu Chrome dla Windows 64‑bit](https://cloud.google.com/chrome-enterprise/browser/download/)
1. Wyodrębnij pliki i zanotuj lokalizację `GoogleChromeStandaloneEnterprise64.msi` do zainstalowania przy użyciu usługi Intune
1. W **witryny Azure portal** wskaż **Microsoft Intune** > **aplikacje klienckie** > **aplikacje**  >  **Dodaj**
1. W obszarze **typ aplikacji**, wybierz **Line-of-business**
1. W obszarze **plik pakietu aplikacji**, wybierz opcję `GoogleChromeStandaloneEnterprise64.msi` wyodrębnione lokalizacji, a następnie kliknij **OK**
1. W obszarze **informacje o aplikacji**, podaj opis i wydawcy i wybierz pozycję **OK**
1. Kliknij przycisk **Dodaj**
1. Na **przypisania** wybierz **dostępne dla zarejestrowanych urządzeń** w obszarze **typ przypisania**
1. W obszarze **objęte grupy**, Dodaj **bezpieczny z stacje robocze** utworzoną wcześniej grupę
1. Kliknij przycisk **OK** następnie **Zapisz**

Dodatkowe wskazówki na temat konfigurowania ustawień dla programu Chrome można znaleźć w artykule pomocy technicznej, ich [Zarządzanie przeglądarki Chrome w usłudze Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurowanie aplikacji portal firmy dla aplikacji niestandardowych

W trybie zabezpieczonym instalowania aplikacji będzie ograniczony do aplikacji portal firmy usługi Intune. Jednakże instalując aplikację portal wymaga dostępu do Microsoft Store. W naszych zabezpieczonych rozwiązaniu podejmiemy portalu dostępny dla wszystkich urządzeń, w trybie offline w portalu firmy.

Instalowanie usługi Intune zarządzanych kopii [aplikacji Portal firmy](https://docs.microsoft.com/Intune/store-apps-company-portal-app) pozwoli na możliwość wypchnięta dodatkowe narzędzia na żądanie dla użytkowników zabezpieczonej stacjach roboczych.

W niektórych organizacjach może wymagane do zainstalowania aplikacji Windows 32-bitowych lub aplikacje, które wymagają inne przygotowania do wdrożenia. W przypadku tych aplikacji [narzędzie przygotowywania zawartości win32 Microsoft](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) zapewnia gotową do użycia `.intunewin` pliku formatu do instalacji.

### <a name="setting-up-custom-settings-using-powershell"></a>Konfigurowanie ustawień niestandardowych przy użyciu programu PowerShell

Również użyjemy przykładem rozszerzalność w zarządzaniu hosta przy użyciu programu PowerShell. Skrypt zostanie skonfigurowany domyślne tło na hoście. Ta funkcja jest również dostępna w profilach i używać tylko w celu przedstawienia możliwości.

W rozwiązaniu może być, aby skonfigurować niektóre formanty niestandardowe i ustawienia na stacjach roboczych bezpieczne. W naszym przykładzie zmienimy tło stacji roboczej przy użyciu programu Powershell, aby można było łatwo zidentyfikować urządzenie jako bezpieczne stacji roboczej, gotowy do użycia. Gdy w naszym przykładzie będzie wykonania tego zadania przy użyciu programu PowerShell, również może zostać ukończona w witrynie Azure portal.

Naszym przykładzie zostanie użyte następujące [obraz tła ogólnego bezpłatne](https://i.imgur.com/OAJ28zO.png) i [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) z Microsoft Center Scripting umożliwia Windows załadować tła w menu start.

1. Pobierz skrypt do urządzenia lokalnego
1. Zaktualizuj customerXXXX i lokalizacji pobierania tła, których chcesz użyć w skrypcie w celu odzwierciedlenia tła pliku i folderu, który chcesz wdrożenia do użycia. W tym przykładzie firma Microsoft Zastąp customerXXXX do tła.  
1. Przejdź do **witryny Azure portal** > **Microsoft Intune** > **konfiguracji urządzenia** > **programu PowerShell skrypty** > **Dodaj**
1. Podaj **nazwa** skryptu i określ **lokalizację skryptu** gdzie został pobrany
1. Wybierz **skonfigurować**
   1. Ustaw **Uruchom ten skrypt, używając poświadczeń zalogowanego**, **tak**
   1. Kliknij przycisk **OK**.
1. Kliknij przycisk **Utwórz**.
1. Wybierz **przypisania** > **wybierz grupy**
   1. Dodaj grupę zabezpieczeń **bezpieczny z stacje robocze** utworzony wcześniej, a następnie kliknij przycisk **Zapisz**

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>Korzystanie z wersji zapoznawczej: Linii bazowej zabezpieczeń zarządzania urządzeniami Przenośnymi na października 2018 r.

Microsoft Intune wprowadziła funkcję Zarządzanie linii bazowej zabezpieczeń zapewnienie administratorom prosty sposób wymusić wspólne ogólny poziom zabezpieczeń linii bazowej. Linia bazowa zapewnia podobne oznacza, że do osiągnięcia zablokowanej stacji roboczej profilu rozszerzony w dół.

Dla bezpiecznych stacji roboczej implementacja tego punktu odniesienia nie jest używana w postaci, w jakiej spowoduje konflikt z wdrożenia konfigurację zabezpieczeń.

|   |   |   |
| :---: | :---: | :---: |
| Blokadzie | Instalacja urządzenia | Usługi pulpitu zdalnego |
| Środowisko uruchomieniowe aplikacji | Zablokuj urządzenie | Zdalne zarządzanie |
| Zarządzanie aplikacjami | Usługa Dziennik zdarzeń | Zdalnego wywołania procedury |
| Automatyczne odtwarzanie | Środowisko | Wyszukiwanie |
| BitLocker | Exploit Guard | SmartScreen |
| Przeglądarka | Eksplorator plików | Wymagania systemowe|
| Łączność | Internet Explorer | Wi-Fi |
| Delegowanie poświadczeń | Opcje zabezpieczeń zasad lokalnych | Menedżer połączeń Windows |
| Poświadczenia interfejsu użytkownika | Przewodnik po zabezpieczeniach MS | Windows Defender|
| Ochrona danych | MSS Legacy | Obszar roboczy pisma odręcznego Windows |
| Device Guard | Zasilania | Windows PowerShell |

Więcej informacji na temat tej funkcji wersji zapoznawczej można znaleźć w artykule [ustawienia linii bazowej zabezpieczeń Windows Intune](https://docs.microsoft.com/Intune/security-baseline-settings-windows).

## <a name="enroll-and-validate-your-first-device"></a>Rejestrowanie i zweryfikować pierwszego urządzenia

1. Aby zarejestrować urządzenie, potrzebne są następujące informacje:
   * **Numer seryjny** — na obudowę urządzenia
   * **Identyfikator produktu Windows** — znaleziono w obszarze **systemu** > **o** z poziomu menu ustawień Windows.
   * Uruchamianie [Get WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) zapewni wyznaczania wartości skrótu pliku CSV do zarejestrowania urządzenia w przypadku wszystkich wymaganych informacji. 
      * Uruchom `Get-WindowsAutoPilotInfo – outputfile device1.csv` w danych wyjściowych informacji jako plik CSV, który można zaimportować w usłudze Intune.

   > [!NOTE]
   > Skrypt będzie wymagać podwyższonym poziomem uprawnień i Uruchom jako zdalny podpisany. Można użyć następującego polecenia skryptów, by działała poprawnie. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  Te informacje można zebrać, logując się do systemu Windows 10 w wersji 1809 lub nowszej urządzeń w celu zebrania informacji lub odsprzedawcy sprzętu mogą dostarczyć te informacje, podczas porządkowania nowych urządzeń.
1. Zbierz informacje o wymaganych i wrócić do **witryny Azure portal**. Przejdź do **Microsoft Intune** > **rejestracji urządzeń** > **rejestracji Windows** > **urządzeń — Zarządzanie urządzeniami z rozwiązaniem Windows Autopilot**, wybierz opcję **importu**i wybierz plik CSV utworzonym lub podano.
1. Dodaj teraz zarejestrowane urządzenie do grupy zabezpieczeń **bezpieczny z stacje robocze** utworzone wcześniej.
1. Z systemu Windows 10 urządzenia chcesz skonfigurować, przejdź do **ustawienia Windows** > **aktualizacja i zabezpieczenia** > **odzyskiwania**. Wybierz **wprowadzenie** w obszarze **zresetować tego komputera** i postępuj zgodnie z monitami, aby zresetować i ponownie skonfigurować urządzenia przy użyciu profilu i zgodność zasady skonfigurowane.

Po skonfigurowaniu urządzenia zakończenia przeglądu i Sprawdź konfigurację. Upewnij się, że prawidłowo skonfigurowano jako pierwsze urządzenie przed kontynuowaniem wdrożenia.

## <a name="assignment-and-monitoring"></a>Przypisywanie i monitorowanie

Przypisywanie urządzeń i użytkowników będzie wymagać mapowanie [wybrane profile](https://docs.microsoft.com/intune/device-profile-assign) do zabezpieczeń grupy i wszystkich nowych użytkowników, którzy otrzymają uprawnienia do usługi będą wymagane do dodania do grupy zabezpieczeń, a także.

Profile do monitorowania może odbywać się za pomocą funkcji monitorowania [profile Microsoft Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Kolejne kroki

[Microsoft Intune](https://docs.microsoft.com/intune/index) dokumentacji

[Usługa Azure AD](https://docs.microsoft.com/azure/active-directory/index) dokumentacji