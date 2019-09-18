---
title: Azure — limit wydatków | Microsoft Docs
description: W tym artykule pokazano, jak działa limit wydatków platformy Azure oraz jak go usunąć.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70114449"
---
# <a name="azure-spending-limit"></a>Azure — limit wydatków

Dzięki limitowi wydatków na platformie Azure nie można wydawać więcej niż wynosi kwota środków. Wszyscy nowi klienci, którzy rejestrują się w celu skorzystania z oferty wersji próbnej platformy Azure lub ofert obejmujących środki na wiele miesięcy, mają domyślnie włączony limit wydatków. Limit wydatków wynosi 0 USD i nie można go zmienić. Na przykład nie można zmienić limitu wydatków na 100 USD. Limit wydatków można jednak usunąć. Dlatego albo nie masz limitu lub obowiązuje limit równy zero, który uniemożliwia wykonanie większości rodzajów wydatków. Limit wydatków nie jest dostępny dla subskrypcji w ramach niektórych planów, takich jak plany zobowiązań i plany z cenami płatności zgodnie z rzeczywistym użyciem. Zobacz [pełną listę ofert platformy Azure i sprawdź dostępność limitu wydatków](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Osiąganie limitu wydatków

Jeśli opłaty za użycie przekroczą miesięczne kwoty określone w subskrypcji platformy Azure, wdrożone usługi zostaną wyłączone na pozostałą część okresu rozliczeniowego.

Jeśli na przykład wydasz wszystkie środki związane z subskrypcją, wdrożone zasoby platformy Azure zostaną usunięte z produkcji, a maszyny wirtualne platformy Azure zostaną zatrzymane i ich przydział zostanie cofnięty. Dane na kontach magazynu są dostępne tylko do odczytu.

Jeśli oferta subskrypcji obejmuje środki na wiele miesięcy, na początku kolejnego okresu rozliczeniowego subskrypcja zostanie ponownie włączona automatycznie. Następnie możesz ponownie wdrożyć zasoby platformy Azure i mieć pełny dostęp do kont magazynu oraz baz danych.

Platforma Azure wysyła powiadomienia e-mail, gdy limit wydatków dla subskrypcji zostanie osiągnięty. Zaloguj się do [Centrum konta](https://account.windowsazure.com/Subscriptions), aby zobaczyć powiadomienia dotyczące subskrypcji, w przypadku których osiągnięto limit wydatków.

Jeśli masz bezpłatną subskrypcję wersji próbnej i osiągniesz limit wydatków, możesz przeprowadzić uaktualnienie do planu z cenami typu [płatność zgodnie z rzeczywistym użyciem](billing-upgrade-azure-subscription.md), aby usunąć limit wydatków i automatycznie włączyć subskrypcję.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Usuwanie limitu wydatków w Centrum konta

Możesz usunąć limit wydatków w dowolnym momencie, o ile z subskrypcją platformy Azure została skojarzona prawidłowa forma płatności. W przypadku ofert obejmujących środki na wiele miesięcy możesz też włączyć limit wydatków na początku następnego okresu rozliczeniowego.

Aby usunąć limit wydatków, wykonaj następujące czynności:

1. Zaloguj się do [Centrum konta](https://account.windowsazure.com/Subscriptions).
1. Wybierz subskrypcję. Jeśli subskrypcja została wyłączona z powodu osiągnięcia limitu wydatków, kliknij powiadomienie: **Subskrypcja osiągnęła limit wydatków i została wyłączona, aby nie naliczono opłat**. W przeciwnym razie kliknij pozycję **Usuń limit wydatków** w obszarze **STAN SUBSKRYPCJI**.
1. Wybierz odpowiednią opcję.

![Wybieranie opcji usuwania limitu wydatków](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opcja | Efekt |
| --- | --- |
| Usuń trwale limit wydatków | Usuwa limit wydatków bez jego automatycznego ponownego włączenia na początku następnego okresu rozliczeniowego. |
| Usuń limit wydatków w bieżącym okresie rozliczeniowym | Usuwa limit wydatków, który jest automatycznie włączany ponownie na początku następnego okresu rozliczeniowego. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Dlaczego warto usunąć limit wydatków

Limit wydatków może uniemożliwić wdrażanie lub używanie niektórych usług innych firm i firmy Microsoft. Poniżej przedstawiono sytuacje, w których należy usunąć limit wydatków dla subskrypcji.

-  Planujesz wdrożyć obrazy firmy Microsoft, takie jak Oracle, i usługi, takie jak Azure DevOps Services. Ta sytuacja powoduje niemal natychmiastowe przekroczenie limitu wydatków i wyłączenie subskrypcji.
- Masz usługi, których działania nie chcesz zakłócać.
- Masz usługi i zasoby z ustawieniami, takimi jak wirtualne adresy IP, których nie chcesz utracić. Te ustawienia zostaną utracone w przypadku cofnięcia przydziałów usług i zasobów.

## <a name="turn-on-the-spending-limit-after-removing"></a>Włączanie limitu wydatków po usunięciu

Ta funkcja jest dostępna tylko wtedy, gdy limit wydatków został usunięty trwale. Zmień go, aby włączyć funkcję automatycznie na początku następnego okresu rozliczeniowego.

1. Zaloguj się do [Centrum konta](https://account.windowsazure.com/Subscriptions).
1. Kliknij żółty baner, aby zmienić opcję limitu wydatków.
1. Wybierz pozycję **Włącz limit wydatków w następnym okresie rozliczeniowym \<data rozpoczęcia okresu rozliczeniowego\>**

## <a name="custom-spending-limit"></a>Niestandardowy limit wydatków

Niestandardowe limity wydatków są niedostępne.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Limit wydatków nie uniemożliwia naliczania wszystkich opłat

[Niektórych usług zewnętrznych opublikowanych w witrynie Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) nie można używać razem ze środkami powiązanymi z subskrypcją — oddzielne opłaty mogą być naliczane nawet po ustawieniu limitu wydatków. Należą do nich licencje programu Visual Studio, usługa Azure Active Directory w warstwie Premium, plany pomocy technicznej i większość usług innych firm. W przypadku aprowizacji nowej usługi zewnętrznej zostanie wyświetlone ostrzeżenie informujące o tym, że usługi są rozliczane osobno:

![Ostrzeżenie dotyczące zakupu w witrynie Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Przeprowadź uaktualnienie do planu z cennikiem typu [płatność zgodnie z rzeczywistym użyciem](billing-upgrade-azure-subscription.md).
