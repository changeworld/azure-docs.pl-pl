---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127353"
---
### <a name="retrieve-output-files"></a>Pobieranie plików wyjściowych

Przy użyciu witryny Azure Portal można pobrać wyjściowe pliki MP3 wygenerowane przez zadania ffmpeg. 

1. Kliknij pozycję **Wszystkie usługi** > **Konta usługi Storage** i kliknij nazwę odpowiedniego konta usługi Storage.
2. Kliknij pozycję **Obiekty Blob** > *dane wyjściowe*.
3. Kliknij prawym przyciskiem myszy jeden z wyjściowych plików MP3, a następnie kliknij polecenie **Pobierz**. Postępuj zgodnie z monitami wyświetlanymi w przeglądarce, aby otworzyć lub zapisać plik.

![Pobieranie pliku wyjściowego](./media/batch-common-tutorial-download/download.png)

Mimo że nie pokazano tego w tym przykładzie, pliki można również pobrać programowo z węzłów obliczeniowych lub z kontenera magazynu.
