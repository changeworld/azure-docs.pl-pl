---
title: Plany i rozliczeń w harmonogramie Azure
description: Plany i rozliczeń w harmonogramie Azure
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: b25e97b0f0d0b6f63134a774856eb7ec8f77b679
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Plany i rozliczeń w harmonogramie Azure
## <a name="job-collection-plans"></a>Plany kolekcji zadań
Kolekcji zadań to płatna jednostka w harmonogramie Azure. Kolekcje zadań zawiera liczbę zadań i są dostępne w trzech planów — Standard, P10 Premium i P20 Premium — które są opisane poniżej.

| **Planu kolekcji zadań** | **Maksymalna liczba zadań na kolekcji zadań** | **Maksymalna liczba cyklu** | **Kolekcje maksymalna liczba zadań dla subskrypcji** | **Limity** |
|:--- |:--- |:--- |:--- |:--- |
| **Standardowa** |50 zadań na kolekcji zadań |Raz na minutę. Nie można wykonać zadania częściej niż raz na minutę |Subskrypcja jest dozwolone do 100 kolekcji zadań standardowych |Dostęp do wszystkich funkcji zestawu harmonogramu |
| **P10 Premium** |50 zadań na kolekcji zadań |Raz na minutę. Nie można wykonać zadania częściej niż raz na minutę |Subskrypcja jest dozwolone do 10 000 kolekcji zadań — wersja Premium P10. <a href="mailto:wapteams@microsoft.com">Skontaktuj się z nami</a> Aby uzyskać więcej informacji. |Dostęp do wszystkich funkcji zestawu harmonogramu |
| **P20 Premium** |1000 zadań na kolekcji zadań |Raz na minutę. Nie można wykonać zadania częściej niż raz na minutę |Subskrypcja jest dozwolone do 10 000 kolekcji zadań — wersja Premium P20. <a href="mailto:wapteams@microsoft.com">Skontaktuj się z nami</a> Aby uzyskać więcej informacji. |Dostęp do wszystkich funkcji zestawu harmonogramu |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Aktualizacje i zmiany na starszą wersję plany kolekcji zadań
Można uaktualnić lub starszą wersję planu kolekcji zadań w dowolnym momencie między planie Standard, P10 Premium i P20 Premium.

## <a name="billing-and-azure-plans"></a>Plany rozliczeń i Azure
Jeśli masz więcej niż 100 kolekcji zadań standardowe (10 rozliczeń jednostek standard), to lepszą transakcji, aby wszystkie kolekcje zadań w planie premium.

Jeśli masz jednej kolekcji zadań standardowa i premium jednej kolekcji zadań są rachunku standardowe rozliczeniowym jednostki *i* premium rozliczeniowym jednostki. Opłaty usługi harmonogramu, na podstawie liczby kolekcji aktywnego zadania, które są ustawione na standardowy lub premium; jest to wyjaśnić bardziej szczegółowo w dwóch następnych sekcjach.

## <a name="standard-billable-units"></a>Standardowe jednostki rozliczeń
Standardowe jednostki rozliczeniowy może zawierać maksymalnie 10 kolekcji standardowych zadań. Ponieważ kolekcja zadań standardowe może mieć maksymalnie 50 zadań na kolekcji zadań, standardowe jednostki rozliczeń umożliwia subskrypcję, aby mieć maksymalnie 500 zadania — do wykonania zadania prawie 22 milionów miesięcznie.

Jeśli masz zakresu od 1 do 10 kolekcji standardowych zadań, zostaną naliczone 1 standardowe jednostki rozliczeń. Jeśli masz między kolekcji standardowych zadań 11 i 20 zostaną naliczone 2 standard jednostek rozliczeń. Jeśli między 21 i kolekcje 30 zadań standardowy, zostaną naliczone 3 standard jednostek rozliczeń i tak dalej.

## <a name="p10-premium-billable-units"></a>P10 Jednostki rozliczeniowy — wersja Premium
Jednostka rozliczeniowy premium P10 może zawierać maksymalnie 10 000 kolekcji zadań — wersja premium P10. Ponieważ kolekcja zadań — wersja premium P10 może mieć maksymalnie 50 zadań na kolekcji zadań, jedną jednostkę rozliczeń premium umożliwia subskrypcję, aby mieć maksymalnie 500 000 zadania — do wykonania zadania prawie 22 MLD miesięcznie.

Jeśli masz zakresu od 1 do 10 000 kolekcji zadań — wersja premium, zostaną naliczone 1 jednostki rozliczeń — wersja premium P10. Jeśli masz zakresu od od 10 001 do 20 000 premium kolekcji zadań, zostaną naliczone 2 jednostek rozliczeń — wersja premium P10 i tak dalej.

W związku z tym kolekcji zadań — wersja premium P10 mają te same funkcje co kolekcji zadań standardowych, ale zawiera podział cen w przypadku, gdy aplikacja wymaga dużo kolekcji zadań.

## <a name="p20-premium-billable-units"></a>P20 Jednostki rozliczeniowy — wersja Premium
Jednostka rozliczeniowy premium P20 może zawierać maksymalnie 5000 kolekcji zadań — wersja premium P20. Ponieważ kolekcja zadań — wersja premium P20 może mieć maksymalnie 1000 zadań na kolekcji zadań, jedną jednostkę rozliczeń premium umożliwia subskrypcję, aby mieć maksymalnie 5,000,000 zadania — do wykonania zadania prawie 220 MLD miesięcznie.

Kolekcje zadań — wersja premium p20 zapewnia takie same możliwości jak kolekcji zadań — wersja premium P10, ale obsługuje również większa liczba zadań dla kolekcji zadań i większą łączną liczbę zadań ogólny niż premium P10, co umożliwia większą skalowalność.

## <a name="billing-and-active-status"></a>Stan rozliczeń i aktywne
Kolekcje zadań są zawsze aktywne, chyba że w niektórych tymczasowego stan wyłączone z powodu problemów z rozliczeniami przeszedł do całej subskrypcji. Jedynym sposobem, aby upewnić się, że kolekcja zadań nie jest on rozliczany jest albo ustawić ją na *wolne* planu lub usunąć kolekcji zadań.

Mimo że można wyłączyć wszystkich zadań w ramach kolekcji zadań, w ramach jednej operacji, nie zmienia rozliczeń stan kolekcji zadań — kolekcji zadań będzie *nadal* opłaty będą naliczane. Podobnie zadanie puste kolekcje są traktowane jako aktywne i będą naliczane.

## <a name="pricing"></a>Cennik
Aby uzyskać szczegółowe informacje o cenach, zobacz [cennik harmonogramu](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Zobacz też
 [Co to jest Scheduler?](scheduler-intro.md)

 [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)

 [Rozpoczynanie pracy z usługą Scheduler w witrynie Azure Portal](scheduler-get-started-portal.md)

 [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)

 [Wysoka dostępność i niezawodność usługi Azure Scheduler](scheduler-high-availability-reliability.md)

 [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Uwierzytelnianie połączeń wychodzących usługi Azure Scheduler](scheduler-outbound-authentication.md)

