---
title: Najlepsze rozwiązania dotyczące zabezpieczania i zarządzania nimi obciążeń migracji na platformę Azure | Dokumentacja firmy Microsoft
description: Po przeprowadzeniu migracji do platformy Azure, należy uzyskać najlepsze rozwiązania dla operacyjnej, zarządzanie i zabezpieczanie obciążeń zmigrowane.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: afcd180146bc349bda9375f10eb56f85f67ccb52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253002"
---
# <a name="best-practices-for-securing-and-managing-workloads-migrated-to-azure"></a>Najlepsze rozwiązania dotyczące zabezpieczania i zarządzania nimi obciążeń migracji na platformę Azure

Jako użytkownik planowania i projektowania dotyczące migracji, oprócz myśleć o migracji, należy wziąć pod uwagę modelu zabezpieczeń i zarządzania na platformie Azure po migracji. W tym artykule opisano planowania oraz najlepsze rozwiązania dotyczące zabezpieczania wdrożenie systemu Azure, po przeprowadzeniu migracji i bieżących zadań Zachowaj uruchomiony pracę na optymalnym poziomie wdrożenia. 

> [!IMPORTANT]
> Najlepsze rozwiązania i opinie opisanych w tym artykule opierają się na platformie Azure oraz z funkcji dostępnych w momencie pisania. Funkcje i możliwości zmiany w czasie.

## <a name="secure-migrated-workloads"></a>Zabezpieczanie zmigrowanych obciążeń

Po zakończeniu migracji najważniejszych zadań jest zabezpieczanie zmigrowanych obciążeń przed zagrożeniami wewnętrznymi i zewnętrznymi. Następujące najlepsze rozwiązania pomagają w tym:

- [Praca z usługą Azure Security Center](#best-practice-follow-azure-security-center-recommendations): Dowiedz się, jak pracować z monitorowaniem, ocenami oraz zalecenia podane przez usługę Azure Security Center
- [Szyfrowanie danych](#best-practice-encrypt-data): Pobieranie najlepszych rozwiązań do szyfrowania danych na platformie Azure.
- [Konfigurowanie ochrony przed złośliwym oprogramowaniem](#best-practice-protect-vms-with-antimalware): Ochrona maszyn wirtualnych przed złośliwym oprogramowaniem i złośliwych ataków.
- [Zabezpieczanie aplikacji sieci web](#best-practice-secure-web-apps): Bezpieczeństwo informacji poufnych w aplikacji sieci web zmigrowane.
- [Sprawdź subskrypcje](#best-practice-review-subscriptions-and-resource-permissions): Sprawdź, kto ma dostęp do subskrypcji platformy Azure i zasobów po zakończeniu migracji.
- [Praca z dziennikami](#best-practice-review-audit-and-security-logs): Przejrzyj dzienniki inspekcji i zabezpieczeń platformy Azure w regularnych odstępach czasu.
- [Przejrzyj inne funkcje zabezpieczeń](#best-practice-evaluate-other-security-features): Zrozumieć i ocenić zaawansowane funkcje zabezpieczeń oferowanych przez platformę Azure.

## <a name="best-practice-follow-azure-security-center-recommendations"></a>Najlepszym rozwiązaniem jest: Postępuj zgodnie z usługi Azure Security Center zaleceń

Firma Microsoft współpracuje trudnych do upewnij się, że Administratorzy dzierżawy platformy Azure informacje wymagane do włączenia funkcji zabezpieczeń, które ochrony obciążeń przed atakami.  Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami. W Centrum zabezpieczeń można stosować zasady zabezpieczeń na potrzeby różnych obciążeń, ograniczania ujawniania zagrożenia i wykrywanie oraz reagowanie na ataki. Usługa Security Center analizuje zasobów i konfiguracje dla dzierżaw usługi Azure i sprawia, że zalecenia dotyczące zabezpieczeń, w tym:

- **Scentralizowane zarządzanie zasadami** — zapewnianie zgodności z firmowymi lub prawnymi wymaganiami dotyczącymi zabezpieczeń przez centralne zarządzanie zasadami zabezpieczeń we wszystkich obciążeniach chmury hybrydowej.
- **Ciągła ocena zabezpieczeń** — monitorowanie poziomu bezpieczeństwa maszyn, sieci, magazynu i usług danych oraz aplikacji pod kątem potencjalnych problemów z zabezpieczeniami.
- **Praktyczne zalecenia** — korygowanie luk w zabezpieczeniach, zanim zostaną wykorzystane przez osoby atakujące, dzięki zaleceniom dotyczącym zabezpieczeń z określonymi priorytetami i możliwością wykonywania akcji.
- **Alerty i zdarzenia uszeregowane według priorytetów** — zdarzenia i alerty zabezpieczeń uszeregowane według priorytetów pozwalają koncentrować się najpierw na krytycznych zagrożeniach.

Oprócz ocen i zalecenia usługi Security Center zapewnia szereg innych funkcjach zabezpieczeń, które mogą być włączone dla określonych zasobów.

- **Dostęp tylko w czas (JIT)**: Zmniejszyć obszar ataków sieciowych przy użyciu dokładnie na czas, kontroli dostępu do portów zarządzania na maszynach wirtualnych platformy Azure.
    - Posiadanie RDP maszyny Wirtualnej portu 3389, Otwórz w Internecie udostępnia maszyny wirtualne do działania ciągłe nieuprawnione. Azure adresy IP są dobrze znane i hakerów stale sondować ich atakom na otwarte porty 3389. 
    - Po prostu w zabezpieczeń sieci w czasie używa grup (NSG) i przychodzące zasady tego limitu ilość czasu, który określony port jest otwarty.
    - Przy użyciu dokładnie na czas włączone, usługa Security Center sprawdza, czy użytkownik ma uprawnienia dostępu zapisu kontroli dostępu opartej na rolach dla maszyny Wirtualnej. Ponadto można określić zasady, w jaki sposób użytkownicy mogą łączyć się maszyn wirtualnych. Jeśli uprawnienia są poprawne, żądanie dostępu zostanie zatwierdzone i usługi Security Center konfiguruje sieciowych grup zabezpieczeń, aby zezwolić na ruch przychodzący do wybranych portów ilości czasu można określić. Sieciowe grupy zabezpieczeń są wróć do poprzedniego stanu, po upływie czasu.
- **Funkcje adaptacyjnego sterowania aplikacjami**: Zachowaj oprogramowania i złośliwym oprogramowaniem wyłączanie maszyn wirtualnych przez kontrolowanie, które aplikacje działają na nich przy użyciu listy dozwolonych aplikacji dynamicznych.
    - Funkcje adaptacyjnego sterowania aplikacjami umożliwiają białą listę aplikacji, a także uniemożliwić instalowanie aplikacji niezatwierdzonych lub weryfikacyjny oprogramowanie na maszynach wirtualnych przez nieautoryzowanych użytkowników lub administratorów.
    - Możesz zablokować lub alertu prób uruchomienia złośliwych aplikacji, należy unikać niechcianych ani złośliwych aplikacji i zapewnienia zgodności z zasadami zabezpieczeń aplikacji w Twojej organizacji.
- **File Integrity Monitoring**: Upewnij się, integralności plików uruchomione na maszynach wirtualnych.
    - Nie trzeba zainstalować oprogramowanie, aby spowodować problemy z maszyny Wirtualnej.  Modyfikacja pliku systemowego może spowodować obniżenie wydajności lub niepowodzenia maszyny Wirtualnej.  Plik integralności monitorowanie sprawdza, czy pliki systemowe i ustawienia rejestru dotyczące zmian i powiadamia, jeśli coś jest zaktualizowane.
    - Zaleceniem usługi Security Center, które pliki powinny monitorować.


**Dowiedz się więcej:**

- [Dowiedz się więcej](https://docs.microsoft.com/azure/security-center/security-center-intro) o usłudze Azure Security Center.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) o tylko dostęp do maszyny Wirtualnej w czasie.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) stosowanie funkcje adaptacyjnego sterowania aplikacjami.
- [Rozpoczynanie pracy](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring) z File Integrity Monitoring.


## <a name="best-practice-encrypt-data"></a>Najlepszym rozwiązaniem jest: Szyfrowanie danych 

Szyfrowanie jest ważną częścią rozwiązania w zakresie zabezpieczeń platformy Azure. Zapewnienie, że szyfrowanie jest włączone na wszystkich poziomach zapobiega nieupoważnione uzyskanie dostępu do poufnych danych, w tym przesyłanych i magazynowanych danych. 

### <a name="encryption-for-iaas"></a>Szyfrowanie dla IaaS

- **Maszyny wirtualne**: W przypadku maszyn wirtualnych usługi Azure Disk Encryption służy również do szyfrowanie dysków Windows i maszyn wirtualnych IaaS z systemem Linux.
    - Korzysta z szyfrowania dysków funkcją BitLocker for Windows i DM-Crypt dla systemu Linux, aby zapewnić szyfrowanie woluminów dysków systemu operacyjnego i danych.
    - Można użyć klucza szyfrowania utworzone przez platformę Azure, lub możesz podać własne klucze szyfrowania w usłudze Azure Key Vault. 
    - Za pomocą szyfrowania dysku danych maszyny Wirtualnej IaaS jest zabezpieczona na poziomie rest (operacja na dysku) i podczas rozruchu maszyny Wirtualnej. 
    - Usługa Azure Security Center ostrzega o tym, jeśli masz maszyny wirtualne, które nie są szyfrowane.
- **Magazyn**: Chronić dane w stanie spoczynku w magazynie platformy Azure.
    - Dane przechowywane na kontach magazynu Azure mogą być szyfrowane za pomocą generowanych przez Microsoft klucza AES, które mają certyfikaty FIPS 140-2 zgodny lub można użyć własnych kluczy.
    - Szyfrowanie usługi Storage jest włączona dla wszystkich kont magazynu z nowymi i istniejącymi i nie może być wyłączony.


### <a name="encryption-for-paas"></a>Szyfrowanie dla usług PaaS

W odróżnieniu od IaaS, w których zarządzasz swoje własne maszyny wirtualne i infrastrukturę w modelu PaaS modelu platforma i infrastruktura jest zarządzane przez dostawcę, pozostawiając pozwala skupić się na podstawowej aplikacji logiki i możliwości. Za pomocą tak wiele różnych typów usług PaaS każda usługa zostanie ono ocenione indywidualnie ze względów bezpieczeństwa. Na przykład Zobaczmy, jak firma Microsoft może włączyć szyfrowanie usługi Azure SQL Database.

- **Zawsze szyfrowane**: Kreator zawsze szyfrowane w SQL Server Management Studio w celu ochrony danych magazynowanych.
    - Możesz utworzyć Always Encrypted klucza szyfrowania danych poszczególnych kolumn.
    - Zawsze zaszyfrowane klucze mogą być przechowywane w postaci zaszyfrowanej w metadanych bazy danych lub przechowywane w magazynach zaufanych takich jak usługi Azure Key Vault.
    - Zmiany aplikacji prawdopodobnie będzie musieli korzystać z tej funkcji.
- **Przezroczyste szyfrowanie danych (TDE)**: Ochrona usługi Azure SQL Database przy użyciu w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku.
    - Funkcja TDE umożliwia działania szyfrowania się odbywać się bez konieczności wprowadzania zmian w warstwie aplikacji.
    - Funkcja TDE można użyć kluczy szyfrowania udostępniana przez firmę Microsoft, lub możesz podać własne klucze przy użyciu własnego klucza obsługi.


**Dowiedz się więcej:**
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) usługa Azure Disk Encryption dla maszyn wirtualnych IaaS.
- [Włącz](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-windows) szyfrowania dla maszyn wirtualnych Windows IaaS.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) szyfrowanie usługi Azure Storage dla danych magazynowanych.
- [Odczyt](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) z omówieniem funkcji zawsze zaszyfrowane.
- [Przeczytaj o](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-2017) TDE dla bazy danych Azure SQL.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql) szyfrowanie TDE w bazie Bring Your Own Key.

## <a name="best-practice-protect-vms-with-antimalware"></a>Najlepszym rozwiązaniem jest: Ochrona maszyn wirtualnych przy użyciu ochrony przed złośliwym kodem

W szczególności starsze maszyny wirtualne migrowane platformy Azure może nie mieć odpowiedniego poziomu ochrony przed złośliwym kodem zainstalowane.  System Azure oferuje rozwiązania bezpłatne punktu końcowego, która pomaga chronić maszyny wirtualne przed wirusami, programami szpiegującymi i inne złośliwe oprogramowanie.
- Microsoft Antimalware dla platformy Azure generuje alerty, gdy wiadomo, że próbuje zainstalować złośliwego lub niechcianego oprogramowania.
- To rozwiązanie jednego agenta, działającego w tle bez interwencji człowieka.
- W usłudze Azure Security Center można łatwo zidentyfikować maszyny wirtualne, które nie program endpoint protection, uruchamianie i instalowanie Microsoft Antimalware, zgodnie z potrzebami.

![Ochrony przed złośliwym kodem dla maszyn wirtualnych](./media/migrate-best-practices-security-management/antimalware.png)
*ochrony przed złośliwym kodem dla maszyn wirtualnych*

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/security/azure-security-antimalware) firmy Microsoft chroniąca przed złośliwym kodem.

## <a name="best-practice-secure-web-apps"></a>Najlepszym rozwiązaniem jest: Zabezpieczanie aplikacji sieci web

Aplikacje sieci web migrowanych twarzy kilka problemów:

- Większość aplikacji sieci web w starszej wersji się zwykle poufnych informacji w plikach konfiguracji.  Pliki zawierające informacje takie mogą spowodować problemy z zabezpieczeniami, gdy aplikacje są kopie zapasowe lub w przypadku, gdy kod aplikacji jest zaznaczone pole wyboru do wyewidencjonowane z kontroli źródła.
- Ponadto podczas migracji aplikacji sieci web znajdującej się na maszynie wirtualnej, prawdopodobnie przenosisz tego komputera z siecią lokalną i chronione przez zaporę środowisko do środowiska połączonego z Internetem. Należy się upewnić, że konfigurowanie rozwiązania, który wykonuje pracę tego samego jako lokalne zasoby.


Platforma Azure udostępnia kilka rozwiązań:

- **Usługa Azure Key Vault**: Obecnie deweloperzy aplikacji sieci web trwa kroki, aby upewnić się, że poufne informacje, nie jest przeciek tych plików. Jedną z metod ochronę informacji jest prowadzenie plików i umieścić go w usłudze Azure Key Vault.
    - Możesz używać usługi Key Vault w celu centralizacji przechowywania kluczy tajnych aplikacji i kontrolowanie ich dystrybucji. Takie rozwiązanie pomaga uniknąć konieczności przechowywania informacji o zabezpieczeniach w plikach aplikacji.
    - Aplikacje mogą zabezpieczeń uzyskiwanie dostępu do informacji w magazynie przy użyciu identyfikatorów URI, bez konieczności używania niestandardowego kodu.
    - Usługa Azure Key Vault pozwala zablokować dostęp za pośrednictwem środki kontroli bezpieczeństwa systemu Azure i bezproblemowo wdrożyć "przerzucić klucze". Microsoft nie zna ani wyodrębnić dane.
- **Środowisko usługi App Service**: Jeśli aplikacji, które migrujesz wymaga dodatkowej ochrony, należy rozważyć dodanie środowiska App Service Environment i zapory aplikacji sieci Web, które chcesz chronić zasoby aplikacji.
    - Azure App Service Environment zapewnia w pełni izolowane, dedykowane środowisko, w którym do uruchamiania usługi App Service, aplikacji, takich jak Windows i aplikacje internetowe systemu Linux, kontenerów platformy Docker, aplikacje mobilne i funkcje.
    - Jest to przydatne w przypadku aplikacji, które są bardzo dużej skali, izolacji i bezpiecznej sieci dostępu lub mają wysokie wykorzystanie pamięci
- **Zapora aplikacji sieci Web**: Funkcja Application Gateway, która zapewnia scentralizowaną ochronę aplikacji sieci web.
    - Chroni aplikacje sieci web bez konieczności modyfikacji kodu zaplecza.
    - W tym samym czasie za bramą aplikacji chroni wiele aplikacji sieci web.
    - Zapora aplikacji sieci Web można monitorować za pomocą usługi Azure Monitor i jest zintegrowana w usłudze Azure Security Center.



![Zabezpieczanie aplikacji sieci web](./media/migrate-best-practices-security-management/web-apps.png)
*usługi Azure Key Vault*

**Dowiedz się więcej:**

- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/key-vault/key-vault-overview) z usługi Azure Key Vault.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/application-gateway/waf-overview) zapory aplikacji sieci Web.
- [Zapoznaj się z wprowadzeniem](https://docs.microsoft.com/azure/app-service/environment/intro) do środowiska usługi App Service.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/key-vault/tutorial-web-application-keyvault) Konfigurowanie aplikacji sieci web, aby przeczytać wpisy tajne z usługi Key Vault.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/application-gateway/waf-overview) Zapora aplikacji sieci Web

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>Najlepszym rozwiązaniem jest: Przegląd subskrypcji i uprawnień dotyczących zasobów

Przeprowadź migrację obciążeń i uruchamiaj je na platformie Azure, pracowników z dostępem do obciążeń zmieniają położenie. Zespołem zabezpieczeń i zapoznać się z dostępu do usługi platformy Azure dzierżawy i grup zasobów w regularnych odstępach czasu. System Azure oferuje wiele ofert na potrzeby zarządzania tożsamościami i kontrolę dostępu, włącznie z kontroli dostępu opartej na rolach (RBAC), aby autoryzować uprawnieniami dostępu do zasobów platformy Azure.

- RBAC przypisuje się uprawnienia dostępu dla podmiotów zabezpieczeń. Podmioty zabezpieczeń reprezentuje użytkowników, grup (zbiór użytkowników), usługi podmiotów zabezpieczeń (tożsamości używane przez aplikacje i usługi), a zarządzane tożsamości (tożsamość usługi Azure Active Directory automatycznie zarządzane przez platformę Azure).
- RBAC można przypisać role do zasad zabezpieczeń, takich jak właściciela, współautora i czytelnika i roli definicje (zbiór uprawnień), które definiują operacje, które mogą być wykonywane przez role.
- RBAC można również ustawić zakresy, które ustawił granic dla roli. Zakres można ustawić na wielu poziomach, łącznie z grupy zarządzania, subskrypcji, grupy zasobów lub zasobu
- Upewnij się, że administratorzy z dostępem do platformy Azure tylko uzyskiwać dostępu do zasobów, które chcesz zezwolić.  Jeśli wstępnie zdefiniowanych ról na platformie Azure nie są wystarczająco szczegółowe, można utworzyć niestandardowe role do oddzielenia i ograniczyć uprawnienia dostępu.

![Kontrola dostępu](./media/migrate-best-practices-security-management/subscription.png)
*kontrola dostępu — IAM*

**Dowiedz się więcej:**

- [Temat](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC.
- [Dowiedz się,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) zarządzanie dostępem przy użyciu RBAC i witryny Azure portal.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) ról niestandardowych.

## <a name="best-practice-review-audit-and-security-logs"></a>Najlepszym rozwiązaniem jest: Przejrzyj dzienniki inspekcji i zabezpieczeń

Usługa Azure Active Directory (AD) zapewnia dzienników aktywności, które pojawiają się w usłudze Azure Monitor. Dzienniki przechwytywania operacji wykonywanych w dzierżawy platformy Azure, gdy miały miejsce i kto wykonał. 

- Dzienniki inspekcji wyświetlanie historii zadań w ramach dzierżawy. Działania logowania rejestruje show, który wykonuje zadania. 
- Dostęp do raportów zabezpieczeń zależy od licencji usługi Azure AD. W bezpłatna i podstawowa możesz pobrać listę ryzykownych użytkowników logowania. W wersjach Premium 1 i 2 — wersja Premium Uzyskaj podstawowe informacje o zdarzeniu.
- Dzienników aktywności można kierować do liczby punktów końcowych na potrzeby długoterminowego przechowywania i wgląd w dane.
- Stał się powszechną praktyką, przejrzyj dzienniki lub Integrowanie usługi zabezpieczeń informacjami i zdarzeniami (SIEM) narzędzia do zarządzania automatycznie Przejrzyj nieprawidłowości.  Jeśli nie używasz Premium 1 lub 2, należy wykonać analizy samodzielnie, lub korzystając z systemu SIEM.  Analiza obejmuje wyszukiwanie ryzykowne logowania i zdarzenia oraz inne wzorce ataków użytkownika.


![Użytkownicy i grupy](./media/migrate-best-practices-security-management/azure-ad.png)
*usługi Azure AD użytkownicy i grupy*

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) Dzienniki aktywności usługi Azure AD w usłudze Azure Monitor.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs) raporty dotyczące inspekcji w portalu usługi Azure AD.

## <a name="best-practice-evaluate-other-security-features"></a>Najlepszym rozwiązaniem jest: Oceń inne funkcje zabezpieczeń

System Azure oferuje wiele innych funkcji zabezpieczeń, które zapewniają zaawansowane opcje zabezpieczeń. Niektóre z tych najlepszych rozwiązań wymagają licencji dodatku i opcji premium.

- **Implementowanie jednostkami administracyjnymi w usłudze Azure AD (AU)**: Delegowanie obowiązków administracyjnych do personelu obsługi może być trudne kontrolką tylko podstawowego dostępu platformy Azure.  Zapewnia dostęp do personelu pomocy technicznej do administrowania wszystkich grup w usłudze Azure AD może nie być idealne podejście do zabezpieczeń organizacji.  Za pomocą funkcji Aktualizacje automatyczne umożliwia oddzielenie czynności związanych z zasobami platformy Azure do kontenerów w podobny sposób jak w środowisku lokalnym jednostki organizacyjne (OU).  Do korzystania z jednostki analizy użycia AU administratora musi mieć licencję usługi Azure AD premium. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).
- **Uwierzytelnianie wieloskładnikowe (MFA)**: Jeśli masz licencję usługi Azure AD premium można włączyć i wymuszanie uwierzytelniania Wieloskładnikowego dla kont administratorów. Wyłudzania informacji to najbardziej popularny sposób, że poświadczenia konta zostaną złamane.  Gdy nieuprawnione ma poświadczenia konta administratora, istnieje nie zatrzymywania je z Week akcji, np. usunięcie wszystkich grupy zasobów. Możesz skonfigurować usługę MFA na różne sposoby, m.in. za pomocą poczty e-mail, aplikacji wystawcy uwierzytelniania i telefonicznej, wiadomości SMS. Jako administrator możesz wybrać opcję najmniej istotnych. MFA integruje się z analizy zagrożeń i reagowanie zasady dostępu warunkowego, aby losowo wymagają żądania uwierzytelniania MFA. Dowiedz się więcej o [wskazówki dotyczące zabezpieczeń](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices), i [sposób konfigurowania](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) usługi MFA.
- **Zaimplementowanie dostępu warunkowego**: W większości organizacji małych i średnich administratorów platformy Azure i zespołu pomocy technicznej prawdopodobnie znajdują się w jednej lokalizacji geograficznej. W przypadku większości logowania będą pochodzić te same obszary. Jeśli adresy IP te lokalizacje są stosunkowo statycznych, dobrym pomysłem, nie powinni dostrzec administrator logowania z spoza tych obszarów. Nawet w przypadku, w którym zdalnego nieuprawnione obniża poświadczenia administratora można zaimplementować funkcje zabezpieczeń, takich jak dostęp warunkowy w połączeniu z usługą MFA, aby uniemożliwić logowanie z lokalizacji zdalnych lub z fałszywego lokalizacji z losowych adresów IP. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) dotyczące dostępu warunkowego i [przeglądanie najlepszych rozwiązań](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices) dla dostępu warunkowego w usłudze Azure AD.
- **Przejrzyj uprawnienia aplikacji przedsiębiorstwa**: Wraz z upływem czasu Administratorzy kliknij firmy Microsoft i innych firm łącza bez znajomości ich wpływu na organizację. Łącza może powodować ekrany zgody przypisać uprawnienia do aplikacji platformy Azure i mogą zezwalać na dostęp do odczytu danych usługi Azure AD lub nawet pełny dostęp do zarządzania subskrypcją platformy Azure cały. Należy regularnie przejrzeć aplikacje, do których Twoje Administratorzy i użytkownicy mają możliwość uzyskania dostępu do zasobów platformy Azure. Należy upewnić się, że te aplikacje mają uprawnienia, które są niezbędne. Ponadto kwartalną lub rocznie rozdzielana mogą w wiadomościach e-mail użytkowników z linkiem do strony aplikacji, dzięki czemu są one znane aplikacje, do których już dostęp do swoich danych organizacji. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/manage-apps/application-types) o typach aplikacji i [sposobu kontrolowania](https://docs.microsoft.com/azure/active-directory/manage-apps/remove-user-or-group-access-portal) przypisań aplikacji w usłudze Azure AD.



## <a name="managed-migrated-workloads"></a>Zarządzane zmigrowanych obciążeń

W tej sekcji firma Microsoft zaleca najlepsze rozwiązania dotyczące zarządzania platformy Azure, w tym:

- [Zarządzanie zasobami](#best-practice-name-resource-groups): Najlepsze rozwiązania dotyczące grupy zasobów platformy Azure i zasobów, w tym inteligentnego nazewnictwa, zapobieganie przypadkowemu usunięciu, zarządzanie uprawnień dotyczących zasobów i tagowania zasobów skuteczne.
- [Użyj plany](#best-practice-implement-blueprints): Uzyskaj szybki przegląd dotyczące używania schematy dla przedsiębiorstw i zarządzania środowiska wdrażania.
- [Przegląd architektury](#best-practice-review-azure-reference-architectures): Przejrzyj przykładowe architektur systemu Azure, aby dowiedzieć się więcej z podczas tworzenia wdrożenia po migracji.
- [Konfigurowanie grup zarządzania](#best-practice-manage-resources-with-management-groups): Jeśli masz wiele subskrypcji, możesz zebrać je do grup zarządzania i zastosować ustawienia zarządzania do tych grup.
- [Konfigurowanie zasad dostępu](#best-practice-deploy-azure-policy): Stosowanie zasad zgodności do Twoich zasobów platformy Azure.
- [Wdrożenie strategii bcdr można wykorzystać](#best-practice-implement-a-bcdr-strategy): Podsumowując strategii biznesowej ciągłość działalności biznesowej i odzyskiwanie po awarii recovery (BCDR) na bieżąco bezpieczeństwo danych, środowisko, odporne na błędy i zasobów i uruchomiony po wystąpieniu awarii.
- [Zarządzanie maszynami wirtualnymi](#best-practice-use-managed-disks-and-availability-sets): Grupowanie maszyn wirtualnych do grupy dostępności w celu zapewnienia odporności i wysokiej dostępności. Korzystają z dysków zarządzanych w celu ułatwienia zarządzania dyskami i magazynu maszyny Wirtualnej.
- [Monitorowanie użycia zasobów](#best-practice-monitor-resource-usage-and-performance): Włączanie rejestrowania diagnostycznego dla zasobów platformy Azure, tworzyć alerty i elementy playbook do aktywnego rozwiązywania problemów i pulpitu nawigacyjnego platformy Azure na użytek spójny widok kondycji wdrożenia i stanu.
- [Zarządzanie pomocy technicznej i aktualizacjami](#best-practice-manage-updates): Dowiedz się, Twój plan pomocy technicznej platformy Azure oraz sposób jej wdrożenia, zawiera najlepsze rozwiązania do przechowywania maszyn wirtualnych jest aktualne i put procesów na potrzeby zarządzania zmianami.


## <a name="best-practice-name-resource-groups"></a>Najlepszym rozwiązaniem jest: Nazwa grupy zasobów

Zagwarantowanie, że grupy zasobów ma znaczące nazwy tego Administratorzy i członkowie zespołu pomocy technicznej mogą łatwo rozpoznać i przejdź będzie znacznie zwiększają produktywność i wydajność.
- Firma Microsoft zaleca, zgodnie z konwencjami nazewnictwa platformy Azure.
- Jeśli synchronizacja lokalnych usług AD DS do usługi Azure AD za pomocą programu AD Connect, należy wziąć pod uwagę dopasowania nazwy zabezpieczeń grupy lokalnej na nazwy grup zasobów na platformie Azure.

![Nazewnictwo](./media/migrate-best-practices-security-management/naming.png)
*nazwy grupy zasobów*


**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) konwencje nazewnictwa

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>Najlepszym rozwiązaniem jest: Implementowanie usuwania blokady dla grup zasobów

Ostatnią czynnością, jaką potrzebne jest dla grupy zasobów zniknięcie, ponieważ został przypadkowo usunięty. Firma Microsoft zaleca zaimplementować blokady usuwania, tak aby nie jest to realizowane.


![Usuwa blokady](./media/migrate-best-practices-security-management/locks.png)
*usuwa blokady*

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian.


## <a name="best-practice-understand-resource-access-permissions"></a>Najlepszym rozwiązaniem jest: Zrozumienie uprawnienia dostępu do zasobów 

Właściciel subskrypcji ma dostęp do wszystkich grup zasobów i zasobów w ramach subskrypcji.
- Dodaj użytkowników oszczędnie do wartościowych przypisania. Zrozumienie konsekwencje tego rodzaju uprawnień jest ważne w ochronie środowiska, bezpieczne i stabilne.
- Upewnij się, że umieść zasoby w odpowiednich zasobach w grupach:
    - Zgodne ze sobą zasobów przy użyciu podobnych cyklu życia. W idealnym przypadku nie powinno być konieczne przejście zasobu, gdy należy usunąć całą grupę zasobów.
    - Zasoby, które obsługują funkcję lub obciążenia powinny być umieszczone razem dla uproszczonego zarządzania.

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/) organizowania subskrypcji i grup zasobów.

## <a name="best-practice-tag-resources-effectively"></a>Najlepszym rozwiązaniem jest: Skutecznie tagów zasobów

Często przy użyciu tylko nazwa grupy zasobów, które są związane z zasobami nie będzie zapewniać wystarczającej ilości metadanych do skutecznego wdrożenia mechanizmów, takich jak wewnętrzne rozliczeń lub zarządzania w ramach subskrypcji.
- Najlepszym rozwiązaniem należy użyć tagów usługi Azure można dodać przydatne metadane, które można tworzyć zapytania i zgłoszone. 
- Tagi umożliwiają celu logicznego uporządkowania zasobów przy użyciu właściwości zdefiniowanych przez użytkownika.  Tagi można zastosować do grupy zasobów lub zasobów bezpośrednio.
- Tagi mogą być stosowane na grupę zasobów lub poszczególnych zasobów. Pozycja znaczniki grupy zasobów nie są dziedziczone przez zasoby w grupie.
- Można zautomatyzować, tagowanie, przy użyciu programu Powershell i usługi Azure Automation lub tagu poszczególnych grup zasobów. -znakowanie podejście lub jeden z samoobsługi.  Jeśli żądanie i zmienić system zarządzania w miejscu, a następnie możesz z łatwością korzystaj z informacji w żądaniu, aby wypełnić tagów zasobów specyficznych dla firmy.


![Znakowanie](./media/migrate-best-practices-security-management/tagging.png)
*znakowanie*

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) i tagowania tag ograniczenia.
- [Przegląd](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#powershell) przykłady programu PowerShell i interfejsu wiersza polecenia skonfigurować znakowanie i zastosować znaczniki z grupy zasobów do jej zasobów.
- [Odczyt](https://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices/) znakowanie najlepsze rozwiązania platformy Azure.


## <a name="best-practice-implement-blueprints"></a>Najlepszym rozwiązaniem jest: Implementowanie plany

Podobnie jak planu umożliwiający inżynierów i architektów szkic parametry projektu z projektu, plany Azure umożliwiają architektów i chmury centralnej grup IT do zdefiniowania powtarzalne zestawu zasobów platformy Azure, które implementuje i zgodnego ze standardami organizacji wzorce i wymagania. Za pomocą platformy Azure plany zespołów programistycznych szybko można budować i tworzyć nowe środowiska, który spełnia wymagania dotyczące zgodności organizacji i mają zestaw wbudowanych składników, takich jak sieć w celu przyspieszenia tworzenia i dostarczania.

- Plany umożliwiają organizowanie wdrożenia grup zasobów, szablony usługi Azure Resource Manager i przypisania zasad i roli.
- Plany usługi Azure są przechowywane w globalnie dystrybuowanej usługi Azure Cosmos DB. Obiekty strategii są replikowane w wielu regionach świadczenia usługi Azure. Replikacja zapewnia małe opóźnienia, wysoką dostępność i spójny dostęp do planu, niezależnie od określonego regionu, do którego planu służy do wdrażania zasobów.

**Dowiedz się więcej:**

- [Odczyt](https://docs.microsoft.com/azure/governance/blueprints/overview) o schematy.
- [Przegląd](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare/) przykład planu, używane w celu przyspieszenia sztucznej Inteligencji w obszarze opieki zdrowotnej.

## <a name="best-practice-review-azure-reference-architectures"></a>Najlepszym rozwiązaniem jest: Przegląd architektury referencyjne platformy Azure

Tworzenie bezpiecznego, skalowalnego i łatwe w zarządzaniu obciążeń na platformie Azure może być czasochłonnym zadaniem.  Za pomocą ciągłych zmian może być trudne na bieżąco z różnymi funkcjami dla optymalnego środowiska. Odwołanie do poznania może być przydatne podczas projektowania i Migrowanie obciążeń.  Platforma Azure i Azure partnerów utworzone kilka przykładowych architektur referencyjnych dla różnych typów środowisk. Te przykłady są przeznaczone do zapewnienia pojęcia, które może uczyć się od i budowanie na. 

Architektury referencyjne są uporządkowane według scenariusza. Zawierają one, zaleca się wskazówki i porady dotyczące zarządzania, dostępność, skalowalność i bezpieczeństwo.
Azure App Service Environment zapewnia w pełni izolowane, dedykowane środowisko do uruchamiania usługi App Service, aplikacji, w tym Windows i aplikacje internetowe systemu Linux, kontenerów platformy Docker, aplikacje mobilne i funkcje. Usługa App Service dodaje możliwości platformy Azure do swojej aplikacji z zabezpieczenia, równoważenie obciążenia, automatycznego skalowania i automatyczne zarządzanie. Możesz również korzystać z zalet funkcji metodyki DevOps, takich jak ciągłe wdrażanie z DevOps platformy Azure i GitHub, Zarządzanie pakietami tymczasowe środowiska, domena niestandardowa i certyfikaty SSL. Usługa App Service jest przydatne w przypadku aplikacji, które wymagają izolacji i bezpiecznego dostępu do sieci i te, które używają wysokiej ilości pamięci i innych zasobów, które chcesz skalować.
**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/architecture/reference-architectures/) architektury referencyjne platformy Azure.
- [Przegląd](https://docs.microsoft.com/azure/architecture/example-scenario/) Azure przykładowe scenariusze.

## <a name="best-practice-manage-resources-with-management-groups"></a>Najlepszym rozwiązaniem jest: Zarządzanie zasobami za pomocą grupy zarządzania

Jeśli Twoja organizacja ma wiele subskrypcji, konieczne jest zarządzanie dostępem, zasady i zgodność dla nich. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji.

- Organizowanie subskrypcji do kontenerów o nazwie grupy zarządzania i nadzoru warunki ich dotyczą.
- Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunków grupy zarządzania.
- Grupy zarządzania zapewnia zarządzania przeznaczonych dla przedsiębiorstw na dużą skalę, niezależnie od tego, jakiego rodzaju subskrypcji masz.
- Na przykład można zastosować zasad grupy zarządzania, który ogranicza regionów, w których można utworzyć maszyny wirtualne. Ta zasada jest następnie stosowane do wszystkich grup zarządzania, subskrypcji i zasobów w ramach tej grupy zarządzania.
- Można tworzyć elastyczne struktury grupy zarządzania i subskrypcje, do organizowania zasobów w hierarchii dla ujednoliconego zasad i zarządzania dostępem.

Na poniższym diagramie przedstawiono przykład tworzenia hierarchii dla nadzoru przy użyciu grup zarządzania.

![Grupy zarządzania](./media/migrate-best-practices-security-management/management-groups.png)
*grup zarządzania*

**Dowiedz się więcej:**
- [Dowiedz się więcej](https://docs.microsoft.com/azure/governance/management-groups/index) temat organizowania zasobów do grup zarządzania.

## <a name="best-practice-deploy-azure-policy"></a>Najlepszym rozwiązaniem jest: Wdrażanie usługi Azure Policy

Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie zasad oraz zarządzanie nimi.

- Zasady wymuszają różne reguły i efekty dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umów dotyczących poziomu usług.
- Usługa Azure Policy daje w wyniku skanowania dla osób, które nie są zgodne z zasadami dotyczącymi zasobów.
- Na przykład można utworzyć zasadę, która zezwala na tylko określony rozmiar jednostki SKU dla maszyn wirtualnych w danym środowisku. Usługa Azure Policy oceni tego ustawienia podczas tworzenia i aktualizowania zasobów, a podczas skanowania istniejących zasobów. 
- System Azure oferuje wbudowane zasady, które można przypisać lub możesz utworzyć swój własny.


![Usługa Azure Policy](./media/migrate-best-practices-security-management/policy.png)
*usługa Azure Policy*

**Dowiedz się więcej:**
- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/governance/policy/overview) usługi Azure Policy.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności.


## <a name="best-practice-implement-a-bcdr-strategy"></a>Najlepszym rozwiązaniem jest: Wdrożenie strategii BCDR

Planowanie ciągłość biznesową i odzyskiwanie po awarii (BCDR), jest krytyczny ćwiczenia, które należy wykonać podczas planowania migracji do platformy Azure. W postanowienia prawne Twoja Umowa zawiera siły wyższej klauzulę usprawiedliwienia zobowiązań ze względu na większą życie, takie jak hurricanes lub trzęsienia ziemi. Jednak również mieć obowiązki dotyczące możliwości, aby upewnić się, że usługi będą w dalszym uruchamiania i odzyskiwanie w przypadku, gdy to konieczne, gdy strike po awarii. Możliwości w tym celu można poprawić lub obniżyć jakość przyszłość firmy.

Ogólnie strategii bcdr można wykorzystać należy wziąć pod uwagę:
- **Tworzenie kopii zapasowych danych**: Jak zapewnić bezpieczeństwo danych, aby odzyskać ją łatwo w przypadku wystąpienia awarii.
- **Odzyskiwanie po awarii**: Jak zachować aplikacji odpornych na błędy i dostępne w przypadku wystąpienia awarii. 

### <a name="azure-resiliency-features"></a>Funkcje odporności platformy Azure
Platforma Azure oferuje pewną liczbę funkcji odporności.

- **Kojarzenie regionu**: Azure par regionów w celu zapewnienia ochrony regionalne w granicach rezydencji danych. Azure zapewnia fizycznych izolację między par regionów, priorytetem odzyskiwania jednego regionu w parze w razie awarii szerokie, wdraża aktualizacje systemu osobno w poszczególnych regionach i umożliwia funkcji, takich jak Azure dla magazynu geograficznie nadmiarowego replikowanie na pary regionalne.
- **Strefy dostępności**: Strefy dostępności chronić przed awariami całego centrum danych platformy Azure, ustanawiając osobnych stref fizycznych z regionu platformy Azure. Każdej ze stref ma szczególne zasilania, infrastruktury sieci i chłodzenia mechanizm.
- **Zestawy dostępności**: Zestawy dostępności zapewnić ochronę przed awariami w centrum danych. Grupowanie maszyn wirtualnych w zestawach dostępności, aby zachować wysoką dostępność. W każdym zestawie dostępności platformy Azure implementuje wiele domen błędów tej grupy, które są razem bazowego sprzętu ze wspólnego źródła zasilania i przełącznika sieciowego i domeny aktualizacji, które zgrupować bazowego sprzętu, który może być poddawane konserwacji lub ponownie uruchomiony, w tym samym czasie. Na przykład gdy obciążenie pracą rozkłada się na maszynach wirtualnych platformy Azure, możesz umieścić co najmniej dwie maszyny wirtualne dla każdej warstwy aplikacji w zestawie. Na przykład można umieścić maszyny wirtualne frontonu w jeden zestaw i maszyn wirtualnych warstwy danych w innym. Ponieważ jest tylko jedna domena aktualizacji co uruchamiany ponownie w czasie w zestawie, a platforma Azure zapewnia, że maszyny wirtualne w zestawie są rozproszone między domeny błędów, upewnij się, że nie wszystkie maszyny wirtualne w zestawie zakończy się niepowodzeniem, w tym samym czasie.

### <a name="set-up-bcdr"></a>Konfigurowanie BCDR

Podczas migracji na platformę Azure, ważne jest, aby zrozumieć, mimo że platforma Azure zapewnia te możliwości wbudowanych odporności, należy zaprojektować wdrożenie systemu Azure, aby móc korzystać z funkcji platformy Azure i usług, które zapewniają wysoką dostępność odzyskiwanie po awarii i kopii zapasowej.

- Rozwiązanie BCDR będzie zależeć cele firmy i zależne od strategii wdrażania na platformie Azure. Infrastruktura jako usługa (IaaS) oraz platforma jako wdrożeń usługi (PaaS) stanowi wyzwanie różnych przypadku BCDR.
- Jeden raz w miejscu, rozwiązania BCDR powinien zostać przetestowany regularnie do sprawdzenia, czy strategii pozostaje możliwego do użycia.


## <a name="best-practice-back-up-your-data"></a>Najlepszym rozwiązaniem jest: Tworzenie kopii zapasowej danych

W większości przypadków to obciążenie w środowisku lokalnym jest wycofywany po migracji, a musi być rozszerzony lub zastąpić strategii lokalne kopie zapasowe danych. Jeśli migrujesz całe centrum danych na platformie Azure, musisz zaprojektować i zaimplementować rozwiązanie pełnej kopii zapasowej przy użyciu technologii platformy Azure lub innych firm, zintegrowane rozwiązania. 


### <a name="back-up-an-iaas-deployment"></a>Tworzenie kopii zapasowej wdrożenia usługi IaaS

W przypadku obciążeń uruchomionych na maszynach wirtualnych IaaS platformy Azure należy wziąć pod uwagę te rozwiązania tworzenia kopii zapasowych:

- **Azure Backup**: Zawiera kopie zapasowe spójne z aplikacjami Windows Azure i maszyn wirtualnych systemu Linux.
- **Migawek magazynu**: Wykonywanie migawek magazynu obiektów blob.

#### <a name="azure-backup"></a>Azure Backup

Usługa Azure Backup tworzy kopię zapasową tworzy punkty odzyskiwania danych, które są przechowywane w usłudze Azure storage. Usługa Azure Backup można utworzyć kopię zapasową dyski maszyny Wirtualnej platformy Azure i usługi Azure Files (wersja zapoznawcza). Usługa pliki Azure udostępniają udziałów plików w chmurze, dostępne za pośrednictwem protokołu SMB.
   
Usługa Azure Backup umożliwia tworzenie kopii zapasowych maszyn wirtualnych na kilka sposobów.

- **Bezpośrednie tworzenie kopii zapasowych w ustawieniach maszyny Wirtualnej**: Można wykonać kopię zapasową maszyn wirtualnych za pomocą usługi Kopia zapasowa Azure bezpośrednio z opcji maszyny Wirtualnej w witrynie Azure portal. Można tworzyć kopie zapasowe maszyn wirtualnych raz i dzień i przywracanie dysku maszyny Wirtualnej, zgodnie z potrzebami. Usługa Azure Backup tworzy migawki danych uwzględniających aplikacji (w tle VSS), agent nie jest zainstalowany na maszynie Wirtualnej.
- **Bezpośrednie tworzenie kopii zapasowych w magazynie usługi Recovery Services**: Można utworzyć kopię zapasową maszyn wirtualnych IaaS za wdrażanie magazyn usługi Azure Backup Recovery Services. Zapewnia to pojedyncza lokalizacja umożliwiająca śledzenie i zarządzanie kopiami zapasowymi oraz zapewnia szczegółowe opcje tworzenia kopii zapasowych i przywracania. Kopia zapasowa jest maksymalnie trzy razy dziennie, na poziomie plików/folderów. Nie jest obsługujących aplikację, a nie jest obsługiwany w systemie Linux. Musisz zainstalować agenta usługi Microsoft Azure Recovery Services (MARS) na każdej maszynie Wirtualnej, aby utworzyć kopię zapasową.
- **Usługa Azure Backup Server: Ochrona maszyn wirtualnych do usługi Azure Backup Server**: Usługa Azure Backup Server znajduje się bezpłatnie przy użyciu usługi Azure Backup. Maszyna wirtualna została skopiowana do lokalnego magazynu usługi Azure Backup Server. Możesz następnie utworzyć kopię zapasową serwera usługi Azure Backup na platformie Azure w magazynie. Kopia zapasowa jest aplikacji obsługujących, z dokładnością pełną za pośrednictwem często kopii zapasowej i przechowywania. Można utworzyć kopię na poziomie aplikacji. Na przykład przez tworzenie kopii zapasowej programu SQL Server lub SharePoint.

Dla bezpieczeństwa usługi Azure Backup szyfruje dane, śledząc przy użyciu algorytmu AES 256, a następnie wysyła je za pośrednictwem protokołu HTTPS na platformie Azure. Kopia zapasowa danych przechowywanych na platformie Azure jest szyfrowana przy użyciu [szyfrowanie usługi Storage (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)i dla transmisji i przechowywania.


![Usługa Azure Backup](./media/migrate-best-practices-security-management/iaas-backup.png)
*usługa Azure Backup*

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) poszczególnych typów kopii zapasowych.
- [Planowanie infrastruktury kopii zapasowych](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) maszyn wirtualnych platformy Azure.

#### <a name="storage-snapshots"></a>Migawek magazynu

Maszyny wirtualne platformy Azure są przechowywane jako stronicowe obiekty BLOB w usłudze Azure Storage. 

- Migawki przechwytują stan obiektu blob w określonym punkcie w czasie.
- Jako alternatywną metodę tworzenia kopii zapasowych dysków maszyny Wirtualnej platformy Azure możesz migawki obiektów blob storage i skopiować je do innego konta magazynu. 
- Możesz skopiować cały obiekt blob, lub użyć migawek przyrostowych kopii — kopiowanie tylko przyrostowe zmiany i zmniejszenia miejsca do magazynowania.
- Jako dodatkowe środki zabezpieczeń aby umożliwić usuwania nietrwałego dla kont usługi blob storage. Ta funkcja jest włączona obiektów blob, który jest usuwany jest oznaczone do usunięcia, ale nie od razu usunięte. W okresie przejściowym można przywrócić obiektu blob.

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) usługi Azure blob storage.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/storage/blobs/storage-blob-snapshots) utworzyć migawki obiektu blob.
- [Przejrzyj przykładowy scenariusz](https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup) dla kopii zapasowej z magazynu obiektów blob.
- [Przeczytaj o](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) usuwania nietrwałego.
- [Odzyskiwanie po awarii i wymuszenie trybu failover (wersja zapoznawcza) w usłudze Azure Storage](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

#### <a name="third-party-backup"></a>Kopii zapasowych innych firm

Ponadto można użyć rozwiązania innych firm do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure i kontenerów magazynów w magazynie lokalnym lub w chmurze innych dostawców. [Dowiedz się więcej](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1) o rozwiązania tworzenia kopii zapasowych w witrynie Azure marketplace. 


### <a name="back-up-a-paas-deployment"></a>Tworzenie kopii zapasowych to wdrożenie PaaS


W odróżnieniu od IaaS, w których zarządzasz swoje własne maszyny wirtualne i infrastrukturę w modelu PaaS modelu platforma i infrastruktura jest zarządzane przez dostawcę, pozostawiając pozwala skupić się na podstawowej aplikacji logiki i możliwości. Za pomocą tak wiele różnych typów usług PaaS każda usługa zostanie ono ocenione osobno na potrzeby kopii zapasowej. Omówimy dwie typowe usług PaaS platformy Azure — usłudze Azure SQL Database i Azure Functions.

#### <a name="back-up-azure-sql-database"></a>Tworzenie kopii zapasowej bazy danych SQL Azure

Usługa Azure SQL Database to w pełni zarządzanego aparatu bazy danych PaaS. Zapewnia wiele funkcji ciągłości działania, w tym zautomatyzowane tworzenie kopii zapasowej.

- SQL Database automatycznie przeprowadza tygodniowe pełne kopie zapasowe i różnicowych kopii zapasowych co 12 godzin. Kopie zapasowe dziennika transakcji są pobierane co pięciu do dziesięciu minut, aby chronić przed utratą danych w bazie danych.
- Kopie zapasowe są niewidoczne i nie powodują naliczania dodatkowych kosztów.
- Kopie zapasowe są przechowywane w magazynie RA-GRS nadmiarowości geograficznej i replikowane do sparowanym regionie geograficznym.
- Przechowywanie kopii zapasowych zależy od model zakupu. Warstwy usług oparte na jednostkach DTU przejść z siedmiu dni dla warstwy podstawowa do 35 dni dla warstwy.
- Możesz przywrócić bazę danych do punktu w czasie w okresie przechowywania. Można także przywrócić usuniętą bazę danych, przywracać w innym regionie geograficznym lub z długoterminowej kopii zapasowej, jeśli baza danych ma długoterminowe zasady przechowywania (od lewej do prawej).


![Usługa Azure backup SQL](./media/migrate-best-practices-security-management/sql-backup.png)
*kopii zapasowej Azure SQL*

**Dowiedz się więcej:**
- [Automatyczne kopie zapasowe](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) dla bazy danych SQL.
- [Odzyskiwanie bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) za pomocą automatycznych kopii zapasowych.

 
#### <a name="back-up-azure-functions"></a>Tworzenie kopii zapasowych usługi Azure Functions

Ponieważ usługi Azure Functions więcej lub mniej funkcji jako kod, należy utworzyć kopię je przy użyciu tych samych metod, których używasz do ochrony kodu, np. kontrolę źródła w usłudze GitHub lub usługom DevOps platformy Azure

**Dowiedz się więcej:**

- [Ochrona danych](/azure/devops/organizations/security/data-protection) dla metodyki DevOps platformy Azure.

## <a name="best-practice-set-up-disaster-recovery"></a>Najlepszym rozwiązaniem jest: Konfigurowanie odzyskiwania po awarii 

Oprócz ochrony danych, BCDR planowania należy wziąć pod uwagę sposób zachować dostępność w razie awarii aplikacji i obciążeń. 

### <a name="set-up-disaster-recovery-for-iaas-apps"></a>Konfigurowanie odzyskiwania po awarii dla aplikacji IaaS

Dla obciążeń działających na maszynach wirtualnych IaaS platformy Azure i usługi Azure storage, należy wziąć pod uwagę te rozwiązania:

- **Azure Site Recovery**: Organizuje replikację maszyn wirtualnych platformy Azure w regionie pomocniczym oddalonym o konfiguracjach z podstawową. W przypadku wystąpienia awarii, przejścia w tryb failover z regionu podstawowego do regionu pomocniczego, a użytkownicy mogą nadal uzyskiwać dostęp do aplikacji. W przypadku elementów na normalne, powrotu po awarii do regionu podstawowego.
- **Usługa Azure storage**: Platforma Azure udostępnia wbudowany odporności i wysokiej dostępności dla różnych typów magazynów:

#### <a name="azure-site-recovery"></a>Azure Site Recovery 

Usługa Azure Site Recovery jest podstawowy usługa zapewniających, maszyn wirtualnych platformy Azure może być przełączany w tryb online i aplikacji maszyny Wirtualnej, udostępnionych podczas wystąpienia awarii. Usługa Site Recovery replikuje maszyny wirtualne z podstawowego do pomocniczego regionu platformy Azure. W przypadku awarii, w trybie Failover maszyny wirtualne z regionu podstawowego, a nadal mieć dostęp do nich jak zwykle w regionie pomocniczym. W przypadku operacji powrócić do normalnego trybu, możesz przełączać maszyny wirtualne z powrotem do regionu podstawowego.


![Usługa Azure Site Recovery](./media/migrate-best-practices-security-management/site-recovery.png)
*Site Recovery*

**Dowiedz się więcej:**
- [Przegląd](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-disaster-recovery-guidance) scenariuszy odzyskiwania po awarii dla maszyn wirtualnych platformy Azure.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-replicate-after-migration) Konfigurowanie odzyskiwania po awarii dla maszyny Wirtualnej platformy Azure po migracji.

#### <a name="azure-storage"></a>Azure Storage

Usługa Azure storage jest replikowana w celu wbudowanej odporności i wysokiej dostępności.

-   **Magazyn geograficznie nadmiarowy (GRS)**: Chroni przed awarią obejmujących cały region, z co najmniej 99,99999999999999% (16 9) trwałości obiektów w danym roku.
    - Storage dane są replikowane do regionu pomocniczego, za pomocą którego jest sparowana z regionu podstawowego.
    - Jeśli region podstawowy ulegnie awarii, a firma Microsoft zainicjuje tryb failover do regionu pomocniczego, będziesz mieć dostęp do odczytu do danych.
- **Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS)**: Chroni przed awarią obejmujących cały region.
    - Storage dane są replikowane do regionu pomocniczego.
    - Masz gwarantowane zreplikowane dane w regionie pomocniczym, niezależnie od tego, czy firma Microsoft zainicjuje tryb failover do odczytu. gdzie dane co najmniej dwóch centrów w tym samym regionie, może być problem, ale dane są nadal dostępne w regionie podstawowemu.
-   **Magazynu strefowo nadmiarowego (ZRS)**:  Chroni przed awariami centrów danych.
    - Magazyn ZRS replikuje dane synchronicznie w trzech klastrach magazynu w jednym regionie. Klastry fizycznie oddzielone i każdy znajdujący się w jego własnej strefie dostępności.
    - W przypadku awarii usługi storage nadal będą dostępne. Magazyn ZRS należy minimalną docelową dla obciążeń o kluczowym znaczeniu.



**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/storage/common/storage-redundancy) replikacji usługi Azure storage.


### <a name="set-up-disaster-recovery-for-paas-workloads"></a>Konfigurowanie odzyskiwania po awarii dla obciążeń PaaS

Rozważmy awarii opcje odzyskiwania dotyczące naszych przykładach obciążenia PaaS.

#### <a name="disaster-recovery-of-azure-sql-server"></a>Odzyskiwanie po awarii programu SQL Server, Azure

Istnieje wiele różnych opcji, każdy wywierania wpływu na utratę danych, czas odzyskiwania i kosztów.

Można użyć grupy trybu failover i aktywna replikacja geograficzna, aby zapewnić odporność względem katastrofalnych awarii i awarii regionalnej

- **Aktywna replikacja geograficzna**: Wdróż aktywnej replikacji geograficznej dla odzyskiwania po awarii szybkie, jeśli wystąpi awaria centrum danych lub nie można nawiązać połączenia z bazą danych w podstawowej.
    - Replikacja geograficzna stale tworzy replik z możliwością odczytu bazy danych w maksymalnie cztery pomocnicze bazy danych w tej samej lub różnych regionach.
    - W przypadku wystąpienia awarii awaryjnej w jednym regionie pomocniczym, a przełączyć bazy danych online.
- **Automatyczny tryb failover grupy**: Grupy trybu failover automatyczne rozszerzanie aktywnej replikacji geograficznej z niewidocznym trybem failover wielu baz danych.
    - Grupa automatyczny tryb failover zapewnia zaawansowane abstrakcji aktywną replikację geograficzną za pomocą replikacji baza danych na poziomie grupy i automatycznej pracy awaryjnej.
    - Możesz utworzyć grupy trybu failover, która zawiera podstawowy serwer obsługujący jeden lub więcej podstawowych baz danych, serwer pomocniczy hostingu tylko do odczytu replik podstawowych baz danych odbiorników, które wskazują na każdym serwerze, a zasady automatycznej pracy awaryjnej.
    - Punkty końcowe określony odbiornik eliminują konieczność zmiany parametrów połączenia SQL po włączeniu trybu failover.
- **Przywracanie geograficzne**: 
    -   Funkcja przywracania geograficznego umożliwia odzyskanie bazy danych w innym regionie. Automatyczne kopie zapasowe wszystkich baz danych platformy Azure będą replikowane do regionu pomocniczego w tle. Zawsze będzie przywrócić bazę danych z kopii plików kopii zapasowej, przechowywane w regionie pomocniczym.
-   **Strefowo nadmiarowe bazy danych** zapewnia wbudowaną obsługę dla stref dostępności platformy Azure.
    - Strefowo nadmiarowe bazy danych pozwala zwiększyć o wysokiej dostępności dla programu SQL Server Azure awarii centrum danych.
    - Dzięki nadmiarowości strefy możesz umieścić replik nadmiarowych baz danych w różnych strefach dostępności w regionie. 



![Replikacja geograficzna](./media/migrate-best-practices-security-management/geo-replication.png)
*Geografickou replikaci*

**Dowiedz się więcej:**
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability) wysoką dostępność dla programu SQL Server Azure.
- [Odczyt](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/) 101 baz danych SQL Azure, odzyskiwania po awarii.
- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) z aktywnej replikacji geograficznej i grupy trybu failover.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery) projektowanie pod kątem odzyskiwania po awarii.
- [Uzyskaj wskazówki](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) dla grupy trybu failover.
- [Uzyskaj wskazówki](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-security-config) zabezpieczeń po Przywracanie geograficzne lub pracy awaryjnej.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability#zone-redundant-configuration) nadmiarowości strefy
- [Dowiedz się, jak](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-drills) wykonać odzyskiwanie po awarii dla bazy danych SQL.

### <a name="disaster-recovery-for-azure-functions"></a>Odzyskiwanie po awarii dla usługi Azure Functions

W przypadku awarii infrastruktury obliczeniowej na platformie Azure aplikację funkcji platformy Azure mogą stać się niedostępne.

- Aby zminimalizować możliwości taki Przestój, należy użyć dwóch aplikacji funkcji wdrożone w różnych regionach.
- Usługa Azure Traffic Manager można skonfigurować do wykrywania problemów w aplikacji funkcji podstawowej oraz automatyczne przekierowywanie ruchu do aplikacji funkcji w regionie pomocniczym
- Traffic Manager z magazynu geograficznie nadmiarowego pozwala na korzystanie z tej samej funkcji w wielu regionach, w razie awarii regionalnej


![Usługa Traffic Manager](./media/migrate-best-practices-security-management/traffic-manager.png)
*usługi Traffic Manager*

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) odzyskiwanie po awarii dla aplikacji platformy Azure.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution) odzyskiwania po awarii i dystrybucja geograficzna trwałe funkcji platformy Azure.


## <a name="best-practice-use-managed-disks-and-availability-sets"></a>Najlepszym rozwiązaniem jest: Za pomocą dysków zarządzanych i zestawach dostępności

Platforma Azure używa zestawów dostępności do logicznego grupowania maszyn wirtualnych ze sobą oraz do izolowania maszyn wirtualnych w zestawie z poziomu innych zasobów. Maszyny wirtualne w zestawie dostępności są rozproszone między wiele domen błędów przy użyciu oddzielnych podsystemów, aby zapewnić ochronę przed awariami lokalnych i również są rozproszone między wiele domen aktualizacji, więc, że nie wszystkie maszyny wirtualne w zestawie ponowny rozruch w tym samym czasie.

Usługi Azure Managed Disks upraszcza zarządzanie dyskami maszyn wirtualnych IaaS platformy Azure, zarządzając konta magazynu skojarzone z dyskami maszyn wirtualnych.

- Firma Microsoft zaleca, korzystają z dysków zarządzanych, jeśli jest to możliwe. Musisz określić typ magazynu, przeznaczonych do użycia i rozmiar dysku, potrzebujesz, a platforma Azure utworzy i zarządza dysku, w tle.
- Możesz również przekonwertować istniejące dyski zarządzane.
- Należy utworzyć maszyny wirtualne w zestawach dostępności w celu zapewnienia wysokiej odporności i dostępności. Po wystąpieniu planowanych lub nieplanowanych przestojów, zestawy dostępności zapewniają, że co najmniej jedną z maszyn wirtualnych w zestawie jest nadal dostępne.


![Usługa Managed disks](./media/migrate-best-practices-security-management/managed-disks.png)
*usługi Managed disks*

**Dowiedz się więcej:**
- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) z dyskami zarządzanymi.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks) konwertowanie dysków zarządzanych.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) Zarządzanie dostępnością maszyn wirtualnych Windows na platformie Azure.


## <a name="best-practice-monitor-resource-usage-and-performance"></a>Najlepszym rozwiązaniem jest: Monitorowanie użycia zasobów i wydajności 

Może być przeniesiono obciążeń na platformę Azure dla jego ogromne możliwości skalowania. Przenoszenie obciążenia nie oznacza to jednak, że Azure automatycznie wdroży skalowanie bez interwencji użytkownika. Na przykład:

- Jeśli marketingowe organizacji wypycha nowy anons TV, która napędza 300% więcej ruchu, może to spowodować problemy z dostępnością lokacji. Właśnie został zmigrowany obciążenie może napotkać ograniczenia przypisane i awarii.
- Innym przykładem mogą być rozproszone ataki (DDoS) typu "odmowa usługi" na obciążeniu zmigrowane. W takim przypadku nie można skalować, ale aby zapobiec osiągnięciu zasobów źródła ataków.

Tych dwóch przypadkach mają różne metody rozwiązywania, ale obie można na potrzeby szczegółowych informacji o tym, co się dzieje z użycia i monitorowania wydajności.

- Usługa Azure Monitor może pomóc powierzchni te metryki i udostępniać odpowiedzi alerty, automatycznego skalowania, usługa event hubs, logic apps i więcej.
- Oprócz monitorowania platformy Azure, można zintegrować monitorowanie dzienników platformy Azure dla zdarzeń inspekcji i wydajności aplikacji SIEM innych firm.


![Usługa Azure Monitor](./media/migrate-best-practices-security-management/monitor.png)
*usługa Azure Monitor*

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/azure-monitor/overview) usługa Azure Monitor.
- [Uzyskaj wskazówki](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) do monitorowania i diagnostyki.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) skalowania automatycznego.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/security-center/security-center-export-data-to-siem) przekierowywanie danych platformy Azure do narzędzia SIEM.

## <a name="best-practice-enable-diagnostic-logging"></a>Najlepszym rozwiązaniem jest: Włączanie rejestrowania diagnostycznego

Zasoby platformy Azure generują szereg uczciwe rejestrowanie danych metryki i dane telemetryczne.

- Domyślnie większość typów zasobów nie ma włączone rejestrowanie diagnostyczne.
- Włączając rejestrowanie diagnostyczne różnych zasobów, wykonywanie zapytań o dane rejestrowania i tworzyć alerty i elementy playbook na jego podstawie.
- Po włączeniu rejestrowania diagnostycznego każdego zasobu mają określone kategorie. Możesz wybrać jedną lub więcej kategorii rejestrowania i lokalizację danych dziennika. Dzienniki mogą być wysyłane do konta magazynu, Centrum zdarzeń lub dzienniki usługi Azure Monitor. 


![Rejestrowanie diagnostyczne](./media/migrate-best-practices-security-management/diagnostics.png)
*rejestrowanie diagnostyczne*

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zbieranie i wykorzystywanie danych dziennika.
- [Dowiedz się, jakie operacje są obsługiwane](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema) rejestrowanie w celach diagnostycznych.


## <a name="best-practice-set-up-alerts-and-playbooks"></a>Najlepszym rozwiązaniem jest: Skonfiguruj alerty i elementy playbook

Rejestrowanie diagnostyczne włączone dla zasobów platformy Azure, możesz rozpocząć na potrzeby rejestrowania danych, aby tworzyć niestandardowe alerty.

- Alerty aktywnie powiadamiają użytkownika warunki znajdują się w danych monitorowania. Następnie można zgłosić problemy, zanim użytkownicy systemu, zwróć uwagę, ich. Może generować alerty na elementów, takich jak wartości metryk, zapytań funkcji przeszukiwania dzienników, zdarzenia dziennika aktywności, kondycji platformy i dostępności witryny sieci Web.
- Gdy alerty są wyzwalane, można uruchomić elementu Playbook aplikacji logiki. Podręcznik ułatwia Automatyzowanie i organizowanie odpowiedzi na określony alert. Elementy playbook są oparte na usłudze Azure Logic Apps. Szablony aplikacji logiki można użyć do tworzenia elementów playbook lub tworzyć własne.
- Prostym przykładem można utworzyć alert, który wyzwala sytuacji skanowanie portów dla sieciowej grupy zabezpieczeń.  Można skonfigurować elementu playbook, który jest uruchamiany i zablokuje adres IP punktu początkowego skanowania.
- Innym przykładem mogą być aplikacji za pomocą przeciek pamięci.  Gdy użycie pamięci pobiera do pewnego momentu, elementu playbook można odtworzyć proces.


![Alerty](./media/migrate-best-practices-security-management/alerts.png)
*alertów*

**Dowiedz się więcej:**
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts) alertów.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/security-center/security-center-playbooks) elementów playbook zabezpieczeń, które odpowiadanie na alerty usługi Security Center.

## <a name="best-practice-use-the-azure-dashboard"></a>Najlepszym rozwiązaniem jest: Użyj pulpitu nawigacyjnego platformy Azure

Azure portal to oparte na sieci web ujednoliconą konsolę, która pozwala na tworzenie, zarządzanie i monitorowanie wszystko — od prostych aplikacji sieci web do aplikacji w chmurze złożonych. Obejmuje ona dostosowywalny pulpit nawigacyjny oraz opcje ułatwień dostępu.
- Można utworzyć wiele pulpitów nawigacyjnych i udostępnianie ich innym osobom, które mają dostęp do subskrypcji platformy Azure.
- W tym modelu udostępnionego członkowie zespołu mają wgląd w środowisku platformy Azure, umożliwiając im stosować proaktywne podejście związane z zarządzaniem systemy w chmurze.


![Pulpit nawigacyjny platformy Azure](./media/migrate-best-practices-security-management/dashboard.png)
*pulpitu nawigacyjnego platformy Azure*

**Dowiedz się więcej:**

- [Dowiedz się, jak](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) Tworzenie pulpitu nawigacyjnego.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-structure) struktury pulpitu nawigacyjnego.


## <a name="best-practice-understand-support-plans"></a>Najlepszym rozwiązaniem jest: Omówienie planów pomocy technicznej

W pewnym momencie należy współpracować z pracownikami działu pomocy technicznej lub działu pomocy technicznej firmy Microsoft. Ważne jest posiadanie zbiór zasadami i procedurami obsługi podczas scenariuszy, takich jak odzyskiwanie po awarii. Ponadto administratorzy i pomocy technicznej pracownikom powinni przejść szkolenie dotyczące we wdrażaniu tych zasad.

- W mało prawdopodobnym przypadku, problem z usługą platformy Azure ma wpływ na obciążenie administratorów wiedzieć, jak przesłać bilet pomocy technicznej w firmie Microsoft w sposób najbardziej odpowiedni i wydajny.
- Zapoznaj się z różnych planach pomocy technicznej oferowanych na platformie Azure. One należeć do zakresu od czasów odpowiedzi przeznaczonych dla deweloperów wystąpień w wersji Premier pomocy technicznej i czas odpowiedzi z mniej niż 15 minut.


![Plany pomocy technicznej](./media/migrate-best-practices-security-management/support.png)
*plany pomocy technicznej*

**Dowiedz się więcej:**
- [Zapoznaj się z omówieniem](https://azure.microsoft.com/support/options/) Azure plany pomocy technicznej.
- [Dowiedz się więcej o](https://azure.microsoft.com/support/legal/sla/) umowami dotyczącymi poziomu (SLA).

## <a name="best-practice-manage-updates"></a>Najlepszym rozwiązaniem jest: Zarządzanie aktualizacjami

Przechowywanie maszyn wirtualnych platformy Azure aktualizowane przy użyciu najnowszego systemu operacyjnego i aktualizacji oprogramowania jest ogromną kwestii. Możliwość udostępnienia wszystkich maszyn wirtualnych, aby aktualizacje, które potrzebują oraz do automatycznego wypychania te aktualizacje są bardzo przydatne.

- Zarządzanie aktualizacjami w usłudze Azure Automation umożliwia zarządzanie aktualizacjami systemu operacyjnego dla komputerów z systemem Windows i Linux komputerów, które są wdrażane na platformie Azure, lokalnie i w chmurze innych dostawców. 
- Umożliwiają szybko ocenić stan dostępnych aktualizacji na wszystkich komputerach agentów zarządzania aktualizacjami i zarządzanie instalacją aktualizacji.
- Zarządzanie aktualizacjami dla maszyn wirtualnych można włączyć bezpośrednio z konta usługi Azure Automation. Można również zaktualizować pojedynczej maszyny Wirtualnej na stronie maszyny Wirtualnej w witrynie Azure portal.
- Ponadto maszyny wirtualne platformy Azure mogą być rejestrowane z System Center Configuration Manager. Można następnie Migrowanie obciążeń programu Configuration Manager na platformę Azure i wykonaj raportowania oraz aktualizowanie oprogramowania za pomocą interfejsu jednej sieci web.


![Maszyna wirtualna aktualizacje](./media/migrate-best-practices-security-management/updates.png)
*aktualizacji*

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/automation/automation-update-management) rozwiązanie update management na platformie Azure.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/automation/oms-solution-updatemgmt-sccmintegration) integracji programu Configuration Manager przy użyciu rozwiązania update management.
- [Często zadawane pytania dotyczące](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure) program Configuration Manager na platformie Azure.


## <a name="implement-a-change-management-process"></a>Implementowanie proces zarządzania zmianami

Podobnie jak w przypadku dowolnego systemu produkcyjnego, dzięki czemu dowolnego typu może mieć wpływ na środowisko. Proces zarządzania zmianami, wymagającego żądań do przesłania w celu zmiany na systemy produkcyjne to uzupełnienie przydatne w środowisku zmigrowane.

- Można tworzyć najlepsze praktyki platformy zarządzania zmianami zwiększyć świadomość w administratorów i personelu pomocy technicznej.
- Aby ułatwić zarządzanie konfiguracją i śledzenie zmian dla migrowanych przepływów pracy, można użyć usługi Azure Automation.
- Gdy wymuszanie proces zarządzania zmianami, połączyć zmiany platformy Azure, można użyć dzienników inspekcji dzienniki, aby prawdopodobnie (lub nie) istniejącego żądania zmiany. Tak, że jeśli widzisz zmian bez odpowiedniego żądania zmiany, można zbadać, co poszło źle w procesie.

Platforma Azure oferuje rozwiązania Change Tracking w usłudze Azure automation:

- Rozwiązanie umożliwia śledzenie zmian do oprogramowania Windows i Linux i pliki, klucze rejestru Windows, usługi Windows oraz demony systemu Linux.
- Zmiany na monitorowanych serwerach są wysyłane do usługi Azure Monitor w chmurze służące do przetwarzania.
- Logika jest stosowana do odebranych danych i usługi w chmurze rejestruje dane.
- Na pulpicie nawigacyjnym śledzenia zmian możesz łatwo zobaczyć zmiany, które zostały wprowadzone w ramach infrastruktury serwera.


![Zarządzanie zmianami](./media/migrate-best-practices-security-management/change.png)
*Zarządzanie zmianami*

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/automation/automation-change-tracking) śledzenie zmian.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/automation/automation-intro) możliwości usługi Azure Automation.




## <a name="next-steps"></a>Kolejne kroki 

Przejrzyj najlepsze rozwiązania:


- [Najlepsze praktyki](migrate-best-practices-networking.md) sieci po zakończeniu migracji.
- [Najlepsze praktyki](migrate-best-practices-costs.md) usługi cost management po migracji.








