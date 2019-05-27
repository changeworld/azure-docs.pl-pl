---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 9ffb02fce41e8805dfccf1dfd6e982cf107039ec
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127542"
---
| **Zasób** | **Limit domyślny** | **Maksymalny limit** |
| --- | --- | --- |
| Konta usługi Azure Batch na region na subskrypcję | 1-3 |50 |
| Liczba rdzeni dedykowanych na konto usługi Batch | 10-100 | Nie dotyczy<sup>1</sup> |
| Rdzeni o niskim priorytecie na konto usługi Batch | 10-100 | N/D<sup>2</sup> |
| Aktywne zadania i harmonogramy zadań<sup>3</sup> na konto usługi Batch | 100-300 | 1,000<sup>4</sup> |
| Liczba pul na konto usługi Batch | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> Domyślne limity zależą od typu Twojej subskrypcji, który umożliwia utworzenie konta usługi Batch. Limity przydziałów rdzeni, wyświetlane są przeznaczone dla konta usługi Batch w trybie usługi Batch. [Wyświetl przydziały na koncie usługi Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup>Liczba dedykowanych rdzeni na konto usługi Batch można zwiększyć, ale maksymalna liczba jest nieokreślona. W celu omówienia możliwości zwiększenia, skontaktuj się z działem pomocy technicznej systemu Azure.

<sup>2</sup>liczby rdzeni o niskim priorytecie na konto usługi Batch można zwiększyć, ale maksymalna liczba jest nieokreślona. W celu omówienia możliwości zwiększenia, skontaktuj się z działem pomocy technicznej systemu Azure.

<sup>3</sup>ukończone zadania i harmonogramy zadań nie są ograniczone.

<sup>4</sup>aby poprosić o zwiększenie wykraczające poza ten limit, skontaktuj się z działem pomocy technicznej systemu Azure.
