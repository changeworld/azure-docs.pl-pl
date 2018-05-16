---
title: Utwórz dane wejściowe Azure Media Services zadania z pliku lokalnego | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób tworzenia wprowadzania zadania z pliku lokalnego.
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
ms.openlocfilehash: 94e7192e13397ad8ec973d92f4c538f430c9cd60
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="create-a-job-input-from-a-local-file"></a>Utwórz wprowadzania zadania z pliku lokalnego

W Media Services w wersji 3 po przesłaniu zadania, aby przetwarzać pliki wideo, należy sprawdzić, gdzie można znaleźć wejściowy plik wideo usługi Media Services. Wejściowy plik wideo mogą być przechowywane jako zasób usługi multimediów w takim przypadku utworzysz wejściowych zasobu na podstawie pliku (przechowywane lokalnie lub w magazynie obiektów Blob platformy Azure). W tym temacie przedstawiono sposób tworzenia wprowadzania zadania z pliku lokalnego. Na przykład pełna, zobacz [próbki github](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Przykładowe .NET

Poniższy kod przedstawia sposób tworzenia zasobu wejściowego i używać go jako dane wejściowe zadania. Funkcja CreateInputAsset wykonuje następujące czynności:

* Tworzy element zawartości 
* Pobranie zapisywalnego [adresu URL sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do [kontenera zasobu w magazynie](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Przekazanie pliku do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Kolejne kroki

[Utwórz wprowadzania zadania na podstawie adresu URL HTTP (s)](job-input-from-http-how-to.md).
