---
title: Korzystanie z usługi Azure API Management z wewnętrznymi sieciami wirtualnymi
titleSuffix: Azure API Management
description: Dowiedz się, jak skonfigurować i skonfigurować usługę Azure API Management w wewnętrznej sieci wirtualnej
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: apimpm
ms.openlocfilehash: 6054c595bca26dc2a0432c53369a60a61e3efde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841867"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Korzystanie z usługi Azure API Management z wewnętrzną siecią wirtualną
Dzięki sieciom wirtualnym platformy Azure usługa Azure API Management może zarządzać interfejsami API niedostępnymi w Internecie. Do nawiązywać połączenie dostępnych jest wiele technologii sieci VPN. Zarządzanie interfejsami API można wdrożyć w dwóch głównych trybach w sieci wirtualnej:
* Zewnętrzna
* Wewnętrzny

Gdy usługa API Management jest wdrażana w wewnętrznym trybie sieci wirtualnej, wszystkie punkty końcowe usługi (brama serwera proxy, portal dewelopera, zarządzanie bezpośrednie i Git) są widoczne tylko w sieci wirtualnej, do której można kontrolować dostęp. Żaden z punktów końcowych usługi nie są zarejestrowane na publicznym serwerze DNS.

> [!NOTE]
> Ponieważ nie ma żadnych wpisów DNS dla punktów końcowych usługi, te punkty końcowe nie będą dostępne, dopóki [usługa DNS nie zostanie skonfigurowana](#apim-dns-configuration) dla sieci wirtualnej.

Korzystając z zarządzania interfejsami API w trybie wewnętrznym, można osiągnąć następujące scenariusze:

* Udostępnij interfejsy API hostowane w prywatnym centrum danych bezpiecznie dostępne dla innych firm spoza niego przy użyciu połączeń sieci VPN typu lokacja-lokacja lub usługi Azure ExpressRoute.
* Włącz scenariusze chmury hybrydowej, uwidaczniając interfejsy API oparte na chmurze i lokalne interfejsy API za pośrednictwem wspólnej bramy.
* Zarządzanie interfejsami API hostowanymi w wielu lokalizacjach geograficznych przy użyciu jednego punktu końcowego bramy.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

+ **Aktywna subskrypcja platformy Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Wystąpienie usługi Azure API Management**. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Gdy usługa api Management jest wdrażana w sieci wirtualnej, [lista portów](./api-management-using-with-vnet.md#required-ports) są używane i muszą zostać otwarte. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Tworzenie zarządzania interfejsami API w wewnętrznej sieci wirtualnej
Usługa api Management w wewnętrznej sieci wirtualnej jest hostowana za [wewnętrznym modułem równoważenia obciążenia (klasycznym).](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud) Jest to jedyna dostępna opcja, która nie może zostać zmieniona.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Włączanie połączenia sieci wirtualnej przy użyciu portalu Azure

1. Przejdź do wystąpienia usługi Azure API Management w [witrynie Azure portal](https://portal.azure.com/).
2. Wybierz **sieć wirtualną**.
3. Skonfiguruj wystąpienie zarządzania interfejsami API do wdrożenia w sieci wirtualnej.

    ![Menu konfigurowania usługi Azure API Management w wewnętrznej sieci wirtualnej][api-management-using-internal-vnet-menu]

4. Wybierz **pozycję Zapisz**.

Po pomyślnym wdrożeniu powinien zostać wyświetlony **prywatny** wirtualny adres IP i **publiczny** wirtualny adres IP usługi api Management w bloku przeglądu. **Prywatny** wirtualny adres IP jest zbilansowanym obciążeniem adresem `gateway`IP `portal` `management` z `scm` poziomu delegowanej podsieci Api Management, za którą można uzyskać dostęp do punktów końcowych. **Publiczny** wirtualny adres IP jest używany `management` **tylko** dla ruchu płaszczyzny sterowania do punktu końcowego nad portem 3443 i może być zablokowany na tagu usługi [ApiManagement.][ServiceTags]

![Pulpit nawigacyjny zarządzania interfejsem API ze skonfigurowaną wewnętrzną siecią wirtualną][api-management-internal-vnet-dashboard]

> [!NOTE]
> Konsola testowa dostępna w witrynie Azure Portal nie będzie działać **w** przypadku wdrożonej wewnętrznej sieci wirtualnej, ponieważ adres URL bramy nie jest zarejestrowany w publicznym systemie DNS. Zamiast tego należy użyć konsoli testowej dostępnej w **portalu dla deweloperów.**

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Włączanie połączenia sieci wirtualnej przy użyciu poleceń cmdlet programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Łączność sieci wirtualnej można również włączyć przy użyciu poleceń cmdlet programu PowerShell.

* Utwórz usługę zarządzania interfejsami API w sieci wirtualnej: Użyj polecenia cmdlet [New-AzApiManagement,](/powershell/module/az.apimanagement/new-azapimanagement) aby utworzyć usługę Azure API Management w sieci wirtualnej i skonfigurować ją do używania wewnętrznego typu sieci wirtualnej.

* Aktualizowanie istniejącego wdrożenia usługi zarządzania interfejsami API w sieci wirtualnej: użyj polecenia cmdlet [Update-AzApiManagementRegion,](/powershell/module/az.apimanagement/update-azapimanagementregion) aby przenieść istniejącą usługę zarządzania interfejsami API wewnątrz sieci wirtualnej i skonfigurować ją do używania wewnętrznego typu sieci wirtualnej.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Konfiguracja DNS
Gdy usługa Api Management jest w trybie zewnętrznej sieci wirtualnej, usługa DNS jest zarządzana przez platformę Azure. W przypadku wewnętrznego trybu sieci wirtualnej musisz zarządzać własnym routingu.

> [!NOTE]
> Usługa API Management nie nasłuchuje żądań pochodzących z adresów IP. Odpowiada tylko na żądania do nazwy hosta skonfigurowane w punktach końcowych usługi. Te punkty końcowe obejmują bramę, portal Azure i portal dla deweloperów, punkt końcowy zarządzania bezpośredniego i git.

### <a name="access-on-default-host-names"></a>Dostęp do domyślnych nazw hostów
Podczas tworzenia usługi api Management, o nazwie "contosointernalvnet" na przykład następujące punkty końcowe usługi są konfigurowane domyślnie:

   * Brama lub serwer proxy: contosointernalvnet.azure-api.net

   * Portal dla deweloperów: contosointernalvnet.portal.azure-api.net

   * Nowy portal dla deweloperów: contosointernalvnet.developer.azure-api.net

   * Bezpośredni punkt końcowy zarządzania: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Aby uzyskać dostęp do tych punktów końcowych usługi zarządzania interfejsami API, można utworzyć maszynę wirtualną w podsieci podłączonej do sieci wirtualnej, w której wdrożono usługę Api Management. Zakładając, że wewnętrzny wirtualny adres IP usługi wynosi 10.1.0.5, można mapować plik hosts, %SystemDrive%\drivers\etc\hosts, w następujący sposób:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Następnie można uzyskać dostęp do wszystkich punktów końcowych usługi z maszyny wirtualnej, który został utworzony.
Jeśli używasz niestandardowego serwera DNS w sieci wirtualnej, można również utworzyć rekordy DNS i uzyskać dostęp do tych punktów końcowych z dowolnego miejsca w sieci wirtualnej.

### <a name="access-on-custom-domain-names"></a>Dostęp do niestandardowych nazw domen

1. Jeśli nie chcesz uzyskiwać dostępu do usługi Api Management z domyślnymi nazwami hostów, możesz skonfigurować niestandardowe nazwy domen dla wszystkich punktów końcowych usługi, jak pokazano na poniższej ilustracji:

   ![Konfigurowanie domeny niestandardowej do zarządzania interfejsami API][api-management-custom-domain-name]

2. Następnie można utworzyć rekordy na serwerze DNS, aby uzyskać dostęp do punktów końcowych, które są dostępne tylko z poziomu sieci wirtualnej.

## <a name="routing"></a><a name="routing"> </a> Routing

* Zbilansowy *prywatny* wirtualny adres IP z zakresu podsieci zostanie zarezerwowany i używany do uzyskiwania dostępu do punktów końcowych usługi API Management z poziomu sieci wirtualnej. Ten *prywatny* adres IP można znaleźć w bloku Przegląd dla usługi w witrynie Azure portal. Ten adres musi być zarejestrowany na serwerach DNS używanych przez sieć wirtualną.
* *Publiczny* adres IP (VIP) z równoważenia obciążenia zostanie również zarezerwowany w celu zapewnienia dostępu do punktu końcowego usługi zarządzania nad portem 3443. Ten *publiczny* adres IP można znaleźć w bloku Przegląd dla usługi w witrynie Azure portal. *Publiczny* adres IP jest używany tylko dla `management` ruchu płaszczyzny sterowania do punktu końcowego nad portem 3443 i może być zablokowany na tagu usługi [ApiManagement.][ServiceTags]
* Adresy IP z zakresu adresów IP podsieci (DIP) zostaną przypisane do każdej maszyny Wirtualnej w usłudze i będą używane do uzyskiwania dostępu do zasobów w sieci wirtualnej. Publiczny adres IP (VIP) będzie używany do uzyskiwania dostępu do zasobów poza siecią wirtualną. Jeśli listy ograniczeń IP są używane do zabezpieczania zasobów w sieci wirtualnej, cały zakres podsieci, w której wdrożona jest usługa api Management, musi być określony w celu udzielenia lub ograniczenia dostępu z usługi.
* Równoważenie obciążenia publicznych i prywatnych adresów IP można znaleźć w bloku Przegląd w witrynie Azure portal.
* Adresy IP przypisane do dostępu publicznego i prywatnego mogą ulec zmianie, jeśli usługa zostanie usunięta, a następnie dodana z powrotem do sieci wirtualnej. W takim przypadku może być konieczne zaktualizowanie rejestracji DNS, reguł routingu i list ograniczeń IP w sieci wirtualnej.

## <a name="related-content"></a><a name="related-content"> </a>Powiązane treści
Aby dowiedzieć się więcej, zobacz następujące artykuły:
* [Typowe problemy z konfiguracją sieci podczas konfigurowania usługi Azure API Management w sieci wirtualnej][Common network configuration problems]
* [Najczęściej zadawane pytania dotyczące sieci wirtualnej](../virtual-network/virtual-networks-faq.md)
* [Tworzenie rekordu w systemie DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

