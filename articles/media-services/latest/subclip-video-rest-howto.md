---
title: Klipu podrzędnego wideo podczas kodowania za pomocą interfejsu API REST usługi Azure Media Services
description: W tym temacie opisano sposób klipu podrzędnego wideo podczas kodowania za pomocą usługi Azure Media Services przy użyciu usługi REST
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305181"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Klipu podrzędnego wideo podczas kodowania za pomocą usługi Media Services — REST

Można przycięcia lub klipu podrzędnego wideo podczas kodowania za pomocą [zadania](https://docs.microsoft.com/rest/api/media/jobs). Ta funkcja działa ze wszystkimi [Przekształcanie](https://docs.microsoft.com/rest/api/media/transforms) utworzonego za pomocą [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ustawienia wstępne, lub [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) ustawienia wstępne. 

Przykład REST, w tym temacie tworzy zadanie, które przycina film wideo, ponieważ przesyła zadania kodowania. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym temacie, musisz:

- [Tworzenie konta usługi Azure Media Services](create-account-cli-how-to.md).
- [Konfigurowanie narzędzia Postman dla wywołania interfejsu API REST usługi Azure Media Services](media-rest-apis-with-postman.md).
    
    Upewnij się, że należy wykonać ostatni krok w temacie [pobrać tokenu usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Tworzenie transformacji i dane wyjściowe elementy zawartości. Możesz dowiedzieć się, jak utworzyć transformację i dane wyjściowe zasobów w [kodowanie pliku zdalnego na podstawie adresu URL i przesyłanie strumieniowe wideo — REST](stream-files-tutorial-with-rest.md) samouczka.
- Przegląd [kodowaniem pojęciem](encoding-concept.md) tematu.

## <a name="create-a-subclipping-job"></a>Utwórz zadanie używać

1. W kolekcji Postman, który został pobrany, wybierz **transformacje i zadania** -> **Utwórz zadanie z przycinania Sub**.
    
    **Umieścić** żądania wygląda następująco:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Zaktualizuj wartość zmiennej środowiskowej "transformName" z Twoją nazwą transformacji. 
1. Wybierz **treści** kartę i zaktualizuj "myOutputAsset" przy użyciu danych wyjściowych nazwę zasobu.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Kliknij pozycję **Wyślij**.

    Zostanie wyświetlony **odpowiedzi** przy użyciu informacji o zadaniu, które zostały utworzone, a następnie przesyłane i stan zadania. 

## <a name="next-steps"></a>Kolejne kroki

[Jak kodowanie za pomocą niestandardowej transformacji](custom-preset-rest-howto.md) 
