---
title: Konfigurowanie prywatnych punktów końcowych dla tematów lub domen usługi Azure Event Grid
description: W tym artykule opisano sposób konfigurowania prywatnych punktów końcowych dla tematów lub domeny usługi Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: d08afe00c13a3f96b9526c3cb29804cfad688ddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299909"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>Konfigurowanie prywatnych punktów końcowych dla tematów lub domen usługi Azure Event Grid (wersja zapoznawcza)
Za pomocą [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) można zezwolić na przychynienie się zdarzeń bezpośrednio z sieci wirtualnej do tematów i domen bezpiecznie za pośrednictwem łącza [prywatnego](../private-link/private-link-overview.md) bez przechodzenia przez publiczny Internet. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla tematu lub domeny. Aby uzyskać więcej informacji koncepcyjnych, zobacz [Bezpieczeństwo sieci](network-security.md).

W tym artykule opisano sposób konfigurowania prywatnych punktów końcowych dla tematów lub domen.

> [!IMPORTANT]
> Funkcja prywatnych punktów końcowych jest dostępna dla tematów i domen tylko w warstwie premium. Aby przejść z warstwy podstawowej do warstwy premium, zobacz artykuł [warstwy cenowej Aktualizowanie.](update-tier.md) 

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 
W tej sekcji pokazano, jak używać witryny Azure Portal do tworzenia prywatnego punktu końcowego dla tematu lub domeny.

> [!NOTE]
> Kroki pokazane w tej sekcji są głównie dla tematów. Podobne kroki można wykonać do tworzenia prywatnych punktów końcowych dla **domen**. 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do tematu lub domeny.
2. Przełącz się na kartę **Sieć** na stronie tematu. Wybierz **+ Prywatny punkt końcowy** na pasku narzędzi.

    ![Dodawanie prywatnego punktu końcowego](./media/configure-private-endpoints/add-button.png)
2. Jedna ze stron **podstawy,** wykonaj następujące kroki: 
    1. Wybierz **subskrypcję platformy Azure,** w której chcesz utworzyć prywatny punkt końcowy. 
    2. Wybierz **grupę zasobów platformy Azure** dla prywatnego punktu końcowego. 
    3. Wprowadź **nazwę** punktu końcowego. 
    4. Wybierz **region** dla punktu końcowego. Prywatny punkt końcowy musi znajdować się w tym samym regionie co sieć wirtualna, ale może w innym regionie niż zasób łącza prywatnego (w tym przykładzie temat siatki zdarzeń). 
    5. Następnie wybierz przycisk **Dalej: >zasobu** u dołu strony. 

      ![Prywatny punkt końcowy — strona podstawy](./media/configure-private-endpoints/basics-page.png)
3. Na stronie **Zasób** wykonaj następujące czynności: 
    1. W przypadku metody połączenia, jeśli wybierzesz **opcję Połącz z zasobem platformy Azure w moim katalogu,** wykonaj następujące kroki. W tym przykładzie pokazano, jak połączyć się z zasobem platformy Azure w katalogu. 
        1. Wybierz **subskrypcję platformy Azure,** w której istnieje **twój temat/domena.** 
        1. W przypadku **typu zasobu**wybierz pozycję **Microsoft.EventGrid/topics** lub **Microsoft.EventGrid/domains** for the **Resource type**.
        2. W obszarze **Zasób**wybierz temat/domenę z listy rozwijanej. 
        3. Upewnij się, że **podźródło docelowe** jest ustawione na **temat** lub **domenę** (na podstawie wybranego typu zasobu).    
        4. Wybierz **przycisk Dalej: Przycisk >konfiguracji** u dołu strony. 

            ![Prywatny punkt końcowy — strona zasobu](./media/configure-private-endpoints/resource-page.png)
    2. W przypadku wybrania opcji **Połącz z zasobem przy użyciu identyfikatora zasobu lub aliasu**wykonaj następujące czynności:
        1. Wprowadź identyfikator zasobu. Na przykład: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. W obszarze **Zasób**wprowadź **temat** lub **domenę**. 
        3. (opcjonalnie) Dodaj wiadomość z prośbą. 
        4. Wybierz **przycisk Dalej: Przycisk >konfiguracji** u dołu strony. 

            ![Prywatny punkt końcowy — strona zasobu](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Na **stronie Konfiguracja** wybierz podsieć w sieci wirtualnej do miejsca, w którym chcesz wdrożyć prywatny punkt końcowy. 
    1. Wybierz **sieć wirtualną**. Na liście rozwijanej znajdują się tylko sieci wirtualne w aktualnie wybranej subskrypcji i lokalizacji. 
    2. Wybierz **podsieć** w wybranej sieci wirtualnej. 
    3. Wybierz **dalej: Przycisk >znaczników** u dołu strony. 

    ![Prywatny punkt końcowy — strona konfiguracji](./media/configure-private-endpoints/configuration-page.png)
5. Na stronie **Znaczniki** utwórz wszystkie znaczniki (nazwy i wartości), które chcesz skojarzyć z zasobem prywatnego punktu końcowego. Następnie wybierz przycisk **Przejrzyj + utwórz** u dołu strony. 
6. W **obszarze Recenzja + utwórz**przejrzyj wszystkie ustawienia i wybierz pozycję **Utwórz,** aby utworzyć prywatny punkt końcowy. 

    ![Prywatny punkt końcowy — recenzja & utworzyć stronę](./media/configure-private-endpoints/review-create-page.png)
    

## <a name="manage-private-link-connection"></a>Zarządzanie połączeniem łącza prywatnego

Podczas tworzenia prywatnego punktu końcowego połączenie musi zostać zatwierdzone. Jeśli zasób, dla którego tworzysz prywatny punkt końcowy znajduje się w katalogu, możesz zatwierdzić żądanie połączenia, pod warunkiem, że masz wystarczające uprawnienia. Jeśli łączysz się z zasobem platformy Azure w innym katalogu, musisz poczekać, aż właściciel tego zasobu zatwierdzi żądanie połączenia.

Istnieją cztery stany inicjowania obsługi administracyjnej:

| Akcja serwisowa | Stan prywatnego punktu końcowego usługi konsumenta | Opis |
|--|--|--|
| Brak | Oczekujące | Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie od prywatnego właściciela zasobu łącza. |
| Zatwierdzenie | Approved (Zatwierdzono) | Połączenie zostało zatwierdzone automatycznie lub ręcznie i jest gotowe do użycia. |
| Reject | Odrzucone | Połączenie zostało odrzucone przez właściciela zasobu łącza prywatnego. |
| Remove | Odłączony | Połączenie zostało usunięte przez właściciela zasobu łącza prywatnego, prywatny punkt końcowy staje się informacyjny i powinien zostać usunięty w celu oczyszczenia. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Jak zarządzać połączeniem prywatnego punktu końcowego
W poniższych sekcjach pokazano, jak zatwierdzić lub odrzucić połączenie prywatnego punktu końcowego. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Na pasku wyszukiwania wpisz **tematy siatki zdarzeń** lub domeny siatki **zdarzeń**.
1. Wybierz **temat** lub **domenę,** którymi chcesz zarządzać.
1. Wybierz kartę **Sieć.**
1. Jeśli istnieją jakieś połączenia, które są oczekujące, zobaczysz połączenie na liście **oczekujące** w stanie inicjowania obsługi administracyjnej. 

### <a name="to-approve-a-private-endpoint"></a>Aby zatwierdzić prywatny punkt końcowy
Można zatwierdzić prywatny punkt końcowy, który jest w stanie oczekiwania. Aby zatwierdzić, wykonaj następujące kroki: 

> [!NOTE]
> Kroki pokazane w tej sekcji są głównie dla tematów. Podobne kroki można wykonać, aby zatwierdzić prywatne punkty końcowe dla **domen**. 

1. Wybierz **prywatny punkt końcowy,** który chcesz zatwierdzić, a następnie wybierz pozycję **Zatwierdź** na pasku narzędzi.

    ![Prywatny punkt końcowy — stan oczekiwania](./media/configure-private-endpoints/pending.png)
1. W oknie dialogowym **Zatwierdzanie połączenia** wprowadź komentarz (opcjonalnie) i wybierz pozycję **Tak**. 

    ![Prywatny punkt końcowy — zatwierdzanie](./media/configure-private-endpoints/approve.png)
1. Upewnij się, że stan punktu końcowego jest widoczny jako **Zatwierdzony**. 

    ![Prywatny punkt końcowy — stan zatwierdzony](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Aby odrzucić prywatny punkt końcowy
Można odrzucić prywatny punkt końcowy, który jest w stanie oczekiwania lub zatwierdzonym. Aby odrzucić, wykonaj następujące kroki: 

> [!NOTE]
> Kroki przedstawione w tej sekcji są dla tematów. Podobne kroki można wykonać, aby odrzucić prywatne punkty końcowe dla **domen**. 

1. Wybierz **prywatny punkt końcowy,** który chcesz odrzucić, a następnie wybierz pozycję **Odrzuć** na pasku narzędzi.

    ![Prywatny punkt końcowy — odrzuć](./media/configure-private-endpoints/reject-button.png)
1. W oknie dialogowym **Odrzucanie połączenia** wprowadź komentarz (opcjonalnie) i wybierz pozycję **Tak**. 

    ![Prywatny punkt końcowy — odrzuć](./media/configure-private-endpoints/reject.png)
1. Upewnij się, że stan punktu końcowego jest widoczny jako **Odrzucony**. 

    ![Prywatny punkt końcowy — stan odrzucony](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Nie można zatwierdzić prywatnego punktu końcowego w witrynie Azure portal po jego odrzuceniu. 


## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Aby utworzyć prywatny punkt końcowy, należy użyć metody [tworzenia prywatnego punktu końcowego az,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) jak pokazano w poniższym przykładzie:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Opisy parametrów użytych w przykładzie można znaleźć w dokumentacji programu [AZ Network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create). Kilka punktów, na które należy zwrócić uwagę w tym przykładzie, to: 

- Dla `private-connection-resource-id`, określ identyfikator zasobu **tematu** lub **domeny**. W poprzednim przykładzie użyto tematu typu: .
- dla `group-ids`, `topic` `domain`określić lub . W poprzednim przykładzie `topic` jest używany. 

Aby usunąć prywatny punkt końcowy, należy użyć metody [usuwania prywatnego punktu końcowego az,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) jak pokazano w poniższym przykładzie:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Kroki przedstawione w tej sekcji są dla tematów. Podobne kroki można wykonać do tworzenia prywatnych punktów końcowych dla **domen**. 

### <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
Oto przykładowy skrypt, który tworzy następujące zasoby platformy Azure:

- Grupa zasobów
- Sieć wirtualna
- Podsieć w sieci wirtualnej
- Temat usługi Azure Event Grid (warstwa premium)
- Prywatny punkt końcowy dla tematu

> [!NOTE]
> Kroki przedstawione w tej sekcji są dla tematów. Podobne kroki można użyć do tworzenia prywatnych punktów końcowych dla domen.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# URI for the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicUri="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>?api-version=2020-04-01-preview"

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az rest --method put \
    --uri $topicUri \
    --body "{\""location\"":\""LOCATION\"", \""sku\"": {\""name\"": \""premium\""}, \""properties\"": {\""publicNetworkAccess\"":\""Disabled\""}}"

# verify that the topic was created.
az rest --method get \
    --uri $topicUri

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az rest --method get \
    --uri $topicUri

```

### <a name="approve-a-private-endpoint-connection"></a>Zatwierdzanie połączenia prywatnego punktu końcowego
Poniższy przykładowy fragment wiersza polecenia pokazuje, jak zatwierdzić połączenie prywatnego punktu końcowego. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""approved\"",\""description\"":\""connection approved\"", \""actionsRequired\"": \""none\""}}}"
```


### <a name="reject-a-private-endpoint-connection"></a>Odrzucanie połączenia prywatnego punktu końcowego
Poniższy przykładowy fragment wiersza polecenia pokazuje, jak odrzucić połączenie prywatnego punktu końcowego. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""rejected\"",\""description\"":\""connection rejected\"", \""actionsRequired\"": \""none\""}}}"
```


## <a name="use-powershell"></a>Korzystanie z programu PowerShell
W tej sekcji pokazano, jak utworzyć prywatny punkt końcowy dla tematu lub domeny przy użyciu programu PowerShell. 

### <a name="prerequisite"></a>Wymagania wstępne
Postępuj zgodnie z instrukcjami [dotyczącymi jak: Użyj portalu, aby utworzyć aplikację i jednostkę usługi Azure AD, która może uzyskać dostęp](../active-directory/develop/howto-create-service-principal-portal.md) do zasobów w celu utworzenia aplikacji usługi Azure Active Directory i zanotować wartości **identyfikatora katalogu (dzierżawy),** **identyfikatora aplikacji (klienta)** i **klucza tajnego aplikacji (klienta).** 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Przygotowywanie tokenu i nagłówków dla wywołań interfejsu API REST 
Uruchom następujące polecenia wymagane, aby uzyskać token uwierzytelniania do użycia z wywołaniami interfejsu API REST i autoryzacji i innych informacji nagłówka. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Tworzenie podsieci z wyłączonymi zasadami sieciowymi punktów końcowych

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Tworzenie tematu siatki zdarzeń z prywatnym punktem końcowym

> [!NOTE]
> Kroki przedstawione w tej sekcji są dla tematów. Podobne kroki można wykonać do tworzenia prywatnych punktów końcowych dla **domen**. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Po sprawdzeniu, czy punkt końcowy został utworzony, powinien zostać wyświetlony wynik podobny do następującego:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Zatwierdzanie połączenia prywatnego punktu końcowego
Poniższy przykładowy fragment kodu programu PowerShell pokazuje, jak zatwierdzić prywatny punkt końcowy. 

> [!NOTE]
> Kroki przedstawione w tej sekcji są dla tematów. Podobne kroki można wykonać, aby zatwierdzić prywatne punkty końcowe dla **domen**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Odrzucanie połączenia prywatnego punktu końcowego
W poniższym przykładzie pokazano, jak odrzucić prywatny punkt końcowy przy użyciu programu PowerShell. Identyfikator GUID dla prywatnego punktu końcowego można uzyskać z wyniku poprzedniego polecenia GET. 

> [!NOTE]
> Kroki przedstawione w tej sekcji są dla tematów. Podobne kroki można wykonać, aby odrzucić prywatne punkty końcowe dla **domen**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

Połączenie można zatwierdzić nawet po jego odrzuceniu za pośrednictwem interfejsu API. Jeśli używasz witryny Azure portal, nie można zatwierdzić punktu końcowego, który został odrzucony. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak skonfigurować ustawienia zapory IP, zobacz [Konfigurowanie zapory IP dla tematów lub domen usługi Azure Event Grid](configure-firewall.md).