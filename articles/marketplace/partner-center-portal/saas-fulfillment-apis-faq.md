---
title: Interfejsy API realizacji SaaS - CZĘSTO ZADAWANE PYTANIA | Azure Marketplace
description: Odnajdowanie i kupowanie przez klientów oferty SaaS w portalu Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275700"
---
# <a name="saas-fulfillment-apis---faq"></a>Interfejsy API realizacji SaaS — często zadawane pytania

Wymagania dotyczące integracji z witryną Azure Marketplace, aby umożliwić klientom platformy Azure subskrybowanie oferty SaaS są wyświetlane.

## <a name="discovery-experience"></a>Doświadczenie odnajdywania

Po opublikowaniu oferty użytkownicy platformy Azure mogą odnajdyć ofertę SaaS w portalu Azure Marketplace. Twoi klienci będą mogli filtrować oferty na podstawie typu produktu (SaaS) i odkrywać usługi SaaS, które są zainteresowani.

## <a name="purchase-experience"></a>Doświadczenie zakupowe

Gdy użytkownik jest zainteresowany określoną usługą SaaS, użytkownik może subskrybować go z usługi Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Co oznacza, że użytkownik platformy Azure subskrybuje ofertę SaaS w portalu Azure Marketplace?

Oznacza to, że użytkownik może przeglądać warunki użytkowania i zasady zachowania poufności informacji związane z usługą SaaS i zgadzać się na opłacenie ich zgodnie z warunkami rozliczeniowymi określonymi przez użytkownika, wydawcę oferty SaaS, na fakturze firmy Microsoft. Użytkownicy mogą używać istniejącego profilu płatności na platformie Azure, aby płacić za zużycie usługi SaaS.

Jest to korzystne z wielu powodów. Klienci mogą teraz odkrywać i subskrybować w jednym miejscu za pomocą platformy Microsoft Cloud Platform jako zaufanego źródła, bez konieczności weryfikacji każdego oprogramowania, którego zamierza używać. Ponadto klienci mogą korzystać z istniejącego profilu płatności bez konieczności jawnego płacenia każdego oprogramowania niezależnego dostawcy oprogramowania.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Czy użytkownik jest naliczany automatycznie, gdy oferta jest subskrybowana?

Podczas subskrybowania oferty SaaS użytkownik zgodził się zapłacić za korzystanie z usługi SaaS za pośrednictwem platformy Microsoft. Jednak opłaty będą uruchamiane tylko wtedy, gdy oferta zostanie wykorzystana. Użytkownik musi przejść do oferty SaaS i potwierdzić utworzenie konta, aby rozpocząć korzystanie z oferty. Następnie użytkownik powiadomi firmę Microsoft o rozpoczęciu rozliczeń za tę subskrypcję SaaS dla klienta.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>W jaki sposób użytkownik jest powiadamiany, gdy użytkownik subskrybuje Twoją ofertę SaaS?

Po zasubskrybowaniu oferty użytkownik platformy Azure może odnajdować wszystkie swoje oferty na platformie Azure i zarządzać nimi. Domyślnie stan nowo subskrybowanych ofert SaaS to **"Inicjowanie obsługi administracyjnej, realizacja oczekujące"**. W tym stanie użytkownik platformy Azure zostanie poproszony o akcję **"Konfigurowanie konta",** aby przejść do środowiska zarządzania subskrypcjami SaaS w witrynie Azure portal.

Gdy użytkownik kliknie **na "Skonfiguruj konto",** zostanie przekierowany na stronę usługi SaaS. Adres URL, do którego są nawigowane, jest dostarczany przez wydawcę w momencie publikowania oferty. Ta strona jest nazywana stroną docelową wydawcy. Użytkownicy platformy Azure powinni mieć możliwość logowania się do strony docelowej SaaS na podstawie istniejących poświadczeń usługi AAD na platformie Azure.

Gdy użytkownik platformy Azure zostanie przekierowany do strony docelowej, token jest dodawany do adresu URL kwerendy. Ten token jest krótkotrwały i ważny przez 24 godziny. Następnie można wykryć obecność tego tokenu i wywołać interfejs API firmy Microsoft, aby uzyskać więcej kontekstu skojarzonego z tokenem.

![Przepływ subskrypcji klienta](media/saas-metering-service-integration-flow-a.png)

Zobacz dokument [interfejsu API realizacji usługi SaaS,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) aby uzyskać więcej informacji na temat umowy interfejsu API do obsługi scenariuszy transakcji w cyklu życia oferty SaaS.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Skąd znasz ofertę SaaS, do której użytkownik subskrybuje na platformie Azure?

Odpowiedź na `Resolve` interfejs API zawiera informacje o ofercie i planie skojarzone z subskrypcją SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Jak użytkownik platformy Azure może zmienić plan skojarzony z tą subskrypcją platformy Azure?

* Użytkownik platformy Azure można zmienić plan skojarzony z subskrypcją SaaS bezpośrednio w środowiska SaaS lub za pośrednictwem platformy Microsoft.

* Konwersje można przeprowadzić w dowolnym momencie w cyklu rozliczeniowym. Musisz potwierdzić każdą konwersję, która wejdzie w życie po uznaniu.

* Stawki za plan przedpłacony **(miesięczny** lub **roczny)** są proporcjonalne. Wszelkie naddunienie wyemitowane do momentu konwersji zostanie obciążone następną fakturą. Nowe nadwiększe będą emitowane w oparciu o nowy plan.

>[!Note]
>Możesz zablokować zmiany na starszą wersję, jeśli nie chcesz obsługiwać określonych ścieżek konwersji.

Poniższa sekwencja przechwytuje przepływ, gdy klient platformy Azure zmienia plan w środowiskach SaaS:

![Przepływ zmian planu klienta](media/saas-metering-service-integration-flow-b.png)

Poniższa sekwencja przechwytuje przepływ, gdy klient platformy Azure zmienia plan w sklepie firmy Microsoft

![Przepływ zmian w planie sklepu klienta](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>W jaki sposób użytkownik platformy Azure może anulować subskrypcję planu skojarzonego z subskrypcją platformy Azure?

Użytkownik platformy Azure może zrezygnować z subskrypcji zakupionej oferty SaaS bezpośrednio w środowiskach SaaS lub za pośrednictwem platformy Firmy Microsoft. Gdy użytkownik zrezygnuje z subskrypcji, nie będzie już naliczany opłat z następnego cyklu rozliczeniowego.

Poniższa sekwencja przechwytuje przepływ, gdy klient platformy Azure anuluje subskrypcję oferty SaaS w środowiskach SaaS:

![Klient wypisuje się z subskrypcji w udziale SaaS](media/saas-metering-service-integration-flow-d.png)

Poniższa sekwencja przechwytuje przepływ, gdy użytkownik platformy Azure ubskrybuje subskrypcję w sklepie firmy Microsoft:

![Klient zrezygnuje z subskrypcji w sklepie firmy Microsoft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [Interfejsy API usługi pomiaru w portalu Marketplace.](./marketplace-metering-service-apis.md)
