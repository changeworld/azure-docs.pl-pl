---
title: Administracja serwisem w portalu
titleSuffix: Azure Cognitive Search
description: Zarządzanie usługą Azure Cognitive Search, hostowana usługa wyszukiwania w chmurze na platformie Microsoft Azure, przy użyciu witryny Azure portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3abbf2c8e0734d17aabadd2ae5f61cc03889964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282928"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Administrowanie usługami dla usługi Azure Cognitive Search w witrynie Azure portal
> [!div class="op_single_selector"]
> * [Powershell](search-manage-powershell.md)
> * [INTERFEJS API ODPOCZYNKU](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Usługa Azure Cognitive Search to w pełni zarządzana, oparta na chmurze usługa wyszukiwania używana do tworzenia zaawansowanego środowiska wyszukiwania w aplikacjach niestandardowych. W tym artykule opisano zadania administracyjne usługi, które można wykonać w [witrynie Azure portal](https://portal.azure.com) dla usługi wyszukiwania, które zostały już zainicjowane. Administracja usługami jest odciążona z założenia, ograniczona do następujących zadań:

> [!div class="checklist"]
> * Zarządzanie dostępem do *kluczy interfejsu API używanych* do odczytu lub zapisu dostępu do usługi.
> * Dostosuj pojemność usługi, zmieniając alokację partycji i replik.
> * Monitorowanie użycia zasobów względem maksymalnych limitów warstwy usług.

Należy zauważyć, że *uaktualnienie* nie jest wymienione jako zadanie administracyjne. Ponieważ zasoby są przydzielane, gdy usługa jest aprowizowana, przejście do innej warstwy wymaga nowej usługi. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie usługi Azure Cognitive Search](search-create-service-portal.md).

Można monitorować wolumin zapytań i inne metryki i używać tych szczegółowych informacji, aby dostosować usługę do krótszych czasów odpowiedzi. Aby uzyskać więcej informacji, zobacz [Monitorowanie użycia i metryk zapytań](search-monitor-usage.md) oraz [wydajność i optymalizacja](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Prawa administratora
Inicjowanie obsługi administracyjnej lub likwidacji samej usługi może być wykonane przez administratora subskrypcji platformy Azure lub współadministratora.

W ramach usługi każda osoba mająca dostęp do adresu URL usługi i klucza interfejsu api administratora ma dostęp do odczytu i zapisu do usługi. Dostęp do odczytu i zapisu umożliwia dodawanie, usuwanie lub modyfikowanie obiektów serwera, w tym kluczy api, indeksów, indeksów, źródeł danych, harmonogramów i przypisań ról, zgodnie z implementowanymi za pomocą [ról zdefiniowanych przez RBAC](search-security-rbac.md).

Cała interakcja użytkownika z usługą Azure Cognitive Search mieści się w jednym z następujących trybów: dostęp do odczytu i zapisu do usługi (prawa administratora) lub dostęp tylko do odczytu do usługi (prawa do zapytań). Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami interfejsu api](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Rejestrowanie i informacje o systemie
Usługa Azure Cognitive Search nie udostępnia plików dziennika dla pojedynczej usługi za pośrednictwem portalu lub interfejsów programowych. W warstwie Podstawowa i powyżej firma Microsoft monitoruje wszystkie usługi Azure Cognitive Search pod kątem dostępności 99,9% na umowy dotyczące poziomu usług (SLA). Jeśli usługa jest powolna lub przepływność żądania spadnie poniżej progów umowy SLA, zespoły pomocy technicznej przejrzeć pliki dziennika dostępne dla nich i rozwiązać problem.

Jeśli chodzi o ogólne informacje o usłudze, można uzyskać informacje w następujący sposób:

* W portalu, na pulpicie nawigacyjnym usługi, za pośrednictwem powiadomień, właściwości i komunikatów o stanie.
* Za pomocą [programu PowerShell](search-manage-powershell.md) lub [interfejsu API REST zarządzania,](https://docs.microsoft.com/rest/api/searchmanagement/) aby [uzyskać właściwości usługi](https://docs.microsoft.com/rest/api/searchmanagement/services)lub stan użycia zasobów indeksu.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorowanie użycia zasobów
Na pulpicie nawigacyjnym monitorowanie zasobów jest ograniczone do informacji wyświetlanych na pulpicie nawigacyjnym usługi i kilku metryk, które można uzyskać, korzystając z kwerendy usługi. Na pulpicie nawigacyjnym usługi w sekcji Użycie można szybko określić, czy poziomy zasobów partycji są odpowiednie dla aplikacji. Można aprowizować zasoby zewnętrzne, takie jak monitorowanie platformy Azure, jeśli chcesz przechwycić i utrwalić zarejestrowane zdarzenia. Aby uzyskać więcej informacji, zobacz [Monitorowanie usługi Azure Cognitive Search](search-monitor-usage.md).

Korzystając z interfejsu API REST usługi wyszukiwania, można uzyskać liczbę dokumentów i indeksów programowo: 

* [Pobierz statystyki indeksu](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Zliczanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Odzyskiwanie po awarii i awarie usług

Mimo że możemy uratować twoje dane, usługa Azure Cognitive Search nie zapewnia natychmiastowego trybu failover usługi w przypadku awarii na poziomie klastra lub centrum danych. Jeśli klaster ulegnie awarii w centrum danych, zespół operacyjny wykryje i będzie pracować, aby przywrócić usługę. Podczas przywracania usługi wystąpią przestoje, ale można zażądać kredytów na usługi, aby zrekompensować niedostępność usługi zgodnie z umową dotyczącą [poziomu usług (SLA).](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 

Jeśli ciągła usługa jest wymagana w przypadku katastrofalnych awarii poza kontrolą firmy Microsoft, można [aprowizować dodatkową usługę](search-create-service-portal.md) w innym regionie i zaimplementować strategię replikacji geograficznej, aby upewnić się, że indeksy są w pełni zbędne we wszystkich usługach.

Klienci, którzy używają [indeksatorów](search-indexer-overview.md) do wypełniania i odświeżania indeksów, mogą obsługiwać odzyskiwanie po awarii za pośrednictwem indeksatorów specyficznych dla geograficznych, korzystających z tego samego źródła danych. Dwie usługi w różnych regionach, z których każda działa indeksatora, można indeksować tego samego źródła danych w celu osiągnięcia nadmiarowości geograficznej. Jeśli indeksujesz ze źródeł danych, które są również geograficznie nadmiarowe, należy pamiętać, że indeksatory usługi Azure Cognitive Search można wykonywać tylko indeksowanie przyrostowe (scalanie aktualizacji z nowych, zmodyfikowanych lub usuniętych dokumentów) z replik podstawowych. W przypadku pracy awaryjnej należy ponownie wskazać indeksatora do nowej repliki podstawowej. 

Jeśli nie używasz indeksatorów, należy użyć kodu aplikacji do wypychania obiektów i danych do różnych usług wyszukiwania równolegle. Aby uzyskać więcej informacji, zobacz [Wydajność i optymalizacja w usłudze Azure Cognitive Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Ponieważ usługa Azure Cognitive Search nie jest podstawowym rozwiązaniem magazynu danych, nie zapewniamy formalnego mechanizmu samoobsługowego tworzenia kopii zapasowych i przywracania. Jednak można użyć **przykładowego kodu przywracania kopii zapasowej indeksu** w tym [przykładowym repozytorium usługi Azure Cognitive Search .NET,](https://github.com/Azure-Samples/azure-search-dotnet-samples) aby wykonać kopię zapasową definicji indeksu i migawki do serii plików JSON, a następnie użyć tych plików do przywrócenia indeksu, jeśli to konieczne. To narzędzie może również przenosić indeksy między warstwami usług.

W przeciwnym razie kod aplikacji używany do tworzenia i wypełniania indeksu jest de facto opcja przywracania, jeśli usuniesz indeks przez pomyłkę. Aby odbudować indeks, należy go usunąć (przy założeniu, że istnieje), ponownie utworzyć indeks w usłudze i ponownie załadować, pobierając dane z magazynu danych podstawowych.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Skalowanie w górę lub w dół
Każda usługa wyszukiwania rozpoczyna się od co najmniej jednej repliki i jednej partycji. Jeśli zarejestrowałeś się w [warstwie zawierającej dedykowane zasoby,](search-limits-quotas-capacity.md)kliknij kafelek **SKALE** na pulpicie nawigacyjnym usługi, aby dostosować użycie zasobów.

Po dodaniu pojemności za pośrednictwem jednego zasobu usługa używa ich automatycznie. Nie jest wymagane żadne dalsze działania z Twojej strony, ale istnieje niewielkie opóźnienie przed realizacją wpływu nowego zasobu. Może upłynąć 15 minut lub więcej, aby aprowizować dodatkowe zasoby.

 ![][10]

### <a name="add-replicas"></a>Dodawanie replik
Zwiększenie zapytań na sekundę (QPS) lub osiągnięcie wysokiej dostępności odbywa się przez dodanie replik. Każda replika ma jedną kopię indeksu, więc dodanie jeszcze jednej repliki przekłada się na jeden indeks dostępny do obsługi żądań zapytań usługi. Aby uzyskać wysoką dostępność, wymagane są co najmniej 3 repliki (szczegółowe informacje można znaleźć w [planowaniu pojemności).](search-capacity-planning.md)

Usługa wyszukiwania posiadająca więcej replik może załadować żądania zapytań o równoważenie w większej liczbie indeksów. Biorąc pod uwagę poziom woluminu zapytania, przepływność zapytania będzie szybsza, gdy istnieje więcej kopii indeksu dostępnych do obsługi żądania. Jeśli występują opóźnienia kwerendy, można oczekiwać pozytywny wpływ na wydajność, gdy dodatkowe repliki są w trybie online.

Mimo że przepływność kwerendy idzie w górę podczas dodawania replik, nie dokładnie dwukrotnie lub potrójne, jak dodać repliki do usługi. Wszystkie aplikacje wyszukiwania podlegają czynnikom zewnętrznym, które mogą mieć wpływ na wydajność kwerendy. Złożone zapytania i opóźnienie sieci są dwa czynniki, które przyczyniają się do różnic w czasie odpowiedzi na zapytania.

### <a name="add-partitions"></a>Dodawanie partycji
Większość aplikacji usługi mają wbudowaną potrzebę więcej replik, a nie partycji. W tych przypadkach, w których wymagana jest zwiększona liczba dokumentów, można dodać partycje, jeśli zarejestrowałeś się w usłudze standardowej. Warstwa podstawowa nie zapewnia dodatkowych partycji.

W warstwie Standardowa partycje są dodawane w wielokrotnościach 12 (w szczególności 1, 2, 3, 4, 6 lub 12). Jest to artefakt dzielenia na fragmenty. Indeks jest tworzony w 12 fragmentów, które mogą być przechowywane na partycji 1 lub równo podzielone na partycje 2, 3, 4, 6 lub 12 (jeden fragment na partycję).

### <a name="remove-replicas"></a>Usuwanie replik
Po okresach dużych woluminów zapytań można użyć suwaka, aby zmniejszyć repliki po znormalizowane obciążenia zapytań wyszukiwania (na przykład po zakończeniu sprzedaży wakacyjnej). Nie są wymagane żadne dalsze kroki z Twojej strony. Obniżenie liczby replik rezygnuje z maszyn wirtualnych w centrum danych. Operacje pozyskiwania zapytań i danych będą teraz uruchamiane na mniejszej liczbie maszyn wirtualnych niż wcześniej. Minimalnym wymaganiem jest jedna replika.

### <a name="remove-partitions"></a>Usuwanie partycji
W przeciwieństwie do usuwania replik, co nie wymaga dodatkowego wysiłku z Twojej strony, możesz mieć trochę pracy do zrobienia, jeśli używasz więcej miejsca niż można zmniejszyć. Na przykład jeśli rozwiązanie używa trzech partycji, downsizing do jednej lub dwóch partycji wygeneruje błąd, jeśli nowe miejsce do magazynowania jest mniejsza niż wymagana do hostowania indeksu. Zgodnie z oczekiwaniami, można dokonać wyboru są usunięcie indeksów lub dokumentów w ramach skojarzonego indeksu, aby zwolnić miejsce lub zachować bieżącą konfigurację.

Nie ma żadnej metody wykrywania, która informuje, które fragmenty indeksu są przechowywane na określonych partycjach. Każda partycja zapewnia około 25 GB w magazynie, więc należy zmniejszyć magazyn do rozmiaru, który może być pochłonięta przez liczbę partycji, które masz. Jeśli chcesz przywrócić jedną partycję, wszystkie 12 fragmentów będzie musiał zmieścić.

Aby pomóc w planowaniu w przyszłości, można sprawdzić magazynu (przy użyciu [Pobierz statystyki indeksu),](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)aby zobaczyć, ile faktycznie używane. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Następne kroki
Po zapoznaniu się z pojęciami administracji usług, należy rozważyć użycie [programu PowerShell](search-manage-powershell.md) do automatyzacji zadań.

Zalecamy również zapoznanie się z [artykułem o wydajności i optymalizacji](search-performance-optimization.md).

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



