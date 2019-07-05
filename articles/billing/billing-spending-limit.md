---
title: Limit wydatków na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak działa limit wydatków platformy Azure i jak go usunąć.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fb43f29827309fc8986ee6b4653f5edf303cc21d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490608"
---
# <a name="azure-spending-limit"></a>Limit wydatków na platformie Azure

Limit wydatków na platformie Azure zapobiega wydatki wynikające z kwoty kredytu. Wszyscy nowi klienci, którzy utworzą konto w wersji próbnej platformy Azure lub ofert obejmujących środki przez wiele miesięcy mają limit wydatków, domyślnie włączona. Limit wydatków wynosi 0 USD. Nie można zmienić. Limit wydatków jest niedostępna dla niektórych subskrypcji plany takich planów zobowiązań i planów z zgodnie z rzeczywistym użyciem. Zobacz [pełną listę ofert platformy Azure i dostępność limitu wydatków](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Osiągnięcia limitu wydatków

Wyniki użycia Naliczanie opłat, które wyczerpać miesięczne kwoty uwzględnione w ramach subskrypcji platformy Azure wdrożone usługi są wyłączone w pozostałej części tego okresu rozliczeniowego.

Na przykład gdy poświęcany wszystkie środki, które są dołączone do subskrypcji zasobów platformy Azure wdrożone są usuwane ze środowiska produkcyjnego i maszynach wirtualnych platformy Azure są zatrzymane i ponownie alokowane. Dane na kontach usługi storage są dostępne jako tylko do odczytu.

Na początku następnego okresu rozliczeniowego, jeśli oferta Subskrypcja obejmuje środki przez wiele miesięcy Twoja subskrypcja jest ponownie włączana automatycznie. Następnie można ponownie wdrożyć swoje zasoby platformy Azure i mają pełny dostęp do swoich kont magazynu i baz danych.

Platforma Azure wysyła wiadomość e-mail, które powiadomienia w przypadku osiągnięcia wydatków ograniczenia dla Twojej subskrypcji. Zaloguj się do [Centrum kont](https://account.windowsazure.com/Subscriptions) powiadomienia dotyczące subskrypcji, dla których osiągnięto limit wydatków.

Jeśli korzystasz z bezpłatnej subskrypcji próbnej i osiągnięcia limitu wydatków, możesz uaktualnić plan z [płatność za rzeczywiste użycie](billing-upgrade-azure-subscription.md) ceny, aby usunąć wydatków ograniczyć i automatyczne włączenie subskrypcji.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Usuń limit wydatków w Centrum konta

Tak długo, jak długo ma prawidłową metodą płatności skojarzonej z subskrypcją platformy Azure, możesz usunąć limit wydatków w dowolnym momencie. W przypadku ofert obejmujących środki przez wiele miesięcy można również włączyć limit wydatków na początku kolejnego okresu rozliczeniowego.

Aby usunąć limit wydatków, wykonaj następujące czynności:

1. Zaloguj się do [Centrum kont](https://account.windowsazure.com/Subscriptions).
1. Wybierz subskrypcję. Jeśli subskrypcja jest wyłączona z powodu osiągnięcia limitu wydatków, kliknij powiadomienie: **Subskrypcja osiągnęła Limit wydatków i została wyłączona, aby nie naliczono opłat.** W przeciwnym razie kliknij przycisk **Usuń limit wydatków** w **stan SUBSKRYPCJI** obszaru.
1. Wybierz odpowiednią opcję.

![Opcja Usuń limit wydatków](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opcja | Efekt |
| --- | --- |
| Usuń trwale limit wydatków | Usuwa limit wydatków bez jego automatycznego ponownego włączenia na początku następnego okresu rozliczeniowego. |
| Usuń limit wydatków w bieżącym okresie rozliczeniowym | Usuwa limit wydatków, który jest automatycznie włączany ponownie na początku następnego okresu rozliczeniowego. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Dlaczego warto usunąć limit wydatków

Limit wydatków może uniemożliwić wdrażanie lub używanie niektórych usług firmy Microsoft i innych podmiotów. W tym miejscu są sytuacje, w których należy usunąć limit wydatków w ramach subskrypcji.

-  Planujesz wdrożyć obrazy firmy Microsoft, takimi jak Oracle i usług, takich jak usługom DevOps platformy Azure. Taka sytuacja powoduje przekroczenie limitu wydatków niemal natychmiast i powoduje, że Twoja subskrypcja jest wyłączona.
- Masz usługi, które nie mają zakłócona.
- Masz usługi i zasoby z takimi ustawieniami jak wirtualne adresy IP, których nie chcesz utracić. Te ustawienia zostaną utracone w przypadku usług i zasobów dezalokowany.

## <a name="turn-on-the-spending-limit-after-removing"></a>Włącz limit wydatków po usunięciu

Ta funkcja jest dostępna tylko wtedy, gdy limit wydatków został usunięty przez czas nieokreślony. Zmień ją na automatyczne włączenie na początku następnego okresu rozliczeniowego.

1. Zaloguj się do [Centrum kont](https://account.windowsazure.com/Subscriptions).
1. Kliknij przycisk żółty transparent, aby zmienić opcję limitu wydatków.
1. Wybierz **włączyć funkcję limit wydatków w następnym okresie rozliczeniowym \<okres rozliczeniowy Data rozpoczęcia\>**

## <a name="custom-spending-limit"></a>Niestandardowe limit wydatków

Limity wydatków niestandardowe nie są dostępne.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Limit wydatków nie uniemożliwia wszystkie opłaty

[Niektóre usługi zewnętrzne opublikowane w witrynie Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) nie można używać z kredytów subskrypcji i mogą być naliczane osobne opłaty nawet wtedy, gdy limit wydatków jest ustawiona. Przykłady obejmują licencji na program Visual Studio, usługi Azure Active Directory premium, plany pomocy technicznej i większość firm usług. Podczas aprowizacji nowej usługi zewnętrznej, zostanie wyświetlone ostrzeżenie z informacją, że usługi są rozliczane oddzielnie:

![Portal Marketplace zakupu ostrzeżenie](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki
- Przeprowadź uaktualnienie do planu z [płatność za rzeczywiste użycie](billing-upgrade-azure-subscription.md) ceny.
