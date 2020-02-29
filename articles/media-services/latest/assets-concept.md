---
title: Elementy zawartości
titleSuffix: Azure Media Services
description: Dowiedz się więcej o zasobach i sposobach ich użycia przez Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68bb1fea88ab7ba30e0ba07839f2d962840b7818
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921185"
---
# <a name="assets-in-azure-media-services"></a>Zasoby w Azure Media Services

W Azure Media Services, element [zawartości](https://docs.microsoft.com/rest/api/media/assets) jest podstawową koncepcją. Jest to miejsce, w którym można wprowadzać multimedia (na przykład za pośrednictwem przekazywania lub pozyskiwania na żywo), nośniki wyjściowe (z danych wyjściowych zadania) i publikować multimedia z (na potrzeby przesyłania strumieniowego). 

Zasób jest mapowany do kontenera obiektów BLOB na [koncie usługi Azure Storage](storage-account-concept.md) , a pliki w elemencie zawartości są przechowywane jako blokowe obiekty blob w tym kontenerze. Elementy zawartości zawierają informacje o plikach cyfrowych przechowywanych w usłudze Azure Storage (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów).

Media Services obsługuje warstwy obiektów blob, gdy konto używa magazynu ogólnego przeznaczenia w wersji 2 (GPv2). Za pomocą GPv2 można przenosić pliki do [magazynu chłodnego lub archiwum](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Magazyn **archiwalny** jest odpowiedni do archiwizowania plików źródłowych, gdy nie są już potrzebne (na przykład po ich zakodowaniu).

Warstwa magazynu **archiwum** jest zalecana tylko dla bardzo dużych plików źródłowych, które zostały już zakodowane i dane wyjściowe zadania kodowania zostały umieszczone w wyjściowym kontenerze obiektów BLOB. Obiekty blob w kontenerze danych wyjściowych, które mają zostać skojarzone z elementem zawartości i używają do przesyłania strumieniowego lub analizowania zawartości, muszą znajdować się w warstwie magazynowania **gorąca** lub **chłodna** .

## <a name="naming"></a>Nazewnictwo 

### <a name="assets"></a>Elementy zawartości

Nazwy zasobów muszą być unikatowe. Nazwy zasobów Media Services v3 (na przykład zasoby, zadania, przekształcenia) podlegają ograniczeniom nazw Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Obiekty blob

Nazwy plików/obiektów BLOB w obrębie elementu zawartości muszą spełniać zarówno [wymagania dotyczące nazw obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) , jak i [wymagania dotyczące nazw NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Przyczyną tych wymagań jest możliwość skopiowania plików z magazynu obiektów BLOB do lokalnego dysku NTFS w celu przetworzenia.

## <a name="map-v3-asset-properties-to-v2"></a>Mapuj właściwości zasobu v3 do wersji 2

W poniższej tabeli przedstawiono sposób, w jaki właściwości [zasobu](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)w wersji v3 mapują się na właściwości zasobu w 2.

|Właściwości v3|Właściwości v2|
|---|---|
|`id` — (unikatowy) pełna ścieżka Azure Resource Manager, zobacz przykłady w elemencie [zawartości](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name` — (unikatowy) zobacz [konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|wartość `Id`-(Unique) rozpoczyna się od prefiksu `nb:cid:UUID:`.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opcje tworzenia)|
|`type`||

## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić Twoje zasoby w spoczynku, zasoby mają zostać zaszyfrowane za pomocą szyfrowania po stronie magazynu. W poniższej tabeli przedstawiono, jak działa szyfrowanie po stronie magazynu w usłudze Media Services:

|Opcja szyfrowania|Opis|Media Services v2|Media Services v3|
|---|---|---|---|
|Szyfrowanie magazynu usługi Media Services|Szyfrowanie AES-256, klucz zarządzany przez Media Services.|Obsługiwane<sup>(1)</sup>|Nieobsługiwane<sup>(2)</sup>|
|[szyfrowanie usługi Storage danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta.|Obsługiwane|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta oferowane przez usługę Azure Storage, klucz zarządzany przez klienta w Key Vault.|Nieobsługiwane|Nieobsługiwane|

<sup>1</sup> , gdy Media Services obsługuje obsługę zawartości w trybie Wyczyść/bez żadnej formy szyfrowania, nie jest to zalecane.

<sup>2</sup> w Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest obsługiwane tylko w przypadku zgodności z poprzednimi wersjami, gdy zasoby zostały utworzone przy użyciu Media Services V2. Oznacza to, że wersja v3 współpracuje z istniejącymi zasobami zaszyfrowanymi magazynu, ale nie umożliwia tworzenia nowych.

## <a name="next-steps"></a>Następne kroki

[Zarządzanie zasobami w Media Services](manage-asset-concept.md)

## <a name="also-see"></a>Zobacz też

[Różnice między Media Services V2 i V3](migrate-from-v2-to-v3.md)
