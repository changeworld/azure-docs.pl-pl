---
title: Jak zarządzać dużych zestawów tematów w usłudze Azure Event Grid i publikowania zdarzeń do nich za pomocą zdarzeń domeny
description: Pokazuje, jak tworzyć i Zarządzaj tematami w usłudze Azure Event Grid i publikować je za pomocą zdarzeń domeny zdarzenia.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 48a5356b03e38e864ba76f048febdb0b040893f5
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634050"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Zarządzaj tematami i publikowania zdarzeń za pomocą zdarzeń domeny

W tym artykule przedstawiono sposób:

* Tworzenie domeny siatki zdarzeń
* Subskrybowanie tematów
* Wyświetl listę kluczy
* Publikowanie zdarzeń do domeny

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Tworzenie domeny zdarzeń

Tworzenie domeny zdarzeń może odbywać się za pośrednictwem `eventgrid` rozszerzenie [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Po utworzeniu domeny można użyć go do zarządzania dużymi zestawami tematów.

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

Pomyślnie utworzono zwróci następujące wartości:

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

Uwaga `endpoint` i `id` zgodnie z ich będą musieli Zarządzanie domeną i publikowania zdarzeń.

## <a name="create-topics-and-subscriptions"></a>Tworzyć tematy i subskrypcje

Usługa Event Grid automatycznie tworzy i zarządza nimi odpowiedni temat w domenie, oparte na wywołanie w celu utworzenia subskrypcji zdarzeń dla tematu domeny. Istnieje nie osobnym kroku, aby utworzyć temat w domenie. Podobnie po usunięciu ostatniej subskrypcji zdarzeń dla tematu tematu jest również usunięte.

Subskrybowanie tematu w domenie jest taka sama jak subskrypcja innych zasobów platformy Azure:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

Podany identyfikator zasobu jest taki sam identyfikator zwrócony podczas tworzenia domeny wcześniej. Aby określić temat chcesz subskrybować, Dodaj `/topics/<my-topic>` do końca identyfikatora zasobu.

Aby utworzyć subskrypcję zdarzeń zakres domeny, który odbiera wszystkie zdarzenia w domenie, należy nadać do domeny, co `resource-id` bez określenia wszystkich tematów, na przykład `/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>`.

Punkt końcowy testu do subskrybowania zdarzenia, należy zawsze można wdrożyć [aplikacji sieci web wstępnie skompilowanych](https://github.com/Azure-Samples/azure-event-grid-viewer) wyświetlającą zdarzeń przychodzących. Możesz wysłać zdarzenia do witryny sieci Web test o `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Uprawnienia ustawione dla tematu są przechowywane w usłudze Azure Active Directory i muszą zostać jawnie usunięte. Usuwanie subskrypcji zdarzeń nie odwołać użytkownikom dostępu do utworzenia subskrypcji zdarzeń, jeśli mają dostęp do zapisu na temat.

## <a name="manage-access-to-topics"></a>Zarządzanie dostępem do tematów

Zarządzanie dostępem do tematów odbywa się za pośrednictwem [przypisania roli](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-cli). Przypisanie roli używa roli na podstawie kontroli dostępu, aby ograniczyć operacje dla zasobów platformy Azure do grona upoważnionych użytkowników w określonym zakresie.

Usługa Event Grid ma dwie wbudowane role, których można użyć do przypisania określonym użytkownikom dostęp do różnych tematów w domenie. Te role są `EventGrid EventSubscription Contributor (Preview)`, która umożliwia tworzenie i usuwanie subskrypcji, i `EventGrid EventSubscription Reader (Preview)`, wówczas tylko lista subskrypcji zdarzeń.

Następujące polecenie będzie ograniczona `alice@contoso.com` do tworzenia i usuwania subskrypcji zdarzeń tylko na temat `foo`:

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

Zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](./security-authentication.md) Aby uzyskać więcej informacji na temat:

* Kontrola dostępu do zarządzania
* Typy operacji
* Tworzenie definicji ról niestandardowych

## <a name="publish-events-to-an-event-grid-domain"></a>Publikowanie zdarzeń do domeny usługi Event Grid

Publikowania zdarzeń w domenie jest taka sama jak [publikowania do tematu niestandardowego](./post-to-custom-topic.md). Jedyna różnica polega na tym, czy należy określić temat, który chcesz, aby każde zdarzenie, aby przejść do. Następującą tablicę zdarzenia mogłoby spowodować zdarzenie z `"id": "1111"` do tematu `foo` podczas zdarzenia o `"id": "2222"` będą wysyłane do tematu `bar`:

```json
[{
  "topic": "foo",
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
  "topic": "bar",
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

Aby pobrać klucze służące do domeny, użyj:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

A następnie użyj ulubionych metodę wprowadzania metodę POST protokołu HTTP do publikowania zdarzeń w domenie siatki zdarzeń.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat ogólne pojęcia związane z domenami zdarzeń i dlaczego są one przydatne, zobacz [omówienie pojęć dotyczących domen zdarzeń](./event-domains.md).