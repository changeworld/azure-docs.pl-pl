---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67080922"
---
| **Zasobów** | **Limit domyślny** | **Maksymalny limit** |
| --- | --- | --- |
| Konta usługi Azure Batch na region na subskrypcję | 1-3 |50 |
| Dedykowane rdzenie na konto usługi Batch | 90-900 | Kontakt z pomocą techniczną |
| Rdzenie o niskim priorytecie na konto usługi Batch | 10-100 | Kontakt z pomocą techniczną |
| **[Aktywne](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** zadania i harmonogramy zadań na konto usługi Batch **(ukończone** zadania nie mają limitu) | 100-300 | 1 000<sup>1</sup> |
| Pule na konto usługi Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Domyślne limity różnią się w zależności od typu subskrypcji używanej do tworzenia konta usługi Batch. Wyświetlane przydziały rdzeni są wyświetlane dla kont usługi Batch w trybie usługi wsadowej. [Służy do wyświetlania przydziałów na koncie usługi Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1.</sup> Aby zażądać zwiększenia poza tym limitem, skontaktuj się z pomocą techniczną platformy Azure.
