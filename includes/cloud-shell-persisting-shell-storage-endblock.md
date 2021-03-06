---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183568"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Przenoszenie plików lokalnych do powłoki chmury
Katalog `clouddrive` jest synchronizowany z blokiem magazynu witryny Azure portal. Użyj tego bloku, aby przenieść pliki lokalne do lub z udziału plików. Aktualizowanie plików z poziomu powłoki cloud jest odzwierciedlane w interfejsie gui magazynu plików podczas odświeżania bloku.

### <a name="download-files"></a>Pobieranie plików

![Lista plików lokalnych](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. W witrynie Azure portal przejdź do zainstalowanego udziału plików.
2. Wybierz plik docelowy.
3. Wybierz przycisk **Pobierz.**

### <a name="upload-files"></a>Przekazywanie plików

![Pliki lokalne do przesłania](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Przejdź do zainstalowanego udziału plików.
2. Wybierz przycisk **Przekaż**.
3. Wybierz plik lub pliki, które chcesz przekazać.
4. Potwierdź przesłanie.

Teraz powinny być widoczne pliki, `clouddrive` które są dostępne w katalogu w aplikacji Cloud Shell.