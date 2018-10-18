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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: bac11640f5256223c1053f03da42c763508af98f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377382"
---
# <a name="create-a-job-input-from-an-https-url"></a>Tworzenie danych wejściowych zadania na podstawie adresu URL HTTPS

W Media Services v3 po przesłaniu zadania, aby przetwarzać pliki wideo, należy stwierdzić, gdzie można znaleźć wejściowego filmu wideo usługi Media Services. Jest jedną z opcji, aby określić adres URL HTTP (s) jako dane wejściowe (jak pokazano w tym przykładzie) zadania. Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS. Aby uzyskać pełny przykład, zobacz ten [przykładowe github](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Przykład programu .NET

Poniższy kod przedstawia sposób tworzenia zadania za pomocą adresu URL HTTPS, dane wejściowe.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md).
