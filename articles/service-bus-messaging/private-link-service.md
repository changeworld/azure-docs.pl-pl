---
title: Integracja usługi Azure Service Bus z usługą Azure Private Link Service
description: Dowiedz się, jak zintegrować usługę Azure Service Bus z usługą Azure Private Link Service
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: b8c4248b7275ac96acce96f890f6ff0148116f48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478008"
---
# <a name="integrate-azure-service-bus-with-azure-private-link-preview"></a>Integracja usługi Azure Service Bus z łączem prywatnym platformy Azure (wersja zapoznawcza)

Usługa Azure Private Link Service umożliwia dostęp do usług platformy Azure (na przykład usługi Azure Service Bus, Azure Storage i Azure Cosmos DB) oraz usługi klienta/partnera platformy Azure hostowane za pośrednictwem **prywatnego punktu końcowego** w sieci wirtualnej.

Prywatny punkt końcowy to interfejs sieciowy, który łączy cię prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, skutecznie wprowadzając usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można połączyć się z wystąpieniem zasobu platformy Azure, co zapewnia najwyższy poziom szczegółowości w kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [Co to jest łącze prywatne platformy Azure?](../private-link/private-link-overview.md)

> [!NOTE]
> Ta funkcja jest obsługiwana w warstwie **premium** usługi Azure Service Bus. Aby uzyskać więcej informacji na temat warstwy premium, zobacz [warstwy obsługi wiadomości usługi Service Bus Premium i Standard.](service-bus-premium-messaging.md)
>
> Ta funkcja jest obecnie w **wersji zapoznawczej**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Dodawanie prywatnego punktu końcowego przy użyciu portalu Azure

### <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować obszar nazw usługi Service Bus z łączem prywatnym platformy Azure, potrzebne są następujące jednostki lub uprawnienia:

- Obszar nazw usługi Service Bus.
- Sieć wirtualna platformy Azure.
- Podsieć w sieci wirtualnej.
- Uprawnienia właściciela lub współautora zarówno dla obszaru nazw usługi Service Bus, jak i sieci wirtualnej.

Twój prywatny punkt końcowy i sieć wirtualna muszą znajdować się w tym samym regionie. Po wybraniu regionu dla prywatnego punktu końcowego za pomocą portalu, automatycznie filtruje tylko sieci wirtualne, które znajdują się w tym regionie. Obszar nazw usługi Service Bus może znajdować się w innym regionie. I twój prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

### <a name="steps"></a>kroki

Jeśli masz już istniejącą przestrzeń nazw, możesz utworzyć prywatny punkt końcowy, wykonując następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
2. Na pasku wyszukiwania wpisz **service bus**.
3. Wybierz **obszar nazw** z listy, do której chcesz dodać prywatny punkt końcowy.
4. Wybierz kartę **Sieć** w obszarze **Ustawienia**.
5. Wybieranie karty **Prywatne połączenia punktów końcowych (podgląd)** u góry strony
6. Wybierz przycisk **+ Prywatny punkt końcowy** u góry strony.

    ![Przycisk Dodaj prywatny punkt końcowy](./media/private-link-service/private-link-service-3.png)
7. Na stronie **Podstawy** wykonaj następujące czynności: 
    1. Wybierz **subskrypcję platformy Azure,** w której chcesz utworzyć prywatny punkt końcowy. 
    2. Wybierz **grupę zasobów** dla zasobu prywatnego punktu końcowego.
    3. Wprowadź **nazwę** prywatnego punktu końcowego. 
    5. Wybierz **region** dla prywatnego punktu końcowego. Twój prywatny punkt końcowy musi znajdować się w tym samym regionie co sieć wirtualna, ale może znajdować się w innym regionie zasobu łącza prywatnego, z którego nawiązujesz połączenie. 
    6. Wybierz **przycisk Dalej: >zasobu** u dołu strony.

        ![Tworzenie prywatnego punktu końcowego — strona Podstawy](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na stronie **Zasób** wykonaj następujące czynności:
    1. W przypadku metody połączenia, jeśli **wybierzesz opcję Połącz z zasobem platformy Azure w moim katalogu,** wykonaj następujące kroki:   
        1. Wybierz **subskrypcję platformy Azure,** w której istnieje **obszar nazw usługi Service Bus.** 
        2. W przypadku **typu zasobu**wybierz pozycję **Microsoft.ServiceBus/namespaces** dla **typu zasobu**.
        3. W obszarze **Zasób**wybierz obszar nazw usługi Service Bus z listy rozwijanej. 
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
12. Upewnij się, że jest tworzony prywatny punkt końcowy. Jeśli jesteś właścicielem zasobu i **wybrano opcję Połącz z zasobem platformy Azure w mojej** opcji katalogu dla metody **Połączenie,** połączenie punktu końcowego powinno zostać **automatycznie zatwierdzone.** Jeśli jest w stanie **oczekiwania,** zobacz [Zarządzanie prywatnymi punktami końcowymi przy użyciu witryny Azure portal](#manage-private-endpoints-using-azure-portal) sekcji.

    ![Utworzony prywatny punkt końcowy](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Dodawanie prywatnego punktu końcowego przy użyciu programu PowerShell
W poniższym przykładzie pokazano, jak używać programu Azure PowerShell do tworzenia połączenia prywatnego punktu końcowego z obszarem nazw usługi Service Bus.

Twój prywatny punkt końcowy i sieć wirtualna muszą znajdować się w tym samym regionie. Obszar nazw usługi Service Bus może znajdować się w innym regionie. I twój prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


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
1. Na pasku wyszukiwania wpisz **service bus**.
1. Wybierz **obszar nazw,** którymi chcesz zarządzać.
1. Wybierz kartę **Sieć.**
5. Przejdź do odpowiedniej sekcji poniżej na podstawie operacji, którą chcesz: zatwierdzić, odrzucić lub usunąć. 

### <a name="approve-a-private-endpoint-connection"></a>Zatwierdzanie połączenia prywatnego punktu końcowego

1. Jeśli istnieją jakieś połączenia, które są oczekujące, zostanie wyświetlone połączenie na liście **Oczekujące** w stanie inicjowania obsługi administracyjnej. 
2. Wybierz **prywatny punkt końcowy,** który chcesz zatwierdzić
3. Wybierz przycisk **Zatwierdź.**

    ![Zatwierdzanie prywatnego punktu końcowego](./media/private-link-service/private-endpoint-approve.png)
4. Na stronie **Zatwierdzanie połączenia** wprowadź **opcjonalny komentarz**i wybierz pozycję **Tak**. Jeśli wybierzesz **nie**, nic się nie stanie. 

    ![Zatwierdź stronę połączenia](./media/private-link-service/approve-connection-page.png)
5. Na liście powinien zostać wyświetlony stan połączenia zmieniony na **Zatwierdzone**. 

    ![Stan połączenia - zatwierdzony](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Odrzucanie połączenia prywatnego punktu końcowego

1. Jeśli istnieją jakieś połączenia prywatnego punktu końcowego, które chcesz odrzucić, niezależnie od tego, czy jest to żądanie oczekujące, czy istniejące połączenie, które zostało zatwierdzone wcześniej, wybierz połączenie punktu końcowego i kliknij przycisk **Odrzuć.**

    ![Przycisk Odrzuć](./media/private-link-service/private-endpoint-reject.png)
2. Na stronie **Odrzucanie połączenia** wprowadź opcjonalny komentarz i wybierz pozycję **Tak**. Jeśli wybierzesz **nie**, nic się nie stanie. 

    ![Strona Odrzuć połączenie](./media/private-link-service/reject-connection-page.png)
3. Powinien być widoczny stan połączenia na liście zmieniono **Odrzucone**. 

    ![Punkt końcowy odrzucony](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Usuwanie połączenia prywatnego punktu końcowego

1. Aby usunąć połączenie prywatnego punktu końcowego, zaznacz je na liście i wybierz pozycję **Usuń** na pasku narzędzi. 

    ![Przycisk Usuń](./media/private-link-service/remove-endpoint.png)
2. Na stronie **Usuwanie połączenia** wybierz pozycję **Tak,** aby potwierdzić usunięcie prywatnego punktu końcowego. Jeśli wybierzesz **nie**, nic się nie stanie. 

    ![Usuwanie strony połączenia](./media/private-link-service/delete-connection-page.png)
3. Powinien zostać wyświetlony stan zmieniony na **Rozłączony**. Następnie zostanie wyświetlony punkt końcowy zniknie z listy. 

## <a name="validate-that-the-private-link-connection-works"></a>Sprawdzanie, czy połączenie łącza prywatnego działa

Należy sprawdzić, czy zasoby w tej samej podsieci zasobu prywatnego punktu końcowego łączą się z obszarem nazw usługi Service Bus za pośrednictwem prywatnego adresu IP i że mają one prawidłową integrację prywatnej strefy DNS.

Najpierw utwórz maszynę wirtualną, wykonując kroki opisane w [obszarze Tworzenie maszyny wirtualnej systemu Windows w portalu Azure](../virtual-machines/windows/quick-create-portal.md)

Na karcie **Sieć:**

1. Określ **sieć wirtualną** i **podsieć**. Można utworzyć nową sieć wirtualną lub wybrać istniejącą. Jeśli wybierzesz istniejący, upewnij się, że region jest zgodny.
1. Określ publiczny zasób **IP.**
1. W przypadku **grupy zabezpieczeń sieciowej karty sieciowej**wybierz pozycję **Brak**.
1. W przypadku **równoważenia obciążenia**wybierz **opcję Nie**.

Otwórz wiersz polecenia i uruchom następujące polecenie:

```console
nslookup <your-service-bus-namespace-name>.servicebus.windows.net
```

Po uruchomieniu polecenia wyszukiwania ns w celu rozpoznania adresu IP obszaru nazw usługi Service Bus nad publicznym punktem końcowym zostanie wyświetlony wynik, który wygląda następująco:

```console
c:\ >nslookup <your-service-bus-namespace-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

Po uruchomieniu polecenia wyszukiwania ns w celu rozpoznania adresu IP obszaru nazw usługi Service Bus nad prywatnym punktem końcowym zostanie wyświetlony wynik, który wygląda następująco:

```console
c:\ >nslookup your_service-bus-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Ograniczenia i zagadnienia projektowe

**Cennik**: Aby uzyskać informacje o cenach, zobacz [Cennik łącza prywatnego platformy Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Ograniczenia:** Prywatny punkt końcowy dla usługi Azure Service Bus jest w publicznej wersji zapoznawczej. Ta funkcja jest dostępna we wszystkich regionach publicznych platformy Azure.

**Maksymalna liczba prywatnych punktów końcowych na obszar nazw usługi Service Bus:** 120.

Aby uzyskać więcej informacji, zobacz [Usługa Azure Private Link: Ograniczenia](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [łączu prywatnym platformy Azure](../private-link/private-link-service-overview.md)
- Dowiedz się więcej o [usłudze Azure Service Bus](service-bus-messaging-overview.md)
