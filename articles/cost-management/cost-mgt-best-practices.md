---
title: Optymalizowanie inwestycję w chmurę dzięki usłudze Azure Cost Management | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga osiągnięcie jak największych korzyści z inwestycji w chmurę, zmniejszenia kosztów i ocenić, gdzie jest poświęcony pieniądze.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 7c562e6f0a1358d16b9abef08a5e582e4ff84472
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002039"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Jak zoptymalizować inwestycję w chmurę dzięki usłudze Azure Cost Management

Usługa Azure Cost Management zapewnia narzędzia do planowania, analizowania i Zmniejsz wydatki w celu maksymalizacji inwestycji w chmurę. W tym dokumencie przedstawiono metodyczny podejście do zarządzania kosztami oraz wyróżnia narzędzia dostępne dla użytkownika jako adresu wyzwania koszt Twojej organizacji. Dzięki systemowi Azure ułatwia tworzenie i wdrażanie rozwiązań w chmurze. Jest ważne, że te rozwiązania są optymalizowane w celu zminimalizowania kosztów dla organizacji. Zgodnie z zasadami opisane w tym dokumencie i za pomocą naszych narzędzi ma na celu upewnić się, że Twoja organizacja jest gotowa do poprawnego działania.

## <a name="methodology"></a>Metodologia

Usługa Cost management organizacji problemu i powinna być trwająca praktyka, która rozpoczyna się przed można kupować zasobów w chmurze. Aby pomyślnie wdrożyć Zarządzanie kosztami i Optymalizowanie kosztów, Twoja organizacja musi:

- Należy przygotować za pomocą odpowiednich narzędzi w celu osiągnięcia sukcesu
- Odpowiada za koszty
- Podejmij odpowiednią akcję, aby zoptymalizować wydatki

Trzy grupy kluczy opisane poniżej, muszą być wyrównane w Twojej organizacji, aby upewnić się, czy pomyślnie Zarządzanie kosztami.

- **Finanse** -osób odpowiedzialnych za zatwierdzanie żądań budżetu w organizacji oparte na chmurze wydatków prognozy. One zapłacić rachunku odpowiedniego i przypisać koszty do różnych zespołów na zapewnienie odpowiedzialności.
- **Menedżerowie** — biznesowe osoby podejmujące decyzje w organizacji, które są niezbędne do zrozumienia chmury wydatków, aby znaleźć najlepszy wydatków wyników.
- **Zespoły aplikacji mogą** — inżynierów zarządzania zasobami chmury na podstawie codziennych, tworzenia usług w celu spełnienia potrzeb organizacji użytkownika. Te zespoły muszą mieć możliwość dostarczania największych korzyści z ich budżetów zdefiniowane.

### <a name="key-principles"></a>Podstawowe zasady

Użyj zasad przedstawionych poniżej w celu umieść organizacji w celu osiągnięcia sukcesu w chmurze kosztów zarządzania.

#### <a name="planning"></a>Planowanie

Kompleksowe, ponoszonych z góry planowania pozwala na dostosowywanie użycie chmury do konkretnych potrzeb biznesowych. Zadaj sobie:

- Jaki problem biznesowy jestem rozwiązywania?
- Wzorce użycia, jakie oczekiwać od Moje zasoby?

Twoje odpowiedzi pomogą wybierz oferty, które są dla Ciebie odpowiednia. Określają one infrastruktury i sposobie ich użycia, aby zmaksymalizować wydajność platformy Azure.

#### <a name="visibility"></a>Widoczność

Gdy dobrze pełną Cost Management pomaga informował o koszty platformy Azure, w których są one odpowiedzialnej za pieniądze, jaki poświęcają lub. Platforma Azure oferuje usługi, umożliwiają wgląd w *gdzie* odbywa się pieniądze. Skorzystaj z tych narzędzi. Mogą one pomóc Ci znaleźć zasoby, które są w pełni wykorzystany, Usuń strat i maksymalizowanie oszczędne możliwości.

#### <a name="accountability"></a>Odpowiedzialność

Atrybut koszty w Twojej organizacji, aby upewnić się, że osoby odpowiedzialne wykonywanych przez ich zespołu wydatków. Aby w pełni zrozumieć wydatków platformy Azure w Twojej organizacji, należy zorganizować zasobów w celu zmaksymalizowania wgląd w koszty autorstwa. Dobre organizacji ułatwia zarządzanie i obniżenie kosztów i przytrzymaj osób odpowiedzialni wydajne wydatków w Twojej organizacji.

#### <a name="optimization"></a>Optymalizacja

Działanie, aby ograniczyć wydatki. Maksymalne wykorzystanie go na podstawie otrzymanych wyników zebrane przez proces planowania i Zwiększanie widoczności kosztów. Należy rozważyć zakup i licencjonowania optymalizacje wraz z infrastruktury wdrażania zmiany, które opisano szczegółowo w dalszej części tego dokumentu.

#### <a name="iteration"></a>Iteracja

Wszyscy w Twojej organizacji muszą angażować się w cyklu życia zarządzania kosztami. Muszą one pozostać zaangażowanych w sposób ciągły, aby zoptymalizować koszty. Być rygorystyczne o ten proces iteracyjny i ułatwiają obejmując nadzoru odpowiada chmury w organizacji.

![Podstawowe zasady diagram przedstawiający widoczność, odpowiedzialność i optymalizacja](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Planowanie kosztów na uwadze

Przed przystąpieniem do wdrażania zasobów w chmurze, oceń następujące elementy:

- Oferta platformy Azure, która najlepiej spełnia Twoje potrzeby
- Zasoby, których zamierzasz używać
- Ile może kosztować

System Azure oferuje narzędzia pomocne podczas w procesie oceny. Narzędzia można nadać dobrze inwestycje wymagane do włączenia obciążeń. Następnie możesz wybrać configuration najlepiej w danej sytuacji.

### <a name="azure-onboarding-options"></a>Opcje dołączania do platformy Azure

Pierwszym etapem maksymalizując środowiska w ramach Cost Management jest badanie i zdecyduj, która oferta platformy Azure jest odpowiedni dla Ciebie. Zastanów się, jak zamierzasz w przyszłości korzystać z platformy Azure. Należy również rozważyć, jak chcesz, aby Twój model rozliczeń, które zostały skonfigurowane. Podczas podejmowania decyzji, należy wziąć pod uwagę następujące kwestie:

- Jak długo planujesz korzystanie z systemu Azure? Jestem testowania, czy zaplanować do tworzenia długoterminowych infrastruktury?
- Jak chcesz zapłacić dla platformy Azure? Należy I Zapłać z góry za obniżonej cenie lub uzyskaj doliczany do faktury na końcu miesiąca?

Aby dowiedzieć się więcej na temat różnych opcji, odwiedź stronę [jak kupić platformę Azure](https://azure.microsoft.com/pricing/purchase-options/). Kilka typowych modeli rozliczeń są identyfikowane poniżej.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Bezpłatna](https://azure.microsoft.com/free/)

- 12 miesięcy z popularnych bezpłatnych usług
- 200 USD środków w celu poznania usług przez 30 dni
- ponad 25 usług zawsze są bezpłatne

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p)

- Brak zobowiązań minimalnych
- Konkurencyjne ceny
- Płatność wyłącznie za rzeczywiste użycie
- Możliwość anulowania w dowolnej chwili

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Umowy Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Opcje ponoszonych z góry zobowiązań pieniężnych
- Dostęp do obniżonej ceny systemu Azure

## <a name="estimate-the-cost-of-your-solution"></a>Oszacuj koszt rozwiązania

Przed wdrożeniem infrastruktury ocenić rozwiązania ile będzie kosztować. Oceny są pomocne podczas tworzenia budżetu dla Twojej organizacji, w przypadku obciążeń z góry. Następnie przy użyciu budżetu wraz z upływem czasu przeprowadzenie testu porównawczego ważności Twoje początkowe oszacowanie. I porównać go z rzeczywisty koszt rozwiązania wdrożone.

### <a name="azure-pricing-calculator"></a>Kalkulator cen platformy Azure

Kalkulator cen platformy Azure umożliwia mieszanie i dopasowywanie różne kombinacje usług platformy Azure, aby wyświetlić oszacowanie kosztów. Można zaimplementować rozwiązania przy użyciu różnych sposobów na platformie Azure — każdy może mieć wpływ na ogólnych wydatków. Ułatwiały wcześnie wszystkie wdrożenia w chmurze w zakresie infrastruktury pomoże Ci najbardziej efektywnie za pomocą narzędzia. Może pomóc Ci solid oszacowanie szacowane wydatki na platformie Azure.

Aby uzyskać więcej informacji, zobacz [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Usługa Azure Migrate to usługa, która ocenia organizacji bieżących obciążeń w lokalnych centrach danych. Udostępnia wgląd w czym może być konieczne z rozwiązania Azure zastępczy. Po pierwsze Migrate analizuje maszyn lokalnych do ustalenia, czy migracja jest to możliwe. Następnie zaleca rozmiaru maszyny Wirtualnej na platformie Azure w celu zmaksymalizowania wydajności. Na koniec tworzy również szacowania kosztów dla rozwiązania oparte na platformie Azure.

Aby uzyskać więcej informacji, zobacz [usługi Azure Migrate](../migrate/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analizowanie i zarządzanie nimi z tego względu koszt

Śledź na bieżąco jak koszty swojej organizacji rozwijać się wraz z upływem czasu. Za pomocą następujących technik prawidłowo oraz zarządzać swoimi wydatkami.

### <a name="organize-and-tag-your-resources"></a>Organizowanie i tagowanie zasobów

Organizowanie zasobów przy użyciu koszt na uwadze. Podczas tworzenia subskrypcji i grup zasobów, należy wziąć pod uwagę zespołów, które są odpowiedzialne za powiązanych z nimi kosztów. Upewnij się, że raportowania przechowuje organizacji na uwadze. Subskrypcji i grup zasobów zapewniają dobrą zasobników do organizowania i atrybutu wydatków w ramach organizacji. Tagi umożliwiają dobre koszt atrybutu. Tagi można użyć jako filtr. I używać ich do grupy podczas analizowania danych oraz badanie kosztów. Klienci z umową Enterprise Agreement mogą również utworzyć działów i umieścić subskrypcje w ramach ich. Na podstawie kosztów organizacji na platformie Azure pomaga odpowiednich osób w Twojej organizacji odpowiedzialni zmniejszenie wydatków ich zespołu.

### <a name="use-cost-analysis"></a>Użyj analizy kosztów

Analiza kosztów pozwala analizować szczegółowe koszty organizacji, dzielenie i grupowanie koszty przy użyciu właściwości standardowych zasobów. Przewodnik analizy, należy wziąć pod uwagę poniższe często zadawane pytania. Odpowiedzi na te pytania, na bieżąco pomoże Ci na bieżąco bardziej i włączenia więcej koszt świadome decyzje.

- **Szacowane koszty w bieżącym miesiącu** — ile poniesionymi do tej pory w tym miesiącu? Będą przechowywane w ramach budżetu?
- **Badanie anomalii** — czy rutynowych testów, aby upewnić się, że koszty pozostają w zakresie uzasadnione normalnego użycia. Jakie są trendy? Czy istnieją jakiekolwiek elementy odstające?
- **Faktury uzgadniania** — jest Moje najnowsze invoiced droższe niż poprzedniego miesiąca? Jak wydatków nawyków zmienić miesiąc do miesiąca?
- **Wewnętrzny obciążenia zwrotnego** — teraz, aby sprawdzić, ile opłata została naliczona, jak należy je podzielić dla mojej organizacji?

Aby uzyskać więcej informacji, zobacz [analiza kosztów](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Eksportowanie danych dotyczących rozliczeń zgodnie z harmonogramem

Potrzebujesz do importowania danych dotyczących rozliczeń do systemu zewnętrznego, podobnie jak pulpit nawigacyjny lub finansowych systemu? Skonfiguruj automatyczne eksportowanie do usługi Azure Storage i uniknąć ręcznego pobierania plików co miesiąc. Możesz następnie łatwo skonfigurować automatyczne integracji z innymi systemami do synchronizowania danych dotyczących rozliczeń.

Aby uzyskać więcej informacji na temat eksportowania danych dotyczących rozliczeń, zobacz [tworzenie i zarządzanie nimi wyeksportowane dane](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Tworzenie budżetów

Po utworzeniu zidentyfikowane i analizowane Twoich wzorców wydatków, należy rozpocząć Ustawianie limitów dla siebie i Twoje zespoły. Budżetów platformy Azure zapewniają możliwość ustawiania koszt lub na podstawie użycia budżetu za pomocą wielu progów i alertów. Upewnij się zapoznać się z budżety, które tworzysz regularnie Wyświetl postęp budżetu postępów w dół i wprowadź wymagane zmiany. Azure budżetów pozwalają również na konfigurowanie wyzwalacza usługi automation, po osiągnięciu progu przez dany budżetu. Na przykład można skonfigurować usługi w celu wyłączania maszyn wirtualnych. Lub infrastruktury można przenieść do innej warstwy cenowej w odpowiedzi na wyzwalacz budżetu.

Aby uzyskać więcej informacji, zobacz [budżetów Azure](tutorial-acm-create-budgets.md).

Aby uzyskać więcej informacji o automatyzacji w budżecie, zobacz [budżetu na podstawie Automation](../billing/billing-cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Działania w celu optymalizacji
Użyj poniższych metod Optymalizowanie wydatków.

### <a name="cut-out-waste"></a>Wytnij strat

Po wdrożeniu infrastruktury na platformie Azure, koniecznie upewnij się, że jest on używany. Najprostszym sposobem zacznij oszczędzać natychmiast jest przejrzeć swoje zasoby i usunąć z nich, które nie są używane. W tym miejscu należy określić, jeśli zasoby są używane jak najbardziej wydajny.

#### <a name="azure-advisor"></a>Azure Advisor

Usługa Azure Advisor to usługa, która między innymi identyfikuje maszyny wirtualne z niskim użyciem z punktu widzenia użycia procesora CPU lub sieci. Z tego miejsca możesz zdecydować się na zamknięty lub zmienianie rozmiaru maszyny, oparte na szacowany koszt nadal będą uruchamiane na maszynach. Advisor zawiera także zalecenia dotyczące zakupów wystąpień zarezerwowanych. Zalecenia są oparte na przez ostatnie 30 dni użytkowania maszyny wirtualnej. Gdy zrealizowany, zalecenia może spowodować zmniejszenie wydatków.

Aby uzyskać więcej informacji, zobacz [usługi Azure Advisor](../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Poprawnie rozmiar maszyn wirtualnych

Ustalanie rozmiaru maszyny Wirtualnej ma znaczący wpływ na koszt całkowity platformy Azure. Liczbę maszyn wirtualnych potrzebnych na platformie Azure nie może być równa się co aktualnie wdrożone w lokalnym centrum danych. Upewnij się, że Twoje wybierz rozmiar odpowiednie dla obciążeń, które mają być uruchamiane.

Aby uzyskać więcej informacji, zobacz [IaaS platformy Azure: odpowiedniego rozmiaru i kosztów](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Użyj rabaty zakupu

Platforma Azure oferuje wiele rabatów, które Twoja organizacja powinna korzystać z oszczędzać pieniądze.

#### <a name="azure-reservations"></a>Rezerwacje platformy Azure

Rezerwacje platformy Azure umożliwiają Zapłać z góry za rok lub trzech lat maszyny wirtualnej lub bazy danych SQL moc obliczeniową. Wstępnie płacenia pozwoli uzyskać rabat na zasoby, których używasz. Azure rezerwacji może znacznie zmniejszyć Twojej maszyny wirtualnej lub koszty operacji obliczeniowych bazy danych SQL — do 72% na ceny zgodnie z rzeczywistym użyciem dzięki jeden rok lub trzy lata zobowiązań z góry. rezerwacje Podaj rozliczeń rabat, a nie wpływają na stan środowiska uruchomieniowego maszyn wirtualnych ani baz danych SQL.

Aby uzyskać więcej informacji, zobacz [co to jest Azure rezerwacje?](../billing/billing-save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Za pomocą korzyści użycia hybrydowego platformy Azure

Jeśli już masz licencje systemu Windows Server lub SQL Server we wdrożeniach w środowisku lokalnym, można użyć programu korzyści użycia hybrydowego platformy Azure, można zapisać na platformie Azure. Korzyści systemu Windows Server z każdej licencji po awarii obejmuje koszt systemu operacyjnego (maksymalnie dwóch maszynach wirtualnych), a ponosisz tylko koszty według podstawowej stawki obliczeniowej. Korzystając z istniejących licencji programu SQL Server, można zapisać do 55 procent na opcjach usługi SQL Database oparty na rdzeniach wirtualnych. Opcje obejmują program SQL Server w usłudze Azure Virtual Machines i usług SQL Server Integration Services.

Aby uzyskać więcej informacji, zobacz [Kalkulator korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Inne zasoby

Platforma Azure ma to usługa, która umożliwia tworzenie usług, które wykorzystują nadwyżkę wydajności na platformie Azure dla obniżonych stawek. Aby uzyskać więcej informacji, zobacz [— niski priorytet maszyn wirtualnych za pomocą usługi Batch](../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Kolejne kroki
- Jeśli jesteś nowym użytkownikiem Cost Management, zapoznaj się z [co to jest Azure Cost Management?](overview-cost-mgt.md) Dowiedz się, jak ułatwia monitorowanie i kontrolowanie wydatków platformy Azure i zoptymalizować wykorzystanie zasobów.
