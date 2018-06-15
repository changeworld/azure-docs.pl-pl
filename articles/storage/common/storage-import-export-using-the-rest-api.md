---
title: Przy użyciu interfejsu API REST usługi Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, gdzie można znaleźć zasobów dla za pomocą usługi Import/Eksport Azure interfejsu API REST, w tym zarówno instrukcje, jak i odwołanie do materiałów.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 9a5a97a5d9f06aa73f1ad521e112fa25f215724f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2017
ms.locfileid: "26690180"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Korzystanie z interfejsu API REST usługi Azure Import/Export

Usługa Import/Eksport Microsoft Azure udostępnia interfejs API REST umożliwiające sterowanie programowe zadań importu i eksportu. Służy do wykonywania operacji importu/eksportu, które można wykonać za pomocą interfejsu API REST [portalu Azure](https://portal.azure.com/). Ponadto można użyć interfejsu API REST wykonywania pewnych operacji szczegółowych, takich jak badania procent zakończenia zadania, która nie jest obecnie dostępna w portalu Azure.

Zobacz [za pomocą usługi Import/Eksport Microsoft Azure przesyłanie danych do magazynu obiektów Blob](../storage-import-export-service.md) omówienie samouczek, który demonstruje sposób tworzenia i zarządzania importowanie i eksportowanie zadań za pomocą portalu i usługi Import/Eksport.

## <a name="service-endpoints"></a>Punkty końcowe usługi

Import/Eksport Azure dostawcy zasobów usługi Azure Resource Manager i usługi zawiera zestaw interfejsów API REST na następujący punkt końcowy HTTPS zarządzania zadania importu/eksportu:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Obsługa wersji

Należy określić żądań do usługi Import/Eksport `api-version` parametru i ustaw dla niego wartość `2016-11-01`.

## <a name="importexport-service-operations"></a>Import/Eksport operacji usługi

[Tworzenie zadania importu](../storage-import-export-creating-an-import-job.md)

[Tworzenie zadania eksportu](../storage-import-export-creating-an-export-job.md)

[Pobieranie informacji o stanie zadania](storage-import-export-retrieving-state-info-for-a-job.md)

[Wyliczanie zadań](../storage-import-export-enumerating-jobs.md)

[Anulowanie i usuwanie zadań](storage-import-export-cancelling-and-deleting-jobs.md)

[Manifesty wykonywanie kopii zapasowych dysków](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostyka i odzyskiwanie po błędach zadań usługi Import/Export](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Następne kroki

* [Magazyn importu/eksportu REST](/rest/api/storageimportexport)
