---
title: Podkompaniuj klipsa wideo podczas kodowania za pomocą usługi Azure Media Services REST
description: W tym temacie opisano sposób podpopinać klip wideo podczas kodowania za pomocą usługi Azure Media Services przy użyciu funkcji REST
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
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514327"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Subclip wideo podczas kodowania z media services - REST

Wideo można przycinać lub podklipować podczas kodowania go za pomocą [zadania](https://docs.microsoft.com/rest/api/media/jobs). Ta funkcja działa z dowolnego [przekształcenia,](https://docs.microsoft.com/rest/api/media/transforms) który jest zbudowany przy użyciu [wbudowanych wbudowanychstandardencoderpreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) presetów lub [standardencoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) presetów. 

Przykład REST w tym temacie tworzy zadanie, które przycina wideo podczas przesyłania zadania kodowania. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym temacie, należy:

- [Utwórz konto usługi Azure Media Services](create-account-cli-how-to.md).
- [Skonfiguruj listonosz dla wywołań interfejsu API REST usługi Azure Media Services](media-rest-apis-with-postman.md).
    
    Upewnij się, że postępuj zgodnie z ostatnim krokiem w temacie [Pobierz token usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Tworzenie przekształcenia i zasobów wyjściowych. Możesz zobaczyć, jak utworzyć transformowanie i zasoby wyjściowe w [pliku Encode na podstawie adresu URL i przesyłać strumieniowo film — samouczek REST.](stream-files-tutorial-with-rest.md)
- Przejrzyj temat [koncepcji kodowania.](encoding-concept.md)

## <a name="create-a-subclipping-job"></a>Tworzenie zadania podkluniania

1. W pobranej kolekcji Postman wybierz pozycję **Przekształcenia i zadania tworzenie** -> **zadań za pomocą przycinania podrzędnego**.
    
    Żądanie **PUT** wygląda następująco:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Zaktualizuj wartość zmiennej środowiskowej "transformName" o nazwę transformacji. 
1. Wybierz kartę **Treść** i zaktualizuj "myOutputAsset" o nazwie zasobu wyjściowego.

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

    Zostanie wyświetlona **odpowiedź** z informacjami o utworzonym i przesłanym zadaniu oraz o stanie zadania. 

## <a name="next-steps"></a>Następne kroki

[Jak zakodować za pomocą transformacji niestandardowej](custom-preset-rest-howto.md) 
