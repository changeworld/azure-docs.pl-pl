---
title: Wyświetlić listę wszystkich zadań w ramach usługi Azure Import/Export | MicrosoftDocs
description: Dowiedz się, jak wyświetlić listę wszystkich zadań usługi Azure Import/Export w ramach subskrypcji.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478695"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Wyliczanie zadań w usłudze Azure Import/Export
Aby wyliczyć wszystkich zadań w ramach subskrypcji, należy wywołać [listy zadań](/rest/api/storageimportexport/jobs) operacji. `List Jobs` Zwraca listę zadań, a także następujące atrybuty:

-   Typ zadania (importu lub eksportu)

-   Bieżący stan zadania

-   Zadanie skojarzonej z kontem magazynu

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Export](storage-import-export-using-the-rest-api.md)
