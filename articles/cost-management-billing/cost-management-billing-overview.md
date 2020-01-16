---
title: Przegląd Azure Cost Management i rozliczeń | Microsoft Docs
description: Funkcje Azure Cost Management i rozliczeń umożliwiają wykonywanie zadań administracyjnych dotyczących rozliczeń i zarządzanie dostępem do rozliczeń w ramach kosztów. Ponadto funkcja do monitorowania i kontrolowania wydatków na platformę Azure oraz do optymalizowania użycia zasobów platformy Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/20/2019
ms.topic: overview
ms.service: cost-management-billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: cadff1d83a8b47a540efe9b74ffaf6de171138b3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987516"
---
# <a name="what-is-azure-cost-management-and-billing"></a>Co to jest Azure Cost Management i rozliczenia?

W przypadku produktów i usług platformy Azure płacisz tylko za to, czego używasz. Podczas tworzenia i używania zasobów platformy Azure opłaty są naliczone za zasoby. Funkcje Azure Cost Management i rozliczeń umożliwiają wykonywanie zadań administracyjnych dotyczących rozliczeń i zarządzanie dostępem do rozliczeń w ramach kosztów. Ponadto funkcje do monitorowania i kontrolowania wydatków na platformę Azure oraz do optymalizowania użycia zasobów platformy Azure.

## <a name="understand-azure-billing"></a>Informacje o rozliczeniach platformy Azure

Funkcje rozliczeń systemu Azure są używane do przeglądania kosztów zafakturowanych i zarządzania dostępem do informacji dotyczących rozliczeń. W większych organizacjach zespoły ds. zakupów i finansów zazwyczaj przeprowadzają zadania rozliczeń.

Konto rozliczeniowe jest tworzone podczas rejestrowania się w celu korzystania z platformy Azure. Konta rozliczeniowego można używać do zarządzania fakturami i płatnościami oraz do śledzenia kosztów. Możesz mieć dostęp do wielu kont rozliczeniowych. Możesz na przykład zarejestrować się na platformie Azure, aby móc pracować nad projektami osobistymi. W związku z tym może istnieć indywidualna subskrypcja platformy Azure z kontem rozliczeniowym. Dostęp jest również możliwy w ramach zawartej przez organizację umowy Enterprise Agreement lub Umowy klienta firmy Microsoft. Dla każdego scenariusza masz oddzielne konto rozliczania.

### <a name="billing-accounts"></a>Konta rozliczeniowe

Azure Portal obecnie obsługuje następujące typy kont rozliczeń:

- **Program Microsoft Online Services**: indywidualne konto rozliczeniowe dla programu Microsoft Online Services jest tworzone podczas tworzenia konta na platformie Azure za pomocą witryny sieci Web systemu Azure. Na przykład po zarejestrowaniu się w celu uzyskania [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Umowa Enterprise**: konto rozliczeniowe dla Umowa Enterprise jest tworzone, gdy organizacja podpisuje [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) w celu korzystania z platformy Azure.

- **Umowa klienta firmy Microsoft**: konto rozliczeniowe dla umowy klienta firmy Microsoft jest tworzone, gdy organizacja współpracuje z przedstawicielem firmy Microsoft w celu podpisania umowy klienta firmy Microsoft. Niektórzy klienci w wybranych regionach, którzy zarejestrują się za pomocą witryny internetowej Azure w celu [utworzenia konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub dokonają podwyższenia poziomu swojego [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), mogą mieć również konto rozliczeniowe do umowy klienta firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z kontem rozliczeniowym do umowy klienta firmy Microsoft](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>Zakresy kont rozliczeniowych
Zakres to węzeł na koncie rozliczeniowym, który służy do wyświetlania rozliczeń i zarządzania nimi. Jest to miejsce, w którym można zarządzać danymi rozliczeń, płatnościami, fakturami i przeprowadzać ogólne zarządzanie kontami.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe     | Reprezentuje pojedynczego właściciela (administratora konta) co najmniej jednej subskrypcji platformy Azure. Administrator konta jest autoryzowany do wykonywania różnych zadań rozliczeniowych, takich jak tworzenie subskrypcji, wyświetlanie faktur lub zmiana rozliczeń dla subskrypcji.  |
|Subskrypcja     |  Reprezentuje zgrupowanie zasobów platformy Azure. W zakresie subskrypcji jest generowana faktura. Ma swoje formy płatności za faktury.|


#### <a name="enterprise-agreement"></a>Umowa Enterprise

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe    | Reprezentuje rejestrację umów Enterprise Agreement. Faktura jest generowana w zakresie konta rozliczeniowego. Jest ona strukturalna przy użyciu działów i kont rejestracji.  |
|Dział     |  Opcjonalne zgrupowanie kont rejestracji.      |
|Konto rejestracji     |  Reprezentuje pojedynczego właściciela konta. Subskrypcje platformy Azure są tworzone w ramach zakresu konta rejestracji.  |


#### <a name="microsoft-customer-agreement"></a>Umowa klienta firmy Microsoft

|Zakres  |Zadania  |
|---------|---------|
|Konto billingowe     |   Reprezentuje umowę klienta dla wielu produktów i usług firmy Microsoft. Konto rozliczeniowe jest strukturalne przy użyciu profilów rozliczeń i sekcji faktur.   |
|Profil rozliczeniowy     |  Reprezentuje fakturę i formy płatności za nią. W tym zakresie jest generowana faktura. Profil rozliczeń może zawierać wiele sekcji faktur.      |
|Sekcja faktury     |   Reprezentuje grupę kosztów na fakturze. Subskrypcje i inne zakupy są skojarzone z zakresem sekcji faktury.    |


## <a name="understand-azure-cost-management"></a>Opis Azure Cost Management
Zarządzanie kosztami to proces efektywnego planowania i kontrolowania kosztów związanych z działalnością. Zadania zarządzania kosztami są zazwyczaj wykonywane przez zespoły ds. finansów, zarządzania i aplikacji. Azure Cost Management i rozliczanie ułatwia organizacjom Planowanie z uwzględnieniem kosztów. Ułatwia ona również analizowanie kosztów i podejmowanie działań w celu zoptymalizowania wydatków na chmurę. Aby dowiedzieć się więcej na temat podejścia do zarządzania kosztami w organizacji, zapoznaj się z artykułem [Azure Cost Management best practices](./costs/cost-mgt-best-practices.md) (Najlepsze rozwiązania w usłudze Azure Cost Management).

Obejrzyj [wideo z omówieniem Azure Cost Management](https://www.youtube.com/watch?v=el4yN5cHsJ0) , aby zapoznać się z krótkim omówieniem, w jaki sposób Azure Cost Management może pomóc zaoszczędzić pieniądze na platformie Azure.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Mimo że te czynności są powiązane, rozliczanie różni się od zarządzania kosztami. Rozliczanie jest procesem fakturowania klientów za towary lub usługi oraz zarządzania relacjami komercyjnymi.

Usługa Cost Management przedstawia koszty i wzorce użycia w organizacji za pomocą zaawansowanej analizy. Raporty w Cost Management pokazują koszty użycia używane przez usługi platformy Azure i oferty w portalu Marketplace innych firm. Koszt jest oparty na wynegocjowanych cenach i współczynniku zastrzeżeń oraz Korzyść użycia hybrydowego platformy Azure rabatów. Zbiorczo raporty przedstawiają wewnętrzne i zewnętrzne koszty użycia i opłat w witrynie Azure Marketplace. Inne opłaty, takie jak zakupy rezerwacji, pomoc techniczna i podatki, nie są jeszcze uwzględniane w raportach. Raporty pomagają w zrozumieniu wydatków i użycia zasobów, a także ułatwiają wyszukiwanie nietypowych wydatków. Dostępna jest również analiza predykcyjna. Usługa Cost Management używa grup zarządzania, budżetów i rekomendacji platformy Azure, aby w czytelny sposób przedstawić, jak wydatki są zorganizowane i jak można zmniejszyć koszty.

Witryny Azure Portal i różnych interfejsów API można używać do automatyzacji eksportu w celu zintegrowania danych kosztów z procesami i systemami zewnętrznymi. Dostępna jest również funkcja zautomatyzowanego eksportu danych rozliczeń oraz raporty zaplanowane.

### <a name="plan-and-control-expenses"></a>Planowanie i kontrolowanie kosztów

Sposoby Cost Management pomóc w planowaniu i kontroli kosztów obejmują: analizowanie kosztów, budżety, zalecenia i eksportowanie danych zarządzania kosztami.

Analiza kosztów pozwala na eksplorowanie i analizowanie kosztów organizacyjnych. Można wyświetlić zagregowane koszty według organizacji, aby dowiedzieć się, gdzie występują koszty, i zidentyfikować trendy wydatków. Ponadto zakumulowane koszty w czasie można przeglądać w celu oszacowania miesięcznych, kwartalnych, a nawet rocznych trendów kosztów w odniesieniu do budżetu.

Budżety ułatwiają planowanie i spełnianie założeń odpowiedzialności finansowej w organizacji. Pomagają one zapobiegać przekroczeniom limitów lub progów kosztów. Budżety mogą również ułatwiać informowanie innych osób o ich wydatkach, aby aktywnie zarządzać kosztami. I można przy ich użyciu zobaczyć, jak kształtują się wydatki w czasie.

Rekomendacje pokazują, jak można przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane. Mogą również dotyczyć tańszych opcji zasobów. Pracując z rekomendacjami, zmieniasz sposób używania zasobów, aby zaoszczędzić pieniądze. Aby rozpocząć pracę, należy najpierw zapoznać się z rekomendacjami dotyczącymi optymalizacji kosztów, aby zidentyfikować potencjalne przypadki nieefektywnego użycia. Następnie należy skorzystać z rekomendacji, aby zmodyfikować użycie zasobów platformy Azure do tańszej opcji. Na koniec należy zweryfikować wykonane akcje, aby upewnić się, że wprowadzanie zmian zakończyło się powodzeniem.

Jeśli używasz systemów zewnętrznych do uzyskiwania dostępu do danych zarządzania kosztami lub ich przeglądania, możesz łatwo wyeksportować dane z platformy Azure. Możesz również ustawić dzienny zaplanowany eksport w formacie CSV i przechowywać pliki danych w usłudze Azure Storage. Dane będą w tej sytuacji dostępne z systemu zewnętrznego.

### <a name="consider-cloudyn"></a>Korzyści z użycia usługi Cloudyn

[Cloudyn](./cloudyn/overview.md) to usługa platformy Azure związana z usługą Cost Management. Usługa Cloudyn pozwala na śledzenie użycia chmury i związanych z nią wydatków dla zasobów platformy Azure. Obsługuje ona również innych dostawców chmury, w tym usługi AWS i Google. Łatwe do zrozumienia raporty pulpitu nawigacyjnego ułatwiają alokację kosztów oraz obsługę przewidywanych kosztów i obciążeń zwrotnych. Obecnie usługa Cost Management nie obsługuje przewidywanych kosztów/obciążeń zwrotnych ani innych dostawców usług w chmurze. Usługa Cloudyn to opcja, która _obsługuje_ te elementy. Obecnie Cost Management nie obsługuje kont Microsoft Cloud Service Provider (CSP), ale Cloudyn. Jeśli masz konta CSP lub chcesz używać przewidywanych kosztów/obciążenia zwrotnego, możesz użyć Cloudyn, aby pomóc w zarządzaniu kosztami.

Obejrzyj [Azure Cost Management i Cloudyn wideo](https://www.youtube.com/watch?v=PmwFWwSluh8) , aby zobaczyć zalecenia, gdy należy użyć Azure Cost Management lub Cloudyn, w zależności od potrzeb firmy.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

### <a name="additional-azure-tools"></a>Dodatkowe narzędzia platformy Azure

Platforma Azure ma inne narzędzia, które nie są częścią zestawu funkcji Azure Cost Management i rozliczeń. Pełnią one jednak ważną rolę w procesie zarządzania kosztami. Aby dowiedzieć się więcej o tych narzędziach, skorzystaj z poniższych linków.

- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) — to narzędzie umożliwia szacowanie początkowych kosztów chmury.
- [Usługa Azure Migrate](../migrate/migrate-overview.md) — umożliwia ocenianie bieżącego obciążenia centrum danych w celu uzyskania szczegółowych informacji o potrzebach w zakresie zastępczego rozwiązania platformy Azure.
- [Usługa Azure Advisor](../advisor/advisor-overview.md) — umożliwia zidentyfikowanie nieużywanych maszyn wirtualnych i zapoznanie się z rekomendacjami w zakresie zakupów wystąpień zarezerwowanych platformy Azure.
- [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) — umożliwia oszczędzanie dzięki użyciu bieżących licencji systemu Windows Server lub programu SQL Server dla maszyn wirtualnych na platformie Azure.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już Cost Management i rozliczanie, następnym krokiem jest rozpoczęcie korzystania z usługi.

- Zacznij korzystać z usługi Azure Cost Management, aby [analizować koszty](./costs/quick-acm-cost-analysis.md).
- Możesz również dowiedzieć się więcej na temat [najlepszych rozwiązań w usłudze Azure Cost Management](./costs/cost-mgt-best-practices.md).
