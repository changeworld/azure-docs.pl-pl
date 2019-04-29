---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b48dbfc33890df12209c3a242d812ad2103e07a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776377"
---
| **Zasób** | **Limit domyślny** | **Maksymalny limit** |
| --- | --- | --- |
| Konta usługi Azure Batch na region na subskrypcję | 1-3 |50 |
| Liczba rdzeni dedykowanych na konto usługi Batch | 10-100 | Nie dotyczy<sup>1</sup> |
| Rdzeni o niskim priorytecie na konto usługi Batch | 10-100 | N/D<sup>2</sup> |
| Aktywne zadania i harmonogramy zadań<sup>3</sup> na konto usługi Batch | 100-300 | 1,000<sup>4</sup> |
| Pule na konto usługi Batch | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> Domyślne limity zależą od typu Twojej subskrypcji, który umożliwia utworzenie konta usługi Batch. Limity przydziałów rdzeni, wyświetlane są przeznaczone dla konta usługi Batch w trybie usługi Batch. [Wyświetl przydziały na koncie usługi Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup>Liczba dedykowanych rdzeni na konto usługi Batch można zwiększyć, ale maksymalna liczba jest nieokreślona. W celu omówienia możliwości zwiększenia, skontaktuj się z działem pomocy technicznej systemu Azure.

<sup>2</sup>liczby rdzeni o niskim priorytecie na konto usługi Batch można zwiększyć, ale maksymalna liczba jest nieokreślona. W celu omówienia możliwości zwiększenia, skontaktuj się z działem pomocy technicznej systemu Azure.

<sup>3</sup>ukończone zadania i harmonogramy zadań nie są ograniczone.

<sup>4</sup>aby poprosić o zwiększenie wykraczające poza ten limit, skontaktuj się z działem pomocy technicznej systemu Azure.
