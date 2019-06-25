---
title: Punkty końcowe usługi dla sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak włączyć bezpośredni dostęp do zasobów platformy Azure z sieci wirtualnej przy użyciu punktów końcowych usługi.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: sumeet.mittal
ms.custom: ''
ms.openlocfilehash: e621eeeca7a4f325efcfb242c204b2f727e55fc4
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147760"
---
# <a name="virtual-network-service-endpoints"></a>Punkty końcowe usługi dla sieci wirtualnej

Punkty końcowe usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową i tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure.

Ta funkcja jest dostępna dla następujących regionów i usług platformy Azure:

**Ogólnie dostępne**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** : ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Serwer usługi Azure Database for PostgreSQL](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : ogólnie dostępny w regionach platformy Azure, w których jest dostępna usługa bazy danych.
- **[Serwer usługi Azure Database for MySQL](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : ogólnie dostępny w regionach platformy Azure, w których jest dostępna usługa bazy danych.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** : ogólnie dostępny w regionach platformy Azure, w których jest dostępna usługa bazy danych.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** : ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : ogólnie dostępna we wszystkich regionach platformy Azure, w których jest dostępna usługa ADLS Gen1.

**Publiczna wersja zapoznawcza**

- **[Usługa Azure Container Registry](../container-registry/container-registry-vnet.md)** : W wersji zapoznawczej dostępne we wszystkich regionach platformy Azure, w których usługa Azure Container Registry jest dostępna.

Najbardziej aktualne powiadomienia można znaleźć na stronie [aktualizacji usługi Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Najważniejsze korzyści

Punkty końcowe usługi oferują następujące korzyści:

- **Lepsze zabezpieczenia zasobów usługi platformy Azure**: przestrzenie adresowe prywatnych sieci wirtualnych mogą się nakładać, przez co nie można za ich pomocą jednoznacznie identyfikować ruchu pochodzącego z sieci wirtualnej. Punkty końcowe usługi dają możliwość zabezpieczania zasobów usługi platformy Azure w sieci wirtualnej przez rozszerzenie tożsamości sieci wirtualnej na usługę. Po włączeniu punktów końcowych usługi w sieci wirtualnej można zabezpieczyć zasoby usług usługi platformy Azure w sieci wirtualnej przez dodanie reguły sieci wirtualnej do zasobów. Zwiększa to bezpieczeństwo przez całkowite uniemożliwienie publicznego dostępu z Internetu do tych zasobów i zezwolenie na ruch tylko z Twojej sieci wirtualnej.
- **Optymalny routing ruchu usług sieci platformy Azure z sieci wirtualnej**: obecnie wszystkie trasy w sieci wirtualnej, które wymuszają ruch internetowy za pomocą urządzeń lokalnych i/lub wirtualnych (sytuacja znana jako tunelowanie wymuszane), wymuszają również ruch w ramach usługi platformy Azure po tej samej trasie, co ruch internetowy. Punkty końcowe usługi zapewniają optymalny routing ruchu platformy Azure. 

  Punkty końcowe zawsze pobierają ruch bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Microsoft Azure. Zachowywanie ruchu w sieci szkieletowej platformy Azure umożliwia kontynuowanie inspekcji i monitorowania wychodzącego ruchu internetowego z sieci wirtualnych za pośrednictwem tunelowania wymuszanego, bez wywierania wpływu na ruch usługi. Dowiedz się więcej na temat [tras definiowanych przez użytkownika i tunelowania wymuszanego](virtual-networks-udr-overview.md).
- **Prosta konfiguracja i mniejsze ogólne koszty zarządzania**: zastrzeżone publiczne adresy IP nie są już potrzebne w Twoich sieciach wirtualnych do zabezpieczania zasobów platformy Azure za pośrednictwem zapory adresów IP. Do skonfigurowania punktów końcowych usługi nie jest wymagany translator adresów sieciowych ani urządzenie bramy. Punkty końcowe usługi można skonfigurować za pomocą prostego kliknięcia w podsieci. Utrzymywanie punktów końcowych nie wiąże się z dodatkowymi kosztami ogólnymi.

## <a name="limitations"></a>Ograniczenia

- Funkcja jest dostępna tylko dla sieci wirtualnych wdrożonych za pomocą modelu wdrażania przy użyciu usługi Azure Resource Manager.
- Punkty końcowe są włączone w podsieciach skonfigurowanych w sieciach wirtualnych platformy Azure. Punktów końcowych nie można używać dla ruchu ze środowiska lokalnego do usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie dostępu do usług platformy Azure ze środowiska lokalnego](#securing-azure-services-to-virtual-networks)
- W przypadku usługi Azure SQL punkt końcowy usługi dotyczy tylko ruchu usługi platformy Azure w regionie sieci wirtualnej. W przypadku usługi Azure Storage na potrzeby obsługi ruchu w magazynach GRS i RA-GRS punkty końcowe są rozszerzane, aby uwzględniać sparowane regiony, w których wdrożono sieć wirtualną. Dowiedz się więcej na temat [sparowanych regionów platformy Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- W przypadku usługi ADLS Gen 1 funkcja integracji z siecią wirtualną jest dostępna tylko dla sieci wirtualnych w tym samym regionie. Ponadto należy pamiętać, że integracja sieci wirtualnej dla usługi Azure Data Lake Storage Gen1 sprawia, że korzystanie z zabezpieczenia punktu końcowego usługi sieci wirtualnej między sieci wirtualnej i usługi Azure Active Directory (Azure AD), aby wygenerować dodatkowe zabezpieczenia oświadczenia w tokenie dostępu. Te oświadczenia są następnie używane do uwierzytelniania sieci wirtualnej na koncie usługi Data Lake Storage Gen1 i uzyskania dostępu. Tag "Microsoft.AzureActiveDirectory" na liście usług obsługujących punkty końcowe usługi jest używana tylko w przypadku obsługi punktów końcowych usługi Azure Data Lake Store Gen 1. Azure Active Directory (Azure AD) nie obsługuje natywnie punktów końcowych usługi. Dowiedz się więcej o [usługi Azure Data Lake Store Gen 1 Integracja z siecią wirtualną](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="securing-azure-services-to-virtual-networks"></a>Zabezpieczanie usług platformy Azure w sieciach wirtualnych

- Punkt końcowy usługi dla sieci wirtualnej zapewnia tożsamość sieci wirtualnej w usłudze platformy Azure. Po włączeniu punktów końcowych usługi w sieci wirtualnej można zabezpieczyć zasoby usług usługi platformy Azure w sieci wirtualnej przez dodanie reguły sieci wirtualnej do zasobów.
- Obecnie ruch usługi platformy Azure z sieci wirtualnej używa publicznych adresów IP jako źródłowych adresów IP. Dzięki punktom końcowym usługi ruch usługi jest przełączany na używanie prywatnych adresów sieci wirtualnej jako źródłowych adresów IP w przypadku uzyskiwania dostępu do usługi platformy Azure z sieci wirtualnej. To przełączenie umożliwia dostęp do usług bez konieczności stosowania zastrzeżonych publicznych adresów IP w zaporach adresów IP.

>[!NOTE]
> Dzięki punktom końcowym usługi źródłowe adresy IP maszyn wirtualnych w podsieci dla ruchu w usłudze mogą przełączyć się z używania publicznych adresów IPv4 na używanie prywatnych adresów IPv4. Istniejące reguły zapory usługi platformy Azure używające publicznych adresów IP platformy Azure przestaną działać po tym przełączeniu. Przed rozpoczęciem konfigurowania punktów końcowych usługi sprawdź, czy reguły zapory usługi platformy Azure zezwalają na ten przełącznik. Może również wystąpić tymczasowe przerwanie ruchu usługi z tej podsieci podczas konfigurowania punktów końcowych usługi. 
 
- __Zabezpieczanie dostępu do usług platformy Azure ze środowiska lokalnego__:

  Domyślnie nie można uzyskać dostępu do zasobów usługi platformy Azure zabezpieczonych w sieciach wirtualnych z sieci lokalnych. Jeśli chcesz zezwolić na ruch ze środowiska lokalnego, musisz również zezwolić na użycie publicznych adresów IP (zazwyczaj translatora adresów sieciowych) z sieci lokalnej lub obwodów usługi ExpressRoute. Te adresy IP można dodawać za pośrednictwem konfiguracji zapory adresów IP dla zasobów usługi platformy Azure.

  ExpressRoute: jeśli korzystasz z usługi [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) w środowisku lokalnym na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, konieczne będzie określenie używanych adresów IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są dostarczane przez klienta lub przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

![Zabezpieczanie usług platformy Azure w sieciach wirtualnych](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfigurowanie

- Punkty końcowe usługi są konfigurowane w podsieci sieci wirtualnej. Punkty końcowe współpracują z wystąpieniami obliczeniowymi dowolnego typu uruchomionymi w danej podsieci.
- W podsieci można skonfigurować wiele punktów końcowych usługi dla wszystkich obsługiwanych usług platformy Azure (na przykład Azure Storage lub Azure SQL Database).
- W przypadku usługi Azure SQL Database sieci wirtualne muszą znajdować się w tym samym regionie, co zasób usługi platformy Azure. Jeśli korzystasz z magazynów GRS i RA-GRS w usłudze Azure Storage, konto podstawowe powinno znajdować się w tym samym regionie, co sieć wirtualna. W przypadku wszystkich innych usług zasoby usługi platformy Azure mogą być chronione w sieciach wirtualnych w dowolnym regionie. 
- Sieć wirtualna, w której konfigurowany jest punkt końcowy, może należeć do tej samej subskrypcji, co zasób usługi platformy Azure, lub innej. Aby uzyskać więcej informacji na temat uprawnień wymaganych do konfigurowania punktów końcowych i zabezpieczania usług platformy Azure, zobacz [Aprowizowanie](#provisioning).
- W przypadku obsługiwanych usług można zabezpieczyć nowe lub istniejące zasoby w sieciach wirtualnych za pomocą punktów końcowych usługi.

### <a name="considerations"></a>Zagadnienia do rozważenia

- Po włączeniu punktu końcowego usługi źródłowe adresy IP maszyn wirtualnych w podsieci przełączają się z używania publicznych adresów IPv4 na używanie prywatnego adresu IPv4 podczas komunikacji z usługą z poziomu tej podsieci. Wszystkie otwarte połączenia TCP z usługą są zamykane podczas tego przełączania. Upewnij się, że żadne krytyczne zadania nie działają podczas włączania lub wyłączania punktu końcowego w usłudze dla podsieci. Upewnij się również, czy aplikacje mogą automatycznie połączyć się z usługami platformy Azure po przełączeniu adresu IP.

  Przełączenie adresu IP ma wpływ tylko na ruch usługi z sieci wirtualnej. Nie ma ono wpływu na pozostałe rodzaje ruchu problemu z/do publicznych adresów IPv4 przypisanych do maszyn wirtualnych. Jeśli masz istniejące reguły zapory używające publicznych adresów IP platformy Azure dla usług platformy Azure, te reguły przestają działać po przełączeniu na użycie prywatnych adresów sieci wirtualnej.
- Dzięki punktom końcowym usług wpisy systemu DNS dla usług platformy Azure są obecnie zachowywane w aktualnej postaci i będą nadal rozpoznawać adresy IP przypisane do usługi platformy Azure.

- Sieciowe grupy zabezpieczeń z punktami końcowymi usługi:
  - Domyślnie sieciowe grupy zabezpieczeń zezwalają na ruch wychodzący z Internetu, a także na ruch z sieci wirtualnej do usług platformy Azure. Ta opcja będzie nadal współdziałać z punktami końcowymi usługi w ten sposób. 
  - Jeśli chcesz odrzucić cały wychodzący ruch internetowy i zezwolić tylko na ruch do określonych usług platformy Azure, możesz to zrobić za pomocą [tagów usług](security-overview.md#service-tags) w sieciowej grupie zabezpieczeń. Obsługiwane usługi platformy Azure można określić jako miejsce docelowe w regułach sieciowych grup zabezpieczeń, a konserwacją adresów IP w obrębie każdego tagu zajmuje się platforma Azure. Aby uzyskać więcej informacji, zobacz [Azure Service tags for NSGs (Tagi usług platformy Azure dla sieciowych grup zabezpieczeń)](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scenariusze

- **Równorzędne lub połączone sieci wirtualne albo wiele sieci wirtualnych**: aby zabezpieczyć usługi platformy Azure w wielu podsieciach sieci wirtualnej lub wielu sieciach wirtualnych, możesz niezależnie włączyć punkty końcowe usługi w każdej z tych podsieci i zabezpieczyć zasoby usługi platformy Azure we wszystkich tych podsieciach.
- **Filtrowanie ruchu wychodzącego z sieci wirtualnej do usług platformy Azure**: jeśli chcesz sprawdzić lub filtrować ruch kierowany do usługi platformy Azure z sieci wirtualnej, możesz wdrożyć urządzenie sieci wirtualnej w ramach tej sieci wirtualnej. Następnie możesz zastosować punkty końcowe usługi do podsieci, w której wdrożono sieciowe urządzenie wirtualne, i zabezpieczyć zasób usługi platformy Azure tylko w tej podsieci. Ten scenariusz może być przydatny, jeśli chcesz ograniczyć dostęp do usługi platformy Azure z sieci wirtualnej tylko do określonych zasobów platformy Azure przy użyciu filtrowania sieciowych urządzeń wirtualnych. Aby uzyskać więcej informacji, zobacz [ruch wychodzący z sieciowych urządzeń wirtualnych](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Zabezpieczanie zasobów platformy Azure w usługach wdrożonych bezpośrednio do sieci wirtualnych**: różne usługi platformy Azure można wdrażać bezpośrednio w określonych podsieciach sieci wirtualnej. Zasoby usługi platformy Azure można zabezpieczyć w podsieciach [usługi zarządzanej](virtual-network-for-azure-services.md), konfigurując punkt końcowy usługi w podsieci usługi zarządzanej.
- **Ruch dyskowy z maszyny wirtualnej platformy Azure**: na ruch dyskowy maszyny wirtualnej (w tym instalowanie i odinstalowywanie oraz operacje we/wy dysku), w przypadku dysków zarządzanych/niezarządzanych, nie mają wpływu zmiany routingu punktów końcowych usługi Azure Storage. Dostęp interfejsu REST do stronicowych obiektów blob można ograniczyć do wybranych sieci za pomocą punktów końcowych usługi i [reguł sieci usługi Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Rejestrowanie i rozwiązywanie problemów

Po skonfigurowaniu punktów końcowych usługi do określonej usługi zweryfikuj obowiązywanie trasy punktu końcowego usługi przez: 
 
- Weryfikowanie źródłowego adresu IP każdego żądania obsługi w diagnostyce usługi. Wszystkie nowe żądania z punktami końcowymi usługi pokazują pozycję „źródłowy adres IP” dla żądania jako prywatny adres sieci wirtualnej przypisany do klienta wysyłającego żądanie z sieci wirtualnej. Bez punktu końcowego ten adres będzie publicznym adresem IP platformy Azure.
- Wyświetlanie skutecznych tras w dowolnym interfejsie sieciowym w podsieci. Trasa do usługi:
  - Pokazuje dokładniej określoną domyślną trasę do zakresu prefiksów adresów danej usługi.
  - Ma element nextHopType o wartości *VirtualNetworkServiceEndpoint*.
  - Wskazuje, że istnieje bardziej bezpośrednie — w porównaniu z trasami tunelowania wymuszanego — połączenie z usługą.

>[!NOTE]
> Trasy punktu końcowego usługi zastępują trasy BGP lub UDR dla dopasowania prefiksu adresu usługi platformy Azure. Dowiedz się więcej na temat [rozwiązywania problemów ze skutecznymi trasami](diagnose-network-routing-problem.md)

## <a name="provisioning"></a>Inicjowanie obsługi

Punkty końcowe usługi można niezależnie konfigurować w sieciach wirtualnych — czynność tę może wykonać użytkownik z uprawnieniami do zapisu w sieci wirtualnej. Aby zabezpieczyć zasoby usługi platformy Azure z siecią wirtualną, użytkownik musi mieć uprawnienia do *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.

Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i przypisywaniu określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure należą do różnych subskrypcji, zasoby muszą znajdować się w tej samej dzierżawie usługi Active Directory (AD). 

## <a name="pricing-and-limits"></a>Ceny i limity

Za korzystanie z punktów końcowych usługi nie są naliczane dodatkowe opłaty. Obecnie stosowany jest bieżący model cen usług platformy Azure (Azure Storage, Azure SQL Database itd.).

Nie ma limitu całkowitej liczby punktów końcowych usługi w sieci wirtualnej.

Niektóre usługi platformy Azure, takimi jak konta magazynu platformy Azure, mogą wymuszać limity liczby podsieci używanych do zabezpieczania zasobów. Szczegółowe informacje można znaleźć w dokumentacji dotyczącej różnych usług w sekcji [Następne kroki](#next-steps).

## <a name="virtual-network-service-endpoint-policies"></a>Zasady punktu końcowego usługi dla sieci wirtualnej 

Zasady punktu końcowego usługi dla sieci wirtualnej umożliwiają filtrowanie ruchu sieci wirtualnej do usług platformy Azure, zezwalając na stosowanie tylko wybranych zasobów usług platformy Azure w punktach końcowych usługi. Zasady punktu końcowego usługi zapewniają szczegółową kontrolę dostępu w przypadku ruchu w sieci wirtualnej do usług platformy Azure. Więcej informacji: [Zasady punktu końcowego usługi sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

## <a name="faqs"></a>Często zadawane pytania

Często zadawane pytania można znaleźć w temacie [Punkt końcowy usługi dla sieci wirtualnej — często zadawane pytania](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [skonfigurować punkty końcowe usługi dla sieci wirtualnej](tutorial-restrict-network-access-to-resources.md)
- Dowiedz się, jak [zabezpieczyć konto usługi Azure Storage w sieci wirtualnej](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Dowiedz się, jak [zabezpieczyć usługę Azure SQL Database w sieci wirtualnej](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Dowiedz się, jak [zabezpieczyć usługę Azure SQL Database Warehouse w sieci wirtualnej](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- Dowiedz się więcej o [integracji usług platformy Azure w sieciach wirtualnych](virtual-network-for-azure-services.md)
- Dowiedz się więcej na temat [zasad punktu końcowego usługi dla sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
-  Szybki start: [szablon usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) do konfigurowania punktu końcowego usługi w podsieci sieci wirtualnej i zabezpieczenia konta usługi Azure Storage do tej podsieci.

