---
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: a8ea55a40f1ee4681b6aec147e02b7bce6f1d7cf
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279807"
---
### <a name="retrieve-output-files"></a>Pobieranie plików wyjściowych

Przy użyciu witryny Azure Portal można pobrać wyjściowe pliki MP3 wygenerowane przez zadania ffmpeg. 

1. Kliknij pozycję **Wszystkie usługi** > **Konta usługi Storage** i kliknij nazwę odpowiedniego konta usługi Storage.
2. Kliknij pozycję **Obiekty Blob** > *dane wyjściowe*.
3. Kliknij prawym przyciskiem myszy jeden z wyjściowych plików MP3, a następnie kliknij polecenie **Pobierz**. Postępuj zgodnie z monitami wyświetlanymi w przeglądarce, aby otworzyć lub zapisać plik.

![Pobieranie pliku wyjściowego](./media/batch-common-tutorial-download/download.png)

Mimo że nie pokazano tego w tym przykładzie, pliki można również pobrać programowo z węzłów obliczeniowych lub z kontenera magazynu.
