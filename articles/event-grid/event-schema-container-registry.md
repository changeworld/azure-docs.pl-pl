---
title: Schemat zdarzeń w usłudze Azure Event Grid Container Registry
description: Opisuje właściwości, które są dostarczane dla zdarzeń rejestru kontenerów za pomocą usługi Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 03/12/2019
ms.author: spelluru
ms.openlocfilehash: c5998ff428c4b6f4c1f7a4087c6ccb27d93773eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345468"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Schemat zdarzeń Azure Event Grid dla rejestru kontenerów

Ten artykuł zawiera właściwości i schematu zdarzeń rejestru kontenerów. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Usługa Azure Container Registry emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Wywoływane, gdy obraz jest przekazywane. |
| Microsoft.ContainerRegistry.ImageDeleted | Wywoływane, gdy obraz zostanie usunięty. |
| Microsoft.ContainerRegistry.ChartPushed | Wywoływane, gdy są wypychane wykresu Helm. |
| Microsoft.ContainerRegistry.ChartDeleted | Wywoływane, gdy wykresu Helm zostanie usunięty. |

## <a name="example-event"></a>Przykład zdarzenia

Poniższy przykład przedstawia schematu obraz wypchnięty zdarzeń: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat zdarzenia usunięto obraz jest podobne:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat wykres wypychania zdarzeń jest podobne do schematu utworzone z obrazów wypychanie zdarzeń, ale nie zawiera obiektu żądania:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat zdarzenia usunięto wykresu jest podobne do schematu na zdarzenie usunięte utworzone z obrazów, ale nie zawiera obiektu żądania:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| topic | string | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| subject | string | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| eventType | string | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | string | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | string | Unikatowy identyfikator zdarzenia. |
| data | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | string | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | string | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| id | string | Identyfikator zdarzenia. |
| timestamp | string | Czas, w którym wystąpiło zdarzenie. |
| action | string | Akcja, która obejmuje podanego zdarzenia. |
| target | obiekt | Obiekt docelowy zdarzenia. |
| request | obiekt | Żądanie, który wygenerował zdarzenie. |

Obiekt docelowy ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| mediaType | string | Typ MIME przywoływanego obiektu. |
| size | liczba całkowita | Liczba bajtów treści. Taka sama jak długość pola. |
| digest | string | Skrót zawartości, zgodnie z definicją w specyfikacji interfejsu API HTTP V2 rejestru. |
| length | liczba całkowita | Liczba bajtów treści. Taka sama jak rozmiar pola. |
| repository | string | Nazwa repozytorium. |
| tag | string | Nazwa tagu. |
| name | string | Nazwa wykresu. |
| version | string | Wersja wykresu. |

Obiekt żądania ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| id | string | Identyfikator żądania, który zainicjował zdarzenie. |
| addr | string | Adresu IP lub nazwę hosta i prawdopodobnie portu połączenia klienta, który zainicjował zdarzenie. Ta wartość jest RemoteAddr z żądania standardowego protokołu http. |
| host | string | Dostępne z zewnątrz nazwę hosta wystąpienia rejestru, określony przez nagłówka hosta http na żądań przychodzących. |
| method | string | Metoda żądania, który wygenerował zdarzenie. |
| useragent | string | Nagłówek agenta użytkownika żądania. |

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
