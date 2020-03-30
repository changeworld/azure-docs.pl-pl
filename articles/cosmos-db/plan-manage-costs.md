---
title: Planowanie kosztów usługi Azure Cosmos DB i zarządzanie nimi
description: Dowiedz się, jak planować koszty usługi Azure Cosmos DB i zarządzać nimi przy użyciu analizy kosztów w witrynie Azure portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152588"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planowanie kosztów usługi Azure Cosmos DB i zarządzanie nimi

W tym artykule opisano, jak można planować i zarządzać kosztami usługi Azure Cosmos DB. Najpierw należy użyć kalkulatora pojemności usługi Azure Cosmos DB, aby ułatwić planowanie kosztów przed dodaniem jakichkolwiek zasobów. Następnie podczas dodawania zasobów platformy Azure można przejrzeć szacowane koszty. Po rozpoczęciu korzystania z zasobów usługi Azure Cosmos DB użyj funkcji zarządzania kosztami, aby ustawić budżety i monitorować koszty. Można również przejrzeć prognozowane koszty i zidentyfikować trendy wydatków, aby zidentyfikować obszary, w których możesz chcieć działać.

Należy pamiętać, że koszty usługi Azure Cosmos DB są tylko część miesięcznych kosztów na rachunku platformy Azure. Jeśli korzystasz z innych usług platformy Azure, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym za usługi innych firm. W tym artykule wyjaśniono, jak planować koszty usługi Azure Cosmos DB i zarządzać nimi. Po zapoznaniu się z zarządzaniem kosztami usługi Azure Cosmos DB, można zastosować podobne metody zarządzania kosztami dla wszystkich usług platformy Azure używanych w ramach subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>Przejrzyj szacowane koszty za pomocą kalkulatora pojemności

Użyj [kalkulatora pojemności usługi Azure Cosmos DB,](https://cosmos.azure.com/capacitycalculator/) aby oszacować koszty przed utworzeniem zasobów na koncie usługi Azure Cosmos. Kalkulator pojemności służy do oszacowania wymaganej przepływności i kosztów obciążenia. Konfigurowanie baz danych i kontenerów usługi Azure Cosmos z odpowiednią ilością aprowizowanej przepływności lub [jednostek żądań (RU/s)](request-units.md)dla obciążenia jest niezbędne do optymalizacji kosztów i wydajności. Musisz wprowadzić szczegóły, takie jak typ interfejsu API, liczba regionów, rozmiar elementu, żądania odczytu/zapisu na sekundę, całkowite dane przechowywane w celu uzyskania oszacowania kosztów. Aby dowiedzieć się więcej o kalkulatorze pojemności, zobacz artykuł [szacowany.](estimate-ru-with-capacity-planner.md)

Poniższy zrzut ekranu przedstawia szacowanie przepływności i kosztów przy użyciu kalkulatora pojemności:

![Szacowanie kosztów w kalkulatorze pojemności usługi Azure Cosmos DB](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Przeglądanie szacowanych kosztów z witryny Azure portal

Podczas tworzenia zasobów usługi Azure Cosmos DB z witryny Azure portal można zobaczyć szacowane koszty. Aby przejrzeć kosztoszywę, należy wykonać następujące czynności:

1. Zaloguj się do witryny Azure portal i przejdź do konta usługi Azure Cosmos.
1. Przejdź do **Eksploratora danych**.
1. Utwórz nowy kontener, taki jak kontener wykresu.
1. Wprowadź przepływność wymaganą dla obciążenia, taką jak 400 RU/s. Po wprowadzeniu wartości przepływności można wyświetlić oszacowanie cen, jak pokazano na poniższym zrzucie ekranu:

   ![Szacowanie kosztów w witrynie Azure portal](./media/plan-manage-costs/cost-estimate-portal.png)

Jeśli twoja subskrypcja platformy Azure ma limit wydatków, platforma Azure uniemożliwia wydawanie ponad kwotę kredytu. Podczas tworzenia i używania zasobów platformy Azure używane są kredyty. Po osiągnięciu limitu kredytowego wdrożone zasoby są wyłączone przez pozostałą część tego okresu rozliczeniowego. Nie można zmienić limitu kredytowego, ale możesz go usunąć. Aby uzyskać więcej informacji na temat limitów wydatków, zobacz [Limit wydatków platformy Azure](../billing/billing-spending-limit.md).

## <a name="use-budgets-and-cost-alerts"></a>Używanie alertów dotyczących budżetu i kosztów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management/tutorial-acm-create-budgets.md) oraz alerty, które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, więc są one przydatne w ramach ogólnej strategii monitorowania kosztów. Jednak mogą one mieć ograniczoną funkcjonalność do zarządzania poszczególnymi kosztami usługi platformy Azure, takich jak koszt usługi Azure Cosmos DB, ponieważ są one przeznaczone do śledzenia kosztów na wyższym poziomie.

## <a name="monitor-costs"></a>Monitoruj koszty

Podczas korzystania z zasobów z usługi Azure Cosmos DB, ponieść koszty. Koszty jednostkowe użycia zasobów różnią się w zależności od przedziałów czasu (sekundy, minuty, godziny i dni) lub użycia jednostki żądania. Jak tylko rozpocznie się korzystanie z usługi Azure Cosmos DB, koszty są ponoszone i można je zobaczyć w okienku [analizy kosztów](../cost-management/quick-acm-cost-analysis.md) w witrynie Azure portal.

Korzystając z analizy kosztów, można wyświetlić koszty usługi Azure Cosmos DB na wykresach i tabelach dla różnych przedziałów czasu. Niektóre przykłady to dzień, bieżący, poprzedni miesiąc i rok. Można również wyświetlać koszty w stosunku do budżetów i prognozowanych kosztów. Przełączanie się na dłuższe widoki w czasie może pomóc w zidentyfikowaniu trendów wydatków i zobaczeniu, gdzie mogło dojść do nadmiernych wydatków. Jeśli budżety zostały utworzone, możesz również łatwo zobaczyć, gdzie zostały przekroczone. Aby wyświetlić koszty usługi Azure Cosmos DB w analizie kosztów:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).

1. Otwórz okno **Zarządzanie kosztami + Rozliczenia,** wybierz z menu **pozycję Zarządzanie kosztami,** a następnie wybierz pozycję **Analiza kosztów**. Następnie można zmienić zakres dla określonej subskrypcji z listy rozwijanej **Zakres.**

1. Domyślnie koszt wszystkich usług jest wyświetlany na pierwszym wykresie pierścieniowym. Wybierz obszar na wykresie oznaczony jako "Azure Cosmos DB".

1. Aby zawęzić koszty dla pojedynczej usługi, takiej jak Usługa Azure Cosmos DB, wybierz pozycję **Dodaj filtr,** a następnie wybierz **pozycję Nazwa usługi**. Następnie wybierz z listy **pozycję Usługa Azure Cosmos DB.** Oto przykład przedstawiający koszty tylko usługi Azure Cosmos DB:
 
   ![Monitorowanie kosztów za pomocą okienka Analiza kosztów](./media/plan-manage-costs/cost-analysis-pane.png)

W poprzednim przykładzie zobaczysz bieżący koszt usługi Azure Cosmos DB w miesiącu luty. Wykresy zawierają również koszty usługi Azure Cosmos DB według lokalizacji i według grupy zasobów.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat funkcjonowania cen w usłudze Azure Cosmos DB:

* [Model cen w usłudze Azure Cosmos DB](how-pricing-works.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](optimize-cost-queries.md)
* [Optymalizacja kosztów magazynowania w usłudze Azure Cosmos DB](optimize-cost-storage.md)