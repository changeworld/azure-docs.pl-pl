---
title: Architektura łączności dla wystąpienia zarządzanego usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o komunikacji wystąpienia usługi Azure SQL Database, zarządzanego i architektura łączności również jak składniki kierować ruch do wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: c7587b6cb2b4b30e265657b9d3792c9d4acd4428
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621553"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architektura łączności dla wystąpienia zarządzanego usługi Azure SQL Database 

W tym artykule opisano komunikację w wystąpieniu zarządzanym usługi Azure SQL Database. Omówiono także architekturę łączności i jak składniki kierować ruch do wystąpienia zarządzanego.  

Wystąpienie zarządzane bazy danych SQL jest umieszczony wewnątrz sieci wirtualnej platformy Azure i podsieć, która jest przeznaczona do wystąpienia zarządzanego. To wdrożenie zapewnia:

- Bezpieczne prywatny adres IP.
- Możliwość łączenia z siecią lokalną do wystąpienia zarządzanego.
- Możliwość łączenia wystąpienia zarządzanego z połączonego serwera lub innego lokalnego magazynu danych.
- Możliwość łączenia z wystąpienia zarządzanego z zasobami platformy Azure.

## <a name="communication-overview"></a>Omówienie komunikacji

Na poniższym diagramie przedstawiono jednostek, które nawiąż połączenie z wystąpieniem zarządzanym. Pokazuje również zasoby, które muszą komunikować się z wystąpieniem zarządzanym. Proces komunikacji w dolnej części diagramu reprezentuje klienta aplikacji i narzędzi, łączących się z wystąpienia zarządzanego jako źródła danych.  

![Jednostki w architektura łączności](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Wystąpienie zarządzane to platforma jako oferty usługi (PaaS). Firma Microsoft używa automatycznych agents (zarządzanie, wdrażania i konserwacji) do zarządzania tej usługi, w oparciu strumieni danych telemetrii. Ponieważ firma Microsoft jest odpowiedzialna za zarządzanie, klienci nie mogą uzyskiwać dostęp do maszyn wirtualnych klastra wystąpienia zarządzanego za pośrednictwem protokołu RDP (Remote Desktop).

Niektóre programu SQL Server, których może wymagać operacji uruchamiane przez użytkowników końcowych lub aplikacje zarządzane wystąpienia do interakcji z platformą. Jeden przypadek jest tworzenia zarządzanego wystąpienia bazy danych. Ten zasób jest dostępna za pośrednictwem witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST.

Wystąpienia zarządzane są zależne od usług platformy Azure, takich jak Azure Storage do przechowywania kopii zapasowych, usługi Azure Service Bus dla telemetrii usługi Azure Active Directory do uwierzytelniania i usługi Azure Key Vault dla przezroczystego szyfrowania danych (TDE). Wystąpienia zarządzane nawiązywać połączenia z tymi usługami.

Cała komunikacja używają certyfikatów do szyfrowania i podpisywania. Aby sprawdzić wiarygodności komunikujące się strony, zarządzane wystąpień stale Sprawdź te certyfikaty, kontaktując się z urzędu certyfikacji. Jeśli certyfikaty zostaną odwołane, nie można zweryfikować wystąpienia zarządzanego zamyka połączenia, aby chronić dane.

## <a name="high-level-connectivity-architecture"></a>Architektura wysokiego poziomu łączności

Na wysokim poziomie wystąpienie zarządzane to zestaw składników usługi. Te składniki są hostowane na dedykowany zestaw izolowanych maszyn wirtualnych, które są uruchamiane w podsieci sieci wirtualnej klienta. Te maszyny tworzą klaster wirtualny.

Klaster wirtualny może obsługiwać wiele wystąpień zarządzanych. W razie potrzeby klastra automatycznie rozszerza się lub umów zmianie liczby wystąpień aprowizowane w podsieci klienta.

Aplikacje klienta umożliwia połączenie z wystąpienia zarządzanego i tworzyć zapytania i aktualizacji bazy danych tylko wtedy, gdy są uruchamiane wewnątrz sieci wirtualnej, równorzędnej wirtualnego sieci lub sieci połączonych przez sieć VPN lub usługi Azure ExpressRoute. Punkt końcowy i prywatnego adresu IP, należy użyć tej sieci.  

![diagram architektury łączności](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Uruchom zarządzanie i wdrażanie usług firmy Microsoft poza siecią wirtualną. Wystąpienie zarządzane i usług firmy Microsoft connect za pośrednictwem punktów końcowych, które mają publiczne adresy IP. Gdy wystąpienie zarządzane tworzy połączenie wychodzące, po stronie odbierającej sprawia, że translacji adresów sieciowych (NAT) podoba Ci się wygląd połączenie pochodzi ten publiczny adres IP.

Ruch zarządzania odbywa się za pośrednictwem sieci wirtualnej klienta. Oznacza to elementy infrastruktury sieci wirtualnej mogą uszkodzić ruch związany z zarządzaniem, wprowadzając wystąpienia zakończyć się niepowodzeniem i staną się niedostępne.

> [!IMPORTANT]
> Do poprawy jakości obsługi klienta oraz dostępności usług Microsoft stosuje zasady intencji sieci na elementy infrastruktury sieci wirtualnej platformy Azure. Zasady mogą wpłynąć na działanie wystąpienia zarządzanego. Ten mechanizm platformy w sposób niewidoczny dla użytkownika komunikuje się wymagania sieciowe dla użytkowników. Głównym celem tej zasady jest zapobiec błędnej konfiguracji sieci i zapewnić operacji normalnego wystąpienia zarządzanego. Po usunięciu wystąpienia zarządzanego intencji zasad sieciowych zostaną również usunięte.

## <a name="virtual-cluster-connectivity-architecture"></a>Architektura łączności klaster wirtualny

Przyjrzyjmy się szczegółowo z wizualizacją do architektura łączności dla wystąpienia zarządzanego. Na poniższym diagramie przedstawiono koncepcyjny układ klaster wirtualny.

![Architektura łączności klastra wirtualnego](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienci łączą się wystąpienia zarządzanego z przy użyciu nazwy hosta, który ma postać `<mi_name>.<dns_zone>.database.windows.net`. Ta nazwa hosta jest rozpoznawany jako prywatny adres IP jest zarejestrowany w strefie systemu nazw domen (DNS) domeny publicznej, i jest rozpoznania publicznie. `zone-id` Jest generowana automatycznie podczas tworzenia klastra. Jeśli nowo utworzony klaster hostów dodatkowej wystąpienia zarządzanego, udostępnia Identyfikatora strefy za pomocą klastra podstawowego. Aby uzyskać więcej informacji, zobacz [umożliwiają automatyczne grupy trybu failover można weryfikować i skoordynowany pracy w trybie failover wielu baz danych](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Ten prywatny adres IP należy do wystąpienia zarządzanego wewnętrznego modułu równoważenia obciążenia. Moduł równoważenia obciążenia kieruje ruch do wystąpienia zarządzanego bramy. Ponieważ w tym samym klastrze można uruchomić wiele wystąpień zarządzanych, brama używa nazwy hosta wystąpienia zarządzanego przekierowywanie ruchu na prawidłowe usługi aparatu programu SQL.

Usługi zarządzania i wdrażania nawiązać wystąpienia zarządzanego przy użyciu [punkt końcowy zarządzania](#management-endpoint) mapuje do zewnętrznego modułu równoważenia obciążenia. Ruch jest kierowany do węzłów, tylko wtedy, gdy zostanie odebrane zestaw wstępnie zdefiniowanych portów, które używają tylko składniki zarządzania wystąpienia zarządzanego. Wbudowane zapory w węzłach skonfigurowano zezwalająca na ruch tylko z zakresów adresów IP firmy Microsoft. Certyfikaty wzajemnego uwierzytelniania, cała komunikacja między składniki zarządzania i płaszczyzny zarządzania.

## <a name="management-endpoint"></a>Punkt końcowy zarządzania

Firma Microsoft zarządza wystąpienia zarządzanego przy użyciu punktu końcowego zarządzania. Ten punkt końcowy znajduje się wewnątrz klastra wirtualnego wystąpienia programu. Punkt końcowy zarządzania jest chroniony przez wbudowane zapory na poziomie sieci. Na poziomie aplikacji jest chroniony przez wzajemne certyfikatu weryfikacji. Aby znaleźć adres IP punktu końcowego, zobacz [określić adres IP punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Podczas połączenia start wewnątrz wystąpienia zarządzanego (podobnie jak w przypadku tworzenia kopii zapasowych i dzienników inspekcji), ruch jest wyświetlana do uruchamiania z punktu końcowego zarządzania publicznego adresu IP. Można ograniczyć dostęp do usług publicznych z wystąpienia zarządzanego przez ustawienie reguły zapory zezwalające na tylko adres IP wystąpienia zarządzanego. Aby uzyskać więcej informacji, zobacz [Sprawdź wystąpienia zarządzanego wbudowanej zapory](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> W przeciwieństwie do zaporę dla połączeń, uruchamianych wewnątrz wystąpienie zarządzane usługi platformy Azure, które znajdują się w regionie wystąpienia zarządzanego mieć zaporą, która jest zoptymalizowana pod kątem ruchu, który przechodzi między tymi usługami.

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Wdrażanie wystąpienia zarządzanego w dedykowanej podsieci w sieci wirtualnej. Podsieć musi mieć następującą charakterystykę:

- **Dedykowanej podsieci:** Podsieci wystąpienia zarządzanego nie może zawierać wszystkie inne usługi w chmurze skojarzonej z nim, a nie może być podsieć bramy. Podsieci nie może zawierać żadnych zasobów, ale wystąpienia zarządzanego, a nie można później dodać zasobów w podsieci.
- **Sieciowa grupa zabezpieczeń (NSG):** Sieciowa grupa zabezpieczeń, która jest skojarzona z siecią wirtualną należy zdefiniować [reguły zabezpieczeń dla ruchu przychodzącego](#mandatory-inbound-security-rules) i [reguły zabezpieczeń dla ruchu wychodzącego](#mandatory-outbound-security-rules) przed innymi regułami. Sieciowa grupa zabezpieczeń służy do kontrolowania dostępu do endpoint danych wystąpienia zarządzanego, filtrując ruch na porcie 1433.
- **Tabela tras definiowanych przez (UDR) użytkownika:** Tabeli trasy zdefiniowanej przez użytkownika, która jest skojarzona z siecią wirtualną, musi zawierać określone [wpisy](#user-defined-routes).
- **Brak punktów końcowych usługi:** Nie punktu końcowego usługi powinna być skojarzona z podsiecią wystąpienia zarządzanego. Upewnij się, wyłączenia opcji punktów końcowych usługi podczas tworzenia sieci wirtualnej.
- **Wystarczającą liczbą adresów IP:** Podsieci wystąpienia zarządzanego musi mieć co najmniej 16 adresów IP. Minimalna zalecana jest 32 adresów IP. Aby uzyskać więcej informacji, zobacz [określi rozmiar podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md). Można wdrożyć wystąpienia zarządzanego w [istniejąca sieć](sql-database-managed-instance-configure-vnet-subnet.md) po skonfigurowaniu go do zaspokojenia [wymagania sieciowe dla wystąpienia zarządzanego](#network-requirements). W przeciwnym razie utwórz [nowej sieci i podsieci](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Nie można wdrożyć nowe wystąpienia zarządzanego, jeśli podsieci docelowej nie ma następujące cechy. Podczas tworzenia wystąpienia zarządzanego intencji zasad sieciowych została zastosowana w tej podsieci, aby uniemożliwić niezgodnych zmiany w konfiguracji sieci. Po usunięciu ostatniego wystąpienia z podsieci, zasady intencji sieci zostaną również usunięte.

### <a name="mandatory-inbound-security-rules"></a>Reguły zabezpieczeń ruchu przychodzącego obowiązkowe

| Name (Nazwa)       |Port                        |Protokół|Obiekt źródłowy           |Element docelowy|Akcja|
|------------|----------------------------|--------|-----------------|-----------|------|
|zarządzanie  |9000, 9003, 1438, 1440, 1452|TCP     |Dowolne              |Dowolne        |Zezwalaj |
|mi_subnet   |Dowolne                         |Dowolne     |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO        |Dowolne        |Zezwalaj |
|health_probe|Dowolne                         |Dowolne     |AzureLoadBalancer|Dowolne        |Zezwalaj |

### <a name="mandatory-outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego obowiązkowe

| Name (Nazwa)       |Port          |Protokół|Obiekt źródłowy           |Element docelowy|Akcja|
|------------|--------------|--------|-----------------|-----------|------|
|zarządzanie  |80, 443, 12000|TCP     |Dowolne              |AzureCloud  |Zezwalaj |
|mi_subnet   |Dowolne           |Dowolne     |Dowolne              |MI PODSIECI *  |Zezwalaj |

> Upewnij się, że istnieje tylko jedna reguła ruchu przychodzącego dla portów 9000, 9003, 1438 1440, 1452 i jednej reguły ruchu wychodzącego dla portów 80, 443, 12000. Zarządzane wystąpienia obsługę administracyjną przy użyciu wdrożenia ARM może zakończyć się niepowodzeniem, jeśli reguły ruchu przychodzącego i dane wyjściowe są skonfigurowane osobno dla poszczególnych portów. 

\* PODSIECI wystąpienia Zarządzanego odnosi się do zakresu adresów IP dla podsieci w 10.x.x.x/y formularza. Te informacje można znaleźć w witrynie Azure portal, w oknie właściwości podsieci.

> [!IMPORTANT]
> Chociaż zasady zabezpieczeń dla ruchu przychodzącego zezwalającą na ruch z _wszelkie_ źródła na portach 9000, 9003, 1438, 1440 i 1452, te porty są chronione przez zaporę wbudowanych. Aby uzyskać więcej informacji, zobacz [ustalić adres punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Jeśli korzystanie z replikacji transakcyjnej w zarządzanym wystąpieniu i można użyć dowolnej bazy danych wystąpienia jako wydawca lub dystrybutora, należy otworzyć port 445 (ruch wychodzący protokołu TCP) w regułach zabezpieczeń tej podsieci. Tego portu umożliwi dostęp do udziału plików platformy Azure.

### <a name="user-defined-routes"></a>Trasy zdefiniowane przez użytkownika

|Name (Nazwa)|Prefiks adresu|Następny przeskok|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|Sieć wirtualna|
|mi-0-5-next-hop-internet|0.0.0.0/5|Internet|
|mi-11-8-następnego skoku — internet|11.0.0.0/8|Internet|
|mi-12-6-następnego skoku — internet|12.0.0.0/6|Internet|
|mi-128-3-następnego skoku — internet|128.0.0.0/3|Internet|
|mi-16-4-następnego skoku — internet|16.0.0.0/4|Internet|
|mi 160-5-następnego skoku — internet|160.0.0.0/5|Internet|
|mi 168-6-następnego skoku — internet|168.0.0.0/6|Internet|
|mi 172-12-następnego skoku — internet|172.0.0.0/12|Internet|
|mi-172-128-9-nexthop-Internet|172.128.0.0/9|Internet|
|mi-172-32-11-nexthop-Internet|172.32.0.0/11|Internet|
|mi-172-64-10-nexthop-Internet|172.64.0.0/10|Internet|
|mi 173-8-następnego skoku — internet|173.0.0.0/8|Internet|
|mi-174-7-nexthop-internet|174.0.0.0/7|Internet|
|mi 176-4-następnego skoku — internet|176.0.0.0/4|Internet|
|mi-192-128-11-nexthop-Internet|192.128.0.0/11|Internet|
|mi-192-160-13-nexthop-Internet|192.160.0.0/13|Internet|
|mi-192-169-16-nexthop-Internet|192.169.0.0/16|Internet|
|mi-192-170-15-nexthop-Internet|192.170.0.0/15|Internet|
|mi-192-172-14-nexthop-Internet|192.172.0.0/14|Internet|
|mi-192-176-12-nexthop-Internet|192.176.0.0/12|Internet|
|mi-192-192-10-nexthop-Internet|192.192.0.0/10|Internet|
|mi 192-9-następnego skoku — internet|192.0.0.0/9|Internet|
|mi 193-8-następnego skoku — internet|193.0.0.0/8|Internet|
|mi 194-7-następnego skoku — internet|194.0.0.0/7|Internet|
|mi-196-6-nexthop-internet|196.0.0.0/6|Internet|
|mi 200-5-następnego skoku — internet|200.0.0.0/5|Internet|
|mi 208-4-następnego skoku — internet|208.0.0.0/4|Internet|
|mi 224-3-następnego skoku — internet|224.0.0.0/3|Internet|
|mi-32-3-następnego skoku — internet|32.0.0.0/3|Internet|
|mi-64-2-następnego skoku — internet|64.0.0.0/2|Internet|
|mi-8-7-nexthop-internet|8.0.0.0/7|Internet|
||||

Ponadto można dodać wpisów do tabeli tras do kierowania ruchu z zakresów IP prywatnych w środowisku lokalnym jako miejsce docelowe za pośrednictwem bramy sieci wirtualnej lub sieci wirtualne urządzenie sieciowe.

Jeśli sieć wirtualna zawiera niestandardowe DNS, należy dodać wpis dla adresu IP rozpoznawania cyklicznego platformy Azure (np. 168.63.129.16). Aby uzyskać więcej informacji, zobacz [skonfigurować niestandardowe DNS](sql-database-managed-instance-custom-dns.md). Niestandardowego serwera DNS musi być w stanie rozpoznać nazwy hostów w tych domenach i poddomenach ich: *microsoft.com*, *windows.net*, *windows.com*,  *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, i *microsoftonline-p.com*.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [zaawansowanych zabezpieczeń danych bazy danych SQL](sql-database-managed-instance.md).
- Dowiedz się, jak [skonfigurować nową sieć wirtualną platformy Azure](sql-database-managed-instance-create-vnet-subnet.md) lub [istniejącej sieci wirtualnej platformy Azure](sql-database-managed-instance-configure-vnet-subnet.md) którym można wdrożyć wystąpienia zarządzanego.
- [Oblicz rozmiar podsieci](sql-database-managed-instance-determine-size-vnet-subnet.md) potrzebne do wdrożenia wystąpienia zarządzanego.
- Dowiedz się, jak utworzyć wystąpienie zarządzane:
  - Z [witryny Azure portal](sql-database-managed-instance-get-started.md).
  - Za pomocą [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Za pomocą [szablonu usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Za pomocą [szablonu usługi Azure Resource Manager (przy użyciu serwera Przesiadkowego, za pomocą programu SSMS uwzględnione)](https://portal.azure.com/).
