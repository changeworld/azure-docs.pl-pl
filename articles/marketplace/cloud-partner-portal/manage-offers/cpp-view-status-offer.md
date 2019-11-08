---
title: Wyświetlanie stanu ofert portalu Marketplace | Portal Azure Marketplace
description: Wyświetlanie stanu ofert na platformie Azure i AppSource Marketplace przy użyciu portal Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: e58670d37e01bb6e453b73e42a87e42e890d10d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826700"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Wyświetlanie stanu publikowania w witrynie Azure Marketplace i ofertach AppSource

Po utworzeniu oferty, w szczególności w trakcie procesu publikowania, można wyświetlić stan oferty w portal Cloud Partner.  Ogólny stan publikowania jest dostępny na stronach [**wszystkie oferty**](../portal-tour/cpp-all-offers-page.md) i [**zatwierdzenia**](../portal-tour/cpp-approvals-page.md) portalu.  Dla każdej oferty należy wyświetlić jeden z następujących wskaźników stanu.  

|            Stan              |   Opis                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Oferta została utworzona, ale proces publikowania nie został rozpoczęty.            |
| **Publikowanie w toku**        | Oferta działa w sposób w ramach kroków procesu publikowania.   |
| **Publikowanie nie powiodło się**             | Wykryto problem krytyczny podczas weryfikacji lub przegląd przez firmę Microsoft. |
| **Publikowanie zostało anulowane**           | Wydawca anulował proces publikowania oferty.  W tym stanie nie zostanie wystawiona istniejąca oferta w portalu Marketplace. | 
| **Oczekiwanie na wylogowanie wydawcy** | Oferta została sprawdzona przez firmę Microsoft i teraz czeka na ostateczną weryfikację przez wydawcę. |
| **Lista**                   | Wcześniej opublikowana oferta w portalu Marketplace została usunięta.      | 
|  |  |


## <a name="publishing-status-details"></a>Szczegóły stanu publikowania 

Więcej szczegółów na temat stanu oferty w ramach procesu publikowania znajduje się na karcie **stan** na **nowej stronie oferty** .  Ta strona zawiera listę wszystkich kroków publikowania dla tego typu oferty.  *Należy zauważyć, że liczba i konkretne kroki często różnią się w zależności od typu oferty.*  Ta strona wskazuje również wszelkie zaległe problemy zgłoszone przez proces sprawdzania poprawności i przeglądu firmy Microsoft, które często wymagają akcji wykonywanej przez wydawcę, zanim będzie można przeprowadzić publikowanie.  Na przykład na poniższej ilustracji przedstawiono kartę **stan** dla nowej oferty maszyny wirtualnej. 

![Karta stan dla oferty maszyny wirtualnej](./media/vm-offer-pub-steps1.png)

Kolejna Przykładowa karta **stan** dla usługi doradczej wyświetlająca zgłoszony błąd w ustawieniach zarządzania potencjalnymi klientami.  Ponieważ zarządzanie potencjalnymi klientami jest wymagane dla usług doradczych, ten błąd należy poprawić, zanim będzie można kontynuować publikowanie.

![Karta stan dla usługi konsultingowej przedstawiająca błąd](./media/consulting-service-error.png)

Ostatni przykładowy stan aplikacji platformy Azure przedstawia krytyczny problem z przeglądem firmy Microsoft.  Zawiera aktywne łącze do elementu usługi Azure DevOps, który zawiera szczegółowe informacje o tym problemie.  Aby uzyskać więcej informacji, zobacz temat [Publikowanie oferty aplikacji platformy Azure](cpp-publish-offer.md).

![Karta stan dla aplikacji platformy Azure z widocznym problemem przeglądu](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Następne kroki

Aby rozwiązać problemy nieobsłużone lub zaktualizować ustawienia oferty, musisz [zaktualizować ofertę](./cpp-update-offer.md). 
