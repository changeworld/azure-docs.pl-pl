---
title: Wprowadzenie — przewodnik dla operatorów IT na platformie Azure | Dokumentacja firmy Microsoft
description: Pobierz Przewodnik Wprowadzenie dla operatorów IT na platformie Azure
services: ''
documentationcenter: ''
author: themichaelbender-ms
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: ''
ms.topic: overview
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 08/24/2018
ms.author: mibender
ms.openlocfilehash: 1222395fd8efb7cf189ae6678f6c39f5a6c63157
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051174"
---
# <a name="get-started-for-azure-it-operators"></a>Wprowadzenie dla operatorów IT na platformie Azure

Ten przewodnik opisuje podstawowe pojęcia związane z wdrażaniem i zarządzaniem infrastrukturą Microsoft Azure. Jeśli dopiero zaczynasz do chmury obliczeniowej lub na platformie Azure, ten przewodnik pomaga szybko rozpocząć pracę z założenia, wdrożenia oraz szczegóły dotyczące zarządzania. Wiele sekcji, w tym przewodniku omówiono operacji takich jak wdrażanie maszyny wirtualnej i podajemy link do pogłębionych szczegółów technicznych.

## <a name="cloud-computing-overview"></a>Omówienie obliczeń w chmurze

Przetwarzanie jest nowoczesny alternatywą dla tradycyjnych w lokalnym centrum danych w chmurze. Dostawców chmury publicznej zapewniają i zarządzaj nimi cała infrastruktura obliczeniowa i podstawowe oprogramowanie do zarządzania. Tych dostawców oferują szeroki wybór usług w chmurze. W tym przypadku usługa w chmurze może być, maszynę wirtualną, serwer sieci web lub aparatu bazy danych z hostowanymi w chmurze. Jako klient dostawcy chmury dzierżawy jest tych usług w chmurze na zgodnie z potrzebami. W ten sposób nakładów inwestycyjnych konserwacji sprzętu konwertowanie koszty operacyjne. Usługa w chmurze zapewnia również następujące korzyści:

- Szybkie wdrażanie środowisk obliczeniowych duże

- Szybkie dezalokacji systemów, które nie są już wymagane

- Łatwe wdrażanie tradycyjnie złożonych systemów, takich jak moduły równoważenia obciążenia

- Możliwość zapewnienia możliwości obliczeniowych elastyczne lub skalowania w razie

- Bardziej ekonomiczne środowisk obliczeniowych

- Dostęp z dowolnego miejsca za pomocą opartego na sieci web portalu lub programowy automatyzacji

- Usługi w chmurze do potrzeb większości obliczeń i aplikacji

Za pomocą infrastruktury lokalnej masz pełną kontrolę nad sprzętu i oprogramowania, które zostało wdrożone. W przeszłości doprowadziło to do decyzji dotyczących zakupów sprzętu koncentrujących się na skalowanie w górę. Przykładem jest zakup serwera za pomocą więcej rdzeni, aby spełnić szczytowe potrzeb związanych z wydajnością. Niestety infrastruktura ta może być skutkowało niewystarczającym wykorzystaniem poza oknem żądanie. Dzięki systemowi Azure wdrażania infrastruktury, które są potrzebne i dostosować to w górę lub w dół w dowolnym momencie. Prowadzi to do fokus na skalowanie w poziomie poprzez wdrożenie dodatkowych węzłów obliczeniowych do zaspokojenia potrzeb wydajności. Skalowanie usług w chmurze jest bardziej opłacalna niż skalowanie w górę za pośrednictwem drogiego sprzętu.

Microsoft wdrożono wiele centrów danych platformy Azure na całym świecie, kolejne zaplanowane. Ponadto Microsoft zwiększa się należących do suwerennej chmury w regionach, takich jak Chinach i Niemczech. Największe globalnego przedsiębiorstwa można wdrażać centrów danych w ten sposób, tak, aby korzystać z platformy Azure ułatwia przedsiębiorstwom dowolnego rozmiaru w celu wdrażania ich usług blisko swoich klientów.

Dla małych firm platforma Azure zezwoli dla punktu wejścia niskich kosztach z możliwością skalowania w szybko, jak żądanie do zwiększenia obliczeń. Pozwala to uniknąć ponoszenia dużych inwestycji kapitałowych w infrastrukturze i zapewnia elastyczność architektury i przebudowywania systemów, zgodnie z potrzebami. Korzystanie z chmury obliczeniowej mieści się również z modelem szybkie skalowanie i bezawaryjną wzrostu uruchamiania.

Aby uzyskać więcej informacji na temat dostępnych regionów platformy Azure, zobacz [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Model przetwarzania w chmurze

Platforma Azure używa chmurowego przetwarzania danych modelu na podstawie kategorii usługi udostępniane klientom. Trzy kategorie usług obejmują infrastruktury jako usługi (IaaS), platforma jako usługa (PaaS) i oprogramowanie jako usługa (SaaS). Dostawców udostępnić część lub całość odpowiedzialność składniki stosu obliczeń w każdej z tych kategorii. Przyjrzyjmy się każdej z kategorii dla chmury obliczeniowej.
![Cloud Computing Stack Comparison](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktura jako usługa

Dostawcy chmury IaaS działa i zarządza wszystkie zasoby obliczeniowe fizycznych oraz wymaganego oprogramowania, aby włączyć wirtualizacji komputerów. Maszyny wirtualne w tych hostowanych centrach danych jest wdrażane przez klienta tej usługi. Mimo że maszyny wirtualne znajdują się w centrum danych poza siedzibą firmy, klient IaaS ma kontrolę konfiguracji i zarządzania, pozostawiając podstawowej infrastruktury dostawcy chmury systemu operacyjnego.

System Azure zawiera kilka rozwiązań IaaS, w tym maszyny wirtualne, zestawy skalowania maszyn wirtualnych i powiązanych infrastruktury sieci. Maszyny wirtualne są popularne wybór początkowo migracji usług na platformie Azure, ponieważ umożliwia modelu migracji "metodą lift and shift". Można skonfigurować Maszynę wirtualną infrastrukturą aktualnie uruchomione usługi w centrum danych, a następnie przeprowadzić migrację oprogramowania do nowej maszyny Wirtualnej. Może być konieczne aktualizowanie konfiguracji, takie jak adresy URL do innych usług lub magazynu, ale wiele aplikacji w ten sposób można migrować.

Zestawy skalowania maszyn wirtualnych są tworzone na podstawie maszyn wirtualnych platformy Azure i zapewniają łatwy sposób wdrażania klastrów identycznych maszyn wirtualnych. Zestawy skalowania maszyn wirtualnych obsługują także skalowania automatycznego, aby nowe maszyny wirtualne można wdrożyć automatycznie gdy jest to wymagane. To sprawia, że zestawy skalowania maszyn wirtualnych jest idealną platformą w klastrach hostów z wyższego poziomu mikrousług obliczeniowych, takich jak Azure Service Fabric i usługi Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platforma jako usługa

Dzięki temu możesz wdrożyć aplikację w środowisku, które zapewnia dostawcy usług chmury. Dostawca zawiera wszystkie zarządzania infrastrukturą, dzięki czemu możesz skupić się na tworzeniu aplikacji i zarządzania danymi.

Platforma Azure udostępnia kilka PaaS obliczeniowe ofert, łącznie z funkcji Web Apps w usłudze Azure App Service i Azure Cloud Services (role sieć web i proces roboczy). W obu przypadkach deweloperzy mają wiele sposobów, aby wdrożyć swoją aplikację bez znajomości bolt i nuts, które go obsługują. Deweloperzy nie muszą tworzyć maszyny wirtualne (VM), użyj protokołu RDP (Remote Desktop), aby zalogować się do każdego z nich lub zainstalować aplikację. One po prostu kliknij przycisk (lub Zamknij, aby ją) i narzędzi dostarczanych przez firmę Microsoft, aprowizowanie maszyn wirtualnych i wdrożyć i zainstalować aplikację na nich.

#### <a name="saas-software-as-a-service"></a>SaaS: Oprogramowanie jako usługa

SaaS to oprogramowanie, które centralne hostowaną i zarządzaną. Zazwyczaj jest on oparty na architektury wielodostępnej — jednej wersji aplikacji jest używany dla wszystkich klientów. Może być skalowana do wielu wystąpień, aby zapewnić najlepszą wydajność we wszystkich lokalizacjach. Oprogramowanie SaaS zwykle jest licencjonowane w ramach miesięcznej lub rocznej subskrypcji. Oprogramowanie SaaS zwykle jest licencjonowane w ramach miesięcznej lub rocznej subskrypcji. SaaS dostawców oprogramowania są odpowiedzialne za wszystkie składniki stosu oprogramowania, więc wszystko, czego możesz zarządzać świadczonych usług.

Microsoft Office 365 jest dobrym przykładem SaaS oferty. Subskrybenci zapłacić opłatę, miesięcznej lub rocznej subskrypcji i staną się Microsoft Exchange, Microsoft OneDrive i pozostałą część pakietu Microsoft Office jako usługa. Subskrybenci uzyskują zawsze najnowszej wersji i Exchange server jest zarządzana za Ciebie. W porównaniu do instalowania i uaktualniania pakietu Office, co roku, jest to mniej kosztowne i wymaga mniej wysiłku.

## <a name="azure-services"></a>Usługi platformy Azure

Platforma Azure oferuje wiele usług w jej platforma obliczeniowa w chmurze. Te usługi są następujące.

### <a name="compute-services"></a>Usługi obliczeniowe

Usługi hostingu i obciążenie aplikacji:

- Usługa Azure Virtual Machines — Linux i Windows

- Usługi aplikacji (aplikacje sieci Web, aplikacje mobilne, aplikacje logiki, aplikacje interfejsu API i aplikacje funkcji)

- Usługa Azure Batch (w przypadku dużych równoległych i wsadowych zadań obliczeniowych)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Data services

Usługi magazynowania i zarządzanie danymi:

- Usługa Azure Storage (składa się z usług obiektów Blob platformy Azure, kolejki, tabela i plik)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>Usługi aplikacji

Usługi dotyczące tworzenia i obsługi aplikacji:

- Azure Active Directory (Azure AD)

- Usługa Azure Service Bus do łączenia z systemami rozproszonymi

- Usługa Azure HDInsight do przetwarzania danych big data

- Azure Scheduler

- Azure Media Services

### <a name="network-services"></a>Usługi sieciowe

Usługi w przypadku sieci zarówno w obrębie platformy Azure, jak i między centrami danych platformy Azure i lokalnej:

- Azure Virtual Network

- Usługa ExpressRoute systemu Azure

- DNS platformy Azure

- Azure Traffic Manager

- Azure Content Delivery Network

Aby uzyskać szczegółową dokumentację usług platformy Azure, zobacz [dokumentacja usługi Azure service](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Kluczowe pojęcia dotyczące usługi Azure

### <a name="datacenters-and-regions"></a>Centra danych i regionów

Azure to platforma chmury globalnej, która jest ogólnie dostępna w wielu regionach na całym świecie. Podczas aprowizowania usługi, aplikacji lub maszyny Wirtualnej na platformie Azure, zostanie wyświetlony monit wybierz region. Wybrany region reprezentuje speciﬁc centrum danych, której działa Twoja aplikacja. Aby uzyskać więcej informacji, zobacz [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/).

Jeden beneﬁts użycia platformy Azure jest wdrożenie aplikacji w różnych centrach danych na całym świecie. Region, który wybierzesz można aﬀect wydajność aplikacji. Jest optymalne, aby wybrać region, który jest bliżej większość klientów, aby zmniejszyć opóźnienia w żądaniach sieci. Możesz również wybrać region, aby spełnić wymagania prawne dystrybucji aplikacji w przypadku pewnych krajów.

### <a name="azure-portal"></a>Azure Portal

Azure portal to aplikacji sieci web, który może służyć do tworzenia, zarządzania i usuwać zasoby platformy Azure i usługi. Witryna Azure portal znajduje się w [portal.azure.com](https://portal.azure.com). Obejmuje dostosowywany pulpit nawigacyjny oraz narzędzia do zarządzania zasobami platformy Azure. Zawiera także informacje pomoc dotycząca rozliczeń i subskrypcji. Aby uzyskać więcej informacji, zobacz [omówienie portalu Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) i [zarządzanie zasobami platformy Azure za pośrednictwem portalu](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Zasoby

Zasoby platformy Azure są poszczególne obliczeniowych, sieci, danych lub aplikacji hostowanie usług, które zostały wdrożone do subskrypcji platformy Azure. Niektóre typowe zasoby to maszyny wirtualne, konta magazynu lub bazy danych SQL. Usługi platformy Azure często składają się z kilku powiązanych zasobów platformy Azure. Na przykład maszynę wirtualną platformy Azure może obejmować maszyna wirtualna, konto magazynu, karta sieciowa i publiczny adres IP. Te zasoby można tworzyć, zarządzane i usuwane indywidualnie lub jako grupa. Zasoby platformy Azure zostały omówione bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="resource-groups"></a>Grupy zasobów

Grupę zasobów platformy Azure jest kontenerem, który zawiera powiązane zasoby rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Grupy zasobów platformy Azure zostały omówione bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Szablon usługi Azure Resource Manager jest plik JavaScript Object Notation (JSON), który definiuje jeden lub więcej zasobów do wdrożenia w grupie zasobów. Definiuje również zależności między wdrożonych zasobów. Szablony usługi Resource Manager zostały omówione bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="automation"></a>Automatyzacja

Oprócz tworzenia, zarządzania i usuwanie zasobów za pomocą witryny Azure portal te działania można zautomatyzować za pomocą programu PowerShell lub interfejsu wiersza polecenia (CLI) platformy Azure.

#### <a name="azure-powershell"></a>Azure PowerShell

Program Azure PowerShell to zestaw modułów, które dostarczają poleceń cmdlet do zarządzania platformy Azure. Można użyć polecenia cmdlet do tworzenia, zarządzania i usuwania usług platformy Azure. Polecenia cmdlet może pomóc osiągnąć spójną, powtarzalną i zdalne wdrożenia. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure jest narzędziem, które służy do tworzenia, zarządzania i usunąć zasoby platformy Azure z poziomu wiersza polecenia. Interfejs wiersza polecenia platformy Azure jest dostępna dla systemów Linux, Mac OS X i Windows. Aby uzyskać więcej informacji i szczegóły techniczne, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>Interfejsy API REST

Jest ona oparta na zestaw interfejsów API REST, która obsługuje interfejs użytkownika witryny Azure portal. Większość tych interfejsów API REST są również obsługiwane umożliwia programowe obsługi administracyjnej i zarządzanie zasobami platformy Azure i aplikacji z dowolnego urządzenia z włączoną funkcją łączności internetowej. Aby uzyskać więcej informacji, zobacz [odwołanie do zestawu SDK REST platformy Azure](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Administratorzy mogą uzyskiwać dostęp do programu Azure PowerShell i wiersza polecenia platformy Azure za pośrednictwem przeglądarki dostępne środowisko o nazwie usługi Azure Cloud Shell. Ten interfejs interakcyjne zapewnia elastyczne narzędzia dla systemów Linux i Windows administratorzy mogą używać ich interfejsu wiersza polecenia, wyboru, Bash lub programu PowerShell. Usługa Azure Cloud Shell może być dostęp za pośrednictwem portalu, jako interfejs autonomicznej w sieci web w [shell.azure.com](https://shell.azure.com), lub z wielu innych punktów dostępu. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Subskrypcje platformy Azure

Subskrypcji to logiczna grupa usług platformy Azure, która jest połączona z kontem platformy Azure. Jedno konto platformy Azure może zawierać wiele subskrypcji. Opłaty za usługi platformy Azure odbywa się na podstawie każdej subskrypcji. Subskrypcje platformy Azure ma Administrator konta, który ma pełną kontrolę nad subskrypcją, a Administrator usługi, kto ma kontrolę nad wszystkich usług w ramach subskrypcji. Aby uzyskać informacji na temat klasyczni Administratorzy, zobacz [Administratorzy subskrypcji platformy Azure Dodaj lub zmień](../../billing/billing-add-change-azure-subscription-administrator.md). Oprócz Administratorzy, mogą być udzielane indywidualnych kont szczegółową kontrolę zasobów platformy Azure przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Wybierz i Włącz subskrypcję platformy Azure

Przed rozpoczęciem pracy z usługami platformy Azure, potrzebna jest subskrypcja. Dostępnych jest kilka typów subskrypcji.

**Bezpłatne konta**: Link do rejestracji dla bezpłatnego konta znajduje się na [witryny sieci Web Azure](https://azure.microsoft.com/). Dzięki temu środki w ciągu 30 dni, aby wypróbować dowolną kombinację zasobów platformy Azure. Twoje konto jest zawieszone w przypadku przekroczenia kwoty kredytu. Po zakończeniu okresu próbnego usługi jest zamknięta i nie będą już działać. Można uaktualnić do subskrypcji zgodnie z rzeczywistym użyciem w dowolnym momencie.

**Subskrypcje MSDN**: Jeśli masz subskrypcję MSDN, możesz uzyskać określoną ilością w zawierających środki platformy Azure każdego miesiąca. Na przykład w przypadku programu Microsoft Visual Studio Enterprise z subskrypcją MSDN otrzymujesz \$150 miesięcznych środków platformy Azure.

W przypadku przekroczenia kwoty kredytu usługi są wyłączone do momentu rozpoczęcia następnego miesiąca. Możesz wyłączyć limit wydatków i dodać kartę kredytową, który ma być używany dla dodatkowych kosztów. Niektóre z tych kosztów są objęte rabatem dla kont w witrynie MSDN. Na przykład możesz wina systemu Linux dla maszyn wirtualnych z systemem Windows Server i nie ma dodatkowych opłat do serwerów firmy Microsoft, takich jak Microsoft SQL Server. To sprawia, że konta MSDN idealne rozwiązanie w przypadku opracowywania i testowania scenariuszy.

**Konta programu BizSpark**: W programie BizSpark firmy Microsoft oferuje wiele korzyści dla startupów. Jedną z tych korzyści jest dostęp do oprogramowania firmy Microsoft dla środowiska deweloperskie i testowe do pięciu kont MSDN. Możesz uzyskać 150 USD środki platformy Azure w każdym z tych pięciu kont MSDN i płacić według obniżonych stawek za kilka usług platformy Azure, takie jak maszyny wirtualne.

**Płatność za rzeczywiste użycie**: Z tą subskrypcją płacisz za to, czego używasz, dołączając na koncie karty kredytowej lub debetowej. Jeśli organizacja może również zostać zatwierdzony dla fakturowania.

**Umowy Enterprise Agreement**: Z umową enterprise agreement zdecydujesz się przy użyciu wielu usług platformy Azure w przyszłym roku, a opłaty są naliczane takimi problemami znacznie mniej wcześniej. Zobowiązanie, wprowadzone jest wykorzystywane przez cały rok. W przypadku przekroczenia kwoty zobowiązania mogą płacić za nadwyżkowe użycie w poprzednim. W zależności od kwoty zobowiązania Uzyskaj rabat na usługi platformy Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Przyznawaj dostęp administracyjny do subskrypcji platformy Azure

RBAC zawiera kilka wbudowanych ról, których można użyć, aby przypisać uprawnienia. Aby użytkownik jest administratorem subskrypcji platformy Azure, należy przypisać je [właściciela](../../role-based-access-control/built-in-roles.md#owner) rolę w zakresie subskrypcji. Rola właściciela zapewnia pełny dostęp użytkownika do wszystkich zasobów w ramach subskrypcji, łącznie z prawej strony można delegować dostępu do innych osób.

Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Wyświetlanie informacji dotyczących rozliczeń w witrynie Azure portal

Ważnym elementem programu przy użyciu platformy Azure jest możliwość wyświetlania informacji dotyczących rozliczeń. Azure portal udostępnia szczegółowy wgląd w informacje rozliczeniowe platformy Azure.

Aby uzyskać więcej informacji, zobacz [jak pobrać faktury i dane dziennego użycia rozliczeniową za platformę Azure](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Uzyskiwanie informacji o rozliczeniach interfejsy API rozliczeń

Oprócz wyświetlania rozliczeń w portalu, można uzyskać dostęp do informacji rozliczeń za pomocą skryptu lub program do rozliczeń REST interfejsów API usługi Azure:

- Interfejs API użycia platformy Azure umożliwia pobieranie danych użycia. Można dostosować informacje rozliczeniowe użycia, przez otagowanie powiązanych zasobów platformy Azure. Na przykład możesz oznaczyć każdy z zasobów w grupie zasobów przy użyciu nazwy działu lub nazwę projektu i śledzić koszty specjalnie do tego jeden tag.

- Można użyć interfejsu API usługi Azure szybkość karty, aby wyświetlić listę wszystkich dostępnych zasobów wraz z metadanych i ceny informacje o każdej z tych zasobów.

Aby uzyskać więcej informacji, zobacz [wgląd w użycie zasobów usługi Microsoft Azure](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognozowane koszty za pomocą kalkulatora cen.

Ceny dla każdej usługi w systemie Azure jest inny. Wiele usług platformy Azure udostępniają warstw Basic, Standard i Premium. Zwykle każda warstwa ma kilka poziomów cen i wydajności. Za pomocą [online kalkulator cen](https://azure.microsoft.com/pricing/calculator), można utworzyć szacowania cen. Kalkulator zawiera elastyczność, aby oszacować koszt pojedynczego zasobu lub tej grupy zasobów.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Usługa Azure Resource Manager jest mechanizm wdrażania, zarządzania i organizacji dla zasobów platformy Azure. Za pomocą usługi Resource Manager, wiele poszczególne zasoby można umieścić ze sobą, w grupie zasobów.

Menedżer zasobów zawiera także możliwości wdrażania, które pozwalają możliwe do dostosowania wdrożenia i konfiguracji powiązane zasoby. Na przykład za pomocą usługi Resource Manager, można wdrożyć aplikację, która składa się z wielu maszyn wirtualnych, moduł równoważenia obciążenia i bazy danych SQL jako pojedyncza jednostka. Możesz tworzyć te wdrożenia przy użyciu szablonu usługi Resource Manager.

Usługa Resource Manager zapewnia kilka korzyści:

- Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).

- Można wielokrotnie wdrażać rozwiązanie podczas cyklu tworzenia oprogramowania i mieć pewność, że Twoje zasoby są wdrażane w spójnym stanie.

- Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

- Możliwość definiowania zależności między zasobami, aby wdrażać je w odpowiedniej kolejności.

- Możliwość stosowania kontroli dostępu do wszystkich usług w grupie zasobów, ponieważ RBAC natywnej integracji z platformą zarządzania.

- Znaczniki można dodawać dla zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.

- Możliwość wyjaśniania rozliczeń w organizacji przez wyświetlanie kosztów dla grupy zasobów, które mają tego samego tagu.

### <a name="tips-for-creating-resource-groups"></a>Wskazówki dotyczące tworzenia grup zasobów

Gdy wprowadzasz decyzji dotyczących grup zasobów, należy wziąć pod uwagę poniższe wskazówki:

- Wszystkie zasoby w grupie zasobów powinny mieć taki sam cykl życia.

- Zasób można przypisać tylko do jednej grupy naraz.

- Można dodać lub usunięcie zasobu z grupy zasobów, w dowolnym momencie. Każdy zasób musi należeć do grupy zasobów. Dlatego jeśli usuniesz zasób z jednej grupy, należy dodać go do innego.

- Większość typów zasobów można przenieść do innej grupy zasobów, w dowolnym momencie.

- Zasoby w grupie zasobów może być w różnych regionach.

- Grupa zasobów służy do kontrolowania dostępu do zasobów w niej.

### <a name="building-resource-manager-templates"></a>Tworzenie szablonów usługi Resource Manager

Szablony usługi Resource Manager deklaratywne Definiowanie zasobów i konfiguracje zasobów, które zostaną wdrożone do pojedynczej grupy zasobów. Szablony usługi Resource Manager umożliwia organizowanie złożonych wdrożeń bez konieczności nadmiernego skryptów lub konfiguracji ręcznej. Po tworzenia szablonu można wdrożyć wiele razy — każdorazowo przy użyciu identycznych wyniku.

Szablon usługi Resource Manager obejmuje cztery sekcje:

- **Parametry**: Są to dane wejściowe do wdrożenia. Można podać wartości parametrów przez człowieka lub zautomatyzowane procesy. Nazwa użytkownika administratora i hasło dla maszyny Wirtualnej z systemem Windows musi być parametrem przykładu. Wartości parametrów są używane w całym wdrażaniu, gdy są one określone.

- **Zmienne**: Są one używane do przechowywania wartości, które są używane w całym wdrożeniu. W przeciwieństwie do parametrów wartość zmiennej nie została podana w czasie wdrażania. Zamiast tego jest twardych kodowane czy generowana dynamicznie.

- **Zasoby**: Ta sekcja szablon definiuje zasoby do wdrożenia, takie jak maszyny wirtualne, konta magazynu i sieci wirtualnych.

- **Dane wyjściowe**: Po zakończeniu wdrażania usługi Resource Manager może zwrócić dane, takie jak parametry połączenia generowanych dynamicznie.

Są dostępne następujące mechanizmy automatyzacji wdrażania:

- **Funkcje**: Kilka funkcji można użyć w szablonach usługi Resource Manager. Obejmują one operacje, takie jak Konwersja ciągu na małe litery, wdrażanie wielu wystąpień klasy zasobem zdefiniowanych i dynamicznie zwracanie docelowej grupy zasobów. Funkcje Menedżera zasobów pomagające w tworzeniu wdrożeń dynamicznych.

- **Zależności zasobu**: Podczas wdrażania wielu zasobów, niektóre zasoby mają zależności na innych użytkowników. W celu ułatwienia wdrażania, można użyć deklaracji zależności, tak aby wdrażane zasoby zależne przed innymi.

- **Łączenie szablonu**: Z w ramach jednego szablonu usługi Resource Manager można połączyć inny szablon. Dzięki temu dekompozycji wdrożenia do zestawu szablonów specjalistyczne, określonego celu.

Możesz tworzyć szablony usługi Resource Manager w dowolnym edytorze tekstów. Zestaw Azure SDK dla programu Visual Studio zawiera jednak narzędzia pomocne. Za pomocą programu Visual Studio, możesz dodać zasoby do szablonu, za pomocą kreatora, a następnie wdrażanie i debugowanie szablon bezpośrednio z poziomu programu Visual Studio. Aby uzyskać więcej informacji, zobacz [tworzenia usługi Azure Resource Manager](../../resource-group-authoring-templates.md).

Na koniec można przekonwertować istniejących grup zasobów do szablonu do ponownego wykorzystania w witrynie Azure portal. Może to być przydatne, jeśli chcesz utworzyć szablon można wdrożyć istniejącą grupę zasobów lub po prostu chcesz zbadać podstawowym JSON. Aby wyeksportować grupy zasobów, wybierz **skrypt automatyzacji** przycisk Ustawienia grupy zasobów.

## <a name="security-of-azure-resources-rbac"></a>Zabezpieczenia zasobów platformy Azure (RBAC)

Możesz udzielić operacyjnej dostępu do kont użytkowników w określonym zakresie: subskrypcji, grupy zasobów lub poszczególnych zasobów. Oznacza to, można wdrożyć zestaw zasobów w grupie zasobów, np. maszyna wirtualna i wszystkie pokrewne zasoby i udzielanie uprawnień do określonego użytkownika lub grupy. To podejście ogranicza dostęp do zasobów, które należą do docelowej grupy zasobów. Można również przyznać dostęp do jednego zasobu, np. maszyny wirtualnej lub sieci wirtualnej.

Aby udzielić dostępu, możesz przypisać rolę użytkownikowi lub grupie użytkowników. Istnieje wiele wstępnie zdefiniowanych ról. Można również definiować własne niestandardowe role.

Poniżej przedstawiono przykład kilka [wbudowanych ról na platformie Azure](../../role-based-access-control/built-in-roles.md):

- **Właściciel**: Użytkownik z tą rolą mogą zarządzać wszystkim łącznie z dostępem.

- **Czytnik**: Użytkownik z tą rolą mogą odczytywać zasoby wszystkich typów (z wyjątkiem wpisy tajne), ale nie może wprowadzać zmian.

- **Współautor maszyny wirtualnej**: Użytkownik z tą rolą mogą zarządzać maszynami wirtualnymi, ale nie może zarządzać sieci wirtualnej, do której są one połączone lub konta magazynu, w którym znajduje się plik wirtualnego dysku twardego.

- **Współautor bazy danych SQL**: Użytkownik z tą rolą mogą zarządzać bazami danych SQL, ale nie ich zasadami związanymi z zabezpieczeniami.

- **Menedżer zabezpieczeń SQL**: Użytkownik z tą rolą mogą zarządzać zasad związanych z zabezpieczeniami serwerów SQL i baz danych.

- **Współautor konta magazynu**: Użytkownik z tą rolą mogą zarządzać kontami magazynu, ale nie może zarządzać dostępem do konta magazynu.

Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Maszyny wirtualne platformy Azure jest jednym z centralnego usług IaaS na platformie Azure. Usługa Azure Virtual Machines obsługuje wdrażanie maszyn wirtualnych Windows lub Linux w centrum danych Microsoft Azure. Usługa Azure Virtual Machines mają całkowitą kontrolę nad konfiguracją maszyny Wirtualnej i są zobowiązani do instalacji oprogramowania, konfiguracji i konserwacji.

Podczas wdrażania maszyny Wirtualnej platformy Azure, można wybrać obraz z witryny Azure Marketplace lub podasz własnych uogólnionego obrazu. Ten obraz jest używany do stosowania systemu operacyjnego i konfiguracji początkowej. Podczas wdrażania usługi Resource Manager będzie obsługiwać niektórych ustawień konfiguracji, takich jak przypisanie nazwy komputera, poświadczeń administracyjnych i konfiguracji sieci. Rozszerzenia maszyny wirtualnej platformy Azure umożliwia dalsze automatyzację konfiguracji, takich jak Instalacja oprogramowania, konfiguracji oprogramowania antywirusowego i rozwiązania do monitorowania.

Można utworzyć maszyny wirtualne w wielu różnych rozmiarach. Rozmiar maszyny wirtualnej decyduje o alokacji zasobów, takich jak pojemność magazynu, pamięci i przetwarzania. W niektórych przypadkach określone funkcje, takie jak karty sieciowe z obsługą funkcji RDMA i dyski SSD są dostępne tylko w przypadku określonych rozmiarów maszyn wirtualnych. Aby uzyskać pełną listę rozmiarów maszyn wirtualnych i możliwości, zobacz "Rozmiary maszyn wirtualnych na platformie Azure" dla [Windows](../../virtual-machines/windows/sizes.md) i [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Przypadki zastosowań

Ponieważ usługa Azure virtual machines oferuje pełną kontrolę nad konfiguracją, doskonale nadają się do szerokiej gamy obciążeń serwera, które nie mieszczą się w modelu PaaS. Obciążenia serwera takich jak serwery bazy danych (SQL Server, Oracle lub bazy danych MongoDB), Windows Server Active Directory, Microsoft SharePoint i wiele więcej stanie się możliwe do uruchomienia na platformie Microsoft Azure. Jeśli to konieczne, można przenieść takich obciążeń z lokalnego centrum danych, do jednego lub więcej regionów platformy Azure, bez dużą ilość ponownej konfiguracji.

### <a name="deployment-of-virtual-machines"></a>Wdrażanie maszyn wirtualnych

Za pomocą witryny Azure portal, za pomocą automatyzacji przy użyciu modułu Azure PowerShell lub za pomocą automatyzacji przy użyciu wieloplatformowego interfejsu wiersza polecenia, można wdrożyć maszyny wirtualne platformy Azure.

#### <a name="portal"></a>Portal

Wdrażanie maszyny wirtualnej przy użyciu witryny Azure portal wymaga aktywnej subskrypcji platformy Azure i dostęp do przeglądarki sieci web. Możesz wybrać wiele obrazów systemów operacyjnych z różnymi konfiguracjami. Wszystkie magazynu i wymagania sieciowe są konfigurowane podczas wdrażania. Aby uzyskać więcej informacji, zobacz temat "Create maszynę wirtualną w witrynie Azure portal" dla [Windows](../../virtual-machines/windows/quick-create-portal.md) i [Linux](../../virtual-machines/linux/quick-create-portal.md).

Oprócz wdrażania maszyny wirtualnej w witrynie Azure portal, można wdrożyć szablonu usługi Azure Resource Manager z poziomu portalu. To wdroży i skonfiguruje wszystkie zasoby, zgodnie z definicją w szablonie. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Wdrażanie maszyny wirtualnej platformy Azure przy użyciu programu PowerShell umożliwia pełną automatyzację wszystkich zasobów powiązanych maszyny wirtualnej, w tym magazyn i sieć. Aby uzyskać więcej informacji, zobacz [Utwórz maszynę Wirtualną Windows przy użyciu usługi Resource Manager i programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Oprócz indywidualnie wdrażane zasoby obliczeniowe platformy Azure, modułu Azure PowerShell można użyć do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Interfejs wiersza polecenia (CLI)

Podobnie jak w przypadku moduł programu PowerShell, interfejsu wiersza polecenia platformy Azure zapewnia automatyzację wdrażania i mogą być używane w systemach Windows, OS X lub Linux. Jeśli używasz interfejsu wiersza polecenia Azure **maszyna wirtualna szybkiego tworzenia** polecenia i wszystkie pokrewne zasoby maszyny wirtualnej (w tym magazynu i sieci), a samej maszyny wirtualnej są wdrażane. Aby uzyskać więcej informacji, zobacz [tworzenie maszyny Wirtualnej z systemem Linux na platformie Azure przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/quick-create-cli.md).

Podobnie można użyć wiersza polecenia platformy Azure do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Dostęp i większe bezpieczeństwo dla maszyn wirtualnych

Uzyskiwanie dostępu do maszyny wirtualnej z Internetu wymaga skojarzonej sieciowej interfejs lub usługi load balancer, jeśli to konieczne, należy skonfigurować z publicznym adresem IP. Publiczny adres IP zawiera nazwę DNS, który będzie prowadzić do maszyny wirtualnej lub moduł równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [adresy IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Zarządzanie dostępem do maszyny wirtualnej za pośrednictwem publicznego adresu IP odbywa się przy użyciu zasób Sieciowej grupy zabezpieczeń sieci. Sieciowa grupa zabezpieczeń i działa jak zapora zezwala lub nie zezwala na ruch przez interfejs sieciowy lub podsieci na zestaw zdefiniowanych portów. Na przykład aby utworzyć sesję pulpitu zdalnego z Maszyną wirtualną platformy Azure, należy skonfigurować sieciowej grupy zabezpieczeń, aby zezwolić na ruch przychodzący na porcie 3389. Aby uzyskać więcej informacji, zobacz [Otwieranie portów dla maszyny Wirtualnej na platformie Azure przy użyciu witryny Azure portal](../../virtual-machines/windows/nsg-quickstart-portal.md).

Na koniec podobnie jak w przypadku zarządzania dowolnego systemu komputera, należy podać zabezpieczeń maszyny wirtualnej platformy Azure w systemie operacyjnym przy użyciu poświadczeń zabezpieczeń i zapór programowych.

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage jest zarządzany przez firmę Microsoft usługa, który udostępnia trwały, skalowalny i nadmiarowy magazyn. Konto magazynu platformy Azure jako zasób można dodać do dowolnej grupy zasobów przy użyciu dowolnej metody wdrażania zasobów. System Azure zawiera cztery typy magazynów: Magazyn obiektów blob, File Storage, Table storage i Queue storage. Podczas wdrażania konta magazynu, dwa typy kont są dostępne, ogólnego przeznaczenia i magazynie obiektów blob. Konto magazynu ogólnego przeznaczenia zapewnia dostęp do wszystkich czterech typów magazynów. Konta usługi blob storage są podobne do kont ogólnego przeznaczenia, ale zawiera specjalistyczne obiektów blob, które obejmują warstwy gorące i zimne dostępu. Aby uzyskać więcej informacji na temat usługi blob storage, zobacz [usługi Azure Blob storage](../../storage/blobs/storage-blob-storage-tiers.md).

Można skonfigurować różne poziomy nadmiarowości konta magazynu platformy Azure:

- **Magazyn lokalnie nadmiarowy** zapewnia wysoką dostępność, zapewniając, że trzy kopie danych są wykonywane synchronicznie przed zapisu jest uznawany za pomyślne. Te kopie są przechowywane w jednym obiekcie w pojedynczym regionie. Repliki znajdują się w oddzielnych domenach błędów i uaktualnień. Oznacza to, że dane są dostępne, nawet jeśli węzeł magazynu, organizuje Twoje dane, kończy się niepowodzeniem lub do trybu offline do zaktualizowania.

- **Magazyn geograficznie nadmiarowy** sprawia, że trzy synchronicznych kopii danych w regionie podstawowym w celu zapewnienia wysokiej dostępności, a następnie asynchronicznie sprawia, że trzy repliki w sparowanym regionie odzyskiwania po awarii.

- **Magazyn geograficznie nadmiarowy geograficznie** to magazyn geograficznie nadmiarowy, a także możliwość odczytu danych w regionie pomocniczym. Ta możliwość sprawia, że odpowiednie do odzyskiwania po awarii częściowe. Jeśli występuje problem z regionu podstawowego, możesz zmienić aplikację, aby mieć dostęp tylko do odczytu do sparowanym regionie.

### <a name="use-cases"></a>Przypadki zastosowań

Każdy typ magazynu ma przypadek użycia innego.

#### <a name="blob-storage"></a>Blob Storage

Wyraz *blob* jest akronimem *duży obiekt binarny*. Obiekty BLOB to pliki bez struktury, takich jak te, które są przechowywane na tym komputerze. Magazyn obiektów blob umożliwia przechowywanie dowolnego typu danych tekstowych lub binarnych, takich jak dokumenty, pliki multimedialne lub instalatory aplikacji. Magazyn obiektów blob jest również nazywany magazynem obiektów. Usługa Azure Blob storage przechowuje także dysków z danymi usługi Azure Virtual Machines.

Usługa Azure Storage obsługuje trzy typy obiektów blob:

- **Blokowe obiekty BLOB** są używane do przechowywania zwykłych pliki 195 GB (4 MB x 50 000 bloków) rozmiar. Głównym zastosowaniem dla blokowych obiektów blob jest przechowywanie plików, które są odczytywane od początku do końca, np. plików multimedialnych lub plików obrazów na potrzeby witryn sieci Web. Ponieważ pliki o rozmiarze większym niż 64 MB, należy przekazać w formie małych blokach są nazywane blokowych obiektów blob. Te bloki są następnie skonsolidowane lub przydzielonej do końcowego obiektu blob.

- **Stronicowe obiekty BLOB** są używane do przechowywania dostępu swobodnego pliki rozmiarze do 1 TB. Stronicowe obiekty BLOB są używane przede wszystkim jako magazyn zapasowy dla wirtualnych dysków twardych, które zapewniają trwałe dyski dla maszyn wirtualnych platformy Azure, IaaS obliczeń usługa na platformie Azure. Stronicowe obiekty BLOB są nazywane ponieważ zapewniają one dostęp losowego odczytu/zapisu do stron 512-bajtowego.

- **Uzupełnialne obiekty BLOB** składają się z bloków, takich jak obiekty BLOB typu block, ale są one zoptymalizowane pod kątem operacji dołączania. Są one często używane do rejestrowania informacji z jednego lub kilku źródeł do tego samego obiektu blob. Na przykład można napisać wszystkie swoje rejestrowanie śledzenia do tego samego uzupełnialnego obiektu blob dla aplikacji, która działa na wielu maszynach wirtualnych. Pojedynczy uzupełniany obiekt blob może być 195 GB.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Blob storage przy użyciu platformy .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>File Storage

Usługa Azure File storage to usługa, która oferuje udziały plików w chmurze przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Usługa obsługuje zarówno protokół SMB 2.1, jak i protokołu SMB 3.0. Za pomocą usługi Azure File storage można migrować aplikacje korzystające z udziałów plików na platformę Azure, szybko i bez kosztownych modyfikacji oprogramowania. Aplikacje uruchomione na maszynach wirtualnych platformy Azure, w usługach w chmurze lub lokalnych klientów można zainstalować udział plików w chmurze. Jest to podobne do jak aplikacja na komputerze instalująca typowy udział SMB. Dowolna liczba składników aplikacji może następnie równocześnie zainstalować udział Magazynu plików i uzyskiwać do niego dostęp.

Ponieważ udział magazynu plików to standardowy udział plików SMB, aplikacje działające na platformie Azure można uzyskać dostęp do danych w udziale za pośrednictwem interfejsów API We/Wy systemu plików. Deweloperzy mogą więc używać istniejący kod i własne umiejętności do migrowania istniejących aplikacji. Informatycy, można użyć poleceń cmdlet programu PowerShell do tworzenia, zainstalować i zarządzać nimi udziałów usługi File storage w ramach administracji aplikacjami platformy Azure.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure File storage w Windows](../../storage/files/storage-how-to-use-files-windows.md) lub [jak używać usługi Azure File storage z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Magazyn tabel

Magazyn tabel Azure to usługa, która przechowuje dane strukturalne NoSQL w chmurze. Magazyn tabel jest magazynem klucz atrybut z projektem bez schematu. Ponieważ Magazyn tabel jest magazynem bez schematu, to można łatwo zaadaptować dane aplikacji rozwijających się potrzeb. Dostęp do danych jest szybki i ekonomiczny dla wszystkich rodzajów aplikacji. Magazyn tabel jest zwykle znacznie tańszy niż tradycyjne bazy SQL dla podobnych ilości danych.

Magazyn tabel umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkownika dla aplikacji internetowych, książki adresowe, informacje o urządzeniach i wszelkie inne metadane, których wymaga Twoja usługa. Dowolną liczbę jednostek można przechowywać w tabeli. Konto magazynu może zawierać dowolną liczbę tabel w granicach pojemności konta magazynu.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure Table storage](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Usługa Azure Queue Storage umożliwia przesyłanie komunikatów za pomocą chmury między składnikami aplikacji. Projektowanie aplikacji do skalowania, składniki aplikacji są często odłączane, tak aby mogły być skalowane niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Magazyn kolejek obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure Queue storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Rozmieszczanie konta magazynu

Dostępnych jest kilka opcji wdrażania konta magazynu.

#### <a name="portal"></a>Portal

Wdrażanie na koncie magazynu przy użyciu witryny Azure portal wymaga aktywnej subskrypcji platformy Azure i dostęp do przeglądarki sieci web. Możesz wdrożyć nowe konto magazynu do nowej lub istniejącej grupy zasobów. Po utworzeniu konta magazynu można utworzyć obiektu blob kontener lub udział plików przy użyciu portalu. Można utworzyć tabelę i programowo kolejki magazynu jednostki. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](../../storage/common/storage-quickstart-create-account.md).

Oprócz wdrażania konta magazynu w witrynie Azure portal, można wdrożyć szablonu usługi Azure Resource Manager z poziomu portalu. To wdroży i skonfiguruje wszystkie zasoby, zgodnie z definicją w szablonie, w tym wszystkie konta magazynu. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Wdrażanie konta usługi Azure storage przy użyciu programu PowerShell umożliwia pełną automatyzację konta magazynu. Aby uzyskać więcej informacji, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Oprócz wdrażania zasobów platformy Azure indywidualnie, moduł Azure PowerShell można użyć do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Interfejs wiersza polecenia (CLI)

Podobnie jak w przypadku moduł programu PowerShell, interfejsu wiersza polecenia platformy Azure zapewnia automatyzację wdrażania i mogą być używane w systemach Windows, OS X lub Linux. Można użyć wiersza polecenia platformy Azure **Tworzenie konta magazynu** polecenie, aby utworzyć konto magazynu. Aby uzyskać więcej informacji, zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage.](../../storage/common/storage-azure-cli.md)

Podobnie można użyć wiersza polecenia platformy Azure do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia Azure](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Dostęp i większe bezpieczeństwo dla usługi Azure Storage

Usługa Azure Storage jest dostępna na różne sposoby, w tym do witryny Azure portal podczas tworzenia maszyny Wirtualnej i operacji i z biblioteki klienta magazynu.

#### <a name="virtual-machine-disks"></a>Dyski maszyny wirtualnej

Podczas wdrażania maszyny wirtualnej, należy utworzyć konto magazynu do przechowywania dysku systemu operacyjnego maszyny wirtualnej i dowolnego dodatkowego dysku z danymi. Można wybrać istniejące konto magazynu lub Utwórz nową. Pojedynczego dysku maszyny Wirtualnej, ponieważ maksymalny rozmiar obiektu blob to 1024 GB, ma maksymalny rozmiar 1,023 GB. Aby skonfigurować na większy dysk danych, można prezentować wiele dysków danych do maszyny wirtualnej i puli je ze sobą jako pojedynczy dysk logiczny. Aby uzyskać więcej informacji, zobacz "Zarządzanie dyskami platformy Azure" dla [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) i [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Narzędzia do magazynu

Konta usługi Azure storage są dostępne za pośrednictwem wielu eksploratory innego magazynu, takie jak Visual Studio Cloud Explorer. Te narzędzia umożliwiają przeglądanie kont usługi storage i danych. Więcej informacji oraz listę eksploratorów dostępnego magazynu, zobacz [narzędzia klienta usługi Azure Storage](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Interfejsu API usługi Storage

Za pomocą dowolnego języka, który może zgłaszać żądania HTTP/HTTPS można uzyskać dostępu do zasobów magazynu. Dodatkowo Magazyn Azure oferuje biblioteki programistyczne dla kilku popularnych języków. Te biblioteki upraszczają pracę z usługą Azure Storage przy obsłudze szczegółów takich jak wywołania synchroniczne i asynchroniczne, przetwarzanie wsadowe operacji, Zarządzanie wyjątkami i Automatyczne ponawianie prób. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST usługi Azure Storage](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Klucze dostępu do magazynu

Każde konto magazynu ma dwa klucze uwierzytelniania podstawowego i pomocniczego. Albo może służyć do operacji dostępu do magazynu. Te klucze magazynu są używane do zabezpieczania konta magazynu i są wymagane do uzyskiwania dostępu do danych. Istnieją dwa klucze, aby umożliwić okazjonalne przerzucania kluczy, aby zwiększyć poziom bezpieczeństwa. Koniecznie bezpieczeństwo kluczy, ponieważ posiadanie ich wraz z nazwą konta daje nieograniczony dostęp do wszystkich danych na koncie magazynu.

#### <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego

Jeśli potrzebujesz użytkownicy mogli mieć kontrolowany dostęp do zasobów magazynu, można utworzyć sygnaturę dostępu współdzielonego. Sygnatury dostępu współdzielonego to token, który można dołączyć do adresu URL, który pozwala na delegowany dostęp do zasobu magazynu. Każdy, kto posiada token dostępu do zasobu, który wskazuje z uprawnieniami, określa, dla okresu czasu jej prawidłowe. Aby uzyskać więcej informacji, zobacz [używanie sygnatury dostępu współdzielonego](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

Sieci wirtualne są niezbędne do obsługi komunikacji między maszynami wirtualnymi. Można zdefiniować niestandardowy adres IP, ustawienia DNS, filtrowania zabezpieczeń w podsieci i równoważenie obciążenia. Platforma Azure obsługuje różne przypadki: sieci tylko w chmurze lub hybrydowe sieci wirtualnych.

### <a name="cloud-only-virtual-networks"></a>Sieci wirtualne oparte tylko na chmurze

Siecią wirtualną platformy Azure, domyślnie jest dostępna tylko do zasobów przechowywanych na platformie Azure. Zasoby podłączone do tej samej sieci wirtualnej mogą komunikować się ze sobą. Można skojarzyć interfejsy sieciowe maszyny wirtualnej i obciążenia równoważenia przy użyciu publicznego adresu IP, aby udostępnić maszynę wirtualną za pośrednictwem Internetu. Za pomocą sieciowej grupy zabezpieczeń, można ułatwić ochronę dostępu do zasoby udostępnione publicznie.

![Sieć wirtualna platformy Azure dla aplikacji sieci Web w warstwie 2](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Wirtualne sieci hybrydowych

Przy użyciu usługi ExpressRoute lub połączenie VPN lokacja lokacja, można połączyć sieć lokalną z siecią wirtualną platformy Azure. W tej konfiguracji sieci wirtualnej platformy Azure jest zasadniczo oparte na chmurze rozszerzenie sieci lokalnej.
![Hybrydowa sieć wirtualną przy użyciu sieci VPN](https://docs.microsoft.com/azure/architecture/reference-architectures/_images/blueprints/hybrid-network-vpn.png)

Ponieważ usługa Azure virtual network jest podłączony do sieci lokalnej, między środowiskami lokalnymi sieci wirtualne muszą używać unikatowy części przestrzeni adresowej, używanymi przez organizację. W ten sam sposób, który różnych lokalizacjach w firmie są przypisane do określonej podsieci IP Azure staje się inną lokalizację przy rozszerzaniu sieci.
Istnieje kilka opcji wdrożenia sieci wirtualnej.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Interfejs wiersza polecenia](../../virtual-network/quick-create-cli.md)

- Szablony usługi Azure Resource Manager

> **Kiedy należy używać**: W dowolnym momencie pracujesz z maszynami wirtualnymi na platformie Azure, będzie działać z sieciami wirtualnymi. Dzięki temu dzielenia maszyn wirtualnych w podsieci publicznych i prywatnych podobne lokalnych centrów danych.
> 
> **Rozpoczynanie pracy**: Wdrażanie sieci wirtualnej platformy Azure przy użyciu witryny Azure portal wymaga aktywnej subskrypcji platformy Azure i dostęp do przeglądarki sieci web. Można wdrożyć nową sieć wirtualną do nowej lub istniejącej grupy zasobów. Podczas tworzenia nowej maszyny wirtualnej z poziomu portalu, możesz wybrać istniejącą sieć wirtualną lub utworzyć nową. Rozpocznij pracę i [Utwórz sieć wirtualną przy użyciu witryny Azure portal](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Dostęp i większe bezpieczeństwo dla sieci wirtualnych

Możesz pomóc bezpiecznych sieci wirtualnych platformy Azure przy użyciu sieciowej grupy zabezpieczeń. Sieciowe grupy zabezpieczeń zawierają listę reguł listę kontroli dostępu (ACL) kontroli dostępu, które blokują lub zezwalają na ruch sieciowy do wystąpień maszyn wirtualnych w sieci wirtualnej. Sieciowe grupy zabezpieczeń można skojarzyć z podsieciami lub poszczególnych wystąpień maszyn wirtualnych w danej podsieci. Po skojarzeniu sieciowej grupy zabezpieczeń z podsiecią, reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci. Ponadto można bardziej ograniczyć ruch do poszczególnych maszyn wirtualnych przez skojarzenie sieciowej grupy zabezpieczeń bezpośrednio z tej maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie maszyny wirtualnej z systemem Windows](../../virtual-machines/windows/quick-create-portal.md)
- [Tworzenie maszyny wirtualnej z systemem Linux](../../virtual-machines/linux/quick-create-portal.md)
