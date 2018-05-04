---
title: Jak używać usługi Azure API Management z wewnętrzne sieci wirtualne | Dokumentacja firmy Microsoft
description: Dowiedz się, jak instalowanie i konfigurowanie usługi Azure API Management w wewnętrznej sieci wirtualnej
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
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: b2b690978c2d67dbf26b74ecd38a408cece91566
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Przy użyciu usługi Azure API Management z wewnętrznej sieci wirtualnej
Sieci wirtualne platformy Azure Azure API Management umożliwiają zarządzanie interfejsów API nie jest dostępny w Internecie. Liczba technologii sieci VPN są dostępne do nawiązania połączenia. Zarządzanie interfejsami API można wdrożyć w dwóch trybach głównego w sieci wirtualnej:
* Zewnętrzne
* Wewnętrzny


Gdy wdraża interfejsu API zarządzania w trybie wewnętrzna sieć wirtualna, wszystkie punkty końcowe usługi (bramy, portalu dla deweloperów, portalu Azure, bezpośrednie zarządzanie i Git) są widoczne tylko w sieci wirtualnej, która umożliwia kontrolę dostępu do. Żaden z punktów końcowych usługi nie jest zarejestrowany na publicznym serwerze DNS.

Za pomocą interfejsu API zarządzania w trybie wewnętrzny można osiągnąć następujące scenariusze:
* Należy interfejsów API hostowanych w centrum danych prywatnych bezpieczny dostęp przez osoby trzecie poza programem przy użyciu lokacja lokacja i połączeń sieci VPN platformy Azure ExpressRoute.
* Włącz scenariuszach chmur hybrydowych dzięki uwidocznieniu działania sieci opartej na chmurze interfejsów API i interfejsów API lokalnego za pośrednictwem wspólnego bramy.
* Zarządzanie swoje interfejsy API hostowanych w wielu lokalizacjach geograficznych przy użyciu punktu końcowego pojedyncza brama. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musi mieć:

+ **Aktywną subskrypcją platformy Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Wystąpienie usługi Azure API Management**. Aby uzyskać więcej informacji, zobacz [utworzenia wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Tworzenie interfejsu API zarządzania w wewnętrznej sieci wirtualnej
Usługi interfejsu API Management w wewnętrznej sieci wirtualnej jest hostowany za wewnętrznego modułu równoważenia obciążenia (ILB).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Włącz połączenie wirtualnej sieci przy użyciu portalu Azure

1. Przejdź do Twojego wystąpienia usługi Azure API Management w [portalu Azure](https://portal.azure.com/).
2. Wybierz **sieci wirtualnej**.
3. Skonfiguruj wystąpienie interfejsu API zarządzania do wdrożenia w sieci wirtualnej.

    ![Menu do konfigurowania usługi Azure API Management w wewnętrznej sieci wirtualnej][api-management-using-internal-vnet-menu]

4. Wybierz pozycję **Zapisz**.

Po wdrożeniu zakończy się powodzeniem, wewnętrznego wirtualnego adresu IP usługi powinny być widoczne na pulpicie nawigacyjnym.

![Pulpit nawigacyjny interfejsu API zarządzania z siecią wewnętrzną wirtualne skonfigurowane][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Włącz połączenie wirtualnej sieci przy użyciu poleceń cmdlet programu PowerShell
Można również włączyć łączność w sieci wirtualnej przy użyciu poleceń cmdlet programu PowerShell.

* Tworzenie usługi Zarządzanie interfejsami API w sieci wirtualnej: Użyj polecenia cmdlet [AzureRmApiManagement nowy](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) utworzenia usługi Azure API Management w sieci wirtualnej i skonfigurować go do używania typu wewnętrznej sieci wirtualnej.

* Wdrażanie istniejącej usługi Zarządzanie interfejsami API w sieci wirtualnej: Użyj polecenia cmdlet [AzureRmApiManagementDeployment aktualizacji](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) przenosić istniejącej usługi Zarządzanie interfejsami API w sieci wirtualnej i skonfigurować go do używania wewnętrznej Typ sieci wirtualnej.

## <a name="apim-dns-configuration"></a>Konfiguracja DNS
W przypadku interfejsu API zarządzania w trybie zewnętrzną sieć wirtualną, DNS jest zarządzana przez Azure. Dla trybu wewnętrznej sieci wirtualnej trzeba zarządzać własną routingu.

> [!NOTE]
> Zarządzanie interfejsami API usługi nie nasłuchuje na żądania pochodzące z adresów IP. Tylko odpowiadały na żądania na nazwę hosta skonfigurowane na jego punktów końcowych usługi. Te punkty końcowe obejmują bramy, portalu Azure i portalu dla deweloperów, bezpośrednie zarządzanie punktu końcowego i Git.

### <a name="access-on-default-host-names"></a>Dostęp do domyślnej nazwy hosta
Podczas tworzenia usługi Zarządzanie interfejsami API o nazwie "contoso", na przykład następujące punkty końcowe usługi są domyślnie skonfigurowane:

   * Brama lub serwer proxy: contoso.azure api.net

   * portalu Azure i portalu dla deweloperów: contoso.portal.azure api.net

   * Zarządzanie bezpośrednie punktu końcowego: contoso.management.azure api.net

   * Git: contoso.scm.azure-api.net

Aby uzyskać dostęp do tych punktów końcowych usługi API Management, należy utworzyć maszynę wirtualną w podsieci podłączone do sieci wirtualnej, w której jest wdrażane zarządzanie interfejsami API. Zakładając, że wewnętrznego wirtualnego adresu IP dla usługi jest 10.0.0.5, możesz mapować pliku hosts % SystemDrive%\drivers\etc\hosts, w następujący sposób:

   * 10.0.0.5 contoso.azure-api.net

   * 10.0.0.5 contoso.portal.azure-api.net

   * 10.0.0.5 contoso.management.azure-api.net

   * 10.0.0.5 contoso.scm.azure-api.net

Wszystkie punkty końcowe usługi można następnie uzyskać dostęp z poziomu maszyny wirtualnej, który został utworzony. Jeśli używasz niestandardowego serwera DNS w sieci wirtualnej, można również utworzyć rekordy A DNS i uzyskiwać dostęp do tych punktów końcowych z dowolnego miejsca w Twojej sieci wirtualnej. 

### <a name="access-on-custom-domain-names"></a>Dostęp do niestandardowych nazw domen

   1. Jeśli nie chcesz uzyskać dostęp do usługi API Management z domyślne nazwy hosta, należy skonfigurować nazwy domeny niestandardowej dla wszystkich sieci punktów końcowych usługi jak pokazano na poniższej ilustracji: 

   ![Skonfigurowanie domeny niestandardowej dla interfejsu API zarządzania][api-management-custom-domain-name]

   2. Następnie można utworzyć rekordy w punkty końcowe, które są tylko dostępny w obrębie sieci wirtualnej dostępu do serwera DNS.

## <a name="routing"> </a> Routing
+ Ze zrównoważonym obciążeniem prywatnej wirtualny adres IP z zakresu podsieci zostanie zarezerwowane i umożliwiają dostęp do punktów końcowych usługi Zarządzanie interfejsami API od w sieci wirtualnej.
+ Aby zapewnić dostęp do punktu końcowego usługi zarządzania tylko za pośrednictwem portu 3443 rezerwowane również ze zrównoważonym obciążeniem publiczny adres IP (VIP).
+ Adres IP z zakresu podsieci IP (DIP) będzie umożliwiać dostęp do zasobów w sieci wirtualnej, a publicznego adresu IP (VIP) będzie używany do dostępu do zasobów poza siecią wirtualną.
+ O zrównoważonym obciążeniu publicznych i prywatnych adresów IP można znaleźć w bloku Przegląd/Essentials w portalu Azure.

## <a name="related-content"> </a>Zawartość pokrewna
Aby dowiedzieć się więcej, zobacz następujące artykuły:
* [Typowe problemy z konfiguracją sieci podczas konfigurowania usługi Azure API Management w sieci wirtualnej][Common network configuration problems]
* [Sieć wirtualna — często zadawane pytania](../virtual-network/virtual-networks-faq.md)
* [Utworzenie rekordu DNS](https://msdn.microsoft.com/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

