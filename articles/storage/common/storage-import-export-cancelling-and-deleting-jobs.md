---
title: Anuluj i usunąć zadanie usługi Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak anulować i usuwać zadania usługi Import/Eksport Microsoft Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3524f1677baaa218b009b8498b851390c7b9da9a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367018"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Anulowanie i usuwania zadań Import/Eksport Azure

 Aby zażądać anulowane zadania przed znajduje się on w `Packaging` stanu, należy wywołać [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs#Jobs_Update) operacji i zestawu `CancelRequested` elementu `true`. Zadanie zostało anulowane w sposób optymalny. Jeśli dyski są w trakcie przesyłania danych, dane mogą nadal ma zostać przesłany nawet po zażądano anulowania.

 Anulowane zadanie zostanie przeniesiony do `Completed` stanu i są przechowywane przez 90 dni, w którym są usuwane.

 Aby usunąć zadania, wywołaj [Usuń zadanie](/rest/api/storageimportexport/jobs#Jobs_Delete) operacji przed wysłał zadania (oznacza to, gdy zadanie jest w `Creating` stanu). Możesz także usunąć zadania, gdy jest on w `Completed` stanu. Po usunięciu zadania, jego informacje i jego stan nie są już dostępne za pośrednictwem interfejsu API REST lub w portalu Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Eksport](storage-import-export-using-the-rest-api.md)
