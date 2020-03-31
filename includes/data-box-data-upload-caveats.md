---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73800011"
---
- Nie kopiuj plików bezpośrednio do żadnego z wstępnie skreakrowanych udziałów. Należy utworzyć folder w ramach udziału, a następnie skopiować pliki do tego folderu.
- Folder pod *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* jest kontenerem. Na przykład kontenery są tworzone jako *StorageAccount_BlockBlob/kontener* i *StorageAccount_PageBlob/kontener*.
- Każdy folder utworzony bezpośrednio w obszarze *StorageAccount_AzureFiles* jest tłumaczony na udział plików platformy Azure.
- Jeśli masz istniejący obiekt platformy Azure (na przykład obiekt blob lub plik) w chmurze o takiej samej nazwie jak obiekt, który jest kopiowany, data box zastąpi plik w chmurze.
- Każdy plik zapisany w *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* udziałów jest przekazywane jako blok obiektu blob i stronicowy obiekt blob odpowiednio.
- Usługa Azure blob storage nie obsługuje katalogów. Jeśli utworzysz folder w folderze *StorageAccount_BlockBlob,* foldery wirtualne zostaną utworzone w nazwie obiektu blob. W przypadku usług Azure Files struktura rzeczywistego katalogu jest utrzymywana.
- Żadna pusta hierarchia katalogów (bez plików) utworzona *w StorageAccount_BlockBlob* i *StorageAccount_PageBlob* folderów nie jest przekazywała.
- Jeśli występują błędy podczas przekazywania danych na platformę Azure, dziennik błędów jest tworzony na koncie magazynu docelowego. Ścieżka do tego dziennika błędów jest dostępna po zakończeniu przekazywania i można przejrzeć dziennik, aby podjąć działania naprawcze. Nie usuwaj danych ze źródła bez weryfikacji przekazanych danych.
- Metadane plików i uprawnienia NTFS nie są zachowywane, gdy dane są przekazywane do usługi Azure Files. Na przykład *atrybut Ostatniej modyfikacji* plików nie będzie przechowywany podczas kopiowania danych.
