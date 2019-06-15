---
title: Dokumentacja schematu elementu webhook w usłudze Azure Container Registry
description: Element Webhook żądania JSON ładunku odwołanie do usługi Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: 4c0845b9cf5194ecbd0ab813997e17e070840f44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61331345"
---
# <a name="azure-container-registry-webhook-reference"></a>Dokumentacja elementu webhook w usłudze Azure Container Registry

Możesz [konfigurowania elementów webhook](container-registry-webhook.md) dla usługi container registry, które generują zdarzenia, gdy niektóre akcje są wykonywane przed nim. Na przykład włączyć elementy webhook, które są wyzwalane, gdy obraz kontenera lub narzędzia Helm jest wypchnięte do rejestru lub usunięta. Po wyzwoleniu elementu webhook usługi Azure Container Registry wysyła żądanie HTTP lub HTTPS zawierający informacje o zdarzeniu do punktu końcowego, które określisz. Punkt końcowy usługi można przetworzyć elementu webhook i podejmowanie odpowiednich działań.

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
|`id`|String|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Godzina, o której zostało wyzwolone zdarzenie elementu webhook.|
|`action`|String|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[target](#target)|Typ złożony|Miejsce docelowe zdarzeń, która wyzwoliła zdarzenie elementu webhook.|
|[request](#request)|Typ złożony|Żądanie, która wygenerowała zdarzenie elementu webhook.|

### <a name="target"></a>target

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|String|Typ MIME przywoływanego obiektu.|
|`size`|Int32|Liczba bajtów treści. Taka sama jak długość pola.|
|`digest`|String|Skrót zawartości, zgodnie z definicją w specyfikacji interfejsu API HTTP V2 rejestru.|
|`length`|Int32|Liczba bajtów treści. Taka sama jak rozmiar pola.|
|`repository`|String|Nazwa repozytorium.|
|`tag`|String|Nazwa tagu obrazu.|

### <a name="request"></a>request

|Element|Typ|Opis|
|------------------|----------|-----------|
|`id`|String|Identyfikator żądania, który zainicjował zdarzenie.|
|`host`|String|Dostępne z zewnątrz nazwę hosta wystąpienia rejestru, określony przez nagłówka hosta HTTP na żądań przychodzących.|
|`method`|String|Metoda żądania, który wygenerował zdarzenie.|
|`useragent`|String|Nagłówek agenta użytkownika żądania.|

### <a name="payload-example-image-push-event"></a>Przykładowy ładunek: obraz wypychania zdarzeń

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Przykład [interfejsu wiersza polecenia Docker](https://docs.docker.com/engine/reference/commandline/cli/) polecenia, które wyzwala obrazu **wypychania** zdarzeń elementu webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Wykres wypychania zdarzeń

Element Webhook jest wyzwalany, gdy wykresu Helm są wypychane do repozytorium.

### <a name="chart-push-event-payload"></a>Ładunek zdarzenia wypychania wykresu

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|String|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Godzina, o której zostało wyzwolone zdarzenie elementu webhook.|
|`action`|String|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[target](#helm_target)|Typ złożony|Miejsce docelowe zdarzeń, która wyzwoliła zdarzenie elementu webhook.|

### <a name="helm_target"></a>target

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|String|Typ MIME przywoływanego obiektu.|
|`size`|Int32|Liczba bajtów treści.|
|`digest`|String|Skrót zawartości, zgodnie z definicją w specyfikacji interfejsu API HTTP V2 rejestru.|
|`repository`|String|Nazwa repozytorium.|
|`tag`|String|Nazwa tagu wykresu.|
|`name`|String|Nazwa wykresu.|
|`version`|String|Wersja wykresu.|

### <a name="payload-example-chart-push-event"></a>Przykładowy ładunek: wykres wypychania zdarzeń

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Przykład [wiersza polecenia platformy Azure](/cli/azure/acr) polecenia, które wyzwala **chart_push** zdarzeń elementu webhook:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Usuń zdarzenie

Wyzwalana przez element Webhook, gdy repozytorium obrazów lub manifest został usunięty. Nie wyzwolone po usunięciu tagów.

### <a name="delete-event-payload"></a>Usuń ładunek zdarzenia

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|String|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Godzina, o której zostało wyzwolone zdarzenie elementu webhook.|
|`action`|String|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[target](#delete_target)|Typ złożony|Miejsce docelowe zdarzeń, która wyzwoliła zdarzenie elementu webhook.|
|[request](#delete_request)|Typ złożony|Żądanie, która wygenerowała zdarzenie elementu webhook.|

### <a name="delete_target"></a> Docelowy

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|String|Typ MIME przywoływanego obiektu.|
|`digest`|String|Skrót zawartości, zgodnie z definicją w specyfikacji interfejsu API HTTP V2 rejestru.|
|`repository`|String|Nazwa repozytorium.|

### <a name="delete_request"></a> Żądanie

|Element|Typ|Opis|
|------------------|----------|-----------|
|`id`|String|Identyfikator żądania, który zainicjował zdarzenie.|
|`host`|String|Dostępne z zewnątrz nazwę hosta wystąpienia rejestru, określony przez nagłówka hosta HTTP na żądań przychodzących.|
|`method`|String|Metoda żądania, który wygenerował zdarzenie.|
|`useragent`|String|Nagłówek agenta użytkownika żądania.|

### <a name="payload-example-image-delete-event"></a>Przykładowy ładunek: zdarzenie usuwania obrazu

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Przykład [wiersza polecenia platformy Azure](/cli/azure/acr) poleceń tym wyzwalacza **Usuń** zdarzeń elementu webhook:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Zdarzenie usuwania wykresu

Gdy wykresu Helm wyzwalana przez element Webhook lub repozytorium zostanie usunięte. 

### <a name="chart-delete-event-payload"></a>Ładunek zdarzenia Usuń wykres

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|String|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Godzina, o której zostało wyzwolone zdarzenie elementu webhook.|
|`action`|String|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[target](#chart_delete_target)|Typ złożony|Miejsce docelowe zdarzeń, która wyzwoliła zdarzenie elementu webhook.|

### <a name="chart_delete_target"></a> Docelowy

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|String|Typ MIME przywoływanego obiektu.|
|`size`|Int32|Liczba bajtów treści.|
|`digest`|String|Skrót zawartości, zgodnie z definicją w specyfikacji interfejsu API HTTP V2 rejestru.|
|`repository`|String|Nazwa repozytorium.|
|`tag`|String|Nazwa tagu wykresu.|
|`name`|String|Nazwa wykresu.|
|`version`|String|Wersja wykresu.|

### <a name="payload-example-chart-delete-event"></a>Przykładowy ładunek: zdarzenie usuwania wykresu

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Przykład [wiersza polecenia platformy Azure](/cli/azure/acr) polecenia, które wyzwala **chart_delete** zdarzeń elementu webhook:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Kolejne kroki

[Przy użyciu elementów webhook usługi Azure Container Registry](container-registry-webhook.md)