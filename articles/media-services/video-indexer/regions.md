---
title: Regiony, w których dostępny jest indeksator wideo — Azure
titleSuffix: Azure Media Services
description: W tym artykule o tym o regionach platformy Azure, w których jest dostępny indeksator wideo usługi Azure Media Services.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c91b38fcbfb9b517651adead010408425e519a82
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382753"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Regiony platformy Azure, w których istnieje indeksator wideo

Interfejsy API indeksatora wideo zawierają parametr **lokalizacji,** który należy ustawić na regionie platformy Azure, do którego należy rozesłać wywołanie. Musi to być [region platformy Azure, w którym dostępny jest indeksator wideo.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

## <a name="locations"></a>Lokalizacje

Parametr **lokalizacji** musi mieć podana nazwę kodu regionu platformy Azure jako jego wartość. Jeśli używasz video indexer w trybie podglądu, należy umieścić *"trial"* jako wartość. W przeciwnym razie, aby uzyskać nazwę kodową regionu platformy Azure, w której znajduje się twoje konto i do którego powinno zostać skierowane wywołanie, można uruchomić następujący wiersz w [usłudze Azure CLI:](/cli/azure)

```azurecli-interactive
az account list-locations
```

Po uruchomieniu wiersza pokazano powyżej, otrzymasz listę wszystkich regionów platformy Azure. Przejdź do regionu platformy Azure, który ma *nazwę wyświetlania,* której szukasz, i użyj jego wartości *nazwy* dla parametru **lokalizacji.**

Na przykład dla regionu platformy Azure West US 2 (wyświetlane poniżej), użyjesz "westus2" dla **parametru lokalizacji.**

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Następne kroki

- [Dostosowywanie modelu języka przy użyciu interfejsów API](customize-language-model-with-api.md)
- [Dostosowywanie modelu marki przy użyciu interfejsów API](customize-brands-model-with-api.md)
- [Dostosowywanie modelu osoby przy użyciu interfejsów API](customize-person-model-with-api.md)
