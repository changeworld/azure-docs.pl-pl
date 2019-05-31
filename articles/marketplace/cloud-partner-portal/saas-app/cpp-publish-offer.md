---
title: Publikowanie oferty aplikacji SaaS usługi Azure | Portal Azure Marketplace
description: Publikowanie procesów i kroki dla aplikacji SaaS oferty w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 5574a7ba216eed42896d6fcb1890585f76561834
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258084"
---
# <a name="publish-a-saas-application-offer"></a>Publikowanie oferty aplikacji SaaS

Po utworzeniu nowej oferty, podając informacje o **nowa oferta** strony, możesz opublikować ofertę. Wybierz **Publikuj** aby rozpocząć proces publikowania.

> [!IMPORTANT] 
> SaaS oferują funkcje jest przeprowadzana migracja do [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Wszyscy wydawcy nowe należy użyć Centrum partnerskiego, do tworzenia nowych ofert SaaS i zarządzania istniejące oferty.  Bieżący wydawców z oferty SaaS są batchwise migrowane z portalu Cloud Partner do Centrum partnerskiego.  Cloud Partner Portal spowoduje wyświetlenie komunikatów o stanie, aby wskazać, kiedy zostały poddane migracji określonych istniejące oferty.
> Aby uzyskać więcej informacji, zobacz [Tworzenie nowej oferty SaaS](../../partner-center-portal/create-new-saas-offer.md).


Na poniższym diagramie przedstawiono ogólne kroki publikowania nowej oferty aplikacji SaaS.

![Kroki publikowania oferty](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

W poniższej tabeli opisano każdy krok publikowania, za pomocą szacowany czas (maksimum) do wykonania poszczególnych kroków.

|     **Step**       |     **czas**      |  **Opis**  |
|  ---------------   |  ---------------  |  ---------------  |
|         Certyfikacja           |       2 tygodnie            |          Oferty są analizowane przez zespół certyfikacji platformy Azure. Ten krok przeprowadzi skanowanie wirusów, złośliwe oprogramowanie, bezpieczeństwa, zgodności i problemy z zabezpieczeniami. Zostanie również sprawdzić, czy ta oferta spełnia wszystkie kryteria kwalifikacyjne (zobacz [wymagania wstępne](./cpp-prerequisites.md)). Ile opinii jest, że problem zostanie znaleziony.         |
|           Tworzenie pakietów         |       1 godzina            |       Zasoby techniczne oferty są pakowane do użytku klienta i systemy potencjalnych klientów są konfigurowane i konfiguracji.            |
|        Logowania wydawcy wyłączone            |         -          |        Wydawcy ostateczny Przegląd i potwierdzenie oferty przejdzie na żywo. Sprawdź, czy spełnia wszystkie wymagania dotyczące można wdrożyć swoją ofertę w wybranych subskrypcjach (w krokach informacji oferty). Wybierz **Go Live** dzięki ofercie przejściem do następnego kroku.           |
|        Tworzenie pakietów            |        1 godzina           |        Ukończone oferty jest replikowana w regionach i systemów produkcyjnych w portalu marketplace.           |
|        Na żywo            |       4 dni            |         Oferta jest zwolniony, replikowane do określonych regionów wymagane i udostępnione publicznie.          |

Zezwalaj na maksymalnie 10 dni roboczych. proces publikowania zakończyć i udostępnieniu oferty. Po zakończeniu procesu publikowania oferty SaaS zostaną wyświetlone w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Kolejne kroki

[Aktualizowanie istniejącej oferty](./cpp-update-existing-offer.md)
