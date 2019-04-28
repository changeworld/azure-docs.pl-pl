---
title: Omówienie usługi Azure Cost Management | Microsoft Docs
description: Azure Cost Management to rozwiązanie do zarządzania kosztami, które ułatwia monitorowanie i kontrolowanie wydatków związanych z platformą Azure oraz optymalizowanie wykorzystania zasobów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: 69f91949347eadcffb3c0d3ff833a40b5e483e24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035752"
---
# <a name="what-is-azure-cost-management"></a>Co to jest usługa Azure Cost Management?

Usługa Cost management jest proces efektywnego planowania i kontrolując koszty związane z Twojej firmy. Zadania zarządzania kosztami są zazwyczaj wykonywane przez zespoły ds. finansów, zarządzania i aplikacji. Usługa Azure Cost Management pomaga organizacjom planowanie kosztów na uwadze. Pozwala ona również skutecznie analizując koszty i podjąć działania w celu optymalizacji chmury wydatków. Aby dowiedzieć się więcej na temat podejścia do zarządzania kosztami w organizacji, zapoznaj się z artykułem [Azure Cost Management best practices](cost-mgt-best-practices.md) (Najlepsze rozwiązania w usłudze Azure Cost Management).

Mimo że te czynności są powiązane, rozliczanie różni się od zarządzania kosztami. Rozliczanie jest procesem fakturowania klientów za towary lub usługi oraz zarządzania relacjami komercyjnymi.  Zadania związane z rozliczeniami są zwykle wykonywane przez zespoły ds. zamówień i finansów.

Usługa Cost Management przedstawia koszty i wzorce użycia w organizacji za pomocą zaawansowanej analizy. Raporty w Cost Management pokazują kosztów na podstawie użycia, używane przez usługi platformy Azure i innych ofert z portalu Marketplace. Koszty są oparte na wynegocjowanym ceny i wziąć pod uwagę zastrzeżenia i rabaty w ramach korzyści użycia hybrydowego platformy Azure. Zbiorczo raporty przedstawiają wewnętrzne i zewnętrzne koszty użycia i opłat w witrynie Azure Marketplace. Inne opłaty, takie jak zakupy rezerwacji, pomoc techniczna i podatki, nie są jeszcze uwzględniane w raportach. Raporty pomagają w zrozumieniu wydatków i użycia zasobów, a także ułatwiają wyszukiwanie nietypowych wydatków. Dostępna jest również analiza predykcyjna. Usługa Cost Management używa grup zarządzania, budżetów i rekomendacji platformy Azure, aby w czytelny sposób przedstawić, jak wydatki są zorganizowane i jak można zmniejszyć koszty.

Witryny Azure Portal i różnych interfejsów API można używać do automatyzacji eksportu w celu zintegrowania danych kosztów z procesami i systemami zewnętrznymi. Dostępna jest również funkcja zautomatyzowanego eksportu danych rozliczeń oraz raporty zaplanowane.

## <a name="plan-and-control-expenses"></a>Planowanie i kontrolowanie kosztów

Usługa Cost Management ułatwia planowanie i kontrolowanie kosztów, oferując następujące funkcje: analiza kosztów, budżety, rekomendacje i eksportowanie danych zarządzania kosztami.

Analiza kosztów pozwala na eksplorowanie i analizowanie kosztów organizacyjnych. Można wyświetlić zagregowane koszty według organizacji, aby dowiedzieć się, gdzie występują koszty, i zidentyfikować trendy wydatków. Ponadto zakumulowane koszty w czasie można przeglądać w celu oszacowania miesięcznych, kwartalnych, a nawet rocznych trendów kosztów w odniesieniu do budżetu.

Budżety ułatwiają planowanie i spełnianie założeń odpowiedzialności finansowej w organizacji. Pomagają one zapobiegać przekroczeniom limitów lub progów kosztów. Budżety mogą również ułatwiać informowanie innych osób o ich wydatkach, aby aktywnie zarządzać kosztami. I można przy ich użyciu zobaczyć, jak kształtują się wydatki w czasie.

Rekomendacje pokazują, jak można przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane. Mogą również dotyczyć tańszych opcji zasobów. Pracując z rekomendacjami, zmieniasz sposób używania zasobów, aby zaoszczędzić pieniądze. Aby rozpocząć pracę, należy najpierw zapoznać się z rekomendacjami dotyczącymi optymalizacji kosztów, aby zidentyfikować potencjalne przypadki nieefektywnego użycia. Następnie należy skorzystać z rekomendacji, aby zmodyfikować użycie zasobów platformy Azure do tańszej opcji. Na koniec należy zweryfikować wykonane akcje, aby upewnić się, że wprowadzanie zmian zakończyło się powodzeniem.

Jeśli używasz systemów zewnętrznych do uzyskiwania dostępu do danych zarządzania kosztami lub ich przeglądania, możesz łatwo wyeksportować dane z platformy Azure. Możesz również ustawić dzienny zaplanowany eksport w formacie CSV i przechowywać pliki danych w usłudze Azure Storage. Dane będą w tej sytuacji dostępne z systemu zewnętrznego.

## <a name="consider-cloudyn"></a>Korzyści z użycia usługi Cloudyn

[Cloudyn](overview.md) to usługa platformy Azure związana z usługą Cost Management. Usługa Cloudyn pozwala na śledzenie użycia chmury i związanych z nią wydatków dla zasobów platformy Azure. Obsługuje ona również innych dostawców chmury, w tym usługi AWS i Google. Łatwe do zrozumienia raporty pulpitu nawigacyjnego ułatwiają alokację kosztów oraz obsługę przewidywanych kosztów i obciążeń zwrotnych. Obecnie usługa Cost Management nie obsługuje przewidywanych kosztów/obciążeń zwrotnych ani innych dostawców usług w chmurze. Usługa Cloudyn to opcja, która _obsługuje_ te elementy. Obecnie Cost Management nie obsługuje konta dostawcy usług firmy Microsoft w chmurze (CSP), ale jest Cloudyn. Jeśli masz konta programu CSP lub jeśli chcesz używać raportów przewidywanych kosztów i obciążeń zwrotnych, można użyć Cloudyn pomagające w zarządzaniu kosztami.

## <a name="additional-azure-tools"></a>Dodatkowe narzędzia platformy Azure

Platforma Azure oferuje inne narzędzia, które nie są częścią zestawu funkcji usługi Azure Cost Management. Pełnią one jednak ważną rolę w procesie zarządzania kosztami. Aby dowiedzieć się więcej o tych narzędziach, skorzystaj z poniższych linków.

- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) — to narzędzie umożliwia szacowanie początkowych kosztów chmury.
- [Usługa Azure Migrate](../migrate/migrate-overview.md) — umożliwia ocenianie bieżącego obciążenia centrum danych w celu uzyskania szczegółowych informacji o potrzebach w zakresie zastępczego rozwiązania platformy Azure.
- [Usługa Azure Advisor](../advisor/advisor-overview.md) — umożliwia zidentyfikowanie nieużywanych maszyn wirtualnych i zapoznanie się z rekomendacjami w zakresie zakupów wystąpień zarezerwowanych platformy Azure.
- [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) — umożliwia oszczędzanie dzięki użyciu bieżących licencji systemu Windows Server lub programu SQL Server dla maszyn wirtualnych na platformie Azure.


## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy znasz już usługę Cost Management, następnym krokiem jest rozpoczęcie pracy z tą usługą.

- Zacznij korzystać z usługi Azure Cost Management, aby [analizować koszty](quick-acm-cost-analysis.md).
- Możesz również dowiedzieć się więcej na temat [najlepszych rozwiązań w usłudze Azure Cost Management](cost-mgt-best-practices.md).
