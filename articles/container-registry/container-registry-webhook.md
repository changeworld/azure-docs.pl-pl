---
title: Elementów Webhook, aby odpowiedzieć na akcje rejestru
description: Dowiedz się, jak używać zestawów webhook do wyzwalania zdarzeń, gdy akcje wypychania lub ściągania występują w repozytoriach rejestru.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454375"
---
# <a name="using-azure-container-registry-webhooks"></a>Korzystanie z elementów webhook rejestru kontenerów platformy Azure

Rejestr kontenera platformy Azure przechowuje prywatne obrazy kontenerów platformy Docker i zarządza nimi podobnie, jak koncentrator platformy Docker przechowuje publiczne obrazy platformy Docker. Może również hostować repozytoria [wykresów Helm](container-registry-helm-repos.md) (wersja zapoznawcza), format pakowania do wdrażania aplikacji w uduchowionych w umięśniach. Za pomocą zestawów webhook można wyzwalać zdarzenia, gdy określone akcje mają miejsce w jednym z repozytoriów rejestru. Webhooks może odpowiadać na zdarzenia na poziomie rejestru lub mogą być ograniczone do określonego tagu repozytorium. Za pomocą rejestru [replikowanego geograficznie](container-registry-geo-replication.md) można skonfigurować każdy element webhook, aby odpowiadał na zdarzenia w określonej replice regionalnej.

Aby uzyskać szczegółowe informacje na temat żądań elementu webhook, zobacz [odwołanie do schematu elementu webhook rejestru kontenerów platformy Azure](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa Azure Container Registry — Tworzy rejestr kontenera w subskrypcji platformy Azure. Na przykład użyj [witryny Azure portal](container-registry-get-started-portal.md) lub interfejsu [wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md). Jednostki [SKU rejestru kontenerów platformy Azure](container-registry-skus.md) mają różne przydziały łączy webhook.
* Interfejs wiersza polecenia platformy Docker — Aby skonfigurować lokalny komputer jako hosta platformy Docker i uzyskać dostęp do poleceń interfejsu wiersza polecenia platformy Docker, zainstaluj [aparat platformy Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Tworzenie elementu webhook — witryna Azure portal

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do rejestru kontenerów, w którym chcesz utworzyć element webhook.
1. W obszarze **Usługi**wybierz pozycję **Webhooks**.
1. Wybierz **pozycję Dodaj** na pasku narzędzi elementu webhook.
1. Wypełnij formularz *Tworzenie elementu webhook* o następujące informacje:

| Wartość | Opis |
|---|---|
| Nazwa elementu webhook | Nazwa, którą chcesz nadać elementowi webhook. Może zawierać tylko litery i cyfry i musi mieć długość od 5 do 50 znaków. |
| Lokalizacja | W przypadku rejestru [replikowanego geograficznie](container-registry-geo-replication.md) określ region platformy Azure repliki rejestru. 
| Identyfikator URI usługi | Identyfikator URI, w którym element webhook powinien wysyłać powiadomienia POST. |
| Nagłówki niestandardowe | Nagłówki, które chcesz przekazać wraz z żądaniem POST. Powinny być w formacie "klucz: wartość". |
| Akcje wyzwalania | Akcje wyzwalające element webhook. Akcje obejmują wypychanie obrazu, usuwanie obrazu, wypychanie wykresu helm, usuwanie wykresu helm i kwarantannę obrazu. Można wybrać jedną lub więcej akcji, aby wyzwolić element webhook. |
| Stan | Stan elementu webhook po jego utworzeniu. Jest domyślnie włączona. |
| Zakres | Zakres, w którym działa element webhook. Jeśli nie zostanie określony, zakres jest dla wszystkich zdarzeń w rejestrze. Można go określić dla repozytorium lub tagu przy użyciu formatu "repozytorium:tag" lub "repozytorium:*" dla wszystkich tagów w repozytorium. |

Przykładowy formularz elementu webhook:

![Interfejs użytkownika tworzenia elementu webhook ACR w witrynie Azure portal](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Tworzenie elementu webhook — interfejs wiersza polecenia platformy Azure

Aby utworzyć element webhook przy użyciu interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure, należy użyć [az acr webhook utworzyć](/cli/azure/acr/webhook#az-acr-webhook-create) polecenie. Następujące polecenie tworzy element webhook dla wszystkich zdarzeń usuwania obrazu w *rejestrze mycontainerregistry:*

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Test webhook

### <a name="azure-portal"></a>Portal Azure

Przed użyciem elementu webhook można go przetestować za pomocą przycisku **Ping.** Ping wysyła ogólne żądanie POST do określonego punktu końcowego i rejestruje odpowiedź. Korzystanie z funkcji ping może pomóc w sprawdzeniu prawidłowej konfiguracji elementu webhook.

1. Wybierz element webhook, który chcesz przetestować.
2. Na górnym pasku narzędzi wybierz **polecenie Ping**.
3. Sprawdź odpowiedź punktu końcowego w kolumnie **STAN HTTP.**

![Interfejs użytkownika tworzenia elementu webhook ACR w witrynie Azure portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przetestować element webhook usługi ACR za pomocą interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure, użyj polecenia [az acr webhook ping.](/cli/azure/acr/webhook#az-acr-webhook-ping)

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Aby wyświetlić wyniki, użyj polecenia [az acr webhook list-events.](/cli/azure/acr/webhook)

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Usuwanie elementu webhook

### <a name="azure-portal"></a>Portal Azure

Każdy element webhook można usunąć, wybierając element webhook, a następnie **przycisk Usuń** w witrynie Azure portal.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Następne kroki

### <a name="webhook-schema-reference"></a>Odwołanie do schematu elementu webhook

Aby uzyskać szczegółowe informacje na temat formatu i właściwości ładunków zdarzeń JSON emitowanych przez usługę Azure Container Registry, zobacz odwołanie do schematu elementu webhook:

[Odwołanie do schematu elementu webhook rejestru kontenerów platformy Azure](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Zdarzenia w siatce zdarzeń

Oprócz natywnych zdarzeń elementu webhook rejestru omówione w tym artykule, usługa Azure Container Registry może emitować zdarzenia do siatki zdarzeń:

[Szybki start: wysyłanie zdarzeń rejestru kontenerów do siatki zdarzeń](container-registry-event-grid-quickstart.md)
