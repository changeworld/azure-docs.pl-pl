---
title: Publikowanie zdarzeń w domenach zdarzeń za pomocą usługi Azure Event Grid
description: Pokazuje, jak zarządzać dużymi zestawami tematów w usłudze Azure Event Grid i publikować zdarzenia przy użyciu domen zdarzeń.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 1d07227249806b7d54523af66817a170c19354ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72786557"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Zarządzanie tematami i publikowanie zdarzeń przy użyciu domen zdarzeń

W tym artykule pokazano, jak:

* Tworzenie domeny siatki zdarzeń
* Subskrybowanie tematów siatki zdarzeń
* Klawisze listy
* Publikowanie zdarzeń w domenie

Aby dowiedzieć się więcej o domenach zdarzeń, zobacz [Opis domen zdarzeń do zarządzania tematami w siatce zdarzeń](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalowanie funkcji w wersji zapoznawczej

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Tworzenie domeny zdarzenia

Aby zarządzać dużymi zestawami tematów, utwórz domenę zdarzeń.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

Pomyślne utworzenie zwraca następujące wartości:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Należy `endpoint` pamiętać, że i `id` jak są one wymagane do zarządzania domeną i publikowania zdarzeń.

## <a name="manage-access-to-topics"></a>Zarządzanie dostępem do tematów

Zarządzanie dostępem do tematów odbywa się za pomocą [przypisania roli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Przypisanie roli używa kontroli dostępu opartej na rolach, aby ograniczyć operacje na zasobach platformy Azure do autoryzowanych użytkowników w określonym zakresie.

Usługa Event Grid ma dwie wbudowane role, za pomocą których można przypisać dostęp określonych użytkowników do różnych tematów w domenie. Role te `EventGrid EventSubscription Contributor (Preview)`są , co pozwala na tworzenie i `EventGrid EventSubscription Reader (Preview)`usuwanie subskrypcji, i , który pozwala tylko na listę subskrypcji zdarzeń.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)
Następujące polecenia interfejsu `alice@contoso.com` wiersza polecenia platformy Azure ograniczają się `demotopic1`do tworzenia i usuwania subskrypcji zdarzeń tylko na temat:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
Następujące polecenia programu PowerShell ograniczają `alice@contoso.com` tworzenie i usuwanie subskrypcji `demotopic1`zdarzeń tylko na temat:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Aby uzyskać więcej informacji na temat zarządzania dostępem do operacji w siatce zdarzeń, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Tworzenie tematów i subskrypcji

Usługa Siatki zdarzeń automatycznie tworzy odpowiedni temat w domenie i zarządza nim na podstawie wywołania w celu utworzenia subskrypcji zdarzenia dla tematu domeny. Nie ma osobnego kroku do tworzenia tematu w domenie. Podobnie po usunięciu ostatniej subskrypcji zdarzenia dla tematu, temat jest usuwany, jak również.

Subskrybowanie tematu w domenie jest takie samo jak subskrybowanie innych zasobów platformy Azure. W przypadku identyfikatora zasobu źródłowego określ identyfikator domeny zdarzenia zwrócony podczas wcześniejszego tworzenia domeny. Aby określić temat, który chcesz subskrybować, dodaj `/topics/<my-topic>` na końcu identyfikatora zasobu źródłowego. Aby utworzyć subskrypcję zdarzenia zakresu domeny, która odbiera wszystkie zdarzenia w domenie, określ identyfikator domeny zdarzenia bez określania żadnych tematów.

Zazwyczaj użytkownik, któremu udzielono dostępu w poprzedniej sekcji, utworzy subskrypcję. Aby uprościć ten artykuł, należy utworzyć subskrypcję. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Jeśli potrzebujesz punktu końcowego testu, aby subskrybować zdarzenia, zawsze możesz wdrożyć [wstępnie skonfliktową aplikację sieci web,](https://github.com/Azure-Samples/azure-event-grid-viewer) która wyświetla przychodzące zdarzenia. Możesz wysłać swoje wydarzenia do `https://<your-site-name>.azurewebsites.net/api/updates`witryny testowej pod adresem .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Uprawnienia ustawione dla tematu są przechowywane w usłudze Azure Active Directory i muszą zostać usunięte jawnie. Usunięcie subskrypcji zdarzeń nie spowoduje odwołania użytkownikom dostępu do tworzenia subskrypcji zdarzeń, jeśli mają dostęp do zapisu na temat.


## <a name="publish-events-to-an-event-grid-domain"></a>Publikowanie zdarzeń w domenie siatki zdarzeń

Publikowanie zdarzeń w domenie jest takie samo jak [publikowanie w temacie niestandardowym](./post-to-custom-topic.md). Jednak zamiast publikowania w temacie niestandardowym, można opublikować wszystkie zdarzenia do punktu końcowego domeny. W danych zdarzenia JSON określisz temat, do którego chcesz przejść zdarzenia. Następująca tablica zdarzeń spowoduje `"id": "1111"` zdarzenie `demotopic1` z do `"id": "2222"` tematu, `demotopic2`podczas gdy zdarzenie z zostanie wysłany do tematu:

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)
Aby uzyskać punkt końcowy domeny za pomocą interfejsu wiersza polecenia platformy Azure, użyj

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Aby uzyskać klucze domeny, użyj:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
Aby uzyskać punkt końcowy domeny z programem PowerShell, użyj

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Aby uzyskać klucze domeny, użyj:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

A następnie użyj ulubionej metody tworzenia postu HTTP, aby opublikować wydarzenia w domenie event grid.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pojęć wysokiego poziomu w domenach zdarzeń i dlaczego są one przydatne, zobacz [omówienie koncepcyjne domen zdarzeń](event-domains.md).
