---
title: Wyświetl listę wszystkich zadań Import/Eksport Azure | MicrosoftDocs
description: Dowiedz się, jak wyświetlić listę wszystkich zadań usługi Import/Eksport Azure w ramach subskrypcji.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873702"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Wyliczanie zadań w usłudze Import/Eksport Azure
Aby wyliczyć wszystkie zadania w ramach subskrypcji, należy wywołać [listy zadań](/rest/api/storageimportexport/jobs#Jobs_List) operacji. `List Jobs`Zwraca listę zadań, a także następujące atrybuty:

-   Typ zadania (Importowanie lub eksportowanie)

-   Bieżący stan zadania

-   Zadanie powiązanych konta magazynu

## <a name="next-steps"></a>Następne kroki

* [Przy użyciu interfejsu API REST usługi Import/Eksport](storage-import-export-using-the-rest-api.md)
