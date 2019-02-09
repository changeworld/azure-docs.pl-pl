---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: ad7ceffed61665a729d4391b5f0ff2935375c253
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967509"
---
Następujące zastrzeżenia stosowane do danych, kiedy przesuwa się on na platformie Azure.

- Zaleca się, że więcej niż jedno urządzenie nie należy zapisać do tego samego kontenera.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt blob lub pliku) w chmurze o tej samej nazwie jako obiektu, które są kopiowane urządzenia spowoduje zastąpienie plików w chmurze.
- Hierarchia pusty katalog (bez żadnych plików) utworzone w ramach folderów udziału nie jest przekazywany do kontenerów obiektów blob.
- Dla dużych plików firma Microsoft zaleca używanie robocopy, ponieważ ponowną próbą operacji kopiowania sporadyczne błędy.
