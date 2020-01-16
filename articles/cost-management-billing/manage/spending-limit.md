---
title: Azure — limit wydatków | Microsoft Docs
description: W tym artykule pokazano, jak działa limit wydatków platformy Azure oraz jak go usunąć.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: banders
ms.openlocfilehash: ddb906aeb8d1c2b0d1f0c97fc12bd389da431646
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989945"
---
# <a name="azure-spending-limit"></a>Azure — limit wydatków

Dzięki limitowi wydatków na platformie Azure nie można wydawać więcej niż wynosi kwota środków. Wszyscy nowi klienci, którzy rejestrują się w celu założenia bezpłatnego konta platformy Azure lub skorzystania z typów subskrypcji obejmujących środki na wiele miesięcy, mają domyślnie włączony limit wydatków. Limit wydatków jest równy kwocie środków i nie można go zmienić. Jeśli na przykład zarejestrujesz się w celu założenia bezpłatnego konta platformy Azure, Twój limit wydatków będzie wynosić 200 USD i nie będzie można go zmienić na 500 USD. Limit wydatków można jednak usunąć. Więc albo nie masz limitu, albo masz limit równy kwocie środków. Pozwala to uniknąć większości rodzajów wydatków. Limit wydatków nie jest dostępny dla subskrypcji z planami zobowiązań lub z cenami po stawkach płatności zgodnie z rzeczywistym użyciem. Zobacz [pełną listę typów subskrypcji platformy Azure i dostępność limitu wydatków](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Osiąganie limitu wydatków

Gdy użycie spowoduje naliczenie opłat wyczerpujących limit wydatków, wdrożone usługi zostaną wyłączone na pozostałą część danego okresu rozliczeniowego.

Jeśli na przykład wydasz wszystkie środki zawarte na bezpłatnym koncie platformy Azure, wdrożone zasoby platformy Azure zostaną usunięte ze środowiska produkcyjnego, a maszyny wirtualne platformy Azure zostaną zatrzymane i ich przydział zostanie cofnięty. Dane na kontach magazynu są dostępne tylko do odczytu.

Jeśli Twój typ subskrypcji obejmuje środki na wiele miesięcy, na początku kolejnego okresu rozliczeniowego subskrypcja zostanie ponownie włączona automatycznie. Następnie możesz ponownie wdrożyć zasoby platformy Azure i mieć pełny dostęp do kont magazynu oraz baz danych.

Platforma Azure wysyła powiadomienia e-mail po osiągnięciu limit wydatków. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), aby zobaczyć powiadomienia dotyczące subskrypcji, w przypadku których osiągnięto limit wydatków.

Jeśli masz bezpłatne konto platformy Azure i osiągniesz limit wydatków, możesz przejść na ceny [płatności zgodnie z rzeczywistym użyciem](upgrade-azure-subscription.md), aby usunąć limit wydatków i automatycznie włączyć ponownie subskrypcję.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-azure-portal"></a>Usuwanie limitu wydatków w witrynie Azure Portal

Możesz usunąć limit wydatków w dowolnym momencie, o ile z subskrypcją platformy Azure została skojarzona prawidłowa forma płatności. W przypadku typów subskrypcji ze środkami na wiele miesięcy, takich jak Visual Studio Enterprise i Visual Studio Professional, możesz również włączyć limit wydatków na początku następnego okresu rozliczeniowego.

Aby usunąć limit wydatków, wykonaj następujące czynności:

1. Zaloguj się do [portalu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję. Jeśli limit wydatków dla subskrypcji zostanie osiągnięty, zostanie ona wyłączona.
1. Wybierz pozycję **Usuń limit wydatków** w górnej części strony.
1. Wybierz odpowiednią opcję.

![Wybieranie opcji usuwania limitu wydatków](./media/spending-limit/remove-spending-limit01.png)

| Opcja | Efekt |
| --- | --- |
| Usuń trwale limit wydatków | Usuwa limit wydatków bez jego automatycznego ponownego włączenia na początku następnego okresu rozliczeniowego. |
| Usuń limit wydatków w bieżącym okresie rozliczeniowym | Usuwa limit wydatków, który jest automatycznie włączany ponownie na początku następnego okresu rozliczeniowego. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Dlaczego warto usunąć limit wydatków

Limit wydatków może uniemożliwić wdrażanie lub używanie niektórych usług innych firm i firmy Microsoft. Poniżej przedstawiono sytuacje, w których należy usunąć limit wydatków dla subskrypcji.

-  Planujesz wdrożyć obrazy firmy Microsoft, takie jak Oracle, lub usługi, takie jak Azure DevOps Services. Ta sytuacja powoduje niemal natychmiastowe osiągnięcie limitu wydatków i wyłączenie subskrypcji.
- Masz usługi, których działania nie chcesz zakłócać. Po osiągnięciu limitu wydatków wdrożone zasoby platformy Azure zostaną usunięte ze środowiska produkcyjnego, a maszyny wirtualne platformy Azure zostaną zatrzymane i zostanie cofnięty ich przydział. Jeśli masz usługi, których działania nie chcesz zakłócać, musisz usunąć limit wydatków.
- Masz usługi i zasoby z takimi ustawieniami jak wirtualne adresy IP, których nie chcesz utracić. Te ustawienia zostaną utracone, gdy osiągniesz limit wydatków, a przydział usługi i zasobów zostanie cofnięty.

## <a name="turn-on-the-spending-limit-after-removing"></a>Włączanie limitu wydatków po usunięciu

Ta funkcja jest dostępna tylko wtedy, gdy limit wydatków dla typów subskrypcji obejmujących środki na wiele miesięcy został usunięty na czas nieokreślony. Za pomocą tej funkcji można automatycznie włączyć limit wydatków na początku kolejnego okresu rozliczeniowego.

1. Zaloguj się do [Centrum konta](https://account.windowsazure.com/Subscriptions).
1. Kliknij żółty baner, aby zmienić opcję limitu wydatków.
1. Wybierz pozycję **Włącz limit wydatków w następnym okresie rozliczeniowym \<data rozpoczęcia okresu rozliczeniowego\>**

## <a name="custom-spending-limit"></a>Niestandardowy limit wydatków

Niestandardowe limity wydatków są niedostępne.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Limit wydatków nie uniemożliwia naliczania wszystkich opłat

[Niektórych usług zewnętrznych opublikowanych w witrynie Azure Marketplace](../understand/understand-azure-marketplace-charges.md) nie można używać razem ze środkami powiązanymi z subskrypcją — oddzielne opłaty mogą być naliczane nawet po ustawieniu limitu wydatków. Należą do nich licencje programu Visual Studio, usługa Azure Active Directory w warstwie Premium, plany pomocy technicznej i większość usług innych firm. W przypadku aprowizacji nowej usługi zewnętrznej zostanie wyświetlone ostrzeżenie informujące o tym, że usługi są rozliczane osobno:

![Ostrzeżenie dotyczące zakupu w witrynie Marketplace](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Przeprowadź uaktualnienie do planu z cennikiem typu [płatność zgodnie z rzeczywistym użyciem](upgrade-azure-subscription.md).
