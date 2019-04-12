---
title: Architektura łączności dla wystąpienia zarządzanego usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o komunikacji wystąpienia usługi Azure SQL Database, zarządzanego i architektura łączności również jak składniki kierować ruch do wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 82b533f7293e00469a5b92b02e8d58967379a585
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59497070"
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

Wystąpienia zarządzane są zależne od usług platformy Azure, takich jak Azure Storage do przechowywania kopii zapasowych, Azure Event Hubs dla telemetrii, usługi Azure Active Directory do uwierzytelniania usługi Azure Key Vault dla przezroczystego szyfrowania danych (TDE) i kilka usług platformy Azure, które zapewniają funkcje zabezpieczeń i możliwości obsługi. Wystąpienia zarządzane umożliwia nawiązanie połączenia z tymi usługami.

Cała komunikacja są szyfrowane i podpisany przy użyciu certyfikatów. Aby sprawdzić wiarygodności komunikujące się strony, zarządzane wystąpienia stale Sprawdź te certyfikaty za pośrednictwem list odwołań certyfikatów. Jeśli certyfikaty zostaną odwołane, wystąpienia zarządzanego zamyka połączenia, aby chronić dane.

## <a name="high-level-connectivity-architecture"></a>Architektura wysokiego poziomu łączności

Na wysokim poziomie wystąpienie zarządzane to zestaw składników usługi. Te składniki są hostowane na dedykowany zestaw izolowanych maszyn wirtualnych, które są uruchamiane w podsieci sieci wirtualnej klienta. Te maszyny tworzą klaster wirtualny.

Klaster wirtualny może obsługiwać wiele wystąpień zarządzanych. W razie potrzeby klastra automatycznie rozszerza się lub umów zmianie liczby wystąpień aprowizowane w podsieci klienta.

Klienta aplikacji można łączyć się z wystąpieniami zarządzanych można zapytania, a aktualizacji baz danych w sieci wirtualnej, równorzędnej sieci wirtualnej, lub sieci połączonych przez sieć VPN lub usługi Azure ExpressRoute. Punkt końcowy i prywatnego adresu IP, należy użyć tej sieci.  

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
> Traffice prowadzącego do usług platformy Azure, które znajdują się w regionie wystąpienia zarządzanego jest zoptymalizowany i z tego powodu nie NATed wystąpień zarządzanych zarządzania punktu końcowego publicznego adresu IP. Dlatego jeśli musisz użyć adresu IP na podstawie reguł zapory, najczęściej w celu skorzystania z magazynu usługi musi być w innym regionie z wystąpienia zarządzanego.

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Wdrażanie wystąpienia zarządzanego w dedykowanej podsieci w sieci wirtualnej. Podsieć musi mieć następującą charakterystykę:

- **Dedykowanej podsieci:** Podsieci wystąpienia zarządzanego nie może zawierać wszystkie inne usługi w chmurze skojarzonej z nim, a nie może być podsieć bramy. Podsieci nie może zawierać żadnych zasobów, ale wystąpienia zarządzanego, a nie można później dodać zasobów w podsieci.
- **Sieciowa grupa zabezpieczeń (NSG):** Sieciowa grupa zabezpieczeń, która jest skojarzona z siecią wirtualną należy zdefiniować [reguły zabezpieczeń dla ruchu przychodzącego](#mandatory-inbound-security-rules) i [reguły zabezpieczeń dla ruchu wychodzącego](#mandatory-outbound-security-rules) przed innymi regułami. Sieciowa grupa zabezpieczeń służy do kontrolowania dostępu do endpoint danych wystąpienia zarządzanego, filtrując ruch na porcie 1433, a porty 11999 11000, jeśli wystąpienie zarządzane jest skonfigurowany do przekierowywać połączeń.
- **Tabela tras definiowanych przez (UDR) użytkownika:** Tabeli trasy zdefiniowanej przez użytkownika, która jest skojarzona z siecią wirtualną, musi zawierać określone [wpisy](#user-defined-routes).
- **Brak punktów końcowych usługi:** Nie punktu końcowego usługi powinna być skojarzona z podsiecią wystąpienia zarządzanego. Upewnij się, wyłączenia opcji punktów końcowych usługi podczas tworzenia sieci wirtualnej.
- **Wystarczającą liczbą adresów IP:** Podsieci wystąpienia zarządzanego musi mieć co najmniej 16 adresów IP. Minimalna zalecana jest 32 adresów IP. Aby uzyskać więcej informacji, zobacz [określi rozmiar podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md). Można wdrożyć wystąpienia zarządzanego w [istniejąca sieć](sql-database-managed-instance-configure-vnet-subnet.md) po skonfigurowaniu go do zaspokojenia [wymagania sieciowe dla wystąpienia zarządzanego](#network-requirements). W przeciwnym razie utwórz [nowej sieci i podsieci](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Nie można wdrożyć nowe wystąpienia zarządzanego, jeśli podsieci docelowej nie ma następujące cechy. Podczas tworzenia wystąpienia zarządzanego intencji zasad sieciowych została zastosowana w tej podsieci, aby uniemożliwić niezgodnych zmiany w konfiguracji sieci. Po usunięciu ostatniego wystąpienia z podsieci, zasady intencji sieci zostaną również usunięte.

### <a name="mandatory-inbound-security-rules"></a>Reguły zabezpieczeń ruchu przychodzącego obowiązkowe

| Name (Nazwa)       |Port                        |Protokół|Obiekt źródłowy           |Element docelowy|Akcja|
|------------|----------------------------|--------|-----------------|-----------|------|
|zarządzanie  |9000, 9003, 1438, 1440, 1452|TCP     |Dowolne              |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO  |Zezwalaj |
|mi_subnet   |Dowolne                         |Dowolne     |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO        |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO  |Zezwalaj |
|health_probe|Dowolne                         |Dowolne     |AzureLoadBalancer|PODSIECI WYSTĄPIENIA ZARZĄDZANEGO  |Zezwalaj |

### <a name="mandatory-outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego obowiązkowe

| Name (Nazwa)       |Port          |Protokół|Obiekt źródłowy           |Element docelowy|Akcja|
|------------|--------------|--------|-----------------|-----------|------|
|zarządzanie  |80, 443, 12000|TCP     |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO        |AzureCloud |Zezwalaj |
|mi_subnet   |Dowolne           |Dowolne     |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO        |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO  |Zezwalaj |

> [!IMPORTANT]
> Upewnij się, istnieje tylko jedna reguła ruchu przychodzącego dla portów 9000, 9003, 1438 1440, 1452 i jednej reguły ruchu wychodzącego dla portów 80, 443, 12000. Zarządzane wystąpienia inicjowania obsługi administracyjnej za pomocą usługi Azure Resource Manager wdrożenie zakończy się niepowodzeniem, jeśli reguły ruchu przychodzącego i dane wyjściowe są skonfigurowane osobno dla poszczególnych portów. Jeśli te porty są oddzielne zasady, wdrożenie zakończy się niepowodzeniem z kodem błędu `VnetSubnetConflictWithIntendedPolicy`

\* PODSIECI wystąpienia Zarządzanego odnosi się do zakresu adresów IP dla podsieci w 10.x.x.x/y formularza. Te informacje można znaleźć w witrynie Azure portal, w oknie właściwości podsieci.

> [!IMPORTANT]
> Chociaż zasady zabezpieczeń dla ruchu przychodzącego zezwalającą na ruch z _wszelkie_ źródła na portach 9000, 9003, 1438, 1440 i 1452, te porty są chronione przez zaporę wbudowanych. Aby uzyskać więcej informacji, zobacz [ustalić adres punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Jeśli korzystanie z replikacji transakcyjnej w zarządzanym wystąpieniu i można użyć dowolnej bazy danych wystąpienia jako wydawca lub dystrybutora, należy otworzyć port 445 (ruch wychodzący protokołu TCP) w regułach zabezpieczeń tej podsieci. Tego portu umożliwi dostęp do udziału plików platformy Azure.

### <a name="user-defined-routes"></a>Trasy zdefiniowane przez użytkownika

|Name (Nazwa)|Prefiks adresu|Następny przeskok|
|----|--------------|-------|
|subnet_to_vnetlocal|PODSIECI WYSTĄPIENIA ZARZĄDZANEGO|Sieć wirtualna|
|mi-13-64-11-nexthop-Internet|13.64.0.0/11|Internet|
|mi-13-96-13-nexthop-Internet|13.96.0.0/13|Internet|
|mi-13-104-14-nexthop-Internet|13.104.0.0/14|Internet|
|mi-20-8-następnego skoku — internet|20.0.0.0/8|Internet|
|mi-23-96-13-nexthop-Internet|23.96.0.0/13|Internet|
|mi-40-64-10-nexthop-Internet|40.64.0.0/10|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-8-następnego skoku — internet|51.0.0.0/8|Internet|
|mi-52-8-następnego skoku — internet|52.0.0.0/8|Internet|
|mi-64-4-18-nexthop-Internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-Internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-Internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-Internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-Internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-Internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-Internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-Internet|103.25.156.0/22|Internet|
|mi-103-36-96-22-nexthop-Internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-Internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-Internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-Internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-Internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-Internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-Internet|131.253.0.0/16|Internet|
|mi-132-245-16-nexthop-Internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-Internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-Internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-Internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-Internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-Internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-Internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-Internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-Internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-Internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-Internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-Internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-Internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-Internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-Internet|192.100.103.0/24|Internet|
|mi-192-197-157-24-nexthop-Internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-Internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-Internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-Internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-Internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-Internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-Internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-Internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-Internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-Internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-Internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-Internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-Internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-Internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-Internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-Internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-Internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-Internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-Internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-Internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-Internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-Internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-Internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-Internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-Internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-Internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-Internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-Internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-Internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-Internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-Internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-Internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-Internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-Internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-Internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-Internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-Internet|216.220.208.0/20|Internet|
||||

Ponadto można dodać wpisów do tabeli tras do kierowania ruchu z zakresów IP prywatnych w środowisku lokalnym jako miejsce docelowe za pośrednictwem bramy sieci wirtualnej lub sieci wirtualne urządzenie sieciowe.

Jeśli sieć wirtualna zawiera niestandardowe DNS, niestandardowego serwera DNS musi być w stanie rozpoznać nazwy hosta w \*. core.windows.net strefy. Za pomocą dodatkowych funkcji, takich jak uwierzytelnianie usługi Azure AD mogą wymagać dodatkowych nazw FQDN rozpoznawania. Aby uzyskać więcej informacji, zobacz [skonfigurować niestandardowe DNS](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [zaawansowanych zabezpieczeń danych bazy danych SQL](sql-database-managed-instance.md).
- Dowiedz się, jak [skonfigurować nową sieć wirtualną platformy Azure](sql-database-managed-instance-create-vnet-subnet.md) lub [istniejącej sieci wirtualnej platformy Azure](sql-database-managed-instance-configure-vnet-subnet.md) którym można wdrożyć wystąpienia zarządzanego.
- [Oblicz rozmiar podsieci](sql-database-managed-instance-determine-size-vnet-subnet.md) potrzebne do wdrożenia wystąpienia zarządzanego.
- Dowiedz się, jak utworzyć wystąpienie zarządzane:
  - Z [witryny Azure portal](sql-database-managed-instance-get-started.md).
  - Za pomocą [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Za pomocą [szablonu usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Za pomocą [szablonu usługi Azure Resource Manager (przy użyciu serwera Przesiadkowego, za pomocą programu SSMS uwzględnione)](https://portal.azure.com/). 
