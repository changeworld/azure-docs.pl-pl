---
title: Zarządzanie Menedżera ruchu na platformie Azure za pomocą programu PowerShell | Dokumentacja firmy Microsoft
description: Przy użyciu programu PowerShell dla Menedżera ruchu z usługi Azure Resource Manager
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: bc247448-1d2e-4104-ac03-42b59ebde065
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 951e845e23a1ed0cbdc83fc24a97a545f00c52ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526645"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Przy użyciu programu PowerShell do zarządzania Menedżer ruchu

Usługa Azure Resource Manager to interfejs zarządzania preferowanych dla usług Azure. Azure profilów usługi Traffic Manager mogą być zarządzane przy użyciu usługi Azure Resource Manager na podstawie interfejsów API i narzędzia.

## <a name="resource-model"></a>Model zasobów

Menedżer ruchu Azure jest konfigurowane przy użyciu kolekcję ustawień o nazwie profilu usługi Traffic Manager. Ten profil zawiera ustawienia DNS, ustawienia kierowania ruchu, ustawienia monitorowania punktu końcowego i listę punktów końcowych usługi, do których ruch jest kierowany.

Każdy profil Menedżera ruchu jest reprezentowana przez zasób typu "TrafficManagerProfiles". Na poziomie interfejsu API REST dla każdego profilu identyfikator URI jest następująca:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Konfigurowanie programu Azure PowerShell

Poniższe instrukcje korzystają z programu Microsoft Azure PowerShell. Artykule wyjaśniono, jak instalowanie i konfigurowanie programu Azure PowerShell.

* [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)

Przykłady w tym artykule założono, że masz istniejącą grupę zasobów. Można utworzyć grupy zasobów za pomocą następującego polecenia:

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Usługa Azure Resource Manager wymaga, aby wszystkie grupy zasobów miały miejsce. Ta lokalizacja jest używana jako domyślna dla zasobów utworzone w tej grupie zasobów. Jednak ponieważ zasoby profilu Menedżera ruchu są globalne, a nie regionalne, wybór lokalizacji grupy zasobów nie ma wpływu na usługi Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu Menedżera ruchu

Aby utworzyć profil Menedżera ruchu, należy użyć `New-AzureRmTrafficManagerProfile` polecenia cmdlet:

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

W poniższej tabeli opisano parametry:

| Parametr | Opis |
| --- | --- |
| Name (Nazwa) |Nazwa zasobu dla zasobu profilu Menedżera ruchu. Profile w tej samej grupie zasobów muszą mieć unikatowe nazwy. Ta nazwa jest oddzielony od nazwy DNS dla zapytań DNS. |
| ResourceGroupName |Nazwa grupy zasobów zawiera zasób profilu. |
| TrafficRoutingMethod |Określa metodę routingu ruchu używany w celu określenia, który punkt końcowy jest zwracany w odpowiedzi do zapytania DNS. Możliwe wartości to "Performance", 'Ważone' lub 'Priority'. |
| RelativeDnsName |Określa nazwę hosta część nazwy DNS podane przez ten profil Menedżera ruchu. Ta wartość jest połączona z nazwą domeny DNS, które są używane przez usługę Azure Traffic Manager do utworzenia w pełni kwalifikowaną nazwę (FQDN) profilu. Na przykład ustawienie wartości "contoso" staje się "contoso.trafficmanager.net." |
| CZAS WYGAŚNIĘCIA |Określa DNS Time-to-Live (TTL), w sekundach. Ten czas wygaśnięcia informuje rozpoznawania nazw lokalnych DNS i klientów DNS, jak długo pamięci podręcznej odpowiedzi DNS dla tego profilu usługi Traffic Manager. |
| MonitorProtocol |Określa protokół używany do monitorowania kondycji punktu końcowego. Możliwe wartości to "HTTP" i "HTTPS". |
| MonitorPort |Określa port TCP używany do monitorowania kondycji punktu końcowego. |
| MonitorPath |Określa ścieżkę względną nazwę domeny punktu końcowego używaną do sondy kondycji punktu końcowego. |

Polecenie cmdlet tworzy profil Menedżera ruchu na platformie Azure i zwraca odpowiedni obiekt profilu do programu PowerShell. W tym momencie profil zastępczy nie zawiera żadnych punktów końcowych. Aby uzyskać więcej informacji dotyczących dodawania punktów końcowych profilu Menedżera ruchu, zobacz [Dodawanie punktów końcowych usługi Traffic Manager](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Pobierz profil Menedżera ruchu

Aby uzyskać dostęp do istniejącego obiektu profilu Menedżera ruchu, użyj `Get-AzureRmTrafficManagerProfle` polecenia cmdlet:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

To polecenie cmdlet zwraca obiekt profilu Menedżera ruchu.

## <a name="update-a-traffic-manager-profile"></a>Zaktualizuj profil Menedżera ruchu

Modyfikowanie profilów usługi Traffic Manager następujący proces krok 3:

1. Pobrać za pomocą profilu `Get-AzureRmTrafficManagerProfile` lub Użyj profilu zwrócony przez `New-AzureRmTrafficManagerProfile`.
2. Modyfikowanie profilu. Można dodać i usunąć punkty końcowe lub zmień parametry punktu końcowego lub profil. Te zmiany są operacji w trybie offline. Zmieniasz lokalnego obiektu w pamięci, która reprezentuje profilu.
3. Zatwierdź zmiany przy użyciu `Set-AzureRmTrafficManagerProfile` polecenia cmdlet.

Wszystkie właściwości profilu można zmienić z wyjątkiem RelativeDnsName profilu. Aby zmienić RelativeDnsName, należy usunąć profil i nowy profil z nową nazwą.

W poniższym przykładzie pokazano, jak zmienić czas wygaśnięcia w profilu:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Istnieją trzy typy punktów końcowych usługi Traffic Manager:

1. **Punkty końcowe systemu Azure** są usługi hostowanej na platformie Azure
2. **Zewnętrzne punkty końcowe** usług hostowanych poza platformą Azure
3. **Zagnieżdżone punkty końcowe** są używane do skonstruowania zagnieżdżone hierarchie profilów usługi Traffic Manager. Punkty końcowe zagnieżdżonych Włącz zaawansowane konfiguracje routingu ruchu w kontekście aplikacji złożonych.

We wszystkich przypadkach trzy punkty końcowe mogą być dodawane na dwa sposoby:

1. Przy użyciu kroku 3 procedury opisane wcześniej. Zaletą tej metody jest, że wiele zmian punktu końcowego można podjąć w jednej aktualizacji.
2. Za pomocą polecenia cmdlet New-AzureRmTrafficManagerEndpoint. To polecenie cmdlet dodaje punkt końcowy do istniejącego profilu Menedżera ruchu w ramach jednej operacji.

## <a name="adding-azure-endpoints"></a>Dodawanie punkty końcowe systemu Azure

Punkty końcowe systemu Azure odwoływać się do usług hostowanych na platformie Azure. Obsługiwane są dwa typy punkty końcowe systemu Azure:

1. Azure Web Apps
2. Azure zasoby publicznego adresu IP (które można dołączyć do równoważenia obciążenia lub maszyny wirtualnej karty Sieciowej). Publicznego adresu IP musi mieć nazwę DNS przypisane do użycia w usłudze Traffic Manager.

W każdym przypadku:

* Usługa jest określona za pomocą parametru "element targetResourceId" `Add-AzureRmTrafficManagerEndpointConfig` lub `New-AzureRmTrafficManagerEndpoint`.
* "Target" i "EndpointLocation" są implikowana przez element TargetResourceId.
* Określanie "waga" jest opcjonalne. Wagi są używane tylko, jeśli profil jest skonfigurowany przy użyciu metody routingu ruchu "Ważone". W przeciwnym razie są ignorowane. Jeśli jest określony, wartość musi być liczbą z zakresu od 1 do 1000. Wartość domyślna to "1".
* Określanie 'Priority' jest opcjonalna. Priorytety są używane tylko, jeśli profil jest skonfigurowany przy użyciu metody routingu ruchu 'Priority'. W przeciwnym razie są ignorowane. Prawidłowe są wartości z zakresu od 1 do 1000 o niższych wartościach, wskazując wyższy priorytet. Jeśli określone dla punktów końcowych, musi być określona dla wszystkich punktów końcowych. Pominięcie wartości domyślne, zaczynając od "1" są stosowane w kolejności, czy są wyświetlane punkty końcowe.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Przykład 1: Dodawanie punktów końcowych aplikacji sieci Web przy użyciu `Add-AzureRmTrafficManagerEndpointConfig`

W tym przykładzie, możemy utworzyć profil Menedżera ruchu i dodaj dwa punkty końcowe aplikacji sieci Web, przy użyciu `Add-AzureRmTrafficManagerEndpointConfig` polecenia cmdlet.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Przykład 2: Dodawanie punktu końcowego publicznego adresu IP za pomocą `New-AzureRmTrafficManagerEndpoint`

W tym przykładzie zasób publiczny adres IP jest dodane do profilu usługi Traffic Manager. Publiczny adres IP musi mieć nazwę DNS skonfigurowane i może być powiązana do karty Sieciowej maszyny wirtualnej lub równoważenia obciążenia.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Dodawanie zewnętrzne punkty końcowe

Menedżer ruchu używa zewnętrzne punkty końcowe umożliwiających kierowanie ruchem do usług hostowanych poza platformą Azure. Zgodnie z punkty końcowe systemu Azure, zewnętrzne punkty końcowe można dodać albo przy użyciu `Add-AzureRmTrafficManagerEndpointConfig` następuje `Set-AzureRmTrafficManagerProfile`, lub `New-AzureRMTrafficManagerEndpoint`.

Podczas określania zewnętrzne punkty końcowe:

* Należy określić nazwę domeny punktu końcowego za pomocą parametru 'Target'
* Jeśli używana jest metoda routingu ruchu "Performance", "EndpointLocation" jest wymagany. W przeciwnym razie jest opcjonalne. Wartość musi być [Nazwa Nieprawidłowa regionu Azure](https://azure.microsoft.com/regions/).
* "Waga" i 'Priority' są opcjonalne.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Przykład 1: Dodawanie zewnętrzne punkty końcowe przy użyciu `Add-AzureRmTrafficManagerEndpointConfig` i `Set-AzureRmTrafficManagerProfile`

W tym przykładzie możemy utworzyć profil Menedżera ruchu, dodaj dwa zewnętrzne punkty końcowe i zatwierdzić zmiany.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Przykład 2: Dodawanie zewnętrzne punkty końcowe przy użyciu `New-AzureRmTrafficManagerEndpoint`

W tym przykładzie dodamy zewnętrznego punktu końcowego do istniejącego profilu. Profil zostanie określona przy użyciu nazwy grupy profilu i zasobów.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Dodawanie punktów końcowych 'Nested'

Każdy profil usługi Traffic Manager określa pojedynczej metody routingu ruchu. Istnieją jednak scenariusze, które wymagają bardziej złożone routingu ruchu niż routingu udostępniane przez jeden profil Menedżera ruchu. Można zagnieżdżać profilów usługi Traffic Manager do łączenia z zalet więcej niż jedna metoda routingu ruchu. Zagnieżdżone Profile umożliwiają zastąpienie zachowania domyślnego menedżera ruchu do obsługi większych i bardziej złożone wdrożenia aplikacji. Aby uzyskać bardziej szczegółowe przykłady, zobacz [profilów usługi Traffic Manager zagnieżdżone](traffic-manager-nested-profiles.md).

Zagnieżdżone punkty końcowe zostały skonfigurowane w profilu nadrzędnej, przy użyciu typu określonego punktu końcowego "NestedEndpoints". Podczas określania zagnieżdżonych punktów końcowych:

* Należy określić punkt końcowy za pomocą parametru "element targetResourceId"
* Jeśli używana jest metoda routingu ruchu "Performance", "EndpointLocation" jest wymagany. W przeciwnym razie jest opcjonalne. Wartość musi być [Nazwa Nieprawidłowa regionu Azure](http://azure.microsoft.com/regions/).
* "Waga" i "Priority" są opcjonalne, jak w przypadku punkty końcowe systemu Azure.
* Parametr "MinChildEndpoints" jest opcjonalny. Wartość domyślna to "1". Jeśli liczba dostępnych punktów końcowych spadnie poniżej tego progu, profil nadrzędnego uwzględnia profilu podrzędny "niższa" i przekierowywanie ruchu do punktów końcowych w profilu nadrzędnej.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Przykład 1: Dodawanie zagnieżdżonych punktów końcowych przy użyciu `Add-AzureRmTrafficManagerEndpointConfig` i `Set-AzureRmTrafficManagerProfile`

W tym przykładzie firma Microsoft Tworzenie nowej usługi Traffic Manager nadrzędnym a podrzędnym profilów, Dodaj dziecko jako punktu końcowego zagnieżdżonych do nadrzędnego i zatwierdzić zmiany.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Jednak w tym przykładzie firma Microsoft nie dodano żadnych punktów końcowych profilów podrzędnej lub nadrzędnej.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Przykład 2: Dodawanie zagnieżdżonych punktów końcowych przy użyciu `New-AzureRmTrafficManagerEndpoint`

W tym przykładzie dodania istniejący profil podrzędnych jako punktu końcowego zagnieżdżonych do istniejącego profilu nadrzędnej. Profil zostanie określona przy użyciu nazwy grupy profilu i zasobów.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Dodawanie punktów końcowych z innej subskrypcji

Menedżer ruchu może współpracować z punktów końcowych z różnych subskrypcji. Musisz przełączyć się do subskrypcji z punktem końcowym, który chcesz dodać do pobierania danych wejściowych wymaganych do usługi Traffic Manager. Następnie należy przełączyć się do subskrypcji z profilem Menedżera ruchu i Dodaj do niej encpoint. Poniższym przykładzie pokazano, jak to zrobić za pomocą publicznego adresu IP.

```powershell
Set-AzureRmContext -SubscriptionId $EndpointSubscription
$ip = Get-AzureRmPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzureRmContext -SubscriptionId $trafficmanagerSubscription
New-AzureRmTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Aktualizowanie punktu końcowego Menedżera ruchu

Istnieją dwa sposoby aktualizacji istniejącego punktu końcowego Menedżera ruchu:

1. Przy użyciu profilu Menedżera ruchu `Get-AzureRmTrafficManagerProfile`, zaktualizować właściwości punktu końcowego w profilu i zatwierdź zmiany przy użyciu `Set-AzureRmTrafficManagerProfile`. Ta metoda ma tę zaletę, można zaktualizować więcej niż jeden punkt końcowy w ramach jednej operacji.
2. Przy użyciu punktu końcowego Menedżera ruchu `Get-AzureRmTrafficManagerEndpoint`, zaktualizować właściwości punktu końcowego i zatwierdź zmiany przy użyciu `Set-AzureRmTrafficManagerEndpoint`. Ta metoda jest prostsza, ponieważ nie jest wymagane, przeprowadzane jest indeksowanie do tablicy punktów końcowych w profilu.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Przykład 1: Aktualizowanie punktów końcowych przy użyciu `Get-AzureRmTrafficManagerProfile` i `Set-AzureRmTrafficManagerProfile`

W tym przykładzie mamy zmodyfikować priorytet na dwa punkty końcowe w ramach istniejącego profilu.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Przykład 2: Aktualizowanie punktu końcowego za pomocą `Get-AzureRmTrafficManagerEndpoint` i `Set-AzureRmTrafficManagerEndpoint`

W tym przykładzie mamy zmodyfikować wagę jeden punkt końcowy w istniejącego profilu.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Włączanie i wyłączanie punktów końcowych i profili

Menedżer ruchu umożliwia poszczególne punkty końcowe, należy włączyć i wyłączone, oraz umożliwiając Włączanie i wyłączanie całego profilów.
Te można wprowadzać zmiany pobierania/aktualizowania/ustawienia punktu końcowego lub profilu zasobów. Może usprawnić tych typowych operacji, są one również obsługiwane za pośrednictwem dedykowanej polecenia cmdlet.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Przykład 1: Włączanie i wyłączanie profilu Menedżera ruchu

Aby włączyć profilu Menedżera ruchu, należy użyć `Enable-AzureRmTrafficManagerProfile`. Profil można określić za pomocą obiektu profilu. Obiekt profilu mogą zostać przekazane za pośrednictwem potoku lub za pomocą "-TrafficManagerProfile" parametru. W tym przykładzie nazwę grupy profilu i zasobu określono profil.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Aby wyłączyć profil Menedżera ruchu:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Polecenie cmdlet Disable-AzureRmTrafficManagerProfile monituje o potwierdzenie. Ten monit można pomijać przy użyciu '-Force "parametru.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Przykład 2: Włączanie i wyłączanie punktu końcowego Menedżera ruchu

Aby włączyć punkt końcowy Menedżera ruchu, należy użyć `Enable-AzureRmTrafficManagerEndpoint`. Istnieją dwa sposoby określania punktu końcowego

1. Przy użyciu obiektu TrafficManagerEndpoint przekazywane za pośrednictwem potoku lub "-TrafficManagerEndpoint" parametru
2. Przy użyciu Nazwa punktu końcowego, typ punktu końcowego, nazwa profilu i nazwa grupy zasobów:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Podobnie Aby wyłączyć punkt końcowy Menedżera ruchu:

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Jak `Disable-AzureRmTrafficManagerProfile`, `Disable-AzureRmTrafficManagerEndpoint` polecenie cmdlet monituje o potwierdzenie. Ten monit można pomijać przy użyciu '-Force "parametru.

## <a name="delete-a-traffic-manager-endpoint"></a>Usuwanie punktu końcowego Menedżera ruchu

Aby usunąć poszczególne punkty końcowe, użyj `Remove-AzureRmTrafficManagerEndpoint` polecenia cmdlet:

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

To polecenie cmdlet wyświetli monit o potwierdzenie. Ten monit można pomijać przy użyciu '-Force "parametru.

## <a name="delete-a-traffic-manager-profile"></a>Usuwanie profilu Menedżera ruchu

Aby usunąć profil Menedżera ruchu, należy użyć `Remove-AzureRmTrafficManagerProfile` polecenia cmdlet, określenie nazwy grupy profilu i zasobów:

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

To polecenie cmdlet wyświetli monit o potwierdzenie. Ten monit można pomijać przy użyciu '-Force "parametru.

Profil do usunięcia można także określić przy użyciu obiektu profilu:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Ta sekwencja również mogą być przekazywane w potoku:

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie Menedżera ruchu](traffic-manager-monitoring.md)

[Zagadnienia dotyczące wydajności usługi Traffic Manager](traffic-manager-performance-considerations.md)
