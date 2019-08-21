---
title: SQL Database często zadawane pytania dotyczące wystąpienia zarządzanego | Microsoft Docs
description: SQL Database często zadawane pytania dotyczące wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 2efeb37f8b98f4cf9a29ec8a6976146b81aab26b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641051"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database często zadawane pytania dotyczące wystąpienia zarządzanego

Ten artykuł zawiera wiele typowych pytań dotyczących [SQL Database wystąpienia zarządzanego](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-supported-on-managed-instance"></a>Gdzie mogę znaleźć listę funkcji obsługiwanych na wystąpieniu zarządzanym?

Aby uzyskać listę obsługiwanych funkcji w wystąpieniu zarządzanym, zobacz [Azure SQL Database i SQL Server](sql-database-features.md).

Różnice między składnią i zachowaniem Azure SQL Database wystąpienia zarządzanego i SQL Server lokalnych znajdują się w temacie [różnice w języku T-SQL z SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Gdzie można znaleźć cechy techniczne i limity zasobów dla wystąpienia zarządzanego?

Aby uzyskać dostęp do charakterystyki generowania sprzętu, zobacz [różnice techniczne w generacjach sprzętowych](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Aby uzyskać dostęp do dostępnych warstw usług i ich cech, zobacz [różnice techniczne między warstwami usług](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Gdzie mogę znaleźć znane problemy i usterki?

Aby poznać usterki i znane problemy, zobacz [znane problemy](sql-database-managed-instance-transact-sql-information.md#Issues).

## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Czy wystąpienie zarządzane może mieć taką samą nazwę jak SQL Server lokalnego?

Wystąpienie zarządzane musi mieć nazwę kończącą się na *Database.Windows.NET*. Aby użyć innej strefy DNS zamiast domyślnego, na przykład **mi-innej-Name**. contoso.com: 
- Użyj CliConfig, aby zdefiniować alias. To narzędzie jest tylko otoką ustawień rejestru, dlatego można ją wykonać przy użyciu zasad grupy lub skryptu.
- Użyj opcji *CNAME* z *TrustServerCertificate = true* .


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Jak przenieść bazę danych z wystąpienia zarządzanego z powrotem do SQL Server lub Azure SQL Database?

[Bazę danych można wyeksportować do BACPAC](sql-database-export.md) , a następnie [zaimportować plik BACPAC]( sql-database-import.md). Jest to zalecane rozwiązanie, jeśli baza danych jest mniejsza niż 100 GB.

Jeśli wszystkie tabele w bazie danych mają klucze podstawowe, można użyć replikacji transakcyjnej.

Nie `COPY_ONLY` można przywrócić natywnych kopii zapasowych wykonanych z wystąpienia zarządzanego do SQL Server, ponieważ wystąpienie zarządzane ma nowszą wersję bazy danych porównaną z SQL Server.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Jak przeprowadzić migrację bazy danych wystąpienia do jednego Azure SQL Database?

Jedną z opcji jest [wyeksportowanie bazy danych do BACPAC](sql-database-export.md) , a następnie zaimportowanie [pliku BACPAC]( sql-database-import.md). 

Jest to zalecane rozwiązanie, jeśli baza danych jest mniejsza niż 100 GB. Jeśli wszystkie tabele w bazie danych mają klucze podstawowe, można użyć replikacji transakcyjnej.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Jak mogę wybrać generowanie sprzętu generacji 4 i pokolenia 5 dla wystąpienia zarządzanego?

Jest to zależne od obciążenia, ponieważ niektóre typy obciążeń są lepsze w przypadku niektórych typów obciążenia. Chociaż temat wydajności jest raczej skomplikowany, aby uprościć, poniżej przedstawiono różnice między generacjami sprzętowymi wpływającymi na wydajność obciążeń:
- Gen 4 oferuje lepszą obsługę obliczeniową, która jest oparta na procesorach fizycznych, a w przypadku generacji 5 opartych na procesorach rdzeń wirtualny. Może być bardziej korzystne w przypadku obciążeń intensywnie korzystających z obliczeń.
- Funkcja gen 5 obsługuje przyspieszone sieci, co zapewnia lepszą przepustowość we/wy do magazynu zdalnego. Może być korzystne w przypadku obciążeń intensywnie korzystających z operacji we/wy na Ogólnego przeznaczenia warstwach usług. W przypadku generacji 5 są stosowane szybsze dyski lokalne SSD w porównaniu do generacji 4. Może być korzystne w przypadku obciążeń intensywnie korzystających z operacji we/wy dla krytycznych warstw usług.

Zdecydowanie zaleca się przetestowanie wydajności rzeczywistych obciążeń przeznaczonych dla środowiska produkcyjnego przed rozpoczęciem pracy, aby określić, które Generowanie sprzętu będzie lepiej w konkretnym przypadku.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Czy mogę przełączać generowanie sprzętu wystąpienia zarządzanego między programem gen 4 i gen 5 online? 

Automatyczne przełączanie do trybu online między generacjami sprzętowymi jest możliwe, jeśli w regionie, w którym zainicjowano wystąpienie zarządzane, są dostępne oba generacji sprzętowe. W takim przypadku dostępna jest opcja w sekcji warstwa cenowa Azure Portal, aby przełączać się między generacjami sprzętowymi.

Jest to długotrwała operacja, ponieważ nowe wystąpienie zarządzane zostanie udostępnione w tle, a bazy danych zostaną automatycznie przeniesione między starym i nowym wystąpieniem z szybką pracą w trybie failover na końcu procesu. 

Jeśli oba generacje sprzętowe nie są obsługiwane w tym samym regionie, zmiana generacji sprzętu jest możliwa, ale należy ją wykonać ręcznie. Wymaga to zainicjowania obsługi nowego wystąpienia w regionie, w którym jest dostępna żądana generacja sprzętu, a następnie ręcznie utworzyć kopię zapasową i przywrócić dane między starym i nowym wystąpieniem.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Jak mogę dostosować wydajność wystąpienia zarządzanego? 

Ogólnego przeznaczenia zarządzanym wystąpieniem używa magazynu zdalnego, ponieważ rozmiar danych i plików dziennika jest istotny dla wydajności. Aby uzyskać więcej informacji, zobacz [wpływ rozmiaru pliku dziennika na wydajność wystąpienia zarządzanego ogólnego przeznaczenia](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

W przypadku obciążeń intensywnie korzystających z operacji we/wy należy rozważyć użycie sprzętu generacji 5 w przeciwieństwie do generacji 4 obciążeń intensywnie korzystających z obliczeń. Aby uzyskać więcej informacji, zobacz [Jak mogę wybrać między gen 4 i gen 5](#how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance).

Jeśli obciążenie obejmuje wiele małych transakcji, rozważ przełączenie typu połączenia z serwera proxy do trybu przekierowania.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Jaki jest maksymalny rozmiar magazynu dla wystąpienia zarządzanego? 

Rozmiar magazynu dla wystąpienia zarządzanego zależy od wybranej warstwy usług (Ogólnego przeznaczenia lub Krytyczne dla działania firmy). Aby uzyskać ograniczenia dotyczące magazynu tych warstw usług, zobacz [Charakterystyka warstwy usług](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>Czy magazyn kopii zapasowych jest odejmowany od magazynu wystąpienia zarządzanego? 

Nie, magazyn kopii zapasowych nie jest odejmowany od przestrzeni dyskowej wystąpienia zarządzanego. Magazyn kopii zapasowych jest niezależny od ilości miejsca w magazynie i nie ma ograniczonego rozmiaru. Magazyn kopii zapasowych jest ograniczony przez okres, aby zachować kopię zapasową baz danych wystąpień, konfigurowalne od 7 do 35 dni. Aby uzyskać szczegółowe informacje, zobacz [zautomatyzowane kopie zapasowe](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Jak ustawić reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego na portach zarządzania?

Wbudowana funkcja zapory konfiguruje Zaporę systemu Windows na wszystkich maszynach wirtualnych w klastrze, aby zezwalać na połączenia przychodzące z zakresów adresów IP skojarzonych tylko z komputerami do zarządzania/wdrażania firmy Microsoft i skutecznie chronić stacje robocze wtargnięcie przez warstwę sieciową.

Oto, jakie porty są używane dla:

Porty 9000 i 9003 są używane przez infrastrukturę Service Fabric. Service Fabric rolą podstawową jest utrzymywanie w dobrej kondycji klastra wirtualnego i zachowanie stanu celu w odniesieniu do liczby replik składników.

Porty 1438, 1440 i 1452 są używane przez agenta węzła. Node Agent to aplikacja, która działa w klastrze i jest używana przez płaszczyznę kontroli do wykonywania poleceń zarządzania.

Oprócz wbudowanej zapory w warstwie sieciowej komunikacja jest również chroniona przy użyciu certyfikatów.
  
Aby uzyskać więcej informacji i jak sprawdzić wbudowaną zaporę, zobacz [Azure SQL Database wbudowanej zapory wystąpienia zarządzanego](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Jak można ograniczyć ryzyko związane z siecią? 

Aby zmniejszyć ryzyko związane z siecią, klienci są zalecani do zastosowania zestawu ustawień zabezpieczeń i kontroli:

- Włącz [transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) we wszystkich bazach danych.
- Wyłącz środowisko uruchomieniowe języka wspólnego (CLR). Jest to również zalecane lokalnie.
- Używaj tylko uwierzytelniania Azure Active Directory (AAD).
- Wystąpienie dostępu przy użyciu konta z niskim poziomem uprawnień DBA.
- Skonfiguruj dostęp JiT serwera przesiadkowego dla konta sysadmin.
- Włącz [inspekcję SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)i Zintegruj ją z mechanizmami alertów.
- Włącz [wykrywanie zagrożeń](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) z poziomu zestawu [Advanced Data Security (AD)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Gdzie mogę znaleźć przypadki użycia i uzyskać oszczędności wynikające z wystąpienia zarządzanego?

Analizy przypadków wystąpienia zarządzanego:

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Aby lepiej zrozumieć korzyści, koszty i ryzyko związane z wdrażaniem Azure SQL Database wystąpienia zarządzanego, istnieje również badanie Forrester: [Łączny wpływ na środowisko mi](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>Czy można odświeżyć DNS? 
  
Obecnie nie udostępniamy funkcji odświeżania konfiguracji serwera DNS dla wystąpienia zarządzanego.

Konfiguracja DNS jest ostatecznie odświeżana:

- Po wygaśnięciu dzierżawy DHCP.
- Podczas uaktualniania platformy.

Obejście tego problemu powoduje obniżenie poziomu zarządzanego wystąpienia do 4 rdzeń wirtualny i ponowne uaktualnienie. Ma to efekt po stronie odświeżenia konfiguracji DNS.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Czy zarządzane wystąpienie ma statyczny adres IP?

W rzadkich przypadkach, ale konieczne może być przeprowadzenie migracji w trybie online wystąpienia zarządzanego do nowego klastra wirtualnego. W razie potrzeby migracja jest spowodowana zmianami w naszym stosie technologii, które mają na celu poprawę bezpieczeństwa i niezawodności usługi. Migracja do nowego klastra wirtualnego powoduje zmianę adresu IP mapowanego na nazwę hosta wystąpienia zarządzanego. Usługa wystąpienia zarządzanego nie wiąże się z obsługą statycznego adresu IP i zastrzega sobie prawo do jego zmiany bez powiadomienia jako części zwykłych cykli konserwacyjnych.

Z tego powodu zdecydowanie odradzamy niezmienności adresu IP, ponieważ może to spowodować niepotrzebne przestoje.

## <a name="can-i-move-a-managed-instance-or-its-vnet-to-another-resource-group"></a>Czy można przenieść wystąpienie zarządzane lub jego sieć wirtualną do innej grupy zasobów?

Nie, to jest bieżące ograniczenie platformy. Po utworzeniu wystąpienia zarządzanego przeniesienie wystąpienia zarządzanego lub sieci wirtualnej do innej grupy zasobów lub subskrypcji nie jest obsługiwane.

## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Czy mogę zmienić strefę czasową dla istniejącego wystąpienia zarządzanego?

Konfigurację strefy czasowej można ustawić, gdy zarządzane wystąpienie jest inicjowane po raz pierwszy. Zmiana strefy czasowej istniejącego wystąpienia zarządzanego nie jest obsługiwana. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia strefy czasowej](sql-database-managed-instance-timezone.md#limitations).

Obejścia obejmują tworzenie nowego wystąpienia zarządzanego ze stosowną strefą czasową, a następnie wykonanie ręcznej kopii zapasowej i przywracania albo to, co jest zalecane, wykonywanie [przywracania do punktu w czasie między wystąpieniami](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Jak mogę Rozwiązywanie problemów z wydajnością z moim wystąpieniem zarządzanym

Aby porównać wydajność między wystąpieniem zarządzanym i SQL Server, dobrym punktem początkowym jest [najlepsze rozwiązanie w zakresie porównania wydajności między wystąpieniem zarządzanym usługi Azure SQL i](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) artykułem SQL Server.

Ładowanie danych jest często wolniejsze na wystąpieniu zarządzanym niż w SQL Server z powodu obowiązkowego pełnego [](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) modelu odzyskiwania i limitów przepływności zapisu w dzienniku transakcji. Czasami może to być spowodowane załadowaniem danych przejściowych do bazy danych tempdb, a nie z użyciem klastrowanej magazynu kolumn lub tabel zoptymalizowanych pod kątem pamięci.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Czy można przywrócić zaszyfrowaną bazę danych do wystąpienia zarządzanego?

Tak, nie musisz odszyfrowywać bazy danych, aby móc przywrócić ją do wystąpienia zarządzanego. Należy podać certyfikat/klucz używany jako funkcja ochrony klucza szyfrowania w systemie źródłowym do wystąpienia zarządzanego, aby można było odczytywać dane z zaszyfrowanego pliku kopii zapasowej. Istnieją dwa sposoby, aby to zrobić:

- *Przekaż ochronę certyfikatu do wystąpienia zarządzanego*. Można to zrobić tylko przy użyciu programu PowerShell. [Przykładowy skrypt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) opisuje cały proces.
- *Przekaż funkcję ochrony klucza asymetrycznego do Azure Key Vault (AKV) i wskaż do niej wystąpienie zarządzane*. Takie podejście przypomina BYOK TDE użycie, który używa integracji AKV w celu przechowywania klucza szyfrowania. Jeśli nie chcesz używać klucza jako funkcji ochrony klucza szyfrowania i po prostu chcesz udostępnić klucz dla wystąpienia zarządzanego w celu przywrócenia zaszyfrowanych baz danych, postępuj zgodnie z instrukcjami dotyczącymi [KONFIGUROWANIA BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption-in-the-azure-portal)i nie zaznaczaj pola wyboru *Oznacz wybrany klucz Domyślna funkcja ochrony TDE*.

Po udostępnieniu funkcji ochrony szyfrowania wystąpieniem zarządzanemu można wykonać procedurę standardowego przywracania bazy danych.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Jak przeprowadzić migrację z Azure SQL Database jednej lub elastycznej puli do wystąpienia zarządzanego? 

Wystąpienie zarządzane oferuje te same poziomy wydajności dla wielkości zasobów obliczeniowych i magazynu, co inne opcje wdrażania Azure SQL Database. Jeśli chcesz skonsolidować dane w jednym wystąpieniu lub po prostu potrzebna jest funkcja obsługiwana wyłącznie w wystąpieniu zarządzanym, można migrować dane przy użyciu funkcji eksportu/importu (BACPAC).
