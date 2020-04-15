---
title: Integruj usługi Azure Event Hubs z usługą Azure Private Link Service
description: Dowiedz się, jak zintegrować usługi Azure Event Hubs z usługą Azure Private Link Service
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: bcc360bbe4dd58200993b9377317ccb608b3529d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383648"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Integruj usługi Azure Event Hubs z łączem prywatnym platformy Azure (wersja zapoznawcza)
Usługa Azure Private Link Service umożliwia dostęp do usług platformy Azure (na przykład usługi Azure Event Hubs, Azure Storage i Azure Cosmos DB) oraz usługi klienta/partnera platformy Azure hostowane za pośrednictwem **prywatnego punktu końcowego** w sieci wirtualnej.

Prywatny punkt końcowy to interfejs sieciowy, który łączy cię prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, skutecznie wprowadzając usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można połączyć się z wystąpieniem zasobu platformy Azure, co zapewnia najwyższy poziom szczegółowości w kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [Co to jest łącze prywatne platformy Azure?](../private-link/private-link-overview.md)

> [!NOTE]
> Ta funkcja jest obsługiwana tylko w **warstwie dedykowanej.** Aby uzyskać więcej informacji na temat warstwy dedykowanej, zobacz [Omówienie dedykowanych centrów zdarzeń](event-hubs-dedicated-overview.md). 
>
> Ta funkcja jest obecnie w **wersji zapoznawczej**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Dodawanie prywatnego punktu końcowego przy użyciu portalu Azure

### <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować obszar nazw Centrum zdarzeń z łączem prywatnym platformy Azure, potrzebne są następujące jednostki lub uprawnienia:

- Obszar nazw centrów zdarzeń.
- Sieć wirtualna platformy Azure.
- Podsieć w sieci wirtualnej.
- Uprawnienia właściciela lub współautora zarówno dla obszaru nazw, jak i sieci wirtualnej.

Twój prywatny punkt końcowy i sieć wirtualna muszą znajdować się w tym samym regionie. Po wybraniu regionu dla prywatnego punktu końcowego za pomocą portalu, automatycznie filtruje tylko sieci wirtualne, które znajdują się w tym regionie. Obszar nazw może znajdować się w innym regionie.

Prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

### <a name="steps"></a>Kroki
Jeśli masz już obszar nazw Centrum zdarzeń, możesz utworzyć połączenie łącza prywatnego, wykonując następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Na pasku wyszukiwania wpisz **w centrach zdarzeń**.
3. Wybierz **obszar nazw** z listy, do której chcesz dodać prywatny punkt końcowy.
4. Wybierz kartę **Sieć** w obszarze **Ustawienia**.
5. Wybierz kartę **Prywatne połączenia punktów końcowych (podgląd)** u góry strony. Jeśli nie używasz dedykowanej warstwy Centrów zdarzeń, zostanie wyświetlony komunikat: **Prywatne połączenia punktów końcowych w centrach zdarzeń są obsługiwane tylko przez przestrzenie nazw utworzone w dedykowanym klastrze**.
6. Wybierz przycisk **+ Prywatny punkt końcowy** u góry strony.

    ![Image (Obraz)](./media/private-link-service/private-link-service-3.png)
7. Na stronie **Podstawy** wykonaj następujące czynności: 
    1. Wybierz **subskrypcję platformy Azure,** w której chcesz utworzyć prywatny punkt końcowy. 
    2. Wybierz **grupę zasobów** dla zasobu prywatnego punktu końcowego.
    3. Wprowadź **nazwę** prywatnego punktu końcowego. 
    5. Wybierz **region** dla prywatnego punktu końcowego. Twój prywatny punkt końcowy musi znajdować się w tym samym regionie co sieć wirtualna, ale może znajdować się w innym regionie zasobu łącza prywatnego, z którego nawiązujesz połączenie. 
    6. Wybierz **przycisk Dalej: >zasobu** u dołu strony.

        ![Tworzenie prywatnego punktu końcowego — strona Podstawy](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na stronie **Zasób** wykonaj następujące czynności:
    1. W przypadku metody połączenia, jeśli **wybierzesz opcję Połącz z zasobem platformy Azure w moim katalogu,** wykonaj następujące kroki: 
        1. Wybierz **subskrypcję platformy Azure,** w której istnieje **obszar nazw usługi Event Hubs.** 
        2. W przypadku **typu zasobu**wybierz pozycję **Microsoft.EventHub/namespaces** dla **typu zasobu**.
        3. W obszarze **Zasób**wybierz obszar nazw Centrów zdarzeń z listy rozwijanej. 
        4. Upewnij się, że **podźródło docelowe** jest ustawione na **obszar nazw**.
        5. Wybierz **przycisk Dalej: Przycisk >konfiguracji** u dołu strony. 
        
            ![Tworzenie prywatnego punktu końcowego — strona Zasób](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Jeśli wybierzesz **opcję Połącz z zasobem platformy Azure według identyfikatora lub aliasu zasobu,** wykonaj następujące kroki:
        1. Wprowadź **identyfikator zasobu** lub **alias**. Może to być identyfikator zasobu lub alias, który niektórzy udostępnili.
        2. W obszarze **podrzędny obiekt Docelowy**wprowadź **obszar nazw**. Jest to typ zasobu podrzędnego, do który można uzyskać dostęp do prywatnego punktu końcowego.
        3. (opcjonalnie) Wprowadź **komunikat żądania**. Właściciel zasobu widzi ten komunikat podczas zarządzania połączeniem prywatnego punktu końcowego.
        4. Następnie wybierz przycisk **Dalej: >konfiguracji** u dołu strony.

            ![Tworzenie prywatnego punktu końcowego — łączenie przy użyciu identyfikatora zasobu](./media/private-link-service/connect-resource-id.png)
9. Na **stronie Konfiguracja** wybierz podsieć w sieci wirtualnej do miejsca, w którym chcesz wdrożyć prywatny punkt końcowy. 
    1. Wybierz **sieć wirtualną**. Na liście rozwijanej znajdują się tylko sieci wirtualne w aktualnie wybranej subskrypcji i lokalizacji. 
    2. Wybierz **podsieć** w wybranej sieci wirtualnej. 
    3. Wybierz **dalej: Przycisk >znaczników** u dołu strony. 

        ![Tworzenie prywatnego punktu końcowego — strona konfiguracji](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na stronie **Znaczniki** utwórz wszystkie znaczniki (nazwy i wartości), które chcesz skojarzyć z zasobem prywatnego punktu końcowego. Następnie wybierz przycisk **Przejrzyj + utwórz** u dołu strony. 
11. W **obszarze Recenzja + utwórz**przejrzyj wszystkie ustawienia i wybierz pozycję **Utwórz,** aby utworzyć prywatny punkt końcowy.
    
    ![Tworzenie prywatnego punktu końcowego — recenzja i tworzenie strony](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Upewnij się, że na liście punktów końcowych jest wyświetlany widok na utworzone połączenie prywatnego punktu końcowego. W tym przykładzie prywatny punkt końcowy jest automatycznie zatwierdzany, ponieważ masz połączenie z zasobem platformy Azure w katalogu i masz wystarczające uprawnienia. 

    ![Utworzony prywatny punkt końcowy](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Dodawanie prywatnego punktu końcowego przy użyciu programu PowerShell
W poniższym przykładzie pokazano, jak utworzyć połączenie prywatnego punktu końcowego za pomocą programu Azure PowerShell. Nie tworzy dedykowanego klastra dla Ciebie. Wykonaj kroki opisane w [tym artykule,](event-hubs-dedicated-cluster-create-portal.md) aby utworzyć dedykowany klaster centrów zdarzeń. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>Konfigurowanie prywatnej strefy DNS
Utwórz prywatną strefę DNS dla domeny Centrum zdarzeń i utwórz łącze skojarzenia z siecią wirtualną:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Zarządzanie prywatnymi punktami końcowymi przy użyciu portalu Azure

Podczas tworzenia prywatnego punktu końcowego połączenie musi zostać zatwierdzone. Jeśli zasób, dla którego tworzysz prywatny punkt końcowy znajduje się w katalogu, możesz zatwierdzić żądanie połączenia, pod warunkiem, że masz wystarczające uprawnienia. Jeśli łączysz się z zasobem platformy Azure w innym katalogu, musisz poczekać, aż właściciel tego zasobu zatwierdzi żądanie połączenia.

Istnieją cztery stany inicjowania obsługi administracyjnej:

| Akcja serwisowa | Stan prywatnego punktu końcowego usługi konsumenta | Opis |
|--|--|--|
| Brak | Oczekujące | Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie przez właściciela zasobu Łącza prywatnego. |
| Zatwierdzenie | Approved (Zatwierdzono) | Połączenie zostało zatwierdzone automatycznie lub ręcznie i jest gotowe do użycia. |
| Reject | Odrzucone | Połączenie zostało odrzucone przez właściciela zasobu łącza prywatnego. |
| Remove | Odłączony | Połączenie zostało usunięte przez właściciela zasobu łącza prywatnego, prywatny punkt końcowy staje się informacyjny i powinien zostać usunięty w celu oczyszczenia. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Zatwierdzanie, odrzucanie lub usuwanie połączenia prywatnego punktu końcowego

1. Zaloguj się do Portalu Azure.
2. Na pasku wyszukiwania wpisz **w centrach zdarzeń**.
3. Wybierz **obszar nazw,** którymi chcesz zarządzać.
4. Wybierz kartę **Sieć.**
5. Przejdź do odpowiedniej sekcji poniżej na podstawie operacji, którą chcesz: zatwierdzić, odrzucić lub usunąć.

### <a name="approve-a-private-endpoint-connection"></a>Zatwierdzanie połączenia prywatnego punktu końcowego
1. Jeśli istnieją jakieś połączenia, które są oczekujące, zostanie wyświetlone połączenie na liście **Oczekujące** w stanie inicjowania obsługi administracyjnej. 
2. Wybierz **prywatny punkt końcowy,** który chcesz zatwierdzić
3. Wybierz przycisk **Zatwierdź.**

    ![Image (Obraz)](./media/private-link-service/approve-private-endpoint.png)
4. Na stronie **Zatwierdzanie połączenia** dodaj komentarz (opcjonalnie) i wybierz pozycję **Tak**. Jeśli wybierzesz **nie**, nic się nie stanie. 
5. Na liście powinien zostać wyświetlony stan połączenia prywatnego punktu końcowego na liście **zmienionej**na Zatwierdzone . 

### <a name="reject-a-private-endpoint-connection"></a>Odrzucanie połączenia prywatnego punktu końcowego

1. Jeśli istnieją jakieś połączenia prywatnego punktu końcowego, które chcesz odrzucić, niezależnie od tego, czy jest to żądanie oczekujące, czy istniejące połączenie, zaznacz połączenie i kliknij przycisk **Odrzuć.**

    ![Image (Obraz)](./media/private-link-service/private-endpoint-reject-button.png)
2. Na stronie **Odrzucanie połączenia** wprowadź komentarz (opcjonalnie) i wybierz pozycję **Tak**. Jeśli wybierzesz **nie**, nic się nie stanie. 
3. Na liście powinien zostać wyświetlony stan połączenia prywatnego punktu końcowego na liście **zmienionej**na Odrzucone . 

### <a name="remove-a-private-endpoint-connection"></a>Usuwanie połączenia prywatnego punktu końcowego

1. Aby usunąć połączenie prywatnego punktu końcowego, zaznacz je na liście i wybierz pozycję **Usuń** na pasku narzędzi.
2. Na stronie **Usuwanie połączenia** wybierz pozycję **Tak,** aby potwierdzić usunięcie prywatnego punktu końcowego. Jeśli wybierzesz **nie**, nic się nie stanie.
3. Powinien zostać wyświetlony stan zmieniony na **Rozłączony**. Następnie zostanie wyświetlony punkt końcowy zniknie z listy.

## <a name="validate-that-the-private-link-connection-works"></a>Sprawdzanie, czy połączenie łącza prywatnego działa

Należy sprawdzić, czy zasoby w tej samej podsieci zasobu prywatnego punktu końcowego łączą się z obszarem nazw centrum zdarzeń za pośrednictwem prywatnego adresu IP i że mają one prawidłową integrację prywatnej strefy DNS.

Najpierw utwórz maszynę wirtualną, wykonując kroki opisane w [obszarze Tworzenie maszyny wirtualnej systemu Windows w portalu Azure](../virtual-machines/windows/quick-create-portal.md)

Na karcie **Sieć:**

1. Określ **sieć wirtualną** i **podsieć**. Można utworzyć nową sieć wirtualną lub wybrać istniejącą. Jeśli wybierzesz istniejący, upewnij się, że region jest zgodny.
1. Określ publiczny zasób **IP.**
1. W **grupie zabezpieczeń sieciowej karty sieciowej**wybierz pozycję **Brak**.
1. W **obszarze Równoważenie obciążenia**wybierz **pozycję Nie**.

Otwórz wiersz polecenia i uruchom następujące polecenie:

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Po uruchomieniu polecenia wyszukiwania ns w celu rozpoznania adresu IP obszaru nazw Centrum zdarzeń za pomocą publicznego punktu końcowego zostanie wyświetlony wynik, który wygląda następująco:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Po uruchomieniu polecenia wyszukiwania ns w celu rozpoznania adresu IP obszaru nazw Centrum zdarzeń w prywatnym punkcie końcowym zostanie wyświetlony wynik, który wygląda następująco:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Ograniczenia i zagadnienia projektowe

**Cennik**: Aby uzyskać informacje o cenach, zobacz [Cennik łącza prywatnego platformy Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Ograniczenia:** Prywatny punkt końcowy dla usługi Azure Event Hubs jest w publicznej wersji zapoznawczej. Ta funkcja jest dostępna we wszystkich regionach publicznych platformy Azure.

**Maksymalna liczba prywatnych punktów końcowych na obszar nazw centrum zdarzeń:** 120.

Aby uzyskać więcej informacji, zobacz [Usługa Azure Private Link: Ograniczenia](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [łączu prywatnym platformy Azure](../private-link/private-link-service-overview.md)
- Dowiedz się więcej o [usłudze Azure Event Hubs](event-hubs-about.md)
