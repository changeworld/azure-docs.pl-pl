---
title: Reguły sieci wirtualnej — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak połączyć się z usługą Azure Database dla postgreSQL — single server za pomocą punktów końcowych usługi sieci wirtualnej (vnet).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 512ad8f93da53afb618491cd1769645d8edb0b14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965845"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql---single-server"></a>Używanie punktów końcowych i reguł usługi sieci wirtualnej dla usługi Azure Database dla postgreSQL — pojedynczy serwer

*Reguły sieci wirtualnej* to jedna funkcja zabezpieczeń zapory, która określa, czy serwer usługi Azure Database for PostgreSQL akceptuje komunikację wysyłaną z określonych podsieci w sieciach wirtualnych. W tym artykule wyjaśniono, dlaczego funkcja reguły sieci wirtualnej jest czasami najlepszym rozwiązaniem dla bezpiecznego zezwalania na komunikację z usługą Azure Database dla serwera PostgreSQL.

Aby utworzyć regułę sieci wirtualnej, musi najpierw być [sieć wirtualna][vm-virtual-network-overview] (VNet) i [punkt końcowy usługi sieci wirtualnej][vm-virtual-network-service-endpoints-overview-649d] dla reguły do odwołania. Na poniższym rysunku przedstawiono sposób działania punktu końcowego usługi sieci wirtualnej z usługą Azure Database for PostgreSQL:

![Przykład działania punktu końcowego usługi sieci wirtualnej](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach chmury publicznej platformy Azure, gdzie usługa Azure Database for PostgreSQL jest wdrażana dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
> W przypadku komunikacji równorzędnej sieci wirtualnej, jeśli ruch przepływa przez wspólną bramę sieci wirtualnej z punktami końcowymi usługi i ma przepływ do elementu równorzędnego, utwórz regułę ACL/VNet, aby zezwolić maszynom wirtualnym platformy Azure w sieci wirtualnej bramy na dostęp do bazy danych Azure dla serwera PostgreSQL.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia i opis

**Sieć wirtualna:** Z subskrypcją platformy Azure mogą być skojarzone sieci wirtualne.

**Podsieć:** Sieć wirtualna zawiera **podsieci**. Wszystkie maszyny wirtualne platformy Azure (maszyny wirtualne), które zostały przypisane do podsieci. Jedna podsieć może zawierać wiele maszyn wirtualnych lub innych węzłów obliczeniowych. Węzły obliczeniowe, które znajdują się poza siecią wirtualną, nie mogą uzyskać dostępu do sieci wirtualnej, chyba że skonfigurujesz zabezpieczenia w taki sposób, aby zezwolić na dostęp.

**Punkt końcowy usługi sieci wirtualnej:** [Punkt końcowy usługi sieci wirtualnej][vm-virtual-network-service-endpoints-overview-649d] jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwy typu usługi Platformy Azure. W tym artykule interesuje nas nazwa typu **Microsoft.Sql**, która odnosi się do usługi Azure o nazwie BAZA DANYCH SQL. Ten tag usługi dotyczy również usługi Azure Database dla usług PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania tagu usługi **Microsoft.Sql** do punktu końcowego usługi sieci wirtualnej skonfiguruje ruch punktu końcowego usługi dla wszystkich usług Azure SQL Database, Azure Database for PostgreSQL i Azure Database for MySQL serwerów w podsieci. 

**Reguła sieci wirtualnej:** Reguła sieci wirtualnej dla serwera usługi Azure Database for PostgreSQL to podsieć wymieniona na liście kontroli dostępu (ACL) serwera usługi Azure Database for PostgreSQL. Aby być na listy ACL dla serwera usługi Azure Database for PostgreSQL, podsieć musi zawierać nazwę typu **Microsoft.Sql.**

Reguła sieci wirtualnej informuje usługę Azure Database for PostgreSQL serwera do akceptowania komunikacji z każdego węzła, który znajduje się w podsieci.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Korzyści wynikające z reguły sieci wirtualnej

Dopóki nie podejmiesz działania, maszyny wirtualne w podsieci nie mogą komunikować się z usługą Azure Database dla serwera PostgreSQL. Jedną z akcji, która ustanawia komunikację jest utworzenie reguły sieci wirtualnej. Uzasadnienie wyboru metody reguły sieci wirtualnej wymaga dyskusji porównania i kontrastu z udziałem konkurencyjnych opcji zabezpieczeń oferowanych przez zaporę.

### <a name="a-allow-access-to-azure-services"></a>A. Zezwalaj na dostęp do usług platformy Azure

Okienko zabezpieczeń Połączenie ma przycisk **ON/OFF,** który jest oznaczony **jako Zezwalaj na dostęp do usług platformy Azure.** Ustawienie **ON** umożliwia komunikację ze wszystkich adresów IP platformy Azure i wszystkich podsieci platformy Azure. Te usługi Azure IP lub podsieci mogą nie być własnością Ciebie. To ustawienie **ON** jest prawdopodobnie bardziej otwarte niż chcesz, aby baza danych usługi Azure database dla bazy danych PostgreSQL była. Funkcja reguły sieci wirtualnej oferuje znacznie większą szczegółową kontrolę.

### <a name="b-ip-rules"></a>B. Reguły IP

Zapora usługi Azure Database for PostgreSQL umożliwia określenie zakresów adresów IP, z których komunikacja jest akceptowana w bazie danych usługi Azure Database for PostgreSQL. Takie podejście jest dobre dla stabilnych adresów IP, które znajdują się poza siecią prywatną platformy Azure. Jednak wiele węzłów w sieci prywatnej platformy Azure jest konfigurowanych z *dynamicznymi* adresami IP. Dynamiczne adresy IP mogą ulec zmianie, na przykład po ponownym uruchomieniu maszyny Wirtualnej. Byłoby głupotą określić dynamiczny adres IP w regule zapory w środowisku produkcyjnym.

Można uratować opcję IP, uzyskując *statyczny* adres IP dla maszyny Wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej przy użyciu witryny Azure portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Jednak statyczne podejście IP może stać się trudne do zarządzania i jest kosztowne, gdy odbywa się na dużą skalę. Reguły sieci wirtualnej są łatwiejsze do ustanowienia i zarządzania.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Nie można jeszcze mieć usługi Azure Database for PostgreSQL w podsieci bez definiowania punktu końcowego usługi

Jeśli serwer **Microsoft.Sql** był węzłem w podsieci w sieci wirtualnej, wszystkie węzły w sieci wirtualnej mogą komunikować się z serwerem usługi Azure Database for PostgreSQL. W takim przypadku maszyny wirtualne mogą komunikować się z usługą Azure Database for PostgreSQL bez konieczności stosowania reguł sieci wirtualnej lub reguł IP.

Jednak od sierpnia 2018 r. usługa Azure Database for PostgreSQL nie jest jeszcze wśród usług, które można przypisać bezpośrednio do podsieci.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Szczegółowe informacje o regułach sieci wirtualnej

W tej sekcji opisano kilka szczegółów dotyczących reguł sieci wirtualnej.

### <a name="only-one-geographic-region"></a>Tylko jeden region geograficzny

Każdy punkt końcowy usługi sieci wirtualnej ma zastosowanie tylko do jednego regionu platformy Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci.

Każda reguła sieci wirtualnej jest ograniczona do regionu, który ma zastosowanie do jej bazowego punktu końcowego.

### <a name="server-level-not-database-level"></a>Poziom serwera, a nie na poziomie bazy danych

Każda reguła sieci wirtualnej ma zastosowanie do całej usługi Azure Database dla serwera PostgreSQL, a nie tylko do jednej określonej bazy danych na serwerze. Innymi słowy reguła sieci wirtualnej ma zastosowanie na poziomie serwera, a nie na poziomie bazy danych.

#### <a name="security-administration-roles"></a>Role administracji zabezpieczeń

Istnieje oddzielenie ról zabezpieczeń w administrowaniu punktami końcowymi usługi sieci wirtualnej. Akcja jest wymagana od każdej z następujących ról:

- **Administrator sieci:** &nbsp; włącz punkt końcowy.
- **Administrator bazy danych:** &nbsp; Zaktualizuj listę kontroli dostępu (ACL), aby dodać daną podsiecię do usługi Azure Database for PostgreSQL server.

*Alternatywa RBAC:*

Role administratora sieci i administratora bazy danych mają więcej możliwości niż są potrzebne do zarządzania regułami sieci wirtualnej. Potrzebny jest tylko podzbiór ich możliwości.

Masz możliwość korzystania z [kontroli dostępu opartej na rolach (RBAC) na][rbac-what-is-813s] platformie Azure, aby utworzyć jedną rolę niestandardową, która ma tylko niezbędny podzbiór możliwości. Rola niestandardowa może być używana zamiast angażowania administratora sieci lub administratora bazy danych. Powierzchnia ekspozycji zabezpieczeń jest niższa, jeśli dodasz użytkownika do roli niestandardowej, w porównaniu do dodawania użytkownika do pozostałych dwóch głównych ról administratora.

> [!NOTE]
> W niektórych przypadkach usługa Azure Database for PostgreSQL i podsieć sieci wirtualnej są w różnych subskrypcjach. W takich przypadkach należy upewnić się, że następujące konfiguracje:
> - Obie subskrypcje muszą znajdować się w tej samej dzierżawie usługi Azure Active Directory.
> - Użytkownik ma wymagane uprawnienia do inicjowania operacji, takich jak włączanie punktów końcowych usługi i dodawanie podsieci sieci wirtualnej do danego serwera.
> - Upewnij się, że obie subskrypcje mają zarejestrowanego dostawcę zasobów **Microsoft.Sql.** Aby uzyskać więcej informacji, zapoznaj się [z rejestracją menedżera zasobów][resource-manager-portal]

## <a name="limitations"></a>Ograniczenia

W przypadku usługi Azure Database for PostgreSQL funkcja reguł sieci wirtualnej ma następujące ograniczenia:

- Aplikację sieci Web można mapować na prywatny adres IP w sieci wirtualnej/podsieci. Nawet jeśli punkty końcowe usługi są włączone z danej sieci wirtualnej/podsieci, połączenia z aplikacji sieci Web do serwera będą miały publiczne źródło IP platformy Azure, a nie źródło sieci wirtualnej/podsieci. Aby włączyć łączność z aplikacji sieci Web do serwera, który ma reguły zapory sieci wirtualnej, należy zezwolić usługom platformy Azure na dostęp do serwera na serwerze.

- W zaporze bazy danych usługi Azure dla postgreSQL każda reguła sieci wirtualnej odwołuje się do podsieci. Wszystkie te podsieci, do których istnieje odwołanie, muszą być hostowane w tym samym regionie geograficznym, w którym znajduje się usługa Azure Database for PostgreSQL.

- Każdy serwer usługi Azure Database for PostgreSQL może mieć maksymalnie 128 wpisów listy ACL dla danej sieci wirtualnej.

- Reguły sieci wirtualnej mają zastosowanie tylko do sieci wirtualnych usługi Azure Resource Manager; a nie do klasycznych sieci [modeli wdrażania.][arm-deployment-model-568f]

- Włączenie punktów końcowych usługi sieci wirtualnej do bazy danych Azure database dla postgreSQL przy użyciu tagu usługi **Microsoft.Sql** umożliwia również punkty końcowe dla wszystkich usług bazy danych Azure Database: Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL Database i Azure SQL Data Warehouse.

- Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

- W zaporze zakresy adresów IP mają zastosowanie do następujących elementów sieci, ale reguły sieci wirtualnej nie:
    - [Wirtualna sieć prywatna (LOKACJa na lokację) (VPN)][vpn-gateway-indexmd-608y]
    - Lokalnie za pośrednictwem usługi [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Jeśli sieć jest połączona z siecią platformy Azure za pomocą usługi [ExpressRoute,][expressroute-indexmd-744v]każdy obwód jest skonfigurowany z dwoma publicznymi adresami IP w przeglądarce Microsoft Edge. Dwa adresy IP są używane do łączenia się z usługami firmy Microsoft, takich jak usługa Azure Storage, przy użyciu publicznej komunikacji równorzędnej platformy Azure.

Aby zezwolić na komunikację z obwodu do usługi Azure Database dla postgreSQL, należy utworzyć reguły sieci IP dla publicznych adresów IP obwodów. Aby znaleźć publiczne adresy IP obwodu usługi ExpressRoute, otwórz bilet pomocy technicznej w usłudze ExpressRoute przy użyciu portalu Azure.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Dodawanie reguły zapory sieci wirtualnej do serwera bez włączania punktów końcowych usługi sieci wirtualnej

Samo ustawienie reguły zapory nie pomaga zabezpieczyć serwera w sieci wirtualnej. Należy również włączyć punkty końcowe usługi sieci **wirtualnej,** aby zabezpieczenia zostały zastosowane. Po włączeniu punktów **On**końcowych usługi w sieci wirtualnej wystąpi przestój, dopóki nie zakończy przejścia z **wyłączonego** **na włączone**. Jest to szczególnie ważne w kontekście dużych sieci wirtualnych. Flaga **IgnoreMissingServiceEndpoint** służy do zmniejszania lub eliminowania przestojów podczas przejścia.

Flagę **IgnoreMissingServiceEndpoint** można ustawić przy użyciu interfejsu wiersza polecenia lub portalu platformy Azure.

## <a name="related-articles"></a>Pokrewne artykuły:
- [Sieci wirtualne platformy Azure][vm-virtual-network-overview]
- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z artykułami dotyczącymi tworzenia reguł sieci wirtualnej, zobacz:
- [Tworzenie reguł usługi Azure Database dla witryny sieci wirtualnej PostgreSQL i zarządzanie nimi przy użyciu witryny Azure portal](howto-manage-vnet-using-portal.md)
- [Tworzenie reguł usługi Azure Database dla witryny sieci Wirtualnej PostgreSQL i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
