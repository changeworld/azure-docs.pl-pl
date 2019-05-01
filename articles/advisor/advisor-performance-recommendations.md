---
title: Poprawianie wydajności aplikacji Azure za pomocą usługi Azure Advisor | Dokumentacja firmy Microsoft
description: Używaj usługi Advisor w celu zoptymalizowania wydajności wdrożeń platformy Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 5850b683189136eac70451075933b0c57ecc37cd
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920442"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Poprawianie wydajności aplikacji Azure za pomocą usługi Azure Advisor

Zalecenia dotyczące wydajności usługi Azure Advisor zwiększyć szybkość i czas odpowiedzi aplikacji krytyczne dla prowadzonej działalności. Możesz uzyskać zalecenia dotyczące wydajności usługi Advisor na **wydajności** karty Pulpit nawigacyjny usługi Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Zmniejsz czas wygaśnięcia w profilu usługi Traffic Manager awaryjnie do dobrej kondycji punktów końcowych szybciej DNS

[Czas wygaśnięcia (TTL) ustawienia](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) w profilu usługi Traffic Manager pozwala użytkownikowi na określenie jak szybko przełączyć punktów końcowych, jeśli dany punkt końcowy nie odpowiada na zapytania. Zmniejszenie wartości TTL oznacza, że klienci będą kierowane do działają punkty końcowe, które są szybsze.

Usługa Azure Advisor identyfikuje profile usługi Traffic Manager przy użyciu dłuższego czasu wygaśnięcia skonfigurowane i zaleca Konfigurowanie czasu wygaśnięcia do 20 sekund lub 60 sekund w zależności od tego, czy profil, który jest skonfigurowany dla [szybkiego trybu Failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Zwiększ wydajność bazy danych dzięki funkcji SQL DB Advisor

Advisor zapewnia spójne, skonsolidowanego widoku zaleceń dotyczących wszystkich zasobów platformy Azure. Integruje się z funkcji SQL Database Advisor, aby zapewnić Ci zalecenia dotyczące poprawy wydajności bazy danych SQL Azure. SQL Database Advisor ocenia wydajność bazy danych SQL Azure, analizując Twojej historii użycia. Oferuje rekomendacje, które są dopasowane do typowego obciążenie bazy danych.

> [!NOTE]
> Można pobrać zaleceń, baza danych musi mieć o tydzień użycia, a w ciągu tego tygodnia musi być jakieś działania, spójne. SQL Database Advisor można zoptymalizować łatwiej wzorców zapytań spójne niż wzmożeniach losowe działania.

Aby uzyskać więcej informacji na temat funkcji SQL Database Advisor, zobacz [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Zwiększ wydajność usługi App Service i niezawodność

Usługa Azure Advisor integruje się poniżej rekomendowane najlepsze rozwiązania dla środowiska usług aplikacji i rozszerzają zakres odnajdywania możliwości platformy. Zalecenia dotyczące usług aplikacji należą:
* Wykrywanie wystąpienia, gdzie przez programy obsługi aplikacji przy użyciu opcji ograniczania ryzyka wyczerpania pamięci lub zasobów procesora CPU.
* Wykrywanie wystąpienia, gdzie collocating zasoby, takie jak aplikacje sieci web i baz danych może zwiększyć wydajność i niższe koszty.

Aby uzyskać więcej informacji o zaleceniach App Services, zobacz [najlepsze rozwiązania dotyczące usługi Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Użycie usługi Managed Disks, aby zapobiegać ograniczaniu przepływności dysków operacji We/Wy

Klasyfikator zidentyfikuje maszyny wirtualne, które należą do konta magazynu, która wkrótce osiągnie docelową wartość skalowalności. Ten warunek sprawia, że te maszyny wirtualne narażone na ograniczenia wydajności operacji We/Wy. Klasyfikator zaleci, którego używają dysków zarządzanych, zapobiegając obniżeniu wydajności.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Aby zwiększyć wydajność i niezawodność dysków maszyny wirtualnej za pomocą usługi Premium Storage

Advisor ustala maszyn wirtualnych przy użyciu dysków, które mają dużej liczby transakcji na koncie magazynu w warstwie standardowa i zaleca się uaktualnienie do dysków w warstwie premium. 

Usługa Azure Premium Storage zapewnia obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych z systemem wyjścia — dużych obciążeń wejścia /. Dyski maszyn wirtualnych, które używają kont usługi premium storage umożliwia przechowywanie danych na dyskach półprzewodnikowych (SSD). Aby uzyskać najlepszą wydajność aplikacji firma Microsoft zaleca, poddane migracji wszystkie dyski maszyny wirtualnej, wymagających wysokiej operacje We/Wy do magazynu premium storage.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Usuwanie danych pochylenia tabeli magazynu danych SQL, tak aby zwiększyć wydajność zapytań

Niesymetryczność danych może spowodować wąskie gardła przenoszenia lub zasób zbędnych danych, podczas uruchamiania obciążenia. Klasyfikator wykryje danych dystrybucji pochylanie większy niż 15% i zaleca, aby ponownie dystrybuować swoje dane, a następnie ponownie wybory klucza dystrybucji tabel. Aby dowiedzieć się więcej na temat identyfikowania i usuwania niesymetryczność, zobacz [Rozwiązywanie problemów z niesymetryczności](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Utwórz lub zaktualizuj nieaktualnych tabela statystyk dotyczących tabeli magazynu danych SQL, tak aby zwiększyć wydajność zapytań

Klasyfikator identyfikuje tabele, które nie mają aktualnych [Statystyka tabeli](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) i zaleca tworzenia lub aktualizowania tabeli statystyk. Zapytanie, że optymalizator używa aktualnych danych statycznych do szacowania kardynalności lub liczbę wierszy w wyniku zapytania, który umożliwia Optymalizator zapytań utworzyć plan zapytania wysokiej jakości największą wydajność magazynu danych SQL.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skalowanie w górę do optymalizacji wykorzystania pamięci podręcznej w tabelach usługi SQL Data Warehouse, aby zwiększyć wydajność zapytań

Usługa Azure Advisor wykrywa, jeśli usługi SQL Data Warehouse ma wysoki pamięć podręczna używana wartość procentowa i niskiej trafień procent. Ten stan wskazuje eksmisji wysokiej pamięci podręcznej, który może mieć wpływ na wydajność usługi SQL Data Warehouse. Klasyfikator sugeruje, skalowanie usługi SQL Data Warehouse, aby upewnić się, że zostało przydzielone wystarczająco dużo pojemności pamięci podręcznej dla danego obciążenia.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Konwertowanie tabel SQL Data Warehouse na zreplikowanych tabel w celu zwiększenia wydajności zapytań

Klasyfikator identyfikuje tabele, które nie są zreplikowane tabele, ale będą korzystać z konwersji i sugeruje, konwertowania tych tabel. Zalecenia są oparte na rozmiar replikowanej tabeli, liczbą kolumn, typ dystrybucji tabeli i liczba partycji w tabeli SQL Data Warehouse. Dodatkowe algorytmy heurystyczne mogą być udostępniane w zalecenia dla kontekstu. Aby dowiedzieć się więcej o sposobie tego zalecenia, zobacz [zalecenia dotyczące usługi SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrację konta magazynu usługi Azure Resource Manager do wszystkich najnowszych funkcji platformy Azure

Przeprowadź migrację konta magazynu modelu wdrażania do usługi Azure Resource Manager (Menedżer zasobów) może korzystać z wdrożeń szablonu, dodatkowe opcje zabezpieczeń i możliwość podniesienia poziomu do konta GPv2 w celu wykorzystania najnowszych funkcji usługi Azure Storage. Klasyfikatora określi, że wszystkie konta magazynu autonomicznych, które korzystają z klasycznego modelu wdrażania i zaleca się migrację do modelu wdrażania usługi Resource Manager.

> [!NOTE]
> Alertów klasycznych w usłudze Azure Monitor są planowane do wycofania w czerwcu 2019 r. Firma Microsoft zaleca się uaktualnienie konta klasycznego magazynu, zachować funkcje alertów z nową platformę przy użyciu usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [klasycznego wycofanie alerty](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Projektowanie konta magazynu, aby zapobiec osiągnięciu limitu maksymalnej subskrypcji

Region platformy Azure może obsługiwać maksymalnie 250 kont magazynu na subskrypcję. Po osiągnięciu limitu nie można utworzyć kolejnych kont magazynu w tej kombinacji regionu i subskrypcji. Klasyfikator sprawdzi subskrypcji i powierzchni zalecenia dotyczące projektowania dla mniejszej liczby kont magazynu w dowolnej znajdują się blisko osiągnięcia maksymalnego limitu.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optymalizuj wydajność serwerów usługi Azure MySQL, Azure PostgreSQL i Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Rozwiązywanie wykorzystanie procesora CPU, serwerów MySQL na platformie Azure, Azure PostgreSQL i Azure MariaDB z wąskich gardeł procesora CPU
Wysokie użycie procesora przez dłuższy czas może spowodować wolnych zapytań wydajności dla obciążenia. Zwiększenie rozmiaru Procesora pomóc w optymalizacji środowiska uruchomieniowego zapytania do bazy danych i zwiększyć ogólną wydajność. Usługa Azure Advisor identyfikuje serwery wysokie wykorzystanie procesora CPU, który prawdopodobnie działają obciążenia procesora CPU ograniczone i zalecamy skalowanie zasobów obliczeniowych.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Zmniejszyć ograniczenia pamięci na serwerach Azure MySQL, Azure PostgreSQL i Azure MariaDB lub przejście do pamięci zoptymalizowane pod kątem jednostki SKU
Współczynnik trafień pamięci podręcznej niski może skutkować mniejszą wydajność zapytań i zwiększonej operacje We/Wy. Może to wynikać z planu zapytania lub uruchamiania obciążenia intensywnie korzystających z pamięci. Naprawianie planu zapytania lub [zwiększenie ilości pamięci](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) usługi Azure Database dla serwera bazy danych PostgreSQL, serwer bazy danych Azure MySQL lub Azure MariaDB server będzie optymalizowania wykonywania obciążenie bazy danych. Usługa Azure Advisor identyfikuje serwery, których to dotyczy, z powodu tych zmian puli buforów o wysokiej i zaleca zastosowanie ustalania planu zapytania, przejście do wyższej wersji jednostki SKU z większej ilości pamięci lub zwiększenie rozmiaru magazynu, aby uzyskać więcej operacji We/Wy.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Umożliwia skalowanie w poziomie operacje odczytu dla obciążeń intensywnie korzystających z odczytu Azure MySQL i Azure PostgreSQL odczytu repliki
Usługa Azure Advisor wykorzystuje oparte na obciążeniu Algorytm heurystyczny, takie jak stosunek odczytów w stosunku do zapisów na serwerze w ciągu ostatnich siedmiu dni do identyfikowania intensywnie odczytujących obciążeń. Twoja usługa Azure database for postgresql w warstwie zasobów lub usługa Azure database for MySQL zasobu o stosunku bardzo duże odczyt/zapis może spowodować rywalizacje procesora CPU lub pamięci, co prowadzi do wydłużenia wydajność zapytań. Dodawanie [repliki](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) zapewni pomoc przy określaniu skalowanie w poziomie odczytów w stosunku do serwera repliki, zapobiegając ograniczenia procesora CPU lub pamięci na serwerze podstawowym. Klasyfikator zidentyfikuje serwerów za pomocą takich wysokiej intensywnie odczytujących obciążeń i zaleca się dodanie [odczytu replik](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) odciążania część obciążeniami odczytu.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Skalowanie serwera usługi Azure MySQL, Azure PostgreSQL lub MariaDB platformy Azure do wyższej jednostki SKU, aby zapobiec ograniczeń połączeń
Każdego nowego połączenia z serwerem bazy danych zajmuje trochę pamięci. Spadku wydajności serwera bazy danych, jeśli połączenia z serwerem kończą się niepowodzeniem z powodu [górny limit](https://docs.microsoft.com/azure/postgresql/concepts-limits) w pamięci. Usługa Azure Advisor zidentyfikuje serwerów z systemem za pomocą wielu błędów połączenia i zaleca się uaktualnienie serwera limitów połączeń zapewnienie większej ilości pamięci do serwera przez skalowanie w górę obliczeń lub przy użyciu pamięci zoptymalizowane pod kątem SKU, która ma większą moc obliczeniową na każdy rdzeń.

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Jak uzyskać dostęp zalecenia dotyczące wydajności w usługi Advisor

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor kliknij **wydajności** kartę.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zalecenia usługi Advisor, zobacz:

* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Rekomendacji dotyczących kosztu usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
