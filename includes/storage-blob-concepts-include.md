---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 203f5a766c4c8a8f1e577f6be1e18d0f9ac95403
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
Magazyn obiektów Blob Azure to rozwiązanie magazynu obiektu firmy Microsoft w chmurze. Magazyn obiektów blob jest zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury, takich jak tekst lub dane binarne.

Magazyn obiektów BLOB to idealne rozwiązanie w przypadku:

* Obsługiwanie obrazów i dokumentów bezpośrednio do przeglądarki.
* Przechowywanie plików do dostępu rozproszonego.
* Przesyłanie strumieniowe audio i wideo.
* Zapisywanie w plikach dziennika.
* Zapisywanie danych dla kopii zapasowej i przywracania, odzyskiwania po awarii i archiwizowania.
* Zapisywanie danych do analizy przez lokalną lub usługi hostowanej platformy Azure.

Obiekty w magazynie obiektów Blob można uzyskać z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Użytkownicy lub aplikacje klienckie mogą uzyskiwać dostęp do obiektów blob za pomocą adresów URL, [interfejsu API REST magazynu Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/storage), lub biblioteka klienta magazynu Azure. Biblioteki klienta magazynu są dostępne dla wielu języków, w tym [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/en/latest/index.html), [PHP](http://azure.github.io/azure-storage-php/), i [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="blob-service-concepts"></a>Pojęcia dotyczące usługi Blob

Magazyn obiektów blob udostępnia trzy zasoby: Twoje konto magazynu, kontenery w ramach konta, a obiekty BLOB w kontenerze. Na poniższym diagramie przedstawiono relacje między tych zasobów.

![Diagram architektury magazynu obiektów Blob (obiekt)](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Konto magazynu

Dostęp do danych obiektów w usłudze Azure Storage odbywa się za pomocą konta magazynu. Aby uzyskać więcej informacji, zobacz [kont magazynu Azure o](../articles/storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="container"></a>Kontener

Kontener organizuje zestawu obiektów blob, podobnie jak folder w systemie plików. Wszystkie obiekty BLOB znajdują się w kontenerze. Konto magazynu może zawierać nieograniczoną liczbę kontenerów, a kontener może przechowywać nieograniczoną liczbę obiektów blob. Pamiętaj, że wszystkie litery w nazwie kontenera muszą być małymi literami.

### <a name="blob"></a>Obiekt blob
 
Usługa Azure Storage udostępnia trzy typy obiektów blob — blokowych obiektów blob, uzupełnialne i [stronicowe](../articles/storage/blobs/storage-blob-pageblob-overview.md) (używanych do plików VHD).

* Blokowe obiekty BLOB przechowywania tekstu i dane binarne do około 4,7 TB. Blokowe obiekty BLOB składają się z bloków danych, który może być zarządzany indywidualnie.
* Dołącz obiekty BLOB składają się z bloków, takich jak blokowych obiektów blob, ale są zoptymalizowane pod kątem operacji dołączania. Dołącz obiekty BLOB są idealne rozwiązanie w przypadku scenariuszy, takich jak rejestrowanie danych z maszyn wirtualnych.
* Dostęp losowy magazynu obiektów blob strona pliki do 8 TB rozmiarze. Stronicowe obiekty BLOB przechowywane pliki VHD, które wykonują kopie maszyn wirtualnych.

Wszystkie obiekty BLOB znajdują się w kontenerze. Kontener jest podobna do folderu w systemie plików. Pozwala dodatkowo organizowania obiekty BLOB w katalogów wirtualnych i ich przenoszenie, jak w przypadku systemu plików. 

W przypadku bardzo dużych zestawów danych, gdy ograniczenia sieci mogą w praktyce uniemożliwić przekazanie lub pobranie danych do usługi Blob Storage, możesz przesłać zestaw dysków twardych do firmy Microsoft, aby zaimportować dane bezpośrednio do centrum danych lub wyeksportować je stamtąd. Aby uzyskać więcej informacji, zobacz [korzystanie z usługi Microsoft Azure importu/eksportu przesyłanie danych do magazynu obiektów Blob](../articles/storage/common/storage-import-export-service.md).
  
Aby uzyskać szczegółowe informacje o nazewnictwie kontenerów i obiektów blob, zobacz temat [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych).
