---
title: Wyświetlanie stanu ofert marketplace | Azure Marketplace
description: Wyświetlanie stanu ofert na platformie Azure i w witrynach AppSource Marketplaces przy użyciu portalu cloud partnerów
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 0cbe6a45ba205f32a764bdadb021dc0dcf5bf0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275972"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Wyświetlanie stanu publikowania ofert w portalu Azure Marketplace i AppSource

Po utworzeniu oferty, a zwłaszcza podczas procesu publikowania, można wyświetlić stan oferty w portalu Cloud Partner.  Ogólny stan publikowania jest dostępny na stronach [**Wszystkie oferty**](../portal-tour/cpp-all-offers-page.md) i [**zatwierdzenia**](../portal-tour/cpp-approvals-page.md) portalu.  Dla każdej oferty powinien być wyświetlany jeden z następujących wskaźników stanu.  

|            Stan              |   Opis                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Oferta została utworzona, ale proces publikowania nie został rozpoczęty.            |
| **Publikowanie w toku**        | Oferta pracuje nad etapami procesu publikowania.   |
| **Publikowanie nie powiodło się**             | Podczas sprawdzania poprawności lub przeglądu przez firmę Microsoft wykryto krytyczny problem. |
| **Publikowanie anulowane**           | Wydawca anulował proces publikowania oferty.  Ten stan nie usuwa istniejącej oferty na rynku. | 
| **Oczekiwanie na wylogowanie się wydawcy** | Oferta została sprawdzona przez firmę Microsoft, a teraz czeka na ostateczną weryfikację przez wydawcę. |
| **Wycofany z listy**                   | Wcześniej opublikowana oferta na rynku została usunięta.      | 
|  |  |


## <a name="publishing-status-details"></a>Szczegóły stanu publikowania 

Więcej szczegółów na temat stanu oferty w trakcie procesu publikowania można znaleźć na karcie **Stan** na stronie **Nowa oferta.**  Ta strona zawiera listę wszystkich kroków publikowania dla tego typu oferty.  *Należy pamiętać, że liczba i konkretne kroki często różnią się między typami ofert.*  Ta strona wskazuje również wszelkie nierozstrzygnięte problemy spowodowane krokami sprawdzania poprawności i przeglądu firmy Microsoft, które często wymagają działania wydawcy, zanim proces publikowania będzie mógł być kontynuowany.  Na przykład na poniższej ilustracji przedstawiono kartę **Stan** dla nowej oferty maszyny wirtualnej. 

![Karta Stan oferty maszyny Wirtualnej](./media/vm-offer-pub-steps1.png)

Następna **przykładowa** karta Stan usługi konsultingowej z raportem w ustawieniach zarządzania potencjalnymi klientami.  Ponieważ zarządzanie potencjalnymi klientami jest wymagane w przypadku usług konsultingowych, ten błąd musi zostać poprawiony przed kontynuowaniem publikowania.

![Karta Stan dla usługi konsultingowej z błędem](./media/consulting-service-error.png)

Końcowy stan przykładu aplikacji platformy Azure pokazuje krytyczny problem z przeglądem firmy Microsoft.  Zawiera gorące łącze do elementu Azure DevOps, który zawiera szczegółowe informacje na temat tego problemu z recenzjami.  Aby uzyskać więcej informacji, zobacz [Publikowanie oferty aplikacji platformy Azure](cpp-publish-offer.md).

![Karta Stan aplikacji platformy Azure z problemem z recenzją](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Następne kroki

Aby rozwiązać nierozstrzygnięte problemy lub zaktualizować ustawienia oferty, należy [zaktualizować ofertę](./cpp-update-offer.md). 
