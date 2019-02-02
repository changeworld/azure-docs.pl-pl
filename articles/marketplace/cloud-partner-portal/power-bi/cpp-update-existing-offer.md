---
title: Aktualizowanie istniejącej oferty aplikacji Power BI — portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Aktualizowanie oferty aplikacji Power BI po opublikowaniu w witrynie Marketplace AppSource firmy Microsoft.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 635e2e71bb952aaee761df6a1d5d87c46db531f6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666709"
---
# <a name="update-an-existing-power-bi-app-offer"></a>Aktualizowanie istniejącej oferty aplikacji Power BI

Ten artykuł przeprowadzi Cię przez różne aspekty aktualizowanie oferty aplikacji Power BI [portalu Cloud Partner](https://cloudpartner.azure.com/) i ponowne opublikowanie oferty.  Jest teraz powszechnie stosowane powodów aktualizacji oferty, w tym:

- Aktualizowanie zawartości aplikacji w usłudze Power BI i pobieranie nowego adresu URL instalacji z nowo spakowanych aplikacji
- Aktualizowanie metadanych witryny marketplace dla oferty: sprzedaży, marketingu, lub informacje pomocy technicznej i zasoby
 
Aby pomóc w tych zmian, oferuje portalu **porównania** i **historii** funkcji.


## <a name="unpermitted-changes-to-offer"></a>Niedozwolonej zmiany oferty

Istnieją pewne atrybuty oferty aplikacji Power BI, którego nie można zmodyfikować po oferty na żywo w usłudze AppSource, głównie **identyfikatora oferty Offer ID** i **Identyfikatora wydawcy**.


## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Chociaż istnieje szereg właściwości, które można zmienić w ramach oferty aplikacji Power BI, następujące operacje są wspólne.


### <a name="update-app-content-in-power-bi"></a>Aktualizacja zawartości aplikacji w usłudze Power BI

Jest to częsty problem w aplikacji w usłudze Power BI, aby okresowo aktualizowana o nową zawartość, poprawek zabezpieczeń, dodatkowe funkcje i tak dalej. W takich przypadkach chcesz zaktualizować adres URL do nowej instalacji zawartości aplikacji wykonując następujące kroki:

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2.  W obszarze **wszystkich ofert**, Znajdź ofertę zaktualizowania.
3.  W **informacje techniczne** wprowadź nowy adres URL Instalatora.
4.  Kliknij pozycję **Publikuj** można uruchomić przepływu pracy, aby opublikować wersję nowej aplikacji do usługi AppSource.


### <a name="update-offer-marketplace-metadata"></a>Zaktualizowanie metadanych portalu marketplace oferty

Wykonaj następujące kroki w celu zaktualizowania metadanych marketplace — firmy, nazwy, logo, itd. — skojarzone z ofertą:

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2.  W obszarze **wszystkich ofert**, odnaleźć oferty, czy chcesz zaktualizować.
3.  Przejdź do **szczegóły Storefront** kartę, a następnie postępuj zgodnie z instrukcjami w [kartę usługi Power BI aplikacje Storefront szczegóły](./cpp-storefront-details-tab.md) zmian metadanych.
4.  Kliknij pozycję **Publikuj** można uruchomić przepływu pracy, aby opublikować zmiany.


## <a name="compare-feature"></a>Porównanie funkcji

Po wprowadzeniu zmian w ramach oferty już opublikowane, możesz użyć **porównania** funkcji inspekcji zmian, które zostały wprowadzone. Aby użyć tej funkcji:

1.  W dowolnym momencie procesu edycji, kliknij przycisk **porównania** przycisk oferty.

    ![Porównanie funkcji przycisku](./media/compare-feature-button.png)

2.  Wyświetl wersje side-by-side marketingu zasoby i metadanych.


## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Aby wyświetlić wszystkie działań historycznych w publikacji, kliknij **historii** kartę na pasku menu nawigacji po lewej stronie, z portalu Cloud Partner. W tym miejscu można wyświetlić oznaczony sygnaturą czasową akcje, które zostały podjęte w okresie istnienia Twoich ofert w usłudze AppSource.


## <a name="next-steps"></a>Kolejne kroki

Należy regularnie używa [Insights sprzedawcy](../../cloud-partner-portal-orig/si-getting-started.md) funkcji [portalu Cloud Partner](https://cloudpartner.azure.com/#insights) udostępnienie szczegółowych informacji w portalu marketplace klienci i użycia.  
