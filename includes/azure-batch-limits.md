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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080922"
---
| **Zasób** | **Limit domyślny** | **Maksymalny limit** |
| --- | --- | --- |
| Konta usługi Azure Batch na region na subskrypcję | 1-3 |50 |
| Liczba rdzeni dedykowanych na konto usługi Batch | 90-900 | Skontaktuj się z pomocą techniczną |
| Rdzeni o niskim priorytecie na konto usługi Batch | 10-100 | Skontaktuj się z pomocą techniczną |
| **[Aktywne](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)**  zadania i harmonogramy zadań na konto usługi Batch (**ukończone** zadań nie mają limitu) | 100-300 | 1,000<sup>1</sup> |
| Pule na konto usługi Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Domyślne limity zależą od typu Twojej subskrypcji, który umożliwia utworzenie konta usługi Batch. Limity przydziałów rdzeni, wyświetlane są przeznaczone dla konta usługi Batch w trybie usługi Batch. [Wyświetl przydziały na koncie usługi Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup>aby poprosić o zwiększenie wykraczające poza ten limit, skontaktuj się z działem pomocy technicznej systemu Azure.
