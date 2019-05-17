---
title: Omówienie rozwiązania Cloudyn na platformie Azure | Microsoft Docs
description: Cloudyn to rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia korzystanie z platformy Azure i innych zasobów w chmurze.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 8b989e5cf5b66d21c19d58f2f64fbba1927f5d69
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792798"
---
# <a name="what-is-the-cloudyn-service"></a>Co to jest rozwiązanie Cloudyn?

Rozwiązanie Cloudyn (będące własnością podmiotu zależnego od firmy Microsoft) umożliwia śledzenie użycia chmury i wydatków na zasoby platformy Azure i innych dostawców rozwiązań w chmurze, w tym AWS i Google. Łatwe do zrozumienia raporty pulpitu nawigacyjnego ułatwiają alokację kosztów oraz obsługę przewidywanych kosztów i obciążeń zwrotnych. Rozwiązanie Cloudyn ułatwia optymalizację wydatków związanych z chmurą przez identyfikowanie niedostatecznie używanych zasobów, którymi można później zarządzać oraz je dostosowywać.

Aby obejrzeć klip wideo z wprowadzeniem, zobacz [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo) (Wprowadzenie do rozwiązania Azure Cloudyn).

Usługa Azure Cost Management oferuje podobne funkcje jak rozwiązanie Cloudyn. Usługa Azure Cost Management to natywne rozwiązanie do zarządzania kosztami na platformie Azure. Ułatwia ona analizowanie kosztów, tworzenie budżetów i zarządzanie nimi, eksportowanie danych, a także zapoznawanie się z rekomendacjami dotyczącymi optymalizacji i ich wdrażanie w celu zaoszczędzenia pieniędzy. Aby uzyskać więcej informacji, zobacz [Azure Cost Management](overview-cost-mgt.md).

Obejrzyj [wideo usługi Azure Cost Management i Cloudyn](https://www.youtube.com/watch?v=PmwFWwSluh8) pozwalają zobaczyć zalecenia, kiedy należy używać usługi Azure Cost Management lub Cloudyn, na podstawie potrzeb biznesowych.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Funkcje platformy Cloudyn, przejście do usługi Azure Cost Management

Firma Microsoft nabyte Cloudyn i jest migrowany jego funkcje zarządzania kosztami portalu Cloudyn natywnie na platformie Azure. Aby korzystać z nowych funkcji, zaloguj się do witryny Azure portal i przejdź do [Zarządzanie kosztami i rozliczenia](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) na liście usług platformy Azure. W porównaniu do rozwiązania Cloudyn, natywne środowisko oferuje lepszą wydajność i mniejsze opóźnienia danych około ośmiu godzin.

Kluczową funkcją migracji dla umowy Enterprise Agreement, płatności i MSDN kategorii oferty do usługi Azure Cost Management zostało ukończone. Subskrypcje dostawcy usług Kryptograficznych to właśnie trwa poddane migracji do usługi Azure Cost Management.

Jeśli jeszcze nie poddano migracji do kategorii oferty, można nadal używać portalu Cloudyn. Wszyscy można użyć usługi Azure Cost Management.

| Oferty Microsoft Azure i funkcji | Usługa zarządzania zalecane kosztów |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Bezpośrednie sieci Web platformy Azure (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Wielu chmur koszt analizy obsługę usług AWS (w wersji zapoznawczej) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Zalecenia dotyczące usług AWS | [Cloudyn](https://azure.cloudyn.com) |

Niektóre z następujących funkcji są dostępne w rozwiązaniu Cloudyn, ale wszystkie z nich są teraz dostępne w usłudze Azure Cost Management.

- Interfejsy API
- Zalecenia dotyczące obliczeń platformy Azure
- Usługa Azure zalecenia rezerwacji
- Budżety
- Analiza kosztów
- Eksportowanie danych do konta usługi Azure storage
- Krótszy czas oczekiwania
- Pakiet zawartości usługi Power BI i łącznik
- Obsługa tagów zasobów

## <a name="monitor-usage-and-spending"></a>Monitorowanie użycia i wydatków

Monitorowanie użycia i wydatków ma kluczowe znaczenie dla infrastruktur w chmurze, ponieważ organizacje płacą za zasoby wykorzystywane w czasie. Gdy użycie przekroczy progi umowy, mogą zostać szybko naliczone nieoczekiwane koszty za użycie nadwyżkowe. Kilka ważnych czynników może utrudnić ad-hoc monitorowania. Po pierwsze, planowanie kosztów na podstawie średniego użycia zakłada, że zużycie pozostaje na stałym poziomie przez określony okres rozliczeniowy. Po drugie, bardzo ważne jest proaktywne otrzymywanie powiadomień, gdy koszty zbliżają się do ustalonego budżetu lub go przekraczają. Dostawcy usług w chmurze mogą nie oferować projekcji kosztów względem progów lub raportów porównawczych okresów.

Raporty ułatwiają monitorowanie wydatków w celu analizowania i śledzenia użycia chmury, kosztów i trendów. Używając raportów w czasie, możesz wykrywać anomalie różniące się od normalnych trendów. Czynniki niskiej wydajności we wdrożeniu w chmurze są widoczne w raportach optymalizacji. Czynniki niskiej wydajności możesz także zobaczyć w raportach analizy kosztów.

## <a name="manage-costs"></a>Zarządzaj kosztami

Dane historyczne mogą ułatwiać zarządzanie kosztami, gdy przeanalizuje się użycie i koszty w czasie, aby zidentyfikować trendy. Przyszłe wydatki są następnie prognozowane przy użyciu trendów. Rozwiązanie Cloudyn zawiera także przydatne raporty kosztów planowanych.

Alokacja kosztów zarządza kosztami, analizując koszty na podstawie zasad tagowania. Możesz używać tagów w swoich kontach niestandardowych, zasobach i jednostkach w celu doprecyzowania alokacji kosztów. Menedżer kategorii organizuje tagi, aby ułatwić uzyskanie dodatkowego ładu. Alokacji kosztów można użyć na potrzeby przewidywanych kosztów i obciążeń zwrotnych, aby uwidocznić wykorzystanie zasobów i związane z tym koszty oraz wpływać na zachowania zużycia lub obciążać klientów dzierżawy.

Kontrola dostępu ułatwia zarządzanie kosztami, zapewniając, że użytkownicy i zespoły mają dostęp tylko do potrzebnych im danych zarządzania kosztami. Możesz przypisywać dostęp przy użyciu struktury jednostki, zarządzania użytkownikami i zaplanowanych raportów z listami adresatów.

Generowanie alertów ułatwia zarządzanie kosztami, automatycznie powiadamiając Cię, gdy występują nietypowe lub nadmiarowe wydatki. Alerty mogą także automatycznie powiadamiać innych uczestników projektu o anomaliach wydatków i ryzykach związanych z nadmiarowymi wydatkami. Różne raporty obsługują alerty oparte na budżecie i progach kosztów. Alerty nie są jednak obecnie obsługiwane w przypadku kont partnerów lub subskrypcji programu CSP.

## <a name="improve-efficiency"></a>Poprawa wydajności

Za pomocą rozwiązania Cloudyn możesz określić optymalne użycie maszyny wirtualnej i zidentyfikować bezczynne maszyny wirtualne lub usunąć bezczynne maszyny wirtualne oraz niedołączone dyski. Korzystając z informacji zawartych w obszarach Optymalizacja rozmiaru i Raporty dotyczące nieefektywności, możesz sporządzić plan zmniejszenia rozmiaru lub usunięcia bezczynnych maszyn wirtualnych. Raporty dotyczące optymalizacji nie są jednak obecnie obsługiwane w przypadku kont partnerów lub subskrypcji programu CSP.

Jeśli zaaprowizowano wystąpienia zarezerwowane usługi AWS, możesz zwiększyć użycie wystąpień zarezerwowanych przy użyciu raportów dotyczących optymalizacji, dzięki którym możesz zobaczyć zalecenia dotyczące zakupów, modyfikować nieużywane rezerwacje i planować aprowizację.


## <a name="next-steps"></a>Kolejne kroki

Kolejnym krokiem po zapoznaniu się z rozwiązaniem Cloudyn jest zarejestrowanie środowiska chmury i rozpoczęcie eksplorowania danych.

- [Rejestrowanie indywidualnej subskrypcji platformy Azure](quick-register-azure-sub.md)
