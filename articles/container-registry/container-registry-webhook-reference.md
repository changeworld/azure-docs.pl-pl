---
title: Odwołanie do schematu elementu webhook Azure rejestru kontenera
description: Element Webhook żądania JSON ładunku odwołanie do rejestru kontenera platformy Azure.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: f62477a4c68abf1617d9689047913fd820ee5461
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32166011"
---
# <a name="azure-container-registry-webhook-reference"></a>Informacje dotyczące elementu webhook rejestru kontenera platformy Azure

Możesz [skonfigurować elementów webhook](container-registry-webhook.md) dla Twojej rejestru kontenera, który generuje zdarzenia, gdy niektóre akcje są wykonywane na nim. Na przykład można włączyć elementów webhook wyzwalająca kontener obrazu `push` i `delete` operacji. Po wyzwoleniu elementu webhook rejestru kontenera Azure wystawia żądanie HTTP lub HTTPS, zawierający informacje o zdarzeniu do punktu końcowego, który określisz. Punkt końcowy można przetworzyć elementu webhook i odpowiednio działają.

W poniższych sekcjach opisano schematu generowanych przez zdarzenia obsługiwanych żądań elementu webhook. Sekcje zdarzeń zawierają ładunek schemat dla typu zdarzenia, przykładowy ładunek żądania i co najmniej jeden przykładowe polecenia wyzwalających elementu webhook.

Aby uzyskać informacje o konfigurowaniu elementów webhook do rejestru kontenera platformy Azure, zobacz [elementów webhook za pomocą rejestru kontenera Azure](container-registry-webhook.md).

## <a name="webhook-requests"></a>Żądania elementu Webhook

### <a name="http-request"></a>Żądanie HTTP

Wyzwalane elementu webhook sprawia, że HTTP `POST` żądania do punktu końcowego adresu URL określonego podczas konfigurowania elementu webhook.

### <a name="http-headers"></a>Nagłówki HTTP

Żądania elementu Webhook zawierać `Content-Type` z `application/json` , jeśli nie określono `Content-Type` niestandardowy nagłówek z elementu webhook.

Nie inne nagłówki nie są dodawane do żądania poza te niestandardowe nagłówki, może być określona dla elementu webhook.

## <a name="push-event"></a>Wypychanie zdarzeń

Element Webhook wyzwalane, gdy obraz kontener jest przypisany do repozytorium.

### <a name="push-event-payload"></a>Ładunek zdarzenia wypychania

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia, elementu webhook.|
|`timestamp`|DateTime|Czas, o której zostało wyzwolone zdarzeń elementu webhook.|
|`action`|Ciąg|Akcja, który wywołał zdarzenie elementu webhook.|
|[docelowy](#target)|Typ złożony|Obiekt docelowy zdarzeń, który wywołał zdarzenie elementu webhook.|
|[Żądanie](#request)|Typ złożony|Żądanie, który wygenerował zdarzenie elementu webhook.|

### <a name="target"></a>docelowy

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME odwołuje się do obiektu.|
|`size`|Int32|Liczba bajtów treści. Taka sama jak pole długości.|
|`digest`|Ciąg|Skrót zawartości, zgodnie z definicją w specyfikacji interfejsu API rejestru V2 HTTP.|
|`length`|Int32|Liczba bajtów treści. Taka sama jak rozmiar pola.|
|`repository`|Ciąg|Nazwa repozytorium.|
|`tag`|Ciąg|Nazwa tagu obrazu.|

### <a name="request"></a>Żądanie

|Element|Typ|Opis|
|------------------|----------|-----------|
|`id`|Ciąg|Identyfikator żądania, który zainicjował zdarzenia.|
|`host`|Ciąg|Nazwa hosta dostępne z zewnątrz wystąpienia rejestru, jak określono w nagłówku HTTP hosta na przychodzące żądania.|
|`method`|Ciąg|Metoda żądania, który wygenerował zdarzenie.|
|`useragent`|Ciąg|Nagłówek agenta użytkownika żądania.|

### <a name="payload-example-push-event"></a>Przykładowy ładunek: zdarzenie wypychania

```JSON
{
  "id": "cb8c3971-9adc-488b-bdd8-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-86cd-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Przykład [interfejsu wiersza polecenia Docker](https://docs.docker.com/engine/reference/commandline/cli/) polecenia, które wyzwala **wypychania** webhook zdarzeń:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Usuń zdarzenia

Wyzwalane po usunięciu repozytorium lub manifestu elementu Webhook. Nie są uruchamiane po usunięciu znacznika.

### <a name="delete-event-payload"></a>Usuń ładunek zdarzenia

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia, elementu webhook.|
|`timestamp`|DateTime|Czas, o której zostało wyzwolone zdarzeń elementu webhook.|
|`action`|Ciąg|Akcja, który wywołał zdarzenie elementu webhook.|
|[docelowy](#delete_target)|Typ złożony|Obiekt docelowy zdarzeń, który wywołał zdarzenie elementu webhook.|
|[Żądanie](#delete_request)|Typ złożony|Żądanie, który wygenerował zdarzenie elementu webhook.|

### <a name="delete_target"></a> docelowy

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME odwołuje się do obiektu.|
|`digest`|Ciąg|Skrót zawartości, zgodnie z definicją w specyfikacji interfejsu API rejestru V2 HTTP.|
|`repository`|Ciąg|Nazwa repozytorium.|

### <a name="delete_request"></a> Żądanie

|Element|Typ|Opis|
|------------------|----------|-----------|
|`id`|Ciąg|Identyfikator żądania, który zainicjował zdarzenia.|
|`host`|Ciąg|Nazwa hosta dostępne z zewnątrz wystąpienia rejestru, jak określono w nagłówku HTTP hosta na przychodzące żądania.|
|`method`|Ciąg|Metoda żądania, który wygenerował zdarzenie.|
|`useragent`|Ciąg|Nagłówek agenta użytkownika żądania.|

### <a name="payload-example-delete-event"></a>Przykładowy ładunek: usuwanie zdarzeń

```JSON
{
    "id": "afc359ce-df7f-4e32-bdde-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-807f-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Przykład [Azure CLI 2.0](/cli/azure/acr) polecenia tego wyzwalacza **usunąć** webhook zdarzeń:

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>Kolejne kroki

[Za pomocą elementów webhook rejestru kontenera platformy Azure](container-registry-webhook.md)