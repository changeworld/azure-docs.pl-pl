---
title: 'Szybki start: Tworzenie usługi Azure Search w portalu — Azure Search'
description: Aprowizowanie zasobu usługi Azure Search w witrynie Azure Portal. Wybierz grupy zasobów, regiony i jednostki SKU lub warstwę cenową.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 483810f89ea4bbb3a68e616929bd7d752c4d509f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883852"
---
# <a name="quickstart-create-an-azure-search-service-in-the-portal"></a>Szybki start: Tworzenie usługi Azure Search w portalu

Usługa Azure Search jest zasobem autonomicznym służącym do dołączania funkcji wyszukiwania do aplikacji niestandardowych. Chociaż Azure Search łatwo integruje się z innymi usługami platformy Azure, można go również użyć jako składnika autonomicznego lub zintegrować go z aplikacjami na serwerach sieciowych lub z oprogramowaniem działającym na innych platformach w chmurze.

Ten artykuł zawiera informacje dotyczące tworzenia zasobu usługi Azure Search w [portalu Azure](https://portal.azure.com/).

[![Animowany obraz GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Preferujesz program PowerShell? Użyj [szablonu usługi](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manager. Aby uzyskać pomoc dotyczącą rozpoczynania pracy, zobacz [Zarządzanie usługą Azure Search przy użyciu programu PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Subskrypcja (bezpłatna lub płatna)

[Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) i skorzystaj z bezpłatnych środków, aby wypróbować płatne usługi platformy Azure. Po wyczerpaniu środków zachowaj konto i nadal korzystaj z bezpłatnych usług platformy Azure, takich jak Websites. Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.

Możesz również [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). W ramach subskrypcji MSDN co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure. 

## <a name="find-azure-search"></a>Znajdowanie usługi Azure Search

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij znak plus („+ Utwórz zasób”) w lewym górnym rogu.
3. Za pomocą paska wyszukiwania znajdź ciąg „Azure Search” lub przejdź do zasobu, wybierając opcje **Sieć Web** > **Azure Search**.

![Przechodzenie do zasobu usługi Azure Search](./media/search-create-service-portal/find-search3.png "Ścieżka nawigacji do usługi Azure Search")

## <a name="select-a-subscription"></a>Wybierz subskrypcję

Jeśli masz więcej niż jedną subskrypcję, wybierz tę, która obejmuje usługi magazynu danych lub plików. Azure Search mogą automatycznie wykrywać usługę Azure Table i BLOB Storage, SQL Database i Azure Cosmos DB do indeksowania za pośrednictwem [*indeksatorów*](search-indexer-overview.md), ale tylko dla usług w ramach tej samej subskrypcji.

## <a name="set-a-resource-group"></a>Ustawianie grupy zasobów

Grupa zasobów jest wymagana i jest przydatna do zarządzania zasobami, w tym zarządzania kosztami. Grupa zasobów może składać się z jednej usługi lub wielu używanych jednocześnie usług. Na przykład, jeśli używasz Azure Search do indeksowania bazy danych Azure Cosmos DB, można utworzyć obie usługi należące do tej samej grupy zasobów na potrzeby zarządzania. 

Jeśli nie łączysz zasobów w pojedynczą grupę lub jeśli istniejące grupy zasobów zawierają zasoby używane w niezwiązanych ze sobą rozwiązaniach, utwórz nową grupę zasobów specjalnie na potrzeby usługi Azure Search. 

Gdy korzystasz z usługi, możesz śledzić bieżące i przewidywane koszty wszystkie (jak pokazano na zrzucie ekranu) lub przewijać w dół, aby wyświetlić opłaty dla poszczególnych zasobów.

![Zarządzanie kosztami na poziomie grupy zasobów](./media/search-create-service-portal/resource-group-cost-management.png "Zarządzanie kosztami na poziomie grupy zasobów")

> [!TIP]
> Usunięcie grupy zasobów powoduje usunięcie zawartych w niej usług. Umieszczenie w tej samej grupie zasobów wszystkich projektów będących prototypami korzystającymi z wielu usług ułatwia proces czyszczenia po zakończeniu projektu.

## <a name="name-the-service"></a>Nazwij usługę

W obszarze Szczegóły wystąpienia Podaj nazwę usługi w polu **adres URL** . Nazwa jest częścią punktu końcowego adresu URL, względem którego są wydawane wywołania `https://your-service-name.search.windows.net`interfejsu API:. Na przykład jeśli chcesz, aby punkt końcowy znajdował się pod adresem `https://myservice.search.windows.net`, wpisz `myservice`.

Wymagania dotyczące nazwy usługi:

* Musi być unikatowa w obrębie przestrzeni nazw search.windows.net
* Długość musi zawierać się w przedziale od 2 do 60 znaków
* Musi składać się z małych liter, cyfr lub łączników („-”)
* Nie może zawierać łączników („-”) na pierwszych dwóch miejscach ani na ostatnim miejscu
* Nie może zawierać następujących po sobie łączników („--”)

> [!TIP]
> Jeśli uważasz, że będziesz korzystać z wielu usług, zalecamy uwzględnienie regionu (lub lokalizacji) w nazwie usługi jako konwencji nazewnictwa. Usługi w tym samym regionie mogą bezpłatnie wymieniać dane, więc jeśli Azure Search jest w regionie zachodnie stany USA, a inne usługi znajdują się również w regionie zachodnie `mysearchservice-westus` stany USA, nazwa taka jak może zaoszczędzić na stronie właściwości podczas decydowania o sposobie łączenia lub dołączania zasobów.

## <a name="choose-a-location"></a>Wybierz lokalizację

W związku z tym, że usługa Azure Search należy do usług platformy Azure, może być hostowana w centrach danych na całym świecie. Listę obsługiwanych regionów można znaleźć na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/search/). 

Możesz zminimalizować lub uniknąć opłat za przepustowość, wybierając tę samą lokalizację dla wielu usług. Na przykład w przypadku indeksowania danych dostarczanych przez inną usługę platformy Azure (Azure Storage, Azure Cosmos DB, Azure SQL Database) Tworzenie usługi Azure Search w tym samym regionie pozwala uniknąć naliczania opłat za przepustowość (nie są naliczane opłaty za dane wychodzące, gdy usługi znajdują się w usłudze ten sam region).

Ponadto, jeśli korzystasz z wzbogacania plików AI do wyszukiwania poznawczego, Utwórz usługę w tym samym regionie, w którym znajduje się zasób Cognitive Services. *Wspólne lokalizacje Azure Search i Cognitive Services w tym samym regionie są wymagane do wzbogacania AI*.

> [!Note]
> Indie Środkowe nie są obecnie dostępne dla nowych usług. W przypadku usług już znajdujących się w centralnym Indiach można skalować w górę bez ograniczeń, a usługa jest w pełni obsługiwana w tym regionie. Ograniczenie w tym regionie jest tymczasowe i ograniczone tylko do nowych usług. Ta uwaga zostanie usunięta, jeśli ograniczenie nie zostanie już zastosowane.

## <a name="choose-a-pricing-tier-sku"></a>Wybierz warstwę cenową (SKU)

[Usługa Azure Search jest obecnie dostępna w wielu warstwach cenowych](https://azure.microsoft.com/pricing/details/search/): bezpłatnej, podstawowej i standardowej. Poszczególne warstwy różnią się między sobą [pojemnością i limitami](search-limits-quotas-capacity.md). Aby uzyskać wskazówki, zobacz [Choose a pricing tier or SKU](search-sku-tier.md) (Wybieranie warstwy cenowej lub jednostek SKU).

Podstawowa i Standardowa są najbardziej typowymi opcjami dotyczącymi obciążeń produkcyjnych, ale większość klientów zaczyna się od bezpłatnej usługi. Najważniejsze różnice między warstwami to rozmiar partycji i szybkość oraz limity dotyczące liczby obiektów, które można utworzyć.

Należy pamiętać, że nie można zmienić warstwy cenowej po utworzeniu usługi. W razie konieczności przejścia do warstwy wyższej lub niższej należy ponownie utworzyć usługę.

## <a name="create-your-service"></a>Tworzenie usługi

Po podaniu niezbędnych danych wejściowych przejdź dalej i Utwórz usługę. 

![Przeglądanie i tworzenie usługi](./media/search-create-service-portal/new-service3.png "Przeglądanie i tworzenie usługi")

Twoja usługa zostanie wdrożona w ciągu kilku minut, którą można monitorować za pomocą powiadomień platformy Azure. Rozważ możliwość przypięcia usługi do pulpitu nawigacyjnego w celu ułatwienia dostępu w przyszłości.

![Monitorowanie i Przypinanie usługi](./media/search-create-service-portal/monitor-notifications.png "Monitorowanie i Przypinanie usługi")

## <a name="get-a-key-and-url-endpoint"></a>Pobieranie klucza i punktu końcowego adresu URL

Jeśli nie korzystasz z portalu, dostęp programistyczny do nowej usługi wymaga podania punktu końcowego adresu URL oraz klucza API uwierzytelniania.

1. W prawej części strony przeglądu usługi zlokalizuj punkt końcowy adresu URL i skopiuj go.

2. W okienku nawigacji po lewej stronie wybierz pozycję **Klucze**, a następnie skopiuj jeden z kluczy administratora (są one równoważne). Klucze interfejsu API administratora są wymagane do tworzenia, aktualizowania i usuwania obiektów w usłudze.

   ![Strona przeglądu usługi z punktem końcowym adresu URL](./media/search-create-service-portal/get-url-key.png "Strona przeglądu usługi z punktem końcowym adresu URL")

Klucz i punkt końcowy nie są wymagane do zadań wykonywanych w portalu. Portal jest już połączony z zasobem usługi Azure Search przy użyciu uprawnień administratora. Przewodnik po samouczku można rozpocząć od [przewodnika Szybki Start: Utwórz indeks Azure Search w portalu](search-get-started-portal.md).

## <a name="scale-your-service"></a>Skalowanie usługi

Po aprowizacji usługi można ją skalować stosownie do potrzeb. W przypadku wybrania warstwy Standardowa dla usługi Azure Search można skalować usługę w dwóch wymiarach: Replicas i partitions. Wybór warstwy podstawowej oznaczałby, że można dodawać wyłącznie repliki. W razie aprowizacji bezpłatnej usługi skalowanie nie jest dostępne.

***Partycje*** umożliwiają usłudze przechowywanie i przeszukiwanie większej liczby dokumentów.

***Repliki*** umożliwiają usłudze obsługę większego obciążenia zapytaniami wyszukiwania.

Dodawanie zasobów wiąże się z dodaniem opłat do rachunku miesięcznego. [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) pomaga w określeniu, jaki wpływ na rozliczenia będzie miało dodanie zasobów. Pamiętaj, że możesz też dostosowywać zasoby do obciążenia. Możesz na przykład zwiększyć ilość zasobów na czas początkowego tworzenia pełnego indeksu, a następnie zmniejszyć ją do poziomu dostosowanego do potrzeb indeksowania przyrostowego.

> [!Important]
> Usługa musi mieć [2 repliki w ramach umowy SLA tylko do odczytu oraz 3 repliki w ramach umowy SLA do odczytu/zapisu](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Przejdź do strony usługi wyszukiwania w witrynie Azure Portal.
2. W okienku nawigacji po lewej wybierz kolejno pozycje **Ustawienia** > **Skalowanie**.
3. Użyj suwaka, aby dodać zasoby wybranego typu.

![Dodawanie pojemności](./media/search-create-service-portal/settings-scale.png "Dodawanie pojemności za pośrednictwem replik i partycji")

> [!Note]
> Magazyn na partycję i szybkość zwiększają się o wyższe warstwy. Aby uzyskać więcej informacji, zobacz [pojemność i limity](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Kiedy należy dodać drugą usługę

Większość klientów używa tylko jednej usługi aprowizowanej dla danej warstwy, która zapewnia [właściwe rozmieszczenie zasobów](search-sku-tier.md). Jedna usługa może obsługiwać wiele indeksów, z zastrzeżeniem [maksymalnych limitów wybranej warstwy](search-capacity-planning.md), przy czym każdy indeks jest odizolowany od innych. W usłudze Azure Search żądania mogą być kierowane tylko do jednego indeksu, minimalizując tym samym ryzyko przypadkowego lub zamierzonego pobierania danych z innych indeksów tej samej usługi.

Chociaż większość klientów używa tylko jednej usługi, nadmiarowość usług może być konieczna, jeśli wymagania operacyjne są następujące:

* Odzyskiwanie po awarii (awaria centrum danych). Usługa Azure Search nie zapewnia natychmiastowego przejścia do trybu failover w przypadku awarii. Aby uzyskać wskazówki i zalecenia, zobacz [Service administration](search-manage.md) (Administrowanie usługą).
* W trakcie badania modelowania wielodostępnego ustalono, że dodatkowe usługi stanowią optymalne rozwiązanie dla danego projektu. Aby uzyskać więcej informacji, zobacz [Design for multi-tenancy](search-modeling-multitenant-saas-applications.md) (Projektowanie na potrzeby wielodostępu).
* Aplikacje wdrażane globalnie mogą wymagać wystąpienia usługi Azure Search w wielu regionach w celu zminimalizowania opóźnienia w ruchu międzynarodowym aplikacji.

> [!NOTE]
> W Azure Search nie można rozdzielić operacji indeksowania i wykonywania zapytań. w ten sposób nigdy nie utworzysz wielu usług dla rozdzielonych obciążeń. Zapytania względem indeksu zawsze dotyczą usługi, w której został utworzony (nie można utworzyć indeksu w jednej usłudze, by następnie skopiować go do innej).

Druga usługa nie jest wymagana w celu zapewnienia wysokiej dostępności. Wysoka dostępność zapytań ma miejsce wtedy, gdy używane są co najmniej 2 repliki w tej samej usłudze. Repliki są aktualizowane w odpowiedniej kolejności, co oznacza, że podczas wdrażania aktualizacji usługi działa co najmniej jedna replika. Aby uzyskać więcej informacji na temat czasu pracy, zobacz [Umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Następne kroki

Po aprowizacji usługi Azure Search możesz utworzyć w portalu swój pierwszy indeks.

> [!div class="nextstepaction"]
> [Szybki start: Tworzenie indeksu Azure Search w portalu](search-get-started-portal.md)
