---
title: Wyświetlanie stanu oferty w portalu Marketplace — portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Wyświetlanie stanu oferty na platformie Azure i usługi AppSource rynków przy użyciu portalu Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
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
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: bdec2d699e8448c8e2303dfbabcb4d176a9ca389
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729789"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Wyświetl stan publikowania ofert w portalu Azure Marketplace i AppSource

Po utworzeniu oferty, a zwłaszcza w procesie publikowania, możesz wyświetlić stan oferty w portalu Cloud Partner.  Ogólny stan publikowania jest dostępna w [ **oferuje wszystkie** ](../portal-tour/cpp-all-offers-page.md) i [ **zatwierdzenia** ](../portal-tour/cpp-approvals-page.md) strony portalu.  Jedną z następujących wskaźników stanu powinien być wyświetlany dla każdej oferty.  

|            Stan              |   Opis                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Oferta została utworzona, ale nie rozpoczął procesu publikowania.            |
| **Publikowanie w toku**        | Oferta pracuje w drodze kolejne kroki procesu publikowania.   |
| **Publikowanie nie powiodło się**             | Krytyczny problem został wykryty podczas sprawdzania poprawności lub przeglądu przez firmę Microsoft. |
| **Publikacja anulowana**           | Wydawca anulował oferty procesu publikowania.  Ten stan nie odmownej istniejące oferty w portalu marketplace. | 
| **Oczekiwanie na wydawcy Wyloguj się** | Oferta został zrecenzowany przez firmę Microsoft i teraz czeka ostatecznej weryfikacji przez wydawcę. |
| **Delisted**                   | Został usunięty wcześniej opublikowane oferty w portalu marketplace.      | 
|  |  |


## <a name="publishing-status-details"></a>Szczegóły stanu publikowania 

Więcej informacji na temat stanu oferty ponieważ przechodzi ona przez proces publikowania znajduje się w **stan** karcie **nowa oferta** strony.  Ta strona zawiera listę wszystkich kroków publikowania dla tego typu oferty.  *Należy pamiętać, że liczba i poznać konkretne kroki często różnią się między typów ofert.*  Na tej stronie wskazuje także wszelkie oczekujących problemy zgłoszone przez weryfikacji firmy Microsoft i przejrzyj kroki, które często wymagają akcji przez wydawcę przed kontynuowaniem procesu publikowania.  Na przykład na poniższej ilustracji przedstawiono **stan** kartę do nowej oferty maszyny wirtualnej. 

![Stan karty oferty maszyny Wirtualnej](./media/vm-offer-pub-steps1.png)

Następny przykład **stan** kartę Usługa doradcza przedstawiający zgłoszony błąd w ustawieniach zarządzania potencjalnymi klientami.  Ponieważ zarządzanie potencjalnymi klientami jest wymagana dla usługi doradcze, muszą zostać poprawione ten błąd, zanim będzie można kontynuować publikowanie.

![Stan karty błąd przedstawiający usługi doradcze](./media/consulting-service-error.png)

Stan końcowy przykład aplikacji platformy Azure pokazuje krytyczny problem przeglądu firmy Microsoft.  Zawiera ona popularnego łącza do elementu VSTS, który zawiera szczegółowe informacje o tym problemie przeglądu.  Aby uzyskać więcej informacji, zobacz [opublikować ofertę platformy Azure aplikacja](cpp-publish-offer.md).

![Karta Stan dla aplikacji platformy Azure, przedstawiający Przegląd problemu](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Kolejne kroki

Aby rozwiązać problemy dotyczące zaległych lub zaktualizuj ustawienia oferty, musisz mieć [zaktualizować oferty](./cpp-update-offer.md). 
