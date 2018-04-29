---
title: Utwórz bramę aplikacji Azure — szablony | Dokumentacja firmy Microsoft
description: Ta strona zawiera instrukcje dotyczące tworzenia bramy aplikacji platformy Azure za pomocą szablonu usługi Azure Resource Manager
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 422ca1d3d3dce07926f361045777701674fa68c4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Tworzenie bramy aplikacji przy użyciu szablonu usługi Azure Resource Manager

Usługa Azure Application Gateway to moduł równoważenia obciążenia warstwy 7. Udostępnia tryb failover oraz oparty na wydajności routing żądań HTTP między różnymi serwerami — w chmurze i lokalnymi. Usługa Application Gateway zapewnia wiele funkcji kontrolera dostarczania aplikacji (ADC, Application Delivery Controller), w tym między innymi równoważenie obciążenia HTTP, koligację sesji na podstawie plików cookie, odciążanie protokołu Secure Sockets Layer (SSL), niestandardowe sondy kondycji i obsługę wielu witryn. Pełną listę obsługiwanych funkcji można znaleźć [omówienie bramy aplikacji](overview.md)

W tym artykule przedstawiono sposób pobierania i modyfikowanie istniejących [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) z usługi GitHub i wdrażania szablonu z serwisu GitHub, programu PowerShell i interfejsu wiersza polecenia Azure.

Jeśli po prostu wdrażasz szablon bezpośrednio z serwisu GitHub, bez wprowadzania żadnych zmian, przejdź do wdrażania szablonu z serwisu GitHub.

## <a name="scenario"></a>Scenariusz

W ramach tego scenariusza wykonasz następujące czynności:

* Utwórz bramę aplikacji z zapory aplikacji sieci web.
* Tworzenie sieci wirtualnej o nazwie VirtualNetwork1 przy użyciu zarezerwowanego bloku CIDR 10.0.0.0/16.
* Tworzenie podsieci o nazwie Appgatewaysubnet używającej bloku 10.0.0.0/28 jako bloku CIDR.
* Ustawianie dwóch wcześniej skonfigurowanych adresów IP zaplecza dla serwerów sieci Web, w przypadku których chcesz równoważyć obciążenie ruchem. W tym przykładzie szablonu adresy IP zaplecza to 10.0.1.10 i 10.0.1.11.

> [!NOTE]
> Te ustawienia to parametry tego szablonu. Aby dostosować szablon, można zmienić zasady, odbiornika protokołu SSL i inne opcje w plik azuredeploy.json.

![Scenariusz](./media/create-vmss-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Pobieranie szablonu usługi Azure Resource Manager i zapoznawanie się z nim

Z witryny GitHub można pobrać istniejący szablon usługi Azure Resource Manager umożliwiający utworzenie sieci wirtualnej z dwoma podsieciami, wprowadzić wybrane zmiany, a następnie ponownie go użyć. Aby to zrobić, wykonaj następujące kroki:

1. Przejdź do [Utwórz bramę aplikacji włączona jest Zapora aplikacji sieci web](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-waf).
1. Kliknij opcję **azuredeploy.json**, a następnie kliknij opcję **RAW**.
1. Zapisz plik w folderze lokalnym na komputerze.
1. Jeśli znasz szablony usługi Azure Resource Manager, przejdź do kroku 7.
1. Otwórz zapisany plik i przyjrzyj się zawartości sekcji **parametry** w wierszu
1. Parametry szablonu usługi Azure Resource Manager zawierają symbole zastępcze wartości, które można wypełnić podczas wdrażania.

  | Parametr | Opis |
  | --- | --- |
  | **subnetPrefix** |Blok CIDR podsieci bramy aplikacji. |
  | **applicationGatewaySize** | Rozmiar bramy aplikacji.  Zapory aplikacji sieci Web udostępnia tylko dla średnich i dużych. |
  | **backendIpaddress1** |Adres IP pierwszego serwera sieci web. |
  | **backendIpaddress2** |Adres IP drugiego serwera sieci web. |
  | **wafEnabled** | Ustawienie, aby określić, czy zapory aplikacji sieci Web jest włączony.|
  | **wafMode** | Tryb zapory aplikacji sieci web.  Dostępne są następujące opcje **zapobiegania** lub **wykrywania**.|
  | **wafRuleSetType** | Typ zestaw reguł zapory aplikacji sieci Web.  OWASP jest obecnie jedyną obsługiwaną opcją. |
  | **wafRuleSetVersion** |Wersja zestaw reguł. CRS OWASP 2.2.9 i 3.0 są obecnie obsługiwane opcje. |

1. Sprawdź zawartość w obszarze **zasobów** i zwróć uwagę, następujące właściwości:

   * **type**. Typ zasobu tworzonego przez szablon. W tym przypadku jest typ `Microsoft.Network/applicationGateways`, który reprezentuje bramę aplikacji.
   * **name**. Nazwa zasobu. Zwróć uwagę na `[parameters('applicationGatewayName')]`, co oznacza, że nazwa jest podana jako dane wejściowe przez Ciebie lub pliku parametrów podczas wdrażania.
   * **properties**. Lista właściwości zasobu. Ten szablon korzysta z sieci wirtualnej i publicznego adresu IP podczas tworzenia aplikacji bramy.

1. Przejdź z powrotem do [ https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf/ ](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf).
1. Kliknij przycisk **parameters.JSON następującym kodem azuredeploy**, a następnie kliknij przycisk **RAW**.
1. Zapisz plik w folderze lokalnym na komputerze.
1. Otwórz zapisany plik i edytuj wartości parametrów. Do wdrożenia opisanej w scenariuszu bramy aplikacji użyj poniższych wartości.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "addressPrefix": {
            "value": "10.0.0.0/16"
            },
            "subnetPrefix": {
            "value": "10.0.0.0/28"
            },
            "applicationGatewaySize": {
            "value": "WAF_Medium"
            },
            "capacity": {
            "value": 2
            },
            "backendIpAddress1": {
            "value": "10.0.1.10"
            },
            "backendIpAddress2": {
            "value": "10.0.1.11"
            },
            "wafEnabled": {
            "value": true
            },
            "wafMode": {
            "value": "Detection"
            },
            "wafRuleSetType": {
            "value": "OWASP"
            },
            "wafRuleSetVersion": {
            "value": "3.0"
            }
        }
    }
    ```

1. Zapisz plik. Szablon JSON i szablon parametrów można przetestować za pomocą narzędzi weryfikacji danych JSON w trybie online, takich jak [JSlint.com](http://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Wdrażanie szablonu usługi Azure Resource Manager przy użyciu programu PowerShell

Jeśli nie znasz programu Azure PowerShell, odwiedź stronę: [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) i postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure i wyboru subskrypcji.

1. Logowanie do programu PowerShell

    ```powershell
    Login-AzureRmAccount
    ```

1. Sprawdź subskrypcje dostępne na koncie.

    ```powershell
    Get-AzureRmSubscription
    ```

    Zostanie wyświetlony monit o uwierzytelnienie przy użyciu własnych poświadczeń.

1. Wybierz subskrypcję platformy Azure do użycia.

    ```powershell
    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
    ```

1. W razie potrzeby utwórz grupę zasobów za pomocą polecenia cmdlet **New-AzureResourceGroup**. W poniższym przykładzie należy utworzyć grupę zasobów o nazwie AppgatewayRG w lokalizacji wschodnie stany USA.

    ```powershell
    New-AzureRmResourceGroup -Name AppgatewayRG -Location "West US"
    ```

1. Uruchom polecenie cmdlet **New-AzureRmResourceGroupDeployment**, aby wdrożyć nową sieć wirtualną przy użyciu uprzednio pobranych i zmodyfikowanych plików szablonu oraz parametrów.
    
    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Wdrażanie szablonu usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Azure Resource Manager, który został pobrany przy użyciu wiersza polecenia platformy Azure, wykonaj następujące czynności:

1. Jeśli po raz pierwszy używasz interfejsu wiersza polecenia platformy Azure, zobacz artykuł [Install and configure the Azure CLI](/cli/azure/install-azure-cli) (Instalowanie i konfigurowanie interfejsu wiersza polecenia Azure) i postępuj zgodnie z instrukcjami aż do momentu wybrania konta i subskrypcji platformy Azure.

1. W razie potrzeby uruchom `az group create` polecenie, aby utworzyć grupę zasobów, jak pokazano w poniższy fragment kodu. Zwróć uwagę na dane wyjściowe polecenia. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów. Aby uzyskać więcej informacji na temat grup zasobów, zobacz temat [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    ```azurecli
    az group create --location westus --name appgatewayRG
    ```
    
    **-n (lub --name)**. Nazwa nowej grupy zasobów. W naszym scenariuszu jest to *appgatewayRG*.
    
    **-l (lub --location)**. Region świadczenia usługi Azure, w którym zostanie utworzona nowa grupa zasobów. W naszym scenariuszu ma *westus*.

1. Uruchom `az group deployment create` polecenia cmdlet, aby wdrożyć nową sieć wirtualną przy użyciu szablonu i parametr pliki, możesz pobrać i zmodyfikować w poprzednim kroku. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.

    ```azurecli
    az group deployment create --resource-group appgatewayRG --name TestAppgatewayDeployment --template-file azuredeploy.json --parameters @azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Wdrażanie szablonu usługi Azure Resource Manager przy użyciu funkcji szybkiego wdrażania

Użycie funkcji szybkiego wdrażania to kolejny sposób korzystania z szablonów usługi Azure Resource Manager. Jest to prosty sposób używania szablonów w portalu Azure.

1. Przejdź do [Utwórz bramę aplikacji z zapory aplikacji sieci web](https://azure.microsoft.com/documentation/templates/101-application-gateway-waf/).

1. Kliknij przycisk **Wdrażaj na platformie Azure**.

    ![Wdrażanie na platformie Azure](./media/create-vmss-template/deploytoazure.png)
    
1. Wprowadź parametry szablonu wdrożenia w portalu i kliknij przycisk **OK**.

    ![Parametry](./media/create-vmss-template/ibiza1.png)
    
1. Wybierz **akceptuję warunki i postanowienia, o których wspomniano** i kliknij przycisk **zakupu**.

1. W bloku wdrożenia niestandardowego kliknij pozycję **Utwórz**.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Dostarcza dane certyfikatu w szablonach usługi Resource Manager

Przy korzystaniu z protokołu SSL przy użyciu szablonu certyfikatu musi zostać zapewniony w zamiast przekazywanych ciąg w formacie base64. Aby przekonwertować pfx lub .cer ciąg base64 użyj jednej z następujących poleceń. Następujące polecenia przekonwertować ciąg base64, który może zostać dostarczona do szablonu certyfikatu. Oczekiwane dane wyjściowe jest ciągiem, który może być przechowywana w zmiennej i wkleić w szablonie.

### <a name="macos"></a>macOS
```bash
cert=$( base64 <certificate path and name>.pfx )
echo $cert
```

### <a name="windows"></a>Windows
```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

Aby usunąć wszystkie zasoby utworzone w tym artykule, wykonaj jedną z następujących czynności:

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureRmResourceGroup -Name appgatewayRG
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az group delete --name appgatewayRG
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz skonfigurować odciążanie protokołu SSL, odwiedź: [Configure an application gateway for SSL offload](tutorial-ssl-cli.md) (Konfigurowanie bramy aplikacji na potrzeby odciążania protokołu SSL).

Jeśli chcesz skonfigurować bramę aplikacji do użycia z wewnętrznym modułem równoważenia obciążenia, odwiedź: [Create an application gateway with an internal load balancer (ILB)](redirect-internal-site-cli.md) (Tworzenie bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia).

Więcej ogólnych informacji na temat opcji równoważenia obciążenia możesz znaleźć, odwiedzając:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

