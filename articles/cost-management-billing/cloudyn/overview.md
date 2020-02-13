---
title: Omówienie rozwiązania Cloudyn na platformie Azure | Microsoft Docs
description: Cloudyn to rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia korzystanie z platformy Azure i innych zasobów w chmurze.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: overview
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ms.openlocfilehash: bfd00613a3949b29e2defcb6f97398a39091d0e6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76774051"
---
# <a name="what-is-the-cloudyn-service"></a>Co to jest rozwiązanie Cloudyn?

Rozwiązanie Cloudyn (będące własnością podmiotu zależnego od firmy Microsoft) umożliwia śledzenie użycia chmury i wydatków na zasoby platformy Azure i innych dostawców rozwiązań w chmurze, w tym AWS i Google. Łatwe do zrozumienia raporty pulpitu nawigacyjnego ułatwiają alokację kosztów oraz obsługę przewidywanych kosztów i obciążeń zwrotnych. Rozwiązanie Cloudyn ułatwia optymalizację wydatków związanych z chmurą przez identyfikowanie niedostatecznie używanych zasobów, którymi można później zarządzać oraz je dostosowywać.

Aby obejrzeć klip wideo z wprowadzeniem, zobacz [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo) (Wprowadzenie do rozwiązania Azure Cloudyn).

Usługa Azure Cost Management oferuje podobne funkcje jak rozwiązanie Cloudyn. Usługa Azure Cost Management to natywne rozwiązanie do zarządzania kosztami na platformie Azure. Ułatwia ona analizowanie kosztów, tworzenie budżetów i zarządzanie nimi, eksportowanie danych, a także zapoznawanie się z rekomendacjami dotyczącymi optymalizacji i ich wdrażanie w celu zaoszczędzenia pieniędzy. Aby uzyskać więcej informacji, zobacz [Azure Cost Management](../cost-management-billing-overview.md).

Obejrzyj [wideo z porównaniem usług Azure Cost Management i Cloudyn](https://www.youtube.com/watch?v=PmwFWwSluh8), aby zobaczyć zalecenia dotyczące przypadków, w których należy skorzystać z usługi Azure Cost Management lub Cloudyn w zależności od potrzeb firmy.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Funkcje rozwiązania Cloudyn są przenoszone do usługi Azure Cost Management

Firma Microsoft nabyła rozwiązanie Cloudyn i migruje jego funkcje zarządzania kosztami z portalu Cloudyn do platformy Azure, aby działały w sposób natywny. Aby móc skorzystać z nowych funkcji, zaloguj się do witryny Azure Portal i przejdź do pozycji [Zarządzanie kosztami i rozliczenia](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) na liście usług platformy Azure. W porównaniu z rozwiązaniem Cloudyn natywne środowisko zapewnia lepszą wydajność i mniejsze opóźnienie danych wynoszące około ośmiu godzin.

Migracja najważniejszych funkcji do usługi Azure Cost Management dla umów Enterprise Agreement, płatności zgodnie z rzeczywistym użyciem i kategorii ofert MSDN została ukończona. Subskrypcje CSP są aktualnie migrowane do usługi Azure Cost Management.

W przypadku korzystania z kategorii ofert, która nie została jeszcze zmigrowana, należy nadal używać portalu Cloudyn. Wszystkie inne osoby mogą korzystać z usługi Azure Cost Management.

| Oferty i funkcje platformy Microsoft Azure | Zalecana usługa zarządzania kosztami |
| --- | --- |
| Umowa Enterprise Agreement platformy Azure | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (płatność zgodnie z rzeczywistym użyciem/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Obsługa analizy kosztów w różnych chmurach dla usługi AWS (w wersji zapoznawczej) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Zalecenia dotyczące usługi AWS | [Cloudyn](https://azure.cloudyn.com) |

Niektóre z następujących funkcji są dostępne w rozwiązaniu Cloudyn, ale wszystkie są teraz dostępne w usłudze Azure Cost Management.

- Interfejsy API
- Zalecenia dotyczące usług obliczeniowych na platformie Azure
- Rekomendacje dotyczące rezerwacji na platformie Azure
- Budżety
- Analiza kosztów
- Eksportowanie danych na konto usługi Azure Storage
- Mniejsze opóźnienia
- Pakiet zawartości i łącznik usługi Power BI
- Obsługa tagów zasobów

## <a name="monitor-usage-and-spending"></a>Monitorowanie użycia i wydatków

Monitorowanie użycia i wydatków ma kluczowe znaczenie dla infrastruktur w chmurze, ponieważ organizacje płacą za zasoby wykorzystywane w czasie. Gdy użycie przekroczy progi umowy, mogą zostać szybko naliczone nieoczekiwane koszty za użycie nadwyżkowe. Monitorowanie ad-hoc może utrudnić kilka ważnych czynników. Po pierwsze, planowanie kosztów na podstawie średniego użycia zakłada, że zużycie pozostaje na stałym poziomie przez określony okres rozliczeniowy. Po drugie, bardzo ważne jest proaktywne otrzymywanie powiadomień, gdy koszty zbliżają się do ustalonego budżetu lub go przekraczają. Dostawcy usług w chmurze mogą nie oferować projekcji kosztów względem progów lub raportów porównawczych okresów.

Raporty ułatwiają monitorowanie wydatków w celu analizowania i śledzenia użycia chmury, kosztów i trendów. Używając raportów w czasie, możesz wykrywać anomalie różniące się od normalnych trendów. Czynniki niskiej wydajności we wdrożeniu w chmurze są widoczne w raportach optymalizacji. Czynniki niskiej wydajności możesz także zobaczyć w raportach analizy kosztów.

## <a name="manage-costs"></a>Zarządzanie kosztami

Dane historyczne mogą ułatwiać zarządzanie kosztami, gdy przeanalizuje się użycie i koszty w czasie, aby zidentyfikować trendy. Przyszłe wydatki są następnie prognozowane przy użyciu trendów. Rozwiązanie Cloudyn zawiera także przydatne raporty kosztów planowanych.

Alokacja kosztów zarządza kosztami, analizując koszty na podstawie zasad tagowania. Możesz używać tagów w swoich kontach niestandardowych, zasobach i jednostkach w celu doprecyzowania alokacji kosztów. Menedżer kategorii organizuje tagi, aby ułatwić uzyskanie dodatkowego ładu. Alokacji kosztów można użyć na potrzeby przewidywanych kosztów i obciążeń zwrotnych, aby uwidocznić wykorzystanie zasobów i związane z tym koszty oraz wpływać na zachowania zużycia lub obciążać klientów dzierżawy.

Kontrola dostępu ułatwia zarządzanie kosztami, zapewniając, że użytkownicy i zespoły mają dostęp tylko do potrzebnych im danych zarządzania kosztami. Możesz przypisywać dostęp przy użyciu struktury jednostki, zarządzania użytkownikami i zaplanowanych raportów z listami adresatów.

Generowanie alertów ułatwia zarządzanie kosztami, automatycznie powiadamiając Cię, gdy występują nietypowe lub nadmiarowe wydatki. Alerty mogą także automatycznie powiadamiać innych uczestników projektu o anomaliach wydatków i ryzykach związanych z nadmiarowymi wydatkami. Różne raporty obsługują alerty oparte na budżecie i progach kosztów. Alerty nie są jednak obecnie obsługiwane w przypadku kont partnerów lub subskrypcji programu CSP.

## <a name="improve-efficiency"></a>Poprawa wydajności

Za pomocą rozwiązania Cloudyn możesz określić optymalne użycie maszyny wirtualnej i zidentyfikować bezczynne maszyny wirtualne lub usunąć bezczynne maszyny wirtualne oraz niedołączone dyski. Korzystając z informacji zawartych w obszarach Optymalizacja rozmiaru i Raporty dotyczące nieefektywności, możesz sporządzić plan zmniejszenia rozmiaru lub usunięcia bezczynnych maszyn wirtualnych. Raporty dotyczące optymalizacji nie są jednak obecnie obsługiwane w przypadku kont partnerów lub subskrypcji programu CSP.

Jeśli zaaprowizowano wystąpienia zarezerwowane usługi AWS, możesz zwiększyć użycie wystąpień zarezerwowanych przy użyciu raportów dotyczących optymalizacji, dzięki którym możesz zobaczyć zalecenia dotyczące zakupów, modyfikować nieużywane rezerwacje i planować aprowizację.


## <a name="next-steps"></a>Następne kroki

Kolejnym krokiem po zapoznaniu się z rozwiązaniem Cloudyn jest zarejestrowanie środowiska chmury i rozpoczęcie eksplorowania danych.

- [Rejestrowanie indywidualnej subskrypcji platformy Azure](quick-register-azure-sub.md)
