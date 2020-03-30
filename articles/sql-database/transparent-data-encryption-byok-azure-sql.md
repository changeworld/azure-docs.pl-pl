---
title: Szyfrowanie przezroczystych danych zarządzanych przez klienta (TDE)
description: Zabierz własny klucz (BYOK) dla przezroczystego szyfrowania danych (TDE) z usługą Azure Key Vault dla bazy danych SQL i usługi Azure Synapse. TDE z przeglądem BYOK, korzyści, jak to działa, zagadnienia i zalecenia.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 462326fb16663a6f25ff4b51ea11791201086fd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528732"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Przezroczyste szyfrowanie danych sql platformy Azure z kluczem zarządzanym przez klienta

Azure SQL [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) z kluczem zarządzanym przez klienta umożliwia scenariusz Bring Your Own Key (BYOK) dla ochrony danych w spoczynku i umożliwia organizacjom implementowanie rozdzielania obowiązków w zarządzaniu kluczami i danymi. Dzięki zarządzanej przez klienta przezroczystemu szyfrowaniu danych klient jest odpowiedzialny za zarządzanie cyklem życia kluczy (tworzenie kluczy, przekazywanie, obracanie, usuwanie), uprawnienia do używania kluczy i inspekcja operacji na kluczach.

W tym scenariuszu klucz używany do szyfrowania klucza szyfrowania bazy danych (DEK), o nazwie TDE protector, jest kluczem asymetrycznym zarządzanym przez klienta przechowywanym w należącym do klienta i zarządzanym przez klienta [usłudze Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)— systemie zarządzania kluczami zewnętrznymi opartym na chmurze. Usługa Key Vault jest wysoce dostępnym i skalowalnym bezpiecznym magazynem kluczy kryptograficznych RSA, opcjonalnie wspieranym przez sprawdzone moduły zabezpieczeń sprzętu (HSM) fips 140-2 poziomu 2. Nie zezwala na bezpośredni dostęp do przechowywanego klucza, ale zapewnia usługi szyfrowania/odszyfrowywania przy użyciu klucza do autoryzowanych jednostek. Klucz może być generowany przez magazyn kluczy, importowany lub [przesyłany do magazynu kluczy z urządzenia HSM na przedm.](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)

W przypadku usługi Azure SQL Database i Azure Synapse ochrona TDE jest ustawiona na poziomie serwera logicznego i jest dziedziczona przez wszystkie zaszyfrowane bazy danych skojarzone z tym serwerem. W przypadku wystąpienia zarządzanego sql platformy Azure ochrona TDE jest ustawiona na poziomie wystąpienia i jest dziedziczona przez wszystkie zaszyfrowane bazy danych w tym wystąpieniu. Termin *serwer* odnosi się zarówno do serwera logicznego bazy danych SQL, jak i do wystąpienia zarządzanego w tym dokumencie, chyba że określono inaczej.

> [!IMPORTANT]
> Dla osób korzystających z usług zarządzanych TDE, którzy chcieliby rozpocząć korzystanie z TDE zarządzanego przez klienta, dane pozostają zaszyfrowane podczas procesu przełączania i nie ma przestojów ani ponownego szyfrowania plików bazy danych. Przejście z klucza zarządzanego przez usługę na klucz zarządzany przez klienta wymaga tylko ponownego szyfrowania pliku DEK, który jest szybką i online operacją.

## <a name="benefits-of-the-customer-managed-tde"></a>Korzyści z zarządzanego przez klienta TDE

TDE zarządzane przez klienta zapewnia klientowi następujące korzyści:

- Pełna i szczegółowa kontrola nad użytkowaniem i zarządzaniem ochroną TDE;

- Przejrzystość użycia ochrony TDE;

- Możliwość realizacji rozdzielenia obowiązków w zarządzaniu kluczami i danymi w organizacji;

- Administrator usługi Key Vault może odwołać uprawnienia dostępu do kluczy, aby szyfrowana baza danych była niedostępna;

- Centralne zarządzanie kluczami w AKV;

- Większe zaufanie od klientów końcowych, ponieważ AKV został zaprojektowany w taki sposób, że firma Microsoft nie może zobaczyć ani wyodrębnić kluczy szyfrowania;

## <a name="how-customer-managed-tde-works"></a>Jak działa TDE zarządzane przez klienta

![Konfiguracja i funkcjonowanie tde zarządzanego przez klienta](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Aby serwer mógł używać ochrony TDE przechowywanej w AKV do szyfrowania usługi DEK, administrator magazynu kluczy musi przyznać następujące prawa dostępu do serwera przy użyciu jego unikatowej tożsamości AAD:

- **get** - do pobierania części publicznej i właściwości klucza w magazynie kluczy

- **wrapKey** - aby móc chronić (szyfrować) DEK

- **unwrapKey** - aby móc odchować (odszyfrować) DEK

Administrator magazynu kluczy może również [włączyć rejestrowanie zdarzeń inspekcji magazynu kluczy,](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)dzięki czemu można je później poddać inspekcji.

Gdy serwer jest skonfigurowany do używania ochrony TDE z AKV, serwer wysyła DEK każdej bazy danych z obsługą TDE do magazynu kluczy w celu szyfrowania. Magazyn kluczy zwraca zaszyfrowany kod DEK, który jest następnie przechowywany w bazie danych użytkowników.

W razie potrzeby serwer wysyła chroniony plik DEK do magazynu kluczy w celu odszyfrowania.

Audytorzy mogą używać usługi Azure Monitor do przeglądania dzienników inspekcji rejestru magazynu kluczy, jeśli rejestrowanie jest włączone.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Wymagania dotyczące konfigurowania TDE zarządzanego przez klienta

### <a name="requirements-for-configuring-akv"></a>Wymagania dotyczące konfigurowania AKV

- Magazyn kluczy i wystąpienie zarządzane bazy danych/zarządzanej bazy danych SQL muszą należeć do tej samej dzierżawy usługi Azure Active Directory. Interakcje między dzierżawcami i interakcjami z serwerem nie są obsługiwane. Aby przenieść zasoby później, TDE z AKV będzie musiał zostać ponownie skonfigurowany. Dowiedz się więcej o [przenoszeniu zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- [Funkcja usuwania nietrwałego](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) musi być włączona w magazynie kluczy, aby chronić przed utratą danych przypadkowe usunięcie klucza (lub magazynu kluczy). Nieuiszczone zasoby są przechowywane przez 90 dni, chyba że odzyskane lub usunięte przez klienta w międzyczasie. Akcje *odzyskiwania* i *przeczyszczania* mają własne uprawnienia skojarzone z zasadami dostępu do magazynu kluczy. Funkcja usuwania nietrwałego jest domyślnie wyłączona i może być włączona za pośrednictwem [programu PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) lub [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete). Nie można włączyć za pośrednictwem witryny Azure portal.  

- Udziel dostępu do serwera bazy danych SQL lub wystąpienia zarządzanego do magazynu kluczy (get, wrapKey, unwrapKey) przy użyciu jego tożsamości usługi Azure Active Directory. Podczas korzystania z witryny Azure portal tożsamość usługi Azure AD jest tworzony automatycznie. Podczas korzystania z programu PowerShell lub interfejsu wiersza polecenia tożsamości usługi Azure AD musi być jawnie utworzony i ukończenia powinny być weryfikowane. Zobacz [Konfigurowanie TDE z byok](transparent-data-encryption-byok-azure-sql-configure.md) i [konfigurowanie TDE z BYOK dla wystąpienia zarządzanego](https://aka.ms/sqlmibyoktdepowershell) szczegółowe instrukcje krok po kroku podczas korzystania z programu PowerShell.

- Podczas korzystania z zapory z AKV, należy włączyć opcję *Zezwalaj zaufanym usługom firmy Microsoft na ominięcie zapory*.

### <a name="requirements-for-configuring-tde-protector"></a>Wymagania dotyczące konfigurowania ochrony TDE

- Ochrona TDE może być tylko asymetryczna, klucz RSA 2048 lub RSA HSM 2048.

- Data aktywacji klucza (jeśli jest ustawiona) musi być datą i godziną w przeszłości. Data wygaśnięcia (jeśli jest ustawiona) musi być datą i godziną przyszłą.

- Klucz musi być w stanie *Włączone.*

- Jeśli importujesz istniejący klucz do magazynu kluczy, upewnij się, że jest on udostępniany w obsługiwanych formatach plików (.pfx, .byok lub .backup).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Zalecenia dotyczące konfigurowania TDE zarządzanego przez klienta

### <a name="recommendations-when-configuring-akv"></a>Zalecenia dotyczące konfigurowania AKV

- Skojarz co najwyżej 500 baz danych ogólnego przeznaczenia lub 200 krytycznych dla firmy z magazynem kluczy w jednej subskrypcji, aby zapewnić wysoką dostępność, gdy serwer uzyskuje dostęp do ochrony TDE w magazynie kluczy. Liczby te są oparte na doświadczeniu i udokumentowane w [limitach usług magazynu kluczy](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits). Celem w tym miejscu jest zapobieganie problemom po pracy awaryjnej serwera, ponieważ spowoduje to wyzwolić tyle operacji klucza w przechowalni, ile w tym serwerze znajdują się bazy danych.

- Ustaw blokadę zasobu w magazynie kluczy, aby kontrolować, kto może usunąć ten krytyczny zasób i zapobiec przypadkowemu lub nieautoryzowanemu usunięciu. Dowiedz się więcej o [blokadach zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Włącz inspekcję i raportowanie wszystkich kluczy szyfrowania: Magazyn kluczy udostępnia dzienniki, które można łatwo wstrzyknąć do innych narzędzi do zarządzania informacjami o zabezpieczeniach i zdarzeniami. Usługa Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) jest jednym z przykładów usługi, która jest już zintegrowana.

- Połącz każdy serwer z dwoma magazynami kluczy, które znajdują się w różnych regionach i przechowują ten sam materiał klucza, aby zapewnić wysoką dostępność zaszyfrowanych baz danych. Oznacz tylko klucz z magazynu kluczy w tym samym regionie co ochrona TDE. System automatycznie przełączy się do magazynu kluczy w regionie zdalnym, jeśli wystąpi awaria wpływająca na magazyn kluczy w tym samym regionie.

### <a name="recommendations-when-configuring-tde-protector"></a>Zalecenia dotyczące konfigurowania ochrony TDE
- Zachowaj kopię ochrony TDE w bezpiecznym miejscu lub escrow go do usługi escrow.

- Jeśli klucz jest generowany w magazynie kluczy, utwórz kopię zapasową klucza przed użyciem klucza w AKV po raz pierwszy. Kopię zapasową można przywrócić tylko do usługi Azure Key Vault. Dowiedz się więcej o [poleceniu Backup-AzKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Utwórz nową kopię zapasową za każdym razem, gdy zostaną wprowadzone zmiany w kluczu (np. atrybuty klucza, znaczniki, listy ACL).

- **Zachowaj poprzednie wersje** klucza w magazynie kluczy podczas obracania kluczy, aby można było przywrócić starsze kopie zapasowe bazy danych. Po zmianie ochrony TDE dla bazy danych, stare kopie zapasowe bazy danych **nie są aktualizowane** w celu użycia najnowszego ochrony TDE. W czasie przywracania każda kopia zapasowa potrzebuje ochrony TDE, z której została zaszyfrowana w czasie tworzenia. Obrót klawiszy można wykonać zgodnie z instrukcjami podanymi w obszarze [Obróć ochronę szyfrowania danych przezroczystych za pomocą programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Zachowaj wszystkie wcześniej używane klucze w AKV nawet po przełączeniu na klucze zarządzane usługą. Zapewnia, że kopie zapasowe bazy danych mogą być przywracane za pomocą ochrony TDE przechowywane w AKV.  Ochrony TDE utworzone za pomocą usługi Azure Key Vault muszą być utrzymywane, dopóki wszystkie pozostałe przechowywane kopie zapasowe nie zostaną utworzone za pomocą kluczy zarządzanych przez usługę. Tworzenie kopii zapasowych tych kluczy do odzyskania za pomocą [narzędzia Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Aby usunąć potencjalnie naruszony klucz podczas incydentu zabezpieczeń bez ryzyka utraty danych, wykonaj kroki z [usuń potencjalnie naruszony klucz](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Niedostępny ochraniacz TDE

Gdy szyfrowanie danych przezroczyste jest skonfigurowane do używania klucza zarządzanego przez klienta, ciągły dostęp do ochrony TDE jest wymagany, aby baza danych pozostała w trybie online. Jeśli serwer utraci dostęp do zarządzanego przez klienta zabezpieczenia TDE w AKV, w ciągu maksymalnie 10 minut baza danych zacznie odrzucać wszystkie połączenia z odpowiednim komunikatem o błędzie i zmienić jego stan *na Niedostępny*. Jedyną czynnością dozwoloną w bazie danych w stanie Niedostępne jest usunięcie go.

> [!NOTE]
> Jeśli baza danych jest niedostępna z powodu przerwy w dostępie do sieci, nie jest wymagana żadna akcja, a bazy danych będą automatycznie wracać do trybu online.

Po przywróceniu dostępu do klucza, przywrócenie bazy danych do trybu online wymaga dodatkowego czasu i kroków, które mogą się różnić w zależności od czasu, jaki upłynął bez dostępu do klucza i rozmiaru danych w bazie danych:

- Jeśli dostęp do klucza zostanie przywrócony w ciągu 8 godzin, baza danych będzie automatycznie leczyć w ciągu następnej godziny.

- Jeśli dostęp do klucza zostanie przywrócony po ponad 8 godzinach, automatyczne leczenie nie jest możliwe, a przywrócenie bazy danych wymaga dodatkowych kroków w portalu i może zająć dużo czasu w zależności od rozmiaru bazy danych. Po powrocie bazy danych do trybu online wcześniej skonfigurowane ustawienia na poziomie serwera, takie jak konfiguracja [grupy trybu failover,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) historia przywracania punktu w czasie i tagi **zostaną utracone**. W związku z tym zaleca się wdrożenie systemu powiadomień, który pozwala zidentyfikować i rozwiązać podstawowe problemy z dostępem klucza w ciągu 8 godzin.

### <a name="accidental-tde-protector-access-revocation"></a>Przypadkowe cofnięcie dostępu obrońcy TDE

Może się zdarzyć, że ktoś z wystarczającymi prawami dostępu do magazynu kluczy przypadkowo wyłącza dostęp serwera do klucza przez:

- odwoływanie *uprawnień*magazynu kluczy , *wrapKey*, *unwrapKey* z serwera

- usuwanie klucza

- usuwanie magazynu kluczy

- zmienianie reguł zapory magazynu kluczy

- usuwanie tożsamości zarządzanej serwera w usłudze Azure Active Directory

Dowiedz się więcej [o typowych przyczynach niedostępności bazy danych.](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitorowanie tde zarządzanego przez klienta

Aby monitorować stan bazy danych i włączyć alerty o utracie dostępu do ochrony TDE, skonfiguruj następujące funkcje platformy Azure:
- [Kondycja zasobów platformy Azure](https://docs.microsoft.com/azure/service-health/resource-health-overview). Niedostępna baza danych, która utraciła dostęp do ochrony TDE, będzie ówczał akcji jako "Niedostępna" po odmówieniu pierwszego połączenia z bazą danych.
- [Dziennik aktywności,](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) gdy dostęp do ochrony TDE w magazynie kluczy zarządzanych przez klienta kończy się niepowodzeniem, wpisy są dodawane do dziennika działań.  Tworzenie alertów dla tych zdarzeń umożliwi przywrócenie dostępu tak szybko, jak to możliwe.
- [Grupy akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) mogą być definiowane w celu wysyłania powiadomień i alertów na podstawie twoich preferencji, takich jak e-mail/SMS/Push/Voice, Logic App, Webhook, ITSM lub Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Tworzenie kopii zapasowych i przywracanie bazy danych za pomocą tde zarządzanego przez klienta

Gdy baza danych jest szyfrowana za pomocą TDE przy użyciu klucza z usługi Key Vault, wszystkie nowo wygenerowane kopie zapasowe są również szyfrowane za pomocą tego samego ochrony TDE. Po zmianie ochrony TDE stare kopie zapasowe bazy danych **nie są aktualizowane** w celu użycia najnowszego ochrony TDE.

Aby przywrócić kopię zapasową zaszyfrowaną za pomocą ochrony TDE z usługi Key Vault, upewnij się, że materiał klucza jest dostępny dla serwera docelowego. W związku z tym zaleca się zachować wszystkie stare wersje ochrony TDE w magazynie kluczy, dzięki czemu można przywrócić kopie zapasowe bazy danych.

> [!IMPORTANT]
> W każdej chwili nie może być więcej niż jeden zestaw ochrony TDE dla serwera. Jest to klucz oznaczony jako "Ustaw klucz jako domyślny program ochrony TDE" w bloku portalu Azure. Jednak wiele dodatkowych kluczy mogą być połączone z serwerem bez oznaczania ich jako ochrony TDE. Klucze te nie są używane do ochrony DEK, ale mogą być używane podczas przywracania z kopii zapasowej, jeśli plik kopii zapasowej jest zaszyfrowany za pomocą klucza z odpowiednim odciskiem palca.

Jeśli klucz potrzebny do przywrócenia kopii zapasowej nie jest już dostępny dla serwera docelowego, podczas próby `<Servername>` przywracania zwracany jest następujący komunikat o \<błędzie: "Serwer docelowy nie \<ma dostępu do wszystkich identyfikatorów URI AKV utworzonych między sygnaturą czasową #1> a sygnaturą czasową #2>. Ponowij próbę wykonania operacji po przywróceniu wszystkich URI AKV."

Aby go złagodzić, uruchom polecenie cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) dla docelowego serwera logicznego bazy danych SQL lub [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) dla docelowego wystąpienia zarządzanego, aby zwrócić listę dostępnych kluczy i zidentyfikować brakujące. Aby upewnić się, że wszystkie kopie zapasowe mogą zostać przywrócone, upewnij się, że serwer docelowy przywracania ma dostęp do wszystkich potrzebnych kluczy. Te klucze nie muszą być oznaczone jako ochrona TDE.

Aby dowiedzieć się więcej o odzyskiwaniu kopii zapasowych dla bazy danych SQL, zobacz [Odzyskiwanie bazy danych SQL platformy Azure](sql-database-recovery-using-backups.md). Aby dowiedzieć się więcej o odzyskiwaniu kopii zapasowych dla puli SQL, zobacz [Odzyskiwanie puli SQL](../synapse-analytics/sql-data-warehouse/backup-and-restore.md). Aby zapoznać się z natywną kopią zapasową/przywracanie programu SQL Server za pomocą wystąpienia zarządzanego, zobacz [Szybki start: Przywracanie bazy danych do wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

Dodatkowe uwagi dotyczące plików dziennika: Kopia zapasowa plików dziennika pozostaje zaszyfrowana przy użyciu oryginalnego ochrony TDE, nawet jeśli został obrócony, a baza danych jest teraz przy użyciu nowego ochrony TDE.  W czasie przywracania oba klucze będą potrzebne do przywrócenia bazy danych.  Jeśli plik dziennika używa ochrony TDE przechowywane w usłudze Azure Key Vault, ten klucz będzie potrzebny w czasie przywracania, nawet jeśli baza danych została zmieniona do korzystania z usługi zarządzane TDE w międzyczasie.

## <a name="high-availability-with-customer-managed-tde"></a>Wysoka dostępność dzięki zarządzanej przez klienta technologii TDE

Nawet w przypadkach, gdy nie ma skonfigurowanego nadmiarowości geograficznej dla serwera, zdecydowanie zaleca się skonfigurowanie serwera do używania dwóch różnych magazynów kluczy w dwóch różnych regionach z tym samym materiałem klucza. Można to osiągnąć, tworząc ochronę TDE przy użyciu podstawowego magazynu kluczy znajdującego się w tym samym regionie co serwer i klonowania klucza do magazynu kluczy w innym regionie platformy Azure, tak aby serwer miał dostęp do drugiego magazynu kluczy, jeśli magazyn kluczy podstawowych powinien być magazynem klucza podstawowego awarii, gdy baza danych jest uruchomiona.

Polecenie cmdlet Backup-AzKeyVaultKey służy do pobierania klucza w formacie zaszyfrowanym z magazynu kluczy podstawowych, a następnie użyj polecenia cmdlet Restore-AzKeyVaultKey i określania magazynu kluczy w drugim regionie w celu sklonowania klucza. Alternatywnie użyj witryny Azure portal do tworzenia kopii zapasowych i przywracania klucza. Klucz w magazynie kluczy pomocniczych w se innych regionach nie powinny być oznaczone jako ochrony TDE i nie jest nawet dozwolone.

 Jeśli występuje awaria wpływająca na magazyn klucza podstawowego, a tylko wtedy system automatycznie przełączy się na inny połączony klucz z tym samym odciskiem palca w pomocniczym magazynie kluczy, jeśli istnieje. Należy jednak pamiętać, że przełącznik nie nastąpi, jeśli ochrona TDE jest niedostępny z powodu odwołanych praw dostępu lub ponieważ klucz lub magazyn kluczy jest usuwany, ponieważ może to oznaczać, że klient celowo chciał ograniczyć serwer dostępu do klucza.

![Wysokiej klasy wieloserwerowej usługi](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR i TDE zarządzane przez klienta

W obu [aktywnych replikacji geograficznej](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) i [pracy awaryjnej grup](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) scenariuszy każdy serwer zaangażowany wymaga oddzielnego magazynu kluczy, który musi być zlokalizowany wspólnie z serwerem w tym samym regionie platformy Azure. Klient jest odpowiedzialny za zachowanie spójności materiału klucza w magazynach kluczy, dzięki czemu geo-pomocniczy jest zsynchronizowany i może przejąć przy użyciu tego samego klucza z lokalnego magazynu kluczy, jeśli podstawowy stanie się niedostępny z powodu awarii w regionie i zostanie wywołany tryb failover . Można skonfigurować maksymalnie cztery pomocnicze pliki, a tworzenie łańcucha (pomocnicze pomocnicze) nie jest obsługiwane.

Aby uniknąć problemów podczas ustanawiania lub podczas replikacji geograficznej z powodu niekompletnego materiału klucza, podczas konfigurowania tde zarządzanego przez klienta należy przestrzegać tych reguł:

- Wszystkie magazyny kluczy zaangażowane muszą mieć takie same właściwości i te same prawa dostępu dla odpowiednich serwerów.

- Wszystkie zaangażowane magazyny kluczy muszą zawierać identyczny materiał klucza. Dotyczy to nie tylko bieżącej ochrony TDE, ale wszystkich poprzednich osłon TDE, które mogą być używane w plikach kopii zapasowych.

- Najpierw początkowa konfiguracja i obrót ochrony TDE muszą być wykonywane na pomocniczym, a następnie na podstawowej.

![Grupy trybu failover i geo-dr](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Aby przetestować tryb failover, wykonaj czynności opisane w [przeglądzie aktywnej replikacji geograficznej](sql-database-geo-replication-overview.md). Należy to zrobić regularnie, aby potwierdzić uprawnienia dostępu do sql do obu magazynów kluczy zostały zachowane.

## <a name="next-steps"></a>Następne kroki

Można również sprawdzić następujące przykładowe skrypty programu PowerShell dla typowych operacji z TDE zarządzanych przez klienta:

- [Obracanie ochrony szyfrowania przezroczystych danych dla bazy danych SQL przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Usuwanie ochrony przezroczystego szyfrowania danych (TDE) dla bazy danych SQL przy użyciu programu PowerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Zarządzanie szyfrowaniem przezroczystych danych w wystąpieniu zarządzanym za pomocą własnego klucza przy użyciu programu PowerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
