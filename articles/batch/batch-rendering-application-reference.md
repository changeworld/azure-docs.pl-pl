---
title: Korzystanie z renderowania aplikacji — Azure Batch
description: Jak korzystać z aplikacji renderowanie przy użyciu usługi Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 4c93abdfb5c523d48ce115ed7d3251a346937f5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775393"
---
# <a name="rendering-applications"></a>Renderowanie aplikacji

Renderowanie aplikacji są używane przez utworzenie zadania wsadowe i zadania. Określa właściwość wiersza polecenia zadania, odpowiedni wiersz polecenia i parametry.  Najprostszym sposobem utworzenia zadania jest za pomocą szablonów programu Batch Explorer, jak to określono w [w tym artykule](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  Szablony można wyświetlać i modyfikować wersji tworzonych w razie potrzeby.

Ten artykuł zawiera krótki opis sposobu uruchamiania każdej aplikacji do renderowania.

## <a name="rendering-with-autodesk-3ds-max"></a>Renderowanie za pomocą programu Autodesk 3ds Max

### <a name="renderer-support"></a>Obsługa programu renderującego

Oprócz renderowania wbudowanych w aplikacji 3ds Max, następujące programy renderujące są dostępne na renderowanie obrazów maszyn wirtualnych i mogą być przywoływane przez plik 3ds Max sceny:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Wiersz polecenia zadania

Wywoływanie `3dsmaxcmdio.exe` aplikacji w celu wykonywania wiersza polecenia renderowania w węźle puli.  Ta aplikacja jest na ścieżce, gdy zadanie zostanie uruchomione. `3dsmaxcmdio.exe` Aplikacja ma te same parametry dostępne jako `3dsmaxcmd.exe` aplikację, która jest udokumentowany w [3ds Max dokumentacji](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (renderowania | Sekcja renderowania wiersza polecenia).

Na przykład:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Uwagi:

* Szczególną uwagę należy upewnić się, że znajdują się pliki zasobów.  Upewnij się, ścieżki są względne i poprawne przy użyciu **trwałe** okna lub użyj `-bitmapPath` parametr w wierszu polecenia.
* Czy istnieją problemy z renderowania, takich jak brakiem, aby znaleźć zasoby, sprawdzając `stdout.txt` plików zapisanych przez 3ds Max, gdy zadanie zostanie uruchomione.

### <a name="batch-explorer-templates"></a>Eksplorator szablonów usługi Batch

Szablony puli i zadania są dostępne z **galerii** programu Batch Explorer.  Pliki źródłowe szablonu są dostępne w [programu Batch Explorer repozytorium danych w usłudze GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Renderowanie na platformie programy Autodesk Maya

### <a name="renderer-support"></a>Obsługa programu renderującego

Oprócz wbudowanych w programie Maya programy renderujące następujące programy renderujące są dostępne na renderowanie obrazów maszyn wirtualnych i mogą być przywoływane przez plik 3ds Max sceny:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Wiersz polecenia zadania

`renderer.exe` Renderowania wiersza polecenia jest używany w wierszu polecenia zadania. Renderowanie wiersza polecenia jest udokumentowany w [Pomoc programu Maya](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

W poniższym przykładzie zadanie podrzędne przygotowania zadania jest używana do kopiowania plików scen i zasoby do katalogu roboczego zadania przygotowania folder wyjściowy jest używany do przechowywania obrazu renderowania i ramki 10 jest renderowany.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Renderowanie V-Ray pliku sceny programu Maya zazwyczaj określić V-Ray jako modułu renderowania.  Można go również określić w wierszu polecenia:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Aby uzyskać Arnold renderowanie, plik sceny programu Maya zazwyczaj określić Arnold jako modułu renderowania.  Można go również określić w wierszu polecenia:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Eksplorator szablonów usługi Batch

Szablony puli i zadania są dostępne z **galerii** programu Batch Explorer.  Pliki źródłowe szablonu są dostępne w [programu Batch Explorer repozytorium danych w usłudze GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Kolejne kroki

Korzystanie z szablonów puli i zadania z [repozytorium danych w usłudze GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) przy użyciu programu Batch Explorer.  Gdy jest to wymagane, tworzyć nowych szablonów, lub zmodyfikuj jedną dostarczane szablony.