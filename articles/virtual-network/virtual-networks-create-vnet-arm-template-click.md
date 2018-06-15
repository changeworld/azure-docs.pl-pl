---
title: Tworzenie sieci wirtualnej | Szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną przy użyciu szablonu usługi Azure Resource Manager.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb096f64a6bc41ad2e75c058c7a9f00bbe480207
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880549"
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Utwórz sieć wirtualną przy użyciu szablonu usługi Azure Resource Manager

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Platforma Azure ma dwa modele wdrażania: usługa Azure Resource Manager i wersja klasyczna. Firma Microsoft zaleca tworzenie zasobów za pomocą modelu wdrożenia usługi Resource Manager. Aby dowiedzieć się więcej o różnicach między dwoma modelami, zapoznaj się z artykułem [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Informacje na temat modeli wdrażania platformy Azure).
 
W tym artykule wyjaśniono, jak utworzyć sieć wirtualną przy użyciu modelu wdrażania usługi Resource Manager przy użyciu szablonu usługi Azure Resource Manager. Sieć wirtualną można również utworzyć w usłudze Resource Manager przy użyciu innych narzędzi albo za pośrednictwem klasycznego modelu wdrożenia, wybierając inną opcję z poniższej listy:

> [!div class="op_single_selector"]
- [Portal](quick-create-portal.md)
- [Program PowerShell](quick-create-powershell.md)
- [Interfejs wiersza polecenia](quick-create-cli.md)
- [Szablon](virtual-networks-create-vnet-arm-template-click.md)
- [Portal (klasyczny)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (klasyczny)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Interfejs wiersza polecenia (klasyczny)](virtual-networks-create-vnet-classic-cli.md)

Dowiedz się, jak pobrać i zmodyfikować i istniejącego szablonu usługi Azure Resource Manager z usługi GitHub i wdrożyć szablon przy użyciu serwisu GitHub, programu PowerShell i interfejsu wiersza polecenia Azure.

Jeśli po prostu wdrażasz szablon usługi Azure Resource Manager bezpośrednio z serwisu GitHub, bez wprowadzania żadnych zmian, przejdź do [wdrażania szablonu z serwisu github](#deploy-the-arm-template-by-using-click-to-deploy).

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Pobieranie szablonu usługi Azure Resource Manager i zapoznawanie się z nim
Możesz pobrać istniejący szablon do tworzenia sieci wirtualnej z dwoma podsieciami z serwisu GitHub, zmiany mogą, a następnie użyć go ponownie. Aby to zrobić, wykonaj następujące kroki:

1. Przejdź do [strony przykładowego szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Kliknij opcję **azuredeploy.json**, a następnie kliknij opcję **RAW**.
3. Zapisz plik do folderu lokalnego na komputerze.
4. Jeśli znasz szablony, przejdź do kroku 7.
5. Otwórz zapisany plik i przyjrzyj się zawartości sekcji **parametry** w wierszu 5. Parametry szablonu usługi Azure Resource Manager zawierają symbole zastępcze wartości, które można wypełnić podczas wdrażania.
   
   | Parametr | Opis |
   | --- | --- |
   | **vnetName** |Nazwa nowej sieci wirtualnej |
   | **addressPrefix** |Przestrzeń adresowa sieci wirtualnej w formacie CIDR |
   | **subnet1Name** |Nazwa pierwszej sieci wirtualnej |
   | **subnet1Prefix** |Blok CIDR pierwszej podsieci |
   | **subnet2Name** |Nazwa drugiej sieci wirtualnej |
   | **subnet2Prefix** |Blok CIDR drugiej podsieci |
   
   > [!IMPORTANT]
   > Szablony usługi Azure Resource Manger utrzymywane w usłudze GitHub mogą z upływem czasu ulec zmianie. Przed użyciem szablonu należy go sprawdzić.
   > 
   > 
6. Sprawdź zawartość w obszarze **resources**, w szczególności następujące pozycje:
   
   * **type**. Typ zasobu tworzonego przez szablon. W tym przypadku jest to **Microsoft.Network/virtualNetworks** reprezentujący sieć wirtualną.
   * **name**. Nazwa zasobu. Zwróć uwagę na **[parameters('vnetName')]**, co oznacza, że nazwa podana jako dane wejściowe użytkownika lub pliku parametrów podczas wdrażania.
   * **properties**. Lista właściwości zasobu. Ten szablon używa właściwości przestrzeni adresowej i podsieci podczas tworzenia sieci wirtualnej.
7. Przejdź z powrotem do [strony przykładowego szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Kliknij opcję **azuredeploy-paremeters.json**, a następnie kliknij opcję **RAW**.
9. Zapisz plik do folderu lokalnego na komputerze.
10. Otwórz zapisany plik i dokonaj edycji wartości parametrów. Aby wdrożyć opisany w scenariuszu sieci wirtualnej, wykonaj następujące wartości poniżej:

    ```json
        {
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. Zapisz plik.


## <a name="deploy-the-template-using-powershell"></a>Wdrażanie szablonu przy użyciu programu PowerShell

Wykonaj poniższe kroki, aby wdrożyć szablon został pobrany przy użyciu programu PowerShell:

1. Instalowanie i konfigurowanie programu Azure PowerShell, wykonując kroki opisane w [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) artykułu.
2. Uruchom poniższe polecenie, aby utworzyć nową grupę zasobów:

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    Polecenie tworzy grupę zasobów o nazwie *TestRG* w *środkowe stany USA* region platformy azure. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    Oczekiwane dane wyjściowe:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. Uruchom następujące polecenie, aby wdrożyć nową sieć wirtualną przy użyciu szablonu i parametr plików pobranego i zmodyfikowanego:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    Oczekiwane dane wyjściowe:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. Uruchom następujące polecenie, aby wyświetlić właściwości nowej sieci wirtualnej:

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    Oczekiwane dane wyjściowe:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>Wdrażanie szablonu przy użyciu kliknij wdrażania

Można ponownie użyć wstępnie zdefiniowanych szablonów usługi Azure Resource Manager przekazany do obsługiwanego przez firmę Microsoft repozytorium GitHub i dla społeczności. Szablony te można wdrożyć bezpośrednio z witryny GitHub, lub pobrać i zmodyfikować odpowiednio do potrzeb. Aby wdrożyć szablon, który pozwala utworzyć sieć wirtualną z dwiema podsieciami, wykonaj następujące kroki:

1. W przeglądarce przejdź do strony [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Przewiń w dół listę szablonów, a następnie kliknij opcję **101-vnet-two-subnets**. Zapoznaj się z plikiem **README.md**, jak pokazano poniżej.

    ![Plik READEME.md w witrynie github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Kliknij przycisk **Wdrażaj na platformie Azure**. W razie potrzeby wprowadź poświadczenia logowania do platformy Azure. 
4. W bloku **Parametry** wprowadź wartości, których chcesz użyć w celu utworzenia nowej sieci wirtualnej, a następnie kliknij opcję **OK**. Na poniższej ilustracji przedstawiono wartości dla tego scenariusza:
   
    ![Parametry szablonu ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. Kliknij opcję **Grupa zasobów** i wybierz grupę zasobów, aby dodać do niej sieć wirtualną, lub kliknij opcję **Utwórz nową**, aby dodać sieć wirtualną do nowej grupy zasobów. Na poniższej ilustracji przedstawiono zasobu ustawienia grupy dla nowej grupy zasobów o nazwie **TestRG**:

    ![Grupa zasobów](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. W razie potrzeby zmień wartości ustawień **Subskrypcja** i **Lokalizacja** sieci wirtualnej.
7. Jeśli nie chcesz, aby sieć wirtualna była wyświetlana jako kafelek na **tablicy startowej**, wyłącz opcję **Przypnij do tablicy startowej**.
8. Kliknij przycisk **postanowienia prawne**, przeczytaj warunki i kliknij przycisk **kupić** do wyrażenia zgody. 
9. Kliknij pozycję **Utwórz**, aby utworzyć sieć wirtualną.
   
    ![Przesyłanie kafelka wdrażania w portalu w wersji zapoznawczej](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. Po zakończeniu wdrażania, w portalu Azure kliknij **wszystkie usługi**, typ *sieci wirtualnych* w wyświetlonym oknie filtru kliknięcie sieci wirtualnych, aby zobaczyć bloku sieci wirtualnej. W bloku, kliknij *TestVNet*. W *TestVNet* bloku, kliknij przycisk **podsieci** wyświetlić utworzony podsieci, jak pokazano na poniższej ilustracji:
    
     ![Tworzenie sieci wirtualnej w portalu w wersji zapoznawczej](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak połączyć:

- Maszynę wirtualną z siecią wirtualną. Odpowiednie informacje znajdziesz w artykułach [Tworzenie maszyny wirtualnej z systemem Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) lub [Tworzenie maszyny wirtualnej z systemem Linux](../virtual-machines/linux/quick-create-portal.md). Zamiast tworzyć sieć wirtualną i podsieć, wykonując kroki opisane w artykułach, można wybrać istniejącą sieć wirtualną i podsieć, z którymi zostanie połączona maszyna wirtualna.
- Sieć wirtualną z innymi sieciami wirtualnymi. Odpowiednie informacje możesz znaleźć w artykule [Łączenie sieci wirtualnych](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Sieć wirtualną z siecią lokalną za pomocą prywatnej sieci wirtualnej (VPN) typu lokacja-lokacja lub obwodu usługi ExpressRoute. Odpowiednie informacje znajdziesz w artykułach [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Łączenie sieci wirtualnej z siecią lokalną za pomocą sieci VPN typu lokacja-lokacja) i [Link a VNet to an ExpressRoute circuit](../expressroute/expressroute-howto-linkvnet-arm.md) (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute).
