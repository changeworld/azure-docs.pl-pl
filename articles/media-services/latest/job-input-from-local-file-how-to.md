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
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: 3eb16034cc6507944ca7bebb59893e0d72a6f4c9
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415463"
---
# <a name="create-a-job-input-from-a-local-file"></a>Tworzenie wejściowych zadania z pliku lokalnego

W Media Services v3 po przesłaniu zadania, aby przetwarzać pliki wideo, należy stwierdzić, gdzie można znaleźć wejściowego filmu wideo usługi Media Services. Wejściowy plik wideo mogą być przechowywane jako zasób usługi multimediów, w którym to przypadku tworzenie wejściowego elementu, na podstawie pliku (przechowywane lokalnie lub w usłudze Azure Blob storage). W tym temacie przedstawiono sposób tworzenia danych wejściowych zadania z pliku lokalnego. Aby uzyskać pełny przykład, zobacz ten [przykładowe GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Przykład programu .NET

Poniższy kod pokazuje, jak utworzyć element zawartości danych wejściowych i korzystać z niego jako dane wejściowe dla zadania. Funkcja CreateInputAsset wykonuje następujące czynności:

* Tworzy element zawartości
* Pobranie zapisywalnego [adresu URL sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do [kontenera zasobu w magazynie](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Przekazanie pliku do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kody błędów zadania

Zobacz [kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie danych wejściowych zadania na podstawie adresu URL HTTPS](job-input-from-http-how-to.md).
