---
title: Utwórz dane wejściowe Azure Media Services zadania na podstawie adresu URL HTTP (s) | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób tworzenia wprowadzania zadania z adresu URL HTTP (s).
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d429665de64dacc5818d1d26c2a9029531cd39b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159931"
---
# <a name="create-a-job-input-from-an-https-url"></a>Utwórz wprowadzania zadania na podstawie adresu URL HTTP (s)

W Media Services w wersji 3 po przesłaniu zadania, aby przetwarzać pliki wideo, należy sprawdzić, gdzie można znaleźć wejściowy plik wideo usługi Media Services. Jest jedną z opcji, aby określić adres URL HTTP (s) jako danych wejściowych (jak pokazano w tym przykładzie) zadanie. Na przykład pełna, zobacz [próbki github](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Przykładowe .net

Poniższy kod przedstawia sposób tworzenia zadania z adresu URL HTTPS danych wejściowych.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Kolejne kroki

[Utwórz wprowadzania zadania z pliku lokalnego](job-input-from-local-file-how-to.md).
