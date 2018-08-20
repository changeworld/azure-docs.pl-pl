---
title: Tworzenie usługi Azure Search w portalu | Microsoft Docs
description: Aprowizowanie usługi Azure Search w portalu.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: heidist
ms.openlocfilehash: 83e34f926ec3133f89ccb90979fc3e6708613a80
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713931"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Tworzenie usługi Azure Search w portalu

Informacje o sposobie tworzenia i aprowizacji usługi Azure Search w portalu. 

Preferujesz program PowerShell? Użyj [szablonu usługi](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manager. Jeśli potrzebujesz pomocy, aby rozpocząć, zapoznaj się z artykułem [Manage Azure Search with PowerShell](search-manage-powershell.md) (Zarządzanie usługą Azure Search przy użyciu programu PowerShell).

## <a name="subscribe-free-or-paid"></a>Subskrypcja (bezpłatna lub płatna)

[Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) i skorzystaj z bezpłatnych środków, aby wypróbować płatne usługi platformy Azure. Po wyczerpaniu środków zachowaj konto i nadal korzystaj z bezpłatnych usług platformy Azure, takich jak Websites. Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.

Możesz również [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). W ramach subskrypcji MSDN co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure. 

## <a name="find-azure-search"></a>Znajdowanie usługi Azure Search
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij znak plus („+ Utwórz zasób”) w lewym górnym rogu.
3. Wybierz pozycję **Internet** > **Azure Search**.

![](./media/search-create-service-portal/find-search3.png)

## <a name="name-the-service-and-url-endpoint"></a>Tworzenie nazwy usługi i punktu końcowego adresu URL

Nazwa usługi jest częścią punktu końcowego adresu URL, względem którego interfejs API wywołuje polecenia: `https://your-service-name.search.windows.net`. Wprowadź nazwę usługi w polu **Adres URL**. 

Wymagania dotyczące nazwy usługi:
   * Musi być unikatowa w obrębie przestrzeni nazw search.windows.net
   * Długość musi zawierać się w przedziale od 2 do 60 znaków
   * Musi składać się z małych liter, cyfr lub łączników („-”)
   * Nie może zawierać łączników („-”) na pierwszych dwóch miejscach ani na ostatnim miejscu
   * Nie może zawierać następujących po sobie łączników („--”)

## <a name="select-a-subscription"></a>Wybieranie subskrypcji
Jeśli masz więcej niż jedną subskrypcję, wybierz tę, która obejmuje usługi magazynu danych lub plików. Usługa Azure Search może automatycznie wykrywać magazyn tabel i obiektów blob platformy Azure, bazę danych SQL i usługę Azure Cosmos DB na potrzeby indeksowania za pośrednictwem [*indeksatorów*](search-indexer-overview.md), ale wyłącznie w ramach usług w tej samej subskrypcji.

## <a name="select-a-resource-group"></a>Wybieranie grupy zasobów
Grupa zasobów jest kolekcją używanych razem usług i zasobów platformy Azure. Jeśli na przykład używasz usługi Azure Search do indeksowania bazy danych SQL, obie usługi powinny należeć do tej samej grupy zasobów.

> [!TIP]
> Usunięcie grupy zasobów powoduje usunięcie zawartych w niej usług. Umieszczenie w tej samej grupie zasobów wszystkich projektów będących prototypami korzystającymi z wielu usług ułatwia proces czyszczenia po zakończeniu projektu. 

## <a name="select-a-hosting-location"></a>Wybieranie lokalizacji hostowania 
W związku z tym, że usługa Azure Search należy do usług platformy Azure, może być hostowana w centrach danych na całym świecie. Należy pamiętać, że [ceny mogą się różnić](https://azure.microsoft.com/pricing/details/search/) w zależności od lokalizacji geograficznej.

## <a name="select-a-pricing-tier-sku"></a>Wybieranie warstwy cenowej (jednostek SKU)
[Usługa Azure Search jest obecnie dostępna w wielu warstwach cenowych](https://azure.microsoft.com/pricing/details/search/): bezpłatnej, podstawowej i standardowej. Poszczególne warstwy różnią się między sobą [pojemnością i limitami](search-limits-quotas-capacity.md). Aby uzyskać wskazówki, zobacz [Choose a pricing tier or SKU](search-sku-tier.md) (Wybieranie warstwy cenowej lub jednostek SKU).

Dla obciążeń produkcyjnych zwykle wybierana jest warstwa Standardowa, ale większość klientów zaczyna od bezpłatnej usługi.

Po utworzeniu usługi nie można zmienić warstwy cenowej. W razie konieczności przejścia do warstwy wyższej lub niższej należy ponownie utworzyć usługę.

## <a name="create-your-service"></a>Tworzenie usługi

Należy pamiętać o przypięciu usługi do pulpitu nawigacyjnego, aby mieć do niej łatwy dostęp po każdym zalogowaniu.

![](./media/search-create-service-portal/new-service3.png)

## <a name="scale-your-service"></a>Skalowanie usługi
Tworzenie usługi może potrwać kilka minut (15 minut lub dłużej w zależności od warstwy). Po aprowizacji usługi można ją skalować stosownie do potrzeb. Ponieważ dla usługi Azure Search wybrano warstwę standardową, można ją skalować w dwóch wymiarach: replik i partycji. Wybór warstwy podstawowej oznaczałby, że można dodawać wyłącznie repliki. W razie aprowizacji bezpłatnej usługi skalowanie nie jest dostępne.

***Partycje*** umożliwiają usłudze przechowywanie i przeszukiwanie większej liczby dokumentów.

***Repliki*** umożliwiają usłudze obsługę większego obciążenia zapytaniami wyszukiwania.

Dodawanie zasobów wiąże się z dodaniem opłat do rachunku miesięcznego. [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) pomaga w określeniu, jaki wpływ na rozliczenia będzie miało dodanie zasobów. Pamiętaj, że możesz też dostosowywać zasoby do obciążenia. Możesz na przykład zwiększyć ilość zasobów na czas początkowego tworzenia pełnego indeksu, a następnie zmniejszyć ją do poziomu dostosowanego do potrzeb indeksowania przyrostowego.

> [!Important]
> Usługa musi mieć [2 repliki w ramach umowy SLA tylko do odczytu oraz 3 repliki w ramach umowy SLA do odczytu/zapisu](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Przejdź do strony usługi wyszukiwania w witrynie Azure Portal.
2. W okienku nawigacji po lewej wybierz kolejno pozycje **Ustawienia** > **Skalowanie**.
3. Użyj suwaka, aby dodać zasoby wybranego typu.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Każda warstwa ma inne [limity](search-limits-quotas-capacity.md) całkowitej liczby jednostek wyszukiwania dozwolonych w ramach jednej usługi (repliki * partycje = suma jednostek wyszukiwania).

## <a name="when-to-add-a-second-service"></a>Kiedy należy dodać drugą usługę

Większość klientów używa tylko jednej usługi aprowizowanej dla danej warstwy, która zapewnia [właściwe rozmieszczenie zasobów](search-sku-tier.md). Jedna usługa może obsługiwać wiele indeksów, z zastrzeżeniem [maksymalnych limitów wybranej warstwy](search-capacity-planning.md), przy czym każdy indeks jest odizolowany od innych. W usłudze Azure Search żądania mogą być kierowane tylko do jednego indeksu, minimalizując tym samym ryzyko przypadkowego lub zamierzonego pobierania danych z innych indeksów tej samej usługi.

Chociaż większość klientów używa tylko jednej usługi, nadmiarowość usług może być konieczna, jeśli wymagania operacyjne są następujące:

+ Odzyskiwanie po awarii (awaria centrum danych). Usługa Azure Search nie zapewnia natychmiastowego przejścia do trybu failover w przypadku awarii. Aby uzyskać wskazówki i zalecenia, zobacz [Service administration](search-manage.md) (Administrowanie usługą).
+ W trakcie badania modelowania wielodostępnego ustalono, że dodatkowe usługi stanowią optymalne rozwiązanie dla danego projektu. Aby uzyskać więcej informacji, zobacz [Design for multi-tenancy](search-modeling-multitenant-saas-applications.md) (Projektowanie na potrzeby wielodostępu).
+ Aplikacje wdrażane globalnie mogą wymagać wystąpienia usługi Azure Search w wielu regionach w celu zminimalizowania opóźnienia w ruchu międzynarodowym aplikacji.

> [!NOTE]
> W usłudze Azure Search nie można oddzielić obciążeń indeksujących i wykonujących zapytania, dlatego nie ma możliwości utworzenia wielu usług dla segregowanych obciążeń. Zapytania względem indeksu zawsze dotyczą usługi, w której został utworzony (nie można utworzyć indeksu w jednej usłudze, by następnie skopiować go do innej).
>

Druga usługa nie jest wymagana w celu zapewnienia wysokiej dostępności. Wysoka dostępność zapytań ma miejsce wtedy, gdy używane są co najmniej 2 repliki w tej samej usłudze. Repliki są aktualizowane w odpowiedniej kolejności, co oznacza, że podczas wdrażania aktualizacji usługi działa co najmniej jedna replika. Aby uzyskać więcej informacji na temat czasu pracy, zobacz [Umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Następne kroki
Po aprowizacji usługi Azure Search można przystąpić do [definiowania indeksu](search-what-is-an-index.md) w celu przekazywania i wyszukiwania danych. 

> [!div class="nextstepaction"]
> [Jak używać usługi Azure Search na platformie .NET](search-howto-dotnet-sdk.md)
