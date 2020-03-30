---
title: Często zadawane pytania dotyczące wystąpień zarządzanych
description: Często zadawane pytania dotyczące wystąpienia zarządzanego bazy danych SQL (CZĘSTO ZADAWANE PYTANIA)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364156"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące wystąpienia zarządzanego bazy danych SQL (CZĘSTO ZADAWANE PYTANIA)

Ten artykuł zawiera wiele najczęściej zadawanych pytań dotyczących [wystąpienia zarządzanego bazy danych SQL.](sql-database-managed-instance.md)

## <a name="supported-features"></a>Obsługiwane funkcje

**Gdzie można znaleźć listę funkcji obsługiwanych w wystąpieniu zarządzanym?**

Aby uzyskać listę obsługiwanych funkcji w wystąpieniu zarządzanym, zobacz [Usługa Azure SQL Database versus SQL Server](sql-database-features.md).

Aby uzyskać różnice w składni i zachowaniu między wystąpieniem zarządzanym usługi Azure SQL Database a lokalnym programem SQL Server, zobacz [Różnice T-SQL w programie SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Specyfikacja technologiczna & limity zasobów
 
**Gdzie mogę znaleźć parametry techniczne i limity zasobów dla wystąpienia zarządzanego?**

Aby uzyskać dostępną charakterystykę generowania sprzętu, zobacz [różnice techniczne w generacjach sprzętu](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Aby uzyskać dostępne warstwy usług i ich charakterystykę, zobacz [różnice techniczne między warstwami usług](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Znane problemy & błędów

**Gdzie mogę znaleźć znane problemy i błędy?**

W przypadku błędów i znanych problemów zobacz [znane problemy](sql-database-release-notes.md#known-issues).

## <a name="new-features"></a>Nowe funkcje

**Gdzie mogę znaleźć najnowsze funkcje i funkcje w publicznej wersji zapoznawczej?**

Aby uzyskać nowe funkcje i funkcje w wersji, zobacz [informacje o wersji](sql-database-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Czasy wdrażania 

**Ile czasu zajmuje utworzenie lub zaktualizowanie wystąpienia lub przywrócenia bazy danych?**

Oczekiwany czas na utworzenie nowego wystąpienia zarządzanego lub zmianę warstwy usługi (vCores, storage) zależy od kilku czynników. Zapoznaj się z [operacjami zarządzania](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Konwencja nazewnictwa

**Czy wystąpienie zarządzane może mieć taką samą nazwę jak lokalny program SQL Server?**

Zmiana nazwy wystąpienia zarządzanego nie jest obsługiwana.

Domyślna strefa DNS wystąpienia zarządzanego *można zmienić database.windows.net.* 

Aby użyć innej strefy DNS zamiast domyślnej, na przykład *.contoso.com:* 
- Użyj CliConfig, aby zdefiniować alias. Narzędzie jest tylko otoką ustawień rejestru, więc można to zrobić za pomocą zasad grupy lub skryptu, jak również.
- Użyj *CNAME* z *opcją TrustServerCertificate=true.*

## <a name="move-db-from-mi"></a>Przenoszenie bazy danych z mi 

**Jak przenieść bazę danych z wystąpienia zarządzanego z powrotem do programu SQL Server lub bazy danych SQL Azure?**

Można [wyeksportować bazę danych do BACPAC,](sql-database-export.md) a następnie [zaimportować plik BACPAC]( sql-database-import.md). Jest to zalecane podejście, jeśli baza danych jest mniejsza niż 100 GB.

Replikacja transakcyjna może służyć, jeśli wszystkie tabele w bazie danych mają klucze podstawowe.

Natywne `COPY_ONLY` kopie zapasowe pobrane z wystąpienia zarządzanego nie można przywrócić do programu SQL Server, ponieważ wystąpienie zarządzane ma wyższą wersję bazy danych w porównaniu do programu SQL Server.

## <a name="migrate-instance-db"></a>Migrowanie bazy danych wystąpienia

**Jak przeprowadzić migrację bazy danych instancji do pojedynczej bazy danych SQL platformy Azure?**

Jedną z opcji jest [wyeksportowanie bazy danych do BACPAC,](sql-database-export.md) a następnie [zaimportowanie pliku BACPAC](sql-database-import.md). 

Jest to zalecane podejście, jeśli baza danych jest mniejsza niż 100 GB. Replikacja transakcyjna może służyć, jeśli wszystkie tabele w bazie danych mają klucze podstawowe.

## <a name="switch-hardware-generation"></a>Przełączanie generacji sprzętu 

**Czy mogę przełączyć generowanie sprzętu z zarządzanych wystąpień między generacją 4 i gen 5 online?**

Automatyczne przełączanie online między generacjami sprzętu jest możliwe, jeśli obie generacje sprzętu są dostępne w regionie, w którym jest aprowizowana zarządzana instancja. W takim przypadku można sprawdzić [stronę przeglądu modelu vCore](sql-database-service-tiers-vcore.md) wyjaśniającą sposób przełączania się między generacjami sprzętu.

Jest to długotrwała operacja, ponieważ nowe wystąpienie zarządzane zostanie aprowied w tle i baz danych automatycznie przeniesione między starym i nowym wystąpieniem z szybką pracę awaryjną na końcu procesu. 

Jeśli obie generacje sprzętu nie są obsługiwane w tym samym regionie, zmiana generowania sprzętu jest możliwa, ale musi być wykonana ręcznie. Wymaga to aprowizowania nowego wystąpienia w regionie, w którym jest dostępne generowanie sprzętu, a następnie ręcznego tworzenia kopii zapasowych i przywracania danych między starym i nowym wystąpieniem.


## <a name="tune-performance"></a>Dostosowywanie wydajności

**Jak dostroić wydajność mojego wystąpienia zarządzanego?**

Wystąpienie zarządzane ogólnego przeznaczenia używa magazynu zdalnego, dzięki czemu rozmiar plików danych i dzienników ma znaczenie dla wydajności. Aby uzyskać więcej informacji, zobacz [Wpływ rozmiaru pliku dziennika na wydajność wystąpienia zarządzanego ogólnego przeznaczenia](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Jeśli obciążenie składa się z wielu małych transakcji, należy rozważyć przełączenie typu połączenia z serwera proxy do trybu przekierowania.

## <a name="maximum-storage-size"></a>Maksymalny rozmiar pamięci masowej

**Jaki jest maksymalny rozmiar magazynu dla wystąpienia zarządzanego?**

Rozmiar magazynu dla wystąpienia zarządzanego zależy od wybranej warstwy usług (ogólnego przeznaczenia lub krytycznego biznesowego). Aby uzyskać ograniczenia magazynowania tych warstw usług, zobacz [Charakterystyka warstwy usługi](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Koszt magazynowania kopii zapasowej 

**Czy magazyn kopii zapasowej jest odejmowany z magazynu wystąpienia zarządzanego?**

Nie, magazyn kopii zapasowych nie jest odejmowy od miejsca do magazynowania wystąpienia zarządzanego. Magazyn kopii zapasowej jest niezależny od miejsca do magazynowania wystąpienia i nie jest ograniczony rozmiar. Magazyn kopii zapasowych jest ograniczony przez okres przechowywania kopii zapasowej baz danych wystąpień, konfigurowany z 7 do 35 dni. Aby uzyskać szczegółowe informacje, zobacz [Automatyczne kopie zapasowe](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Śledzenie rozliczeń

**Czy istnieje sposób śledzenia kosztów rozliczeń dla mojego zarządzanego wystąpienia?**

Można to zrobić za pomocą [rozwiązania Usługi Azure Cost Management](/azure/cost-management/). Przejdź do **subskrypcji** w [witrynie Azure portal](https://portal.azure.com) i wybierz pozycję Analiza **kosztów**. 

Użyj opcji **Skumulowane koszty,** a następnie `microsoft.sql/managedinstances`filtruj według **typu zasobu** jako . 
  
## <a name="inbound-nsg-rules"></a>Przychodzące reguły sieciowych sieci płciowych

**Jak ustawić przychodzące reguły sieciowej sieciowej sieciowej sieciowej dla portów zarządzania?**

Płaszczyzna sterowania wystąpienia zarządzanego utrzymuje reguły sieciowej sieciowej sieciowej, które chronią porty zarządzania.

Oto, do czego służą porty zarządzania:

Porty 9000 i 9003 są używane przez infrastrukturę sieci szkieletowej usług. Podstawową rolą sieci szkieletowej usług jest utrzymanie klastra wirtualnego w dobrej kondycji i utrzymanie stanu celu pod względem liczby replik składników.

Porty 1438, 1440 i 1452 są używane przez agenta węzła. Agent węzła to aplikacja, która działa wewnątrz klastra i jest używana przez płaszczyznę sterowania do wykonywania poleceń zarządzania.

Oprócz reguł sieciowej grupy sieciowej wbudowana zapora chroni wystąpienie w warstwie sieciowej. W warstwie aplikacji komunikacja jest chroniona certyfikatami.
  
Aby uzyskać więcej informacji i jak zweryfikować wbudowaną zaporę, zobacz [Wbudowana zapora z wystąpieniem zarządzanym usługi Azure SQL Database](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Ograniczanie ryzyka eksfiltracji danych  

**Jak ograniczyć ryzyko eksfiltracji danych?**

Aby ograniczyć wszelkie zagrożenia związane z eksfiltracją danych, zaleca się, aby klienci stosują zestaw ustawień zabezpieczeń i formantów:

- Włącz [przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) we wszystkich bazach danych.
- Wyłącz środowisko uruchomieniowe języka wspólnego (CLR). Jest to zalecane również lokalnie.
- Użyj tylko uwierzytelniania usługi Azure Active Directory (AAD).
- Dostęp do wystąpienia z niskim kontem DBA o niskich uprawnieniach.
- Skonfiguruj dostęp do skrzynki szybkiego jit dla konta sysadmin.
- Włącz [inspekcję SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)i zintegruj ją z mechanizmami alertów.
- Włącz [wykrywanie zagrożeń](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) w pakiecie [Zaawansowane zabezpieczenia danych (ADS).](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="cost-saving-use-cases"></a>Przypadki użycia oszczędności kosztów

**Gdzie mogę znaleźć przypadki użycia i wynikające z tego oszczędności kosztów z zarządzanym wystąpieniem?**

Studia przypadków wystąpienia zarządzanego:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [Szczegóły programu PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Wszystkie skrypty](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Aby lepiej zrozumieć korzyści, koszty i ryzyko związane z wdrażaniem wystąpienia zarządzanego usługi Azure SQL Database, istnieje również badanie firmy Forrester: [Całkowity wpływ ekonomiczny MI.](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)


## <a name="dns-refresh"></a>Odświeżanie DNS 

**Czy mogę odświeżać dns?**

Obecnie nie udostępniamy funkcji odświeżania konfiguracji serwera DNS dla wystąpienia zarządzanego.

Konfiguracja DNS jest po pewnym czasie odświeżana:

- Po wygaśnięciu dzierżawy DHCP.
- Na uaktualnieniu platformy.

Aby obejść ten problem, przejdź do poziomu 4 vCore i uaktualnij go ponownie. Ma to efekt uboczny odświeżania konfiguracji DNS.


## <a name="ip-address"></a>Adres IP

**Czy mogę połączyć się z wystąpieniem zarządzanym przy użyciu adresu IP?**

Łączenie się z wystąpieniem zarządzanym przy użyciu adresu IP nie jest obsługiwane. Nazwa hosta wystąpienia zarządzanego jest mapowana do modułu równoważenia obciążenia przed klastrem wirtualnym wystąpienia zarządzanego. Ponieważ jeden klaster wirtualny może obsługiwać wiele wystąpień zarządzanych, nie można przekierować połączenia do właściwego wystąpienia zarządzanego bez określania jego nazwy.

Aby uzyskać więcej informacji na temat architektury klastra wirtualnego wystąpienia [zarządzanego, zobacz Architektura łączności klastra wirtualnego](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture).

**Czy wystąpienie zarządzane może mieć statyczny adres IP?**

W rzadkich, ale niezbędnych sytuacjach może być konieczne przeprowadzić migrację online wystąpienia zarządzanego do nowego klastra wirtualnego. W razie potrzeby migracja ta jest wynikiem zmian w naszym stosie technologicznym mających na celu poprawę bezpieczeństwa i niezawodności usługi. Migracja do nowego klastra wirtualnego powoduje zmianę adresu IP, który jest mapowany na nazwę hosta wystąpienia zarządzanego. Usługa wystąpienia zarządzanego nie zgłasza roszczeń do obsługi statycznych adresów IP i zastrzega sobie prawo do zmiany bez powiadomienia w ramach regularnych cykli konserwacji.

Z tego powodu zdecydowanie odradzamy poleganie na niezmienności adresu IP, ponieważ może to spowodować niepotrzebne przestoje.

## <a name="change-time-zone"></a>Zmienianie strefy czasowej

**Czy mogę zmienić strefę czasową dla istniejącego wystąpienia zarządzanego?**

Konfigurację strefy czasowej można ustawić, gdy wystąpienie zarządzane jest aprowizowane po raz pierwszy. Zmiana strefy czasowej istniejącego wystąpienia zarządzanego nie jest obsługiwana. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia strefy czasowej](sql-database-managed-instance-timezone.md#limitations).

Obejścia obejmują utworzenie nowego wystąpienia zarządzanego z odpowiednią strefą czasową, a następnie wykonanie ręcznej kopii zapasowej i przywracania lub zalecane przez nas [wykonanie przywracania między wystąpieniami punktu w czasie](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Rozwiązywanie problemów z wydajnością

**Jak rozwiązać problemy z wydajnością mojego wystąpienia zarządzanego?**

W przypadku porównania wydajności między wystąpieniem zarządzanym a programem SQL Server dobrym punktem wyjścia są [najlepsze rozwiązania dotyczące porównywania wydajności między wystąpieniem zarządzanym usługi Azure SQL a](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) artykułem programu SQL Server.

Ładowanie danych jest często wolniejsze w wystąpieniu zarządzanym niż w programie SQL Server ze względu na obowiązkowy model pełnego odzyskiwania i [limity](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) przepływności zapisu dziennika transakcji. Czasami można to obejść, ładując dane przejściowe do bazy danych typu tempdb zamiast do bazy danych użytkowników lub używając klastrowanego magazynu kolumn lub tabel zoptymalizowanych pod kątem pamięci.


## <a name="restore-encrypted-backup"></a>Przywracanie zaszyfrowanej kopii zapasowej

**Czy mogę przywrócić zaszyfrowaną bazę danych do wystąpienia zarządzanego?**

Tak, nie trzeba odszyfrowywać bazy danych, aby móc przywrócić ją do wystąpienia zarządzanego. Aby można było odczytać dane z zaszyfrowanego pliku kopii zapasowej, należy podać certyfikat/klucz używany jako ochrona klucza szyfrowania w systemie źródłowym. Istnieją dwa możliwe sposoby, aby to zrobić:

- *Przekaż ochronę certyfikatu do wystąpienia zarządzanego*. Można to zrobić tylko za pomocą programu PowerShell. Przykładowy [skrypt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) opisuje cały proces.
- *Przekaż asymetryczny ochraniacz kluczy do usługi Azure Key Vault (AKV) i wskaż do niego wystąpienie zarządzanego przez punkt.* Takie podejście przypomina przypadek użycia TDE bring-your-own-key (BYOK), który również używa integracji AKV do przechowywania klucza szyfrowania. Jeśli nie chcesz używać klucza jako ochrony klucza szyfrowania i chcesz tylko udostępnić klucz dla wystąpienia zarządzanego w celu przywrócenia zaszyfrowanych baz danych, postępuj zgodnie z instrukcjami [dotyczącymi konfigurowania funkcji BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)i nie zaznaczaj pola wyboru *Ustaw wybrany klawisz jako domyślną ochronę TDE*.

Po udostępnieniu ochrony szyfrowania do wystąpienia zarządzanego, można kontynuować standardową procedurę przywracania bazy danych.

## <a name="migrate-from-single-db"></a>Migrowanie z pojedynczej bazy danych 

**Jak przeprowadzić migrację z pojedynczej lub elastycznej puli usługi Azure SQL Database do wystąpienia zarządzanego?**

Wystąpienie zarządzane oferuje te same poziomy wydajności na rozmiar obliczeń i magazynu, jak inne opcje wdrażania usługi Azure SQL Database. Jeśli chcesz skonsolidować dane w jednym wystąpieniu lub po prostu potrzebujesz funkcji obsługiwanej wyłącznie w wystąpieniu zarządzanym, możesz migrować dane przy użyciu funkcji eksportu/importu (BACPAC).
