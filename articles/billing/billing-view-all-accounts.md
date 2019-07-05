---
title: Wyświetl konta rozliczeniowego w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlać konta rozliczeniowego w witrynie Azure portal.
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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490216"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Wyświetlić konta rozliczeniowego w witrynie Azure portal  

Konto rozliczeniowe powstaje po zarejestrowaniu się za pomocą platformy Azure. Użyj konta rozliczeniowego na fakturach płatności, zarządzania i śledzenie kosztów. Masz dostęp do wielu kont rozliczeń. Na przykład może być zarejestrowaniu się na platformie Azure dla Twoich projektów osobistych. Może również mieć dostęp za pośrednictwem umowy Enterprise Agreement lub umowy klienta firmy Microsoft Twojej organizacji. Dla każdego z tych scenariuszy trzeba oddzielnego konta rozliczeniowego.

Azure portal obsługuje obecnie następującego typu konta rozliczeniowego:

- **Microsoft Online Services Program**: Konto rozliczeniowe dla programu Microsoft Online Services powstaje po zarejestrowaniu się na platformie Azure za pośrednictwem witryny internetowej platformy Azure. Na przykład podczas rejestrowania się w celu [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [konta przy użyciu stawki](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Umowa Enterprise Agreement**: Konto rozliczeniowe dla umowy Enterprise Agreement jest tworzony, gdy organizacja rejestruje [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) za pomocą platformy Azure.

- **Umowy klienta Microsoft**: Konto rozliczeniowe dla umowy klienta firmy Microsoft jest tworzony, podczas Twoja organizacja pracuje się z przedstawicielem firmy Microsoft do podpisania umowy klienta firmy Microsoft. Niektórzy klienci w wybranych regionach, którzy utworzą konto przy użyciu witrynie internetowej platformy Azure dla [konta przy użyciu stawki](https://azure.microsoft.com/offers/ms-azr-0003p/) lub uaktualnić ich [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) może mieć konta rozliczeniowego dla Customer firmy Microsoft Umowa dotycząca także. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie korzystania z konta rozliczeniowego dla umowy klienta Microsoft](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Zakresy dla kont rozliczeniowych
Zakres jest węzłem w ramach konta rozliczeniowego, używanego przez użytkowników do wyświetlania i zarządzania rozliczeniami. Gdzie użytkownicy Zarządzanie danych dotyczących rozliczeń, płatności, faktury i postępowania Zarządzanie kontem ogólnego jest. 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Scope  |Definicja  |
|---------|---------|
|Konto billingowe     | Reprezentuje pojedynczy właściciela (konto administrator) dla co najmniej jedną subskrypcję platformy Azure. Administrator konta jest autoryzowany do wykonywania różnych zadań rozliczeń, jak tworzyć subskrypcje, wyświetlanie faktur lub zmienić rozliczeń dla subskrypcji.  |
|Subskrypcja     |  Reprezentuje grupę zasobów platformy Azure. Faktura jest generowany w tym zakresie. Ma swoje własne metody płatności, które są używane do płacenia jego faktury.|


### <a name="enterprise-agreement"></a>Enterprise Agreement

|Scope  |Definicja  |
|---------|---------|
|Konto billingowe    | Reprezentuje umowy Enterprise Agreement. Faktura jest generowany w tym zakresie. Struktury, przy użyciu konta rejestracji i działów.  |
|Dział     |  Opcjonalnie grupa kont rejestracji.      |
|Konto rejestracji     |  Reprezentuje właściciela jednego konta. Subskrypcje platformy Azure są tworzone w ramach tego zakresu.  |


### <a name="microsoft-customer-agreement"></a>Umowa klienta firmy Microsoft

|Scope  |Zadania  |
|---------|---------|
|Konto billingowe     |   Reprezentuje umowy klienta w wielu usług i produktów firmy Microsoft. Struktury, przy użyciu profilów rozliczeń i sekcje faktury.   |
|Karta profilu     |  Reprezentuje faktury i jego metody płatności. Faktura jest generowany w tym zakresie. Może mieć wiele sekcji faktury.      |
|Sekcja faktury     |   Reprezentuje grupę kosztów w faktury. Subskrypcje i innych zakupy są skojarzone z tego zakresu.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Przełącz zakres rozliczeń w witrynie Azure portal


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Wybierz **wszystkie zakresy rozliczeń** z po lewej stronie.

   ![Zrzut ekranu przedstawiający wszystkie zakresy rozliczeń](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Nie będzie mógł przeglądać **wszystkie zakresy rozliczeń** Jeśli masz tylko dostęp do jednego zakresu.

4. Wybierz zakres, aby wyświetlić szczegóły.



## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak uruchomić [analizując koszty](../cost-management/quick-acm-cost-analysis.md).