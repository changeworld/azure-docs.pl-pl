---
title: Przy użyciu interfejsu API REST usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, gdzie można znaleźć zasoby dotyczące korzystania z usługi Azure Import/Export interfejsu API REST, w tym zarówno instrukcje oraz materiał odniesienia.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 1e8b60f37cefb81fbbbbb7823be7752dd1188dc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320282"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Korzystanie z interfejsu API REST usługi Azure Import/Export

Usługa Microsoft Azure Import/Export uwidacznia interfejs API REST do umożliwiają programistyczną kontrolę zadania importu/eksportu. Można użyć interfejsu API REST do wykonywania operacji importu/eksportu, które można wykonywać za pomocą [witryny Azure portal](https://portal.azure.com/). Ponadto można użyć interfejsu API REST do wykonywania określonych operacji szczegółowe, takich jak zapytania procent ukończenia zadania, które nie jest obecnie dostępna w witrynie Azure portal.

Zobacz [przy użyciu usługi Microsoft Azure Import/Export przesyłanie danych do magazynu obiektów Blob](../storage-import-export-service.md) z omówieniem usługi Import/Export a samouczek, w którym pokazano, jak korzystać z portalu do tworzenia i zarządzanie nimi, Importuj i Eksportuj zadania.

## <a name="service-endpoints"></a>Punkty końcowe usługi

Usługa Azure Import/Export jest dostawcy zasobów usługi Azure Resource Manager i zawiera zestaw interfejsów API REST na następujący punkt końcowy HTTPS do zarządzania zadaniami importowania/eksportowania:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Obsługa wersji

Należy określić żądania do usługi Import/Export `api-version` parametru i ustawić jej wartość na `2016-11-01`.

## <a name="importexport-service-operations"></a>Operacje usługi Import/Export

[Tworzenie zadania importu](../storage-import-export-creating-an-import-job.md)

[Tworzenie zadania eksportu](../storage-import-export-creating-an-export-job.md)

[Pobieranie informacji o stanie zadania](storage-import-export-retrieving-state-info-for-a-job.md)

[Wyliczanie zadań](../storage-import-export-enumerating-jobs.md)

[Anulowanie i usuwanie zadań](storage-import-export-cancelling-and-deleting-jobs.md)

[Wykonywanie kopii zapasowej manifestów dysków](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostyka i odzyskiwanie po błędach zadań usługi Import/Export](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Kolejne kroki

* [Magazyn Import/Export REST](/rest/api/storageimportexport)
