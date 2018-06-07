---
title: Omówienie punktu końcowego usługi Azure bazy danych PostgreSQL serwera w sieci wirtualnej | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak działają punktów końcowych usługi sieci wirtualnej Azure bazy danych dla serwera PostgreSQL.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2018
ms.openlocfilehash: a832f45027fc5337d9e76ec9cc4898286121278c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655660"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql"></a>Na użytek punktów końcowych usługi sieci wirtualnej i reguły dla bazy danych Azure PostgreSQL

*Zasady sieci wirtualnej* są jedną funkcję zabezpieczeń zapory, która kontroluje, czy bazy danych Azure, aby serwer PostgreSQL akceptuje komunikacji, które są wysyłane z określonej podsieci w sieci wirtualnych. W tym artykule opisano, dlaczego funkcja reguły sieci wirtualnej jest czasami najlepszym dotycząca bezpiecznie zezwolenie na komunikację z bazą danych Azure PostgreSQL serwera.

Aby utworzyć regułę sieci wirtualnej, najpierw należy [sieci wirtualnej] [ vm-virtual-network-overview] (VNet) i [punkt końcowy usługi sieci wirtualnej] [ vm-virtual-network-service-endpoints-overview-649d] dla reguły do odwołania. Poniżej przedstawiono, jak działa punkt końcowy usługi sieci wirtualnej z bazą danych Azure dla PostgreSQL:

![Przykład działania punkt końcowy usługi sieci wirtualnej](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> W bazie danych Azure PostgreSQL ta funkcja jest dostępna w publicznej wersji zapoznawczej we wszystkich regionach Azure chmury publicznej, w której wdrażana jest bazą danych Azure dla PostgreSQL.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia i opis

**Sieć wirtualna:** może mieć skojarzone z subskrypcją platformy Azure sieci wirtualnych.

**Podsieci:** sieć wirtualna zawiera **podsieci**. Wszelkie Azure maszyn wirtualnych (VM), które masz przypisanych do podsieci. W jednej podsieci może zawierać wiele maszyn wirtualnych lub innych węzłów obliczeniowych. Obliczenia bazy danych węzłów, które znajdują się poza sieci wirtualnej nie może uzyskiwać dostępu do sieci wirtualnej, dopiero po skonfigurowaniu zabezpieczeń, aby zezwolić na dostęp.

**Punkt końcowy usługi sieci wirtualnej:** A [punkt końcowy usługi sieci wirtualnej] [ vm-virtual-network-service-endpoints-overview-649d] podsieć, w których wartości właściwości zawierają jedną lub więcej nazw typu formalnego usługi Azure. W tym artykule Dbamy o nazwę typu **Microsoft.Sql**, które odwołuje się do usługi Azure o nazwie bazy danych SQL. Ten numer seryjny usługi ma również zastosowanie do bazy danych Azure dla usług PostgreSQL i MySQL. Ważne jest, aby podczas stosowania należy uwzględnić **Microsoft.Sql** usługi tagu dla punktu końcowego sieci wirtualnej zostanie ona skonfigurowana ruch punkt końcowy usługi dla bazy danych SQL Azure, Azure bazy danych PostgreSQL i bazy danych Azure, serwerów MySQL w tej podsieci. 

**Reguła sieci wirtualnej:** jest reguła sieci wirtualnej Azure bazy danych dla serwera PostgreSQL podsieć, która znajduje się na liście kontroli dostępu (ACL) bazy danych Azure, aby serwer PostgreSQL. Się na liście ACL PostgreSQL serwera bazy danych Azure, musi zawierać podsieci **Microsoft.Sql** nazwy typu.

Reguła sieci wirtualnej informuje bazy danych Azure PostgreSQL serwera do akceptowania komunikacji z każdego węzła, który znajduje się w podsieci.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Korzyści wynikające z reguły sieci wirtualnej

Aż do wprowadzenia akcji, maszyn wirtualnych w podsieci nie może komunikować się z bazą danych Azure PostgreSQL serwera. Jedną akcję, która nawiązuje komunikację jest tworzenie reguły sieci wirtualnej. Uzasadnienie Wybieranie sieci wirtualnej podejście reguły wymaga dyskusji Porównaj kontrast, obejmujące konkurencyjnych opcji zabezpieczeń oferowanych przez zaporę.

### <a name="a-allow-access-to-azure-services"></a>A. Zezwalaj na dostęp do usług platformy Azure

W okienku zabezpieczeń połączenia jest **ON/OFF** przycisk o nazwie **zezwolić na dostęp do usług platformy Azure**. **ON** ustawienie umożliwia komunikację z wszystkie adresy IP platformy Azure i wszystkie podsieci platformy Azure. Te adresy IP platformy Azure lub podsieci nie może należeć do przez użytkownika. To **ON** ustawienie jest prawdopodobnie bardziej otwarte niż bazy danych Azure, PostgreSQL bazy danych. Funkcja reguły sieci wirtualnej oferuje znacznie skuteczniejszą kontrolę.

### <a name="b-ip-rules"></a>B. Reguły IP

Bazy danych Azure zapory PostgreSQL służy do określenia zakresów adresów IP, z których łączności są akceptowane do bazy danych Azure dla bazy danych PostgreSQL. Ta metoda jest poprawnie stabilna adresów IP jest spoza sieci prywatnej platformy Azure. Ale wiele węzłów w prywatnej sieci platformy Azure są skonfigurowane przy użyciu *dynamiczne* adresów IP. Dynamiczne adresy IP mogą ulec zmianie, np. podczas ponownego uruchamiania maszyny Wirtualnej. Byłoby folly, aby określić dynamicznego adresu IP w regule zapory w środowisku produkcyjnym.

Opcji IP może być odzyskana, uzyskując *statycznych* adres IP dla maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [skonfigurować prywatnych adresów IP dla maszyny wirtualnej przy użyciu portalu Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Jednak ujęciu statycznego adresu IP może być trudne do zarządzania i jest kosztowne, jeśli zostaną wykonane na dużą skalę. Zasady sieci wirtualnej mają ułatwić ustalenie, jak i do zarządzania.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Nie można jeszcze bazy danych platformy Azure dla PostgreSQL w podsieci bez zdefiniowania punkt końcowy usługi

Jeśli Twoje **Microsoft.Sql** serwer był węzłem w podsieci w sieci wirtualnej, wszystkie węzły w sieci wirtualnej może skomunikować się z bazy danych Azure, PostgreSQL serwera. W takim przypadku maszyn wirtualnych może komunikować się z bazy danych Azure dla PostgreSQL bez konieczności wszystkie reguły sieci wirtualnej lub reguły IP.

Jednak począwszy od 2018 maja bazą danych PostgreSQL usługi Azure nie jest jeszcze pomiędzy usługami, które można przypisać bezpośrednio do podsieci.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Szczegółowe informacje o zasadach sieci wirtualnej

W tej sekcji opisano kilka szczegółowe informacje o regułach sieci wirtualnej.

### <a name="only-one-geographic-region"></a>Tylko jeden region geograficzny

Każdy punkt końcowy usługi sieci wirtualnej dotyczy tylko jeden region platformy Azure. Punkt końcowy nie obsługuje innych regionów akceptował komunikację z podsieci.

Wszystkie reguły sieci wirtualnej jest ograniczone do regionu, który dotyczy jej podstawowy punkt końcowy.

### <a name="server-level-not-database-level"></a>Poziom serwera, nie poziom bazy danych

Każda reguła sieci wirtualnej ma zastosowanie do całej bazy danych Azure, aby serwer PostgreSQL, a nie tylko do jednej określonej bazy danych na serwerze. Innymi słowy sieć wirtualną reguła jest stosowana na poziomie serwera, nie na poziomie bazy danych.

#### <a name="security-administration-roles"></a>Role zabezpieczeń administracji

Brak separacji ról zabezpieczeń w administrowanie punktów końcowych usługi sieci wirtualnej. Akcja jest wymagany z każdej z następujących ról:

- **Administrator sieci:** &nbsp; włączyć punkt końcowy.
- **Administrator bazy danych:** &nbsp; aktualizowanie listy kontroli dostępu (ACL), aby dodać jednej podsieci do bazy danych Azure PostgreSQL serwera.

*Alternatywa RBAC:*

Role administratora sieci i administratora bazy danych ma więcej możliwości niż są wymagane do zarządzania zasadami sieci wirtualnej. Wymagany jest tylko podzestaw możliwości ich.

Istnieje możliwość użycia [kontroli dostępu opartej na rolach (RBAC)] [ rbac-what-is-813s] na platformie Azure, aby utworzyć jednej roli niestandardowej, która ma niezbędne podzestaw możliwości. Tworzona rola niestandardowa można użyć zamiast z administratorem sieci lub administratorem bazy danych. Powierzchni z zagrożenie bezpieczeństwa jest mniejszy, jeśli użytkownik zostanie dodany do niestandardowej roli zabezpieczeń, i dodanie użytkownika do dwóch innych ról administratora głównych.

> [!NOTE]
> W niektórych przypadkach bazą danych PostgreSQL i podsieci sieci wirtualnej Azure znajdują się w różnych subskrypcji. W takich sytuacjach należy następujące konfiguracje:
> - Obie subskrypcje muszą być w tej samej dzierżawy usługi Azure Active Directory.
> - Użytkownik ma uprawnienia wymagane do zainicjowania operacji, takich jak włączanie punktów końcowych usługi i dodawanie podsieci sieci wirtualnej do danego serwera.

## <a name="limitations"></a>Ograniczenia

W bazie danych Azure PostgreSQL funkcja reguł sieć wirtualna ma następujące ograniczenia:

- W zaporze dla bazy danych Azure, PostgreSQL każda reguła sieci wirtualnej odwołuje się do podsieci. Te przywoływanego podsieci muszą być obsługiwane w tym samym regionie geograficznym, obsługującym bazę danych Azure dla PostgreSQL.

- Każda baza danych Azure PostgreSQL serwera może mieć maksymalnie 128 wpisy list kontroli dostępu dla dowolnego danej sieci wirtualnej.

- Reguły sieci wirtualnej mają zastosowanie tylko do sieci wirtualnych Azure Resource Manager. i nie [klasycznego modelu wdrażania] [ arm-deployment-model-568f] sieci.

- Włączanie w sieci wirtualnej usługi punktów końcowych bazy danych platformy Azure dla PostgreSQL przy użyciu **Microsoft.Sql** numer seryjny umożliwia również punkty końcowe dla wszystkich usług Azure bazy danych: baza danych Azure dla programu MySQL, PostgreSQL bazy danych Azure , Baza danych azure SQL i magazyn danych Azure SQL.

- W czasie publicznej wersji zapoznawczej nie jest obsługiwane dla operacji przenoszenia sieci wirtualnej. Aby przenieść regułę sieci wirtualnej, Porzuć i utwórz ją ponownie.

- Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

- W zaporze zakresów adresów IP są stosowane do następujących elementów sieci, ale zasady sieci wirtualnej nie:
    - [Lokacja-lokacja (S2S) wirtualnej sieci prywatnej (VPN)][vpn-gateway-indexmd-608y]
    - Lokalnymi za pośrednictwem [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Jeśli sieć jest podłączony do sieci Azure przy użyciu [ExpressRoute][expressroute-indexmd-744v], każdy obwód jest skonfigurowany z dwóch publicznych adresów IP w Microsoft Edge. Dwa adresy IP są używane nawiązać Services firmy Microsoft, takich jak do magazynu Azure, korzystając z publicznej komunikacji równorzędnej platformy Azure.

Aby umożliwić komunikację z obwodu bazą danych Azure PostgreSQL, należy utworzyć zasady sieci IP dla publicznych adresów IP w sieci obwodów. Aby można było znaleźć publiczne adresy IP obwodu ExpressRoute, otwórz bilet pomocy technicznej z ExpressRoute przy użyciu portalu Azure.

## <a name="related-articles"></a>Pokrewne artykuły:
- [Sieci wirtualnych platformy Azure][vm-virtual-network-overview]
- [Punkty końcowe usługi sieci wirtualnej platformy Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Kolejne kroki
W przypadku artykułów na temat tworzenia zasad sieci wirtualnej zobacz:
- [Tworzenie i zarządzanie bazą danych Azure dla reguły PostgreSQL sieci wirtualnej przy użyciu portalu Azure](howto-manage-vnet-using-portal.md)
- [Tworzenie i zarządzanie bazą danych Azure dla reguł PostgreSQL sieci wirtualnej za pomocą wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same Github repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml