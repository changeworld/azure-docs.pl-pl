---
title: Łącze prywatne — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak działa łącze prywatne dla usługi Azure Database for PostgreSQL — pojedynczy serwer.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4216abdf8cc8aae00e3ba0c57961c4b8b7403672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371685"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Prywatne łącze dla usługi Azure Database dla serwera PostgreSQL-Single

Private Link umożliwia tworzenie prywatnych punktów końcowych dla usługi Azure Database dla PostgreSQL — pojedynczy serwer i tak przynosi usługi platformy Azure wewnątrz prywatnej sieci wirtualnej (VNet). Prywatny punkt końcowy udostępnia prywatny adres IP, którego można użyć do nawiązania połączenia z serwerem bazy danych, podobnie jak każdy inny zasób w sieci wirtualnej.

Aby uzyskać listę usług PaaS obsługujących funkcję Łącza prywatnego, zapoznaj się z [dokumentacją](https://docs.microsoft.com/azure/private-link/index)łącza prywatnego . Prywatny punkt końcowy to prywatny adres IP w określonej sieci [wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) i podsieci.

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach platformy Azure, gdzie usługa Azure Database for PostgreSQL Single server obsługuje warstwy cenowe ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.

## <a name="data-exfiltration-prevention"></a>Zapobieganie eksfiltracji danych

Ex-filtracji danych w usłudze Azure Database dla postgreSQL pojedynczy serwer jest, gdy autoryzowany użytkownik, takich jak administrator bazy danych, jest w stanie wyodrębnić dane z jednego systemu i przenieść go do innej lokalizacji lub systemu poza organizację. Na przykład użytkownik przenosi dane do konta magazynu należącego do innej firmy.

Należy wziąć pod uwagę scenariusz z użytkownikiem z systemem PGAdmin wewnątrz maszyny wirtualnej platformy Azure (VM), który łączy się z usługą Azure Database dla postgreSql pojedynczy serwer aprowizowana w zachodnie stany USA. W poniższym przykładzie pokazano, jak ograniczyć dostęp z publicznych punktów końcowych w usłudze Azure Database dla postgreSQL pojedynczy serwer przy użyciu kontroli dostępu do sieci.

* Wyłącz cały ruch usługi platformy Azure do usługi Azure Database dla pojedynczego serwera PostgreSQL za pośrednictwem publicznego punktu końcowego, ustawiając *opcję Zezwalaj na usługi platformy Azure* na WYŁ. Upewnij się, że żadne adresy IP lub zakresy nie mają dostępu do serwera za pośrednictwem [reguł zapory](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) lub [punktów końcowych usługi sieci wirtualnej](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet).

* Zezwalaj tylko na ruch do usługi Azure Database dla pojedynczego serwera PostgreSQL przy użyciu prywatnego adresu IP maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz artykuły dotyczące [reguł punktu końcowego usługi](concepts-data-access-and-security-vnet.md) i sieci [wirtualnej.](howto-manage-vnet-using-portal.md)

* Na maszynie Wirtualnej platformy Azure zawęź zakres połączenia wychodzącego przy użyciu sieciowych grup zabezpieczeń (nsg) i tagów usług w następujący sposób

    * Określ regułę nsg, aby zezwolić na ruch dla *tagu usługi = SQL. WestUS* — zezwalanie tylko na połączenie z usługą Azure Database dla pojedynczego serwera PostgreSQL w zachodnie stany USA
    * Określ regułę sieciowej grupy danych (o wyższym priorytecie), aby odmówić ruchu dla *tagu usługi = SQL* - odmówiając połączeń do bazy danych PostgreSQL we wszystkich regionach</br></br>

Po zakończeniu tej konfiguracji maszyna wirtualna platformy Azure może łączyć się tylko z usługą Azure Database dla pojedynczego serwera PostgreSQL w regionie Zachodnie stany USA. Jednak łączność nie jest ograniczona do jednej bazy danych platformy Azure dla postgreSQL pojedynczy serwer. Maszyna wirtualna nadal można połączyć się z dowolnego serwera usługi Azure Database dla postgreSQL single w regionie Zachodnie stany USA, w tym baz danych, które nie są częścią subskrypcji. Firma Microsoft zmniejszyła zakres eksfiltracji danych w powyższym scenariuszu do określonego regionu, nie wyeliminowaliśmy go całkowicie.</br>

Za pomocą łącza prywatnego można teraz skonfigurować kontrolki dostępu do sieci, takie jak sieciowe sieciowe, aby ograniczyć dostęp do prywatnego punktu końcowego. Poszczególne zasoby usługi Azure PaaS są następnie mapowane do określonych prywatnych punktów końcowych. Złośliwy niejawny tester może uzyskać dostęp tylko do zamapowanych zasobów PaaS (na przykład usługi Azure Database dla pojedynczego serwera PostgreSQL) i nie ma innego zasobu.

## <a name="on-premises-connectivity-over-private-peering"></a>Łączność lokalna za prywatną komunikację równorzędną

Po nawiązaniu połączenia z publicznym punktem końcowym z komputerów lokalnych adres IP musi zostać dodany do zapory opartej na protokãoła IP przy użyciu reguły zapory na poziomie serwera. Ten model działa dobrze, umożliwiając dostęp do poszczególnych maszyn dla obciążeń deweloperskich lub testowych, ale jest trudny do zarządzania w środowisku produkcyjnym.

Za pomocą łącza prywatnego można włączyć dostęp międzylokacyjny do prywatnego punktu końcowego przy użyciu [trasy ekspresowej](https://azure.microsoft.com/services/expressroute/) (ER), prywatnej komunikacji równorzędnej lub [tunelu sieci VPN.](https://docs.microsoft.com/azure/vpn-gateway/) Następnie mogą wyłączyć cały dostęp za pośrednictwem publicznego punktu końcowego i nie używać zapory opartej na protokãoła IP.

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Konfigurowanie łącza prywatnego dla bazy danych platformy Azure dla pojedynczego serwera PostgreSQL

### <a name="creation-process"></a>Proces tworzenia

Prywatne punkty końcowe są wymagane do włączenia łącza prywatnego. Można to zrobić za pomocą następujących przewodników in how-to.

* [Portal Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal)
* [Cli](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Proces zatwierdzania
Gdy administrator sieci tworzy prywatny punkt końcowy (PE), administrator PostgreSQL może zarządzać połączeniem prywatnego punktu końcowego (PEC) do bazy danych Azure Database for PostgreSQL. To rozdzielenie obowiązków między administratorem sieci i DBA jest przydatne do zarządzania usługą Azure Database dla łączności PostgreSQL. 

* Przejdź do usługi Azure Database for PostgreSQL zasobów serwera w witrynie Azure portal. 
    * Wybieranie połączeń prywatnego punktu końcowego w lewym okienku
    * Pokazuje listę wszystkich połączeń prywatnych punktów końcowych (PEC)
    * Utworzony odpowiedni prywatny punkt końcowy

![wybieranie portalu prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Wybierz indywidualny PEC z listy, wybierając go.

![wybieranie prywatnego punktu końcowego oczekującego na zatwierdzenie](media/concepts-data-access-and-security-private-link/select-private-link.png)

* Administrator serwera PostgreSQL może zatwierdzić lub odrzucić PEC i opcjonalnie dodać krótką odpowiedź tekstową.

![wybieranie wiadomości prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Po zatwierdzeniu lub odrzuceniu lista będzie odzwierciedlać odpowiedni stan wraz z tekstem odpowiedzi

![wybieranie stanu końcowego prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Przypadki użycia łącza prywatnego dla usługi Azure Database dla postgreSQL

Klienci mogą łączyć się z prywatnym punktem końcowym z tej samej sieci wirtualnej, sieci wirtualnej w tym samym regionie lub za pośrednictwem połączenia sieci wirtualnej do sieci wirtualnej w różnych regionach. Ponadto klienci mogą łączyć się z lokalnymi przy użyciu usługi ExpressRoute, prywatnej komunikacji równorzędnej lub tunelowania sieci VPN. Poniżej znajduje się uproszczony diagram przedstawiający typowe przypadki użycia.

![wybieranie przeglądu prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Łączenie się z maszyny Wirtualnej platformy Azure w sieci wirtualnej (sieci wirtualnej)
Konfigurowanie [komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) w celu ustanowienia łączności z usługą Azure Database for PostgreSQL — pojedynczy serwer z maszyny Wirtualnej platformy Azure w sieci wirtualnej równorzędnej.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Łączenie się z maszyny wirtualnej platformy Azure w środowisku sieci wirtualnej do sieci wirtualnej
Skonfiguruj [połączenie bramy sieci VPN sieci wirtualnej do sieci wirtualnej,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) aby ustanowić łączność z usługą Azure Database for PostgreSQL — pojedynczy serwer z maszyny Wirtualnej platformy Azure w innym regionie lub subskrypcji.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Łączenie się ze środowiska lokalnego za pośrednictwem sieci VPN
Aby ustanowić łączność ze środowiska lokalnego do usługi Azure Database for PostgreSQL — pojedynczy serwer, wybierz i zaimplementuj jedną z opcji:

* [Połączenie z punktem lokacji](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Połączenie sieci VPN typu lokacja-lokacja](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Obwód usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Łącze prywatne w połączeniu z regułami zapory

Następujące sytuacje i wyniki są możliwe podczas korzystania z łącza prywatnego w połączeniu z regułami zapory:

* Jeśli nie skonfigurujesz żadnych reguł zapory, domyślnie żaden ruch nie będzie mógł uzyskać dostępu do usługi Azure Database dla pojedynczego serwera PostgreSQL.

* Jeśli skonfigurujesz ruch publiczny lub punkt końcowy usługi i utworzysz prywatne punkty końcowe, różne typy ruchu przychodzącego są autoryzowane przez odpowiedni typ reguły zapory.

* Jeśli nie skonfigurujesz żadnego ruchu publicznego lub punktu końcowego usługi i utworzysz prywatne punkty końcowe, serwer Azure Database for PostgreSQL Single jest dostępny tylko za pośrednictwem prywatnych punktów końcowych. Jeśli nie skonfigurujesz ruchu publicznego lub punktu końcowego usługi, po odrzuceniu lub usunięciu wszystkich zatwierdzonych prywatnych punktów końcowych, żaden ruch nie będzie mógł uzyskać dostępu do usługi Azure Database dla pojedynczego serwera PostgreSQL.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Odmów dostępu publicznego dla usługi Azure Database dla pojedynczego serwera PostgreSQL

Jeśli chcesz polegać tylko na prywatnych punktach końcowych dostępu do ich usługi Azure Database dla pojedynczego serwera PostgreSQL, możesz wyłączyć ustawianie wszystkich publicznych punktów[końcowych(reguły zapory](concepts-firewall-rules.md) i [punkty końcowe usługi sieci wirtualnej),](concepts-data-access-and-security-vnet.md)ustawiając konfigurację **Odmowa dostępu do sieci publicznej** na serwerze bazy danych. 

Gdy to ustawienie jest ustawione na *TAK* tylko połączenia za pośrednictwem prywatnych punktów końcowych są dozwolone do bazy danych Azure dla PostgreSQL. Gdy to ustawienie jest ustawione na *NO* klienci mogą połączyć się z usługą Azure Database dla PostgreSQL na podstawie ustawienia punktu końcowego usługi zapory lub sieci wirtualnej. Ponadto po ustawieniu wartości dostępu do sieci prywatnej klienci nie mogą dodawać i/lub aktualizować istniejących "reguł zapory" i "Reguły punktu końcowego usługi sieci wirtualnej"

> [!Note]
> Ta funkcja jest dostępna we wszystkich regionach platformy Azure, gdzie usługa Azure Database for PostgreSQL — pojedynczy serwer obsługuje warstwy cenowe ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.
>
> To ustawienie nie ma żadnego wpływu na konfiguracje SSL i TLS dla usługi Azure Database dla serwera PostgreSQL single.

Aby dowiedzieć się, jak ustawić **odmowę dostępu** do sieci publicznej dla usługi Azure Database dla pojedynczego serwera PostgreSQL z witryny Azure portal, zobacz [Jak skonfigurować odmowę dostępu do sieci publicznej](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń usługi Azure Database for PostgreSQL Single server, zobacz następujące artykuły:

* Aby skonfigurować zaporę dla usługi Azure Database dla pojedynczego serwera PostgreSQL, zobacz [Obsługa zapory](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules).

* Aby dowiedzieć się, jak skonfigurować punkt końcowy usługi sieci wirtualnej dla usługi Azure Database dla pojedynczego serwera PostgreSQL, zobacz [Konfigurowanie dostępu z sieci wirtualnych](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet).

* Aby zapoznać się z omówieniem usługi Azure Database dla łączności z pojedynczym serwerem PostgreSQL, zobacz [Usługa Azure Database for PostgreSQL Connectivity Architecture](https://docs.microsoft.com/azure/postgresql/concepts-connectivity-architecture)