---
title: Przeglądanie stanu zadania importowania/eksportowania platformy Azure — wersja 1 | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać plików dziennika utworzonych podczas uruchamiania zadania importu lub eksportu, aby wyświetlić stan zadania Importuj/Eksportuj.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 33e6ecca0eb4ca05b1f6bf53cdb3bd4ac8c8d8a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978433"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Przeglądanie stanu zadania importowania/eksportowania platformy Azure za pomocą plików dziennika kopiowania
Gdy usługa Import/Eksport microsoft Azure przetwarza dyski skojarzone z zadaniem importowania lub eksportowania, zapisuje pliki dziennika kopiowania na konto magazynu lub z których importujesz lub eksportujesz obiekty blob. Plik dziennika zawiera szczegółowy stan każdego pliku, który został zaimportowany lub wyeksportowany. Adres URL do każdego pliku dziennika kopiowania jest zwracany podczas kwerendy o stanie ukończonego zadania; Zobacz [Get Job, aby](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) uzyskać więcej informacji.  

## <a name="example-urls"></a>Przykładowe adresy URL

Poniżej przedstawiono przykładowe adresy URL plików dziennika kopiowania dla zadania importowania z dwoma dyskami:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Zobacz [Format pliku dziennika usługi import/eksportowanie,](../storage-import-export-file-format-log.md) aby uzyskać format dzienników kopiowania i pełną listę kodów stanu.  

## <a name="next-steps"></a>Następne kroki

 * [Konfigurowanie narzędzia Importu/Eksportu platformy Azure](storage-import-export-tool-setup-v1.md)   
 * [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Naprawianie zadania importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
