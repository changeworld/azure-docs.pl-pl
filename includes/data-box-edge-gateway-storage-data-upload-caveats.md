---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161257"
---
Następujące zastrzeżenia stosowane do danych, kiedy przesuwa się on na platformie Azure.

- Zaleca się, że więcej niż jedno urządzenie nie należy zapisać do tego samego kontenera.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt blob lub pliku) w chmurze o tej samej nazwie jako obiektu, które są kopiowane urządzenia spowoduje zastąpienie plików w chmurze.
- Hierarchia pusty katalog (bez żadnych plików) utworzone w ramach folderów udziału nie jest przekazywany do kontenerów obiektów blob.
- Można skopiować danych za pomocą przeciągania i upuszczania za pomocą Eksploratora plików lub za pomocą wiersza polecenia. Jeśli łączny rozmiar plików kopiowanych jest większy niż 10 GB, firma Microsoft zaleca, że używasz programu do kopiowania zbiorczego, takie jak Robocopy lub polecenia rsync. Narzędzia do kopiowania zbiorczego ponów próbę wykonania operacji kopiowania sporadyczne błędy i zapewnia dodatkową odporność.
- Jeśli udział skojarzonych z kontenerem usługi Azure storage prześle obiektów blob, które nie są zgodne z typu zdefiniowane dla tego udziału w czasie tworzenia obiektów blob, takich obiektów blob nie są aktualizowane. Na przykład utworzyć udział blokowych obiektów blob na urządzeniu. Skojarzyć udział z istniejącego kontenera w chmurze zawierającej stronicowych obiektów blob. Odśwież udziału do pobierania plików. Modyfikowanie niektórych plików odświeżenia, które już są przechowywane jako stronicowe obiekty BLOB w chmurze. Zostanie wyświetlony błędy przekazywania.
