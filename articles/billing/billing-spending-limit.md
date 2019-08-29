---
title: Limit wydatków platformy Azure | Microsoft Docs
description: W tym artykule opisano, jak działa limit wydatków platformy Azure oraz jak go usunąć.
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
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114449"
---
# <a name="azure-spending-limit"></a>Limit wydatków platformy Azure

Limit wydatków na platformie Azure uniemożliwia korzystanie z wydatków w wysokości. Wszyscy nowi klienci, którzy zarejestrują się w celu korzystania z wersji próbnej platformy Azure lub ofert obejmujących kredyty w wielu miesiącach, mają domyślnie włączony limit wydatków. Limit wydatków wynosi $0 i nie można go zmienić. Na przykład nie można zmienić limitu wydatków na $100. Można jednak usunąć limit wydatków. W związku z tym nie masz limitu lub obowiązuje limit równy zero, który uniemożliwia wykonanie większości rodzajów wydatków. Limit wydatków nie jest dostępny dla subskrypcji w ramach niektórych planów, takich jak plany zobowiązań i plany z cenami płatnymi zgodnie z rzeczywistym użyciem. Zapoznaj się z [pełną listą ofert platformy Azure oraz dostępnością limitu wydatków](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Osiąganie limitu wydatków

Gdy użycie spowoduje naliczanie opłat za wyczerpanie miesięcznych kwot uwzględnionych w ramach subskrypcji platformy Azure, wdrożone usługi są wyłączone dla pozostałej części okresu rozliczeniowego.

Na przykład, gdy użytkownik spędza wszystkie środki związane z subskrypcją, wdrożone zasoby platformy Azure zostaną usunięte z produkcji, a maszyny wirtualne platformy Azure zostaną zatrzymane i cofnięte. Dane na kontach magazynu są dostępne tylko do odczytu.

Jeśli oferta subskrypcji obejmuje środki na początku kolejnego okresu rozliczeniowego, subskrypcja zostanie ponownie włączona automatycznie. Następnie możesz ponownie wdrożyć zasoby platformy Azure i mieć pełny dostęp do swoich kont magazynu i baz danych.

Na platformie Azure wysyłane są powiadomienia e-mail po osiągnięciu limitu wydatków dla subskrypcji. Zaloguj się do [centrum konta](https://account.windowsazure.com/Subscriptions) , aby wyświetlić powiadomienia dotyczące subskrypcji, w przypadku których osiągnięto limit wydatków.

Jeśli masz bezpłatną subskrypcję wersji próbnej i osiągniesz limit wydatków, możesz przeprowadzić uaktualnienie do planu z cennikiem z [opcją płatność zgodnie z rzeczywistym](billing-upgrade-azure-subscription.md) użyciem, aby usunąć limit wydatków i automatycznie włączyć subskrypcję.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Usuń limit wydatków w centrum konta

Limit wydatków można usunąć w dowolnej chwili, o ile istnieje ważna Metoda płatności skojarzona z subskrypcją platformy Azure. W przypadku ofert mających kredyt w wielu miesiącach można także włączyć limit wydatków na początku następnego okresu rozliczeniowego.

Aby usunąć limit wydatków, wykonaj następujące czynności:

1. Zaloguj się do [centrum konta](https://account.windowsazure.com/Subscriptions).
1. Wybierz subskrypcję. Jeśli subskrypcja jest wyłączona z powodu osiągnięcia limitu wydatków, kliknij powiadomienie: **Subskrypcja osiągnęła limit wydatków i została wyłączona, aby uniknąć naliczania opłat.** W przeciwnym razie kliknij pozycję **Usuń limit wydatków** w obszarze **stan subskrypcji** .
1. Wybierz odpowiednią opcję.

![Wybieranie opcji usuwania limitu wydatków](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opcja | Efekt |
| --- | --- |
| Usuń limit wydatków całkowicie | Usuwa limit wydatków bez jego automatycznego ponownego włączenia na początku następnego okresu rozliczeniowego. |
| Usuń limit wydatków w bieżącym okresie rozliczeniowym | Usuwa limit wydatków, który jest automatycznie włączany ponownie na początku następnego okresu rozliczeniowego. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Dlaczego warto usunąć limit wydatków

Limit wydatków może uniemożliwić wdrożenie lub użycie niektórych usług innych firm i firmy Microsoft. Poniżej wymieniono sytuacje, w których należy usunąć limit wydatków dla subskrypcji.

-  Planujesz wdrożyć obrazy z pierwszej strony, takie jak Oracle i usługi, takie jak Azure DevOps Services. Ta sytuacja powoduje, że przekroczenie limitu wydatków niemal natychmiast i spowoduje, że subskrypcja zostanie wyłączona.
- Masz usługi, których nie chcesz zakłócać.
- Masz usługi i zasoby z takimi ustawieniami jak wirtualne adresy IP, których nie chcesz utracić. Te ustawienia zostaną utracone po cofnięciu przydziału usług i zasobów.

## <a name="turn-on-the-spending-limit-after-removing"></a>Włącz limit wydatków po usunięciu

Ta funkcja jest dostępna tylko wtedy, gdy limit wydatków został usunięty przez czas nieokreślony. Zmień ją, aby włączyć ją automatycznie na początku następnego okresu rozliczeniowego.

1. Zaloguj się do [centrum konta](https://account.windowsazure.com/Subscriptions).
1. Kliknij żółty baner, aby zmienić opcję limit wydatków.
1. Wybierz pozycję **Włącz limit wydatków w następnym okresie \<rozliczeniowym dla okresu\> rozliczeniowego**

## <a name="custom-spending-limit"></a>Niestandardowy limit wydatków

Niestandardowe limity wydatków nie są dostępne.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Limit wydatków nie uniemożliwia wszystkich opłat

[Niektórych usług zewnętrznych opublikowanych w portalu Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) nie można używać wraz z kredytami subskrypcji i mogą być naliczane oddzielne opłaty nawet po ustawieniu limitu wydatków. Przykłady obejmują licencje programu Visual Studio, Azure Active Directory Premium, plany pomocy technicznej i większość usług innych firm. W przypadku aprowizacji nowej usługi zewnętrznej zostanie wyświetlone ostrzeżenie informujące o tym, że usługi są rozliczane osobno:

![Ostrzeżenie dotyczące zakupu w witrynie Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Przejdź do planu z cennikiem z [opcją płatność zgodnie z rzeczywistym](billing-upgrade-azure-subscription.md) użyciem.
