---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie i przesyłanie zadania | Microsoft Docs
description: Skrypt interfejsu wiersza polecenia platformy Azure w tym temacie pokazuje sposób przesyłania zadania do prostego przekształcenia kodowania przy użyciu adresu URL protokołu HTTPS.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 63d036ea4faaf7e24f337fa3956986d165c84854
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466973"
---
# <a name="cli-example-create-and-submit-a-job"></a>Przykład użycia interfejsu wiersza polecenia: Tworzenie i przesyłanie zadania

W przypadku usługi Media Services 3 po przesłaniu zadań w celu przetworzenia wideo należy poinformować usługę Media Services o tym, gdzie można znaleźć wejściowe wideo. Jedną z opcji jest określenie adresu URL HTTPS jako zadanie, dane wejściowe (jak pokazano w tym artykule). 

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto usługi Media Services](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Przykładowy skrypt

Po uruchomieniu polecenia `az ams job start` można ustawić etykietę dla danych wyjściowych zadania. Etykieta może później służyć do identyfikowania przeznaczenia wyjściowego elementu zawartości. 

- W przypadku przypisywania wartości do etykiety należy dla parametru „--output-assets” ustawić wartość „assetname=etykieta”.
- W przeciwnym przypadku należy dla parametru „--output-assets” ustawić wartość „assetname=”.
  Zwróć uwagę na dodanie znaku „=” do parametru `output-assets`. 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
```

Uzyskasz odpowiedź podobną do następującej:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej przykładów, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](../cli-samples.md).
