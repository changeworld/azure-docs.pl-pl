---
title: Tworzenie wejściowych usługi Azure Media Services zadania z pliku lokalnego | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób tworzenia danych wejściowych zadania z pliku lokalnego.
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
ms.openlocfilehash: 3b4c11c359c15f1275a16774b490c08b543572c3
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378716"
---
# <a name="create-a-job-input-from-a-local-file"></a>Tworzenie wejściowych zadania z pliku lokalnego

W Media Services v3 po przesłaniu zadania, aby przetwarzać pliki wideo, należy stwierdzić, gdzie można znaleźć wejściowego filmu wideo usługi Media Services. Wejściowy plik wideo mogą być przechowywane jako zasób usługi multimediów, w którym to przypadku tworzenie wejściowego elementu, na podstawie pliku (przechowywane lokalnie lub w usłudze Azure Blob storage). W tym temacie przedstawiono sposób tworzenia danych wejściowych zadania z pliku lokalnego. Aby uzyskać pełny przykład, zobacz ten [przykładowe github](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Przykład programu .NET

Poniższy kod pokazuje, jak utworzyć element zawartości danych wejściowych i korzystać z niego jako dane wejściowe dla zadania. Funkcja CreateInputAsset wykonuje następujące czynności:

* Tworzy element zawartości 
* Pobranie zapisywalnego [adresu URL sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do [kontenera zasobu w magazynie](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Przekazanie pliku do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie danych wejściowych zadania na podstawie adresu URL HTTPS](job-input-from-http-how-to.md).
