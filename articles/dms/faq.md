---
title: Często zadawane pytania dotyczące korzystania z usługi migracji bazy danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się często zadawane pytania dotyczące korzystania z usługi migracji bazy danych Azure do przeprowadzania migracji bazy danych.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 85052e1363ebbfe21cd7d6d5b3720f79cec7c417
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Często zadawane pytania dotyczące korzystania z usługi migracji bazy danych Azure
W tym artykule wymieniono często zadawane pytania dotyczące korzystania z usługi migracji bazy danych Azure oraz powiązanych odpowiedzi.

### <a name="q-what-is-azure-database-migration-service"></a>Q. Co to jest usługa migracji bazy danych Azure?
Usługa Azure bazy danych migracji jest pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemowego migracji z wielu źródeł bazy danych do platformy Azure danych z minimalnym czasem przestojów. Usługa jest obecnie w wersji zapoznawczej, z programistycznych koncentruje się na:
- Niezawodność i wydajność.
- Iteracyjny dodanie par źródłowy i docelowy.
- Dalsze inwestycje w bez tarcia migracji.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>Q. Jakie pary źródłowy i docelowy usługa migracji bazy danych Azure obecnie obsługuje?
Usługi w publicznej wersji zapoznawczej obsługuje obecnie migracji z programu SQL Server z bazą danych SQL Azure i można przejść do portalu Azure teraz, aby rozpocząć korzystanie z usługi migracji bazy danych Azure, w tym scenariuszu. Inne pary źródłowy i docelowy, takich jak SQL Server do wystąpienia zarządzane bazy danych SQL Azure i Oracle do bazy danych SQL Azure są dostępne za pośrednictwem ograniczone prywatnej wersji zapoznawczej. Dla możliwość uczestnictwa w ograniczonym prywatnej wersji zapoznawczej te scenariusze, zarejestruj się [tutaj](https://sqldatabase-migrationpreview.azurewebsites.net/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>Q. Jak usługa Azure bazy danych migracji porównać do innych narzędzi migracji bazy danych firmy Microsoft, takich jak bazy danych migracji Asystenta (DMA) lub SQL Server Migration Assistant (SSMA)?
Usługa Azure bazy danych migracji jest preferowaną metodą migracja bazy danych do systemu Microsoft Azure na dużą skalę. Aby uzyskać więcej szczegółów na jak usługa Azure bazy danych migracji porównuje do innych firmy Microsoft bazy danych narzędzi migracji, a aby uzyskać zalecenia dotyczące korzystania z usługi dla różnych scenariuszy, zobacz Publikowanie blogu [rozróżnianie Microsoft bazy danych Narzędzia migracji i usługi](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>Q. Jak usługa Azure bazy danych migracji porównania z ofertą Azure migracji?
Migracja lokalnych maszyn wirtualnych IaaS platformy Azure obsługuje usługę Azure migracji. Usługa ocenia przydatność migracji i wydajności na podstawie rozmiaru i zapewnia szacowanych kosztów działających maszyn wirtualnych lokalnie na platformie Azure. Azure migracji jest przydatne w przypadku migracji przyrostu shift lokalnej maszyny Wirtualnej na podstawie obciążenia do maszyn wirtualnych IaaS platformy Azure. Jednak w przeciwieństwie do usługi Azure migracji bazy danych Azure migracji nie jest usługą migracji specjalne bazy danych oferty dla platform relacyjnej bazy danych Azure PaaS, takich jak usługi Azure SQL Database lub SQL Azure lub wystąpienia zarządzane bazy danych SQL Azure.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>Q. Co to jest podsumowanie kroków wymaganych do korzystania z usługi migracji bazy danych Azure, aby przeprowadzić migrację bazy danych?
Podczas migracji typowe, prosty bazy danych możesz:
1.  Tworzenie baz danych docelowych.
2.  Migrowanie schematu baz danych za pomocą [Asystenta migracji bazy danych](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Utwórz wystąpienie usługi migracji bazy danych Azure.
4.  Utwórz projekt migracji określenie źródła baz danych, docelowy baz danych i tabele, aby przeprowadzić migrację.
5.  Zainicjuj pełnego obciążenia.
6.  Wybierz kolejne sprawdzania poprawności.
7.  Wykonaj ręczne przełączenie środowiska produkcyjnego do nowej bazy danych opartej na chmurze. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>Q. Jakie są wymagania wstępne dotyczące korzystania z usługi migracji bazy danych Azure?
Istnieje kilka wymagań wstępnych dotyczących upewnić, że usługa migracji bazy danych Azure przeprowadzane bezproblemowego podczas przeprowadzania migracji bazy danych. Niektóre wymagania wstępne są stosowane we wszystkich scenariuszach (pary źródłowy i docelowy), obsługiwane przez usługę, podczas gdy inne wymagania wstępne są unikatowe dla danego scenariusza.
Azure wymagania wstępne usługi migracji bazy danych, które są wspólne dla wszystkich obsługiwane scenariusze migracji obejmują konieczność:
- Tworzenie sieci Wirtualnej dla usługi Azure migracji bazy danych przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia połączenie lokacja lokacja z serwerami lokalnymi źródła przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Upewnij się, że Twoje Azure (VNET) sieciowej grupy zabezpieczeń sieci wirtualnej reguły blok następujący komunikat porty 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na filtrowanie ruchu NSG sieci Wirtualnej Azure, zobacz artykuł [filtrowania ruchu sieciowego z grup zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Podczas korzystania z urządzenia zapory przed baz danych z źródła, może być konieczne dodanie reguły zapory, aby umożliwić dostęp do bazy danych źródłowego do migracji z usługi migracji bazy danych Azure.
 
Listę wszystkich wymagań wstępnych dotyczących konkurować scenariusze migracji przy użyciu usługi migracji bazy danych Azure, zobacz pokrewne samouczków w usłudze Azure bazy danych migracji [dokumentacji](https://docs.microsoft.com/azure/dms/dms-overview) na docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>Q. Jak znaleźć adres IP dla usługi Azure bazy danych migracji, w którym można utworzyć listy dozwolonych dla reguł zapory umożliwiające dostęp do bazy danych źródłowego do migracji?
Może być konieczne dodanie reguły zapory, dzięki czemu usługa migracji bazy danych Azure na dostęp do bazy danych źródłowego do migracji. Adres IP dla usługi jest dynamiczny, ale jeśli używasz Express Route sieci firmowej przypisano prywatnie ten adres. Najprostszym sposobem zidentyfikować odpowiedniego adresu IP, który jest Szukaj w tej samej grupie zasobów co elastycznie zasobu usługi migracji bazy danych Azure można znaleźć skojarzonego interfejsu sieciowego. Zazwyczaj nazwa zasobu interfejsu sieciowego rozpoczyna się od prefiksu karty Sieciowej i następuje znak unikatowy i numer sekwencji, przykład jj6tnztnmarpsskr82rbndyp karty Sieciowej. Po wybraniu tego zasobu interfejsu sieciowego, znajduje się adres IP, który musi być uwzględniony na liście dozwolonych Przegląd zasobów strony portalu Azure.

Konieczne może również zawierać źródła portu, który serwer SQL nasłuchuje na liście dozwolonych. Domyślnie jest port 1433, ale można skonfigurować źródła programu SQL Server w celu nasłuchiwania również inne porty. W takim wypadku konieczne jest uwzględnienie tych portów na liście dozwolonych. Można określić port, który serwer SQL nasłuchuje na za pomocą zapytania dynamiczny widok zarządzania:

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Można także określić port, który serwer SQL nasłuchuje badając dziennik błędów programu SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>Q. Czy istnieją zaleceniami dotyczącymi optymalizacji wydajności usługi Azure migracji bazy danych?
Możesz wykonać kilka rzeczy w celu przyspieszenia migracji bazy danych przy użyciu usługi:
- Podczas tworzenia wystąpienia usługi, aby umożliwić usłudze móc korzystać z wielu Vcpu paralelizacja i szybsze transfer danych za pomocą wielu Procesora ogólnego przeznaczenia warstwy cenowej.
- Tymczasowo skalowania w górę docelowego wystąpienia bazy danych SQL Azure do warstwy Premium SKU podczas operacji migracji danych, aby zminimalizować ograniczenie, które bazy danych SQL Azure może mieć wpływ działań transferu danych, korzystając z jednostki SKU niższego poziomu.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>Q. Sposób konfigurowania sieci wirtualnej platformy Azure
Podczas wielu samouczki firmy Microsoft, które prowadzi użytkownika przez proces konfigurowania sieć Wirtualną platformy Azure, oficjalnej dokumentacji pojawia się w artykule [sieci wirtualnej Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>Q. Dlaczego jest Moja usługa migracji bazy danych Azure niedostępna lub zatrzymana?
Jeśli użytkownik jawnie zatrzymuje usługę migracji bazy danych Azure (DMS) lub jeśli usługa jest nieaktywna dla okresu 24 godzin, usługi będą znajdować się w zatrzymana lub auto stan wstrzymania. W każdym przypadku usługa będzie niedostępny i w stan zatrzymania.  Aby wznowić active migracji, należy ponownie uruchomić usługę.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>Q. Gdzie można pozostawić swoją opinię na temat usługi Azure bazy danych migracji
Chcemy poznać Twoją opinię. Wyślij opinię i / pomysły mają o usłudze Azure bazy danych migracji za pomocą User Voice [tutaj](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>Kolejne kroki
Omówienie usługi migracji bazy danych Azure i regionalnych dostępność w publicznej wersji zapoznawczej, zobacz artykuł [co to jest podgląd z migracji bazy danych Azure](dms-overview.md). 
