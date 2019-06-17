---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: b159ec8620fa8c93e4917f73be9b9898e1b4fbcc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244585"
---
- Nie należy kopiować pliki bezpośrednio z precreated udziałów. Należy utworzyć folder w udziale, a następnie skopiuj pliki do tego folderu.
- Folder w węźle *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* jest kontenerem. Na przykład kontenery są tworzone jako *StorageAccount_BlockBlob/kontenera* i *StorageAccount_PageBlob/kontenera*.
- Każdy folder utworzone bezpośrednio w ramach *StorageAccount_AzureFiles* jest tłumaczony na udział plików platformy Azure.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt blob lub pliku) w chmurze o tej samej nazwie jako obiektu, które są kopiowane urządzenia Data Box spowoduje zastąpienie plików w chmurze.
- Każdy plik zapisywane *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* akcji jest przekazywany jako blokowe obiekty blob i stronicowych obiektów blob odpowiednio.
- Magazyn obiektów blob platformy Azure nie obsługuje katalogów. Jeśli utworzysz folder w węźle *StorageAccount_BlockBlob* folder, a następnie foldery wirtualne, które są tworzone w nazwie obiektu blob. Dla usługi Azure Files jest zachowywana na strukturę katalogu.
- Dowolne puste hierarchii katalogów (bez żadnych plików) utworzone w ramach *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* folderów nie jest przekazany.
- Jeśli występują błędy podczas przekazywania danych na platformie Azure, w dzienniku błędów jest tworzony w docelowym koncie magazynu. Ścieżka do tego dziennika błędów jest dostępna po zakończeniu przekazywania i przejrzeć dziennik podjęcia akcji naprawczej. Nie należy usuwać dane ze źródła bez weryfikowania przekazane dane.
