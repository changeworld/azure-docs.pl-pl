---
title: Podręczna karta informacyjna dotycząca polecenia zadania importu narzędzia importu/eksportu Azure - v1 | Dokumentacja firmy Microsoft
description: Dokumentacja poleceń Azure narzędzie importu/eksportu importu często używanych poleceń zadania. Odnosi się do v1 narzędzia importu/eksportu.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 370cf6fae7ae106e8341f65086c8b8187d335746
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873681"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Krótki przewodnik dotyczący często używanych poleceń zadań importu
Ta sekcja zawiera szybki przegląd niektóre często używanych poleceń. Aby uzyskać szczegółowe dane użycia, zobacz [przygotowywanie dyski twarde dla zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Przygotowanie dysk twardy, jeśli dane zostały już skopiowane na dysku twardym
 Następujące polecenie przygotowuje dysk twardy, gdy dane już został skopiowany do niego, ale jeszcze nie został zaszyfrowany za pomocą funkcji BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Skopiuj jednego katalogu na dysku twardym  
 Następujące polecenie kopiuje katalog źródłowy pojedynczy dysk twardy, który nie ma jeszcze zaszyfrowany za pomocą funkcji BitLocker:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Skopiuj dwa katalogi na dysku twardym  
 Aby skopiować dwa katalogi źródłowe na dysku, użyj następujących poleceń:  
  
 Pierwsze polecenie Określa katalog dziennika, klucz konta magazynu, literę dysku docelowym, `format/encrypt` wymagania oraz typowe parametry:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 Drugie polecenie Określa plik dziennika, nowy identyfikator sesji i lokalizacje źródłowa i docelowa:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Kopiowanie dużych plików na dysk twardy w drugiej sesji kopiowania  
 Polecenie kopiuje pojedynczy duży plik na dysk twardy, który został przygotowany w poprzedniej sesji kopiowania:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Następne kroki

* [Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
