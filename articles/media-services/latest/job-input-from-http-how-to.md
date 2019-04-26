---
title: Tworzenie danych wejściowych usługi Azure Media Services zadania na podstawie adresu URL HTTPS | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak utworzyć dane wejściowe zadania na podstawie adresu URL HTTP (s).
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
ms.openlocfilehash: 5e301a671551ee65e8dc56ca6f86e273fe2f6241
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322604"
---
# <a name="create-a-job-input-from-an-https-url"></a>Tworzenie danych wejściowych zadania na podstawie adresu URL HTTPS

W przypadku usługi Media Services 3 po przesłaniu zadań w celu przetworzenia wideo należy poinformować usługę Media Services o tym, gdzie można znaleźć wejściowe wideo. Jedną z możliwości jest określenie adresu URL protokołu HTTPS jako danych wejściowych zadania (jak pokazano w tym przykładzie). Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS. Aby uzyskać pełny przykład, zobacz ten [przykładowe GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Przykład programu .NET

Poniższy kod przedstawia sposób tworzenia zadania za pomocą adresu URL HTTPS, dane wejściowe.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md).
