---
title: Usługa Azure zalecenia dotyczące wydajności usługi Advisor | Dokumentacja firmy Microsoft
description: Używaj usługi Advisor w celu zoptymalizowania wydajności wdrożeń platformy Azure.
services: advisor
documentationcenter: NA
author: kasparks
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kasparks
ms.openlocfilehash: 53571fa0c29c4b9bf5493a1e558356eba38fe617
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244420"
---
# <a name="advisor-performance-recommendations"></a>Zalecenia dotyczące wydajności usługi Advisor

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

Klasyfikator zidentyfikuje maszyny wirtualne, które należą do konta magazynu, która wkrótce osiągnie docelową wartość skalowalności. Dzięki temu narażone na ograniczenia wydajności operacji We/Wy. Advisor zaleci, że te maszyny wirtualne używać dysków Managed Disks, zapobiegając obniżeniu wydajności.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Aby zwiększyć wydajność i niezawodność dysków maszyny wirtualnej za pomocą usługi Premium Storage

Advisor ustala maszyn wirtualnych przy użyciu dysków, które mają dużej liczby transakcji na koncie magazynu w warstwie standardowa i zaleca się uaktualnienie do dysków w warstwie premium. 

Usługa Azure Premium Storage zapewnia obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych z systemem wyjścia — dużych obciążeń wejścia /. Dyski maszyn wirtualnych, które używają kont usługi premium storage umożliwia przechowywanie danych na dyskach półprzewodnikowych (SSD). Aby uzyskać najlepszą wydajność aplikacji firma Microsoft zaleca, poddane migracji wszystkie dyski maszyny wirtualnej, wymagających wysokiej operacje We/Wy do magazynu premium storage.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Usuwanie danych pochylenia tabeli magazynu danych SQL, tak aby zwiększyć wydajność zapytań

Niesymetryczność danych może spowodować wąskie gardła przenoszenia lub zasób zbędnych danych, podczas uruchamiania obciążenia. Klasyfikator wykryje danych dystrybucji pochylanie większy niż 15% i zaleca, aby ponownie dystrybuować swoje dane, a następnie ponownie wybory klucza dystrybucji tabel. Aby dowiedzieć się więcej na temat identyfikowania i usuwania niesymetryczność, zobacz [Rozwiązywanie problemów z niesymetryczności](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Utwórz lub zaktualizuj nieaktualnych tabela statystyk dotyczących tabeli magazynu danych SQL, tak aby zwiększyć wydajność zapytań

Klasyfikator identyfikuje tabele, które nie mają aktualnych [Statystyka tabeli](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) i zaleca tworzenia lub aktualizowania tabeli statystyk. Zapytanie, że optymalizator używa aktualnych danych statycznych do szacowania kardynalności lub liczbę wierszy w wyniku zapytania, co umożliwia Optymalizator zapytań utworzyć planu zapytania o wysokiej jakości największą wydajność magazynu danych SQL.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skalowanie w górę do optymalizacji wykorzystania pamięci podręcznej w tabelach usługi SQL Data Warehouse, aby zwiększyć wydajność zapytań

Usługa Azure Advisor wykrywa, jeśli usługi SQL Data Warehouse ma wysoki pamięć podręczna używana wartość procentowa i niskiej trafień procent. Oznacza to, eksmisji wysokiej pamięci podręcznej, który może mieć wpływ na wydajność usługi SQL Data Warehouse. Klasyfikator sugeruje, skalowanie usługi SQL Data Warehouse, aby upewnić się, że zostało przydzielone wystarczająco dużo pojemności pamięci podręcznej dla danego obciążenia.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Konwertowanie tabel SQL Data Warehouse na zreplikowanych tabel w celu zwiększenia wydajności zapytań

Klasyfikator identyfikuje tabele, które nie są zreplikowane tabele, ale będą korzystać z konwersji i sugeruje, konwertowania tych tabel. Zalecenia są oparte na rozmiar replikowanej tabeli, liczbą kolumn, typ dystrybucji tabeli i liczba partycji w tabeli SQL Data Warehouse. Dodatkowe algorytmy heurystyczne mogą być udostępniane w zalecenia dla kontekstu. Aby dowiedzieć się więcej o sposobie tego zalecenia, zobacz [zalecenia dotyczące usługi SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrację konta magazynu usługi Azure Resource Manager do wszystkich najnowszych funkcji platformy Azure

Przeprowadź migrację do usługi Azure Resource Manager (ARM) było czerpanie korzyści z wdrożenia szablonu, dodatkowe opcje zabezpieczeń i możliwość podniesienia poziomu do konta GPv2 w celu wykorzystania najnowszych funkcji usługi Azure Storage modelu wdrażania konta magazynu. Klasyfikatora określi, że wszystkie konta magazynu autonomicznych, które korzystają z klasycznego modelu wdrażania i zaleca się migrację do modelu wdrażania ARM.

Alertów klasycznych w usłudze Azure Monitor jest ogłaszany wycofywania w czerwcu 2019, zalecane jest, Uaktualnij swoje konto klasycznego magazynu do ARM, aby zachować funkcje alertów z nową platformę alertów. Aby uzyskać więcej informacji, zobacz [klasycznego wycofanie alerty](https://azure.microsoft.com/en-us/updates/classic-alerting-monitoring-retirement/).

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
