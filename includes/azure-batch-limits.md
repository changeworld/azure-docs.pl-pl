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
ms.openlocfilehash: c98a2146a019817152be9fae76638dbaa4d9de3d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49458861"
---
| **Zasób** | **Limit domyślny** | **Limit maksymalny** |
| --- | --- | --- |
| Konta usługi Batch na region na subskrypcję | 1 – 3 |50 |
| Liczba rdzeni dedykowanych na konto usługi Batch | 10 - 100 | Nie dotyczy<sup>1</sup> |
| Rdzeni o niskim priorytecie na konto usługi Batch | 10 - 100 | N/D<sup>2</sup> |
| Aktywne zadania i harmonogramy zadań<sup>3</sup> na konto usługi Batch | 100 - 300 | 1000<sup>4</sup> |
| Pule na konto usługi Batch | 20 - 100 | 500<sup>4</sup> |

> [!NOTE]
> Domyślne limity zależą od typu Twojej subskrypcji, który umożliwia utworzenie konta usługi Batch. Limity przydziałów rdzeni, wyświetlane są przeznaczone dla konta usługi Batch w trybie usługi Batch. [Wyświetl przydziały na koncie usługi Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup> Liczba dedykowanych rdzeni na konto usługi Batch można zwiększyć, ale maksymalna liczba jest nieokreślona. Skontaktuj się z działem pomocy technicznej platformy Azure w celu omówienia możliwości zwiększenia.

<sup>2</sup> liczby rdzeni o niskim priorytecie na konto usługi Batch można zwiększyć, ale maksymalna liczba jest nieokreślona. Skontaktuj się z działem pomocy technicznej platformy Azure w celu omówienia możliwości zwiększenia.

<sup>3</sup> ukończone zadania i harmonogramy zadań nie są ograniczone.

<sup>4</sup> pomocy technicznej systemu Azure kontaktu, jeśli chcesz poprosić o zwiększenie wykraczające poza ten limit.
