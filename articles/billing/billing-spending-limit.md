---
title: Zrozumienie limit wydatków na platformie Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób działania usługi Azure limit wydatków i sposobie jego usunięcia
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6049e3614b63bfabee6721dcaa83008eb3306493
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Zrozumienie wydatków limit i sposobie jego usunięcia na platformie Azure

Limit wydatków w Azure istnieje, aby uniknąć wydatków za pośrednictwem kwota środki. Wszystkich nowych klientów, którzy konta w wersji próbnej lub oferty, które obejmuje środki na korzystanie z wielu miesiącach ma limit wydatków, domyślnie włączona. Limit wydatków jest $0. Nie można zmienić. Limit wydatków jest niedostępny na przykład dla subskrypcji korzystających z płatności zgodnie z rzeczywistym użyciem i planów zobowiązań. Zobacz [pełną listę oferty Azure i dostępność limit wydatków](https://azure.microsoft.com/support/legal/offer-details/).

**Szukasz rozliczeń alerty?** Zobacz [skonfigurować rozliczeń lub środki alerty dotyczące subskrypcji platformy Azure](billing-set-up-alerts.md).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Co się dzieje w przypadku osiągnięcia limitu wydatków?

Wyniki użycia w wyczerpanie zasobów miesięcznej opłaty uwzględnionych w subskrypcji, usług, które można wdrożyć są wyłączone w pozostałej części tego okresu rozliczeniowego. 

Na przykład gdy spędzają na wszystkich środków uwzględnionych w subskrypcji usługi w chmurze, które zostanie wdrożone są usuwane z produkcyjnym i maszynach wirtualnych platformy Azure są zatrzymane i zwalnia przydzielone. Dane konta magazynu i bazy danych są dostępne w sposób tylko do odczytu.

Na początku następnym okresie rozliczeniowym, jeśli ofertę subskrypcji zawiera środki na korzystanie z wielu miesiącach subskrypcji będzie ponownie włączona automatycznie. Następnie można ponownie wdrożyć usługi w chmurze i ma pełny dostęp do bazy danych i konta magazynu.

Możemy wysyłać powiadomienia e-mail, gdy osiągnęła limit wydatków dla subskrypcji. Zaloguj się do [Centrum konta](https://account.windowsazure.com/Subscriptions), i widzieć powiadomień dotyczących subskrypcji, które osiągnęły limit wydatków.

Jeśli korzystasz z bezpłatnej wersji próbnej i osiągnięcia limitu wydatków, możesz [uaktualnienia do płatność za rzeczywiste użycie](billing-upgrade-azure-subscription.md) Aby usunąć limit wydatków i automatycznie ponownie włączyć subskrypcję.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Usuń limit wydatków w Centrum konta

Możesz usunąć limit wydatków w dowolnym momencie, o ile z subskrypcją jest skojarzona prawidłowa metoda płatności. W przypadku ofert mających środki przez wiele miesięcy można także ponownie włączyć limit wydatków na początku z następnym okresie rozliczeniowym.

Aby usunąć limit wydatków, wykonaj następujące czynności:

1. Zaloguj się do [Centrum konta](https://account.windowsazure.com/Subscriptions).
1. Wybierz subskrypcję.
. Jeśli subskrypcja jest wyłączona z powodu osiągnięcia limitu wydatków, kliknij to powiadomienie: "Subskrypcja osiągnęła Limit wydatków i została wyłączona, aby uniknąć naliczania opłat." W przeciwnym razie kliknij przycisk **Usuń limit wydatków** w **stan SUBSKRYPCJI** obszaru.
1. Wybierz odpowiednią opcję.

![Opcja Usuń limit wydatków](./media/billing-spending-limit/remove-spending-limit.PNG)

|Opcja|Efekt|
|-------|-----|
|Usuń trwale limit wydatków|Usuwa limit wydatków bez jego automatycznego ponownego włączenia na początku następnego okresu rozliczeniowego.|
|Usuń limit wydatków w bieżącym okresie rozliczeniowym|Usuwa limit wydatków, który jest automatycznie włączany ponownie na początku następnego okresu rozliczeniowego.|

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Dlaczego chcesz usunąć limit wydatków?

Limit wydatków może uniemożliwiać wdrażania lub przy użyciu określonych innych firm i usług firmy Microsoft. Poniżej przedstawiono scenariusze, w których należy usunąć limit wydatków dla subskrypcji.

* Planujesz wdrożyć obrazy firmy Microsoft (np. Oracle) i usługi, takie jak Visual Studio Team Services. W tym scenariuszu powoduje przekroczenie limitu wydatków niemal natychmiast i powoduje, że Twoja subskrypcja jest wyłączona.
* Masz usługi, których działania nie można przerwać.
* Masz usługi i zasoby z takimi ustawieniami jak wirtualne adresy IP, których nie chcesz utracić. Te ustawienia zostaną utracone, jeśli usługi i zasoby są cofnąć przydziału.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Jak włączyć limit wydatków po usunięciu go?

Ta funkcja jest dostępna tylko wtedy, gdy limit wydatków został usunięty przez nieograniczony czas. Zmień, aby włączyć automatyczne na początku następnym okresie rozliczeniowym.

1. Zaloguj się do [Centrum konta](https://account.windowsazure.com/Subscriptions).
1. Kliknij żółty Baner, aby zmienić opcję limitu wydatków.
1. Wybierz **Włącz limit wydatków w następnym okresie rozliczeniowym \<rozliczeń okres Data rozpoczęcia\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Jak ustawić limit wydatków niestandardowych?

Nie mamy niestandardowe spędzają limity dzisiaj. Jednak można zgodzić się na [Użyj alerty dotyczące rozliczeń do formantu z spędzają](billing-set-up-alerts.md).

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>Limit wydatków uniemożliwia wszelkich opłat z platformy Azure?

[Niektóre usługi zewnętrzne opublikowane w portalu Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) nie można używać z Twojej kredytów subskrypcji i może spowodować naliczenie opłat oddzielne nawet wtedy, gdy ustawiono limit wydatków. Przykładami licencji programu Visual Studio, Azure Active Directory premium plany pomocy technicznej i większości firm marki usług. Podczas obsługi administracyjnej nowej usługi zewnętrzne jest wyświetlane ostrzeżenie z informacją, że usługi są rozliczane oddzielnie:

![Ostrzeżenie zakupu Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
