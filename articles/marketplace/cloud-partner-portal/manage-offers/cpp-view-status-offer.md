---
title: Wyświetlanie stanu oferty w witrynie marketplace | Portal Azure Marketplace
description: Wyświetlanie stanu oferty na platformie Azure i usługi AppSource rynków przy użyciu portalu Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: fff89dd8a17aaf6d45462edeaa22f1d2efc8d02b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064316"
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

Stan końcowy przykład aplikacji platformy Azure pokazuje krytyczny problem przeglądu firmy Microsoft.  Zawiera ona popularne łącze do elementu DevOps platformy Azure, który zawiera szczegółowe informacje o tym problemie przeglądu.  Aby uzyskać więcej informacji, zobacz [opublikować ofertę platformy Azure aplikacja](cpp-publish-offer.md).

![Karta Stan dla aplikacji platformy Azure, przedstawiający Przegląd problemu](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Kolejne kroki

Aby rozwiązać problemy dotyczące zaległych lub zaktualizuj ustawienia oferty, musisz mieć [zaktualizować oferty](./cpp-update-offer.md). 
