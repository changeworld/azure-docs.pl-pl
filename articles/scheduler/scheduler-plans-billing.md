---
title: Plany i rozliczenia w usłudze Azure Scheduler
description: Plany i rozliczenia w usłudze Azure Scheduler
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
ms.openlocfilehash: 03f335634b7ce1fe4aa6251d6ec21922ed9b84c8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887491"
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Plany i rozliczenia w usłudze Azure Scheduler
## <a name="job-collection-plans"></a>Plany kolekcji zadań
Kolekcje zadań to płatne jednostki w usłudze Azure Scheduler. Kolekcje zadań zawierają liczbę zadań i są dostępne w trzech planów — Standard, P10 Premium i P20 Premium —, które są opisane poniżej.

| **Planu kolekcji zadań** | **Maksymalna liczba zadań na kolekcję zadań** | **Maks. cykl** | **Kolekcje zadań Max na subskrypcję** | **Limity** |
|:--- |:--- |:--- |:--- |:--- |
| **Standardowa** |50 zadań na kolekcję zadań |Raz na minutę. Nie można wykonać zadania częściej niż raz na minutę |Subskrypcja jest dozwolony do 100 kolekcji zadań standardowe |Dostęp do pełny zestaw funkcji usługi Scheduler |
| **P10 Premium** |50 zadań na kolekcję zadań |Raz na minutę. Nie można wykonać zadania częściej niż raz na minutę |Subskrypcja jest dozwolony do 10 000 kolekcji zadań P10 Premium. <a href="mailto:wapteams@microsoft.com">Skontaktuj się z nami</a> Aby uzyskać więcej informacji. |Dostęp do pełny zestaw funkcji usługi Scheduler |
| **P20 Premium** |1000 zadań na kolekcję zadań |Raz na minutę. Nie można wykonać zadania częściej niż raz na minutę |Subskrypcja jest dozwolony do 10 000 kolekcji zadań P20 Premium. <a href="mailto:wapteams@microsoft.com">Skontaktuj się z nami</a> Aby uzyskać więcej informacji. |Dostęp do pełny zestaw funkcji usługi Scheduler |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Uaktualnienia i zmiany na starszą wersję plany kolekcji zadań
Może uaktualnić lub obniżyć wersję planu kolekcji zadań w dowolnym czasie między planami Standard, P10 Premium i P20 Premium.

## <a name="billing-and-azure-plans"></a>Pomoc dotycząca rozliczeń i Azure plany
Jeśli masz więcej niż 100 kolekcji zadań standard (10 standardowych jednostek rozliczeniowych), to lepszą ofertę, aby wszystkie kolekcje zadań w plan w warstwie premium.

Jeśli masz jedną kolekcję zadań standard i premium jednej kolekcji zadań są rozliczane co standardowa jednostka rozliczeniowa *i* jednej jednostki do rozliczeń — wersja premium. W ramach usługi Scheduler na podstawie liczby kolekcji zadań, które są ustawione na standardowy lub premium; jest to wyjaśnione bardziej szczegółowo w dwóch następnych sekcjach.

## <a name="standard-billable-units"></a>Standardowe jednostki do rozliczenia
Standardowa naliczaną na jednostkę płatną może zawierać maksymalnie 10 kolekcji zadań standardowych. Ponieważ kolekcja zadań standardowych mogą mieć maksymalnie 50 zadań na kolekcji zadań, co standardowa jednostka rozliczeniowa umożliwia subskrypcji można mieć maksymalnie 500 zadania — maksymalnie prawie 22 milionom osób wykonań zadań miesięcznie.

Jeśli między 1 a 10 kolekcji zadań standardowa, opłata jest naliczana dla jednego standardowa jednostka rozliczeniowa. Jeśli masz między 11 i kolekcje 20 standardowych zadań, opłata jest naliczana dla dwóch standardowych jednostek rozliczeniowych. Jeśli od 21 do kolekcji 30 zadań standardowa, opłata jest naliczana dla trzech standardowych jednostek rozliczeniowych i tak dalej.

## <a name="p10-premium-billable-units"></a>P10 Jednostki do rozliczenia — wersja Premium
P10 premium naliczaną na jednostkę płatną, może zawierać maksymalnie 10 000 kolekcji zadań — wersja premium P10. Ponieważ kolekcja zadań P10 premium mogą mieć maksymalnie 50 zadań na kolekcji zadań, co jednostka rozliczeniowa — wersja premium umożliwia subskrypcji można mieć maksymalnie 500 000 zadania — maksymalnie prawie 22 MLD wykonań zadań miesięcznie.

Jeśli wynosi od 1 do 10 000 kolekcji zadań — wersja premium, opłata jest naliczana dla jednej jednostki rozliczeń — wersja premium P10. Jeśli masz między 10,001 i premium 20 000 kolekcji zadań, zostanie naliczona opłata za 2 jednostki rozliczeń — wersja premium P10 i tak dalej.

W związku z tym kolekcje zadań w warstwie premium P10 mają taką samą funkcjonalność jak kolekcji standardowych zadań, ale zapewnia podział cen, w przypadku, gdy aplikacja wymaga wielu kolekcji zadań.

## <a name="p20-premium-billable-units"></a>P20 Jednostki do rozliczenia — wersja Premium
Jednostka płatnych premium P20 może zawierać maksymalnie 5000 kolekcji zadań — wersja premium P20. Ponieważ kolekcja zadań premium P20 mogą mieć maksymalnie 1000 zadań na kolekcji zadań, co jednostka rozliczeniowa — wersja premium umożliwia subskrypcji można mieć maksymalnie 5 000 000 zadania — maksymalnie prawie 220 MLD wykonań zadań miesięcznie.

Kolekcje zadań w warstwie premium p20 zapewnia te same możliwości jak kolekcje zadań w warstwie premium P10, ale obsługuje również większa liczba zadań na kolekcji zadań i większą łączną liczbę zadań ogólny niż premium P10, co umożliwia większą skalowalność.

## <a name="billing-and-active-status"></a>Stan rozliczeń i aktywne
Kolekcje zadań są zawsze aktywne, chyba że cały subskrypcji włożono w niektórych tymczasowa wyłączone z powodu problemów z rozliczeniami. Jest jedynym sposobem, aby upewnić się, że kolekcja zadań nie są naliczane za można usunąć kolekcji zadań.

Mimo że można wyłączyć wszystkie zadania w obrębie kolekcji zadań w ramach jednej operacji, nie powoduje zmiany rozliczeń stan kolekcji zadań — kolekcja zadań będzie *nadal* jest naliczana. Podobnie kolekcji zadań puste są traktowane jako aktywny i będą naliczane.

## <a name="pricing"></a>Cennik
Aby uzyskać szczegółowe informacje o cenach, zobacz [cennik usługi Scheduler](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Zobacz też
 [Co to jest Scheduler?](scheduler-intro.md)

 [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)

 [Rozpoczynanie pracy z usługą Scheduler w witrynie Azure Portal](scheduler-get-started-portal.md)

 [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)

 [Wysoka dostępność i niezawodność usługi Azure Scheduler](scheduler-high-availability-reliability.md)

 [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Uwierzytelnianie połączeń wychodzących usługi Azure Scheduler](scheduler-outbound-authentication.md)

