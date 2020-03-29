---
title: Tworzenie danych wejściowych usługi Azure Media Services z adresu URL HTTPS | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak utworzyć dane wejściowe usługi Azure Media Services z adresu URL HTTPS.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: a3d4568dd237491f28ae2880bdd78dd236870c3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899827"
---
# <a name="create-a-job-input-from-an-https-url"></a>Tworzenie danych wejściowych zadania z adresu URL HTTPS

W przypadku usługi Media Services 3 po przesłaniu zadań w celu przetworzenia wideo należy poinformować usługę Media Services o tym, gdzie można znaleźć wejściowe wideo. Jedną z możliwości jest określenie adresu URL protokołu HTTPS jako danych wejściowych zadania (jak pokazano w tym przykładzie). Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS. Pełny przykład można znaleźć w tym [przykładzie github](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Przed rozpoczęciem tworzenia, przegląd [tworzenie za pomocą interfejsów API usługi Media Services w wersji 3](media-services-apis-overview.md) (zawiera informacje na temat uzyskiwania dostępu do interfejsów API, konwencje nazewnictwa, itp.)

## <a name="net-sample"></a>Próbka .NET

Poniższy kod pokazuje, jak utworzyć zadanie z wejściem adresu URL HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Następne kroki

[Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md).
