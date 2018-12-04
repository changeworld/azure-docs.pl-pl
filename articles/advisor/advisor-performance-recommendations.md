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
ms.openlocfilehash: ba79d2d5ed4350960af1b92ee863595e3540a1d2
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843931"
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

## <a name="improve-redis-cache-performance-and-reliability"></a>Zwiększ wydajność pamięci podręcznej redis Cache i niezawodność

Klasyfikator identyfikuje wystąpienia pamięci podręcznej redis cache, gdzie mogą być niekorzystny wpływ na wydajność wysokiego użycia pamięci, obciążenie serwera, przepustowość sieci lub dużej liczby połączeń klienta. Advisor udostępnia także najlepsze rozwiązania zalecenia, aby uniknąć potencjalnych problemów. Aby uzyskać więcej informacji na temat zaleceń dotyczących pamięci podręcznej redis Cache zobacz [Redis Cache Advisor](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Zwiększ wydajność usługi App Service i niezawodność

Usługa Azure Advisor integruje się poniżej rekomendowane najlepsze rozwiązania dla środowiska usług aplikacji i rozszerzają zakres odnajdywania możliwości platformy. Zalecenia dotyczące usług aplikacji należą:
* Wykrywanie wystąpienia, gdzie przez programy obsługi aplikacji przy użyciu opcji ograniczania ryzyka wyczerpania pamięci lub zasobów procesora CPU.
* Wykrywanie wystąpienia, gdzie collocating zasoby, takie jak aplikacje sieci web i baz danych może zwiększyć wydajność i niższe koszty. 

Aby uzyskać więcej informacji o zaleceniach App Services, zobacz [najlepsze rozwiązania dotyczące usługi Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Usuwanie danych pochylenia tabeli magazynu danych SQL, tak aby zwiększyć wydajność zapytań

Niesymetryczność danych może spowodować wąskie gardła przenoszenia lub zasób zbędnych danych, podczas uruchamiania obciążenia. Klasyfikator wykryje danych dystrybucji pochylanie większy niż 15% i zaleca, aby ponownie dystrybuować swoje dane, a następnie ponownie wybory klucza dystrybucji tabel. Aby dowiedzieć się więcej na temat identyfikowania i usuwania niesymetryczność, zobacz [Rozwiązywanie problemów z niesymetryczności](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Utwórz lub zaktualizuj nieaktualnych tabela statystyk dotyczących tabeli magazynu danych SQL, tak aby zwiększyć wydajność zapytań

Klasyfikator identyfikuje tabele, które nie mają aktualnych [Statystyka tabeli](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) i zaleca tworzenia lub aktualizowania tabeli statystyk. Zapytanie, że optymalizator używa aktualnych danych statycznych do szacowania kardynalności lub liczbę wierszy w wyniku zapytania, co umożliwia Optymalizator zapytań utworzyć planu zapytania o wysokiej jakości największą wydajność magazynu danych SQL.

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrację konta magazynu usługi Azure Resource Manager do wszystkich najnowszych funkcji platformy Azure

Przeprowadź migrację do usługi Azure Resource Manager (ARM) było czerpanie korzyści z wdrożenia szablonu, dodatkowe opcje zabezpieczeń i możliwość podniesienia poziomu do konta GPv2 w celu wykorzystania najnowszych funkcji usługi Azure Storage modelu wdrażania konta magazynu. Klasyfikatora określi, że wszystkie konta magazynu autonomicznych, które korzystają z klasycznego modelu wdrażania i zaleca się migrację do modelu wdrażania ARM. 

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

