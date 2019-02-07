---
title: Elementy webhook usługi Azure Container Registry
description: Dowiedz się, jak korzystać z elementami webhook w celu wyzwalać zdarzenia wystąpieniu określonych akcji w swoich repozytoriów w rejestrze.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/20/2017
ms.author: danlep
ms.openlocfilehash: cbfbe5bf0df1b4f40752b5b233dff6416bcdd309
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770605"
---
# <a name="using-azure-container-registry-webhooks"></a>Przy użyciu elementów webhook usługi Azure Container Registry

Rejestr kontenerów platformy Azure przechowuje i zarządza prywatnych obrazów kontenerów Docker, podobnie jak usługi Docker Hub przechowuje publiczne obrazy platformy Docker. Możesz użyć elementów webhook, aby wyzwalać zdarzenia, gdy określone akcje wykonane w jednym z repozytoriów rejestru. Elementy Webhook mogą reagować na zdarzenia na poziomie rejestru lub ich może zostać obniżone do tagu danego repozytorium.

Aby uzyskać szczegółowe informacje dotyczące żądania elementu webhook, zobacz [odwołanie do schematu elementu webhook usługi Azure Container Registry](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa Azure container registry — Tworzenie rejestru kontenerów w ramach subskrypcji platformy Azure. Na przykład użyć [witryny Azure portal](container-registry-get-started-portal.md) lub [wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
* Zainstaluj interfejs wiersza polecenia — Aby skonfigurować lokalny komputer jako hosta platformy Docker i uzyskać dostęp do poleceń interfejsu wiersza polecenia platformy Docker, platformy docker [aparat platformy Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Utwórz element webhook w witrynie Azure portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do rejestru kontenerów, w której chcesz utworzyć element webhook.
1. W obszarze **usług**, wybierz opcję **elementów Webhook**.
1. Wybierz **Dodaj** na pasku narzędzi elementu webhook.
1. Wykonaj *Utwórz element webhook* formularza następujące informacje:

| Wartość | Opis |
|---|---|
| Name (Nazwa) | Nazwa, która ma zostać przypisany do elementu webhook. Może zawierać tylko małe litery i cyfry i musi być 5 do 50 znaków. |
| Identyfikator URI usługi | Identyfikator URI, gdzie wysłać powiadomienia WPIS elementu webhook. |
| Nagłówki niestandardowe | Nagłówki, które mają być przekazywane wraz z żądaniem POST. Powinny one być w "klucz: wartość" format. |
| Akcje wyzwalacza | Akcje, które mogą powodować elementu webhook. Elementy Webhook obecnie mogą być wywoływane wypychania obrazów i/lub Usuń akcje. |
| Stan | Stan elementu webhook, po jego utworzeniu. Jest ona włączona domyślnie. |
| Zakres | Zakres, w którym działa element webhook. Domyślnie zakres jest dla wszystkich zdarzeń w rejestrze. Można ją określić repozytorium lub tag, używając formatu "repozytorium: tag". |

Przykład elementu webhook formularza:

![Utworzenie elementu webhook rekordu ACR interfejsu użytkownika w witrynie Azure portal](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Utwórz element webhook wiersza polecenia platformy Azure

Aby utworzyć element webhook przy użyciu wiersza polecenia platformy Azure, użyj [tworzenia elementu webhook rekordu acr az](/cli/azure/acr/webhook#az-acr-webhook-create) polecenia.

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testuj element webhook

### <a name="azure-portal"></a>Azure Portal

Przed przy użyciu elementu webhook w kontenerze obrazów wypychania i akcje usuwania, możesz przetestować ją za pomocą **Ping** przycisku. Polecenie ping wysyła żądanie POST ogólnego do określonego punktu końcowego i rejestruje odpowiedzi. Za pomocą funkcji ping może pomóc sprawdzić, czy zostały poprawnie skonfigurowane elementu webhook.

1. Wybierz element webhook, który ma zostać przetestowana.
2. Na górnym pasku narzędzi wybierz **Ping**.
3. Sprawdź odpowiedzi punktu końcowego w **stan HTTP** kolumny.

![Utworzenie elementu webhook rekordu ACR interfejsu użytkownika w witrynie Azure portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przetestować element webhook usługi ACR przy użyciu wiersza polecenia platformy Azure, użyj [az acr elementu webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) polecenia.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Aby wyświetlić wyniki, należy użyć [az acr zdarzenia listy elementów webhook-](/cli/azure/acr/webhook) polecenia.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Usuń element webhook

### <a name="azure-portal"></a>Azure Portal

Każdy element webhook można usunąć, wybierając element webhook i następnie **Usuń** przycisku w witrynie Azure portal.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Kolejne kroki

### <a name="webhook-schema-reference"></a>Odwołanie do schematu elementu Webhook

Aby uzyskać szczegółowe informacje na format i właściwości ładunków zdarzeń JSON, które są emitowane przez usługę Azure Container Registry Zobacz odwołania do schematu elementu webhook:

[Dokumentacja schematu elementu webhook w usłudze Azure Container Registry](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Zdarzeń usługi Event Grid

Oprócz zdarzeń elementu webhook rejestru macierzystego omówionych w tym artykule usługi Azure Container Registry może emitować zdarzenia do usługi Event Grid:

[Szybki start: Wysyłanie zdarzeń rejestru kontenera do usługi Event Grid](container-registry-event-grid-quickstart.md)
