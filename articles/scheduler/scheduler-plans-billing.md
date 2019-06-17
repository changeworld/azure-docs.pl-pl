---
title: Plany i rozliczenia — usługa Azure Scheduler
description: Dowiedz się więcej o planach i rozliczeń dla usługi Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 3a8664497d3d082ec1c7f584188854991e872d50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720433"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Plany i rozliczanie dla usługi Azure Scheduler

> [!IMPORTANT]
> Usługa [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która zostanie wycofana. Zamiast niej [spróbuj używać usługi Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) do planowania zadań. 

## <a name="job-collection-plans"></a>Plany kolekcji zadań

W usłudze Azure Scheduler kolekcja zadań zawiera określoną liczbę zadań. Kolekcja zadań jest płatna jednostka i jest dostępna w planach Standard, P10 Premium i P20 Premium, które są opisane poniżej: 

| Planu kolekcji zadań | Maksymalna liczba zadań na kolekcję | Maks. cykl | Maksymalna liczba kolekcji zadań na subskrypcję | Limits | 
|:--- |:--- |:--- |:--- |:--- |
| **Standardowa** | 50 zadań na kolekcję | Jeden na minutę. Nie można uruchomić zadania częściej niż jeden raz na minutę. | Każda subskrypcja platformy Azure może mieć maksymalnie 100 kolekcji zadań standardowych. | Dostęp do usługi Scheduler pełny zestaw funkcji | 
| **P10 Premium** | 50 zadań na kolekcję | Jeden na minutę. Nie można uruchomić zadania częściej niż jeden raz na minutę. | Każda subskrypcja platformy Azure może mieć maksymalnie 10 000 kolekcji zadań P10 Premium. Aby uzyskać więcej kolekcji <a href="mailto:wapteams@microsoft.com">skontaktuj się z nami</a>. | Dostęp do usługi Scheduler pełny zestaw funkcji |
| **P20 Premium** | 1000 zadań na kolekcję | Jeden na minutę. Nie można uruchomić zadania częściej niż jeden raz na minutę. | Każda subskrypcja platformy Azure może mieć maksymalnie 5000 kolekcji zadań P20 Premium. Aby uzyskać więcej kolekcji <a href="mailto:wapteams@microsoft.com">skontaktuj się z nami</a>. | Dostęp do usługi Scheduler pełny zestaw funkcji |
|||||| 

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [cennik usługi Scheduler](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Uaktualnić lub obniżyć jej poziom plany

W dowolnym momencie można uaktualnić lub obniżyć wersję planu kolekcji zadań w planach Standard, P10 Premium i P20 Premium.

## <a name="active-status-and-billing"></a>Stan aktywny i rozliczenia

Kolekcje zadań są zawsze aktywne, chyba że subskrypcji platformy Azure z całego przechodzi w stan tymczasowy wyłączone z powodu problemów z rozliczeniami. I mimo że można ją wyłączyć wszystkie zadania w kolekcji zadań, za pomocą jednej operacji i tej akcji nie zmienia stan rozliczeń kolekcji zadań, aby kolekcja zadań jest *nadal* rozliczane. Kolekcje zadań puste są traktowane jako aktywny i są rozliczane.

Aby upewnić się, że kolekcja zadań nie są naliczane, należy usunąć tę kolekcję zadań.

## <a name="standard-billable-units"></a>Standardowe jednostki do rozliczenia

Jednostka standardowego płatnych może mieć maksymalnie 10 kolekcji zadań standardowych. Ponieważ kolekcja zadań standardowych mogą mieć maksymalnie 50 zadań na kolekcję, co standardowa jednostka rozliczeniowa umożliwia Twojej subskrypcji platformy Azure, zawierać maksymalnie 500 zadań lub maksymalnie prawie 22 *mln* zadania do wykonania na miesiąc. Ta lista wyjaśniono, jak są naliczane za oparte na różne liczby kolekcji standardowych zadań:

* Jeśli masz zakresu od 1 do kolekcji 10 standardowych zadań naliczane standardowe jednostki rozliczeń. 

* Jeśli masz między 11 i kolekcje 20 standardowych zadań, naliczane dla dwóch standardowych jednostek rozliczeniowych. 

* Jeśli masz od 21 do kolekcji 30 standardowych zadań, opłaty są naliczane dla trzech standardowych jednostek rozliczeniowych i tak dalej.

## <a name="p10-premium-billable-units"></a>Jednostki do rozliczenia — wersja premium P10

P10 premium naliczaną na jednostkę płatną, może mieć maksymalnie 10 000 kolekcji zadań P10 Premium. Ponieważ kolekcja zadań P10 Premium mogą mieć maksymalnie 50 zadań na kolekcję, jedną jednostką rozliczeniową P10 premium umożliwia Twojej subskrypcji platformy Azure, zawierać maksymalnie 500 000 zadań lub maksymalnie prawie 22 *MLD* zadania do wykonania na miesiąc. 

Kolekcje zadań P10 Premium zapewniają takie same możliwości jak kolekcji standardowych zadań, ale oferują podziału ceny dla aplikacji, które wymagają wielu kolekcji zadań i zapewnia lepszą skalowalność. Ta lista wyjaśniono, jak są naliczane za oparte na różne liczby kolekcji zadań P10 Premium:

* Jeśli wynosi od 1 do 10 000 kolekcji zadań P10 Premium, naliczane dla jednej jednostki rozliczeń — wersja premium P10. 

* Jeśli masz między 10,001 i 20 000 kolekcji zadań P10 Premium opłaty są naliczane za 2 jednostki rozliczeń — wersja premium P10 i tak dalej.

## <a name="p20-premium-billable-units"></a>Jednostki do rozliczenia — wersja premium p20

Jednostka płatnych premium P20 może mieć maksymalnie 5000 kolekcji zadań P20 Premium. Ponieważ kolekcja zadań P20 Premium mogą mieć maksymalnie 1000 zadań na kolekcji zadań, co jednostka rozliczeniowa premium P20 umożliwia Twojej subskrypcji platformy Azure, mieć do 5 000 000 zadań lub do niemal 220 *MLD* zadania do wykonania na miesiąc.

Kolekcje zadań p20 Premium zapewnia te same możliwości jak kolekcje zadań P10 Premium, ale również obsługiwać większą liczbę zadań na kolekcję i większą łączną liczbę zadań ogólny niż Premium P10, zapewniając lepszą skalowalność.

## <a name="plan-comparison"></a>Porównanie planów

* Jeśli masz więcej niż 100 kolekcji zadań Standard (10 standardowych jednostek rozliczeniowych), można uzyskać lepszą ofertę przez wszystkie kolekcje zadań w ramach planu Premium.

* Jeśli masz jedną kolekcję standardowych zadań i jedna kolekcja zadań — wersja Premium, a następnie opłaty są naliczane standardowe jednostki rozliczeń *i* jednej jednostki do rozliczeń — wersja premium.

  Rachunki usługi Scheduler na podstawie liczby kolekcji aktywnego zadania, które są standardowa lub premium.

## <a name="see-also"></a>Zobacz także

* [Czym jest Azure Scheduler?](scheduler-intro.md)
* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)