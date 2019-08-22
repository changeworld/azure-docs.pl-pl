---
title: Administrowanie usługą dla Azure Search w portalu — Azure Search
description: Zarządzanie usługą Azure Search, hostowaną usługą wyszukiwania w chmurze na Microsoft Azure przy użyciu Azure Portal.
author: HeidiSteen
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2c4b2a03e7e5c818453eaf4ad6881b2caba3b93c
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647675"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administrowanie usługą dla Azure Search w Azure Portal
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Search to w pełni zarządzana usługa wyszukiwania oparta na chmurze służąca do tworzenia bogatego środowiska wyszukiwania w aplikacjach niestandardowych. W tym artykule omówiono zadania administracyjne usługi, które można wykonać w [Azure Portal](https://portal.azure.com) dla usługi wyszukiwania, która została już zainicjowana. Administrowanie usługami jest lekkie przez projektowanie, ograniczone do następujących zadań:

> [!div class="checklist"]
> * Zarządzanie dostępem do *kluczy API-Keys* używanych na potrzeby dostępu do odczytu lub zapisu do usługi.
> * Dostosuj pojemność usługi, zmieniając alokację partycji i replik.
> * Monitoruj użycie zasobów względem maksymalnych limitów warstwy usług.

Należy zauważyć, że *uaktualnienie* nie jest wymienione jako zadanie administracyjne. Ponieważ zasoby są alokowane po aprowizacji usługi, przeniesienie do innej warstwy wymaga nowej usługi. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie usługi Azure Search](search-create-service-portal.md).

> [!Tip]
> Szukasz pomocy na temat analizowania ruchu wyszukiwania lub wydajności zapytań? Można monitorować woluminy zapytań, które wyszukiwane są osoby i jak pomyślne wyniki wyszukiwania znajdują się w identyfikatorach klientów, którzy mają do określonych dokumentów w indeksie. Aby uzyskać więcej informacji, zobacz [Analiza ruchu wyszukiwania Azure Search](search-traffic-analytics.md), [monitorowania metryk użycia i zapytań](search-monitor-usage.md)oraz [wydajności i optymalizacji](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Prawa administratora
Inicjowanie obsługi administracyjnej lub likwidowanie samej usługi może odbywać się przez administratora subskrypcji platformy Azure lub współadministratora.

W ramach usługi każda osoba mająca dostęp do adresu URL usługi i klucza API administratora ma dostęp do odczytu i zapisu do usługi. Dostęp do odczytu i zapisu umożliwia dodawanie, usuwanie i modyfikowanie obiektów serwera, w tym klucze API, indeksy, indeksatory, źródła danych, harmonogramy i przypisania ról, które są implementowane za pomocą [ról zdefiniowanych](search-security-rbac.md)przez funkcję RBAC.

Wszystkie interakcje użytkownika z Azure Search są w jednym z następujących trybów: dostęp do odczytu i zapisu do usługi (prawa administratora) lub dostęp tylko do odczytu do usługi (uprawnienia zapytania). Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami interfejsu API](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Rejestrowanie i informacje o systemie
Azure Search nie uwidacznia plików dziennika dla pojedynczej usługi za pomocą portalu ani interfejsów programistycznych. W warstwie Podstawowa i powyżej firma Microsoft monitoruje wszystkie usługi Azure Search na dostępność na 99,9% według umów dotyczących poziomu usług (SLA). Jeśli usługa działa wolno lub żądanie przepływności spadnie poniżej progów SLA, zespoły pomocy technicznej Przejrzyj pliki dziennika dostępne dla nich i rozwiąż problem.

W zakresie ogólnych informacji o usłudze możesz uzyskać informacje w następujący sposób:

* W portalu na pulpicie nawigacyjnym usługi, za pomocą powiadomień, właściwości i komunikatów o stanie.
* Korzystanie z [programu PowerShell](search-manage-powershell.md) lub [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/) w celu [pobrania właściwości usługi](https://docs.microsoft.com/rest/api/searchmanagement/services)lub stanu użycia zasobów indeksu.
* Za pomocą [analizy ruchu wyszukiwania](search-traffic-analytics.md), jak wspomniano wcześniej.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorowanie użycia zasobów
Na pulpicie nawigacyjnym monitorowanie zasobów jest ograniczone do informacji wyświetlanych na pulpicie nawigacyjnym usługi i kilku metryk, które można uzyskać, wykonując zapytania dotyczące usługi. Na pulpicie nawigacyjnym usługi, w sekcji użycie, można szybko określić, czy poziomy zasobów partycji są odpowiednie dla aplikacji. Możesz zainicjować obsługę zasobów zewnętrznych, takich jak monitorowanie platformy Azure, jeśli chcesz przechwytywać i utrwalać zarejestrowane zdarzenia. Aby uzyskać więcej informacji, zobacz [monitorowanie Azure Search](search-monitor-usage.md).

Korzystając z interfejsu API REST Search Service, można programowo pobrać liczbę dokumentów i indeksów: 

* [Pobieranie statystyki indeksu](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Liczba dokumentów](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Odzyskiwanie po awarii i awaria usługi

Mimo że możemy odzyskać Twoje dane, Azure Search nie zapewnia natychmiastowej pracy w trybie failover w przypadku wystąpienia awarii w klastrze lub na poziomie centrum danych. Jeśli klaster ulegnie awarii w centrum danych, zespół operacyjny wykryje i zacznie działać do przywracania usługi. Podczas przywracania usługi możesz zażądać kredytów na korzystanie z usługi, aby wyrównać niedostępność usługi na [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Jeśli ciągła usługa jest wymagana w przypadku wystąpienia awarii poza kontrolą firmy Microsoft, można [zainicjować obsługę administracyjną dodatkowej usługi](search-create-service-portal.md) w innym regionie i wdrożyć strategię replikacji geograficznej, aby upewnić się, że indeksy są w pełni nadmiarowe dla wszystkich usług.

Klienci używający [indeksatorów](search-indexer-overview.md) do wypełniania i odświeżania indeksów mogą obsługiwać odzyskiwanie po awarii za pomocą indeksatorów specyficznych dla lokalizacji geograficznych wykorzystujących to samo źródło danych. Dwie usługi w różnych regionach, z których każdy uruchamia indeksator, może indeksować to samo źródło danych w celu zapewnienia nadmiarowości geograficznej. W przypadku indeksowania ze źródeł danych, które są również geograficznie nadmiarowe, należy pamiętać, że indeksatory Azure Search mogą wykonywać tylko indeksowanie przyrostowe z replik podstawowych. W przypadku zdarzenia trybu failover Pamiętaj, aby ponownie wskazać indeksator do nowej repliki podstawowej. 

Jeśli nie używasz indeksatorów, użyj kodu aplikacji do wypychania obiektów i danych do różnych usług wyszukiwania równolegle. Aby uzyskać więcej informacji, zobacz [wydajność i optymalizacja w Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Ponieważ Azure Search nie jest podstawowym rozwiązaniem magazynu danych, firma Microsoft nie udostępnia formalnego mechanizmu tworzenia i przywracania kopii zapasowych samoobsługi. Kod aplikacji używany do tworzenia i wypełniania indeksu jest opcją przywracania w przypadku usunięcia indeksu przez pomyłkę. 

Aby ponownie skompilować indeks, należy go usunąć (przy założeniu, że istnieje), ponownie utworzyć indeks w usłudze i załadować ponownie przez pobranie danych z podstawowego magazynu danych.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Skalowanie w górę lub w dół
Każda usługa wyszukiwania jest uruchamiana z co najmniej jedną repliką i jedną partycją. Jeśli zarejestrowano się w celu uzyskania [warstwy zapewniającej zasoby dedykowane](search-limits-quotas-capacity.md), kliknij kafelek **skalowanie** na pulpicie nawigacyjnym usługi, aby dostosować użycie zasobów.

Po dodaniu pojemności za pośrednictwem dowolnego zasobu usługa korzysta z nich automatycznie. W Twojej części nie są wymagane żadne dalsze działania, ale istnieje niewielkie opóźnienie przed zrealizowaniem nowego zasobu. Udostępnienie dodatkowych zasobów może potrwać 15 minut lub dłużej.

 ![][10]

### <a name="add-replicas"></a>Dodaj repliki
Rosnące zapytania na sekundę (zapytań) lub osiąganie wysokiej dostępności są wykonywane przez dodanie replik. Każda replika ma jedną kopię indeksu, więc dodanie jednej repliki tłumaczy do jednego indeksu dostępnego do obsługi żądań zapytań usługi. Do wysokiej dostępności są wymagane co najmniej 3 repliki (zobacz [Planowanie pojemności](search-capacity-planning.md) , aby uzyskać szczegółowe informacje).

Usługa wyszukiwania mająca więcej replik może równoważyć obciążenie żądaniami zapytań w większej liczbie indeksów. Uwzględniając poziom woluminu zapytania, przepływność zapytań jest szybsza, gdy istnieje więcej kopii indeksu dostępnych do obsługi żądania. Jeśli występują opóźnienia zapytań, można spodziewać się pozytywnego wpływu na wydajność, gdy dodatkowe repliki są w trybie online.

Mimo że przepływność zapytań jest dodawana podczas dodawania replik, nie jest ona dokładnie podwójna ani potrójna podczas dodawania replik do usługi. Wszystkie aplikacje wyszukiwania podlegają zewnętrznym czynnikom, które mogą wykonywać pingowanie względem wydajności zapytań. Złożone zapytania i opóźnienia sieci to dwa czynniki, które przyczyniają się do wahań czasów odpowiedzi na zapytanie.

### <a name="add-partitions"></a>Dodawanie partycji
Większość aplikacji usługi ma wbudowaną potrzebę tworzenia większej liczby replik, a nie partycji. W przypadku, gdy jest wymagana większa liczba dokumentów, można dodać partycje, jeśli utworzono konto w usłudze Standard. Warstwa Podstawowa nie zapewnia dodatkowych partycji.

W warstwie Standardowa partycje są dodawane w wielokrotnościach 12 (w odróżnieniu od 1, 2, 3, 4, 6 lub 12). To jest artefakt fragmentowania. Indeks jest tworzony w 12 fragmentów, który może być przechowywany na 1 partycji lub równo podzielony na 2, 3, 4, 6 lub 12 partycji (jedną fragmentu na partycję).

### <a name="remove-replicas"></a>Usuwanie replik
Po upływie okresów dużych ilości zapytań można użyć suwaka, aby zmniejszyć liczbę replik po ukończeniu obciążeń zapytań wyszukiwania. W Twojej części nie są wymagane żadne dalsze czynności. Zmniejszenie liczby replik zwalnia maszyny wirtualne w centrum danych. Wykonywanie zapytań i operacji pozyskiwania danych zostanie teraz uruchomione na mniejszej liczbie maszyn wirtualnych niż wcześniej. Minimalnym wymaganiem jest jedna replika.

### <a name="remove-partitions"></a>Usuwanie partycji
W przeciwieństwie do usuwania replik, które nie wymagają żadnych dodatkowych nakładów pracy, może być konieczne wykonanie pewnych zadań w przypadku używania większej ilości miejsca niż można je zmniejszyć. Na przykład jeśli Twoje rozwiązanie używa trzech partycji, downsizing do jednej lub dwóch partycji wygeneruje błąd, jeśli nowe miejsce do magazynowania jest mniejsze niż wymagane do hostowania Twojego indeksu. Jak można się spodziewać, wybrane opcje polegają na usunięciu indeksów lub dokumentów w skojarzonym indeksie, aby zwolnić miejsce lub zachować bieżącą konfigurację.

Nie ma metody wykrywania informującej, który indeks fragmentów jest przechowywany w określonych partycjach. Każda partycja zapewnia około 25 GB miejsca w magazynie, dlatego należy zmniejszyć ilość miejsca w magazynie do rozmiaru, który można obsłużyć przez liczbę posiadanych partycji. Jeśli chcesz przywrócić jedną partycję, należy dopasować wszystkie 12 fragmentów.

Aby ułatwić planowanie w przyszłości, warto sprawdzić magazyn (przy użyciu [statystyk Get index](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)), aby zobaczyć, ile faktycznie używasz. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Najlepsze rozwiązania dotyczące skalowania i wdrażania
Te 30-minutowe przeglądy wideo są najlepsze rozwiązania dla zaawansowanych scenariuszy wdrażania, w tym obciążeń rozproszonych geograficznie. Możesz również zobaczyć [wydajność i optymalizację w Azure Search](search-performance-optimization.md) dla stron pomocy, które obejmują te same punkty.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Następne kroki
Po zrozumieniu koncepcji związanych z administracją usługi należy rozważyć użycie [programu PowerShell](search-manage-powershell.md) w celu zautomatyzowania zadań.

Zalecamy również przejrzenie artykułu dotyczącego [wydajności i optymalizacji](search-performance-optimization.md).

Innym zaleceniem jest Obejrzyj film wideo wymieniony w poprzedniej sekcji. Zapewnia to dokładniejsze pokrycie technik wymienionych w tej sekcji.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



