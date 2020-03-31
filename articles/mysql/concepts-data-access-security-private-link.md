---
title: Link prywatny — usługa Azure Database for MySQL
description: Dowiedz się, jak działa łącze prywatne dla usługi Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c2cc4986542404281424286882c046dec39f5daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371294"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Prywatne łącze dla bazy danych platformy Azure dla mysql

Private Link umożliwia łączenie się z różnymi usługami PaaS na platformie Azure za pośrednictwem prywatnego punktu końcowego. Usługa Azure Private Link zasadniczo przynosi usługi platformy Azure wewnątrz prywatnej sieci wirtualnej (VNet). Zasoby PaaS można uzyskać przy użyciu prywatnego adresu IP, podobnie jak każdy inny zasób w sieci wirtualnej.

Aby uzyskać listę usług PaaS obsługujących funkcję Łącza prywatnego, zapoznaj się z [dokumentacją](https://docs.microsoft.com/azure/private-link/index)łącza prywatnego . Prywatny punkt końcowy to prywatny adres IP w określonej sieci [wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) i podsieci.

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach platformy Azure, gdzie usługa Azure Database dla MySQL obsługuje warstwy cenowe ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.

## <a name="data-exfiltration-prevention"></a>Zapobieganie eksfiltracji danych

Ex-filtracji danych w usłudze Azure Database dla MySQL jest, gdy autoryzowany użytkownik, takich jak administrator bazy danych, jest w stanie wyodrębnić dane z jednego systemu i przenieść go do innej lokalizacji lub systemu poza organizację. Na przykład użytkownik przenosi dane do konta magazynu należącego do innej firmy.

Należy wziąć pod uwagę scenariusz z użytkownikiem z systemem MySQL Workbench wewnątrz maszyny wirtualnej platformy Azure (VM), który łączy się z usługą Azure Database dla serwera MySQL aprowizowanego w zachodnie stany USA. W poniższym przykładzie pokazano, jak ograniczyć dostęp z publicznych punktów końcowych w usłudze Azure Database dla MySQL przy użyciu kontroli dostępu do sieci.

* Wyłącz cały ruch usługi platformy Azure do usługi Azure Database dla MySQL za pośrednictwem publicznego punktu końcowego, ustawiając *opcję Zezwalaj na usługi platformy Azure* na WYŁĄCZANIE. Upewnij się, że żadne adresy IP lub zakresy nie mają dostępu do serwera za pośrednictwem [reguł zapory](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) lub [punktów końcowych usługi sieci wirtualnej](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* Zezwalaj tylko na ruch do bazy danych platformy Azure dla mysql przy użyciu prywatnego adresu IP maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz artykuły dotyczące reguł [punktu końcowego usługi](concepts-data-access-and-security-vnet.md) i [sieci wirtualnej](howto-manage-vnet-using-portal.md).

* Na maszynie Wirtualnej platformy Azure zawęź zakres połączenia wychodzącego przy użyciu sieciowych grup zabezpieczeń (nsg) i tagów usług w następujący sposób

    * Określ regułę nsg, aby zezwolić na ruch dla *tagu usługi = SQL. WestUs* — zezwalanie tylko na połączenie z usługą Azure Database dla mysql w zachodnie stany USA
    * Określ regułę sieciowej grupy danych (o wyższym priorytecie), aby odmówić ruchu dla *tagu usługi = SQL* — odmawianie połączeń z usługą Azure Database dla mysql we wszystkich regionach</br></br>

Po zakończeniu tej konfiguracji maszyna wirtualna platformy Azure może łączyć się tylko z usługą Azure Database for MySQL w regionie Zachodnie stany USA. Jednak łączność nie jest ograniczona do jednej usługi Azure Database dla MySQL. Maszyna wirtualna nadal można połączyć się z dowolnej usługi Azure Database dla MySQL w regionie Zachodnie stany USA, w tym baz danych, które nie są częścią subskrypcji. Firma Microsoft zmniejszyła zakres eksfiltracji danych w powyższym scenariuszu do określonego regionu, nie wyeliminowaliśmy go całkowicie.</br>

Za pomocą łącza prywatnego można teraz skonfigurować kontrolki dostępu do sieci, takie jak sieciowe sieciowe, aby ograniczyć dostęp do prywatnego punktu końcowego. Poszczególne zasoby usługi Azure PaaS są następnie mapowane do określonych prywatnych punktów końcowych. Złośliwy niejawny tester może uzyskać dostęp tylko do zamapowanych zasobów PaaS (na przykład usługi Azure Database for MySQL) i nie ma innego zasobu.

## <a name="on-premises-connectivity-over-private-peering"></a>Łączność lokalna za prywatną komunikację równorzędną

Po nawiązaniu połączenia z publicznym punktem końcowym z komputerów lokalnych adres IP musi zostać dodany do zapory opartej na protokãoła IP przy użyciu reguły zapory na poziomie serwera. Ten model działa dobrze, umożliwiając dostęp do poszczególnych maszyn dla obciążeń deweloperskich lub testowych, ale jest trudny do zarządzania w środowisku produkcyjnym.

Za pomocą łącza prywatnego można włączyć dostęp międzylokacyjny do prywatnego punktu końcowego przy użyciu [trasy ekspresowej](https://azure.microsoft.com/services/expressroute/) (ER), prywatnej komunikacji równorzędnej lub [tunelu sieci VPN.](https://docs.microsoft.com/azure/vpn-gateway/) Następnie mogą wyłączyć cały dostęp za pośrednictwem publicznego punktu końcowego i nie używać zapory opartej na protokãoła IP.

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Konfigurowanie łącza prywatnego dla bazy danych platformy Azure dla mysql

### <a name="creation-process"></a>Proces tworzenia

Prywatne punkty końcowe są wymagane do włączenia łącza prywatnego. Można to zrobić za pomocą następujących przewodników in how-to.

* [Portal Azure](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [Cli](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Proces zatwierdzania
Gdy administrator sieci utworzy prywatny punkt końcowy (PE), administrator MySQL może zarządzać połączeniem prywatnego punktu końcowego (PEC) z usługą Azure Database for MySQL. To rozdzielenie obowiązków między administratorem sieci i DBA jest przydatne do zarządzania usługą Azure Database dla łączności MySQL. 

* Przejdź do zasobu serwera Usługi Azure Database for MySQL w witrynie Azure portal. 
    * Wybieranie połączeń prywatnego punktu końcowego w lewym okienku
    * Pokazuje listę wszystkich połączeń prywatnych punktów końcowych (PEC)
    * Utworzony odpowiedni prywatny punkt końcowy

![wybieranie portalu prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Wybierz indywidualny PEC z listy, wybierając go.

![wybieranie prywatnego punktu końcowego oczekującego na zatwierdzenie](media/concepts-data-access-and-security-private-link/select-private-link.png)

* Administrator serwera MySQL może zatwierdzić lub odrzucić PEC i opcjonalnie dodać krótką odpowiedź tekstową.

![wybieranie wiadomości prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Po zatwierdzeniu lub odrzuceniu lista będzie odzwierciedlać odpowiedni stan wraz z tekstem odpowiedzi

![wybieranie stanu końcowego prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Przypadki użycia łącza prywatnego dla usługi Azure Database dla mysql

Klienci mogą łączyć się z prywatnym punktem końcowym z tej samej sieci wirtualnej, sieci wirtualnej w tym samym regionie lub za pośrednictwem połączenia sieci wirtualnej do sieci wirtualnej w różnych regionach. Ponadto klienci mogą łączyć się z lokalnymi przy użyciu usługi ExpressRoute, prywatnej komunikacji równorzędnej lub tunelowania sieci VPN. Poniżej znajduje się uproszczony diagram przedstawiający typowe przypadki użycia.

![wybieranie przeglądu prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Łączenie się z maszyny Wirtualnej platformy Azure w sieci wirtualnej (sieci wirtualnej)
Konfigurowanie [komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) w celu ustanowienia łączności z usługą Azure Database dla mysql z maszyny wirtualnej platformy Azure w równorzędnej sieci wirtualnej.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Łączenie się z maszyny wirtualnej platformy Azure w środowisku sieci wirtualnej do sieci wirtualnej
Skonfiguruj [połączenie bramy sieci VPN sieci wirtualnej do sieci wirtualnej,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) aby ustanowić łączność z usługą Azure Database dla mySQL z maszyny wirtualnej platformy Azure w innym regionie lub subskrypcji.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Łączenie się ze środowiska lokalnego za pośrednictwem sieci VPN
Aby ustanowić łączność ze środowiska lokalnego do usługi Azure Database for MySQL, wybierz i zaimplementuj jedną z opcji:

* [Połączenie z punktem lokacji](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Połączenie sieci VPN typu lokacja-lokacja](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Obwód usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Łącze prywatne w połączeniu z regułami zapory

Następujące sytuacje i wyniki są możliwe podczas korzystania z łącza prywatnego w połączeniu z regułami zapory:

* Jeśli nie skonfigurujesz żadnych reguł zapory, domyślnie żaden ruch nie będzie mógł uzyskać dostępu do bazy danych Azure Database dla mysql.

* Jeśli skonfigurujesz ruch publiczny lub punkt końcowy usługi i utworzysz prywatne punkty końcowe, różne typy ruchu przychodzącego są autoryzowane przez odpowiedni typ reguły zapory.

* Jeśli nie skonfigurujesz żadnego ruchu publicznego lub punktu końcowego usługi i utworzysz prywatne punkty końcowe, usługa Azure Database for MySQL jest dostępna tylko za pośrednictwem prywatnych punktów końcowych. Jeśli nie skonfigurujesz ruchu publicznego lub punktu końcowego usługi, po odrzuceniu lub usunięciu wszystkich zatwierdzonych prywatnych punktów końcowych, żaden ruch nie będzie mógł uzyskać dostępu do bazy danych Azure Database for MySQL.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Odmów dostępu publicznego dla usługi Azure Database dla mysql

Jeśli chcesz polegać tylko na prywatnych punktach końcowych dostępu do ich bazy danych azure dla MySQL, można wyłączyć ustawienie wszystkich publicznych punktów końcowych (tj. [reguł zapory](concepts-firewall-rules.md) i [punktów końcowych usługi sieci wirtualnej),](concepts-data-access-and-security-vnet.md)ustawiając konfigurację **Odmowa dostępu do sieci publicznej** na serwerze bazy danych. 

Gdy to ustawienie jest ustawione na *TAK,* tylko połączenia za pośrednictwem prywatnych punktów końcowych są dozwolone do usługi Azure Database dla MySQL. Gdy to ustawienie jest ustawione na *NIE,* klienci mogą połączyć się z usługą Azure Database for MySQL na podstawie ustawień punktu końcowego usługi zapory lub sieci wirtualnej. Ponadto po ustawieniu wartości dostępu do sieci prywatnej nie można dodawać i/lub aktualizować istniejących reguł zapory i punktu końcowego usługi sieci wirtualnej.

> [!Note]
> Ta funkcja jest dostępna we wszystkich regionach platformy Azure, gdzie usługa Azure Database for PostgreSQL — pojedynczy serwer obsługuje warstwy cenowe ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.
>
> To ustawienie nie ma żadnego wpływu na konfiguracje SSL i TLS dla usługi Azure Database for MySQL.

Aby dowiedzieć się, jak ustawić **odmowę dostępu** do sieci publicznej dla usługi Azure Database dla mysql z witryny Azure portal, zobacz [Jak skonfigurować odmowę dostępu do sieci publicznej](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usługach Azure Database dla funkcji zabezpieczeń MySQL, zobacz następujące artykuły:

* Aby skonfigurować zaporę dla usługi Azure Database dla mysql, zobacz [Obsługa zapory](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules).

* Aby dowiedzieć się, jak skonfigurować punkt końcowy usługi sieci wirtualnej dla usługi Azure Database dla mysql, zobacz [Konfigurowanie dostępu z sieci wirtualnych](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* Aby zapoznać się z omówieniem usługi Azure Database dla łączności MySQL, zobacz [Usługa Azure Database for MySQL Connectivity Architecture](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture)
