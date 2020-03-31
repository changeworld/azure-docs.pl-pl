---
title: Odwołanie do schematu elementu webhook rejestru
description: Odwołanie do ładunku JSON dla żądań elementu webhook w rejestrze kontenerów platformy Azure, które są generowane, gdy elementy webhook są włączone dla zdarzeń wypychania lub usuwania artefaktów
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455962"
---
# <a name="azure-container-registry-webhook-reference"></a>Odwołanie do elementu webhook rejestru kontenerów platformy Azure

Można [skonfigurować elementy webhook](container-registry-webhook.md) dla rejestru kontenerów, które generują zdarzenia, gdy niektóre akcje są wykonywane przeciwko niemu. Na przykład włącz elementy webhook, które są wyzwalane, gdy obraz kontenera lub wykres helm jest wypychany do rejestru lub usunięte. Po wyzwoleniu elementu webhook, usługa Azure Container Registry wystawia żądanie HTTP lub HTTPS zawierające informacje o zdarzeniu do określonego punktu końcowego. Punkt końcowy można następnie przetworzyć element webhook i działać odpowiednio.

W poniższych sekcjach szczegółowo schemat żądań elementu webhook generowanych przez obsługiwane zdarzenia. Sekcje zdarzeń zawierają schemat ładunku dla typu zdarzenia, ładunek żądania przykład i co najmniej jedno polecenie przykładowe, które wyzwoliłoby element webhook.

Aby uzyskać informacje dotyczące konfigurowania zestawów webhook dla rejestru kontenerów platformy Azure, zobacz [Korzystanie z elementów webhook rejestru kontenerów platformy Azure.](container-registry-webhook.md)

## <a name="webhook-requests"></a>Żądania elementu Webhook

### <a name="http-request"></a>Żądanie HTTP

Wyzwalany element webhook `POST` sprawia, że żądanie HTTP do punktu końcowego adresu URL określonego podczas konfigurowania elementu webhook.

### <a name="http-headers"></a>Nagłówki HTTP

Żądania elementu `Content-Type` webhook `application/json` obejmują, jeśli `Content-Type` nie określono niestandardowego nagłówka dla elementu webhook.

Żadne inne nagłówki nie są dodawane do żądania poza te nagłówki niestandardowe, które mogły być określone dla elementu webhook.

## <a name="push-event"></a>Zdarzenie wypychania

Element Webhook wyzwalany, gdy obraz kontenera jest wypychany do repozytorium.

### <a name="push-event-payload"></a>Ładunek zdarzenia wypychania

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Czas, w którym zdarzenie webhook zostało wyzwolone.|
|`action`|Ciąg|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[Docelowego](#target)|Typ złożony|Miejsce docelowe zdarzenia, które wyzwoliło zdarzenie elementu webhook.|
|[Żądanie](#request)|Typ złożony|Żądanie, które wygenerowało zdarzenie elementu webhook.|

### <a name="target"></a><a name="target"></a>Docelowego

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME obiektu, do którego istnieje odwołanie.|
|`size`|Int32|Liczba bajtów zawartości. Tak samo jak pole Długość.|
|`digest`|Ciąg|Podsumowanie zawartości, zgodnie z definicją specyfikacji interfejsu API HTTP rejestru V2.|
|`length`|Int32|Liczba bajtów zawartości. Tak samo jak pole Rozmiar.|
|`repository`|Ciąg|Nazwa repozytorium.|
|`tag`|Ciąg|Nazwa znacznika obrazu.|

### <a name="request"></a><a name="request"></a>Żądanie

|Element|Typ|Opis|
|------------------|----------|-----------|
|`id`|Ciąg|Identyfikator żądania, które zainicjowało zdarzenie.|
|`host`|Ciąg|Zewnętrznie dostępna nazwa hosta wystąpienia rejestru, określona przez nagłówek hosta HTTP dla żądań przychodzących.|
|`method`|Ciąg|Metoda żądania, która wygenerowała zdarzenie.|
|`useragent`|Ciąg|Nagłówek agenta użytkownika żądania.|

### <a name="payload-example-image-push-event"></a>Przykład ładunku: zdarzenie wypychania obrazu

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

Przykładowe polecenie [interfejsu wiersza polecenia platformy Docker,](https://docs.docker.com/engine/reference/commandline/cli/) które wyzwala element webhook zdarzenia **wypychania** obrazu:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Zdarzenie wypychania wykresu

Element Webhook wyzwalany, gdy wykres Helm jest wypychany do repozytorium.

### <a name="chart-push-event-payload"></a>Ładunek zdarzenia wypychania wykresu

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Czas, w którym zdarzenie webhook zostało wyzwolone.|
|`action`|Ciąg|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[Docelowego](#helm_target)|Typ złożony|Miejsce docelowe zdarzenia, które wyzwoliło zdarzenie elementu webhook.|

### <a name="target"></a><a name="helm_target"></a>Docelowego

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME obiektu, do którego istnieje odwołanie.|
|`size`|Int32|Liczba bajtów zawartości.|
|`digest`|Ciąg|Podsumowanie zawartości, zgodnie z definicją specyfikacji interfejsu API HTTP rejestru V2.|
|`repository`|Ciąg|Nazwa repozytorium.|
|`tag`|Ciąg|Nazwa znacznika wykresu.|
|`name`|Ciąg|Nazwa wykresu.|
|`version`|Ciąg|Wersja wykresu.|

### <a name="payload-example-chart-push-event"></a>Przykład ładunku: zdarzenie wypychania wykresu

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

Przykładowe polecenie [interfejsu wiersza polecenia platformy Azure,](/cli/azure/acr) które wyzwala element webhook **zdarzenia chart_push:**

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Usuń zdarzenie

Element Webhook wyzwalany po usunięciu repozytorium lub manifestu obrazu. Nie jest wyzwalany po usunięciu znacznika.

### <a name="delete-event-payload"></a>Usuwanie ładunku zdarzeń

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Czas, w którym zdarzenie webhook zostało wyzwolone.|
|`action`|Ciąg|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[Docelowego](#delete_target)|Typ złożony|Miejsce docelowe zdarzenia, które wyzwoliło zdarzenie elementu webhook.|
|[Żądanie](#delete_request)|Typ złożony|Żądanie, które wygenerowało zdarzenie elementu webhook.|

### <a name="target"></a><a name="delete_target"></a>Docelowego

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME obiektu, do którego istnieje odwołanie.|
|`digest`|Ciąg|Podsumowanie zawartości, zgodnie z definicją specyfikacji interfejsu API HTTP rejestru V2.|
|`repository`|Ciąg|Nazwa repozytorium.|

### <a name="request"></a><a name="delete_request"></a>Żądanie

|Element|Typ|Opis|
|------------------|----------|-----------|
|`id`|Ciąg|Identyfikator żądania, które zainicjowało zdarzenie.|
|`host`|Ciąg|Zewnętrznie dostępna nazwa hosta wystąpienia rejestru, określona przez nagłówek hosta HTTP dla żądań przychodzących.|
|`method`|Ciąg|Metoda żądania, która wygenerowała zdarzenie.|
|`useragent`|Ciąg|Nagłówek agenta użytkownika żądania.|

### <a name="payload-example-image-delete-event"></a>Przykład ładunku: zdarzenie usuwania obrazu

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

Przykładowe polecenia [interfejsu wiersza polecenia platformy Azure,](/cli/azure/acr) które wyzwalają element webhook zdarzenia **usuwania:**

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Zdarzenie usuwania wykresu

Element Webhook wyzwalany po usunięciu wykresu helm lub repozytorium. 

### <a name="chart-delete-event-payload"></a>Ładunek zdarzenia usuwania wykresu

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Czas, w którym zdarzenie webhook zostało wyzwolone.|
|`action`|Ciąg|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[Docelowego](#chart_delete_target)|Typ złożony|Miejsce docelowe zdarzenia, które wyzwoliło zdarzenie elementu webhook.|

### <a name="target"></a><a name="chart_delete_target"></a>Docelowego

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME obiektu, do którego istnieje odwołanie.|
|`size`|Int32|Liczba bajtów zawartości.|
|`digest`|Ciąg|Podsumowanie zawartości, zgodnie z definicją specyfikacji interfejsu API HTTP rejestru V2.|
|`repository`|Ciąg|Nazwa repozytorium.|
|`tag`|Ciąg|Nazwa znacznika wykresu.|
|`name`|Ciąg|Nazwa wykresu.|
|`version`|Ciąg|Wersja wykresu.|

### <a name="payload-example-chart-delete-event"></a>Przykład ładunku: zdarzenie usuwania wykresu

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

Przykładowe polecenie [interfejsu wiersza polecenia platformy Azure,](/cli/azure/acr) które wyzwala element webhook **zdarzenia chart_delete:**

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Następne kroki

[Korzystanie z elementów webhook rejestru kontenerów platformy Azure](container-registry-webhook.md)