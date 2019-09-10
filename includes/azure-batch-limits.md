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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "67080922"
---
| **Zasób** | **Limit domyślny** | **Maksymalny limit** |
| --- | --- | --- |
| Azure Batch kont na region na subskrypcję | 1-3 |50 |
| Rdzenie dedykowane na konto partii | 90-900 | Skontaktuj się z pomocą techniczną |
| Rdzenie o niskim priorytecie na konto partii | 10-100 | Skontaktuj się z pomocą techniczną |
| **[Aktywne](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** zadania i harmonogramy zadań na konto usługi Batch (**ukończone** zadania nie mają limitu) | 100-300 | 1 000<sup>1</sup> |
| Liczba pul na konto usługi Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Domyślne limity różnią się w zależności od typu subskrypcji używanej do tworzenia konta usługi Batch. Wyświetlane są przydziały dla kont usługi Batch w trybie usłudze Batch. [Wyświetl przydziały na koncie w usłudze Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> Aby zażądać zwiększenia ponad ten limit, skontaktuj się z pomocą techniczną platformy Azure.
