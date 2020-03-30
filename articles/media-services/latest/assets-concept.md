---
title: Elementy zawartości
titleSuffix: Azure Media Services
description: Dowiedz się, jakie są zasoby i jak są one używane przez usługę Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087902"
---
# <a name="assets-in-azure-media-services-v3"></a>Zasoby w usłudze Azure Media Services w wersji 3

W usłudze Azure Media Services [zasób](https://docs.microsoft.com/rest/api/media/assets) jest podstawową koncepcją. Jest to miejsce, w którym są wprowadzane media (na przykład za pośrednictwem przekazywania lub pozyskiwania na żywo), media wyjściowe (z danych wyjściowych zadania) i publikowanie multimediów z (do przesyłania strumieniowego). 

Zasób jest mapowany do kontenera obiektów blob na [koncie usługi Azure Storage,](storage-account-concept.md) a pliki w zasobie są przechowywane jako blokowe obiekty blob w tym kontenerze. Zasoby zawierają informacje o plikach cyfrowych przechowywanych w usłudze Azure Storage (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki podpisów kodowanych).

Usługi Media Services obsługuje warstwy obiektów Blob, gdy konto używa magazynu ogólnego przeznaczenia w wersji 2 (GPv2). Za pomocą GPv2 można przenosić pliki do [magazynu Cool lub Archive.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) **Magazyn archiwum** nadaje się do archiwizacji plików źródłowych, gdy nie są już potrzebne (na przykład po ich zakodowaniu).

**Warstwa magazynu archiwum** jest zalecane tylko dla bardzo dużych plików źródłowych, które zostały już zakodowane i kodowania dane wyjściowe zadania został umieszczony w kontenerze obiektu blob wyjściowe. Obiekty BLOB w kontenerze danych wyjściowych, które chcesz skojarzyć z zasobem i używać do przesyłania strumieniowego lub analizowania zawartości musi istnieć w warstwie magazynu **gorąca** lub **chłodna.**

## <a name="naming"></a>Nazewnictwo 

### <a name="assets"></a>Elementy zawartości

Nazwy zasobu muszą być unikatowe. Nazwy zasobów usługi Media Services w wersji 3 (na przykład zasoby, zadania, przekształcenia) podlegają ograniczeniom nazewnictwa usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Obiekty blob

Nazwy plików/obiektów blob w zasobie muszą być zgodne zarówno [z wymaganiami dotyczącymi nazwy obiektu blob,](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) jak i [wymaganiami dotyczącymi nazw ntfs](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Powodem tych wymagań jest pliki można uzyskać skopiowane z magazynu obiektów blob do lokalnego dysku NTFS do przetwarzania.

## <a name="next-steps"></a>Następne kroki

[Zarządzanie zasobami w umiejce w umiań](manage-asset-concept.md)

## <a name="see-also"></a>Zobacz też

[Różnice między usługami Media Services w wersji 2 i v3](migrate-from-v2-to-v3.md)
