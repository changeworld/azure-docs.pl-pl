---
title: Korzystanie z interfejsu API REST usługi importu/eksportowania platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, gdzie można znaleźć zasoby do korzystania z interfejsu API REST usługi Azure Import/Export, w tym materiałów inkoncyzowych i referencyjnych.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978870"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Korzystanie z interfejsu API REST usługi Azure Import/Export

Usługa Import/Eksport microsoft Azure udostępnia interfejs API REST, aby włączyć programową kontrolę zadań importowania/eksportowania. Za pomocą interfejsu API REST można wykonać wszystkie operacje importowania/eksportowania, które można wykonać za pomocą [witryny Azure portal](https://portal.azure.com/). Ponadto można użyć interfejsu API REST do wykonywania niektórych szczegółowych operacji, takich jak wykonywanie zapytań procentowego ukończenia zadania, które nie jest obecnie dostępne w witrynie Azure portal.

Zobacz [Korzystanie z usługi Importowanie/eksportowanie platformy Microsoft Azure w celu przeniesienia danych do magazynu obiektów blob, aby zapoznać](../storage-import-export-service.md) się z omówieniem usługi importu/eksportu oraz samouczkiem, który pokazuje, jak używać portalu do tworzenia zadań importowania i eksportowania oraz zarządzania nimi.

## <a name="service-endpoints"></a>Punkty końcowe usługi

Usługa importu/eksportu platformy Azure jest dostawcą zasobów dla usługi Azure Resource Manager i udostępnia zestaw interfejsów API REST w następującym punkcie końcowym HTTPS do zarządzania zadaniami importu/eksportu:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Przechowywanie wersji

Żądania do usługi Import/Eksport `api-version` należy określić parametr `2016-11-01`i ustawić jego wartość na .

## <a name="importexport-service-operations"></a>Importowanie/eksportowanie operacji serwisowych

[Tworzenie zadania importu](../storage-import-export-creating-an-import-job.md)

[Tworzenie zadania eksportu](../storage-import-export-creating-an-export-job.md)

[Pobieranie informacji o stanie zadania](storage-import-export-retrieving-state-info-for-a-job.md)

[Wyliczanie zadań](../storage-import-export-enumerating-jobs.md)

[Anulowanie i usuwanie zadań](storage-import-export-cancelling-and-deleting-jobs.md)

[Tworzenie kopii zapasowych manifestów dysku](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostyka i odzyskiwanie po błędach zadań usługi Import/Export](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Następne kroki

* [Magazyn Import/Eksport REST](/rest/api/storageimportexport)
