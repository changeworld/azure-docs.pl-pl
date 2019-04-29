---
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: a8ea55a40f1ee4681b6aec147e02b7bce6f1d7cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616618"
---
### <a name="retrieve-output-files"></a>Pobieranie plików wyjściowych

Przy użyciu witryny Azure Portal można pobrać wyjściowe pliki MP3 wygenerowane przez zadania ffmpeg. 

1. Kliknij pozycję **Wszystkie usługi** > **Konta usługi Storage** i kliknij nazwę odpowiedniego konta usługi Storage.
2. Kliknij pozycję **Obiekty Blob** > *dane wyjściowe*.
3. Kliknij prawym przyciskiem myszy jeden z wyjściowych plików MP3, a następnie kliknij polecenie **Pobierz**. Postępuj zgodnie z monitami wyświetlanymi w przeglądarce, aby otworzyć lub zapisać plik.

![Pobieranie pliku wyjściowego](./media/batch-common-tutorial-download/download.png)

Mimo że nie pokazano tego w tym przykładzie, pliki można również pobrać programowo z węzłów obliczeniowych lub z kontenera magazynu.
