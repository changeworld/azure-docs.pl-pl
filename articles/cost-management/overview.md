---
title: Omówienie usługi Azure Cost Management | Microsoft Docs
description: Azure Cost Management to rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia korzystanie z platformy Azure i innych zasobów w chmurze.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: overview
ms.service: cost-management
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 3e5caff5ff1af79154baddf39bf465ddea5aadae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-cost-management"></a>Co to jest usługa Azure Cost Management?

Usługa Azure Cost Management licencjonowana przez firmę Cloudyn (podmiot zależny od firmy Microsoft) umożliwia śledzenie użycia chmury i wydatków na zasoby platformy Azure i innych dostawców rozwiązań w chmurze, w tym AWS i Google. Łatwe do zrozumienia raporty pulpitu nawigacyjnego ułatwiają alokację kosztów oraz obsługę przewidywanych kosztów i obciążeń zwrotnych. Zarządzanie kosztami ułatwia optymalizację wydatków związanych z chmurą przez identyfikowanie niedostatecznie używanych zasobów, którymi można później zarządzać oraz je dostosowywać.

Aby obejrzeć klip wideo z wprowadzeniem, zobacz [Wprowadzenie do usługi Azure Cost Management](https://azure.microsoft.com/en-us/resources/videos/azure-cost-management-overview-and-demo).

## <a name="monitor-usage-and-spending"></a>Monitorowanie użycia i wydatków

Monitorowanie użycia i wydatków ma kluczowe znaczenie dla infrastruktur w chmurze, ponieważ organizacje płacą za zasoby wykorzystywane w czasie. Gdy użycie przekroczy progi umowy, mogą zostać szybko naliczone nieoczekiwane koszty za użycie nadwyżkowe. Monitorowanie ad-hoc może utrudnić kilka ważnych czynników. Po pierwsze, planowanie kosztów na podstawie średniego użycia zakłada, że zużycie pozostaje na stałym poziomie przez określony okres rozliczeniowy. Po drugie, bardzo ważne jest proaktywne otrzymywanie powiadomień, gdy koszty zbliżają się do ustalonego budżetu lub go przekraczają. Dostawcy usług w chmurze mogą nie oferować projekcji kosztów względem progów lub raportów porównawczych okresów.

Raporty ułatwiają monitorowanie wydatków w celu analizowania i śledzenia użycia chmury, kosztów i trendów. Używając raportów w czasie, możesz wykrywać anomalie różniące się od normalnych trendów. Czynniki niskiej wydajności we wdrożeniu w chmurze są widoczne w raportach optymalizacji. Czynniki niskiej wydajności możesz także zobaczyć w raportach analizy kosztów.

![Raport kosztu w czasie](media\overview\cost-over-time-rpt.png)


## <a name="manage-costs"></a>Zarządzanie kosztami

Dane historyczne mogą ułatwiać zarządzanie kosztami, gdy przeanalizuje się użycie i koszty w czasie, aby zidentyfikować trendy. Przyszłe wydatki są następnie prognozowane przy użyciu trendów. Usługa Cost Management zawiera także przydatne raporty kosztów planowanych.

Alokacja kosztów zarządza kosztami, analizując koszty na podstawie zasad tagowania. Możesz używać tagów w swoich kontach niestandardowych, zasobach i jednostkach w celu doprecyzowania alokacji kosztów. Menedżer kategorii organizuje tagi, aby ułatwić uzyskanie dodatkowego ładu. Alokacji kosztów można użyć na potrzeby przewidywanych kosztów i obciążeń zwrotnych, aby uwidocznić wykorzystanie zasobów i związane z tym koszty oraz wpływać na zachowania zużycia lub obciążać klientów dzierżawy.

Kontrola dostępu ułatwia zarządzanie kosztami, zapewniając, że użytkownicy i zespoły mają dostęp tylko do potrzebnych im danych zarządzania kosztami. Możesz przypisywać dostęp przy użyciu struktury jednostki, zarządzania użytkownikami i zaplanowanych raportów z listami adresatów.

Generowanie alertów ułatwia zarządzanie kosztami, automatycznie powiadamiając Cię, gdy występują nietypowe lub nadmiarowe wydatki. Alerty mogą także automatycznie powiadamiać innych uczestników projektu o anomaliach wydatków i ryzykach związanych z nadmiarowymi wydatkami. Różne raporty obsługują alerty oparte na budżecie i progach kosztów. Alerty nie są jednak obecnie obsługiwane w przypadku kont partnerów lub subskrypcji programu CSP.

## <a name="improve-efficiency"></a>Poprawa wydajności

Za pomocą usługi Cost Management możesz określić optymalne użycie maszyny wirtualnej i zidentyfikować bezczynne maszyny wirtualne lub usunąć bezczynne maszyny wirtualne oraz niedołączone dyski. Korzystając z informacji zawartych w obszarach Optymalizacja rozmiaru i Raporty dotyczące nieefektywności, możesz sporządzić plan zmniejszenia rozmiaru lub usunięcia bezczynnych maszyn wirtualnych. Raporty dotyczące optymalizacji nie są jednak obecnie obsługiwane w przypadku kont partnerów lub subskrypcji programu CSP.

![Zalecenia dotyczące ustalania rozmiaru](.\media\overview\sizing.png)

Jeśli zaaprowizowano wystąpienia zarezerwowane usługi AWS, możesz zwiększyć użycie wystąpień zarezerwowanych przy użyciu raportów dotyczących optymalizacji, dzięki którym możesz zobaczyć zalecenia dotyczące zakupów, modyfikować nieużywane rezerwacje i planować aprowizację.

## <a name="next-steps"></a>Następne kroki

Kolejnym krokiem po zapoznaniu się z usługą Cost Management jest zarejestrowanie środowiska chmury i rozpoczęcie eksplorowania danych.

- [Rejestrowanie indywidualnej subskrypcji platformy Azure](quick-register-azure-sub.md)
