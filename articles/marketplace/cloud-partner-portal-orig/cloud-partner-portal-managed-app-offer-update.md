---
title: Aktualizowanie istniejącej oferty w portalu Azure Marketplace
description: Aktualizowanie istniejącej oferty w portalu Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 2bfa10441cf5531c9383527a21b033da26322b34
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073242"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Aktualizowanie istniejącej oferty w portalu Azure Marketplace 
==============================================

Istnieją różne rodzaje aktualizacje, które warto wykonać oferty gdy na żywo, na przykład:

1.  Dodano nowe \"pakietu\" do istniejących jednostek SKU
2.  Dodawanie nowych jednostek SKU do istniejącej oferty
3.  Aktualizowanie metadanych portalu marketplace oferty/jednostki SKU

Będzie Użyj portalu Cloud Partner, aby zaimplementować te zmiany, a następnie ponownie opublikować ofertę. W tym artykule przedstawiono różne aspekty aktualizowanie oferty aplikacji platformy Azure.

<a name="unpermitted-changes-to-azure-application-offer"></a>Oferty niedozwolonej zmian w aplikacji platformy Azure 
-----------------------------------------------

Istnieją atrybuty aplikacji na platformie Azure oferty/jednostki SKU, nie można zmodyfikować po oferty przechodzi na żywo w witrynie Azure Marketplace.

* Identyfikator oferty i identyfikatorze wydawcy, oferty.
* Identyfikator jednostki SKU istniejących jednostek SKU.
* Zaktualizuj pakiet, który został opublikowany.

<a name="adding-a-new-package-to-an-existing-sku"></a>Dodawanie nowego pakietu do istniejących jednostek SKU 
---------------------------------------

Wydawcy mogą chcieć dodać nową wersję pakietu, aby zaktualizować istniejący pakiet. To dodawanie może odbywać się przez przekazanie nowego pakietu z liczbą innej wersji.

1.  Zaloguj się do [portalu dla partnerów w chmurze](http://cloudpartner.azure.com)
2.  W **wszystkich ofert**, odnaleźć oferty, czy chcesz zaktualizować
3.  Na **jednostki SKU** formularza, kliknij w jednostce SKU kto\'pakiet s miał chcesz zaktualizować
4.  Kliknij pozycję \"nowy pakiet\" i podaj nową wersję
5.  Przekaż nowy plik zip zawierający plik zaktualizowany szablon
6.  Kliknij pozycję Publikuj, aby uruchamiał przepływu pracy publikowania ma nowe jednostki SKU emisji na żywo.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Dodawanie nowej jednostki SKU do istniejącej oferty
-------------------------------------

Istnieje możliwość udostępnienia nowej jednostki SKU istniejącej oferty przez następujące kroki:

1.  Zaloguj się do [portalu dla partnerów w chmurze](http://cloudpartner.azure.com)
2.  W **wszystkich ofert**, odnaleźć oferty, czy chcesz zaktualizować
3.  Na **jednostki SKU** formularza, kliknij pozycję Dodaj nowej jednostki SKU i podaj identyfikator jednostki SKU, w oknie podręcznym.
4.  Wykonaj pozostałe kroki zgodnie z określonym [tutaj](./cloud-partner-portal-managed-app-publish.md).
5.  Kliknij pozycję **Publikuj** umożliwiającą przepływu pracy publikowania ma nowe jednostki SKU emisji na żywo.

<a name="updating-offer-marketplace-metadata"></a>Aktualizowanie oferty z portalu Marketplace metadanych 
-----------------------------------

Masz scenariusze, w których należy zaktualizować metadane marketplace skojarzone z ofertą, takie jak aktualizowanie Twoje logo firmy, np. Wykonaj poniższe kroki.

1.  Zaloguj się do portalu Cloud Partner.
2.  W **wszystkich ofert**, odnaleźć oferty, czy chcesz zaktualizować
3.  Przejdź do portalu Marketplace tworzą i postępuj zgodnie z instrukcjami [tutaj](../cloud-partner-portal/azure-applications/cpp-marketplace-tab.md) wprowadzać żadnych zmian.
4.  Kliknij pozycję Publikuj, aby uruchamiał przepływu pracy publikowania zostały wprowadzone zmiany, przejdź na żywo.
