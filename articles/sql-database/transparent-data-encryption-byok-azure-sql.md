---
title: TDE Azure Key Vault Integration lub Bring Your Own Key (BYOK) — Azure SQL Database | Microsoft Docs
description: Bring Your Own Key (BYOK) obsługa Transparent Data Encryption (TDE) z Azure Key Vault dla SQL Database i magazynu danych. TDE z BYOK Omówienie, korzyści, jak to działa, zagadnienia i zalecenia.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 07/18/2019
ms.openlocfilehash: 095ecc360e5639a5d47dff4bc4675fc237cf81da
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348925"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Usługa Azure SQL Transparent Data Encryption z kluczami zarządzanymi przez klienta w programie Azure Key Vault: Obsługa Bring Your Own Key

[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) z integracją Azure Key Vault umożliwia zaszyfrowanie klucza szyfrowania bazy danych (.) przy użyciu klucza asymetrycznego zarządzanego przez klienta o nazwie funkcja ochrony TDE. Jest to również ogólnie określane jako Bring Your Own Key (BYOK) obsługa Transparent Data Encryption.  W scenariuszu BYOK funkcja ochrony TDE jest przechowywana w Azure Key Vaultach należących do klienta i [](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)zarządzanych przez chmurę systemu zarządzania Kluczami na platformie Azure. Funkcja ochrony TDE może być [generowana](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) przez Magazyn kluczy lub [transferowana](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) do magazynu kluczy z urządzenia modułu HSM Premium. TDE, który jest przechowywany na stronie rozruchowej bazy danych, jest szyfrowany i odszyfrowywany przez funkcję ochrony TDE przechowywaną w Azure Key Vault, która nigdy nie opuszcza.  SQL Database musi mieć przyznane uprawnienia do magazynu kluczy należącego do klienta, aby odszyfrować i zaszyfrować klucz szyfrowania danych. Jeśli uprawnienia logicznego serwera SQL do magazynu kluczy zostaną odwołane, baza danych będzie niedostępna, nastąpi odmowa połączeń i wszystkie dane są szyfrowane. W przypadku Azure SQL Database funkcja ochrony TDE jest ustawiana na poziomie logicznego serwera SQL i jest dziedziczona przez wszystkie bazy danych skojarzone z tym serwerem. W przypadku [wystąpienia zarządzanego Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)funkcja ochrony TDE jest ustawiana na poziomie wystąpienia i jest dziedziczona przez wszystkie zaszyfrowane bazy danych w tym wystąpieniu. Termin " *serwer* " dotyczy zarówno serwera, jak i wystąpienia w tym dokumencie, chyba że określono inaczej.

> [!NOTE]
> Transparent Data Encryption z integracją Azure Key Vault (Bring Your Own Key) dla Azure SQL Database zarządzanego wystąpienia jest w wersji zapoznawczej.


Dzięki funkcji TDE z integracją Azure Key Vault użytkownicy mogą kontrolować zadania zarządzania kluczami, w tym kluczowe rotacje, uprawnienia do magazynu kluczy, kopie zapasowe kluczy, a także włączać inspekcje/raportowanie we wszystkich funkcjach Azure Key Vault ochrony TDE. Key Vault udostępnia centralne zarządzanie kluczami, korzysta ze ściśle monitorowanych sprzętowych modułów zabezpieczeń (sprzętowych modułów zabezpieczeń) i umożliwia rozdzielenie obowiązków między zarządzaniem kluczami i danymi, aby zapewnić zgodność z zasadami zabezpieczeń.  

TDE z integracją Azure Key Vault zapewnia następujące korzyści:

- Większa przejrzystość i kontrola Szczegółowa z możliwością samodzielnego zarządzania funkcją ochrony TDE
- Możliwość odwoływania uprawnień w dowolnym momencie w celu renderowania niedostępności bazy danych
- Centralne zarządzanie TDEmi ochroną (wraz z innymi kluczami i wpisami tajnymi używanymi w innych usługach platformy Azure) przez hostowanie ich w Key Vault
- Rozdzielenie obowiązków związanych z zarządzaniem kluczami i danymi w organizacji w celu obsługi rozdzielenia obowiązków
- Większe zaufanie od własnych klientów, ponieważ Key Vault jest zaprojektowana tak, aby firma Microsoft nie widziała ani nie wyodrębni kluczy szyfrowania.
- Obsługa rotacji kluczy

> [!IMPORTANT]
> Dla osób korzystających z TDE zarządzanych przez usługę, którzy chcą rozpocząć korzystanie z Key Vault, TDE pozostanie włączona w trakcie procesu przełączania do ochrony TDE w programie Key Vault. Nie ma żadnych przestojów ani ponownego szyfrowania plików bazy danych. Przełączenie z klucza zarządzanego przez usługę do klucza Key Vault tylko wymaga ponownego szyfrowania klucza szyfrowania bazy danych (w tym przypadku jest to szybka i w trybie online).

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>Jak działa TDE z obsługą integracji Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

![Uwierzytelnianie serwera do Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Gdy TDE jest najpierw skonfigurowany do korzystania z funkcji ochrony TDE z Key Vault, serwer wysyła do Key Vault żądania przewinięcia klucza, który jest używany przez program. Key Vault zwraca zaszyfrowany klucz szyfrowania bazy danych, który jest przechowywany w bazie danych użytkownika.  

> [!IMPORTANT]
> Należy pamiętać, że **gdy funkcja ochrony TDE jest przechowywana w Azure Key Vault, nie pozostawia Azure Key Vault**. Serwer może wysyłać tylko żądania operacji Key do materiału klucza ochrony TDE w ramach Key Vault, a **nigdy nie uzyskują dostępu do pamięci podręcznej funkcji TDE i nie są w niej dostępne**. Administrator Key Vault ma prawo do odwoływania uprawnień Key Vault serwera w dowolnym momencie, w którym wystąpiła odmowa wszystkich połączeń z bazą danych.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Wytyczne dotyczące konfigurowania TDE za pomocą Azure Key Vault

### <a name="general-guidelines"></a>Ogólne wskazówki

- Upewnij się, że Azure Key Vault i Azure SQL Database/zarządzane wystąpienie ma należeć do tej samej dzierżawy.  Magazyn kluczy między dzierżawcami i interakcje serwera **nie są obsługiwane**.
- Jeśli planujesz przeniesienie dzierżawy, TDE z AKV będzie musiała zostać ponownie skonfigurowana, Dowiedz się więcej o [przenoszeniu zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Podczas konfigurowania TDE z Azure Key Vault należy wziąć pod uwagę obciążenie umieszczane w magazynie kluczy przez powtarzające się operacje zawijania/odpakowania. Na przykład, ponieważ wszystkie bazy danych skojarzone z serwerem SQL Database korzystają z tej samej funkcji ochrony TDE, przełączenie w tryb failover tego serwera spowoduje wyzwolenie w postaci wielu kluczowych operacji związanych z magazynem, ponieważ na serwerze znajdują się bazy danych. W oparciu o nasze doświadczenie i udokumentowane [limity usługi magazynu kluczy](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)zalecamy 500 skojarzenie baz danych w warstwie standardowa/Ogólnego przeznaczenia lub 200 Premium/krytyczne dla działania firmy z jednym Azure Key Vault w ramach jednej subskrypcji w celu zapewnienia spójnej wysokiej wydajności dostępność podczas uzyskiwania dostępu do funkcji ochrony TDE w magazynie.
- Zalecane: Zachowaj kopię funkcji ochrony TDE lokalnie.  Wymaga to urządzenia HSM do lokalnego utworzenia funkcji ochrony TDE i klucza systemu Escrow do przechowywania lokalnej kopii funkcji ochrony TDE.  Dowiedz się, [jak przenieść klucz z lokalnego modułu HSM do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).


### <a name="guidelines-for-configuring-azure-key-vault"></a>Wskazówki dotyczące konfigurowania Azure Key Vault

- Utwórz magazyn kluczy z włączoną opcją [trwałe usuwanie](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) i przeczyszczanie ochrony, aby chronić przed utratą danych w przypadku przypadkowego klucza — lub magazynu kluczy — usunięcie.  Musisz użyć [programu PowerShell, aby włączyć właściwość "Soft-Delete"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) w magazynie kluczy (Ta opcja nie jest jeszcze dostępna w portalu AKV, ale jest wymagana przez usługę Azure SQL):  
  - Nietrwałe usunięte zasoby są przechowywane przez określony czas, 90 dni, chyba że zostaną odzyskane lub usunięte.
  - Akcje odzyskania i **przeczyszczania** mają własne uprawnienia skojarzone z zasadami dostępu magazynu kluczy.
- Ustaw blokadę zasobów w magazynie kluczy, aby kontrolować, kto może usunąć ten zasób krytyczny i pomóc w zapobieganiu przypadkowej lub nieautoryzowanej usunięciu.  [Dowiedz się więcej o blokadach zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Przyznaj serwerowi SQL Database dostęp do magazynu kluczy przy użyciu tożsamości Azure Active Directory (Azure AD).  W przypadku korzystania z interfejsu użytkownika portalu tożsamość usługi Azure AD zostanie utworzona automatycznie, a do serwera są przyznawane uprawnienia dostępu do magazynu kluczy.  Aby skonfigurować TDE z BYOK przy użyciu programu PowerShell, należy utworzyć tożsamość usługi Azure AD, a ukończenie powinno być zweryfikowane. Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące korzystania z programu PowerShell, zobacz [Configure TDE with BYOK](transparent-data-encryption-byok-azure-sql-configure.md) i [Configure TDE with BYOK for Managed instance](https://aka.ms/sqlmibyoktdepowershell) .

   > [!NOTE]
   > Jeśli tożsamość usługi Azure AD **zostanie przypadkowo usunięta lub uprawnienia serwera zostaną odwołane** za pomocą zasad dostępu magazynu kluczy lub przypadkowo przez przeniesienie serwera do innej dzierżawy, serwer utraci dostęp do magazynu kluczy i TDE zaszyfrowane bazy danych będzie niedostępny, a logowania są odrzucane do momentu przywrócenia tożsamości i uprawnień usługi Azure AD serwera logicznego.  

- W przypadku używania zapór i sieci wirtualnych z Azure Key Vault należy zezwolić zaufanym usługom firmy Microsoft na ominięcie tej zapory. Wybierz opcję tak.

   > [!NOTE]
   > Jeśli TDE zaszyfrowane bazy danych SQL utracą dostęp do magazynu kluczy, ponieważ nie mogą ominąć zapory, bazy danych będą niedostępne i logowania do momentu przywrócenia uprawnień do obejścia zapory.

- Włącz inspekcję i raportowanie dla wszystkich kluczy szyfrowania: Key Vault udostępnia dzienniki, które można łatwo wstrzyknąć do innych narzędzi do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM). Pakiet Operations Management Suite (OMS) [log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) to jeden przykład usługi, która jest już zintegrowana.
- Aby zapewnić wysoką dostępność zaszyfrowanych baz danych, należy skonfigurować każdy serwer SQL Database z dwoma magazynami kluczy platformy Azure, które znajdują się w różnych regionach.


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Wytyczne dotyczące konfigurowania funkcji ochrony TDE (klucz asymetryczny)

- Utwórz klucz szyfrowania lokalnie na urządzeniu lokalnego modułu HSM. Upewnij się, że jest to asymetryczny klucz modułu 2048 HSM RSA 2048 lub RSA, więc Storable w Azure Key Vault.
- W kluczowym systemie Escrow należy zalogować klucz.  
- Zaimportuj plik klucza szyfrowania (PFX,. BYOK lub. Backup) do Azure Key Vault.

   > [!NOTE]
   > W celach testowych istnieje możliwość utworzenia klucza z Azure Key Vault, ale nie będzie można wypróbować tego klucza, ponieważ klucz prywatny nigdy nie opuszcza magazynu kluczy.  Należy zawsze tworzyć kopie zapasowe kluczy, które są używane do szyfrowania danych produkcyjnych, w przypadku utraty klucza (przypadkowego usunięcia w magazynie kluczy, wygaśnięcia itp.) skutkuje trwałą utratą danych.

- Jeśli używasz klucza z datą wygaśnięcia — Zaimplementuj system ostrzegania o wygasaniu, aby obrócić klucz przed jego wygaśnięciem: **po wygaśnięciu klucza zaszyfrowane bazy danych utracą dostęp do funkcji ochrony TDE i będą niedostępne** , a wszystkie logowania będą odrzucane do klucz został obrócony do nowego klucza i wybrany jako nowy klucz i domyślna funkcja ochrony TDE dla serwera logicznego SQL.
- Upewnij się, że klucz jest włączony i ma uprawnienia do wykonywania operacji *pobrania*, *zawijania klucza*i *depakowania kluczy* .
- Utwórz kopię zapasową klucza Azure Key Vault przed użyciem klucza w Azure Key Vault po raz pierwszy. Dowiedz się więcej na temat polecenia [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) .
- Utwórz nową kopię zapasową po każdym wprowadzeniu zmian w kluczu (na przykład Dodaj listy ACL, Dodaj Tagi, Dodaj atrybuty klucza).
- **Zachowaj poprzednie wersje** klucza w magazynie kluczy podczas obracania kluczy, aby można było przywrócić starsze kopie zapasowe bazy danych. Gdy funkcja ochrony TDE została zmieniona dla bazy danych, stare kopie zapasowe bazy danych **nie są aktualizowane** do korzystania z najnowszej funkcji ochrony TDE.  Każda kopia zapasowa wymaga ochrony TDE, która została utworzona przy użyciu w czasie przywracania. Rotacje kluczy można wykonać zgodnie z instrukcjami w [obszarze obróć transparent Data Encryption funkcję ochrony przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Zachowaj wszystkie poprzednio używane klucze w Azure Key Vault po zmianie z powrotem na klucze zarządzane przez usługę.  Dzięki temu można przywrócić kopie zapasowe bazy danych za pomocą funkcji ochrony TDE przechowywanych w Azure Key Vault.  Funkcje ochrony TDE utworzone przy użyciu Azure Key Vault muszą być utrzymywane, dopóki wszystkie zapisane kopie zapasowe nie zostaną utworzone przy użyciu kluczy zarządzanych przez usługę.  
- Wykonaj odzyskiwalne kopie zapasowe tych kluczy za pomocą polecenia [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).
- Aby usunąć potencjalnie złamany klucz podczas zdarzenia zabezpieczeń bez ryzyka utraty danych, wykonaj kroki opisane w sekcji [usuwanie potencjalnie złamanego klucza](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

### <a name="guidelines-for-monitoring-the-tde-with-azure-key-vault-configuration"></a>Wskazówki dotyczące monitorowania TDE z konfiguracją Azure Key Vault

Jeśli logiczny serwer SQL utraci dostęp do funkcji ochrony TDE zarządzanej przez klienta w Azure Key Vault, baza danych odmówi wszystkich połączeń i nie będzie dostępna w Azure Portal.  Najczęstszymi przyczynami tego są:
- Przypadkowo usunięto Magazyn kluczy lub za zaporą
- Klucz magazynu kluczy przypadkowo usunięty, wyłączony lub wygasł
- Przypadkowo usunięto wystąpienie klasy logicznej wystąpienia SQL Server
- Uprawnienia specyficzne dla klucza dla identyfikatora AppId wystąpienia logicznego SQL Server

 > [!NOTE]
 > Baza danych zostanie automatycznie zaleczona i przestanie być w trybie online, jeśli dostęp do funkcji ochrony TDE zarządzanej przez klienta zostanie przywrócony w ciągu 48 godzin.  Jeśli baza danych jest niedostępna z powodu sporadycznej awarii sieci, nie jest wymagana żadna akcja, a bazy danych zostaną automatycznie przywrócone do trybu online.
  
- Więcej informacji o rozwiązywaniu problemów z istniejącymi konfiguracjami można znaleźć w temacie [Rozwiązywanie problemów](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde)

- Aby monitorować stan bazy danych i włączyć alert o utracie dostępu do funkcji ochrony TDE, skonfiguruj następujące funkcje platformy Azure:
    - [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Niedostępna baza danych, która utraciła dostęp do funkcji ochrony TDE, będzie wyświetlana jako "niedostępna" po odmowie pierwszego połączenia z bazą danych.
    - [Dziennik aktywności](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) , gdy dostęp do funkcji ochrony TDE w magazynie kluczy zarządzanych przez klienta nie powiedzie się, wpisy są dodawane do dziennika aktywności.  Utworzenie alertów dla tych zdarzeń umożliwi przywrócenie dostępu tak szybko, jak to możliwe.
    - [Grupy akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) można definiować w celu wysyłania powiadomień i alertów na podstawie preferencji, np. wiadomości E-mail/SMS/wypychania/głosu, aplikacji logiki, elementu webhook, narzędzia ITSM lub elementu Runbook usługi Automation.
    

## <a name="high-availability-geo-replication-and-backup--restore"></a>Wysoka dostępność, replikacja geograficzna i tworzenie kopii zapasowej/przywracanie

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

Sposób konfigurowania wysokiej dostępności przy użyciu Azure Key Vault zależy od konfiguracji bazy danych i serwera SQL Database, a poniżej przedstawiono zalecane konfiguracje dla dwóch odrębnych przypadków.  Pierwszy przypadek to autonomiczna baza danych lub SQL Database serwer bez skonfigurowanej nadmiarowości geograficznej.  Drugi przypadek to baza danych lub serwer SQL Database skonfigurowany z grupami trybu failover lub nadmiarowością geograficzną, w której należy zapewnić, że każda kopia nadmiarowa geograficznie ma lokalne Azure Key Vault w grupie trybu failover, aby zapewnić pracę w trybie failover.

W pierwszym przypadku, jeśli wymagana jest wysoka dostępność bazy danych i serwera SQL Database bez skonfigurowanej nadmiarowości geograficznej, zdecydowanie zaleca się skonfigurowanie serwera tak, aby korzystał z dwóch różnych magazynów kluczy w dwóch różnych regionach z tym samym kluczem. Można to zrobić, tworząc TDE funkcję ochrony przy użyciu podstawowego Key Vault, co znajduje się w tym samym regionie co serwer SQL Database i klonowanie klucza do magazynu kluczy w innym regionie świadczenia usługi Azure, tak aby serwer miał dostęp do drugiego magazynu kluczy, który powinien być podstawowym w magazynie kluczy wystąpi awaria, podczas gdy baza danych jest uruchomiona. Użyj polecenia cmdlet Backup-AzKeyVaultKey, aby pobrać klucz w szyfrowanym formacie z magazynu kluczy podstawowych, a następnie użyć polecenia cmdlet Restore-AzKeyVaultKey i określić Magazyn kluczy w drugim regionie.

![Jednoserwerowa HA i bez lokalizacji geograficznej Dr](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Jak skonfigurować program Geo-DR z Azure Key Vault

Aby zapewnić wysoką dostępność dla zaszyfrowanych baz danych, należy skonfigurować nadmiarowe magazyny kluczy platformy Azure w oparciu o istniejące lub wymagane SQL Database grupy trybu failover lub aktywne wystąpienia replikacji geograficznej.  Każdy serwer z replikacją geograficzną wymaga oddzielnego magazynu kluczy, który musi znajdować się na serwerze w tym samym regionie świadczenia usługi Azure. Jeśli podstawowa baza danych stanie się niedostępna z powodu przestoju w jednym regionie, a wyzwolenie trybu failover zostanie wyzwolone, pomocnicza baza danych będzie mogła przejąć Magazyn kluczy pomocniczych.

W przypadku replikowanych geograficznie baz danych usługi Azure SQL Database wymagana jest następująca Azure Key Vault konfiguracja:

- Jedna podstawowa baza danych z magazynem kluczy w regionie i jedna pomocnicza baza danych z magazynem kluczy w regionie.
- Wymagana jest co najmniej jedna pomocnicza, obsługiwane są maksymalnie cztery pomocnicze.
- Pomocnicze serwery pomocnicze (łańcucha) nie są obsługiwane.

W poniższej sekcji opisano kroki instalacji i konfiguracji w bardziej szczegółowy sposób.

### <a name="azure-key-vault-configuration-steps"></a>Azure Key Vault kroki konfiguracji

- Zainstaluj [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Utwórz dwa magazyny kluczy platformy Azure w dwóch różnych regionach przy użyciu [programu PowerShell, aby włączyć właściwość "Soft-Delete"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) w magazynach kluczy (Ta opcja nie jest jeszcze dostępna w portalu AKV, ale jest wymagana przez program SQL).
- Oba magazyny kluczy platformy Azure muszą znajdować się w dwóch regionach dostępnych w tej samej lokalizacji geograficznej platformy Azure w celu tworzenia kopii zapasowych i przywracania kluczy do pracy.  Jeśli potrzebujesz dwóch magazynów kluczy, które mają znajdować się w różnych georegionyach w celu spełnienia wymagań geograficznych SQL-DR, postępuj zgodnie z [procesem BYOK](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) , który umożliwia importowanie kluczy z lokalnego modułu HSM.
- Utwórz nowy klucz w pierwszym magazynie kluczy:  
  - Klucz RSA/RSA-HSM 2048
  - Brak dat wygaśnięcia
  - Klucz jest włączony i ma uprawnienia do wykonywania operacji pobrania, zawijania klucza i rozwinięcia klucza
- Wykonaj kopię zapasową klucza podstawowego i Przywróć klucz do drugiego magazynu kluczy.  Zobacz [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) i [Restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey).

### <a name="azure-sql-database-configuration-steps"></a>Azure SQL Database kroki konfiguracji

Poniższe kroki konfiguracji różnią się od tego, czy zaczynają się od nowego wdrożenia SQL, czy pracy z już istniejącym wdrożeniem Geo-DR języka SQL.  Najpierw należy utworzyć konspekt kroków konfiguracyjnych dla nowego wdrożenia, a następnie wyjaśnić sposób przypisywania TDE chronionych w Azure Key Vault do istniejącego wdrożenia, które ma już ustanowione łącze Geo-DR.

**Kroki dla nowego wdrożenia**:

- Utwórz dwa serwery SQL Database w tych samych dwóch regionach co poprzednio utworzone magazyny kluczy.
- Wybierz okienko TDE serwera SQL Database i dla każdego serwera SQL Database:  
  - Wybierz AKV w tym samym regionie
  - Wybierz klucz, który ma być używany jako funkcja ochrony TDE — każdy serwer będzie używać lokalnej kopii funkcji ochrony TDE.
  - W tym celu w portalu zostanie utworzony [identyfikator appid](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) dla serwera SQL Database, który jest używany do przypisywania uprawnień serwera SQL Database w celu uzyskania dostępu do magazynu kluczy — nie usuwaj tej tożsamości. Dostęp można odwołać, usuwając uprawnienia w Azure Key Vault zamiast serwera SQL Database, który jest używany do przypisywania uprawnień serwera SQL Database w celu uzyskania dostępu do magazynu kluczy.
- Utwórz podstawową bazę danych.
- Postępuj zgodnie ze [wskazówkami dotyczącymi aktywnej replikacji](sql-database-geo-replication-overview.md) geograficznej, aby ukończyć ten scenariusz. ten krok spowoduje utworzenie pomocniczej bazy danych.

![Grupy trybu failover i geograficznie](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> Należy upewnić się, że te same funkcje ochrony TDE są obecne w obu magazynach kluczy, przed przystąpieniem do nawiązania połączenia geograficznego między bazami danych.

**Kroki dla istniejącej bazy danych SQL z wdrożeniem Geo-Dr**:

Ponieważ serwery SQL Database już istnieją, a podstawowe i pomocnicze bazy danych są już przypisane, należy wykonać kroki konfigurowania Azure Key Vault w następującej kolejności:

- Zacznij od serwera SQL Database, który obsługuje pomocniczą bazę danych:
  - Przypisywanie magazynu kluczy znajdującego się w tym samym regionie
  - Przypisz funkcję ochrony TDE
- Teraz przejdź do serwera SQL Database, który hostuje podstawową bazę danych:
  - Wybierz tę samą funkcję ochrony TDE, która jest używana dla pomocniczej bazy danych

![Grupy trybu failover i geograficznie](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

> [!NOTE]
> Podczas przypisywania magazynu kluczy do serwera należy zacząć od serwera pomocniczego.  W drugim kroku przypiszesz Magazyn kluczy do serwera podstawowego i zaktualizujesz funkcję ochrony TDE, link Geo-DR będzie nadal działał, ponieważ na tym etapie funkcja ochrony TDE używana przez zreplikowaną bazę danych będzie dostępna dla obu serwerów.

Przed włączeniem TDE z kluczami zarządzanymi przez klienta w Azure Key Vault dla SQL Database scenariusza geograficznego odzyskiwania po awarii należy utworzyć i obsługiwać dwa magazyny kluczy platformy Azure z identyczną zawartością w tych samych regionach, które będą używane do SQL Database replikacji geograficznej.  "Identyczna zawartość" oznacza, że oba magazyny kluczy muszą zawierać kopie tych samych funkcji ochrony TDE, tak aby oba serwery miały dostęp do ochrony TDE używanych przez wszystkie bazy danych.  W przód jest wymagane utrzymywanie obu magazynów kluczy, co oznacza, że muszą zawierać te same kopie TDE ochrony po rotacji kluczy, utrzymywać stare wersje kluczy używanych dla plików dziennika lub kopii zapasowych, funkcje ochrony TDE muszą zachować te same właściwości klucza i klucz. magazyny muszą mieć te same uprawnienia dostępu do bazy danych SQL.  

Postępuj zgodnie z instrukcjami w temacie [Aktywna replikacja geograficzna](sql-database-geo-replication-overview.md) , aby przetestować i wyzwolić tryb failover, co powinno być regularnie wykonywane w celu potwierdzenia, że uprawnienia dostępu do bazy danych SQL są utrzymywane w obu magazynach kluczy.

### <a name="backup-and-restore"></a>Wykonywanie kopii zapasowych i przywracanie

Po zaszyfrowaniu bazy danych za pomocą usługi TDE przy użyciu klucza z Key Vault wszystkie wygenerowane kopie zapasowe są również szyfrowane za pomocą tej samej funkcji ochrony TDE.

Aby przywrócić kopię zapasową zaszyfrowaną przy użyciu funkcji ochrony TDE z Key Vault, upewnij się, że kluczowy materiał nadal znajduje się w oryginalnym magazynie w ramach pierwotnej nazwy klucza. Gdy funkcja ochrony TDE została zmieniona dla bazy danych, stare kopie zapasowe bazy danych **nie są** aktualizowane do korzystania z najnowszej funkcji ochrony TDE. Dlatego zalecamy zachowanie wszystkich starych wersji funkcji ochrony TDE w Key Vault, aby można było przywrócić kopie zapasowe bazy danych.

Jeśli klucz, który może być wymagany do przywrócenia kopii zapasowej, nie jest już w oryginalnym magazynie kluczy, zwracany jest następujący komunikat o błędzie: "Serwer `<Servername>` docelowy nie ma dostępu do wszystkich identyfikatorów URI AKV utworzonych między \<#1 znacznika czasu > \<i sygnatury czasowej #2 >. Spróbuj ponownie wykonać operację po przywróceniu wszystkich identyfikatorów URI AKV ".

Aby rozwiązać ten problem, uruchom polecenie cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) , aby zwrócić listę kluczy z Key Vault, które zostały dodane do serwera (chyba że zostały usunięte przez użytkownika). Aby mieć pewność, że można przywrócić wszystkie kopie zapasowe, upewnij się, że serwer docelowy dla kopii zapasowej ma dostęp do wszystkich tych kluczy.

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Aby dowiedzieć się więcej na temat odzyskiwania kopii zapasowej SQL Database, zobacz [odzyskiwanie bazy danych Azure SQL Database](sql-database-recovery-using-backups.md). Aby dowiedzieć się więcej na temat odzyskiwania kopii zapasowej SQL Data Warehouse, zobacz [odzyskiwanie Azure SQL Data Warehouse](../sql-data-warehouse/backup-and-restore.md).

Dodatkowe zagadnienia dotyczące kopii zapasowych plików dziennika: Kopie zapasowe plików dziennika pozostaną zaszyfrowane przy użyciu oryginalnego modułu szyfrującego TDE, nawet jeśli funkcja ochrony TDE została obrócona i baza danych używa teraz nowej funkcji ochrony TDE.  W czasie przywracania do przywrócenia bazy danych są konieczne oba klucze.  Jeśli plik dziennika używa funkcji ochrony TDE przechowywanej w Azure Key Vault, ten klucz będzie wymagany w czasie przywracania, nawet jeśli baza danych została zmieniona tak, aby korzystała z usługi TDE zarządzanej w międzyczasie.
