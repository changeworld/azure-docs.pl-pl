---
title: Wyświetlić listę wszystkich zadań w ramach usługi Azure Import/Export | MicrosoftDocs
description: Dowiedz się, jak wyświetlić listę wszystkich zadań usługi Azure Import/Export w ramach subskrypcji.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 69daac71b69969a7ad9acfeb7095053f8138bf53
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520884"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Wyliczanie zadań w usłudze Azure Import/Export
Aby wyliczyć wszystkich zadań w ramach subskrypcji, należy wywołać [listy zadań](/rest/api/storageimportexport/jobs#Jobs_List) operacji. `List Jobs` Zwraca listę zadań, a także następujące atrybuty:

-   Typ zadania (importu lub eksportu)

-   Bieżący stan zadania

-   Zadanie skojarzonej z kontem magazynu

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Export](storage-import-export-using-the-rest-api.md)
