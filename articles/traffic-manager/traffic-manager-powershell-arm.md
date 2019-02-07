---
title: Zarządzanie usługą Traffic Manager na platformie Azure przy użyciu programu PowerShell
description: Przy użyciu programu PowerShell dla usługi Traffic Manager z usługą Azure Resource Manager
services: traffic-manager
documentationcenter: na
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 921788d1cd3ff24140bdff0c9b6a181e4ab7f0a8
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816224"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Zarządzanie usługą Traffic Manager przy użyciu programu PowerShell

Usługa Azure Resource Manager jest interfejsem preferowane dla usług na platformie Azure. Profile usługi Traffic Manager platformy Azure mogą być zarządzane przy użyciu narzędzia i interfejsy API oparte na usłudze Azure Resource Manager.

## <a name="resource-model"></a>Model zasobów

Usługa Azure Traffic Manager jest skonfigurowany przy użyciu kolekcji ustawień profilu usługi Traffic Manager. Ten profil zawiera ustawienia DNS, ustawienia routingu ruchu, ustawienia monitorowania punktu końcowego i lista punktów końcowych usługi, do których ruch jest kierowany.

Każdy profil usługi Traffic Manager jest reprezentowany przez zasobu typu "TrafficManagerProfiles". Na poziomie interfejsu API REST identyfikator URI dla każdego profilu jest następująca:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Konfigurowanie programu Azure PowerShell

W poniższych instrukcjach użyto programu Microsoft Azure PowerShell. Następujący artykuł wyjaśnia, jak zainstalować i skonfigurować program Azure PowerShell.

* [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)

Przykłady w niniejszym artykule przyjęto założenie, że masz istniejącą grupę zasobów. Można utworzyć grupę zasobów za pomocą następującego polecenia:

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Usługi Azure Resource Manager wymaga, że wszystkie grupy zasobów ma lokalizacji. Ta lokalizacja jest używana jako domyślny dla zasobów utworzonych w tej grupie zasobów. Jednak ponieważ zasoby profilu usługi Traffic Manager są globalne, a nie regionalne, wybór lokalizacji grupy zasobów nie ma wpływu na usługi Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Aby utworzyć profil usługi Traffic Manager, użyj `New-AzureRmTrafficManagerProfile` polecenia cmdlet:

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

W poniższej tabeli opisano parametry:

| Parametr | Opis |
| --- | --- |
| Name (Nazwa) |Nazwa zasobu dla zasobu profilu usługi Traffic Manager. Profile w tej samej grupie zasobów, muszą mieć unikatowe nazwy. Ta nazwa jest oddzielony od nazwy DNS na zapytania DNS. |
| ResourceGroupName |Nazwa grupy zasobów zawierającej zasób profilu. |
| TrafficRoutingMethod |Określa metodę routingu ruchu, używany do określenia, który punkt końcowy jest zwracany w odpowiedzi na kwerendę DNS. Możliwe wartości to "Wydajność", "Ważona" lub "Priority". |
| RelativeDnsName |Określa nazwę hosta część nazwy DNS podane przez ten profil usługi Traffic Manager. Ta wartość jest połączone z nazwą domeny DNS, które są używane przez usługę Azure Traffic Manager w celu utworzenia w pełni kwalifikowana nazwa domeny (FQDN) profilu. Na przykład ustawienie wartości "contoso" staje się "contoso.trafficmanager.net." |
| TTL |Określa DNS Time-to-Live (TTL) w ciągu kilku sekund. Ten czas wygaśnięcia informuje rozpoznawania nazw DNS lokalnym i klientów DNS, jak długo buforowanie odpowiedzi DNS dla tego profilu usługi Traffic Manager. |
| MonitorProtocol |Określa protokół używany do monitorowania kondycji punktu końcowego. Możliwe wartości to "HTTP" i "HTTPS". |
| MonitorPort |Określa port TCP używany do monitorowania kondycji punktu końcowego. |
| MonitorPath |Określa ścieżkę względną nazwa domeny punktu końcowego, które są używane do sondowania dla punktu końcowego kondycji. |

Polecenie cmdlet tworzy profil usługi Traffic Manager na platformie Azure i zwraca odpowiedni obiekt profilu do programu PowerShell. W tym momencie profil, który nie zawiera żadnych punktów końcowych. Aby uzyskać więcej informacji na temat dodawania punktów końcowych do profilu usługi Traffic Manager Zobacz Dodawanie punktów końcowych usługi Traffic Manager.

## <a name="get-a-traffic-manager-profile"></a>Pobierz profil usługi Traffic Manager

Aby pobrać istniejący obiekt profilu usługi Traffic Manager, użyj `Get-AzureRmTrafficManagerProfle` polecenia cmdlet:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

To polecenie cmdlet zwraca obiekt profilu usługi Traffic Manager.

## <a name="update-a-traffic-manager-profile"></a>Aktualizuj profil usługi Traffic Manager

Modyfikowanie profile usługi Traffic Manager poniżej proces krok 3:

1. Pobrać za pomocą profilu `Get-AzureRmTrafficManagerProfile` lub Użyj profilu zwrócony przez `New-AzureRmTrafficManagerProfile`.
2. Modyfikowanie profilu. Możesz dodać i usunąć punkty końcowe lub zmień parametry profil lub punkt końcowy. Te zmiany są operacje trybu offline. Zmieniasz lokalnego obiektu w pamięci, który reprezentuje profilu.
3. Zatwierdź zmiany przy użyciu `Set-AzureRmTrafficManagerProfile` polecenia cmdlet.

Z wyjątkiem RelativeDnsName profil, który można zmienić wszystkie właściwości profilu. Aby zmienić RelativeDnsName, należy usunąć profil i nowy profil pod nową nazwą.

Poniższy przykład pokazuje, jak zmienić czas wygaśnięcia w profilu:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Istnieją trzy typy punktów końcowych usługi Traffic Manager:

1. **Punkty końcowe platformy Azure** usług hostowanych na platformie Azure
2. **Zewnętrzne punkty końcowe** usług hostowanych poza platformą Azure
3. **Zagnieżdżone punktów końcowych** są używane do konstruowania zagnieżdżonej hierarchii profilów usługi Traffic Manager. Zagnieżdżone punktów końcowych włączyć zaawansowane konfiguracje routingu ruchu dla złożonych aplikacji.

We wszystkich trzech przypadkach punkty końcowe można dodać na dwa sposoby:

1. Za pomocą procesu kroku 3 opisane wcześniej. Zaletą tej metody jest, że kilka punktu końcowego zmian w jednej aktualizacji.
2. Za pomocą polecenia New-AzureRmTrafficManagerEndpoint polecenia cmdlet. To polecenie cmdlet dodaje punkt końcowy do istniejącego profilu usługi Traffic Manager w ramach jednej operacji.

## <a name="adding-azure-endpoints"></a>Dodawanie punktów końcowych platformy Azure

Punkty końcowe platformy Azure odwoływać się do usług hostowanych na platformie Azure. Obsługiwane są dwa rodzaje punkty końcowe platformy Azure:

1. Azure App Service
2. Zasoby usługi platformy Azure publicznego adresu IP, (które mogą być dołączane do równoważenia obciążenia lub maszynie wirtualnej karty Sieciowej). Publiczny adres IP musi mieć nazwę DNS, przypisany do użycia w usłudze Traffic Manager.

W każdym przypadku:

* Usługa jest określony za pomocą parametru "element targetResourceId" `Add-AzureRmTrafficManagerEndpointConfig` lub `New-AzureRmTrafficManagerEndpoint`.
* 'Target' i "EndpointLocation" są też dorozumianych przez element TargetResourceId.
* Określanie "waga" jest opcjonalna. Wagi są używane tylko w przypadku, jeśli profil, który jest skonfigurowany do używania metody routingu ruchu "Ważona". W przeciwnym razie są ignorowane. Jeśli zostanie określony, wartość musi być liczbą z przedziału od 1 do 1000. Wartość domyślna to "1".
* Określanie "priorytet" jest opcjonalna. Priorytety są używane tylko w przypadku, jeśli profil, który jest skonfigurowany do używania metody routingu ruchu "Priority". W przeciwnym razie są ignorowane. Prawidłowe wartości to od 1 do 1000 o niższych wartościach, wskazując wyższy priorytet. Jeśli określona dla punktów końcowych, muszą one być określone dla wszystkich punktów końcowych. W przypadku pominięcia wartości domyślne, zaczynając od "1" są stosowane w kolejności, czy punkty końcowe są wyświetlane.

### <a name="example-1-adding-app-service-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Przykład 1: Dodawanie punktów końcowych usługi App Service przy użyciu `Add-AzureRmTrafficManagerEndpointConfig`

W tym przykładzie firma Microsoft Tworzenie profilu usługi Traffic Manager i dodaj dwa punkty końcowe usługi aplikacji przy użyciu `Add-AzureRmTrafficManagerEndpointConfig` polecenia cmdlet.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Przykład 2: Dodawanie punktu końcowego publicznego adresu IP za pomocą `New-AzureRmTrafficManagerEndpoint`

W tym przykładzie zasób publicznego adresu IP jest dodawane do profilu usługi Traffic Manager. Publiczny adres IP musi mieć nazwę DNS, skonfigurowane i może być powiązana, do karty Sieciowej maszyny wirtualnej lub z modułem równoważenia obciążenia.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Dodawanie zewnętrzne punkty końcowe

Przy użyciu zewnętrzne punkty końcowe usługi Traffic Manager kieruje ruch do usług hostowanych poza platformą Azure. Jako punkty końcowe platformy Azure, zewnętrzne punkty końcowe można dodać albo przy użyciu `Add-AzureRmTrafficManagerEndpointConfig` następuje `Set-AzureRmTrafficManagerProfile`, lub `New-AzureRMTrafficManagerEndpoint`.

Podczas określania zewnętrzne punkty końcowe:

* Należy określić nazwę domeny punktu końcowego za pomocą parametru 'Target'
* Jeśli używana jest metoda routingu ruchu "Wydajność", "EndpointLocation" jest wymagana. W przeciwnym razie jest to opcjonalne. Wartość musi być [nazwa prawidłowy region platformy Azure](https://azure.microsoft.com/regions/).
* "Waga" i "Priority" są opcjonalne.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Przykład 1: Dodawanie zewnętrznych punktów końcowych przy użyciu `Add-AzureRmTrafficManagerEndpointConfig` i `Set-AzureRmTrafficManagerProfile`

W tym przykładzie firma Microsoft Tworzenie profilu usługi Traffic Manager, dodaj dwa zewnętrzne punkty końcowe i zatwierdź zmiany.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Przykład 2: Dodawanie zewnętrznych punktów końcowych przy użyciu `New-AzureRmTrafficManagerEndpoint`

W tym przykładzie dodamy zewnętrzny punkt końcowy do istniejącego profilu. Profil, który jest określony, przy użyciu nazwy grupy profilu i zasobów.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Dodawanie punktów końcowych "Zagnieżdżone"

Każdy profil usługi Traffic Manager określa pojedynczą metodę routingu ruchu. Jednak istnieją scenariusze, które wymagają bardziej zaawansowanych routing ruchu niż routingu przez jeden profil usługi Traffic Manager. Można zagnieżdżać profile usługi Traffic Manager, aby połączyć korzyści wynikające z więcej niż jednej metody routingu ruchu. Zagnieżdżone Profile umożliwiają zastąpić domyślne zachowanie usługi Traffic Manager, obsługi większych i bardziej złożonych wdrożeń aplikacji. Aby uzyskać bardziej szczegółowe przykłady, zobacz [profile usługi Traffic Manager zagnieżdżone](traffic-manager-nested-profiles.md).

Zagnieżdżone punkty końcowe są konfigurowane w profilu nadrzędnego, przy użyciu typu określonego punktu końcowego "NestedEndpoints". Podczas określania zagnieżdżonych punktów końcowych:

* Należy określić punkt końcowy przy użyciu parametru "element targetResourceId"
* Jeśli używana jest metoda routingu ruchu "Wydajność", "EndpointLocation" jest wymagana. W przeciwnym razie jest to opcjonalne. Wartość musi być [nazwa prawidłowy region platformy Azure](https://azure.microsoft.com/regions/).
* "Waga" i "Priority" są opcjonalne, jak w przypadku punkty końcowe platformy Azure.
* Parametr "MinChildEndpoints" jest opcjonalny. Wartość domyślna to "1". Jeśli liczba dostępnych punktów końcowych nie spadnie poniżej tego progu, profilu nadrzędnego uważa, profilu podrzędny "negatywny wpływ na dostępność" i przekierowywanie ruchu do punktów końcowych w profilu nadrzędnej.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Przykład 1: Dodawanie zagnieżdżonych punktów końcowych przy użyciu `Add-AzureRmTrafficManagerEndpointConfig` i `Set-AzureRmTrafficManagerProfile`

W tym przykładzie będziemy tworzyć profile nowy nadrzędnymi i podrzędnymi usługi Traffic Manager, Dodaj element podrzędny jako zagnieżdżony punkt końcowy do elementu nadrzędnego i zatwierdź zmiany.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Celu skrócenia programu w tym przykładzie firma Microsoft nie dodano żadnych punktów końcowych profilów podrzędnej lub nadrzędnej.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Przykład 2: Dodawanie zagnieżdżonych punktów końcowych przy użyciu `New-AzureRmTrafficManagerEndpoint`

W tym przykładzie dodamy istniejący profil podrzędnych jako zagnieżdżony punkt końcowy do istniejącego profilu nadrzędnej. Profil, który jest określony, przy użyciu nazwy grupy profilu i zasobów.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Dodawanie punktów końcowych z innej subskrypcji

Usługa Traffic Manager może współpracować z punktów końcowych z różnych subskrypcji. Musisz przełączyć się do subskrypcji z punktem końcowym, który chcesz dodać do pobrania wymaganych danych wejściowych do usługi Traffic Manager. Następnie należy przełączyć się do subskrypcji z profilu usługi Traffic Manager, a następnie dodaj encpoint do niego. Poniższy przykład przedstawia sposób to zrobić przy użyciu publicznego adresu IP.

```powershell
Set-AzureRmContext -SubscriptionId $EndpointSubscription
$ip = Get-AzureRmPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzureRmContext -SubscriptionId $trafficmanagerSubscription
New-AzureRmTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Aktualizacja punktu końcowego usługi Traffic Manager

Istnieją dwa sposoby, aby zaktualizować istniejący punkt końcowy usługi Traffic Manager:

1. Pobierz profil usługi Traffic Manager używa `Get-AzureRmTrafficManagerProfile`, zaktualizować właściwości punktu końcowego w profilu i zatwierdź zmiany przy użyciu `Set-AzureRmTrafficManagerProfile`. Ta metoda ma tę zaletę, można zaktualizować więcej niż jeden punkt końcowy w ramach jednej operacji.
2. Pobieranie przy użyciu punktu końcowego usługi Traffic Manager `Get-AzureRmTrafficManagerEndpoint`, zaktualizować właściwości punktu końcowego i zatwierdź zmiany przy użyciu `Set-AzureRmTrafficManagerEndpoint`. Ta metoda jest prostsze, ponieważ nie wymaga indeksowania w tablicy punktów końcowych w profilu.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Przykład 1: Aktualizowanie punktów końcowych przy użyciu `Get-AzureRmTrafficManagerProfile` i `Set-AzureRmTrafficManagerProfile`

W tym przykładzie zmodyfikujemy priorytetu na dwa punkty końcowe w ramach istniejącego profilu.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Przykład 2: Aktualizowanie punktu końcowego za pomocą `Get-AzureRmTrafficManagerEndpoint` i `Set-AzureRmTrafficManagerEndpoint`

W tym przykładzie zmodyfikujemy wagę pojedyncze punkty końcowe w istniejącego profilu.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Włączanie i wyłączanie punktów końcowych i profile

Usługa Traffic Manager umożliwia poszczególne punkty końcowe włączone i wyłączone, a także umożliwiające włączanie i wyłączanie całego profilów.
Tych zmian przez pobieranie/aktualizowanie/ustawienia zasobów profil lub punkt końcowy. Aby usprawnić tych typowych operacji, są one również obsługiwane za pomocą polecenia cmdlet przeznaczone.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Przykład 1: Włączanie i wyłączanie profilu usługi Traffic Manager

Aby włączyć profilu usługi Traffic Manager, należy użyć `Enable-AzureRmTrafficManagerProfile`. Profil, który można określić za pomocą obiektu profilu. Obiekt profil może być przekazywany, za pośrednictwem potoku lub za pomocą "-TrafficManagerProfile" parametru. W tym przykładzie określamy profil nazwę grupy profilu i zasobów.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Aby wyłączyć profil usługi Traffic Manager:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Polecenie cmdlet Disable-polecenia AzureRmTrafficManagerProfile monituje o potwierdzenie. Ten monit można pominąć za pomocą "-Force" parametru.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Przykład 2: Włączanie i wyłączanie punktu końcowego usługi Traffic Manager

Aby włączyć punkt końcowy usługi Traffic Manager, należy użyć `Enable-AzureRmTrafficManagerEndpoint`. Istnieją dwa sposoby określania punktu końcowego

1. Za pomocą obiektu TrafficManagerEndpoint przekazywane za pośrednictwem potoku lub "-TrafficManagerEndpoint" parametru
2. Przy użyciu nazwy punktu końcowego, typ punktu końcowego, nazwa profilu i nazwy grupy zasobów:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Podobnie Aby wyłączyć punkt końcowy usługi Traffic Manager:

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Podobnie jak w przypadku `Disable-AzureRmTrafficManagerProfile`, `Disable-AzureRmTrafficManagerEndpoint` polecenie cmdlet monituje o potwierdzenie. Ten monit można pominąć za pomocą "-Force" parametru.

## <a name="delete-a-traffic-manager-endpoint"></a>Usuwanie punktu końcowego usługi Traffic Manager

Aby usunąć poszczególne punkty końcowe, użyj `Remove-AzureRmTrafficManagerEndpoint` polecenia cmdlet:

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

To polecenie cmdlet monituje o potwierdzenie. Ten monit można pominąć za pomocą "-Force" parametru.

## <a name="delete-a-traffic-manager-profile"></a>Usuwanie profilu usługi Traffic Manager

Aby usunąć profil usługi Traffic Manager, użyj `Remove-AzureRmTrafficManagerProfile` polecenia cmdlet, określenie nazwy grupy profilu i zasobów:

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

To polecenie cmdlet monituje o potwierdzenie. Ten monit można pominąć za pomocą "-Force" parametru.

Profil, który ma zostać usunięty można również określić za pomocą obiektu profilu:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Ta sekwencja można również przekazać w potoku:

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie usługi Traffic Manager](traffic-manager-monitoring.md)

[Zagadnienia dotyczące wydajności usługi Traffic Manager](traffic-manager-performance-considerations.md)
