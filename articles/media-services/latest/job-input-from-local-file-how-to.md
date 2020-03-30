---
title: Tworzenie danych wejściowych zadania usługi Azure Media Services z pliku lokalnego | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak utworzyć dane wejściowe zadania usługi Azure Media Services z pliku lokalnego.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: aba987ba232a29ffc240f72039b1e24bb87a2ed4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345906"
---
# <a name="create-a-job-input-from-a-local-file"></a>Tworzenie danych wejściowych zadania z pliku lokalnego

W przypadku usługi Media Services 3 po przesłaniu zadań w celu przetworzenia wideo należy poinformować usługę Media Services o tym, gdzie można znaleźć wejściowe wideo. Wejściowy klip wideo może być przechowywany jako zasób usługi Media Service, w którym to przypadku utworzysz zasób wejściowy na podstawie pliku (przechowywane lokalnie lub w magazynie obiektów Blob platformy Azure). W tym temacie pokazano, jak utworzyć dane wejściowe zadania z pliku lokalnego. Pełny przykład można znaleźć w tym [przykładzie github](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Wymagania wstępne 

* [Utwórz konto usługi Media Services](create-account-cli-how-to.md).
* Przegląd [zarządzania zasobami](manage-asset-concept.md).

## <a name="net-sample"></a>Próbka .NET

Poniższy kod pokazuje, jak utworzyć zasób wejściowy i użyć go jako dane wejściowe dla zadania. Funkcja CreateInputAsset wykonuje następujące akcje:

* Tworzy środek trwały
* Pobiera zapisywalny [adres URL sygnatury dostępu Współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do [kontenera zasobu w magazynie](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Przekazanie pliku do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Poniższy fragment kodu tworzy zasób wyjściowy, jeśli jeszcze nie istnieje:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Poniższy fragment kodu przesyła zadanie kodowania:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Następne kroki

[Tworzenie danych wejściowych zadania z adresu URL HTTPS](job-input-from-http-how-to.md).
