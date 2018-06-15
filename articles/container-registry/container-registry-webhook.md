---
title: Azure rejestru kontener elementów webhook
description: Dowiedz się, jak używać elementów webhook na zdarzenia wyzwalacza wystąpieniu określonych akcji w repozytoriami rejestru.
services: container-registry
author: neilpeterson
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: nepeters
ms.openlocfilehash: cf71deda9ec9053a91596c17e18fc797995754e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164374"
---
# <a name="using-azure-container-registry-webhooks"></a>Za pomocą elementów webhook rejestru kontenera platformy Azure

Rejestru kontenera platformy Azure przechowywanych i zarządzanych prywatnej kontenera obrazy usługi Docker, podobnie jak Centrum Docker przechowuje publiczny obrazy usługi Docker. Można użyć elementów webhook na zdarzenia wyzwalacza, gdy pewne akcje została wykonana w jednym z repozytoriami rejestru. Elementów Webhook można odpowiadanie na zdarzenia na poziomie rejestru lub może być zakres do tagu określonych repozytorium.

Aby uzyskać szczegółowe informacje dotyczące żądań elementu webhook, zobacz [odwołanie do schematu elementu webhook rejestru kontenera Azure](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Rejestru kontenera platformy Azure — Tworzenie rejestru kontenera w Twojej subskrypcji platformy Azure. Na przykład użyć [portalu Azure](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia Azure](container-registry-get-started-azure-cli.md).
* Zainstaluj interfejs wiersza polecenia — Konfigurowanie lokalnego komputera jako hosta Docker i dostępu do poleceń interfejsu wiersza polecenia Docker docker [aparatem platformy Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Tworzenie elementu webhook w portalu Azure

1. Zaloguj się do [portalu Azure](https://portal.azure.com)
1. Przejdź do rejestru kontenera, w którym chcesz utworzyć elementu webhook.
1. W obszarze **usług**, wybierz pozycję **elementów Webhook**.
1. Wybierz **Dodaj** na pasku narzędzi elementu webhook.
1. Zakończenie *tworzenia elementu webhook* formularza z następującymi informacjami:

| Wartość | Opis |
|---|---|
| Name (Nazwa) | Nazwa, która ma zostać przypisany do elementu webhook. Może zawierać tylko małe litery i cyfry i musi składać się z 5 – 50 znaków. |
| Identyfikator URI usługi | Identyfikator URI, gdzie elementu webhook wysłać powiadomienia POST. |
| Nagłówki niestandardowe | Nagłówki, które mają być przekazywane wraz z żądaniem POST. Należy je w "klucz: wartość" format. |
| Akcje wyzwalacza | Akcje, które mogą powodować elementu webhook. Elementów Webhook aktualnie może zostać wyzwolone przez obraz wypychania i/lub akcje usuwania. |
| Stan | Stan elementu webhook po jego utworzeniu. Jest ona włączona domyślnie. |
| Zakres | Zakres, w którym działa elementu webhook. Domyślnie zakres jest dla wszystkich zdarzeń w rejestrze. Można ją określić dla repozytorium lub znacznik w formacie "repozytorium: tag". |

Przykład elementu webhook formularza:

![Tworzenie elementu webhook ACR interfejsu użytkownika w portalu Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Tworzenie elementu webhook wiersza polecenia platformy Azure

Aby utworzyć elementu webhook przy użyciu wiersza polecenia platformy Azure, użyj [tworzenia elementu webhook acr az](/cli/azure/acr/webhook#az_acr_webhook_create) polecenia.

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Element webhook testu

### <a name="azure-portal"></a>Azure Portal

Przed przy użyciu elementu webhook w kontenerze obrazu wypychania i akcje usuwania, można sprawdzić za pomocą **Ping** przycisku. Polecenie ping wysyła żądanie POST ogólnych do określonego punktu końcowego i dzienniki odpowiedzi. Za pomocą funkcji ping może pomóc sprawdzić, czy zostały poprawnie skonfigurowane elementu webhook.

1. Wybierz element webhook, który ma zostać przetestowana.
2. Na górnym pasku narzędzi wybierz **Ping**.
3. Sprawdź odpowiedzi punktu końcowego w **stan HTTP** kolumny.

![Tworzenie elementu webhook ACR interfejsu użytkownika w portalu Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przetestować element webhook ACR z wiersza polecenia platformy Azure, użyj [az acr elementu webhook ping](/cli/azure/acr/webhook#az_acr_webhook_ping) polecenia.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Aby wyświetlić wyniki, użyj [webhook acr az listy zdarzenia](/cli/azure/acr/webhook#list-events) polecenia.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Usuń element webhook

### <a name="azure-portal"></a>Azure Portal

Każdy element webhook może zostać usunięta przez wybranie elementu webhook, a następnie **usunąć** przycisk w portalu Azure.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Kolejne kroki

[Odwołanie do schematu elementu webhook Azure rejestru kontenera](container-registry-webhook-reference.md)