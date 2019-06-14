---
title: Omówienie limitu wydatków na platformie Azure | Dokumentacja firmy Microsoft
description: Opisuje, jak działa platforma Azure limit wydatków i sposobach jego usuwania
services: ''
documentationcenter: ''
author: genlin
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: c234f75ec752ec79e9f2a051f79e91bdde81de15
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371028"
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Zrozumienie limicie wydatków i sposobach jego usuwania

Istnieje limit wydatków w usłudze Azure, aby uniknąć wydatków za pośrednictwem wynikające z kwoty kredytu. Wszyscy nowi klienci, którzy utworzą konto w wersji próbnej lub ofert obejmujących środki przez wiele miesięcy mają limit wydatków, domyślnie włączona. Limit wydatków wynosi 0 USD. Nie można zmienić. Limit wydatków jest niedostępny na przykład dla subskrypcji korzystających z płatności zgodnie z rzeczywistym użyciem i planów zobowiązań. Zobacz [pełną listę ofert platformy Azure i dostępność limitu wydatków](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Co się dzieje w przypadku osiągnięcia limitu wydatków?

Wyniki użycia Naliczanie opłat, które wyczerpać miesięczne kwoty uwzględnione w ramach subskrypcji, wdrożone usługi są wyłączone w pozostałej części tego okresu rozliczeniowego. 

Na przykład gdy poświęcany wszystkie środki, które są dołączone do subskrypcji usług w chmurze, które zostanie wdrożone są usuwane ze środowiska produkcyjnego i maszynach wirtualnych platformy Azure są zatrzymane i ponownie alokowane. Dane na kontach usługi storage są dostępne w sposób tylko do odczytu.

Na początku następnego okresu rozliczeniowego, jeśli oferta Subskrypcja obejmuje środki przez wiele miesięcy Twoja subskrypcja będzie ponownie włączona automatycznie. Następnie można ponownie wdrożyć swoje usługi w chmurze i mają pełny dostęp do swoich kont magazynu i baz danych.

Po osiągnięciu limitu wydatków dla subskrypcji, wysyłanie powiadomień e-mail. Zaloguj się do [Centrum kont](https://account.windowsazure.com/Subscriptions), wyświetlić powiadomienia dotyczące subskrypcji, dla których osiągnięto limit wydatków.

Jeśli korzystasz z bezpłatnej wersji próbnej i osiągnięcia limitu wydatków, możesz to zrobić [Przeprowadź uaktualnienie do płatności](billing-upgrade-azure-subscription.md) Usuń limit wydatków i subskrypcja będzie automatycznie włączona ponownie dostępna.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Usuń limit wydatków w Centrum konta

Możesz usunąć limit wydatków w dowolnym momencie, o ile z subskrypcją jest skojarzona prawidłowa metoda płatności. W przypadku ofert obejmujących środki przez wiele miesięcy możesz też ponownie włączyć limit wydatków na początku kolejnego okresu rozliczeniowego.

Aby usunąć limit wydatków, wykonaj następujące czynności:

1. Zaloguj się do [Centrum kont](https://account.windowsazure.com/Subscriptions).
1. Wybierz subskrypcję. Jeśli subskrypcja została wyłączona z powodu osiągnięcia limitu wydatków, kliknij to powiadomienie: „Subskrypcja osiągnęła limit wydatków i została wyłączona, aby nie naliczono opłat”. W przeciwnym razie kliknij przycisk **Usuń limit wydatków** w **stan SUBSKRYPCJI** obszaru.
1. Wybierz odpowiednią opcję.

![Opcja Usuń limit wydatków](./media/billing-spending-limit/remove-spending-limit.PNG)

|Opcja|Efekt|
|-------|-----|
|Usuń trwale limit wydatków|Usuwa limit wydatków bez jego automatycznego ponownego włączenia na początku następnego okresu rozliczeniowego.|
|Usuń limit wydatków w bieżącym okresie rozliczeniowym|Usuwa limit wydatków, który jest automatycznie włączany ponownie na początku następnego okresu rozliczeniowego.|

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Dlaczego chcesz usunąć limit wydatków?

Limit wydatków może uniemożliwić wdrażanie lub używanie niektórych usług firmy Microsoft i innych podmiotów. Poniżej przedstawiono scenariusze, w których należy usunąć limit wydatków dla subskrypcji.

* Planujesz wdrożyć obrazy firmy Microsoft, takimi jak Oracle i usług, takich jak usługom DevOps platformy Azure. W tym scenariuszu powoduje przekroczenie limitu wydatków niemal natychmiast i powoduje, że Twoja subskrypcja jest wyłączona.
* Masz usługi, których działania nie można przerwać.
* Masz usługi i zasoby z takimi ustawieniami jak wirtualne adresy IP, których nie chcesz utracić. Te ustawienia zostaną utracone w przypadku usług i zasobów dezalokowany.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Jak włączyć limit wydatków po usunięciu go?

Ta funkcja jest dostępna tylko wtedy, gdy limit wydatków został usunięty przez czas nieokreślony. Zmień ją na automatyczne włączenie na początku następnego okresu rozliczeniowego.

1. Zaloguj się do [Centrum kont](https://account.windowsazure.com/Subscriptions).
1. Kliknij przycisk żółty transparent, aby zmienić opcję limitu wydatków.
1. Wybierz **włączyć funkcję limit wydatków w następnym okresie rozliczeniowym \<okres rozliczeniowy Data rozpoczęcia\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Jak ustawić niestandardowe limitu wydatków?

Limity wydatków niestandardowe nie są dostępne.

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>Limit wydatków uniemożliwia wszystkie opłaty z platformy Azure?

[Niektóre usługi zewnętrzne opublikowane w witrynie Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) nie można używać z kredytów subskrypcji i mogą być naliczane osobne opłaty nawet wtedy, gdy limit wydatków jest ustawiona. Przykłady obejmują licencji na program Visual Studio, usługi Azure Active Directory premium, plany pomocy technicznej i większość firm usług. Podczas aprowizacji nowej usługi zewnętrznej, zostanie wyświetlone ostrzeżenie z informacją, że usługi są rozliczane oddzielnie:

![Portal Marketplace zakupu ostrzeżenie](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
