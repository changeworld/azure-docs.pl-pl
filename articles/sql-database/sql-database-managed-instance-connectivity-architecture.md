---
title: Usługa Azure SQL Database managed architektura łączności wystąpienia | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera usługi Azure SQL Database managed Omówienie komunikacji wystąpienia i wyjaśniono również architektura łączności na temat różnych składników funkcji kierować ruch do wystąpienia zarządzanego.
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
ms.date: 02/18/2019
ms.openlocfilehash: 70206fac7bfe34ea1a138437db35720a1f02337d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585372"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Usługa Azure SQL Database managed wystąpienia architektura łączności

Ten artykuł zawiera usługi Azure SQL Database managed Omówienie komunikacji wystąpienia i wyjaśniono również architektura łączności na temat różnych składników funkcji kierować ruch do wystąpienia zarządzanego.  

Wystąpienie zarządzane usługi Azure SQL Database znajduje się wewnątrz sieci wirtualnej platformy Azure i podsieć, przeznaczonych dla wystąpienia zarządzanego. To wdrożenie umożliwia następujące scenariusze:

- Zabezpiecz prywatny adres IP.
- Łączenie z wystąpienia zarządzanego, bezpośrednio z siecią lokalną.
- Nawiązywanie połączenia z wystąpienia zarządzanego połączonego serwera lub innego lokalnego magazynu danych.
- Wystąpienie zarządzane nawiązywania połączenia z zasobami platformy Azure.

## <a name="communication-overview"></a>Omówienie komunikacji

Na poniższym diagramie przedstawiono jednostek, które łączą się wystąpienia zarządzanego z, a także zasoby, które wystąpienie zarządzane ma dotrzeć do prawidłowego.

![jednostki architektura łączności](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Komunikat, który jest przedstawiony w dolnej części diagramu reprezentuje aplikacji klientów i narzędzia do nawiązywania połączenia z wystąpienia zarządzanego jako źródło danych.  

Podobnie jak wystąpienie zarządzane platformy jako a usługi (PaaS) oferty, firma Microsoft zarządza tę usługę przy użyciu zautomatyzowanych agentów (zarządzania, wdrażania i konserwacji) oparte na strumieniach danych telemetrycznych. Jako zarządzany Zarządzanie wystąpieniami jest wyłączną odpowiedzialność firmy Microsoft, klienci nie są w stanie dostęp do maszyn wirtualnych klastra wystąpienia zarządzanego za pośrednictwem protokołu RDP.

Niektóre programu SQL Server, które mogą wymagać operacji zainicjowanych przez użytkowników końcowych lub aplikacje zarządzane wystąpienia do interakcji z platformą. Jeden przypadek jest tworzenia zarządzanego wystąpienia bazy danych — z zasobem, który jest dostępna za pośrednictwem witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST.

Wystąpienie zarządzane jest zależny od innych usług platformy Azure do ich prawidłowego funkcjonowania (np. usługi Azure Storage do przechowywania kopii zapasowych dla telemetrii usługi Azure Service Bus, usługa Azure AD do uwierzytelniania, usługi Azure Key Vault dla funkcji TDE i tak dalej) i inicjuje połączenia z tymi odpowiednio.

Cała komunikacja z wymienionych powyżej, są szyfrowane i podpisany przy użyciu certyfikatów. Aby upewnić się, że komunikujące się strony są zaufane, wystąpienia zarządzanego stale sprawdza te certyfikaty, kontaktując się z urzędu certyfikacji. Jeśli certyfikaty zostaną odwołane lub wystąpienia zarządzanego nie można zweryfikować je, zamyka połączenia, aby chronić dane.

## <a name="high-level-connectivity-architecture"></a>Architektura wysokiego poziomu łączności

Na wysokim poziomie wystąpienie zarządzane to zestaw składników usługi w serwisie to dedykowany zestaw izolowane maszyny wirtualne uruchamiane w podsieci sieci wirtualnej klienta, które tworzą klaster wirtualny.

Wiele wystąpień zarządzanych mogą być hostowane w jednym klastrem wirtualnym. Klaster jest automatycznie rozszerzony lub nabytej w razie potrzeby zmianie liczby wystąpień aprowizowane w podsieci klienta.

Aplikacje klienta można nawiązać połączenia wystąpienia zarządzanego, zapytań i aktualizacji bazy danych tylko wtedy, gdy są uruchamiane wewnątrz sieci wirtualnej lub równorzędnej sieci wirtualnej lub sieci VPN / Expressroute połączone sieci przy użyciu punktu końcowego za pomocą prywatnego adresu IP.  

![diagram architektury łączności](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Zarządzanie i wdrażanie usług firmy Microsoft jest wykonywany poza siecią wirtualną, dzięki połączeń między wystąpienia zarządzanego i usług firmy Microsoft przechodzi przez punkty końcowe z publicznych adresów IP. Gdy wystąpienie zarządzane tworzy połączenie wychodzące, po odebraniu końcowy prawdopodobnie pochodzi ten publiczny adres IP z powodu translacji adresów sieciowych (NAT).

Ruch zarządzania odbywa się za pośrednictwem sieci wirtualnej klienta. Oznacza to, że wpływają na elementy infrastruktury sieci wirtualnej i może potencjalnie szkód ruch związany z zarządzaniem powodują, że wystąpienia wejść w stan uszkodzony i staną się niedostępne.

> [!IMPORTANT]
> Do poprawy jakości obsługi klienta i dostępności usług, Microsoft stosuje zasady intencji sieci elementy infrastruktury sieci wirtualnej platformy Azure, które mogą wpłynąć na działanie wystąpienia zarządzanego. Jest to mechanizm platformy do komunikowania się wymagania sieciowe w sposób niewidoczny dla użytkownika użytkownikom końcowym z głównym celem, aby zapobiec błędnej konfiguracji sieci i zapewnić normalny wystąpienia zarządzanego operacji. Po usunięciu wystąpienia zarządzanego sieciowe przeznaczenie zasady zostaną usunięte także.

## <a name="virtual-cluster-connectivity-architecture"></a>Architektura łączności klaster wirtualny

Przyjrzyjmy się szczegółowo z wizualizacją w architekturze połączenia wystąpienia zarządzanego. Na poniższym diagramie przedstawiono koncepcyjny układ klaster wirtualny.

![klaster wirtualny diagram architektury łączności](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienci łączą się przy użyciu nazwy hosta, który ma postać wystąpienia zarządzanego z `<mi_name>.<dns_zone>.database.windows.net`. Ta nazwa hosta jest rozpoznawany jako prywatny adres IP jest zarejestrowany w publicznej strefie DNS, i jest rozpoznania publicznie. `zone-id` Jest generowana automatycznie po utworzeniu klastra. Jeśli nowo utworzonego klastra jest hostem dodatkowej wystąpienia zarządzanego, udostępnia Identyfikatora strefy za pomocą klastra podstawowego. Aby uzyskać więcej informacji, zobacz [grup automatyczny tryb failover](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)

Ten prywatny adres IP należy do wystąpienia zarządzanego wewnętrznego modułu równoważenia obciążenia (ILB), która kieruje ruch do bramy wystąpienia zarządzanego (GW). W tym samym klastrze, potencjalnie może uruchomić wiele wystąpień zarządzanych, bramy używany nazwę hosta wystąpienia zarządzanego przekierowywanie ruchu na prawidłowe usługi aparatu programu SQL.

Połączenie usług zarządzania i wdrażania do wystąpienia zarządzanego przy użyciu [punkt końcowy zarządzania](#management-endpoint) mapuje do zewnętrznego modułu równoważenia obciążenia. Ruch jest kierowany do węzłów, tylko wtedy, gdy odbierane na zestaw wstępnie zdefiniowanych portów, które są używane wyłącznie przez składniki zarządzania wystąpienia zarządzanego. Wbudowane zapory w węzłach jest skonfigurowana do zezwolenia na ruch tylko z określonych zakresów adresów IP firmy Microsoft. Cała komunikacja między składniki zarządzania i płaszczyzna zarządzania jest wzajemnie uwierzytelnienia certyfikatu.

## <a name="management-endpoint"></a>Punkt końcowy zarządzania

Klaster wirtualny wystąpienia zarządzanego zawiera punkt końcowy zarządzania, używane przez firmę Microsoft do zarządzania wystąpienia zarządzanego. Punkt końcowy zarządzania jest chroniony za pomocą wbudowanej zapory sieciowej poziomu i wzajemne certyfikat weryfikacji na poziomie aplikacji. Możesz [znaleźć adres ip punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Gdy połączenia były inicjowane z wewnątrz wystąpienia zarządzanego (kopia zapasowa, dziennik inspekcji) wygląda na to, że ruch pochodzący z punktu końcowego zarządzania publicznego adresu IP. Można ograniczyć dostęp usług publicznych z wystąpienia zarządzanego przez ustawienie reguły zapory zezwalające na tylko adres IP wystąpienia zarządzanego. Znajdź więcej informacji o metodzie, która może być [Sprawdź wbudowanej zapory wystąpienia zarządzanego](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> To nie ma zastosowania do ustawiania reguły zapory dla usług platformy Azure, które znajdują się w tym samym regionie, co wystąpienie zarządzane platformy Azure ma optymalizacji dla ruchu, który przechodzi między usługami, które jest kolokowane.

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Możesz wdrożyć wystąpienie zarządzane w dedykowanej podsieci (podsieci wystąpienia zarządzanego) wewnątrz sieci wirtualnej, która spełnia następujące wymagania:

- **W wersji dedykowanej podsieci**: W podsieci wystąpienia zarządzanego nie może zawierać wszystkie inne usługi w chmurze skojarzone z nim, a nie może być podsieć bramy. Nie można utworzyć wystąpienia zarządzanego w podsieci, która zawiera zasobów innych niż wystąpienia zarządzanego, a nie można później dodać innych zasobów w podsieci.
- **Sieciowa grupa zabezpieczeń (NSG)**: Sieciowa grupa zabezpieczeń skojarzone z siecią wirtualną mogą zawierać te zdefiniowane obowiązkowe [reguły zabezpieczeń dla ruchu przychodzącego](#mandatory-inbound-security-rules) i [reguły zabezpieczeń dla ruchu wychodzącego](#mandatory-outbound-security-rules) przed jakiekolwiek inne reguły. Sieciowa grupa zabezpieczeń można użyć w pełni kontrolować dostęp do endpoint danych wystąpienia zarządzanego, filtrując ruch na porcie 1433.
- **Tabela tras zdefiniowanych przez użytkownika (UDR)**: Tabela tras zdefiniowanych przez użytkownika skojarzonego z siecią wirtualną muszą mieć one [wpisy](#user-defined-routes) w tabeli tras zdefiniowanych przez użytkownika.
- **Brak punktów końcowych usługi**: W podsieci wystąpienia zarządzanego nie może mieć powiązany punktu końcowego usługi. Upewnij się, że opcji punktów końcowych usługi jest wyłączona podczas tworzenia sieci wirtualnej.
- **Wystarczającą liczbą adresów IP**: Podsieć wystąpienia zarządzanego musi mieć absolutnego minimum 16 adresów IP (zalecane są co najmniej 32 adresów IP). Aby uzyskać więcej informacji, zobacz [określi rozmiar podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md). Można wdrożyć wystąpienia zarządzanego w [istniejąca sieć](sql-database-managed-instance-configure-vnet-subnet.md) po skonfigurowaniu go do zaspokojenia [wymagania sieciowe wystąpienie zarządzane](#network-requirements), lub Utwórz [nowej sieci i podsieci](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Nie można wdrożyć nowe wystąpienia zarządzanego, jeśli podsieci docelowej nie jest zgodny ze wszystkimi te wymagania. Podczas tworzenia wystąpienia zarządzanego *sieciowe przeznaczenie zasady* została zastosowana w tej podsieci, aby uniemożliwić niezgodnych zmian do konfiguracji sieci. Po usunięciu ostatniego wystąpienia z podsieci, *sieciowe przeznaczenie zasady* zostanie także usunięta

### <a name="mandatory-inbound-security-rules"></a>Reguły zabezpieczeń ruchu przychodzącego obowiązkowe

| Name (Nazwa)       |Port                        |Protokół|Obiekt źródłowy           |Element docelowy|Akcja|
|------------|----------------------------|--------|-----------------|-----------|------|
|zarządzanie  |9000, 9003, 1438, 1440, 1452|TCP     |Dowolne              |Dowolne        |Zezwalaj |
|mi_subnet   |Dowolne                         |Dowolne     |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO        |Dowolne        |Zezwalaj |
|health_probe|Dowolne                         |Dowolne     |AzureLoadBalancer|Dowolne        |Zezwalaj |

### <a name="mandatory-outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego obowiązkowe

| Name (Nazwa)       |Port          |Protokół|Obiekt źródłowy           |Element docelowy|Akcja|
|------------|--------------|--------|-----------------|-----------|------|
|zarządzanie  |80, 443, 12000|TCP     |Dowolne              |Internet   |Zezwalaj |
|mi_subnet   |Dowolne           |Dowolne     |Dowolne              |MI PODSIECI *  |Zezwalaj |

\* PODSIECI wystąpienia Zarządzanego odnosi się do zakresu adresów IP dla podsieci w 10.x.x.x/y formularza. Te informacje można znaleźć w witrynie Azure portal (za pośrednictwem właściwości podsieci).

> [!IMPORTANT]
> Chociaż zasady obowiązkowych zabezpieczeń dla ruchu przychodzącego zezwalającą na ruch z _wszelkie_ źródła na portach 9000, 9003, 1438, 1440, 1452 te porty są chronione przez zaporę wbudowanych. To [artykułu](sql-database-managed-instance-find-management-endpoint-ip-address.md) pokazuje, jak odnaleźć adres IP punktu końcowego zarządzania i sprawdź reguły zapory.
> [!NOTE]
> Jeśli używasz replikacji transakcyjnej wystąpienia zarządzanego i wszystkie wystąpienia bazy danych jest używany jako wydawca lub dystrybutora, port 445 (ruch wychodzący protokołu TCP) również musi być otwarte w regułach zabezpieczeń dla podsieci w celu uzyskania dostępu do udziału plików platformy Azure.

### <a name="user-defined-routes"></a>Trasy definiowane przez użytkownika

|Name (Nazwa)|Prefiks adresu|NET przeskoku|
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

Ponadto możesz dodać wpisy na potrzeby kierowania ruchu trasy tabeli zawierającej zakresów IP prywatnych w środowisku lokalnym jako miejsca docelowego za pośrednictwem bramy sieci wirtualnej lub sieci wirtualne urządzenie sieciowe.

- **Opcjonalne niestandardowe DNS**: Jeśli niestandardowe DNS jest określona w sieci wirtualnej, należy dodać adres IP platformy Azure cyklicznego programu rozpoznawania nazw (na przykład 168.63.129.16) do listy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md). Niestandardowego serwera DNS musi być w stanie rozpoznać nazwy hostów w następujących domen i poddomen ich: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, i *microsoftonline-p.com*.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Dowiedz się, jak [skonfigurować nową sieć wirtualną](sql-database-managed-instance-create-vnet-subnet.md) lub [Konfigurowanie istniejącej sieci wirtualnej](sql-database-managed-instance-configure-vnet-subnet.md) którym można wdrożyć wystąpienia zarządzanego.
- [Oblicz się rozmiar podsieci](sql-database-managed-instance-determine-size-vnet-subnet.md) którym będą wdrażane wystąpienia zarządzanego.
- Dla przewodników Szybki Start zobacz temat Tworzenie wystąpienia zarządzanego:
  - Z [witryny Azure portal](sql-database-managed-instance-get-started.md)
  - za pomocą [programu PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - za pomocą [szablonu usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - za pomocą [szablonu usługi Azure Resource Manager (Rampa za pomocą programu SSMS uwzględnione)](https://portal.azure.com/)
