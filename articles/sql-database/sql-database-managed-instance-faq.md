---
title: Zarządzana wystąpienia często zadawane pytania dotyczące usługi SQL Database | Dokumentacja firmy Microsoft
description: Wystąpienie zarządzane usługi SQL Database — często zadawane pytania (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/08/2019
ms.openlocfilehash: c3a070eb7e1435055b47b39985cf8cb0b182a514
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798076"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Wystąpienie zarządzane usługi SQL Database — często zadawane pytania (FAQ)

Ten artykuł zawiera wiele często zadawane pytania dotyczące [wystąpienie zarządzane bazy danych SQL](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>Gdzie mogę znaleźć listę funkcji, które są obsługiwane w wystąpieniu zarządzanym

Aby uzyskać listę obsługiwanych funkcji w wystąpieniu zarządzanym, zobacz [usługi Azure SQL Database a baza danych programu SQL Server](sql-database-features.md).

Pod względem różnic w składni i zachowanie między wystąpieniem usługi Azure SQL Database, zarządzanego i na lokalnym serwerze SQL Server, zobacz [różnice języka T-SQL z programu SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Gdzie mogę znaleźć dane techniczne i limity zasobów dla wystąpienia zarządzanego?

Dla właściwości generacji sprzętu, zobacz [różnice techniczne generacji sprzętu](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Dostępna usługa warstwy i ich właściwości można znaleźć [techniczne różnic między warstwami usług](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Gdzie można znaleźć znane problemy i usterki?

Usterki i znane problemy, zobacz [zmiany zachowania](sql-database-managed-instance-transact-sql-information.md#Changes) i [znane problemy dotyczące](sql-database-managed-instance-transact-sql-information.md#Issues).


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Wystąpienie zarządzane, można mieć taką samą nazwę jak programu SQL Server w środowisku lokalnym?

Wystąpienie zarządzane musi mieć nazwę, która kończy się *database.windows.net*. Aby użyć innej strefy DNS zamiast domyślnej, na przykład **mi innego nazwa-** . contoso.com: 
- Użyj CliConfig, aby zdefiniować alias (Narzędzie to tylko otoka ustawienia rejestru, dzięki czemu można to również zrobić przy użyciu zasad grupy lub skryptu).
- Użyj *CNAME* z *TrustServerCertificate = true* opcji.


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Jak przenieść bazę danych z wystąpienia zarządzanego powrót do programu SQL Server lub usługi Azure SQL Database?

Możesz [eksportowanie bazy danych do pliku bacpac](sql-database-export.md) i następnie [Importowanie pliku bacpac]( sql-database-import.md). Takie podejście jest zalecane, jeśli baza danych jest mniejsza niż 100 GB.

Replikacji transakcyjnej może służyć, jeśli wszystkie tabele w bazie danych mają klucze podstawowe.

Natywne `COPY_ONLY` kopii zapasowych wykonanych z wystąpienia zarządzanego nie można przywrócić do programu SQL Server, ponieważ wystąpienie zarządzane ma wyższą wersję bazy danych w porównaniu do programu SQL Server.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Jak mogę zmigrować Moje wystąpienia bazy danych do pojedynczej bazy danych SQL Azure

Jedną z opcji jest [eksportowania bazy danych do pliku bacpac](sql-database-export.md) i następnie [Importowanie pliku bacpac]( sql-database-import.md). 

Jest to zalecane podejście, jeśli baza danych jest mniejsza niż 100 GB. Replikacji transakcyjnej może służyć, jeśli wszystkie tabele w bazie danych mają klucze podstawowe.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Jak wybrać między Gen 4 i 5 ogólnego generacja sprzętu dla wystąpienia zarządzanego?

Zależy to od obciążenia niektóre generacja sprzętu jest lepsze dla niektórych rodzajów obciążeń niż ten drugi. Temat wydajności jest raczej złożonych katalog w celu uproszczenia, następujące różnice między generacji sprzętu wpływających na wydajność obciążenia:
- 4\. generacji zapewnia lepszą obsługę obliczeniowych, ponieważ opiera się na procesorów fizycznych, a 5 ogólnego, który jest oparty na rdzeniach wirtualnych procesorów. Może to być bardziej korzystne dla obciążeń wymagających intensywnych obliczeń.
- 5\. generacji obsługuje accelerated networking skutkuje lepszą przepustowość operacji We/Wy do magazynu zdalnego. Może to być korzystne dla obciążeń intensywnie korzystających z operacji We/Wy w następujących warstwach usługi ogólnego przeznaczenia. 5\. generacji używa szybsze dyski lokalne dyski SSD w porównaniu do Gen 4. Może to być korzystne dla obciążeń intensywnie korzystających z operacji We/Wy w następujących warstwach usługi krytyczne biznesowych.

Klienci są zalecane do testowania wydajności obciążeń rzeczywistego przeznaczone dla środowiska produkcyjnego przed zakładano ustalenie, jaki sprzęt generacji będą działać lepiej w Twoim przypadku.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Czy mogę przełączać Moje wystąpienia zarządzanego generacja sprzętu Gen 4 i 5 ogólnego online 

Automatyczne online przełączania się między generacji sprzętu jest możliwe w przypadku obu generacji sprzętu są dostępne w tym samym regionie, w którym zainicjowano wystąpienia zarządzanego. W tym przypadku masz opcję w sekcji warstwy cenowej witryny Azure Portal, aby przełączać się między generacji sprzętu.

Jest to bardzo długo wykonywania operacji, ponieważ nowe wystąpienia zarządzanego zostanie zainicjowana dla zaplecza i baz danych automatycznie przesyłane między starego i nowego wystąpienia. Ten proces będzie bezproblemowe dla klientów.

W przypadku obu generacji sprzętu nie są obsługiwane w tym samym regionie, zmiana generacja sprzętu jest możliwe, ale musi być wykonywane ręcznie. Wymaga to udostępnienie nowego wystąpienia w regionie, gdzie generowania żądaną sprzęt jest dostępny i ręcznie wykonujesz kopie zapasowe i przywracanie danych między starego i nowego wystąpienia.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Jak dostrajanie wydajności Moje wystąpienia zarządzanego? 

Ogólnego przeznaczenia wystąpienia zarządzanego używa magazynu zdalnego, z powodu której rozmiaru plików danych i dziennika ma znaczenie dla wydajności. Można dostrajanie wydajności warstwa ogólnego przeznaczenia, postępuj zgodnie z instrukcjami w tym wpisie w blogu.

Dla obciążeń intensywnie korzystających z operacji We/Wy należy wziąć pod uwagę przy użyciu sprzętu Gen 5, w porównaniu z użyciem Gen 4 dla obciążeń wymagających intensywnych obliczeń. Aby uzyskać więcej informacji, zobacz sekcję często zadawanych PYTAŃ na temat wybierania między generacji sprzętu.

Jeśli obciążenie składa się z wielu małych transakcji, należy wziąć pod uwagę zmianą typu połączenia z serwera proxy do przekierowywania trybu.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Co to jest maksymalny rozmiar magazynu dla wystąpienia zarządzanego? 

Rozmiar magazynu dla wystąpienia zarządzanego zależy od warstwy wybranej usługi (ogólnego przeznaczenia lub krytyczne dla działania firmy). Dla ograniczenia magazynu z tych warstw usługi, zobacz [cechy warstwy usługi](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>Magazyn kopii zapasowych pomniejsza magazynu wystąpienia zarządzanego? 

Nie, Magazyn kopii zapasowych nie jest odejmowany od miejsca do magazynowania wystąpienia zarządzanego. Magazyn kopii zapasowych jest niezależna od miejsca do magazynowania wystąpienia i nie ma ograniczony rozmiar. Magazyn kopii zapasowych jest ograniczona przez czas przechowywania kopii zapasowych wystąpienia bazy danych, można skonfigurować od 7 do 35 dni. Aby uzyskać więcej informacji, zobacz [automatyczne kopie zapasowe](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Jak ustawić reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego na porty zarządzania

Funkcja wbudowanej zapory konfiguruje zaporę Windows na wszystkich maszynach wirtualnych w klastrze, aby zezwolić na połączenia przychodzące z zakresów adresów IP skojarzone tylko z Microsoft zarządzania/wdrożenia. maszyny wirtualne i bezpieczne administracyjne stacje robocze, efektywnie uniemożliwia nieautoryzowanym dostępem przez warstwę sieciową.

Oto, jakie porty są używane do:

Porty 9000 i 9003 są używane przez infrastrukturę usługi Service Fabric. Podstawową rolą usługi Service Fabric jest zachować klaster wirtualny dobrej kondycji i Zachowaj stan docelowy pod względem liczby replik składnika.

Porty 1438, 1440 i 1452 są używane przez agenta węzła. Agent węzła jest aplikacja, która działa w klastrze i jest używany przez na płaszczyźnie kontroli do wykonywania poleceń zarządzania.

Oprócz wbudowanych zapora na warstwy sieci komunikacji jest również chroniony za pomocą certyfikatów.
  
Więcej informacji oraz jak zweryfikować wbudowanej zapory, zobacz [usługi Azure SQL Database managed wystąpienia wbudowanej zapory](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Jak można ograniczyć ryzyko sieci? 

Aby uniknąć ryzyka sieci, aby zastosować zestaw ustawień zabezpieczeń i kontroli, zaleca się klientów:

- Włącz na przezroczyste szyfrowanie danych (TDE) dla wszystkich baz danych.
- Włącz Wyłącz środowiska uruchomieniowego języka wspólnego (CLR). Jest to zalecane w środowisku lokalnym także.
- Użyj tylko konta usługi Azure AD.
- Dostęp do wystąpienia Zarządzanego SQL przy użyciu mniej uprzywilejowanego konta Administrator.
- Skonfiguruj dostęp do serwera przesiadkowego JiT dla konta administratora systemu.
- Włączanie inspekcji usługi SQL, a następnie zintegrować go z mechanizmy alertów.
- Włącz wykrywanie zagrożeń z ATS zestawu.


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Gdzie mogę znaleźć przypadki użycia i wynikowy oszczędności kosztów za pomocą wystąpienia zarządzanego?

Analizy przypadków: miejsca wystąpienia zarządzanego:

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Aby lepiej zrozumieć korzyści, kosztów i zagrożeń związanych z wdrażaniem wystąpienia zarządzanego usługi Azure SQL Database, istnieje również badania firmy Forrester: [Łączna liczba Economic Impact MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>Możliwości odświeżania DNS? 
  
Obecnie nie zapewniamy funkcji, aby odświeżyć konfigurację serwera DNS dla wystąpienia zarządzanego.

Po pewnym czasie odświeżeniu konfiguracji DNS:

- Jeśli dzierżawa DHCP wygasa.
- W uaktualnieniu platformy.

Jako obejście obniżenia poziomu wystąpienia zarządzanego na rdzeń wirtualny 4, a następnie uaktualnić go ponownie później. Ma to efekt uboczny odświeżanie konfiguracji DNS.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Wystąpienie zarządzane, można mieć statyczny adres IP?

W sytuacjach, w rzadkich, ale konieczne firma Microsoft może być konieczne wykonanie migracji online, wystąpienia zarządzanego do nowego klastra wirtualnego. W razie potrzeby tej migracji jest z powodu zmian w naszej stosu technologii mających na celu poprawę bezpieczeństwa i niezawodności usługi. Migracja do nowego klastra wirtualnego skutkuje zmiana adresu IP, który jest zamapowany na nazwę hosta wystąpienia zarządzanego. Usługa wystąpienia zarządzanego nie oświadczenia statyczne obsługę adresów IP i zastrzega sobie prawo do zmiany bez powiadomienia, jako część cykle regularnej konserwacji.

Z tego powodu firma Microsoft zdecydowanie odradzamy opierając się na niezmienności adresu IP, ponieważ może spowodować, że niepotrzebnych przestojów.


## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Czy mogę zmienić strefę czasową dla istniejącego wystąpienia zarządzanego

Można ustawić konfiguracji strefy czasowej, gdy wystąpienie zarządzane jest aprowizowane po raz pierwszy. Zmiana strefy czasowej istniejącego wystąpienia zarządzanego nie jest obsługiwana. Aby uzyskać więcej informacji, zobacz [ograniczenia strefy czasowej](sql-database-managed-instance-timezone.md#limitations).

Rozwiązania obejmują tworzenie nowego wystąpienia zarządzanego przy użyciu prawidłowego strefy czasowej i następnie albo ręczne wykonywanie kopii zapasowej i przywracania lub firma Microsoft zaleca, wykonywać [dla wielu wystąpień w momencie przywracania](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Jak rozwiązać problemy z wydajnością za pomocą Moje wystąpienia zarządzanego

Dla porównania wydajności między wystąpienia zarządzanego i programu SQL Server, to dobry punkt wyjścia [najlepsze rozwiązania dotyczące porównania wydajności między wystąpieniem usługi Azure SQL managed i programu SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Ładowania danych są często wolniej w wystąpieniu zarządzanym niż w programie SQL Server ze względu na model odzyskiwania pełnego obowiązkowe i [limity](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) na przepływność zapisu dziennika transakcji. Czasami to możesz pracować nad całym ładowania danych przejściowych do bazy danych tempdb, zamiast na bazę danych użytkownika lub za pomocą klastrowanego magazynu kolumn lub tabel zoptymalizowanych pod kątem pamięci.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Moje zaszyfrowane bazę danych można przywrócić do wystąpienia zarządzanego?

Tak, nie należy do odszyfrowania bazę danych, aby móc przywrócić go do wystąpienia zarządzanego. Musisz podać certyfikat/klucz używany jako funkcja ochrony klucza szyfrowania w systemie źródłowym do wystąpienia zarządzanego, aby można było odczytać danych z zaszyfrowanego pliku kopii zapasowej. Istnieją dwa sposoby, w tym celu:

- Przekaż certyfikat ochrony do wystąpienia zarządzanego. Można to zrobić tylko przy użyciu programu PowerShell. Przykładowy skrypt w tym artykule opisano cały proces.
- Przekaż funkcję ochrony klucza asymetrycznego do platformy Azure Key Vault (AKV) i wskaż wystąpienia zarządzanego. Podejście to przypomina bring-your-own key przypadek użycia TDE (BYOK), który używa także integracja do przechowywania klucza szyfrowania. Jeśli chcesz, klucz, który został przekazany do AKV dostępne dla wystąpienia zarządzanego przywracania szyfrowanymi bazami danych bez użycia klucza jako funkcja ochrony klucza szyfrowania, wykonaj instrukcje dotyczące konfigurowania funkcji TDE BYOK i nie zaznaczaj pola wyboru Utwórz wybrany klucz domyślną funkcję ochrony TDE.

Po wprowadzeniu szyfrowania ochrony dostępne dla wystąpienia zarządzanego, możesz przejść za pomocą procedury przywracania database w warstwie standardowa.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Jak mogę zmigrować z puli pojedyncze lub elastyczne usługi Azure SQL Database do wystąpienia zarządzanego? 

Zarządzane wystąpienia oferuje te same poziomy wydajności na rozmiar zasobów obliczeniowych i magazynu jako inne opcje wdrażania usługi Azure SQL Database. Jeśli chcesz Konsolidowanie danych w pojedynczym wystąpieniu lub po prostu potrzebujesz funkcji obsługiwane wyłącznie w przypadku wystąpienia zarządzanego, można migrować dane za pomocą funkcji eksportu i importu (BACPAC).
