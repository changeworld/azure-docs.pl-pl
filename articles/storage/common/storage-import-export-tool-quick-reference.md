---
title: Krótki przewodnik dotyczący poleceń zadań importu narzędzie importu/eksportu platformy Azure | Dokumentacja firmy Microsoft
description: Dokumentacja poleceń Azure narzędzie importu/eksportu poleceń zadań importu często używanych.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 7d21ab42188d5b8d6cb8c179a28d1b5bee822f05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478423"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Krótki przewodnik dotyczący często używanych poleceń zadań importu

Ten artykuł zawiera krótki przewodnik dla niektórych często używanych poleceń. Aby uzyskać szczegóły użycia, zobacz [przygotowywania dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="first-session"></a>Pierwsza sesja

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>Drugi sesji

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>Przerwij najnowszych sesji

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>Wznów najnowsza wersja przerwane sesji

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>Dodawanie dysków do najnowszych sesji

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Kolejne kroki

* [Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
