---
title: Testowanie rozwiązania szablon oferty w portalu Marketplace | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przetestować ofertę szablonu rozwiązania w portalu Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: ef8f9b5e-b98c-49f3-913f-cdf772c14c12
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2015
ms.author: hascipio; v-divte
ms.openlocfilehash: da1fc4713fd1d832c7ba91226f72cbef63b241bc
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714829"
---
# <a name="test-your-solution-template-offer-in-staging"></a>Przetestować ofertę szablonu rozwiązania w środowisku tymczasowym
Wdrożenie przejściowe oznacza wdrożenie oferty prywatnej "piaskownicy", gdzie można przetestować i weryfikować jej funkcje przed wypchnięciem do środowiska produkcyjnego. Oferta pojawia się na etapie wdrażania przejściowego, tak jak do klienta, który wdrożył ją. Oferty muszą mieć certyfikat ma zostać wypchnięty do wdrażania przejściowego.

Po przemieszczeniu oferty można wyświetlać i przetestować oferty w [witryny Azure Portal](https://portal.azure.com/).

Wykonaj poniższe kroki w celu wypchnięcia oferty do wdrażania przejściowego i przetestować go w [witryny Azure Portal](https://portal.azure.com/):

1. Przejdź do [portalu wydawców](https://publish.windowsazure.com) > **szablony rozwiązań** kartę > oferty > **Publikuj** > **Wypychanie do przemieszczania**.
2. Podaj listę subskrypcji platformy Azure, których używasz do przetestowania oferty.
3. Zaloguj się do portalu Azure w wersji zapoznawczej, za pomocą Identyfikatora subskrypcji, które było używane w poprzednim kroku.
4. Wykonaj co najmniej jedną podczas testowania w portalu Azure w wersji zapoznawczej w punktach wymienione poniżej:
   * Upewnij się, że marketingowych zawartości pojawia się poprawnie w witrynie Azure Marketplace.
   * End-to-end wdrażanie topologii.
   * Wykonaj testowanie wydajności i testów obciążenia.
   * Upewnij się, że topologia działa zgodnie z najlepszych rozwiązań.

## <a name="next-steps"></a>Kolejne kroki
Jeśli jesteś zadowolony z wyników, a następnie możesz przejść do fazy publikowania oferta końcowego **kroku 4**: [wdrażanie oferty w portalu Marketplace](marketplace-publishing-push-to-production.md). W przeciwnym razie zmiany w ofercie i żądanie certyfikacji ponownie.

> [!NOTE]
> Marketing zmian zawartości certyfikacji nie jest wymagana.
> 
> 

Zobacz [wprowadzenie: jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md) przewodnik dotyczący wszystkie zadania wydawcy.

