---
title: Administrowania usługami dla usługi Azure Search w portalu — usługa Azure Search
description: Zarządzaj usługi Azure Search, Usługa wyszukiwania w chmurze hostowanych na Microsoft Azure, przy użyciu witryny Azure portal.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d5820c927b88eba37eaf092dfd4b209180bfc8eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565451"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administrowania usługami dla usługi Azure Search w witrynie Azure portal
> [!div class="op_single_selector"]
> * [Program PowerShell](search-manage-powershell.md)
> * [Interfejs API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Usługa Azure Search to usługa wyszukiwania w pełni zarządzanej, oparte na chmurze używana do tworzenia zaawansowanych funkcji wyszukiwania w aplikacjach niestandardowych. W tym artykule omówiono usługę zadań administracyjnych, które można wykonywać w [witryny Azure portal](https://portal.azure.com) usługi wyszukiwania, które zostały już aprowizowane. Administrowania usługami jest uproszczone przez projekt, ograniczone do następujących zadań:

> [!div class="checklist"]
> * Zarządzanie dostępem do *klucze api Key* używany do odczytu lub zapisu do usługi.
> * Dostosuj pojemności usługi, zmieniając Alokacja partycje i repliki.
> * Monitorowanie użycia zasobów, względem maksymalnych limitów wybranej warstwie usługi.

Należy zauważyć, że *uaktualnienia* nie jest wymieniony jako zadań administracyjnych. Ponieważ zasoby są przydzielane, gdy usługa jest aprowizowana, przejście do innej warstwy wymaga nowej usługi. Aby uzyskać więcej informacji, zobacz [Tworzenie usługi Azure Search](search-create-service-portal.md).

> [!Tip]
> Szukasz pomocy na temat sposobu analizowania wydajności ruchu sieciowego lub zapytanie wyszukiwania? Można monitorować wolumin zapytań, który warunki osoby, wyszukiwanie i wyszukiwania jak pomyślne wyniki znajdują się w podstawowych wytycznych klientów do określonych dokumentów w indeksie. Aby uzyskać więcej informacji, zobacz [Search Traffic Analytics dla usługi Azure Search](search-traffic-analytics.md), [monitorować metryki użycia i zapytania](search-monitor-usage.md), i [wydajność i optymalizacja](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Uprawnienia administratora
Inicjowanie obsługi administracyjnej lub likwidowanie sama usługa może odbywać się przez administratora subskrypcji platformy Azure lub administrator współpracujący.

W ramach usługi każda osoba mająca dostęp do adresu URL usługi i klucza interfejsu api usługi Administrator ma dostęp do odczytu i zapisu do usługi. Dostęp do odczytu i zapisu zapewnia możliwość dodania, usunięcia lub zmodyfikowania obiektów serwera, w tym klucze api Key, indeksów, indeksatorów, źródła danych, harmonogramy i przypisania roli zaimplementowanego przy użyciu [role RBAC, zdefiniowane](search-security-rbac.md).

Cała interakcja użytkownika z usługi Azure Search znajduje się w jednym z następujących trybów: dostęp do odczytu i zapisu do usługi (uprawnienia administratora) lub dostęp tylko do odczytu do usługi (prawa do zapytania). Aby uzyskać więcej informacji, zobacz [zarządzanie kluczami interfejsu api](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Informacje o rejestracji i system
Usługa Azure Search nie ujawnia plików dziennika dla poszczególnych usług za pośrednictwem portalu lub interfejsów programistycznych. W warstwie podstawowa i powyżej Microsoft monitoruje wszystkie usługi Azure Search przez 99,9% czasu według umowy dotyczące poziomu usług (SLA). Jeśli usługa jest powolne lub żądań spadnie poniżej progów umowy SLA, zespoły pomocy technicznej Przejrzyj pliki dziennika dostępne dla nich i rozwiązać problem.

Pod względem ogólne informacje na temat usługi informacje można uzyskać w następujący sposób:

* W portalu na pulpicie nawigacyjnym usługi za pośrednictwem powiadomienia, właściwości i komunikaty o stanie.
* Za pomocą [PowerShell](search-manage-powershell.md) lub [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/) do [pobrać właściwości usługi](https://docs.microsoft.com/rest/api/searchmanagement/services), lub stan użycia zasobów indeksu.
* Za pomocą [analiza ruchu wyszukiwania](search-traffic-analytics.md), jak wspomniano wcześniej.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorowanie użycia zasobów
Na pulpicie nawigacyjnym monitorowania zasobów jest ograniczona do informacje wyświetlane na pulpicie nawigacyjnym usługi i kilka metryk, które można uzyskać, badając usługę. Na pulpicie nawigacyjnym usługi w sekcji użycia może szybko określić, czy poziomy zasobów partycji są odpowiednie dla twojej aplikacji. Można udostępnić zasobów zewnętrznych, takich jak monitorowanie platformy Azure, aby przechwytywania i utrwalić zarejestrowanych zdarzeń. Aby uzyskać więcej informacji, zobacz [monitorowania usługi Azure Search](search-monitor-usage.md).

Przy użyciu interfejsu API REST usługi Search, możesz uzyskać liczby w indeksach i dokumentach programowe: 

* [Pobieranie statystyki indeksu](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Liczba dokumentów](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Awarie awaryjnego odzyskiwania i usługa

Mimo że firma Microsoft jest odzyskana danych, usługi Azure Search nie zapewnia błyskawiczne pracy awaryjnej usługi, w przypadku awarii na poziomie Centrum klastra lub danych. Jeśli klaster nie powiedzie się w centrum danych, zespół operacyjny wykryje i współpracują w celu przywrócenia usługi. Będzie przestoju podczas przywracania usługi, ale możesz poprosić o środki na usługi w celu kompensacji niedostępność usługi na [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Jeśli usługa ciągłej jest wymagana w przypadku poważnej awarii poza kontrolą firmy Microsoft, można wykonać następujące akcje [obsługi administracyjnej dodatkowych usług](search-create-service-portal.md) w innym regionie i wdrożenie strategii replikacji geograficznej, aby upewnić się, indeksy są w pełni nadmiarowe we wszystkich usługach.

Klienci, którzy używają [indeksatory](search-indexer-overview.md) do wypełniania i Odśwież indeksów może obsługiwać odzyskiwania po awarii przy użyciu indeksatorów specyficzne dla geograficznie korzystanie z tego samego źródła danych. Dwie usługi w różnych regionach, uruchamianie indeksatora, można indeksować tego samego źródła danych w celu osiągnięcia nadmiarowość geograficzna. Jeśli indeksujesz ze źródeł danych, które są również magazynu geograficznie nadmiarowego należy pamiętać, że indeksatorów usługi Azure Search może wykonywać tylko przyrostowe indeksowanie z replikami podstawowymi. W przypadku pracy awaryjnej należy ponownie punkt indeksator do nowej repliki podstawowej. 

Jeśli nie korzystać z indeksatorów, należy użyć kodu aplikacji do wypychania obiektów i danych do usługi wyszukiwania innym równolegle. Aby uzyskać więcej informacji, zobacz [wydajność i optymalizacja w usłudze Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Ponieważ usługa Azure Search nie jest rozwiązanie magazynu danych podstawowych, nie są oferowane posiadanie mechanizmu samoobsługi kopii zapasowych i przywracania. Kod aplikacji używanych do tworzenia i wypełniania indeksu jest de facto opcja przywracania, jeśli przez pomyłkę usuwanie indeksu. 

Do ponownego utworzenia indeksu, czy usunąć go (przy założeniu, że istnieje), ponowne utworzenie indeksu w usłudze i ponownie załaduj przez pobranie danych z podstawowym magazynem danych.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Skalowanie w górę lub w dół
Każda usługa wyszukiwania rozpoczyna się od co najmniej jedną replikę i jedną partycję. Jeśli rejestracja w programie [warstwę, która zapewnia zasoby dedykowane](search-limits-quotas-capacity.md), kliknij przycisk **SKALOWANIA** kafelka na pulpicie nawigacyjnym usługi dostosowania użycia zasobów.

Po dodaniu pojemność przy użyciu dowolnego zasobu, usługa korzysta z ich automatycznie. Ze strony użytkownika są wymagane żadne dalsze działania, ale występuje niewielkie opóźnienie, zanim wpływ nowy zasób jest wykonywane. Może potrwać 15 minut lub dłużej zainicjowanie obsługi dodatkowych zasobów.

 ![][10]

### <a name="add-replicas"></a>Dodawanie replik
Zwiększenie zapytań na sekundę (QPS) i osiągnięcie wysokiej dostępności odbywa się przez dodanie replik. Każdej repliki ma jedną kopię indeksu, więc dodanie jednej replice więcej przekłada się na jednym więcej indeksu do obsługi żądań zapytań usługi. Co najmniej 3 replik są wymagane w celu zapewnienia wysokiej dostępności (zobacz [planowania pojemności](search-capacity-planning.md) Aby uzyskać szczegółowe informacje).

Usługę wyszukiwania, w której większa liczba replik można załadować saldo żądań zapytań za pośrednictwem większej liczby indeksów. Biorąc pod uwagę na poziomie woluminu zapytania, przepustowości operacji zapytań będzie można szybciej w przypadku większej liczby kopii indeksu, które są dostępne do obsłużenia żądania. Jeśli występuje opóźnienie zapytań, można oczekiwać, że pozytywnie wpłyną na wydajność po dodatkowe repliki są w trybie online.

Przepływności zapytań rośnie w miarę dodawania repliki, go nie precyzyjnie dwukrotnie lub potrójne repliki podczas dodawania do usługi. Wszystkie aplikacje wyszukiwania podlegają czynniki zewnętrzne, które można wpływają na wydajność zapytań. Złożone zapytania i opóźnienie sieci są dwa czynniki przyczyniające się do zmian w czas odpowiedzi na zapytania.

### <a name="add-partitions"></a>Dodawanie partycji
Większość aplikacji usługi ma wbudowane potrzebę replik więcej, a nie partycji. Dla tych przypadków, w których wymagane są liczby dokumentów zwiększona można dodać partycji, jeśli zostało utworzone konto dla usług w warstwie standardowa. Warstwa podstawowa udostępniają dodatkowe partycje.

W warstwie standardowa partycje są dodawane w wielokrotnościach 12 (w szczególności 1, 2, 3, 4, 6 lub 12). To jest pozostałością dzielenia na fragmenty. Indeks jest tworzony w 12 fragmentów, które można wszystkie przechowywane na partycji 1 lub dzielone równo między 2, 3, 4, 6 lub 12 partycjami (jednego fragmentu na partycję).

### <a name="remove-replicas"></a>Usuwanie repliki
Po okresach wysokiej zapytań woluminów suwak służy również do zmniejszenia repliki po obciążeń zapytaniami wyszukiwania mają znormalizowane (na przykład po sprzedaży w okresie świątecznym za pośrednictwem). Nie ma żadnych dalszych kroków wymagane ze strony użytkownika. Zmniejszenie liczby replik zwalnia maszyn wirtualnych w centrum danych. Pozyskiwanie operacji zapytań i danych teraz zostanie uruchomiona na maszynach wirtualnych mniej niż przed. Musi istnieć co najmniej jedna replika.

### <a name="remove-partitions"></a>Usuwanie partycji
W przeciwieństwie do usuwania repliki, co wymaga nie dodatkowych działań ze strony użytkownika, może mieć jakąś pracę, należy zrobić, jeśli używasz więcej miejsca niż można zmniejszyć. Na przykład jeśli Twoje rozwiązanie używa trzech partycji, redukcję zatrudnienia na co najmniej dwie partycje wygeneruje błąd, jeśli nowe miejsce do magazynowania jest mniejsza niż jest to wymagane do obsługi Twojego indeksu. Zgodnie z oczekiwaniami, wybrane opcje są umożliwia usunięcie indeksów lub dokumentów w ramach skojarzonego indeksu, aby zwolnić miejsce lub zachować bieżącą konfigurację.

Nie istnieje metoda wykrywania informujące o tym, które fragmenty indeksu są przechowywane na określone partycje. Każda partycja zawiera około 25 GB w magazynie, więc należy zmniejszyć magazyn do rozmiaru, że są spełnione przez liczbę partycji, do których masz. Jeśli chcesz powrócić do jednej partycji, wszystkich fragmentów 12 będzie konieczne jest dopasowanie.

Aby ułatwić planowanie w przyszłości, możesz chcieć sprawdzić magazynu (przy użyciu [pobieranie statystyki indeksu](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) aby zobaczyć, ile rzeczywiście używane. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Najlepsze rozwiązania dotyczące skalowania i wdrażania
W tym 30-minutowe wideo przeglądów najlepsze rozwiązania dotyczące zaawansowanych scenariuszach wdrożenia, w tym rozproszone geograficznie obciążenia. Można również wyświetlić [wydajność i optymalizacja w usłudze Azure Search](search-performance-optimization.md) dla stron pomocy, które obejmują tych samych punktów.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Kolejne kroki
Po zrozumieniu pojęć dotyczących administracji usługi, należy wziąć pod uwagę przy użyciu [PowerShell](search-manage-powershell.md) do automatyzowania zadań.

Zalecamy również przeglądanie [wydajność i optymalizacja artykułu](search-performance-optimization.md).

Inny zalecane jest aby obejrzeć wideo, o których wspomniano w poprzedniej sekcji. Zawiera pokrycie głębiej techniki opisane w tej sekcji.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



