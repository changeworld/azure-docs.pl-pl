---
title: Regiony, w których Video Indexer jest dostępna — Azure
titleSuffix: Azure Media Services
description: Ten artykuł zawiera informacje na temat regionów świadczenia usługi Azure, w których Video Indexer jest dostępna.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b67c89e02091d57b4f2dc901005c2dae36deafc2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838198"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Regiony platformy Azure, w których istnieje Video Indexer

Interfejsy API Video Indexer zawierają parametr **lokalizacji** , który należy ustawić dla regionu platformy Azure, do którego ma zostać przekierowane wywołanie. Musi to być [region platformy Azure, w którym Video Indexer jest dostępna](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Lokalizacje

Jako wartość parametru **Location** należy nadać nazwę kodową regionu platformy Azure. Jeśli używasz Video Indexer w trybie wersji zapoznawczej, należy umieścić jako wartość *"wersję próbną"* . W przeciwnym razie, aby uzyskać nazwę kodową regionu platformy Azure, w którym znajduje się konto, a połączenie powinno być kierowane, można uruchomić następujący wiersz w [interfejsie wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```bash
az account list-locations
```

Po uruchomieniu pokazanego powyżej wiersza otrzymujesz listę wszystkich regionów świadczenia usługi Azure. Przejdź do regionu platformy Azure, dla którego szukasz *DisplayName* , i użyj jego wartości *nazwy* dla parametru **Location** .

Na przykład dla regionu Azure zachodnie stany USA 2 (wyświetlone poniżej) dla parametru **Location** zostanie użyta wartość "westus2".

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
- [Dostosowywanie modelu marek przy użyciu interfejsów API](customize-brands-model-with-api.md)
- [Dostosuj model osoby przy użyciu interfejsów API](customize-person-model-with-api.md)
