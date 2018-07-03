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
ms.openlocfilehash: 63593ff5f02f5e37fc25c988c4cef071a03a00b4
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37066037"
---
Azure Blob Storage to rozwiązanie do magazynowania obiektów w chmurze firmy Microsoft. Usługa Blob Storage jest zoptymalizowana pod kątem przechowywania olbrzymich ilości danych bez struktury, takich jak dane tekstowe lub binarne.

Usługa Blob Storage to idealne rozwiązanie w następujących przypadkach:

* Obsługiwanie obrazów i dokumentów bezpośrednio w przeglądarce.
* Przechowywanie plików do dostępu rozproszonego.
* Przesyłanie strumieniowe audio i wideo.
* Zapisywanie plików dziennika.
* Zapisywanie danych w celu tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania.
* Przechowywanie danych w celu analizy w usłudze lokalnej lub hostowanej na platformie Azure.

Dostęp do obiektów w usłudze Blob Storage można uzyskać z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Użytkownicy lub aplikacje klienckie mogą uzyskiwać dostęp do obiektów blob za pomocą adresów URL, [interfejsu API REST usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/storage) lub biblioteki klienta usługi Azure Storage. Biblioteki klienta magazynu są dostępne dla wielu języków, w tym [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [PHP](http://azure.github.io/azure-storage-php/) i [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="blob-service-concepts"></a>Pojęcia dotyczące usługi Blob

Usługa Blob Storage udostępnia trzy rodzaje zasobów: konto magazynu, kontenery na koncie i obiekty blob w kontenerach. Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury usługi Blob Storage (magazynu obiektów blob)](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Konto magazynu

Dostęp do obiektów danych w usłudze Azure Storage odbywa się za pośrednictwem konta magazynu. Aby uzyskać więcej informacji, zobacz [Informacje o kontach usługi Azure Storage](../articles/storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="container"></a>Kontener

Kontener porządkuje zestaw obiektów blob, pełniąc funkcję podobną do folderu w systemie plików. Wszystkie obiekty blob znajdują się w określonym kontenerze. Konto magazynu może zawierać nieograniczoną liczbę kontenerów, a każdy kontener może zawierać nieograniczoną liczbę obiektów blob. Pamiętaj, że wszystkie litery w nazwie kontenera muszą być małymi literami.

### <a name="blob"></a>Obiekt blob
 
W usłudze Azure Storage dostępne są trzy typy obiektów blob: blokowe obiekty blob, uzupełnialne obiekty blob i [stronicowe obiekty blob](../articles/storage/blobs/storage-blob-pageblob-overview.md) (używane w przypadku plików VHD).

* Blokowe obiekty blob mogą zawierać dane tekstowe i binarne o rozmiarze do około 4,7 TB. Blokowe obiekty blob składają się z bloków danych, którymi można zarządzać indywidualnie.
* Uzupełnialne obiekty blob również składają się z bloków, podobnie jak blokowe obiekty blob, lecz są zoptymalizowane pod kątem operacji dołączania. Uzupełnialne obiekty blob doskonale sprawdzają się w sytuacjach takich jak zapisywanie danych dzienników z maszyn wirtualnych.
* Stronicowe obiekty blob umożliwiają przechowywanie plików o dostępie swobodnym o rozmiarze do 8 TB. Stronicowe obiekty blob są używane do przechowywania plików VHD wspierających maszyny wirtualne.

Wszystkie obiekty blob znajdują się w określonym kontenerze. Kontener przypomina folder w systemie plików. Można dodatkowo porządkować obiekty blob w katalogach wirtualnych i przechodzić pomiędzy nimi tak jak w systemie plików. 

W przypadku bardzo dużych zestawów danych, gdy ograniczenia sieci mogą w praktyce uniemożliwić przekazanie lub pobranie danych do usługi Blob Storage, możesz przesłać zestaw dysków twardych do firmy Microsoft, aby zaimportować dane bezpośrednio do centrum danych lub wyeksportować je stamtąd. Aby uzyskać więcej informacji, zobacz [Use the Microsoft Azure Import/Export Service to Transfer Data to Blob Storage (Przesyłanie danych do usługi Blob Storage za pomocą usługi Microsoft Azure Import/Export)](../articles/storage/common/storage-import-export-service.md).
  
Aby uzyskać szczegółowe informacje o nazewnictwie kontenerów i obiektów blob, zobacz temat [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych).
