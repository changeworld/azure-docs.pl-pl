---
title: Wyświetlanie kont rozliczeniowych w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak wyświetlać konta rozliczeniowe w witrynie Azure Portal.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 2768d6e146a37e86bb36353f661179ebd7b5033d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994131"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Konta rozliczeń i zakresy w Azure Portal

Konto rozliczeniowe jest tworzone podczas rejestrowania się w celu korzystania z platformy Azure. Konta rozliczeniowego można używać do zarządzania fakturami i płatnościami oraz do śledzenia kosztów. Możesz mieć dostęp do wielu kont rozliczeniowych. Możesz na przykład zarejestrować się na platformie Azure, aby móc pracować nad projektami osobistymi. Dostęp jest również możliwy w ramach zawartej przez organizację umowy Enterprise Agreement lub Umowy klienta firmy Microsoft. W każdym z tych scenariuszy używane jest oddzielne konto rozliczeniowe.

Azure Portal obsługuje następujący typ kont rozliczeń:

- **Program Microsoft Online Services**: konto rozliczeniowe dla programu Microsoft Online Services jest tworzone podczas tworzenia konta na platformie Azure za pomocą witryny sieci Web systemu Azure. Na przykład po zarejestrowaniu się w celu uzyskania [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Umowa Enterprise**: konto rozliczeniowe dla Umowa Enterprise jest tworzone, gdy organizacja podpisuje [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) w celu korzystania z platformy Azure.

- **Umowa klienta firmy Microsoft**: konto rozliczeniowe dla umowy klienta firmy Microsoft jest tworzone, gdy organizacja współpracuje z przedstawicielem firmy Microsoft w celu podpisania umowy klienta firmy Microsoft. Niektórzy klienci w wybranych regionach, którzy zarejestrują się za pomocą witryny sieci Web systemu Azure w celu [uwzględnienia stawek płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) , mogą również mieć konto rozliczeniowe dla umowy klienta firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z kontem rozliczeniowym do umowy klienta firmy Microsoft](../understand/mca-overview.md).

- **Umowa partnerska firmy Microsoft**: konto rozliczeniowe dla umowy partnerskiej firmy Microsoft jest tworzone dla partnerów dostawcy rozwiązań w chmurze (CSP) do zarządzania klientami w nowym środowisku handlowym. Partnerzy muszą mieć co najmniej jednego klienta z [planem platformy Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) , aby zarządzać kontem rozliczeń w Azure Portal. Aby uzyskać więcej informacji, zobacz Rozpoczynanie pracy [z kontem rozliczeniowym w umowie partnerskiej firmy Microsoft](../understand/mpa-overview.md).

Aby określić typ swojego konta rozliczeniowego, zobacz [Sprawdzanie typu konta rozliczeniowego](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Zakresy kont rozliczeniowych
Zakres to węzeł w ramach konta rozliczeniowego, który służy do wyświetlania rozliczeń i zarządzania nimi. Jest to miejsce, w którym można zarządzać danymi rozliczeń, płatnościami, fakturami i przeprowadzać ogólne zarządzanie kontami.

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

 ![Zrzut ekranu przedstawiający hierarchię MOSP](./media/view-all-accounts/mosp-hierarchy.png)

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe     | Reprezentuje umowę zaakceptowaną przez klienta do korzystania z platformy Azure. Zawiera ona co najmniej jedną subskrypcję.  |
|Subskrypcja     |  Reprezentuje zgrupowanie zasobów platformy Azure. W tym zakresie jest generowana faktura. Inne informacje dotyczące rozliczeń, takie jak metody płatności i adresy użycia, są skojarzone z tym zakresem.|

### <a name="enterprise-agreement"></a>Umowa Enterprise

![Zrzut ekranu przedstawiający hierarchię EA](./media/view-all-accounts/ea-hierarchy.png)

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe    | Reprezentuje rejestrację umów Enterprise Agreement. Zawiera co najmniej jeden dział i konta. W tym zakresie jest generowana faktura. |
|Dział     |  Opcjonalne grupowanie kont w celu segmentacji kosztów na logiczne grupowanie i ustawienie budżetu.     |
|Konto     |  Reprezentuje pojedynczego właściciela konta. Właściciele kont mają uprawnienia do tworzenia subskrypcji platformy Azure, które są rozliczane na potrzeby rejestracji i zarządzania nimi. |

### <a name="microsoft-customer-agreement"></a>Umowa klienta firmy Microsoft

![Zrzut ekranu przedstawiający hierarchię MCA](./media/view-all-accounts/mca-hierarchy.png)

|Zakres  |Zadania  |
|---------|---------|
|Konto billingowe     |   Reprezentuje umowę zaakceptowaną przez klienta do korzystania z produktów i usług firmy Microsoft. Zawiera co najmniej jeden profil rozliczania. |
|Profil rozliczeniowy     |   Reprezentuje fakturę i powiązane informacje rozliczeniowe, takie jak formy płatności i adres rozliczeniowy. Zawiera co najmniej jedną sekcję faktury. |
|Sekcja faktury     |   Reprezentuje grupowanie kosztów na fakturze. Subskrypcje platformy Azure i inne zakupy, takie jak Azure Marketplace i produkty źródłowe aplikacji, są skojarzone z tym zakresem.    |

### <a name="microsoft-partner-agreement"></a>Umowa partnerska firmy Microsoft

![Zrzut ekranu, który pokazuje hierarchię MPA](./media/view-all-accounts/mpa-hierarchy.png)

|Zakres  |Zadania  |
|---------|---------|
|Konto billingowe     |   Reprezentuje umowę partnerskią w celu zarządzania produktami i usługami firmy Microsoft w nowym środowisku handlowym. Zawiera co najmniej jeden profil rozliczeń i klientów.   |
|Profil rozliczeniowy     |   Reprezentuje fakturę dla waluty.     |
|Klient    |   Reprezentuje klienta dla partnera dostawcy rozwiązań w chmurze (CSP).  Subskrypcje platformy Azure i inne zakupy, takie jak Azure Marketplace i produkty źródłowe aplikacji, są skojarzone z tym zakresem.  |
|Odsprzedawca    |   Odsprzedawca, który udostępnia klientom usługi. Jest to opcjonalne pole dla subskrypcji i ma zastosowanie tylko do dostawców pośrednich w modelu dwuwarstwowej dostawcy CSP.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Przełączanie zakresu rozliczeniowego w witrynie Azure Portal

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Na stronie Przegląd wybierz pozycję **Przełącz zakres**.

   ![Zrzut ekranu przedstawiający zakresy rozliczeń](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Nie widzisz zakresu przełączania, jeśli masz tylko dostęp do jednego zakresu.

4. Wybierz zakres, aby wyświetlić szczegóły.

   ![Zrzut ekranu przedstawiający zakresy rozliczeń](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Sprawdź typ konta
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak rozpocząć [analizowanie kosztów](../costs/quick-acm-cost-analysis.md).
