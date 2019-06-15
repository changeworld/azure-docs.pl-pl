---
title: Krótki przewodnik dotyczący poleceń zadań importu narzędzie importu/eksportu platformy Azure — v1 | Dokumentacja firmy Microsoft
description: Dokumentacja poleceń Azure narzędzie importu/eksportu poleceń zadań importu często używanych. Dotyczy to v1 narzędzie importu/eksportu.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 44ee23a510bc15d5cb52a338be1652180b922db9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478440"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Krótki przewodnik dotyczący często używanych poleceń zadań importu
Ta sekcja zawiera krótki przewodnik dla niektórych często używanych poleceń. Aby uzyskać szczegóły użycia, zobacz [przygotowywania dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Przygotuj dysk twardy, jeśli dane zostały skopiowane na dysku twardym
 Następujące polecenie przygotowuje dysk twardy, gdy dane już został skopiowany do niego, ale jeszcze nie został zaszyfrowany za pomocą funkcji BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Skopiuj jeden katalog do dysku twardego  
 Następujące polecenie kopiuje katalog źródłowy pojedynczy dysk twardy, który jeszcze nie został zaszyfrowany za pomocą funkcji BitLocker:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Skopiuj dwa katalogi na dysku twardym  
 Aby skopiować dwa katalogi źródłowe na dysku, użyj następujących poleceń:  
  
 Pierwsze polecenie Określa katalog dziennika, klucz konta magazynu, literę dysku docelowym, `format/encrypt` wymagania oraz typowe parametry:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 Drugie polecenie Określa plik dziennika, nowy identyfikator sesji i lokalizacja źródłowa i docelowa:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Kopiowanie dużych plików na dysku twardym w kolejnej sesji kopiowania  
 Następujące polecenie kopiuje pojedynczego dużego pliku na dysku twardym, który został przygotowany w poprzedniej sesji kopii:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Kolejne kroki

* [Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
