---
title: Wyświetlanie kont rozliczeniowych w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak wyświetlać konta rozliczeniowe w witrynie Azure Portal.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490216"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Wyświetlanie kont rozliczeniowych w witrynie Azure Portal  

Konto rozliczeniowe jest tworzone podczas rejestrowania się w celu korzystania z platformy Azure. Konta rozliczeniowego można używać do zarządzania fakturami i płatnościami oraz do śledzenia kosztów. Możesz mieć dostęp do wielu kont rozliczeniowych. Możesz na przykład zarejestrować się na platformie Azure, aby móc pracować nad projektami osobistymi. Dostęp jest również możliwy w ramach zawartej przez organizację umowy Enterprise Agreement lub Umowy klienta firmy Microsoft. W każdym z tych scenariuszy używane jest oddzielne konto rozliczeniowe.

W witrynie Azure Portal są obecnie obsługiwane następujące typy kont rozliczeniowych:

- **Microsoft Online Services Program**: Konto rozliczeniowe dla programu Microsoft Online Services Program jest tworzone podczas rejestrowania się na platformie Azure za pomocą witryny internetowej Azure. Na przykład po zarejestrowaniu się w celu uzyskania [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Umowa Enterprise Agreement**: Konto rozliczeniowe dla umowy Enterprise Agreement jest tworzone, gdy organizacja podpisuje [umowę Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) w celu korzystania z platformy Azure.

- **Umowa klienta firmy Microsoft**: Konto rozliczeniowe dla umowy klienta firmy Microsoft jest tworzone, gdy organizacja współpracuje z przedstawicielem firmy Microsoft w celu podpisania umowy klienta firmy Microsoft. Niektórzy klienci w wybranych regionach, którzy zarejestrują się za pomocą witryny internetowej Azure w celu [utworzenia konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub dokonają podwyższenia poziomu swojego [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), mogą mieć również konto rozliczeniowe do umowy klienta firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z kontem rozliczeniowym do umowy klienta firmy Microsoft](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Zakresy kont rozliczeniowych
Zakres to węzeł w ramach konta rozliczeniowego, za pomocą którego użytkownicy wyświetlają rozliczenia i zarządzają nimi. Jest to miejsce, w którym użytkownicy zarządzają danymi rozliczeniowymi, płatnościami i fakturami oraz przeprowadzają ogólne czynności dotyczące zarządzania kontami. 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe     | Reprezentuje pojedynczego właściciela (administratora konta) co najmniej jednej subskrypcji platformy Azure. Administrator konta jest autoryzowany do wykonywania różnych zadań rozliczeniowych, takich jak tworzenie subskrypcji, wyświetlanie faktur lub zmiana rozliczeń dla subskrypcji.  |
|Subskrypcja     |  Reprezentuje zgrupowanie zasobów platformy Azure. W tym zakresie jest generowana faktura. Ma swoje formy płatności za faktury.|


### <a name="enterprise-agreement"></a>Enterprise Agreement

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe    | Reprezentuje rejestrację umów Enterprise Agreement. W tym zakresie jest generowana faktura. W skład jego struktury wchodzą działy i konta rejestracji.  |
|Dział     |  Opcjonalne zgrupowanie kont rejestracji.      |
|Konto rejestracji     |  Reprezentuje pojedynczego właściciela konta. W tym zakresie są tworzone subskrypcje platformy Azure.  |


### <a name="microsoft-customer-agreement"></a>Umowa klienta firmy Microsoft

|Zakres  |Zadania  |
|---------|---------|
|Konto billingowe     |   Reprezentuje umowę klienta dla wielu produktów i usług firmy Microsoft. W skład jego struktury wchodzą profile rozliczeniowe i sekcje faktur.   |
|Profil rozliczeniowy     |  Reprezentuje fakturę i formy płatności za nią. W tym zakresie jest generowana faktura. Może zawierać wiele sekcji faktur.      |
|Sekcja faktury     |   Reprezentuje grupę kosztów na fakturze. Z tym zakresem są skojarzone subskrypcje i inne zakupy.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Przełączanie zakresu rozliczeniowego w witrynie Azure Portal


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Po lewej stronie wybierz pozycję **Wszystkie zakresy rozliczeń**.

   ![Zrzut ekranu przedstawiający wszystkie zakresy rozliczeniowe](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Pozycja **Wszystkie zakresy rozliczeń** nie jest widoczna, jeśli masz dostęp tylko do jednego zakresu.

4. Wybierz zakres, aby wyświetlić szczegóły.



## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak rozpocząć [analizowanie kosztów](../cost-management/quick-acm-cost-analysis.md).