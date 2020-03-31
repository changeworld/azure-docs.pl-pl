---
title: Często zadawane pytania — usługa migracji bazy danych platformy Azure
description: Często zadawane pytania dotyczące korzystania z usługi migracji bazy danych Platformy Azure do przeprowadzania migracji bazy danych.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a664f12843585ac7524cf8d51aef156d15d32504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650985"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Często zadawane pytania dotyczące korzystania z usługi migracji bazy danych platformy Azure

W tym artykule wymieniono często zadawane pytania dotyczące korzystania z usługi migracji bazy danych Azure wraz z powiązanymi odpowiedziami.

## <a name="overview"></a>Omówienie

**P. Co to jest usługa migracji bazy danych platformy Azure?**
Usługa migracji bazy danych Azure to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemowej migracji z wielu źródeł bazy danych do platform Azure Data przy minimalnym przestoju. Usługa jest obecnie w ogólnej dostępności, a bieżące działania rozwojowe koncentrują się na:

* Niezawodność i wydajność.
* Iteracyjny dodatek par źródłowych i docelowych.
* Dalsze inwestycje w migracje bez tarcia.

**P. Jakie pary źródłowe/docelowe obsługuje obecnie usługa migracji bazy danych Platformy Azure?**
Usługa obsługuje obecnie różne pary źródłowe/docelowe lub scenariusze migracji. Aby uzyskać pełną listę stanu każdego dostępnego scenariusza migracji, zobacz artykuł [Stan scenariuszy migracji obsługiwanych przez usługę migracji bazy danych Platformy Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Inne scenariusze migracji są w wersji zapoznawczej i wymagają przesłania nominacji za pośrednictwem witryny wersji zapoznawczej systemu DMS. Aby uzyskać pełną listę scenariuszy w wersji zapoznawczej i zarejestrować się, aby wziąć udział w jednej z tych ofert, zobacz [witrynę podglądu DMS](https://aka.ms/dms-preview/).

**P. Jakie wersje programu SQL Server obsługuje usługa migracji bazy danych Azure jako źródło?**
Podczas migracji z programu SQL Server obsługiwane źródła usługi migracji bazy danych azure to SQL Server 2005 za pośrednictwem programu SQL Server 2019.

**Pyt.: Podczas korzystania z usługi migracji bazy danych Azure, jaka jest różnica między migracji w trybie offline i online?**
Usługi migracji bazy danych azure można używać do przeprowadzania migracji w trybie offline i online. Po zakończeniu migracji *w trybie offline* przestoj aplikacji rozpoczyna się po rozpoczęciu migracji. W wyniku migracji *online* przestoje są ograniczone do czasu, w który można ograniczyć się pod koniec migracji. Zalecamy przetestowanie migracji offline w celu ustalenia, czy przestój jest dopuszczalny. Jeśli nie jest on dopuszczalny, przeprowadź migrację online.

> [!NOTE]
> Korzystanie z usługi migracji bazy danych Azure do wykonywania migracji online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

**P. Jak usługa migracji bazy danych platformy Azure w porównaniu z innymi narzędziami migracji bazy danych firmy Microsoft, takimi jak Asystent migracji bazy danych (DMA) lub Asystent migracji programu SQL Server (SSMA)?**
Usługa migracji bazy danych platformy Azure jest preferowaną metodą migracji bazy danych do platformy Microsoft Azure na dużą skalę. Aby uzyskać więcej informacji na temat porównywania usługi migracji bazy danych platformy Azure z innymi narzędziami migracji bazy danych firmy Microsoft oraz zaleceń dotyczących korzystania z usługi w różnych scenariuszach, zobacz blog publikujący w blogu [Differentiating Microsoft Database Migration Tools and Services](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529).

**P. Jak usługa migracji bazy danych platformy Azure w porównaniu z ofertą migracji platformy Azure?**
Usługa Azure Migrate pomaga w migracji lokalnych maszyn wirtualnych do usługi Azure IaaS. Usługa ocenia przydatność migracji i rozmiary oparte na wydajności i zapewnia szacunki kosztów uruchamiania lokalnych maszyn wirtualnych na platformie Azure. Usługa Azure Migrate jest przydatna w przypadku migracji lokalnych obciążeń opartych na maszynach wirtualnych do maszyn wirtualnych platformy Azure IaaS. Jednak w przeciwieństwie do usługi migracji bazy danych Azure usługa migracji usługi Azure Migrate nie jest wyspecjalizowaną usługą migracji bazy danych dla relacyjnych platform baz danych usługi Azure PaaS, takich jak usługa Azure SQL Database lub wystąpienie zarządzanego usługi Azure SQL Database.

## <a name="setup"></a>Konfiguracja

**P. Jakie są wymagania wstępne dotyczące korzystania z usługi migracji bazy danych platformy Azure?**
Istnieje kilka wymagań wstępnych wymaganych, aby upewnić się, że usługa migracji bazy danych Azure działa płynnie podczas wykonywania migracji bazy danych. Niektóre wymagania wstępne mają zastosowanie we wszystkich scenariuszach (pary źródłowe docelowe) obsługiwane przez usługę, podczas gdy inne wymagania wstępne są unikatowe dla określonego scenariusza.

Wymagania wstępne usługi migracji bazy danych platformy Azure, które są wspólne dla wszystkich obsługiwanych scenariuszy migracji obejmują potrzebę:

* Utwórz usługę migracji bazy danych usługi Azure Database dla platformy Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Upewnij się, że reguły sieciowej sieci owej sieci nie blokują następujących portów komunikacyjnych 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Podczas korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory, aby umożliwić usłudze migracji usługi Azure Database dostęp do źródłowych baz danych w celu migracji.

Aby uzyskać listę wszystkich wymagań wstępnych wymaganych do konkurowania z określonymi scenariuszami migracji przy użyciu usługi migracji bazy danych Platformy Azure, zobacz powiązane samouczki w [dokumentacji](https://docs.microsoft.com/azure/dms/dms-overview) usługi migracji bazy danych platformy Azure na docs.microsoft.com.

**P. Jak znaleźć adres IP usługi migracji bazy danych Platformy Azure, aby utworzyć listę dozwolonych dla reguł zapory używanych do uzyskiwania dostępu do mojej źródłowej bazy danych w celu migracji?**
Może być konieczne dodanie reguł zapory umożliwiających usłudze migracji bazy danych platformy Azure dostęp do źródłowej bazy danych w celu migracji. Adres IP usługi jest dynamiczny, ale jeśli używasz usługi ExpressRoute, ten adres jest przypisany prywatnie przez sieć firmową. Najprostszym sposobem zidentyfikowania odpowiedniego adresu IP jest szukanie w tej samej grupie zasobów, co aprowizowany zasób usługi migracji bazy danych Azure Database w celu znalezienia skojarzonego interfejsu sieciowego. Zazwyczaj nazwa zasobu interfejsu sieciowego zaczyna się od prefiksu karty sieciowej, a następnie unikatowa sekwencja znaków i numerów, na przykład NIC-jj6tnztnmarpsskr82rbndyp. Wybierając ten zasób interfejsu sieciowego, można zobaczyć adres IP, który musi zostać uwzględniony na liście dozwolonych na stronie portalu Azure omówienie zasobów.

Może być również konieczne dołączenie źródła portu, które sql server nasłuchuje na liście dozwolonych. Domyślnie jest to port 1433, ale źródłowy program SQL Server może być skonfigurowany do nasłuchiwać również na innych portach. W takim przypadku należy również uwzględnić te porty na liście dozwolonych. Port, na który nasłuchuje program SQL Server, można określić za pomocą kwerendy dynamicznego widoku zarządzania:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Można również określić port, który program SQL Server nasłuchuje, odpytując dziennik błędów programu SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**P. Jak skonfigurować sieć wirtualną platformy Microsoft Azure?**
Podczas gdy wiele samouczków firmy Microsoft, które mogą cię przez proces konfigurowania sieci wirtualnej, oficjalna dokumentacja pojawia się w artykule [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Sposób użycia

**P. Co to jest podsumowanie kroków wymaganych do korzystania z usługi migracji bazy danych Azure do przeprowadzenia migracji bazy danych?**
Podczas typowej, prostej migracji bazy danych:

1. Tworzenie docelowych baz danych.
2. Oceń źródłowe bazy danych.
    * W przypadku migracji jednorodnych należy ocenić istniejące bazy danych za pomocą [narzędzia DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * W przypadku migracji heterogenicznych (ze źródeł konkurencji) należy ocenić istniejące bazy danych za pomocą [programu SSMA](https://aka.ms/get-ssma). Funkcja SSMA służy również do konwertowania obiektów bazy danych i migracji schematu do platformy docelowej.
3. Utwórz wystąpienie usługi Azure Database Migration Service.
4. Utwórz projekt migracji określający źródłowe bazy danych, docelowe bazy danych i tabele do migracji.
5. Uruchom pełne obciążenie.
6. Wybierz kolejne sprawdzanie poprawności.
7. Wykonaj ręczne przełączanie środowiska produkcyjnego do nowej bazy danych opartej na chmurze.

## <a name="troubleshooting-and-optimization"></a>Rozwiązywanie problemów i optymalizacja

**P. Konfiguruję projekt migracji w dms i mam trudności z połączeniem się z źródłową bazą danych. Co należy zrobić?**
Jeśli masz problemy z połączeniem się z systemem źródłowej bazy danych podczas pracy nad migracją, utwórz maszynę wirtualną w sieci wirtualnej, za pomocą której skonfigurowane jest wystąpienie DMS. Na maszynie wirtualnej powinieneś być w stanie uruchomić test połączenia, na przykład przy użyciu pliku UDL do testowania połączenia z programem SQL Server lub pobraniem Robo 3T w celu przetestowania połączeń MongoDB. Jeśli test połączenia zakończy się pomyślnie, nie powinien mieć problemu z łączeniem się ze źródłową bazą danych. Jeśli test połączenia nie powiedzie się, skontaktuj się z administratorem sieci.

**P. Dlaczego moja usługa migracji bazy danych Azure jest niedostępna lub zatrzymana?**
Jeśli użytkownik jawnie zatrzymuje usługę Azure Database Migration Service (DMS) lub jeśli usługa jest nieaktywna przez okres 24 godzin, usługa będzie w stanie zatrzymania lub automatycznego wstrzymania. W każdym przypadku usługa będzie niedostępna i w stanie zatrzymania.  Aby wznowić aktywne migracje, uruchom ponownie usługę.

**P. Czy są jakieś zalecenia dotyczące optymalizacji wydajności usługi migracji bazy danych platformy Azure?**
Aby przyspieszyć migrację bazy danych, można wykonać kilka czynności, aby przyspieszyć migrację bazy danych za pomocą usługi:

* Podczas tworzenia wystąpienia usługi należy używać warstwy cenowej ogólnego przeznaczenia wielu procesorów CPU, aby umożliwić usłudze korzystanie z wielu procesorów wirtualnych w celu równoległości i szybszego transferu danych.
* Tymczasowo skaluj swoje wystąpienie docelowe bazy danych SQL usługi Azure do jednostki SKU warstwy Premium podczas operacji migracji danych, aby zminimalizować ograniczanie przepustowości bazy danych SQL platformy Azure, które mogą mieć wpływ na działania związane z transferem danych podczas korzystania z jednostek SKU niższego poziomu.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem usługi migracji bazy danych platformy Azure i dostępności regionalnej, zobacz artykuł [Co to jest usługa migracji bazy danych platformy Azure](dms-overview.md).
