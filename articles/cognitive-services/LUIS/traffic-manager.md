---
title: Aby zwiększyć przydział punktu końcowego w języku opis (LUIS) - Azure, użyj Menedżera ruchu Microsoft Azure | Dokumentacja firmy Microsoft
description: Umożliwia przydziału punktu końcowego rozmieszczenie do kilku subskrypcje w języku opis (LUIS) aby zwiększyć przydział punktu końcowego Menedżera ruchu Microsoft Azure
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/07/2018
ms.author: v-geberr
ms.openlocfilehash: 8c8228b13c972c65596f0389e2fdfde585f8a742
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110317"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Umożliwia zarządzanie przydziału punktu końcowego za pośrednictwem kluczy Menedżera ruchu Microsoft Azure
Opis języka (LUIS) oferuje możliwość zwiększyć ten przydział żądania punktu końcowego po przekroczeniu przydziału jednego klucza. Jest to zrobić, tworząc więcej kluczy dla LUIS oraz dodanie ich do aplikacji LUIS na **publikowania** strony **zasobów i klucze** sekcji. 

Aplikacja kliencka musi zarządzać ruch między kluczy. LUIS nie spełniają tę funkcję. 

W tym artykule opisano sposób zarządzania ruch między kluczy Azure [Traffic Manager][traffic-manager-marketing]. Musi już przeszkolone i opublikowanych aplikacji LUIS. Jeśli nie masz, wykonaj wbudowane domeny [szybkiego startu](luis-get-started-create-app.md). 

## <a name="connect-to-powershell-in-the-azure-portal"></a>Połącz do programu PowerShell w portalu Azure
W [Azure] [ azure-portal] portalu, Otwórz okno programu PowerShell. Ikona okno programu PowerShell jest **> _** w górnym pasku nawigacyjnym. Za pomocą programu PowerShell z portalu, możesz pobrać najnowszą wersję programu PowerShell i użytkownik jest uwierzytelniony. Wymaga środowiska PowerShell w portalu [usługi Azure Storage](https://azure.microsoft.com/services/storage/) konta. 

![Otwórz portal zrzut ekranu Azure z okna programu Powershell](./media/traffic-manager/azure-portal-powershell.png)

W poniższych sekcjach przedstawiono użycie [poleceń cmdlet programu PowerShell Menedżera ruchu](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/?view=azurermps-6.2.0#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Tworzenie grupy zasobów platformy Azure przy użyciu programu PowerShell
Przed utworzeniem zasobów platformy Azure, Utwórz grupę zasobów, aby zawierała wszystkie zasoby. Określ nazwę grupy zasobów `luis-traffic-manager` i użyj region jest `West US`. Region grupy zasobów są przechowywane metadane dotyczące grupy. Go nie zwolnić zasoby jeśli znajdują się w innym regionie. 

Tworzenie grupy zasobów z **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.2.0)** polecenia cmdlet:

```PowerShell
New-AzureRmResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Utwórz LUIS kluczy w celu zwiększenia limitu całkowitej punktu końcowego
1. W portalu Azure, należy utworzyć dwa **opis języka** kluczy w `West US` i jeden w `East US`. Użyj istniejącej grupy zasobów, utworzony w poprzedniej sekcji o nazwie `luis-traffic-manager`. 

    ![Zrzut ekranu Azure portalu o dwa klucze LUIS w grupie zasobów Menedżera luis ruchu](./media/traffic-manager/luis-keys.png)

2. W [LUIS] [ LUIS] witryny sieci Web na **publikowania** strony, Dodaj klucze do aplikacji i opublikować aplikację. 

    ![Zrzut ekranu LUIS portal za pomocą dwóch kluczy LUIS na strony publikowania](./media/traffic-manager/luis-keys-in-luis.png)

    Przykładowy adres URL w **punktu końcowego** kolumna używa żądanie GET z kluczem punktu końcowego jako parametr zapytania. Skopiuj dwa klucze nowe adresy URL punktów końcowych. Są one używane w ramach konfiguracji Menedżera ruchu w dalszej części tego artykułu.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Zarządzaj żądaniami punktu końcowego LUIS między klucze Menedżera ruchu
Menedżer ruchu tworzy nowy punkt dostępu DNS dla punktów końcowych. Nie działa jako brama lub serwer proxy, ale wyłącznie na poziomie DNS. W tym przykładzie nie zmienia żadnych rekordów DNS. Biblioteka DNS używa do komunikacji z usługą Traffic Manager można uzyskać właściwego punktu końcowego dla tego konkretnego żądania. _Każdy_ żądania przeznaczonych dla LUIS wymaga najpierw żądanie Menedżera ruchu, aby określić, które LUIS punktu końcowego. 

### <a name="polling-uses-luis-endpoint"></a>Sondowanie używa LUIS punktu końcowego
Menedżer ruchu sonduje punkty końcowe okresowo, aby upewnić się, że punkt końcowy jest nadal dostępny. Adres URL Menedżera ruchu sondowania musi być dostępny z żądania GET i zwrócić 200. Adres URL punktu końcowego na **publikowania** strony robi to. Ponieważ każdy klucz punkt końcowy ma inną trasę i parametrów ciągu zapytania, klucz każdego punktu końcowego musi sondowania inną ścieżkę. Zawsze ankiety Traffic Manager kosztuje żądania limitu przydziału. Parametr ciągu zapytania **q** LUIS punktu końcowego jest utterance wysyłane do LUIS. Ten parametr, zamiast wysyłać utterance służy do dodawania sondowania Traffic Manager do dziennika punktu końcowego LUIS jako technika debugowania podczas pobierania usługi Traffic Manager skonfigurowane.

Ponieważ każdy punkt końcowy LUIS musi mieć własny ścieżki, musi on własny profil Menedżera ruchu. Aby można było zarządzać w profilach, tworzenia [ _zagnieżdżonych_ Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) architektury. Jeden profil nadrzędnego wskazuje profile elementów podrzędnych i zarządzanie ruchem między nimi.

Po skonfigurowaniu usługi Traffic Manager Pamiętaj, aby zmienić ścieżkę do używania funkcji rejestrowania = parametru ciągu zapytania false, więc nie zapełnienia dziennika z sondowania.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Skonfiguruj Menedżera ruchu z zagnieżdżonych profilów
Poniższe sekcje utworzyć dwa profile podrzędnych, jeden dla klucza LUIS Wschód i jeden dla klucza LUIS zachodnie. Następnie tworzony jest profil nadrzędne i podrzędne dwa profile są dodawane do profilu nadrzędnej. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Tworzenie profilu Menedżera ruchu wschodnie stany USA przy użyciu programu PowerShell
Aby utworzyć profil Menedżera ruchu wschodnie stany USA, istnieje kilka czynności: Tworzenie profilu, Dodaj punkt końcowy i ustaw punkt końcowy. Profil usługi Traffic Manager może mieć wiele punktów końcowych, ale każdy punkt końcowy ma taką samą ścieżkę weryfikacji. Ponieważ adresy URL punktów końcowych LUIS subskrypcji Wschodnią i zachodnią są różne z powodu klucza regionu i punktu końcowego, każdy punkt końcowy LUIS musi mieć jeden punkt końcowy w profilu. 

1. Utwórz profil z **[AzureRmTrafficManagerProfile nowy](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** polecenia cmdlet

    Użyj następującego polecenia cmdlet, aby utworzyć profil. Upewnij się zmienić `appIdLuis` i `subscriptionKeyLuis`. SubscriptionKey jest wschód nam LUIS klucza. Jeśli ścieżka jest niepoprawny, przy tym LUIS aplikacji identyfikator i punktu końcowego klucz, sondowania Menedżera ruchu jest stan `degraded` ponieważ ruch zarządzania pomyślnie nie można żądać LUIS punktu końcowego. Upewnij się, że wartość `q` jest `traffic-manager-east` pozwala zobaczyć tę wartość w dziennikach LUIS punktu końcowego.

    ```PowerShell
    $eastprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    W następującej tabeli opisano każdej zmiennej w polecenia cmdlet:
    
    |Parametr konfiguracji|Nazwa lub wartość zmiennej|Przeznaczenie|
    |--|--|--|
    |-Name|Luis profilu eastus|Nazwa menedżera ruchu w portalu Azure|
    |-ResourceGroupName|Menedżer Luis ruchu|Utworzony w poprzedniej sekcji|
    |-TrafficRoutingMethod|Wydajność|Aby uzyskać więcej informacji, zobacz [metody routingu ruchu Menedżera][routing-methods]. Jeśli używasz wydajności, żądanie adresu URL Menedżera ruchu muszą pochodzić z regionu użytkownika. Jeśli pośrednictwa chatbot lub innej aplikacji, odpowiada chatbot naśladować regionu w wywołaniu Menedżera ruchu. |
    |-RelativeDnsName|Luis-dns-eastus|Jest poddomeną usługi: luis-dns-eastus.trafficmanager.net|
    |Wartość Ttl-|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port i protokół LUIS jest protokołu HTTPS i 443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Zastąp <appIdLuis> i <subscriptionKeyLuis> z własne wartości.|
    
    Pomyślne żądanie ma nie otrzymano odpowiedzi.

2. Dodaj punkt końcowy wschodnie stany USA z **[AzureRmTrafficManagerEndpointConfig Dodaj](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/add-azurermtrafficmanagerendpointconfig?view=azurermps-6.2.0)** polecenia cmdlet

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    W następującej tabeli opisano każdej zmiennej w polecenia cmdlet:

    |Parametr konfiguracji|Nazwa lub wartość zmiennej|Przeznaczenie|
    |--|--|--|
    |-EndpointName|Luis punkt końcowy Region Azji i Pacyfiku|Nazwa punktu końcowego wyświetlane w obszarze profil|
    |-TrafficManagerProfile|$eastprofile|Użyj obiektu profilu utworzonego w kroku 1.|
    |— Typ|ExternalEndpoints|Aby uzyskać więcej informacji, zobacz [punktu końcowego Menedżera ruchu][traffic-manager-endpoints] |
    |-Docelowego|eastus.API.cognitive.microsoft.com|Jest to domena LUIS punktu końcowego.|
    |-EndpointLocation|"eastus"|Region punktu końcowego|
    |-EndpointStatus|Enabled (Włączony)|Włącz punktu końcowego, po utworzeniu|

    Odpowiedź oznaczająca Powodzenie wygląda następująco:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Ustaw punkt końcowy wschodnie stany USA z **[AzureRmTrafficManagerProfile zestaw](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/set-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** polecenia cmdlet

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Odpowiedź oznaczająca Powodzenie będzie taka sama odpowiedź w kroku 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Tworzenie profilu Menedżera ruchu zachodnie stany USA przy użyciu programu PowerShell
Aby utworzyć profil Menedżera ruchu zachodnie stany USA, wykonaj te same kroki: Tworzenie profilu, Dodaj punkt końcowy i ustaw punkt końcowy.

1. Utwórz profil z **[AzureRmTrafficManagerProfile nowy](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** polecenia cmdlet

    Użyj następującego polecenia cmdlet, aby utworzyć profil. Upewnij się zmienić `appIdLuis` i `subscriptionKeyLuis`. SubscriptionKey jest wschód nam LUIS klucza. Jeśli ścieżka nie jest poprawna, w tym aplikacji LUIS klucza identyfikator i punktu końcowego, sondowania Menedżera ruchu jest stan `degraded` ponieważ ruch zarządzania pomyślnie nie można żądać LUIS punktu końcowego. Upewnij się, że wartość `q` jest `traffic-manager-west` pozwala zobaczyć tę wartość w dziennikach LUIS punktu końcowego.

    ```PowerShell
    $westprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    W następującej tabeli opisano każdej zmiennej w polecenia cmdlet:
    
    |Parametr konfiguracji|Nazwa lub wartość zmiennej|Przeznaczenie|
    |--|--|--|
    |-Name|Luis profilu westus|Nazwa menedżera ruchu w portalu Azure|
    |-ResourceGroupName|Menedżer Luis ruchu|Utworzony w poprzedniej sekcji|
    |-TrafficRoutingMethod|Wydajność|Aby uzyskać więcej informacji, zobacz [metody routingu ruchu Menedżera][routing-methods]. Jeśli używasz wydajności, żądanie adresu URL Menedżera ruchu muszą pochodzić z regionu użytkownika. Jeśli pośrednictwa chatbot lub innej aplikacji, odpowiada chatbot naśladować regionu w wywołaniu Menedżera ruchu. |
    |-RelativeDnsName|Luis-dns-westus|Jest poddomeną usługi: luis-dns-westus.trafficmanager.net|
    |Wartość Ttl-|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port i protokół LUIS jest protokołu HTTPS i 443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Zastąp <appId> i <subscriptionKey> z własne wartości. Należy pamiętać, że ten klucz punktu końcowego jest inny niż klucz punktu końcowego Region Azji i Pacyfiku|
    
    Pomyślne żądanie ma nie otrzymano odpowiedzi.

2. Dodaj punkt końcowy zachodnie stany USA z **[AzureRmTrafficManagerEndpointConfig Dodaj](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** polecenia cmdlet

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    W następującej tabeli opisano każdej zmiennej w polecenia cmdlet:

    |Parametr konfiguracji|Nazwa lub wartość zmiennej|Przeznaczenie|
    |--|--|--|
    |-EndpointName|punkt końcowy zachodnie Luis|Nazwa punktu końcowego wyświetlane w obszarze profil|
    |-TrafficManagerProfile|$westprofile|Użyj obiektu profilu utworzonego w kroku 1.|
    |— Typ|ExternalEndpoints|Aby uzyskać więcej informacji, zobacz [punktu końcowego Menedżera ruchu][traffic-manager-endpoints] |
    |-Docelowego|westus.API.cognitive.microsoft.com|Jest to domena LUIS punktu końcowego.|
    |-EndpointLocation|"westus"|Region punktu końcowego|
    |-EndpointStatus|Enabled (Włączony)|Włącz punktu końcowego, po utworzeniu|

    Odpowiedź oznaczająca Powodzenie wygląda następująco:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Ustaw punkt końcowy zachodnie stany USA z **[AzureRmTrafficManagerProfile zestaw](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** polecenia cmdlet

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Odpowiedź oznaczająca Powodzenie to ta sama odpowiedź w kroku 2.

### <a name="create-parent-traffic-manager-profile"></a>Tworzenie profilu Menedżera ruchu nadrzędnego
Utwórz nadrzędnego profilu Menedżera ruchu i połącz dwa profile Menedżera ruchu podrzędny element nadrzędny.

1. Utwórz profil nadrzędnego o **[AzureRmTrafficManagerProfile nowy](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** polecenia cmdlet

    ```PowerShell
    $parentprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    W następującej tabeli opisano każdej zmiennej w polecenia cmdlet:

    |Parametr konfiguracji|Nazwa lub wartość zmiennej|Przeznaczenie|
    |--|--|--|
    |-Name|Luis profilu-relacji nadrzędny-|Nazwa menedżera ruchu w portalu Azure|
    |-ResourceGroupName|Menedżer Luis ruchu|Utworzony w poprzedniej sekcji|
    |-TrafficRoutingMethod|Wydajność|Aby uzyskać więcej informacji, zobacz [metody routingu ruchu Menedżera][routing-methods]. Jeśli używasz wydajności, żądanie adresu URL Menedżera ruchu muszą pochodzić z regionu użytkownika. Jeśli pośrednictwa chatbot lub innej aplikacji, odpowiada chatbot naśladować regionu w wywołaniu Menedżera ruchu. |
    |-RelativeDnsName|Luis dns-relacji nadrzędny-|Jest poddomeną usługi: luis-dns-parent.trafficmanager.net|
    |Wartość Ttl-|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port i protokół LUIS jest protokołu HTTPS i 443|
    |-MonitorPath|`/`|Tej ścieżki nie ma znaczenia, ponieważ ścieżki podrzędnego punktu końcowego są używane zamiast tego.|

    Pomyślne żądanie ma nie otrzymano odpowiedzi.

2. Dodawanie profilu podrzędnych wschodnie stany USA do elementu nadrzędnego z **[AzureRmTrafficManagerEndpointConfig Dodaj](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** i **NestedEndpoints** typu

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    W następującej tabeli opisano każdej zmiennej w polecenia cmdlet:

    |Parametr konfiguracji|Nazwa lub wartość zmiennej|Przeznaczenie|
    |--|--|--|
    |-EndpointName|useast-punkt końcowy podrzędne|Profil Region Azji i Pacyfiku|
    |-TrafficManagerProfile|$parentprofile|Profil, aby przypisać ten punkt końcowy do|
    |— Typ|NestedEndpoints|Aby uzyskać więcej informacji, zobacz [AzureRmTrafficManagerEndpointConfig Dodaj](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |Element TargetResourceId-|$eastprofile. Identyfikator|Identyfikator profilu podrzędne|
    |-EndpointStatus|Enabled (Włączony)|Stan punktu końcowego po dodaniu do elementu nadrzędnego|
    |-EndpointLocation|"eastus"|[Nazwa regionu Azure](https://azure.microsoft.com/global-infrastructure/regions/) zasobu|
    |-MinChildEndpoints|1|Minimalna liczba podrzędnych punktami końcowymi|

    Odpowiedź oznaczająca Powodzenie wygląd podobnie do następującego i zawiera nowe `child-endpoint-useast` punktu końcowego:    

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Dodawanie profilu podrzędnych zachodnie stany USA do elementu nadrzędnego z **[AzureRmTrafficManagerEndpointConfig Dodaj](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** polecenia cmdlet i **NestedEndpoints** typu

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    W następującej tabeli opisano każdej zmiennej w polecenia cmdlet:

    |Parametr konfiguracji|Nazwa lub wartość zmiennej|Przeznaczenie|
    |--|--|--|
    |-EndpointName|uswest-punkt końcowy podrzędne|Profil Zachodnia|
    |-TrafficManagerProfile|$parentprofile|Profil, aby przypisać ten punkt końcowy do|
    |— Typ|NestedEndpoints|Aby uzyskać więcej informacji, zobacz [AzureRmTrafficManagerEndpointConfig Dodaj](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |Element TargetResourceId-|$westprofile. Identyfikator|Identyfikator profilu podrzędne|
    |-EndpointStatus|Enabled (Włączony)|Stan punktu końcowego po dodaniu do elementu nadrzędnego|
    |-EndpointLocation|"westus"|[Nazwa regionu Azure](https://azure.microsoft.com/global-infrastructure/regions/) zasobu|
    |-MinChildEndpoints|1|Minimalna liczba podrzędnych punktami końcowymi|

    Wygląd pomyślnej odpowiedzi, takich jak i obejmuje zarówno poprzedniej `child-endpoint-useast` punktu końcowego i nowych `child-endpoint-uswest` punktu końcowego:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Ustaw punkty końcowe z **[AzureRmTrafficManagerProfile zestaw](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** polecenia cmdlet 

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Odpowiedź oznaczająca Powodzenie to ta sama odpowiedź w kroku 3.

### <a name="powershell-variables"></a>Zmienne środowiska PowerShell
W poprzednich sekcjach, zostały utworzone trzy zmienne środowiska PowerShell: `$eastprofile`, `$westprofile`, `$parentprofile`. Te zmienne są używane do końca konfiguracji Menedżera ruchu. Jeśli wybierz nie utworzyć zmienne lub zapomniał lub limit czasu okna programu PowerShell, możesz użyć polecenia cmdlet programu PowerShell  **[Get-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)**, aby ponownie pobrać profilu i przypisz do niego do zmiennej. 

Zastąpienie elementów w nawiasach ostrych `<>`, przy użyciu prawidłowych wartości dla każdego z trzech profilów należy. 

```PowerShell
$<variable-name> = Get-AzureRmTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Weryfikowanie działania Menedżera ruchu
Aby sprawdzić, czy pracy profile Menedżera ruchu, profile musi mieć stan `Online` ten stan jest oparta na ścieżce sondowania punktu końcowego. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Widok nowych profilów w portalu Azure
Możesz sprawdzić, czy wszystkie trzy profile są tworzone analizując zasobów w `luis-traffic-manager` grupy zasobów.

![Zrzut ekranu Azure zasobów grupy luis--Menedżera ruchu](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Sprawdź, czy stan profilu jest w trybie Online
Menedżer ruchu sonduje ścieżka każdego punktu końcowego, aby upewnić się, że jest w trybie online. Jeśli jest w trybie online, stan profile podrzędne są `Online`. Jest on wyświetlany na **omówienie** dla każdego profilu. 

![Zrzut ekranu w usłudze Azure Traffic Manager profilu przedstawiający Monitor stanu elementu Online — omówienie](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Sprawdź poprawność sondowania działania Menedżera ruchu
Innym sposobem weryfikacji sondowania działania Menedżera ruchu jest z dziennikami LUIS punktu końcowego. Na [LUIS] [ LUIS] lista aplikacji witryny sieci Web pozycję Eksportuj Dziennik punktu końcowego dla aplikacji. Ponieważ usługi Traffic Manager często sonduje dwa punkty końcowe, Brak wpisów w dziennikach nawet, jeśli zostały one tylko na kilka minut. Pamiętaj, aby wyszukać wpisów, gdy zapytanie rozpoczyna się od `traffic-manager-`.

```text
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Zweryfikować odpowiedź DNS z działania Menedżera ruchu
Aby zweryfikować, że odpowiedź DNS zwraca punkt końcowy LUIS, żądanie ruchu Zarządzanie profilami nadrzędnej DNS za pomocą biblioteki klienta DNS. Nazw DNS w nadrzędnej profilu jest `luis-dns-parent.trafficmanager.net`.

Poniższy kod Node.js zażąda profilu nadrzędny i zwraca punkt końcowy LUIS:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Odpowiedź oznaczająca Powodzenie z punktem końcowym LUIS jest:

```cmd
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Użyj profilu usługi Traffic Manager nadrzędnego
Aby zarządzać ruchem w obrębie punktów końcowych, trzeba Wstawianie wywołania w systemie DNS Menedżera ruchu można znaleźć LUIS punktu końcowego. To wywołanie jest dokonywane przy każdym żądaniu punktu końcowego LUIS i musi zostać symulować lokalizacji geograficznej użytkownika aplikacji klienckiej LUIS. Dodaj kod odpowiedzi DNS Between LUIS aplikacji klienckiej i żądania do LUIS prognozowania punktu końcowego. 


## <a name="clean-up"></a>Czyszczenie
Usuń dwa klucze punktu końcowego LUIS, trzy profile Menedżera ruchu i grupę zasobów, która zawiera te zasoby pięć. Można to zrobić w portalu Azure. Pięć zasoby zostaną usunięte z listy zasobów. Następnie usuń grupę zasobów. 

## <a name="next-steps"></a>Kolejne kroki

Przegląd [oprogramowanie pośredniczące](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) opcje BotFramework w wersji 4, aby zrozumieć, jak można dodać ten kod zarządzania ruchem do BotFramework bot. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
