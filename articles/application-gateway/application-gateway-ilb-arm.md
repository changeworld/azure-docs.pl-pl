---
title: Przy użyciu bramy aplikacji Azure z wewnętrznego modułu równoważenia obciążenia - PowerShell | Dokumentacja firmy Microsoft
description: Ta strona zawiera instrukcje dotyczące tworzenia, konfigurowania, uruchamiania i usuwania bramy aplikacji platformy Azure za pomocą wewnętrznego modułu równoważenia obciążenia na potrzeby usługi Azure Resource Manager
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: a558a5f4f1e19be7b15721c8f99dc5a2d7b396bc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Tworzenie i konfigurowanie bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia i usługi Azure Resource Manager

> [!div class="op_single_selector"]
> * [Klasyczny portal Azure — program PowerShell](application-gateway-ilb.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-ilb-arm.md)

Usługę Azure Application Gateway można skonfigurować z internetowym wirtualnym adresem IP lub wewnętrznym punktem końcowym niepołączonym z Internetem, znanym także jako punkt końcowy wewnętrznego modułu równoważenia obciążenia. Konfigurowanie bramy przy użyciu wewnętrznego modułu równoważenia obciążenia jest pomocne w przypadku wewnętrznych aplikacji LOB niepołączonych z Internetem. Ta opcja jest również przydatna w przypadku usług i warstw w aplikacji wielowarstwowej, która znajduje się w granicach zabezpieczeń bez połączenia z Internetem, ale nadal wymaga dystrybucji obciążenia z działaniem okrężnym, lepkości sesji lub zakończenia protokołu SSL (Secure Sockets Layer).

W tym artykule przeprowadzimy Cię przez proces konfigurowania bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Zainstaluj najnowszą wersję poleceń cmdlet programu Azure PowerShell za pomocą Instalatora platformy sieci Web. Najnowszą wersję można pobrać i zainstalować z sekcji **Windows PowerShell** strony [Pliki do pobrania](https://azure.microsoft.com/downloads/).
2. Utworzysz sieć wirtualną i podsieć dla usługi Application Gateway. Upewnij się, że z podsieci nie korzystają żadne maszyny wirtualne ani wdrożenia w chmurze. Usługa Application Gateway musi sama znajdować się w podsieci sieci wirtualnej.
3. Serwery konfigurowane do używania bramy aplikacji muszą być umieszczone w sieci wirtualnej lub z przypisanym adresem IP/VIP lub mieć w niej utworzone punkty końcowe.

## <a name="what-is-required-to-create-an-application-gateway"></a>Co jest wymagane do utworzenia bramy aplikacji?

* **Pula serwerów zaplecza:** lista adresów IP serwerów zaplecza. Adresy IP na liście powinny należeć do sieci wirtualnej, ale w innej podsieci bramy aplikacji, lub być publicznymi bądź wirtualnymi adresami IP.
* **Ustawienia puli serwerów zaplecza:** każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są powiązane z pulą i są stosowane do wszystkich serwerów w tej puli.
* **Port frontonu:** port publiczny, który jest otwierany w bramie aplikacji. Ruch trafia do tego portu, a następnie jest przekierowywany do jednego z serwerów zaplecza.
* **Odbiornik:** odbiornik ma port frontonu, protokół (Http lub Https, z uwzględnieniem wielkości liter) oraz nazwę certyfikatu SSL (w przypadku konfigurowania odciążania protokołu SSL).
* **Reguła:** reguła wiąże odbiornik z pulą serwerów zaplecza i umożliwia zdefiniowanie, do której puli serwerów zaplecza ma być przekierowywany ruch w przypadku trafienia do określonego odbiornika. Obecnie jest obsługiwana tylko reguła *podstawowa*. Reguła *podstawowa* to dystrybucja obciążenia z działaniem okrężnym.

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Różnica między klasycznym modelem wdrożenia Azure i usługą Azure Resource Manager polega na kolejności tworzenia bramy aplikacji i elementów, które należy skonfigurować.
W usłudze Resource Manager wszystkie elementy składające się na bramę aplikacji są konfigurowane osobno, a następnie składane w celu utworzenia zasobu bramy aplikacji.

Oto kroki wymagane do utworzenia bramy aplikacji:

1. Tworzenie grupy zasobów dla usługi Resource Manager
2. Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji
3. Tworzenie obiektu konfiguracji bramy aplikacji
4. Tworzenie zasobu bramy aplikacji

## <a name="create-a-resource-group-for-resource-manager"></a>Tworzenie grupy zasobów dla usługi Resource Manager

Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Azure Resource Manager. Więcej informacji znajduje się w artykule [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

### <a name="step-1"></a>Krok 1

```powershell
Connect-AzureRmAccount
```

### <a name="step-2"></a>Krok 2

Sprawdź subskrypcje dostępne na koncie.

```powershell
Get-AzureRmSubscription
```

Zostanie wyświetlony monit o uwierzytelnienie przy użyciu własnych poświadczeń.

### <a name="step-3"></a>Krok 3

Wybierz subskrypcję platformy Azure do użycia.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Krok 4

Utwórz nową grupę zasobów (ten krok można pominąć, jeśli używasz istniejącej grupy zasobów).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -location "West US"
```

Usługa Azure Resource Manager wymaga, żeby wszystkie grupy zasobów miały lokalizację. Będzie ona używana jako domyślna lokalizacja zasobów w danej grupie. Upewnij się, że we wszystkich poleceniach służących do tworzenia bramy aplikacji jest używana ta sama grupa zasobów.

W powyższym przykładzie utworzono grupę zasobów o nazwie "zarządcy zasobów appgw" i lokalizacji "Zachodnie US".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji

W poniższym przykładzie pokazano, jak utworzyć sieć wirtualną przy użyciu usługi Resource Manager:

### <a name="step-1"></a>Krok 1

```powershell
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Ten krok przypisuje zakresu adresów 10.0.0.0/24 zmiennej podsieci ma być używany do tworzenia sieci wirtualnej.

### <a name="step-2"></a>Krok 2

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Spowoduje to utworzenie sieci wirtualnej o nazwie "appgwvnet" w zasobów grupy "appgw-zarządcy zasobów" dla regionu zachodnie stany USA, przy użyciu 10.0.0.0/16 prefiks z podsieci 10.0.0.0/24.

### <a name="step-3"></a>Krok 3

```powershell
$subnet = $vnet.subnets[0]
```

Ten krok przypisuje obiektu podsieci do zmiennej $subnet dalsze czynności.

## <a name="create-an-application-gateway-configuration-object"></a>Tworzenie obiektu konfiguracji bramy aplikacji

### <a name="step-1"></a>Krok 1

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Spowoduje to utworzenie konfiguracji IP bramy dla aplikacji o nazwie "gatewayIP01". Uruchomiona usługa Application Gateway wybierze adres IP ze skonfigurowanej podsieci i skieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

### <a name="step-2"></a>Krok 2

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Ten krok obejmuje skonfigurowanie puli adresów IP zaplecza, o nazwie "pool01" z adresem IP, adresy "10.1.1.8, 10.1.1.9, 10.1.1.10". Są to adresy IP odbierające ruch sieciowy pochodzący z punktu końcowego adresu IP frontonu. Powyższe adresy IP można zastąpić własnymi dodawanymi punktami końcowymi adresów IP aplikacji.

### <a name="step-3"></a>Krok 3

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Ten krok obejmuje skonfigurowanie ruchu sieciowego bramy ustawienie "poolsetting01" obciążenia zrównoważonym aplikacji w puli zaplecza.

### <a name="step-4"></a>Krok 4

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Ten krok obejmuje skonfigurowanie portu IP frontonu dla ILB o nazwie "frontendport01".

### <a name="step-5"></a>Krok 5

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Ten krok konfiguracji IP frontonu o nazwie "fipconfig01" tworzy i kojarzy ją z prywatnego adresu IP z bieżącej podsieci sieci wirtualnej.

### <a name="step-6"></a>Krok 6

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Ten krok odbiornik o nazwie "listener01" tworzy i kojarzy frontonu port konfiguracji IP frontonu.

### <a name="step-7"></a>Krok 7

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Spowoduje to utworzenie reguły routingu modułu równoważenia obciążenia o nazwie "rule01", który konfiguruje zachowanie usługi równoważenia obciążenia.

### <a name="step-8"></a>Krok 8

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Ten krok obejmuje skonfigurowanie rozmiar wystąpienia bramy aplikacji.

> [!NOTE]
> Wartość domyślna parametru *InstanceCount* to 2, a wartość maksymalna — 10. Wartość domyślna parametru *GatewaySize* to Medium. Możesz wybrać następujące wartości: Standard_Small, Standard_Medium i Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Tworzenie bramy aplikacji przy użyciu polecenia New-AzureApplicationGateway

Tworzy bramę aplikacji z wszystkich elementów konfiguracji z powyższych kroków. W tym przykładzie brama aplikacji ma nazwę „appgwtest”.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Ten krok tworzy bramę aplikacji z wszystkich elementów konfiguracji z powyższych kroków. W przykładzie brama aplikacji ma nazwę „appgwtest”.

## <a name="delete-an-application-gateway"></a>Usuwanie bramy aplikacji

Aby usunąć bramę aplikacji, należy wykonać poniższe kroki w kolejności:

1. Użyj polecenia cmdlet `Stop-AzureRmApplicationGateway`, aby zatrzymać bramę.
2. Użyj polecenia cmdlet `Remove-AzureRmApplicationGateway`, aby usunąć bramę.
3. Aby sprawdzić, czy brama została usunięta, użyj polecenia cmdlet `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Krok 1

Pobierz obiekt bramy aplikacji i skojarz go ze zmienną „$getgw”.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Krok 2

Użyj polecenia `Stop-AzureRmApplicationGateway`, aby zatrzymać bramę aplikacji. W tym przykładzie pokazano `Stop-AzureRmApplicationGateway` polecenia cmdlet w pierwszym wierszu, a następnie dane wyjściowe.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Po zatrzymaniu bramy aplikacji użyj polecenia cmdlet `Remove-AzureRmApplicationGateway`, aby usunąć usługę.

```powershell
Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> Przełącznik **-force** umożliwia pomijanie komunikatu potwierdzającego usunięcie.

Aby sprawdzić, czy usługa została usunięta, możesz użyć polecenia cmdlet `Get-AzureRmApplicationGateway`. Ten krok nie jest wymagany.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz skonfigurować odciążanie protokołu SSL, zobacz artykuł [Configure an application gateway for SSL offload](application-gateway-ssl.md) (Konfigurowanie bramy aplikacji na potrzeby odciążania protokołu SSL).

Jeśli chcesz skonfigurować bramę aplikacji do użycia z wewnętrznym modułem równoważenia obciążenia, zobacz artykuł [Create an application gateway with an internal load balancer (ILB)](application-gateway-ilb.md) (Tworzenie bramy aplikacji z wewnętrznym modułem równoważenia obciążenia).

Więcej ogólnych informacji na temat opcji równoważenia obciążenia możesz znaleźć w następujących artykułach:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

