---
title: Zarządzanie dużych zestawów tematów w usłudze Azure Event Grid z domenami zdarzeń
description: Pokazuje, jak zarządzać dużych zestawów tematów w usłudze Azure Event Grid i publikowania zdarzeń za pomocą zdarzeń domeny.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: c49044d8bd96efb7e86cf54509c32033900be305
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561784"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Zarządzaj tematami i publikowania zdarzeń za pomocą zdarzeń domeny

W tym artykule przedstawiono sposób:

* Tworzenie domeny usługi Event Grid
* Subskrybować tematy usługi event grid
* Wyświetl listę kluczy
* Publikowanie zdarzeń do domeny

Aby dowiedzieć się więcej na temat domen zdarzeń, zobacz [zrozumieć domen zdarzeń związanych z zarządzaniem tematy usługi Event Grid](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalowanie funkcji w wersji zapoznawczej

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Tworzenie domeny zdarzeń

Aby zarządzać dużych zestawów tematów, Tworzenie domeny zdarzeń.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```

Pomyślnie utworzono zwraca następujące wartości:

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

Uwaga `endpoint` i `id` ponieważ są one wymagane do zarządzania domeny i publikowania zdarzeń.

## <a name="manage-access-to-topics"></a>Zarządzanie dostępem do tematów

Zarządzanie dostępem do tematów odbywa się za pośrednictwem [przypisania roli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Przypisanie roli używa kontroli dostępu opartej na rolach, aby ograniczyć operacje dla zasobów platformy Azure do grona upoważnionych użytkowników w określonym zakresie.

Usługa Event Grid ma dwie wbudowane role, których można użyć do przypisania określonym użytkownikom dostęp do różnych tematów w domenie. Te role są `EventGrid EventSubscription Contributor (Preview)`, która umożliwia tworzenie i usuwanie subskrypcji, i `EventGrid EventSubscription Reader (Preview)`, wówczas tylko lista subskrypcji zdarzeń.

Następujące limity polecenia wiersza polecenia platformy Azure `alice@contoso.com` do tworzenia i usuwania subskrypcji zdarzeń tylko na temat `demotopic1`:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Następujące limity polecenia programu PowerShell `alice@contoso.com` do tworzenia i usuwania subskrypcji zdarzeń tylko na temat `demotopic1`:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Aby uzyskać więcej informacji na temat zarządzania dostępem do operacji usługi Event Grid, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Tworzenie tematów i subskrypcji

Usługa Event Grid automatycznie tworzy i zarządza nimi odpowiedni temat w domenie, oparte na wywołanie w celu utworzenia subskrypcji zdarzeń dla tematu domeny. Istnieje nie osobnym kroku, aby utworzyć temat w domenie. Podobnie po usunięciu ostatniej subskrypcji zdarzeń dla tematu tematu jest również usunięte.

Subskrybowanie tematu w domenie jest taka sama jak subskrypcja innych zasobów platformy Azure. Identyfikator zasobu źródłowego należy określić identyfikator domeny zdarzenia zwrócony podczas tworzenia domeny, wcześniej. Aby określić temat chcesz subskrybować, Dodaj `/topics/<my-topic>` na końcu identyfikator zasobu źródłowej. Aby utworzyć subskrypcję zdarzeń zakres domeny, który odbiera wszystkie zdarzenia w domenie, należy określić identyfikator zdarzenia domeny bez określenia wszystkich tematów.

Zazwyczaj użytkownik zostanie przyznane dostęp, na liście poprzedniej sekcji będzie utworzyć subskrypcję. Aby uprościć ten artykuł, należy utworzyć subskrypcję. 

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

Punkt końcowy testu do subskrybowania zdarzenia, należy zawsze można wdrożyć [aplikacji sieci web wstępnie skompilowanych](https://github.com/Azure-Samples/azure-event-grid-viewer) wyświetlającą zdarzeń przychodzących. Możesz wysłać zdarzenia do witryny sieci Web test o `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Uprawnienia ustawione dla tematu są przechowywane w usłudze Azure Active Directory i muszą zostać jawnie usunięte. Usuwanie subskrypcji zdarzeń nie odwołać użytkownikom dostępu do utworzenia subskrypcji zdarzeń, jeśli mają dostęp do zapisu na temat.


## <a name="publish-events-to-an-event-grid-domain"></a>Publikowanie zdarzeń do domeny usługi Event Grid

Publikowania zdarzeń w domenie jest taka sama jak [publikowania do tematu niestandardowego](./post-to-custom-topic.md). Jednak zamiast publikowania do tematu niestandardowego, możesz opublikować wszystkie zdarzenia do punktu końcowego domeny. W formacie JSON dane zdarzenia należy określić temat, który chcesz zdarzeń, aby przejść do. Następującą tablicę zdarzenia mogłoby spowodować zdarzenie z `"id": "1111"` do tematu `demotopic1` podczas zdarzenia o `"id": "2222"` będą wysyłane do tematu `demotopic2`:

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

Aby uzyskać punkt końcowy domeny przy użyciu wiersza polecenia platformy Azure, użyj

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Aby pobrać klucze służące do domeny, należy użyć:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Aby uzyskać punkt końcowy domeny przy użyciu programu PowerShell, użyj

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Aby pobrać klucze służące do domeny, należy użyć:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

A następnie użyj ulubionych metodę wprowadzania metodę POST protokołu HTTP do publikowania zdarzeń do Twojej domeny usługi Event Grid.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat ogólne pojęcia związane z domenami zdarzeń i dlaczego są one przydatne, zobacz [omówienie pojęć dotyczących domen zdarzeń](event-domains.md).
