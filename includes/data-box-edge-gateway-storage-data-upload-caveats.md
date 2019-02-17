---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 8bc4b78d262cf5f30076e90b40b92c4f3237924a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333986"
---
Następujące zastrzeżenia stosowane do danych, kiedy przesuwa się on na platformie Azure.

- Zaleca się, że więcej niż jedno urządzenie nie należy zapisać do tego samego kontenera.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt blob lub pliku) w chmurze o tej samej nazwie jako obiektu, które są kopiowane urządzenia spowoduje zastąpienie plików w chmurze.
- Hierarchia pusty katalog (bez żadnych plików) utworzone w ramach folderów udziału nie jest przekazywany do kontenerów obiektów blob.
- Dla dużych plików firma Microsoft zaleca używanie robocopy, ponieważ ponowną próbą operacji kopiowania sporadyczne błędy.
- Jeśli udział skojarzonych z kontenerem usługi Azure storage prześle obiektów blob, które nie są zgodne z typu zdefiniowane dla tego udziału w czasie tworzenia obiektów blob, takich obiektów blob nie są aktualizowane. Na przykład utworzyć udział blokowych obiektów blob na urządzeniu. Skojarzyć udział z istniejącego kontenera w chmurze zawierającej stronicowych obiektów blob. Odśwież udziału do pobierania plików. Modyfikowanie niektórych plików odświeżenia, które już są przechowywane jako stronicowe obiekty BLOB w chmurze. Zostanie wyświetlony błędy przekazywania.
