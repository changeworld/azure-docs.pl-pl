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
ms.openlocfilehash: 1760c63bee6bc1b0a631499a8875bedb34e71afd
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308846"
---
# <a name="create-a-job-input-from-an-https-url"></a>Tworzenie danych wejściowych zadania na podstawie adresu URL HTTPS

W Media Services v3 po przesłaniu zadania, aby przetwarzać pliki wideo, należy stwierdzić, gdzie można znaleźć wejściowego filmu wideo usługi Media Services. Jedną z opcji jest określenie adresu URL HTTPS jako zadanie, dane wejściowe (jak pokazano w tym przykładzie). Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS. Aby uzyskać pełny przykład, zobacz ten [przykładowe GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Przykład programu .NET

Poniższy kod przedstawia sposób tworzenia zadania za pomocą adresu URL HTTPS, dane wejściowe.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md).
