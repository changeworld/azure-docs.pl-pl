---
title: Korzystanie z aplikacji renderowania — usługa Azure Batch
description: Jak używać aplikacji renderowania za pomocą usługi Azure Batch. Ten artykuł zawiera krótki opis sposobu uruchamiania każdej aplikacji renderowania.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: dc0ce23c90a4ba6575ba26b37d97f94ba8fa1f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75390479"
---
# <a name="rendering-applications"></a>Renderowanie aplikacji

Aplikacje renderowania są używane przez tworzenie zadań i zadań wsadowych. Właściwość wiersza polecenia zadania określa odpowiedni wiersz polecenia i parametry.  Najprostszym sposobem utworzenia zadań zadania jest użycie szablonów Eksploratora wsadowego, jak określono w [tym artykule](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  Szablony można przeglądać i modyfikować wersje utworzone w razie potrzeby.

Ten artykuł zawiera krótki opis sposobu uruchamiania każdej aplikacji renderowania.

## <a name="rendering-with-autodesk-3ds-max"></a>Renderowanie za pomocą programu Autodesk 3ds Max

### <a name="renderer-support"></a>Obsługa modułu renderowania

Oprócz modułów renderowania wbudowanych w program 3ds Max na obrazach maszyn wirtualnych renderowania dostępne są następujące moduły renderowania, do których można się odwoływać za pomocą pliku sceny 3ds Max:

* Autodesk Arnold
* Grupa Chaos V-Ray

### <a name="task-command-line"></a>Wiersz polecenia zadania

Wywołaj `3dsmaxcmdio.exe` aplikację, aby wykonać renderowanie wiersza polecenia w węźle puli.  Ta aplikacja znajduje się na ścieżce po uruchomieniu zadania. Aplikacja `3dsmaxcmdio.exe` ma takie same dostępne `3dsmaxcmd.exe` parametry jak aplikacja, co jest udokumentowane w [dokumentacji pomocy 3ds Max](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (Renderowanie | renderowania wiersza polecenia).

Przykład:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Uwagi:

* Należy dopilnować, aby pliki zasobów zostały znalezione.  Upewnij się, że ścieżki są poprawne i względne `-bitmapPath` za pomocą okna Śledzenie **zasobów** lub użyj parametru w wierszu polecenia.
* Sprawdź, czy występują problemy z renderowaniem, takie jak niemożność znalezienia zasobów, sprawdzając `stdout.txt` plik napisany przez program 3ds Max po uruchomieniu zadania.

### <a name="batch-explorer-templates"></a>Szablony Eksploratora wsadowego

Dostęp do szablonów puli i zadań można uzyskać z **galerii** w Eksploratorze wsadowym.  Pliki źródłowe szablonu są dostępne w [repozytorium danych Eksploratora partii w usłudze GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Renderowanie za pomocą programu Autodesk Maya

### <a name="renderer-support"></a>Obsługa modułu renderowania

Oprócz rendererów wbudowanych w Maya, następujące renderery są dostępne na renderujących obrazach maszyn wirtualnych i mogą się odwoływać do pliku sceny 3ds Max:

* Autodesk Arnold
* Grupa Chaos V-Ray

### <a name="task-command-line"></a>Wiersz polecenia zadania

Renderer wiersza `renderer.exe` polecenia jest używany w wierszu polecenia zadania. Renderer wiersza polecenia jest udokumentowany w [pomocy Maya](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

W poniższym przykładzie zadanie przygotowania zadania służy do kopiowania plików sceny i zasobów do katalogu roboczego przygotowania zadania, folder wyjściowy jest używany do przechowywania obrazu renderowania, a ramka 10 jest renderowana.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

W przypadku renderowania V-Ray plik sceny Maya normalnie określałby V-Ray jako renderer.  Można go również określić w wierszu polecenia:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

W przypadku renderowania Arnolda plik sceny Maya zwykle określałby Arnolda jako renderera.  Można go również określić w wierszu polecenia:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Szablony Eksploratora wsadowego

Dostęp do szablonów puli i zadań można uzyskać z **galerii** w Eksploratorze wsadowym.  Pliki źródłowe szablonu są dostępne w [repozytorium danych Eksploratora partii w usłudze GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Następne kroki

Użyj szablonów puli i zadań z [repozytorium danych w usłudze GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) przy użyciu Eksploratora wsadowego.  W razie potrzeby utwórz nowe szablony lub zmodyfikuj jeden z dostarczonych szablonów.