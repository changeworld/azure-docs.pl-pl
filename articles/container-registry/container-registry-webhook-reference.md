---
title: Dokumentacja schematu elementu webhook w usłudze Azure Container Registry
description: Element Webhook żądania JSON ładunku odwołanie do usługi Azure Container Registry.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: 87fe978416c29b50abeef0e0a6624d7440dd87ef
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644690"
---
# <a name="azure-container-registry-webhook-reference"></a>Dokumentacja elementu webhook w usłudze Azure Container Registry

Możesz [konfigurowania elementów webhook](container-registry-webhook.md) dla usługi container registry, które generują zdarzenia, gdy niektóre akcje są wykonywane przed nim. Na przykład, aby umożliwić elementy webhook, które są wyzwalane w obrazie kontenera `push` i `delete` operacji. Po wyzwoleniu elementu webhook usługi Azure Container Registry wysyła żądanie HTTP lub HTTPS zawierający informacje o zdarzeniu do punktu końcowego, które określisz. Punkt końcowy usługi można przetworzyć elementu webhook i podejmowanie odpowiednich działań.

Schemat żądania elementu webhook generowanych przez zdarzenia obsługiwane można znaleźć w poniższych sekcjach. Sekcje zdarzeń zawierają ładunek schemat dla typu zdarzenia, przykładowy ładunek żądania i co najmniej jeden przykładowe polecenia, które będą wyzwalać elementu webhook.

Aby uzyskać informacji na temat konfigurowania elementów webhook do usługi Azure container registry, zobacz [elementów webhook przy użyciu usługi Azure Container Registry](container-registry-webhook.md).

## <a name="webhook-requests"></a>Żądania elementu Webhook

### <a name="http-request"></a>Żądanie HTTP

Wyzwolono element webhook sprawia, że HTTP `POST` żądanie do punktu końcowego adresu URL, określony podczas konfigurowania elementu webhook.

### <a name="http-headers"></a>Nagłówki HTTP

Żądania elementu Webhook zawierać `Content-Type` z `application/json` , jeśli nie określono `Content-Type` nagłówka niestandardowego do usługi elementu webhook.

Żadne inne nagłówki są dodawane do żądania poza te nagłówki niestandardowe, które użytkownik może określić dla elementu webhook.

## <a name="push-event"></a>Wypychanie zdarzeń

Element Webhook jest wyzwalany, gdy obraz kontenera zostanie przypisany do repozytorium.

### <a name="push-event-payload"></a>Wypychanie ładunek zdarzenia

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Godzina, o której zostało wyzwolone zdarzenie elementu webhook.|
|`action`|Ciąg|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[Docelowy](#target)|Typ złożony|Miejsce docelowe zdarzeń, która wyzwoliła zdarzenie elementu webhook.|
|[Żądanie](#request)|Typ złożony|Żądanie, która wygenerowała zdarzenie elementu webhook.|

### <a name="target"></a>cel

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME przywoływanego obiektu.|
|`size`|Int32|Liczba bajtów treści. Taka sama jak długość pola.|
|`digest`|Ciąg|Skrót zawartości, zgodnie z definicją w specyfikacji interfejsu API HTTP V2 rejestru.|
|`length`|Int32|Liczba bajtów treści. Taka sama jak rozmiar pola.|
|`repository`|Ciąg|Nazwa repozytorium.|
|`tag`|Ciąg|Nazwa tagu obrazu.|

### <a name="request"></a>wyślij żądanie

|Element|Typ|Opis|
|------------------|----------|-----------|
|`id`|Ciąg|Identyfikator żądania, który zainicjował zdarzenie.|
|`host`|Ciąg|Dostępne z zewnątrz nazwę hosta wystąpienia rejestru, określony przez nagłówka hosta HTTP na żądań przychodzących.|
|`method`|Ciąg|Metoda żądania, który wygenerował zdarzenie.|
|`useragent`|Ciąg|Nagłówek agenta użytkownika żądania.|

### <a name="payload-example-push-event"></a>Przykładowy ładunek: wypychania zdarzeń

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

Przykład [interfejsu wiersza polecenia Docker](https://docs.docker.com/engine/reference/commandline/cli/) polecenia, które wyzwala **wypychania** zdarzeń elementu webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Usuń zdarzenie

Element Webhook jest wyzwalany, gdy repozytorium lub manifest jest usuwany. Nie wyzwolone po usunięciu tagów.

### <a name="delete-event-payload"></a>Usuń ładunek zdarzenia

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Godzina, o której zostało wyzwolone zdarzenie elementu webhook.|
|`action`|Ciąg|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[Docelowy](#delete_target)|Typ złożony|Miejsce docelowe zdarzeń, która wyzwoliła zdarzenie elementu webhook.|
|[Żądanie](#delete_request)|Typ złożony|Żądanie, która wygenerowała zdarzenie elementu webhook.|

### <a name="delete_target"></a> Docelowy

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME przywoływanego obiektu.|
|`digest`|Ciąg|Skrót zawartości, zgodnie z definicją w specyfikacji interfejsu API HTTP V2 rejestru.|
|`repository`|Ciąg|Nazwa repozytorium.|

### <a name="delete_request"></a> Żądanie

|Element|Typ|Opis|
|------------------|----------|-----------|
|`id`|Ciąg|Identyfikator żądania, który zainicjował zdarzenie.|
|`host`|Ciąg|Dostępne z zewnątrz nazwę hosta wystąpienia rejestru, określony przez nagłówka hosta HTTP na żądań przychodzących.|
|`method`|Ciąg|Metoda żądania, który wygenerował zdarzenie.|
|`useragent`|Ciąg|Nagłówek agenta użytkownika żądania.|

### <a name="payload-example-delete-event"></a>Przykładowy ładunek: Usuń zdarzenie

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

Przykład [wiersza polecenia platformy Azure](/cli/azure/acr) poleceń tym wyzwalacza **Usuń** zdarzeń elementu webhook:

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>Kolejne kroki

[Przy użyciu elementów webhook usługi Azure Container Registry](container-registry-webhook.md)