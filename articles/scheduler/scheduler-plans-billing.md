---
title: Plany i rozliczanie
description: Dowiedz się więcej o planach i rozliczeniach dla usługi Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898475"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Plany i rozliczenia dla harmonogramu platformy Azure

> [!IMPORTANT]
> [Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje harmonogram platformy Azure, który jest [wycofywany.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Aby kontynuować pracę z zadaniami skonfigurowane w harmonogramie, należy jak [najszybciej przeprowadzić migrację do usługi Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Harmonogram nie jest już dostępny w witrynie Azure portal, ale [interfejsy CMDLET interfejsu REST](/rest/api/scheduler) i [narzędzia cmdlet programu Azure Scheduler PowerShell](scheduler-powershell-reference.md) pozostają dostępne w tej chwili, dzięki czemu można zarządzać zadaniami i kolekcjami zadań.

## <a name="job-collection-plans"></a>Plany zbierania ofert pracy

W usłudze Azure Scheduler kolekcja zadań zawiera określoną liczbę zadań. Kolekcja zadań jest jednostką podlegającej rozliczaniu i jest dostępna w planach Standard, P10 Premium i P20 Premium, które są opisane poniżej: 

| Plan zbierania zadań | Maksymalna liczba zadań na kolekcję | Maksymalny cykl | Maksymalna liczba kolekcji zadań na subskrypcję | Limity | 
|:--- |:--- |:--- |:--- |:--- |
| **Standardowa** | 50 zadań na kolekcję | Jeden na minutę. Nie można uruchamiać zadań częściej niż jeden na minutę. | Każda subskrypcja platformy Azure może mieć maksymalnie 100 standardowych kolekcji zadań. | Dostęp do pełnego zestawu funkcji Harmonogram | 
| **P10 Premium** | 50 zadań na kolekcję | Jeden na minutę. Nie można uruchamiać zadań częściej niż jeden na minutę. | Każda subskrypcja platformy Azure może mieć do 10 000 kolekcji zadań P10 Premium. Aby uzyskać więcej kolekcji, <a href="mailto:wapteams@microsoft.com">Skontaktuj się z nami</a>. | Dostęp do pełnego zestawu funkcji Harmonogram |
| **P20 Premium** | 1000 zadań na kolekcję | Jeden na minutę. Nie można uruchamiać zadań częściej niż jeden na minutę. | Każda subskrypcja platformy Azure może mieć do 5000 kolekcji zadań P20 Premium. Aby uzyskać więcej kolekcji, <a href="mailto:wapteams@microsoft.com">Skontaktuj się z nami</a>. | Dostęp do pełnego zestawu funkcji Harmonogram |
|||||| 

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Ustalanie cen harmonogramu](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Uaktualnianie lub uaktualnianie planów na starszą wersję produktu

W dowolnym momencie można uaktualnić lub obniżyć plan zbierania zadań w planach Standard, P10 Premium i P20 Premium.

## <a name="active-status-and-billing"></a>Aktywny stan i rozliczenia

Kolekcje zadań są zawsze aktywne, chyba że cała subskrypcja platformy Azure przechodzi w tymczasowy stan wyłączony z powodu problemów z rozliczeniami. I chociaż można wyłączyć wszystkie zadania w kolekcji zadań za pomocą jednej operacji, ta akcja nie zmienia stanu rozliczeń kolekcji zadań, więc kolekcja zadań jest *nadal* rozliczana. Puste kolekcje zadań są uważane za aktywne i są rozliczane.

Aby upewnić się, że kolekcja zadań nie jest rozliczana, należy usunąć kolekcję zadań.

## <a name="standard-billable-units"></a>Standardowe jednostki rozliczane

Standardowa jednostka rozliczalna może mieć do 10 standardowych kolekcji zadań. Ponieważ kolekcja zadań standardowych może mieć maksymalnie 50 zadań na kolekcję, jedna standardowa jednostka rozliczeniowa umożliwia subskrypcji platformy Azure maksymalnie 500 zadań lub prawie 22 *miliony* wykonań zadań miesięcznie. Ta lista wyjaśnia, w jaki sposób są rozliczane na podstawie różnych numerów kolekcji zadań standardowych:

* Jeśli masz od 1 do 10 standardowych kolekcji zadań, naliczane są opłaty za jedną standardową jednostkę rozliczeniową. 

* Jeśli masz od 11 do 20 standardowych kolekcji zadań, naliczane są opłaty za dwie standardowe jednostki rozliczeniowe. 

* Jeśli masz od 21 do 30 standardowych kolekcji zadań, naliczane są opłaty za trzy standardowe jednostki rozliczeniowe itd.

## <a name="p10-premium-billable-units"></a>Jednostki premium premium do rozliczania

Jednostka premium premium do pobrania p10 może mieć do 10 000 kolekcji zadań P10 Premium. Ponieważ kolekcja zadań P10 Premium może mieć do 50 zadań na kolekcję, jedna jednostka rozliczeniowa P10 premium pozwala subskrypcji platformy Azure mieć do 500 000 zadań lub do prawie 22 *miliardów* wykonań zadań miesięcznie. 

Kolekcje zadań p10 Premium zapewniają takie same możliwości jak kolekcje zadań standardowych, ale oferują przerwę cenową dla aplikacji, które wymagają wielu kolekcji zadań i zapewniają większą skalowalność. Ta lista wyjaśnia, w jaki sposób są rozliczane na podstawie różnych numerów kolekcji zadań P10 Premium:

* Jeśli masz od 1 do 10 000 kolekcji zadań P10 Premium, naliczane są opłaty za jedną jednostkę rozliczeniową P10 premium. 

* Jeśli masz od 10 001 do 20 000 kolekcji zadań P10 Premium, naliczane są opłaty za 2 jednostki rozliczeniowe premium P10 itd.

## <a name="p20-premium-billable-units"></a>Jednostki premium premium do rozliczania

Jednostka premium p20 może mieć do 5000 kolekcji zadań P20 Premium. Ponieważ kolekcja zadań P20 Premium może mieć do 1000 zadań na kolekcję zadań, jedna jednostka rozliczeniowa P20 premium pozwala subskrypcji platformy Azure mieć do 5 000 000 zadań lub do prawie 220 *miliardów* wykonań zadań miesięcznie.

Kolekcje zadań P20 Premium zapewniają takie same możliwości jak kolekcje zadań P10 Premium, ale także obsługują większą liczbę zadań na kolekcję i większą całkowitą liczbę zadań ogółem niż P10 Premium, zapewniając większą skalowalność.

## <a name="plan-comparison"></a>Porównanie planu

* Jeśli masz więcej niż 100 standardowych kolekcji zadań (10 standardowych jednostek rozliczeniowych), możesz uzyskać lepszą ofertę, mając wszystkie kolekcje zadań w planie Premium.

* Jeśli masz jedną kolekcję zadań standardową i jedną kolekcję zadań Premium, naliczane są opłaty za jedną standardową jednostkę rozliczeniową *i* jedną jednostkę rozliczeniową premium.

  Rachunki usługi harmonogramu na podstawie liczby aktywnych kolekcji zadań, które są standardowe lub premium.

## <a name="next-steps"></a>Następne kroki

* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)