---
title: Zwiększanie przydziału punktu końcowego — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Zrozumienie języka (LUIS) oferuje możliwość zwiększenia przydziału żądania punktu końcowego poza przydział jednego klucza. Odbywa się to przez utworzenie większej liczby kluczy dla usługi LUIS i dodanie ich do aplikacji usługi LUIS na stronie **Publikowania** w **zasoby i klucze** sekcji.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: c4ea9c5663755a4feb1693dd925d99b10c466140
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70256604"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Zarządzanie przydziałem punktów końcowych między kluczami za pomocą usługi Microsoft Azure Traffic Manager
Zrozumienie języka (LUIS) oferuje możliwość zwiększenia przydziału żądania punktu końcowego poza przydział jednego klucza. Odbywa się to przez utworzenie większej liczby kluczy dla usługi LUIS i dodanie ich do aplikacji usługi LUIS na stronie **Publikowania** w **zasoby i klucze** sekcji. 

Aplikacja kliencka musi zarządzać ruchem między kluczami. Usługa LUIS tego nie robi. 

W tym artykule wyjaśniono, jak zarządzać ruchem między kluczami za pomocą usługi Azure [Traffic Manager][traffic-manager-marketing]. Musisz już mieć przeszkoloną i opublikowaną aplikację usługi LUIS. Jeśli go nie masz, postępuj zgodnie z programem [Szybki start](luis-get-started-create-app.md)domeny prekompilowana . 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Łączenie się z programem PowerShell w witrynie Azure portal
W witrynie [Azure][azure-portal] portal otwórz okno programu PowerShell. Ikoną okna programu PowerShell jest **>_** na górnym pasku nawigacyjnym. Za pomocą programu PowerShell z portalu, otrzymasz najnowszą wersję programu PowerShell i są uwierzytelnione. Program PowerShell w portalu wymaga konta [usługi Azure Storage.](https://azure.microsoft.com/services/storage/) 

![Zrzut ekranu przedstawiający witrynę Azure portal z otwartym oknem programu Powershell](./media/traffic-manager/azure-portal-powershell.png)

W poniższych sekcjach używane są [polecenia cmdlet programu Traffic Manager programu PowerShell](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Tworzenie grupy zasobów platformy Azure za pomocą programu PowerShell
Przed utworzeniem zasobów platformy Azure należy utworzyć grupę zasobów zawierającą wszystkie zasoby. Nazwij `luis-traffic-manager` grupę zasobów `West US`i użyj regionu jest . Region grupy zasobów przechowuje metadane dotyczące grupy. Nie spowolni zasobów, jeśli znajdują się one w innym regionie. 

Utwórz grupę zasobów za pomocą polecenia cmdlet **[New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)**

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Tworzenie kluczy usługi LUIS w celu zwiększenia całkowitego przydziału punktu końcowego
1. W witrynie Azure portal utwórz dwa `West US` klucze **opisu języka,** jeden w i jeden w . `East US` Użyj istniejącej grupy zasobów utworzonej w `luis-traffic-manager`poprzedniej sekcji o nazwie . 

    ![Zrzut ekranu przedstawiający witrynę Azure portal z dwoma kluczami usługi LUIS w grupie zasobów menedżera usługi Luis-traffic](./media/traffic-manager/luis-keys.png)

2. W witrynie sieci Web [usługi LUIS][LUIS] w sekcji **Zarządzanie** na stronie Zasoby **platformy Azure** przypisz klucze do aplikacji i ponownie opublikuj aplikację, wybierając przycisk **Publikuj** w prawym górnym menu. 

    Przykładowy adres URL w kolumnie **punktu końcowego** używa żądania GET z kluczem punktu końcowego jako parametru kwerendy. Skopiuj adresy URL punktów końcowych dwóch nowych kluczy. Są one używane jako część konfiguracji usługi Traffic Manager w dalszej części tego artykułu.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Zarządzanie żądaniami punktów końcowych usługi LUIS za pomocą menedżera ruchu
Usługa Traffic Manager tworzy nowy punkt dostępu DNS dla punktów końcowych. Nie działa jako brama lub serwer proxy, ale ściśle na poziomie DNS. W tym przykładzie nie zmienia żadnych rekordów DNS. Używa biblioteki DNS do komunikowania się z usługą Traffic Manager w celu uzyskania poprawnego punktu końcowego dla tego konkretnego żądania. _Każde_ żądanie przeznaczone dla usługi LUIS najpierw wymaga żądania usługi Traffic Manager, aby określić, który punkt końcowy usługi LUIS do użycia. 

### <a name="polling-uses-luis-endpoint"></a>Sondowanie używa punktu końcowego usługi LUIS
Usługa Traffic Manager okresowo sonduje punkty końcowe, aby upewnić się, że punkt końcowy jest nadal dostępny. Sondowany adres URL Menedżera ruchu musi być dostępny za pomocą żądania GET i zwrócić 200. Adres URL punktu końcowego na stronie **Publikowania** to robi. Ponieważ każdy klucz punktu końcowego ma inne parametry trasy i ciągu zapytania, każdy klucz punktu końcowego wymaga innej ścieżki sondowania. Za każdym razem, gdy usługa Traffic Manager sonduje, kosztuje żądanie przydziału. Parametr ciągu zapytania **q** punktu końcowego usługi LUIS jest wypowiedź wysłana do usługi LUIS. Ten parametr, zamiast wysyłania wypowiedź, służy do dodawania usługi Traffic Manager sondowania do dziennika punktu końcowego usługi LUIS jako techniki debugowania podczas uzyskiwania usługi Traffic Manager skonfigurowany.

Ponieważ każdy punkt końcowy usługi LUIS potrzebuje własnej ścieżki, potrzebuje własnego profilu usługi Traffic Manager. Aby zarządzać między profilami, należy utworzyć [ _zagnieżdżoną_ ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) architekturę usługi Traffic Manager. Jeden profil nadrzędny wskazuje profile podrzędne i zarządza ruchem między nimi.

Po skonfigurowaniu usługi Traffic Manager należy pamiętać o zmianie ścieżki, aby użyć parametru logging=false query string, aby dziennik nie zapełniał się sondowaniem.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Konfigurowanie usługi Traffic Manager z profilami zagnieżdżanym
W poniższych sekcjach utworzy się dwa profile podrzędne, jeden dla klucza usługi LUIS wschodnia i jeden dla klucza zachodniej usługi LUIS. Następnie tworzony jest profil nadrzędny i dwa profile podrzędne są dodawane do profilu nadrzędnego. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Tworzenie profilu east us traffic manager za pomocą programu PowerShell
Aby utworzyć profil East US Traffic Manager, istnieje kilka kroków: tworzenie profilu, dodawanie punktu końcowego i ustawianie punktu końcowego. Profil usługi Traffic Manager może mieć wiele punktów końcowych, ale każdy punkt końcowy ma tę samą ścieżkę sprawdzania poprawności. Ponieważ adresy URL punktów końcowych usługi LUIS dla subskrypcji wschodnich i zachodnich są różne ze względu na klucz regionu i punktu końcowego, każdy punkt końcowy usługi LUIS musi być pojedynczym punktem końcowym w profilu. 

1. Tworzenie profilu za pomocą polecenia cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    Aby utworzyć profil, użyj następującego polecenia cmdlet. Pamiętaj, aby `appIdLuis` zmienić i `subscriptionKeyLuis`. SubscriptionKey jest dla klucza usługi LUIS wschodnich stanów USA. Jeśli ścieżka nie jest poprawna, w tym identyfikator aplikacji usługi LUIS i `degraded` klucz punktu końcowego, sondowanie usługi Traffic Manager jest stanem, ponieważ zarządzanie ruchem nie może pomyślnie zażądać punktu końcowego usługi LUIS. Upewnij się, `q` że `traffic-manager-east` wartość jest, dzięki czemu można zobaczyć tę wartość w dziennikach punktu końcowego usługi LUIS.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    W tej tabeli wyjaśniono każdą zmienną w policzce cmdlet:
    
    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Name|luis-profil-eastus|Nazwa usługi Traffic Manager w witrynie Azure portal|
    |-Nazwa grupy zasobów|luis-traffic-manager|Utworzono w poprzedniej sekcji|
    |-TrafficRoutingMetoda|Wydajność|Aby uzyskać więcej informacji, zobacz [Metody routingu usługi Traffic Manager][routing-methods]. Jeśli używasz wydajności, żądanie adresu URL do Usługi Traffic Manager musi pochodzić z regionu użytkownika. W przypadku przechodzenia przez chatbota lub inną aplikację, obowiązkiem chatbota jest naśladowanie regionu w połączeniu z Menedżerem ruchu. |
    |-RelativeDnsName|luis-dns-eastus|Jest to poddomena usługi: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port i protokół usługi LUIS to HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Wymień `<appIdLuis>` i `<subscriptionKeyLuis>` z własnymi wartościami.|
    
    Pomyślne żądanie nie ma odpowiedzi.

2. Dodawanie punktu końcowego wschodnich stanów USA za pomocą polecenia cmdlet **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    W tej tabeli wyjaśniono każdą zmienną w policzce cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Nazwa punktu końcowego|luis-wschód-punkt końcowy|Nazwa punktu końcowego wyświetlana pod profilem|
    |-TrafficManagerProfile|$eastprofile|Użyj obiektu profilu utworzonego w kroku 1|
    |-Typ|Zewnętrzne punkty końcowe|Aby uzyskać więcej informacji, zobacz [Punkt końcowy usługi Traffic Manager][traffic-manager-endpoints] |
    |-Cel|eastus.api.cognitive.microsoft.com|Jest to domena punktu końcowego usługi LUIS.|
    |-EndpointLocation (Lokalizacja punktu końcowego)|"eastus"|Region punktu końcowego|
    |-EndpointStatus|Enabled (Włączony)|Włączanie punktu końcowego podczas jego tworzenia|

    Pomyślna odpowiedź wygląda następująco:

    ```console
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

3. Ustawianie punktu końcowego wschodnich stanów USA za pomocą polecenia cmdlet **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Pomyślna odpowiedź będzie taka sama odpowiedź jak krok 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Tworzenie profilu zachodniego programu Us Traffic Manager za pomocą programu PowerShell
Aby utworzyć profil Zachodniego programu Us Traffic Manager, wykonaj te same kroki: utwórz profil, dodaj punkt końcowy i ustaw punkt końcowy.

1. Tworzenie profilu za pomocą polecenia cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    Aby utworzyć profil, użyj następującego polecenia cmdlet. Pamiętaj, aby `appIdLuis` zmienić i `subscriptionKeyLuis`. SubscriptionKey jest dla klucza usługi LUIS wschodnich stanów USA. Jeśli ścieżka nie jest poprawna, w tym identyfikator aplikacji usługi LUIS i `degraded` klucz punktu końcowego, sondowanie usługi Traffic Manager jest stanem, ponieważ zarządzanie ruchem nie może pomyślnie zażądać punktu końcowego usługi LUIS. Upewnij się, `q` że `traffic-manager-west` wartość jest, dzięki czemu można zobaczyć tę wartość w dziennikach punktu końcowego usługi LUIS.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    W tej tabeli wyjaśniono każdą zmienną w policzce cmdlet:
    
    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Name|luis-profile-westus|Nazwa usługi Traffic Manager w witrynie Azure portal|
    |-Nazwa grupy zasobów|luis-traffic-manager|Utworzono w poprzedniej sekcji|
    |-TrafficRoutingMetoda|Wydajność|Aby uzyskać więcej informacji, zobacz [Metody routingu usługi Traffic Manager][routing-methods]. Jeśli używasz wydajności, żądanie adresu URL do Usługi Traffic Manager musi pochodzić z regionu użytkownika. W przypadku przechodzenia przez chatbota lub inną aplikację, obowiązkiem chatbota jest naśladowanie regionu w połączeniu z Menedżerem ruchu. |
    |-RelativeDnsName|luis-dns-westus|Jest to poddomena usługi: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port i protokół usługi LUIS to HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Wymień `<appId>` i `<subscriptionKey>` z własnymi wartościami. Pamiętaj, że ten klucz punktu końcowego różni się od klucza wschodniego punktu końcowego|
    
    Pomyślne żądanie nie ma odpowiedzi.

2. Dodawanie punktu końcowego zachodnie stany USA za pomocą polecenia cmdlet **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    W tej tabeli wyjaśniono każdą zmienną w policzce cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Nazwa punktu końcowego|luis-zachód-punkt końcowy|Nazwa punktu końcowego wyświetlana pod profilem|
    |-TrafficManagerProfile|$westprofile|Użyj obiektu profilu utworzonego w kroku 1|
    |-Typ|Zewnętrzne punkty końcowe|Aby uzyskać więcej informacji, zobacz [Punkt końcowy usługi Traffic Manager][traffic-manager-endpoints] |
    |-Cel|westus.api.cognitive.microsoft.com|Jest to domena punktu końcowego usługi LUIS.|
    |-EndpointLocation (Lokalizacja punktu końcowego)|"Westus"|Region punktu końcowego|
    |-EndpointStatus|Enabled (Włączony)|Włączanie punktu końcowego podczas jego tworzenia|

    Pomyślna odpowiedź wygląda następująco:

    ```console
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

3. Ustawianie punktu końcowego zachodniego us z poleceniem cmdlet **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Pomyślna odpowiedź jest taka sama odpowiedź jak krok 2.

### <a name="create-parent-traffic-manager-profile"></a>Utwórz nadrzędny profil usługi Traffic Manager
Utwórz nadrzędny profil usługi Traffic Manager i połącz dwa podrzędne profile usługi Traffic Manager z elementem nadrzędnym.

1. Tworzenie profilu nadrzędnego za pomocą polecenia cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    W tej tabeli wyjaśniono każdą zmienną w policzce cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Name|luis-profile-nadrzędny|Nazwa usługi Traffic Manager w witrynie Azure portal|
    |-Nazwa grupy zasobów|luis-traffic-manager|Utworzono w poprzedniej sekcji|
    |-TrafficRoutingMetoda|Wydajność|Aby uzyskać więcej informacji, zobacz [Metody routingu usługi Traffic Manager][routing-methods]. Jeśli używasz wydajności, żądanie adresu URL do Usługi Traffic Manager musi pochodzić z regionu użytkownika. W przypadku przechodzenia przez chatbota lub inną aplikację, obowiązkiem chatbota jest naśladowanie regionu w połączeniu z Menedżerem ruchu. |
    |-RelativeDnsName|luis-dns-parent|Jest to poddomena usługi: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port i protokół usługi LUIS to HTTPS/443|
    |-MonitorPath|`/`|Ta ścieżka nie ma znaczenia, ponieważ zamiast tego są używane ścieżki punktu końcowego podrzędnego.|

    Pomyślne żądanie nie ma odpowiedzi.

2. Dodawanie profilu podrzędnego wschodnich stanów USA do obiektu nadrzędnego z typem **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** i **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    W tej tabeli wyjaśniono każdą zmienną w policzce cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Nazwa punktu końcowego|dziecko-punkt końcowy-useast|Profil wschodni|
    |-TrafficManagerProfile|$parentprofile|Profil, aby przypisać ten punkt końcowy do|
    |-Typ|Punkty nestedend|Aby uzyskać więcej informacji, zobacz [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$eastprofile. Identyfikator|Identyfikator profilu dziecka|
    |-EndpointStatus|Enabled (Włączony)|Stan punktu końcowego po dodaniu do nadrzędnego|
    |-EndpointLocation (Lokalizacja punktu końcowego)|"eastus"|[Nazwa zasobu regionu platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minimalna liczba do podrzędnych punktów końcowych|

    Pomyślna odpowiedź wygląda następująco i `child-endpoint-useast` zawiera nowy punkt końcowy:    

    ```console
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

3. Dodawanie profilu podrzędnego zachodnie stany USA do obiektu nadrzędnego z typem polecenia cmdlet **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** i **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    W tej tabeli wyjaśniono każdą zmienną w policzce cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Nazwa punktu końcowego|child-endpoint-uswest|Profil zachodni|
    |-TrafficManagerProfile|$parentprofile|Profil, aby przypisać ten punkt końcowy do|
    |-Typ|Punkty nestedend|Aby uzyskać więcej informacji, zobacz [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$westprofile. Identyfikator|Identyfikator profilu dziecka|
    |-EndpointStatus|Enabled (Włączony)|Stan punktu końcowego po dodaniu do nadrzędnego|
    |-EndpointLocation (Lokalizacja punktu końcowego)|"Westus"|[Nazwa zasobu regionu platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minimalna liczba do podrzędnych punktów końcowych|

    Pomyślna odpowiedź wygląda i obejmuje `child-endpoint-useast` zarówno poprzedni `child-endpoint-uswest` punkt końcowy, jak i nowy punkt końcowy:

    ```console
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

4. Ustawianie punktów końcowych za pomocą polecenia cmdlet **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Pomyślna odpowiedź jest taka sama odpowiedź jak krok 3.

### <a name="powershell-variables"></a>Zmienne programu PowerShell
W poprzednich sekcjach utworzono trzy zmienne `$eastprofile` `$westprofile`programu `$parentprofile`PowerShell: , , . Zmienne te są używane pod koniec konfiguracji usługi Traffic Manager. Jeśli nie chcesz tworzyć zmiennych lub zapomniałeś lub czas okna programu PowerShell, można użyć polecenia cmdlet programu PowerShell, **[Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)**, aby ponownie uzyskać profil i przypisać go do zmiennej. 

Zastąp elementy w `<>`nawiasach kątowych, z odpowiednimi wartościami dla każdego z trzech potrzebnych profili. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Weryfikowanie pracy programu Traffic Manager
Aby sprawdzić, czy profile usługi Traffic Manager działają, profile `Online` muszą mieć stan Ten stan jest oparty na ścieżce sondowania punktu końcowego. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Wyświetlanie nowych profili w witrynie Azure portal
Można sprawdzić, czy wszystkie trzy profile są tworzone, `luis-traffic-manager` patrząc na zasoby w grupie zasobów.

![Zrzut ekranu przedstawiający menedżera usługi Luis-traffic-manager grupy zasobów platformy Azure](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Sprawdź, czy stan profilu jest w trybie online
Usługa Traffic Manager sonduje ścieżkę każdego punktu końcowego, aby upewnić się, że jest w trybie online. Jeśli jest ono online, status profili dziecka są `Online`. Jest to wyświetlane w **przeglądzie** każdego profilu. 

![Zrzut ekranu przedstawiający profil usługi Azure Traffic Manager — widok stanu monitora w trybie online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Sprawdzanie poprawności pracy sondowania menedżera ruchu
Innym sposobem sprawdzania poprawności pracy sondowania menedżera ruchu jest za pomocą dzienników punktu końcowego usługi LUIS. Na stronie listy aplikacji sieci [Web usługi LUIS][LUIS] wyeksportuj dziennik punktu końcowego dla aplikacji. Ponieważ usługa Traffic Manager często sonduje dwa punkty końcowe, istnieją wpisy w dziennikach, nawet jeśli były one tylko na kilka minut. Pamiętaj, aby wyszukać wpisy, w których rozpoczyna się kwerenda `traffic-manager-`.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Sprawdzanie poprawności odpowiedzi DNS z usługi Traffic Manager działa
Aby sprawdzić, czy odpowiedź DNS zwraca punkt końcowy usługi LUIS, poproś o adres DNS profilu nadrzędnego Zarządzanie ruchem przy użyciu biblioteki klienta DNS. Nazwa DNS profilu nadrzędnego `luis-dns-parent.trafficmanager.net`to .

Następujący kod Node.js sprawia, że żądanie dla profilu nadrzędnego i zwraca punkt końcowy usługi LUIS:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Pomyślna odpowiedź z punktem końcowym usługi LUIS jest:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Korzystanie z profilu nadrzędnego usługi Traffic Manager
Aby zarządzać ruchem między punktami końcowymi, należy wstawić wywołanie do usługi DNS menedżera ruchu, aby znaleźć punkt końcowy usługi LUIS. To wywołanie jest dla każdego żądania punktu końcowego usługi LUIS i musi symulować lokalizację geograficzną użytkownika aplikacji klienckiej usługi LUIS. Dodaj kod odpowiedzi DNS między aplikacją kliencką usługi LUIS a żądaniem do usługi LUIS w celu przewidywania punktu końcowego. 

## <a name="resolving-a-degraded-state"></a>Rozwiązywanie stanu zdegradowanego

Włącz [dzienniki diagnostyczne](../../traffic-manager/traffic-manager-diagnostic-logs.md) dla usługi Traffic Manager, aby zobaczyć, dlaczego stan punktu końcowego jest obniżony.

## <a name="clean-up"></a>Czyszczenie
Usuń dwa klucze punktu końcowego usługi LUIS, trzy profile usługi Traffic Manager i grupę zasobów, która zawierała te pięć zasobów. Odbywa się to za pomocą witryny Azure portal. Usuń pięć zasobów z listy zasobów. Następnie usuń grupę zasobów. 

## <a name="next-steps"></a>Następne kroki

Przejrzyj opcje [oprogramowania pośredniczącego](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) w BotFramework v4, aby dowiedzieć się, jak ten kod zarządzania ruchem można dodać do bota BotFramework. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
