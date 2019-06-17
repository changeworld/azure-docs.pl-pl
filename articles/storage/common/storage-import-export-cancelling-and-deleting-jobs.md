---
title: Anuluj i Usuń zadanie usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak anulowania i usuwania zadań dla usługi Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 6eb56413319208feef1b4ab51296fe12a1e0bcf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61483149"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Anulowanie i usuwanie zadań usługi Azure Import/Export

 Na żądanie, czy można anulować zadania, zanim zostanie w `Packaging` stanu, wywołaj [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs) operacji i ustaw `CancelRequested` elementu `true`. Zadanie zostało anulowane na zasadzie największej staranności. Jeśli dyski znajdują się w trakcie przesyłania danych, dane mogą być nadal przesyłane, mimo że zażądano anulowania.

 Anulowano zadanie zostanie przeniesiona do `Completed` stanu i są przechowywane przez 90 dni, w tym momencie zostanie usunięty.

 Aby usunąć zadanie, należy wywołać [Usuń zadanie](/rest/api/storageimportexport/jobs) operacji przed zadanie zostało wysłane (oznacza to, że podczas wykonywania zadania w `Creating` stanu). Można również usunąć zadanie, gdy jest on w `Completed` stanu. Po usunięciu zadania jego informacje i jego stan nie są już dostępne za pośrednictwem interfejsu API REST lub witryny Azure portal.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Export](storage-import-export-using-the-rest-api.md)
