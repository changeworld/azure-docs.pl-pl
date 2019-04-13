---
title: Jak używać usługi Azure API Management przy użyciu wewnętrznej sieci wirtualnych | Dokumentacja firmy Microsoft
description: Informacje o sposobie instalowania i konfigurowania usługi Azure API Management w wewnętrznej sieci wirtualnej
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 738dd451ca44801c799d793e6501c547655bd0e0
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524153"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Przy użyciu usługi Azure API Management z wewnętrzną siecią wirtualną
Sieci wirtualnych platformy Azure usługi Azure API Management umożliwia zarządzanie interfejsami API nie jest dostępny w Internecie. Wiele technologii sieci VPN są dostępne do nawiązania połączenia. Usługa API Management można wdrożyć w dwa główne tryby wewnątrz sieci wirtualnej:
* Zewnętrzna
* Wewnętrzny

Podczas wdrażania usługi API Management w trybie wewnętrznej sieci wirtualnej, wszystkie punkty końcowe usługi (bramy, portal dla deweloperów, witryny Azure portal, zarządzanie bezpośrednie i Git) są widoczne tylko wewnątrz sieci wirtualnej, która umożliwia kontrolę dostępu do. Brak punktów końcowych usługi są rejestrowane na publicznym serwerze DNS.

Za pomocą usługi API Management w trybie wewnętrznego, można osiągnąć następujące scenariusze:

* Należy interfejsów API hostowanych w centrum danych prywatnych bezpiecznego zapewniania dostępu przez strony trzecie poza nim przy użyciu lokacja lokacja lub połączenia sieci VPN usługi ExpressRoute platformy Azure.
* Udostępnianie Twoje interfejsy API oparte na chmurze i lokalnymi interfejsami API za pomocą wspólnej bramy, aby włączyć scenariuszy hybrydowych w chmurze.
* Zarządzanie interfejsami API hostowanych w wielu lokalizacjach geograficznych przy użyciu punktu końcowego jednej bramy.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

+ **Aktywna subskrypcja platformy Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Wystąpienie usługi Azure API Management**. Aby uzyskać więcej informacji, zobacz [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).
+ Po wdrożeniu usługi API Management w sieci wirtualnej, [listę portów](./api-management-using-with-vnet.md#required-ports) są używane i muszą być otwarte. 

## <a name="enable-vpn"> </a>Tworzenie usługi API Management w wewnętrznej sieci wirtualnej
Usługa API Management w wewnętrznej sieci wirtualnej znajduje się za zaporą [wewnętrznego modułu równoważenia obciążenia (klasyczny)](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). To jest jedyna dostępna opcja i nie można jej zmienić.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Włączanie połączenia z siecią wirtualną przy użyciu witryny Azure portal

1. Przejdź do swojego wystąpienia usługi Azure API Management w [witryny Azure portal](https://portal.azure.com/).
2. Wybierz pozycję **Sieć wirtualna**.
3. Konfigurowanie wystąpienia usługi API Management, można wdrożyć w sieci wirtualnej.

    ![Menu do konfigurowania usługi Azure API Management w wewnętrznej sieci wirtualnej][api-management-using-internal-vnet-menu]

4. Wybierz pozycję **Zapisz**.

Po pomyślnym zakończeniu wdrożenia powinien zostać wyświetlony **prywatnej** wirtualnego adresu IP i **publicznych** wirtualnego adresu IP w bloku przeglądu usługi API Management. **Prywatnej** wirtualny adres IP jest obciążenia o zrównoważonym obciążeniu adresu IP z w ramach usługi API Management delegowane podsieci, nad którym `gateway`, `portal`, `management` i `scm` punkty końcowe można uzyskać dostęp. **Publicznych** wirtualny adres IP jest używany **tylko** ruchu płaszczyznę kontroli dla `management` punkt końcowy ponad portu 3443 i może być zablokowany w dół do [ApiManagement] [ ServiceTags] servicetag.

![Pulpit nawigacyjny usługi API Management, z wewnętrzną siecią wirtualną skonfigurowane][api-management-internal-vnet-dashboard]

> [!NOTE]
> Dostępne w witrynie Azure Portal konsoli testów nie będzie działać dla **wewnętrzne** sieci Wirtualnej wdrożono usługę, jak adres Url bramy nie jest zarejestrowany na publicznym serwerze DNS. Zamiast tego należy używać konsoli testów na **portalu dla deweloperów**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Włączanie połączenia sieci wirtualnej przy użyciu poleceń cmdlet programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Połączenie sieci wirtualnej można również włączyć przy użyciu poleceń cmdlet programu PowerShell.

* Tworzenie usługi API Management w sieci wirtualnej: Użyj polecenia cmdlet [New AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) Tworzenie usługi Azure API Management w sieci wirtualnej i skonfiguruj ją, aby użyć typu wewnętrznej sieci wirtualnej.

* Aktualizacja istniejącego wdrożenia usługi API Management w sieci wirtualnej: Użyj polecenia cmdlet [AzApiManagementRegion aktualizacji](/powershell/module/az.apimanagement/update-azapimanagementregion) Aby przenieść istniejącą usługę API Management w sieci wirtualnej i skonfiguruj ją, aby użyć typu wewnętrznej sieci wirtualnej.

## <a name="apim-dns-configuration"></a>Konfiguracja DNS
Gdy usługa API Management jest w trybie zewnętrzną sieć wirtualną, DNS jest zarządzane przez platformę Azure. Dla trybu wewnętrznej sieci wirtualnej, które istnieje konieczność zarządzania własnych routingu.

> [!NOTE]
> Usługa API Management nie będzie nasłuchiwać żądań pochodzących z adresów IP. Odpowiadały na żądania dla nazwy hosta skonfigurowane na jego punktów końcowych usługi. Te punkty końcowe obejmują bramy, witryny Azure portal i portalu dla deweloperów, punkt końcowy zarządzania bezpośredniego i Git.

### <a name="access-on-default-host-names"></a>Dostęp do domyślnej nazwy hostów
Podczas tworzenia usługi API Management, o nazwie "contosointernalvnet", na przykład, następujące punkty końcowe usługi są konfigurowane domyślnie:

   * Brama lub serwer proxy: contosointernalvnet.azure-api.net.

   * Witryna Azure portal i portalu dla deweloperów: contosointernalvnet.portal.azure-api.net.

   * Punkt końcowy zarządzania bezpośredniego: contosointernalvnet.management.azure-api.net.

   * Git: contosointernalvnet.scm.azure-api.net.

Aby uzyskać dostęp do tych punktów końcowych usługi API Management, można utworzyć maszynę wirtualną w podsieci podłączone do sieci wirtualnej, w której jest wdrażany usługi API Management. Przy założeniu, że wewnętrznego wirtualnego adresu IP dla usługi jest 10.1.0.5, można mapować pliku hosts % SystemDrive%\drivers\etc\hosts, w następujący sposób:

   * 10.1.0.5 contosointernalvnet.azure-api.net.

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net.

   * 10.1.0.5 contosointernalvnet.management.azure-api.net.

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net.

Mogą uzyskiwać dostęp do wszystkich punktów końcowych usługi z maszyny wirtualnej, który został utworzony.
Jeśli używasz niestandardowego serwera DNS w sieci wirtualnej, możesz również utworzyć rekordy DNS i uzyskać dostęp do tych punktów końcowych z dowolnego miejsca w sieci wirtualnej.

### <a name="access-on-custom-domain-names"></a>Dostęp do niestandardowych nazw domen

1. Jeśli nie chcesz uzyskać dostęp do usługi API Management z domyślnymi nazwami hostów, możesz skonfigurować niestandardowe nazwy domen dla wszystkich punktów końcowych usługi jak pokazano na poniższej ilustracji:

   ![Konfigurowanie domeny niestandardowej dla usługi API Management][api-management-custom-domain-name]

2. Następnie możesz utworzyć rekordy na serwerze DNS w celu dostępu do punktów końcowych, które są dostępne jedynie z w ramach sieci wirtualnej.

## <a name="routing"> </a> Routing
+ Równoważenia obciążenia prywatny wirtualny adres IP z zakresu podsieci zostanie zarezerwowane i umożliwiają dostęp do punktów końcowych usługi API Management z w obrębie sieci wirtualnej.
+ Aby zapewnić dostęp do punktu końcowego usługi zarządzania tylko za pośrednictwem portu 3443 rezerwowane również równoważenia obciążenia publiczny adres IP (VIP).
+ Adres IP z zakresu adresów IP podsieci (DIP) będzie służyć do dostępu do zasobów w sieci wirtualnej i publicznego adresu IP (VIP) będzie służyć do dostępu do zasobów spoza sieci wirtualnej.
+ Ze zrównoważonym obciążeniem: publiczne i prywatne adresy IP można znaleźć w bloku Przegląd/Essentials w witrynie Azure portal.

## <a name="related-content"> </a>Powiązana zawartość
Aby dowiedzieć się więcej, zobacz następujące artykuły:
* [Typowe problemy z konfiguracją sieci podczas konfigurowania usługi Azure API Management w sieci wirtualnej][Common network configuration problems]
* [Sieć wirtualna — często zadawane pytania](../virtual-network/virtual-networks-faq.md)
* [Utworzenie rekordu DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

