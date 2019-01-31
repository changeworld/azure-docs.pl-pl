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
ms.openlocfilehash: 017208c6fca7c4e55a45070f5aa21652e83e7e8d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462929"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Wyliczanie zadań w usłudze Azure Import/Export
Aby wyliczyć wszystkich zadań w ramach subskrypcji, należy wywołać [listy zadań](/rest/api/storageimportexport/jobs#Jobs_List) operacji. `List Jobs` Zwraca listę zadań, a także następujące atrybuty:

-   Typ zadania (importu lub eksportu)

-   Bieżący stan zadania

-   Zadanie skojarzonej z kontem magazynu

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Export](storage-import-export-using-the-rest-api.md)
