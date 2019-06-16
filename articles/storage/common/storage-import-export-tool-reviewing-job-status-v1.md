---
title: Sprawdzanie stanu zadania usługi Azure Import/Export - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać pliki dziennika utworzone podczas uruchomienia zadania importu lub eksportu, aby wyświetlić stan zadania importu/eksportu.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306e3ccf19ba8db2de01e4b20a52707215a4a040
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320710"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Sprawdzanie stanu zadania usługi Azure Import/Export, za pomocą plików dziennika kopiowania
Kiedy usługa Microsoft Azure Import/Export przetwarza dyski skojarzone z zadaniem importowania lub eksportowania, zapisuje kopiowania plików dziennika do konta magazynu do lub z którego są importowanie lub eksportowanie obiektów blob. Plik dziennika zawiera szczegółowe informacje o każdym z plików, który został zaimportować lub wyeksportować. Kiedy wykonujesz zapytanie o stan ukończonego zadania; zwracany jest adres URL do każdego pliku dziennika kopiowania zobacz [pobrania zadania](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) Aby uzyskać więcej informacji.  

## <a name="example-urls"></a>Adresy URL

Adresy URL dla plików dziennika kopiowania do zadania importu za pomocą dwa dyski są następujące:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Zobacz [usługi Import/Export Format pliku dziennika](../storage-import-export-file-format-log.md) dla formatu Kopiuj dzienniki i pełną listę kodów stanu.  
  
## <a name="next-steps"></a>Kolejne kroki
 
 * [Konfigurowanie narzędzia Azure Import/Export](storage-import-export-tool-setup-v1.md)   
 * [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Naprawianie zadania importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
