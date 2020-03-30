---
title: Konfigurowanie zapory IP dla tematów lub domen usługi Azure Event Grid (wersja zapoznawcza)
description: W tym artykule opisano sposób konfigurowania ustawień zapory dla tematów lub domen w siatce zdarzeń.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299870"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Konfigurowanie zapory IP dla tematów lub domen usługi Azure Event Grid (wersja zapoznawcza)
Domyślnie temat i domena są dostępne z Internetu, o ile żądanie jest wyposażone w prawidłowe uwierzytelnianie i autoryzację. Zaporą IP można ją ograniczyć do tylko zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Wydawcy pochodzący z innego adresu IP zostaną odrzuceni i otrzymają odpowiedź 403 (zabronione). Aby uzyskać więcej informacji na temat funkcji zabezpieczeń sieci obsługiwanych przez siatkę zdarzeń, zobacz [Zabezpieczenia sieci dla siatki zdarzeń](network-security.md).

W tym artykule opisano sposób konfigurowania ustawień zapory IP dla tematów lub domen usługi Azure Event Grid.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak używać witryny Azure Portal do tworzenia reguł zapory przychodzącej zapory IP. Kroki przedstawione w tej sekcji są dla tematów. Podobne kroki można wykonać do tworzenia reguł przychodzących **adresów**IP dla domen . 

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do tematu lub domeny siatki zdarzeń i przełącz się do karty **Sieć.**
2. Wybierz **sieci publiczne,** aby umożliwić dostęp do zasobu całej sieci, w tym Internetu. 

    Ruch można ograniczyć przy użyciu reguł zapory opartej na protokãoła IP. Określ pojedynczy adres IPv4 lub zakres adresów IP w notacji CIDR (Classless inter-domain routingu). 

    ![Strona sieci publicznych](./media/configure-firewall/public-networks-page.png)
3. Wybierz **prywatne punkty końcowe tylko,** aby zezwolić tylko na prywatne połączenia punktów końcowych, aby uzyskać dostęp do tego zasobu. Użyj **karty Prywatne połączenia punktów końcowych** na tej stronie, aby zarządzać połączeniami. 

    ![Strona sieci publicznych](./media/configure-firewall/private-endpoints-page.png)
4. Wybierz pozycję **Zapisz** na pasku narzędzi. 



## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
W tej sekcji pokazano, jak używać poleceń interfejsu wiersza polecenia platformy Azure do tworzenia tematów z przychodzącymi regułami IP. Kroki przedstawione w tej sekcji są dla tematów. Podobne kroki można wykonać do tworzenia reguł przychodzących **adresów**IP dla domen . 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Włączanie dostępu do sieci publicznej dla istniejącego tematu
Domyślnie dostęp do sieci publicznej jest włączony dla tematów i domen. Ruch można ograniczyć, konfigurując przychodzące reguły zapory IP. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Wyłączanie dostępu do sieci publicznej dla istniejącego tematu
Gdy dostęp do sieci publicznej jest wyłączony dla tematu lub domeny, ruch za pośrednictwem publicznego Internetu jest niedozwolony. Dostęp do tych zasobów będą mogły uzyskać tylko połączenia prywatnych punktów końcowych. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Tworzenie tematu z przychodzącymi regułami ip
Następujące przykładowe polecenie interfejsu wiersza polecenia tworzy temat siatki zdarzeń z przychodzącymi regułami IP w jednym kroku. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Najpierw utwórz temat, a następnie dodaj przychodzące reguły ip
W tym przykładzie najpierw tworzy temat siatki zdarzeń, a następnie dodaje przychodzące reguły IP dla tematu w osobnym poleceniu. Aktualizuje również przychodzące reguły IP, które zostały ustawione w drugim poleceniu. 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>Korzystanie z programu PowerShell
W tej sekcji pokazano, jak używać poleceń programu Azure PowerShell do tworzenia tematów usługi Azure Event Grid z przychodzącymi regułami zapory IP. Kroki przedstawione w tej sekcji są dla tematów. Podobne kroki można wykonać do tworzenia reguł przychodzących **adresów**IP dla domen . 

### <a name="prerequisite"></a>Wymagania wstępne
Postępuj zgodnie z instrukcjami [dotyczącymi jak: Użyj portalu, aby utworzyć aplikację i jednostkę usługi Azure AD, która może uzyskiwać dostęp](../active-directory/develop/howto-create-service-principal-portal.md) do zasobów w celu utworzenia aplikacji usługi Azure Active Directory i zanotowania następujących wartości:

- Identyfikator katalogu (dzierżawcy)
- Identyfikator aplikacji (klienta)
- Klucz tajny aplikacji (klienta)

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>Włączanie dostępu do sieci publicznej dla istniejącego tematu
Domyślnie dostęp do sieci publicznej jest włączony dla tematów i domen. Ruch można ograniczyć, konfigurując przychodzące reguły zapory IP. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Wyłączanie dostępu do sieci publicznej dla istniejącego tematu
Gdy dostęp do sieci publicznej jest wyłączony dla tematu lub domeny, ruch za pośrednictwem publicznego Internetu jest niedozwolony. Dostęp do tych zasobów będą mogły uzyskać tylko połączenia prywatnych punktów końcowych. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Tworzenie tematu siatki zdarzeń z regułami przychodzącymi w jednym kroku

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Najpierw utwórz temat siatki zdarzeń, a następnie dodaj przychodzące reguły ip

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące monitorowania dostaw zdarzeń, zobacz [Monitorowanie dostarczania komunikatów w sieci zdarzeń](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
