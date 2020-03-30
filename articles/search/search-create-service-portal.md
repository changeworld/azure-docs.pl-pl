---
title: 'Szybki start: tworzenie usługi wyszukiwania w portalu'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku Szybki start portalu dowiedz się, jak skonfigurować zasób usługi Azure Cognitive Search w witrynie Azure portal. Wybierz grupy zasobów, regiony i jednostki SKU lub warstwę cenową.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3bc3edcd0e75d8f6e3e4d6f9b200032909318040
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77209362"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Szybki start: tworzenie usługi Azure Cognitive Search w portalu

Usługa Azure Cognitive Search to samodzielny zasób używany do podłączania środowiska wyszukiwania do aplikacji niestandardowych. Usługa Azure Cognitive Search łatwo integruje się z innymi usługami platformy Azure, aplikacjami na serwerach sieciowych lub z oprogramowaniem uruchomionym na innych platformach w chmurze.

W tym artykule dowiesz się, jak utworzyć zasób w [witrynie Azure portal](https://portal.azure.com/).

[![Animowany GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Preferujesz program PowerShell? Użyj [szablonu usługi](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manager. Aby uzyskać pomoc dotyczącą rozpoczynania pracy, zobacz [Zarządzanie usługą Azure Cognitive Search za pomocą programu PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Subskrypcja (bezpłatna lub płatna)

[Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) i skorzystaj z bezpłatnych środków, aby wypróbować płatne usługi platformy Azure. Po wyczerpaniu środków zachowaj konto i nadal korzystaj z bezpłatnych usług platformy Azure, takich jak Websites. Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.

Możesz również [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). W ramach subskrypcji MSDN co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure. 

## <a name="find-azure-cognitive-search"></a>Znajdź usługę Azure Cognitive Search

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Kliknij znak plus („+ Utwórz zasób”) w lewym górnym rogu.
3. Użyj paska wyszukiwania, aby znaleźć "Azure Cognitive Search" lub przejdź do zasobu za pośrednictwem **usługi Web** > **Azure Cognitive Search**.

![Tworzenie zasobu w portalu](./media/search-create-service-portal/find-search3.png "Tworzenie zasobu w portalu")

## <a name="choose-a-subscription"></a>Wybierz subskrypcję

Jeśli masz więcej niż jedną subskrypcję, wybierz jedną dla usługi wyszukiwania.

## <a name="set-a-resource-group"></a>Ustawianie grupy zasobów

Grupa zasobów to kontener, który przechowuje powiązane zasoby dla rozwiązania platformy Azure. Jest to wymagane dla usługi wyszukiwania. Jest to również przydatne do zarządzania zasobami all-up, w tym kosztów. Grupa zasobów może składać się z jednej usługi lub wielu usług używanych razem. Na przykład jeśli używasz usługi Azure Cognitive Search do indeksowania bazy danych usługi Azure Cosmos DB, można uczynić obie usługi częścią tej samej grupy zasobów do celów zarządzania. 

Jeśli nie łączysz zasobów w jedną grupę lub istniejące grupy zasobów są wypełnione zasobami używanymi w niepowiązanych rozwiązaniach, utwórz nową grupę zasobów tylko dla zasobu usługi Azure Cognitive Search. 

![Tworzenie nowej grupy zasobów](./media/search-create-service-portal/new-resource-group.png "Tworzenie nowej grupy zasobów")

Z biegiem czasu można śledzić bieżące i przewidywane koszty all-up lub można wyświetlić opłaty dla poszczególnych zasobów. Poniższy zrzut ekranu przedstawia rodzaj informacji o kosztach, których można oczekiwać po połączeniu wielu zasobów w jedną grupę.

![Zarządzanie kosztami na poziomie grupy zasobów](./media/search-create-service-portal/resource-group-cost-management.png "Zarządzanie kosztami na poziomie grupy zasobów")

> [!TIP]
> Grupy zasobów upraszczają oczyszczanie, ponieważ usunięcie grupy usuwa wszystkie usługi w niej. Umieszczenie w tej samej grupie zasobów wszystkich projektów będących prototypami korzystającymi z wielu usług ułatwia proces czyszczenia po zakończeniu projektu.

## <a name="name-the-service"></a>Nadaj nazwę usłudze

W polu Szczegóły wystąpienia podaj nazwę usługi w polu **ADRES URL.** Nazwa jest częścią punktu końcowego adresu URL, dla `https://your-service-name.search.windows.net`którego są wystawiane wywołania interfejsu API: . Na przykład jeśli chcesz, aby punkt końcowy znajdował się pod adresem `https://myservice.search.windows.net`, wpisz `myservice`.

Wymagania dotyczące nazwy usługi:

* Musi być unikatowa w obrębie przestrzeni nazw search.windows.net
* Musi mieć długość od 2 do 60 znaków
* Należy używać małych liter, cyfr lub kresek ("-")
* Nie używaj myślników ("-") w pierwszych 2 znakach ani jako ostatniego pojedynczego znaku
* Nie wolno używać kolejnych kresek ("--") w dowolnym miejscu

> [!TIP]
> Jeśli uważasz, że będziesz używać wielu usług, zalecamy uwzględnienie regionu (lub lokalizacji) w nazwie usługi jako konwencji nazewnictwa. Usługi w tym samym regionie można wymieniać dane bezpłatnie, więc jeśli usługa Azure Cognitive Search znajduje się `mysearchservice-westus` w zachodnie stany USA, a masz inne usługi również w zachodnie stany USA, nazwa taka jak można zapisać podróż do strony właściwości przy podejmowaniu decyzji, jak połączyć lub dołączyć zasoby.

## <a name="choose-a-location"></a>Wybieranie lokalizacji

Usługa Azure Cognitive Search może być hostowana w centrach danych na całym świecie. Listę obsługiwanych regionów można znaleźć na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/search/). 

Można zminimalizować lub uniknąć opłat za przepustowość, wybierając tę samą lokalizację dla wielu usług. Na przykład, jeśli indeksujesz dane dostarczane przez inną usługę platformy Azure (usługa Azure storage, Usługa Azure Cosmos DB, usługa Azure SQL Database), tworzenie usługi Azure Cognitive Search w tym samym regionie pozwala uniknąć opłat za przepustowość (nie ma żadnych opłat za dane wychodzące, gdy usługi znajdują się w tym samym regionie).

Jeśli używasz wzbogacania ai, utwórz usługę wyszukiwania w tym samym regionie co usługi Cognitive Services. *Kolokcja usługi Azure Cognitive Search i Cognitive Services w tym samym regionie jest wymagana dla wzbogacania sztucznej inteligencji.*

> [!Note]
> Środkowe Indie są obecnie niedostępne dla nowych usług. W przypadku usług już w Indiach Środkowych można skalować w górę bez żadnych ograniczeń, a usługa jest w pełni obsługiwana w tym regionie. Ograniczenie w tym regionie jest tymczasowe i ograniczone tylko do nowych usług. Usuniemy tę notatkę, gdy ograniczenie nie będzie już obowiązuje.

## <a name="choose-a-pricing-tier-sku"></a>Wybieranie warstwy cenowej (SKU)

[Usługa Azure Cognitive Search jest obecnie oferowana w wielu warstwach cenowych:](https://azure.microsoft.com/pricing/details/search/)bezpłatna, podstawowa lub standardowa. Poszczególne warstwy różnią się między sobą [pojemnością i limitami](search-limits-quotas-capacity.md). Aby uzyskać wskazówki, zobacz [Choose a pricing tier or SKU](search-sku-tier.md) (Wybieranie warstwy cenowej lub jednostek SKU).

Podstawowe i standardowe są najczęstszymi wyborami dla obciążeń produkcyjnych, ale większość klientów zaczyna od bezpłatnej usługi. Kluczowe różnice między warstwami jest rozmiar partycji i szybkość i ograniczenia liczby obiektów, które można utworzyć.

Pamiętaj, że warstwy cenowej nie można zmienić po utworzeniu usługi. Jeśli potrzebujesz wyższej lub niższej warstwy, musisz ponownie utworzyć usługę.

## <a name="create-your-service"></a>Tworzenie usługi

Po podasz niezbędne dane wejściowe, śmiało i utwórz usługę. 

![Przeglądanie i tworzenie usługi](./media/search-create-service-portal/new-service3.png "Przeglądanie i tworzenie usługi")

Usługa zostanie wdrożona w ciągu kilku minut. Możesz monitorować postęp za pomocą powiadomień platformy Azure. Rozważ przypięcie usługi do pulpitu nawigacyjnego, aby mieć łatwy dostęp w przyszłości.

![Monitoruj i przypinaj usługę](./media/search-create-service-portal/monitor-notifications.png "Monitoruj i przypinaj usługę")

## <a name="get-a-key-and-url-endpoint"></a>Pobieranie klucza i punktu końcowego adresu URL

Jeśli nie używasz portalu, programowy dostęp do nowej usługi wymaga podania punktu końcowego adresu URL i klucza api uwierzytelniania.

1. Na **stronie Przegląd** znajdź i skopiuj punkt końcowy adresu URL po prawej stronie strony.

2. Na **keys** strony, skopiuj jeden z kluczy administratora (są one równoważne). Klucze interfejsu API administratora są wymagane do tworzenia, aktualizowania i usuwania obiektów w usłudze. Z kolei klucze zapytań zapewniają dostęp do odczytu do zawartości indeksu.

   ![Strona przegląd usługi z punktem końcowym adresu URL](./media/search-create-service-portal/get-url-key.png "Punkt końcowy adresu URL i inne szczegóły usługi")

Klucz i punkt końcowy nie są wymagane do zadań wykonywanych w portalu. Portal jest już połączony z zasobem usługi Azure Cognitive Search z prawami administratora. W przypadku przewodnika po portalu zacznij od [przewodnika Szybki start: utwórz indeks usługi Azure Cognitive Search w portalu](search-get-started-portal.md).

## <a name="scale-your-service"></a>Skalowanie usługi

Po aprowizacji usługi można ją skalować stosownie do potrzeb. Jeśli wybrano warstwę standardową dla usługi Azure Cognitive Search, można skalować usługę w dwóch wymiarach: repliki i partycje. Wybór warstwy podstawowej oznaczałby, że można dodawać wyłącznie repliki. W razie aprowizacji bezpłatnej usługi skalowanie nie jest dostępne.

***Partycje*** umożliwiają usłudze przechowywanie i przeszukiwanie większej liczby dokumentów.

***Repliki*** umożliwiają usłudze obsługę większego obciążenia zapytaniami wyszukiwania.

Dodawanie zasobów wiąże się z dodaniem opłat do rachunku miesięcznego. [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) pomaga w określeniu, jaki wpływ na rozliczenia będzie miało dodanie zasobów. Pamiętaj, że możesz też dostosowywać zasoby do obciążenia. Możesz na przykład zwiększyć ilość zasobów na czas początkowego tworzenia pełnego indeksu, a następnie zmniejszyć ją do poziomu dostosowanego do potrzeb indeksowania przyrostowego.

> [!Important]
> Usługa musi mieć [2 repliki w ramach umowy SLA tylko do odczytu oraz 3 repliki w ramach umowy SLA do odczytu/zapisu](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Przejdź do strony usługi wyszukiwania w witrynie Azure Portal.
2. W lewym okienku nawigacji wybierz pozycję > **Skaluj** **ustawienia**.
3. Użyj suwaka, aby dodać zasoby wybranego typu.

![Dodawanie pojemności](./media/search-create-service-portal/settings-scale.png "Dodawanie pojemności za pomocą replik i partycji")

> [!Note]
> Magazyn na partycje i szybkość zwiększa się w wyższych warstwach. Aby uzyskać więcej informacji, zobacz [pojemność i limity](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Kiedy należy dodać drugą usługę

Większość klientów używa tylko jednej usługi aprowizowanej dla danej warstwy, która zapewnia [właściwe rozmieszczenie zasobów](search-sku-tier.md). Jedna usługa może obsługiwać wiele indeksów, z zastrzeżeniem [maksymalnych limitów wybranej warstwy](search-capacity-planning.md), przy czym każdy indeks jest odizolowany od innych. W usłudze Azure Cognitive Search żądania mogą być kierowane tylko do jednego indeksu, minimalizując ryzyko przypadkowego lub zamierzonego pobierania danych z innych indeksów w tej samej usłudze.

Chociaż większość klientów używa tylko jednej usługi, nadmiarowość usług może być konieczna, jeśli wymagania operacyjne są następujące:

* Odzyskiwanie po awarii (awaria centrum danych). Usługa Azure Cognitive Search nie zapewnia natychmiastowego trybu failover w przypadku awarii. Aby uzyskać wskazówki i zalecenia, zobacz [Service administration](search-manage.md) (Administrowanie usługą).
* W trakcie badania modelowania wielodostępnego ustalono, że dodatkowe usługi stanowią optymalne rozwiązanie dla danego projektu. Aby uzyskać więcej informacji, zobacz [Design for multi-tenancy](search-modeling-multitenant-saas-applications.md) (Projektowanie na potrzeby wielodostępu).
* W przypadku aplikacji wdrożonych globalnie może być wymagane wystąpienie usługi Azure Cognitive Search w wielu regionach, aby zminimalizować opóźnienia ruchu międzynarodowego aplikacji.

> [!NOTE]
> W usłudze Azure Cognitive Search nie można segregować operacji indeksowania i wykonywania zapytań; w związku z tym nigdy nie utworzyć wiele usług dla oddzielnych obciążeń. Zapytania względem indeksu zawsze dotyczą usługi, w której został utworzony (nie można utworzyć indeksu w jednej usłudze, by następnie skopiować go do innej).

Druga usługa nie jest wymagana w celu zapewnienia wysokiej dostępności. Wysoka dostępność zapytań ma miejsce wtedy, gdy używane są co najmniej 2 repliki w tej samej usłudze. Aktualizacje repliki są sekwencyjne, co oznacza, że co najmniej jeden działa po włączeniu aktualizacji usługi. Aby uzyskać więcej informacji na temat czasu pracy bez [przestojów,](https://azure.microsoft.com/support/legal/sla/search/v1_0/)zobacz Umowy dotyczące poziomu usług .

## <a name="next-steps"></a>Następne kroki

Po inicjowaniu obsługi administracyjnej usługi, można kontynuować w portalu, aby utworzyć pierwszy indeks.

> [!div class="nextstepaction"]
> [Szybki start: tworzenie indeksu usługi Azure Cognitive Search w portalu](search-get-started-portal.md)
