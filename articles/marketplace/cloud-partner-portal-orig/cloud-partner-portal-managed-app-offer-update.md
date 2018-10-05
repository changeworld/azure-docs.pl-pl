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
ms.openlocfilehash: 5633392bdf1293ee9196fafe67cf901e0d8c8014
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810409"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Aktualizowanie istniejącej oferty w portalu Azure Marketplace 
==============================================

Istnieją różne rodzaje aktualizacje, które warto wykonać oferty gdy na żywo.

1.  Dodano nowe \"pakietu\" do istniejących jednostek SKU
2.  Dodawanie nowych jednostek SKU do istniejącej oferty
3.  Aktualizowanie metadanych portalu marketplace oferty/jednostki SKU

Musisz zaktualizować swoje oferty w portalu Cloud Partner i ponownie opublikuj. W tym artykule przedstawiono różne aspekty aktualizowanie oferty aplikacji na platformie Azure.

<a name="unpermitted-changes-to-azure-application-offersku"></a>Niedozwolonej zmian aplikacji na platformie Azure, oferty/jednostki SKU 
--------------------------------------------------

Istnieją atrybuty aplikacji na platformie Azure oferty/jednostki SKU, nie można zmodyfikować po oferty przechodzi na żywo w witrynie Azure Marketplace.

1.  Identyfikator oferty i identyfikatorze wydawcy, oferty.
2.  Identyfikator jednostki SKU istniejących jednostek SKU.
3.  Zaktualizuj pakiet, który został opublikowany.

<a name="adding-a-new-package-to-an-existing-sku"></a>Dodawanie nowego pakietu do istniejących jednostek SKU 
---------------------------------------

Wydawcy mogą chcieć dodać nową wersję pakietu, aby zaktualizować istniejący pakiet. Można to zrobić przez przekazanie nowego pakietu z liczbą innej wersji.

1.  Zaloguj się do [portalu dla partnerów w chmurze](http://cloudpartner.azure.com)
2.  Wszystkie oferty zawiera oferty, które Twoim zdaniem zaktualizować
3.  W formularzu jednostek SKU, kliknij w jednostce SKU kto\'pakiet s miał chcesz zaktualizować
4.  Kliknij pozycję \"nowy pakiet\" i podaj nową wersję
5.  Przekaż nowy plik zip zawierający plik zaktualizowany szablon
6.  Kliknij pozycję Publikuj, aby uruchamiał przepływu pracy publikowania ma nowe jednostki SKU emisji na żywo.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Dodawanie nowej jednostki SKU do istniejącej oferty
-------------------------------------

Istnieje możliwość udostępnienia nowej jednostki SKU istniejącej oferty. Aby je włączyć, wykonaj poniższe kroki.

1.  Zaloguj się do [portalu dla partnerów w chmurze](http://cloudpartner.azure.com)
2.  Wszystkie oferty zawiera oferty, które Twoim zdaniem zaktualizować
3.  O jednostkach SKU tworzą clik na dodawanie nowej jednostki SKU i podaj identyfikator jednostki SKU, w oknie podręcznym.
4.  Wykonaj pozostałe kroki zgodnie z określonym [tutaj](./cloud-partner-portal-managed-app-publish.md).
5.  Kliknij pozycję Publikuj, aby uruchamiał przepływu pracy publikowania ma nowe jednostki SKU emisji na żywo.

<a name="updating-offer-marketplace-metadata"></a>Aktualizowanie oferty z portalu Marketplace metadanych 
-----------------------------------

Masz scenariusze, w których należy zaktualizować metadane marketplace skojarzone z ofertą, takie jak aktualizowanie Twoje logo firmy, np. Wykonaj poniższe kroki.

1.  Zaloguj się do portalu Cloud Partner
2.  Wszystkie oferty zawiera oferty, które Twoim zdaniem zaktualizować
3.  Przejdź do portalu Marketplace tworzą i postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-push-to-staging) wprowadzać żadnych zmian.
4.  Kliknij pozycję Publikuj, aby uruchamiał przepływu pracy publikowania zostały wprowadzone zmiany, przejdź na żywo.
