---
title: Zarządzanie usługą Traffic Manager na platformie Azure za pomocą programu PowerShell
description: Dzięki tej ścieżce szkoleniowej rozpocznij korzystanie z usługi Azure PowerShell for Traffic Manager.
services: traffic-manager
documentationcenter: na
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 7886764a69eefa68be071a801bea65ae995fbdc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938509"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Zarządzanie menedżerem ruchu za pomocą programu PowerShell

Usługa Azure Resource Manager jest preferowanym interfejsem zarządzania usługami na platformie Azure. Profile usługi Azure Traffic Manager można zarządzać za pomocą interfejsów API i narzędzi opartych na usłudze Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Model zasobów

Usługa Azure Traffic Manager jest konfigurowana przy użyciu kolekcji ustawień nazywanych profilem usługi Traffic Manager. Ten profil zawiera ustawienia DNS, ustawienia routingu ruchu, ustawienia monitorowania punktu końcowego oraz listę punktów końcowych usługi, do których kierowany jest ruch.

Każdy profil usługi Traffic Manager jest reprezentowany przez zasób typu "TrafficManagerProfiles". Na poziomie interfejsu API REST identyfikator URI dla każdego profilu jest następujący:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Konfigurowanie programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Te instrukcje używają programu Microsoft Azure PowerShell. W poniższym artykule wyjaśniono, jak zainstalować i skonfigurować program Azure PowerShell.

* [How to install and configure Azure PowerShell](/powershell/azure/overview)

Przykłady w tym artykule założono, że masz istniejącą grupę zasobów. Grupę zasobów można utworzyć za pomocą następującego polecenia:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Usługa Azure Resource Manager wymaga, aby wszystkie grupy zasobów miały lokalizację. Ta lokalizacja jest używana jako domyślna dla zasobów utworzonych w tej grupie zasobów. Jednak ponieważ zasoby profilu usługi Traffic Manager są globalne, a nie regionalne, wybór lokalizacji grupy zasobów nie ma wpływu na usługę Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Aby utworzyć profil usługi Traffic `New-AzTrafficManagerProfile` Manager, użyj polecenia cmdlet:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

W poniższej tabeli opisano parametry:

| Parametr | Opis |
| --- | --- |
| Nazwa |Nazwa zasobu zasobu usługi Traffic Manager. Profile w tej samej grupie zasobów muszą mieć unikatowe nazwy. Ta nazwa jest oddzielona od nazwy DNS używanej dla kwerend DNS. |
| ResourceGroupName |Nazwa grupy zasobów zawierającej zasób profilu. |
| Natężenie ruchuMetoda |Określa metodę routingu ruchu używaną do określenia, który punkt końcowy jest zwracany w odpowiedzi na kwerendę DNS. Możliwe wartości to "Wydajność", "Ważona" lub "Priorytet". |
| Nazwa względna |Określa część nazwy hosta nazwy DNS podaną przez ten profil usługi Traffic Manager. Ta wartość jest łączona z nazwą domeny DNS używaną przez usługę Azure Traffic Manager do utworzenia w pełni kwalifikowanej nazwy domeny (FQDN) profilu. Na przykład ustawienie wartości "contoso" staje się "contoso.trafficmanager.net". |
| TTL |Określa czas wygaśnięcia DNS (TTL) w sekundach. Ten czas wygaśnięcia informuje lokalne programy rozpoznawania nazw DNS i klientów DNS, jak długo buforować odpowiedzi DNS dla tego profilu usługi Traffic Manager. |
| MonitorProtocol |Określa protokół używany do monitorowania kondycji punktu końcowego. Możliwe wartości to "HTTP" i "HTTPS". |
| Port monitora |Określa port TCP używany do monitorowania kondycji punktu końcowego. |
| Ścieżka monitora |Określa ścieżkę względem nazwy domeny punktu końcowego używanej do sondowania kondycji punktu końcowego. |

Polecenie cmdlet tworzy profil usługi Traffic Manager na platformie Azure i zwraca odpowiedni obiekt profilu do programu PowerShell. W tym momencie profil nie zawiera żadnych punktów końcowych. Aby uzyskać więcej informacji na temat dodawania punktów końcowych do profilu usługi Traffic Manager, zobacz Dodawanie punktów końcowych usługi Traffic Manager.

## <a name="get-a-traffic-manager-profile"></a>Uzyskaj profil usługi Traffic Manager

Aby pobrać istniejący obiekt profilu usługi `Get-AzTrafficManagerProfle` Traffic Manager, użyj polecenia cmdlet:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

To polecenie cmdlet zwraca obiekt profilu usługi Traffic Manager.

## <a name="update-a-traffic-manager-profile"></a>Aktualizowanie profilu usługi Traffic Manager

Modyfikowanie profilów usługi Traffic Manager odbywa się w ramach 3-etapowego procesu:

1. Pobierz profil przy `Get-AzTrafficManagerProfile` użyciu profilu zwróconego przez `New-AzTrafficManagerProfile`program .
2. Zmodyfikuj profil. Można dodawać i usuwać punkty końcowe lub zmieniać parametry punktu końcowego lub profilu. Zmiany te są operacjami off-line. Zmieniasz tylko obiekt lokalny w pamięci, który reprezentuje profil.
3. Zaobjęć `Set-AzTrafficManagerProfile` zmiany za pomocą polecenia cmdlet.

Wszystkie właściwości profilu można zmienić z wyjątkiem profilu RelativeDnsName. Aby zmienić RelativeDnsName, należy usunąć profil i nowy profil z nową nazwą.

W poniższym przykładzie pokazano, jak zmienić czas wygaśnięcia profilu:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Istnieją trzy typy punktów końcowych programu Traffic Manager:

1. **Punkty końcowe platformy Azure** to usługi hostowane na platformie Azure
2. **Zewnętrzne punkty końcowe** to usługi hostowane poza platformą Azure
3. **Zagnieżdżone punkty końcowe** są używane do konstruowania zagnieżdżonych hierarchii profilów usługi Traffic Manager. Zagnieżdżone punkty końcowe umożliwiają zaawansowane konfiguracje routingu ruchu dla złożonych aplikacji.

We wszystkich trzech przypadkach punkty końcowe można dodać na dwa sposoby:

1. Przy użyciu 3-etapowy proces opisany wcześniej. Zaletą tej metody jest to, że kilka zmian punktu końcowego można wyw ciągu jednej aktualizacji.
2. Korzystanie z polecenia cmdlet New-AzTrafficManagerEndpoint. To polecenie cmdlet dodaje punkt końcowy do istniejącego profilu usługi Traffic Manager w jednej operacji.

## <a name="adding-azure-endpoints"></a>Dodawanie punktów końcowych platformy Azure

Usługi referencyjne punktów końcowych platformy Azure hostowane na platformie Azure. Obsługiwane są dwa typy punktów końcowych platformy Azure:

1. Azure App Service
2. Zasoby usługi Azure PublicIpAddress (które można dołączyć do modułu równoważenia obciążenia lub karty sieciowej maszyny wirtualnej). Adres PublicIpAddress musi mieć przypisaną nazwę DNS do użycia w usłudze Traffic Manager.

W każdym przypadku:

* Usługa jest określona przy użyciu parametru `Add-AzTrafficManagerEndpointConfig` "targetResourceId" lub `New-AzTrafficManagerEndpoint`.
* "Target" i "EndpointLocation" są implikowane przez TargetResourceId.
* Określenie "Wagi" jest opcjonalne. Wagi są używane tylko wtedy, gdy profil jest skonfigurowany do używania metody routingu ruchu "Ważona". W przeciwnym razie są one ignorowane. Jeśli jest określony, wartość musi być liczbą z 1 do 1000. Wartością domyślną jest "1".
* Określenie "Priorytet" jest opcjonalne. Priorytety są używane tylko wtedy, gdy profil jest skonfigurowany do korzystania z metody routingu ruchu "Priorytet". W przeciwnym razie są one ignorowane. Prawidłowe wartości są od 1 do 1000 z niższymi wartościami wskazującymi wyższy priorytet. Jeśli określono dla jednego punktu końcowego, muszą być określone dla wszystkich punktów końcowych. Jeśli pominięto, wartości domyślne zaczynające się od "1" są stosowane w kolejności, w kolejności, w których są wyświetlane punkty końcowe.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Przykład 1: Dodawanie punktów końcowych usługi App Service przy użyciu`Add-AzTrafficManagerEndpointConfig`

W tym przykładzie tworzymy profil usługi Traffic Manager i `Add-AzTrafficManagerEndpointConfig` dodajemy dwa punkty końcowe usługi App Service przy użyciu polecenia cmdlet.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Przykład 2: Dodawanie publicznego punktu końcowego usługiIpAddress przy użyciu`New-AzTrafficManagerEndpoint`

W tym przykładzie zasób publicznego adresu IP jest dodawany do profilu usługi Traffic Manager. Publiczny adres IP musi mieć skonfigurowaną nazwę DNS i może być powiązany z kartą sieciową maszyny wirtualnej lub z modułem równoważenia obciążenia.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Dodawanie zewnętrznych punktów końcowych

Usługa Traffic Manager używa zewnętrznych punktów końcowych do kierowania ruchu do usług hostowanych poza platformą Azure. Podobnie jak w przypadku punktów końcowych platformy Azure, `Add-AzTrafficManagerEndpointConfig` zewnętrzne `Set-AzTrafficManagerProfile`punkty `New-AzTrafficManagerEndpoint`końcowe można dodawać za pomocą programu , lub .

Podczas określania zewnętrznych punktów końcowych:

* Nazwa domeny punktu końcowego musi być określona przy użyciu parametru "Target"
* Jeśli używana jest metoda routingu ruchu "Wydajność", wymagana jest "Lokalizacja punktu końcowego". W przeciwnym razie jest to opcjonalne. Wartość musi być [prawidłową nazwą regionu platformy Azure](https://azure.microsoft.com/regions/).
* "Waga" i "Priorytet" są opcjonalne.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Przykład 1: Dodawanie zewnętrznych `Add-AzTrafficManagerEndpointConfig` punktów końcowych przy użyciu i`Set-AzTrafficManagerProfile`

W tym przykładzie tworzymy profil usługi Traffic Manager, dodajemy dwa zewnętrzne punkty końcowe i zatwierdzamy zmiany.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Przykład 2: Dodawanie zewnętrznych punktów końcowych przy użyciu`New-AzTrafficManagerEndpoint`

W tym przykładzie dodajemy zewnętrzny punkt końcowy do istniejącego profilu. Profil jest określony przy użyciu nazw profili i grup zasobów.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Dodawanie punktów końcowych "Zagnieżdżonych"

Każdy profil usługi Traffic Manager określa jedną metodę routingu ruchu. Istnieją jednak scenariusze, które wymagają bardziej zaawansowanego routingu ruchu niż routing dostarczony przez jeden profil usługi Traffic Manager. Profile usługi Traffic Manager można zagnieżdżać, aby połączyć zalety więcej niż jednej metody routingu ruchu. Profile zagnieżdżone umożliwiają zastąpienie domyślnego zachowania usługi Traffic Manager w celu obsługi większych i bardziej złożonych wdrożeń aplikacji. Aby uzyskać bardziej szczegółowe przykłady, zobacz [Profile zagnieżdżonego programu Traffic Manager](traffic-manager-nested-profiles.md).

Zagnieżdżone punkty końcowe są konfigurowane w profilu nadrzędnym przy użyciu określonego typu punktu końcowego , "NestedEndpoints". Podczas określania zagnieżdżonych punktów końcowych:

* Punkt końcowy musi być określony przy użyciu parametru "targetResourceId"
* Jeśli używana jest metoda routingu ruchu "Wydajność", wymagana jest "Lokalizacja punktu końcowego". W przeciwnym razie jest to opcjonalne. Wartość musi być [prawidłową nazwą regionu platformy Azure](https://azure.microsoft.com/regions/).
* "Waga" i "Priorytet" są opcjonalne, podobnie jak dla punktów końcowych platformy Azure.
* Parametr "MinChildEndpoints" jest opcjonalny. Wartością domyślną jest "1". Jeśli liczba dostępnych punktów końcowych spadnie poniżej tego progu, profil nadrzędny uznaje profil podrzędny za "zdegradowany" i kieruje ruch do innych punktów końcowych w profilu nadrzędnym.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Przykład 1: Dodawanie zagnieżdżonych punktów końcowych przy użyciu `Add-AzTrafficManagerEndpointConfig` i`Set-AzTrafficManagerProfile`

W tym przykładzie tworzymy nowe profile podrzędne i nadrzędne usługi Traffic Manager, dodajemy element podrzędny jako zagnieżdżony punkt końcowy do nadrzędnego i zatwierdzamy zmiany.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

W tym przykładzie nie dodano żadnych innych punktów końcowych do profilów podrzędnych lub nadrzędnych.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Przykład 2: Dodawanie zagnieżdżonych punktów końcowych przy użyciu`New-AzTrafficManagerEndpoint`

W tym przykładzie dodajemy istniejący profil podrzędny jako zagnieżdżony punkt końcowy do istniejącego profilu nadrzędnego. Profil jest określony przy użyciu nazw profili i grup zasobów.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Dodawanie punktów końcowych z innej subskrypcji

Usługa Traffic Manager może pracować z punktami końcowymi z różnych subskrypcji. Musisz przełączyć się do subskrypcji z punktem końcowym, który chcesz dodać, aby pobrać potrzebne dane wejściowe do usługi Traffic Manager. Następnie należy przełączyć się do subskrypcji za pomocą profilu usługi Traffic Manager i dodać do niego punkt końcowy. Poniższy przykład pokazuje, jak to zrobić z publicznym adresem IP.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Aktualizowanie punktu końcowego usługi Traffic Manager

Istnieją dwa sposoby aktualizowania istniejącego punktu końcowego usługi Traffic Manager:

1. Pobierz profil Usługi `Get-AzTrafficManagerProfile`Traffic Manager za pomocą , zaktualizuj `Set-AzTrafficManagerProfile`właściwości punktu końcowego w profilu i zatwierdź zmiany za pomocą . Ta metoda ma tę zaletę, że można zaktualizować więcej niż jeden punkt końcowy w jednej operacji.
2. Pobierz punkt końcowy usługi `Get-AzTrafficManagerEndpoint`Traffic Manager za pomocą , zaktualizuj właściwości punktu końcowego i zaajmy zmiany za pomocą . `Set-AzTrafficManagerEndpoint` Ta metoda jest prostsza, ponieważ nie wymaga indeksowania do endpoints tablicy w profilu.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Przykład 1: Aktualizowanie punktów `Get-AzTrafficManagerProfile` końcowych przy użyciu i`Set-AzTrafficManagerProfile`

W tym przykładzie zmodyfikujemy priorytet w dwóch punktach końcowych w istniejącym profilu.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Przykład 2: Aktualizowanie punktu `Get-AzTrafficManagerEndpoint` końcowego przy użyciu i`Set-AzTrafficManagerEndpoint`

W tym przykładzie modyfikujemy wagę pojedynczego punktu końcowego w istniejącym profilu.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Włączanie i wyłączanie punktów końcowych i profili

Usługa Traffic Manager umożliwia włączanie i wyłączanie poszczególnych punktów końcowych, a także umożliwia włączanie i wyłączanie całych profili.
Zmiany te można wyw. Aby usprawnić te wspólne operacje, są one również obsługiwane za pośrednictwem dedykowanych poleceń cmdlet.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Przykład 1: Włączanie i wyłączanie profilu usługi Traffic Manager

Aby włączyć profil usługi `Enable-AzTrafficManagerProfile`Traffic Manager, użyj pliku . Profil można określić za pomocą obiektu profilu. Obiekt profilu mogą być przekazywane za pośrednictwem potoku lub za pomocą parametru '-TrafficManagerProfile'. W tym przykładzie określamy profil według nazwy profilu i grupy zasobów.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Aby wyłączyć profil usługi Traffic Manager:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Polecenie cmdlet Disable-AzTrafficManagerProfile monituje o potwierdzenie. Ten monit można pominąć za pomocą parametru '-Force'.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Przykład 2: Włączanie i wyłączanie punktu końcowego programu Traffic Manager

Aby włączyć punkt końcowy programu `Enable-AzTrafficManagerEndpoint`Traffic Manager, użyj pliku . Istnieją dwa sposoby określania punktu końcowego

1. Za pomocą TrafficManagerEndpoint obiektu przekazywane przez potok lub przy użyciu parametru "-TrafficManagerEndpoint"
2. Używanie nazwy punktu końcowego, typu punktu końcowego, nazwy profilu i nazwy grupy zasobów:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Podobnie, aby wyłączyć punkt końcowy programu Traffic Manager:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Podobnie `Disable-AzTrafficManagerProfile`jak `Disable-AzTrafficManagerEndpoint` w przypadku polecenia cmdlet monituje o potwierdzenie. Ten monit można pominąć za pomocą parametru '-Force'.

## <a name="delete-a-traffic-manager-endpoint"></a>Usuwanie punktu końcowego menedżera ruchu

Aby usunąć poszczególne punkty końcowe, użyj polecenia `Remove-AzTrafficManagerEndpoint` cmdlet:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

To polecenie cmdlet monituje o potwierdzenie. Ten monit można pominąć za pomocą parametru '-Force'.

## <a name="delete-a-traffic-manager-profile"></a>Usuwanie profilu usługi Traffic Manager

Aby usunąć profil usługi Traffic `Remove-AzTrafficManagerProfile` Manager, użyj polecenia cmdlet, określając nazwy profilów i grup zasobów:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

To polecenie cmdlet monituje o potwierdzenie. Ten monit można pominąć za pomocą parametru '-Force'.

Profil do usunięcia można również określić za pomocą obiektu profilu:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Sekwencja ta może być również potoku:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Następne kroki

[Monitorowanie usługi Traffic Manager](traffic-manager-monitoring.md)

[Zagadnienia dotyczące wydajności usługi Traffic Manager](traffic-manager-performance-considerations.md)
