---
title: Przeglądanie stanu zadania Import/Eksport Azure - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pliki dziennika utworzone po sprzed uruchomienia zadania importu lub eksportu, aby wyświetlić stan zadania importu/eksportu.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.openlocfilehash: bdb30bc28c36ab9e969efc8be3b87b97e4027b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873709"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Przeglądanie Import/Eksport Azure stan zadania kopiowania plików dzienników
Kiedy usługa Import/Eksport Microsoft Azure przetwarza dyski skojarzone z zadaniem importu lub eksportu, zapisuje kopiowania plików dziennika do konta magazynu do lub z którego są importowania lub eksportowania obiektów blob. Plik dziennika zawiera szczegółowe informacje dotyczące każdego pliku, który został zaimportowany ani eksportowane. Zwracany jest adres URL do każdego pliku dziennika kopii po wykonaniu kwerendy stanu zadanie zostało ukończone; zobacz [pobrania zadania](/rest/api/storageservices/Get-Job3) Aby uzyskać więcej informacji.  

## <a name="example-urls"></a>Adresy URL

Adresy URL do kopiowania plików dziennika dla zadania importu z dwóch dysków są następujące:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Zobacz [usługi Import/Eksport Format pliku dziennika](../storage-import-export-file-format-log.md) formatu kopiowaniu dzienników i z pełną listą kodów stanu.  
  
## <a name="next-steps"></a>Następne kroki
 
 * [Trwa konfigurowanie narzędzia Azure Import/Eksport](storage-import-export-tool-setup-v1.md)   
 * [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Naprawianie zadania importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
