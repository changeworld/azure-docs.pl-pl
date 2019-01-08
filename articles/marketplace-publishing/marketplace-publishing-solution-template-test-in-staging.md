---
title: Testowanie rozwiązania szablon oferty w portalu Marketplace | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przetestować ofertę szablonu rozwiązania w portalu Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
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
ROBOTS: NOINDEX
ms.openlocfilehash: 48c2befbf42f90c31d5631448e9d1276b23b965f
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078834"
---
# <a name="test-your-solution-template-offer-in-staging"></a>Przetestować ofertę szablonu rozwiązania w środowisku tymczasowym
Wdrożenie przejściowe oznacza wdrożenie oferty prywatnej "piaskownicy", gdzie można przetestować i weryfikować jej funkcje przed wypchnięciem do środowiska produkcyjnego. Oferta pojawia się na etapie wdrażania przejściowego, tak jak do klienta, który wdrożył ją. Oferta musi mieć certyfikat przed wypychania do wdrażania przejściowego.

Po przemieszczeniu oferty można wyświetlać i przetestować oferty w [witryny Azure portal](https://portal.azure.com/).

Wykonaj poniższe kroki w celu wypchnięcia oferty do wdrażania przejściowego i przetestować go w [witryny Azure portal](https://portal.azure.com/):

1. Przejdź do [portalu wydawców](https://publish.windowsazure.com) > **szablony rozwiązań** kartę > oferty > **Publikuj** > **Wypychanie do przemieszczania**.
2. Podaj listę subskrypcji platformy Azure, których używasz do przetestowania oferty.
3. Zaloguj się do witryny Azure portal przy użyciu Identyfikatora subskrypcji, które było używane w poprzednim kroku.
4. Wykonaj co najmniej jedną podczas testowania w witrynie Azure portal w punktach wymienione poniżej:
   * Upewnij się, że marketingowych zawartości pojawia się poprawnie w witrynie Azure Marketplace.
   * End-to-end wdrażanie topologii.
   * Wykonaj testowanie wydajności i testów obciążenia.
   * Upewnij się, że topologia działa zgodnie z najlepszych rozwiązań.

## <a name="next-steps"></a>Kolejne kroki
Jeśli jesteś zadowolony z wyników, a następnie możesz przejść do fazy publikowania oferta końcowego **kroku 4**:  [Wdrażanie oferty w portalu Marketplace](marketplace-publishing-push-to-production.md). W przeciwnym razie zmiany w ofercie i żądanie certyfikacji ponownie.

> [!NOTE]
> Marketing zmian zawartości certyfikacji nie jest wymagana.
> 
> 

Zobacz [wprowadzenie: Jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md) przewodnik dotyczący wszystkie zadania wydawcy.

