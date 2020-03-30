---
title: Aktualizowanie warstwy cenowej tematu lub domeny usługi Azure Event Grid
description: W tym artykule opisano sposób aktualizowania warstwy cenowej tematu lub domeny usługi Azure Event Grid (od wersji premium od wersji premium do podstawowej) przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure i programu Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300143"
---
# <a name="update-pricing-tier"></a>Aktualizowanie warstwy cenowej 
W tym artykule pokazano, jak zaktualizować warstwę cenową tematu lub domeny usługi Azure Event Grid przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure i programu Azure PowerShell. 

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak zmienić warstwę cenową tematu lub domeny w witrynie Azure portal. 

### <a name="overview-page"></a>Strona przeglądowa
Warstwę cenową tematu lub domeny można zmienić na stronie **Przegląd.** W poniższym przykładzie pokazano, jak uaktualnić temat z warstwy podstawowej do warstwy premium. Kroki, aby przejść na dół z warstwy premium do warstwy podstawowej są podobne.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do tematu lub strony domeny. 
2. Na stronie **Przegląd** wybierz bieżącą warstwę cenową (w poniższym przykładzie jest **to podstawowe).**
    
    ![Wybierz bieżącą warstwę cenową](./media/update-tier/select-tier.png)
3. Na stronie **Warstwa cenowa** zmień warstwę i wybierz przycisk **OK**. 

    ![Aktualizowanie warstwy cenowej](./media/update-tier/change-tier.png)
4. Sprawdź stan operacji na stronie **Powiadomienia.**

    ![Stan aktualizacji](./media/update-tier/status.png)    
5. Upewnij się, że na stronie Przegląd jest widoczna zaktualizowana **warstwa.** 

    ![Stan aktualizacji](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Strona sieciowa
Możesz **uaktualnić** z warstwy podstawowej do warstwy premium na stronie **Sieci.** Nie możesz jednak przejść z warstwy premium do warstwy podstawowej na tej stronie. 

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do tematu lub strony domeny. 
2. Na stronie **Sieć** przełącz się na kartę **Prywatne połączenia punktów końcowych (podgląd).** 
3. Jeśli bieżąca warstwa cenowa jest **podstawowa,** zostanie wyświetlony następujący komunikat. Wybierz ją. 

    ![Aktualizacja warstwy na stronie połączenia prywatnych punktów końcowych](./media/update-tier/private-endpoint-connections-page.png)
4. Na stronie **Warstwa cenowa Aktualizacja do wersji premium** wybierz pozycję **Tak**. 
    
    ![Potwierdź uaktualnienie](./media/update-tier/message-private-endpoint-connection.png)
5. Sprawdź stan operacji na stronie **Powiadomienia.**

    ![Stan aktualizacji](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
W tej sekcji pokazano, jak używać poleceń interfejsu wiersza polecenia platformy Azure do zmiany warstwy cenowej tematu lub domeny. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Uaktualnianie tematu z podstawowego do premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Obniż poziom tematyki z premium na podstawową

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Uaktualnianie domeny z podstawowej do premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Obniżanie poziomu domeny z wersji premium na podstawową

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
W tej sekcji pokazano, jak za pomocą poleceń programu PowerShell zmienić warstwę cenową tematu lub domeny. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Przygotowywanie tokenu i nagłówków dla wywołań interfejsu API REST 
Uruchom następujące polecenia wymagane, aby uzyskać token uwierzytelniania do użycia z wywołaniami interfejsu API REST i autoryzacji i innych informacji nagłówka. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Uaktualnianie tematu z podstawowego do premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Obniż poziom tematyki z premium na podstawową

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Uaktualnianie domeny z podstawowej do premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Obniżanie poziomu domeny z wersji premium na podstawową

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Następne kroki
W przypadku tematów i domen warstwy premium można skonfigurować prywatne punkty końcowe, aby ograniczyć dostęp tylko z wybranych sieci wirtualnych. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie prywatnych punktów końcowych](configure-private-endpoints.md).
