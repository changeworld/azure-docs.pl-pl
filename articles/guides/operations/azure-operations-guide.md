---
title: Wprowadzenie — przewodnik dla operatorów Azure IT | Dokumentacja firmy Microsoft
description: Przewodnik Wprowadzenie dla operatorów IT na platformie Azure
services: ''
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: nepeters
ms.openlocfilehash: b63beffdd3b10071c6d18128e78a949374c171b2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361659"
---
# <a name="introduction-to-cloud-computing-and-microsoft-azure"></a>Wprowadzenie do przetwarzania danych w chmurze i Microsoft Azure

W tym przewodniku przedstawiono podstawowe koncepcje dotyczące wdrażania i zarządzania infrastrukturą Microsoft Azure. Jeśli dopiero zaczynasz do chmury obliczeniowej lub Azure, ten przewodnik pomaga szybko ułatwiające rozpoczęcie pracy z założenia, wdrażania oraz szczegóły dotyczące zarządzania. Wielu sekcjach tego przewodnika omówienia operacji, takich jak wdrażanie maszyny wirtualnej, a następnie podaj łącze, aby uzyskać szczegółowe informacje techniczne.


## <a name="cloud-computing-overview"></a>Przegląd rozwiązań w chmurze

Przetwarzania nowoczesnych alternatywą do tradycyjnych, lokalnie centrum danych w chmurze. Dostawców chmury publicznej umożliwiają i zarządzanie nim wszystkie infrastruktury obliczeniowej i podstawowe oprogramowanie do zarządzania. Te dostawcy zapewniają szeroką gamę usługi w chmurze. W takim przypadku usługa w chmurze może być, maszynę wirtualną, serwer sieci web lub aparatu hostowanych w chmurze bazy danych. Jako klient dostawcy chmury dzierżawy są te usługi w chmurze na zgodnie z potrzebami. W ten sposób koszt zakupu konserwacji sprzętu konwertowanie wydatków operacyjnych. Usługi w chmurze zapewnia również następujące korzyści:

-   Szybkie wdrażanie obliczeń dużych środowisk

-   Szybkie dezalokacji systemów, które nie są już wymagane

-   Łatwe wdrażanie systemów tradycyjnie złożonych, takich jak moduły równoważenia obciążenia

-   Elastyczne możliwości obliczeniowe wydajności lub skali w razie potrzeby

-   Bardziej ekonomiczne środowisk komputerowych

-   Dostęp z dowolnego miejsca przy użyciu portalu sieci web lub programowe automatyzacji

-   Usług do potrzeb większości obliczeniowych i aplikacji w chmurze

Z infrastruktury lokalnej masz pełną kontrolę nad sprzętu i oprogramowania, które zostało wdrożone. W przeszłości spowodowało decyzje dotyczące sprzętu nabywania skupiających się na skalowanie w. Przykładem jest zakupu serwera z większej liczby rdzeni do potrzeb wydajności godzinami szczytu. Niestety tej infrastruktury może być wykorzystany poza oknem żądanie. Przy użyciu platformy Azure wdrażania infrastruktury, które są potrzebne i dostosować w górę lub w dół w dowolnym momencie. Prowadzi to do fokus na skalowanie w poziomie do wdrożenia węzłów obliczeniowych dodatkowe do zaspokojenia danej potrzeby wydajności. Skalowania usługi w chmurze jest bardziej ekonomiczne rozwiązanie niż skalowaniu za pośrednictwem kosztowne sprzętu.

Microsoft zostało wdrożone w wielu centrach danych platformy Azure na całym świecie, z planowane. Ponadto firmy Microsoft jest zwiększenie suwerennych chmur w regionach, takich jak Chin i Niemczech. Tylko globalne największych przedsiębiorstw można wdrażać centrów danych w taki sposób, tak aby przy użyciu usługi Azure ułatwia dla przedsiębiorstw o dowolnym rozmiarze do wdrożenia usługi w pobliżu klientom.

Dla małych firm Azure umożliwia dla punktu wejścia ekonomicznych możliwość skalowania szybko, jak żądanie dla zwiększa obliczeń. Zapobiega to duży początkowych inwestycji kapitału w infrastrukturze i zapewnia elastyczność projektowania i ponownego projektowania tych systemów zgodnie z potrzebami. Użycie chmury obliczeniowej mieści się również z modelem fast skali i fast niepowodzenie uruchomienia wzrostu.

Aby uzyskać więcej informacji o dostępnych regionów platformy Azure, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-is-classified-into-three-categories-saas-paas-and-iaas"></a>Chmury obliczeniowej dzieli się na trzy kategorie: SaaS, PaaS i IaaS.

#### <a name="saas-software-as-a-service"></a>SaaS: Oprogramowanie jako usługa

SaaS to oprogramowanie, które centralnie jest hostowany i zarządzany. Zwykle jest on oparty na architektura wielodostępnej — jednej wersji aplikacji jest używany dla wszystkich klientów. Może być skalowana do wielu wystąpień w celu zapewnienia najlepszej wydajności we wszystkich lokalizacjach. Oprogramowanie SaaS jest zwykle licencjonowane za pośrednictwem subskrypcji miesięcznego lub rocznego.

Microsoft Office 365 jest dobrym przykładem SaaS, oferty. Subskrybenci opłata miesięcznego lub rocznego subskrypcji i otrzymują Microsoft Exchange, Microsoft OneDrive oraz pozostała część pakietu Microsoft Office jako usługa. Subskrybenci zawsze uzyskać najnowszą wersję i Exchange server odbywa się. W porównaniu do instalowania i uaktualniania pakietu Office co roku, jest mniej kosztowne i wymaga mniej wysiłku.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platforma jako usługa 

Z PaaS w przypadku wdrażania aplikacji w środowisku, które umożliwia dostawcy usług chmury. Dostawca zawiera wszystkie infrastruktury zarządzania, aby skupić się na projektowanie aplikacji.

Azure zapewnia kilka PaaS obliczeniowe oferty, w tym funkcja Web Apps w usłudze Azure App Service i usługi w chmurze Azure (role sieci web i proces roboczy). W obu przypadkach deweloperzy mają różne sposoby wdrażania aplikacji bez znajomości śruby obsługujących go. Programiści nie muszą Tworzenie maszyn wirtualnych (VM), użyj protokołu RDP (Remote Desktop), aby zalogować się do każdego z nich lub instalowania aplikacji. Ich po prostu kliknij przycisk (lub Zamknij, aby go) i narzędzi dostarczonych przez firmę Microsoft, obsługi administracyjnej maszyn wirtualnych i wdrożyć i zainstalować aplikację na nich.

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktura jako usługa

Dostawcy chmury IaaS uruchamia i zarządza wszystkie zasoby obliczeniowe fizycznego oraz wymaganego oprogramowania, aby włączyć wirtualizacji komputerów. Klient usługi wdraża maszyny wirtualne w tych hostowanej centrach danych. Chociaż maszyny wirtualne znajdują się w centrum danych poza siedzibą firmy, klient IaaS ma kontrolę nad konfiguracją i zarządzanie nimi.

Azure obejmuje kilka rozwiązań IaaS tym maszyny wirtualne, zestawy skalowania maszyny wirtualnej i powiązanych infrastruktury sieciowej. Maszyny wirtualne są popularne wybór początkowo migracji usług Azure, ponieważ dzięki modelu migracji "przyrostu i przesunięcia". Można skonfigurować maszyny Wirtualnej, takie jak infrastruktury aktualnie uruchomionych usług w centrum danych, a następnie przeprowadzić migrację oprogramowania do nowej maszyny Wirtualnej. Konieczne może być aktualizowanie konfiguracji, takie jak adresy URL do innych usług lub magazynu, ale można migrować wiele aplikacji w ten sposób.

Zestawy skalowania maszyn wirtualnych są tworzone na maszynach wirtualnych platformy Azure i łatwa do wdrożenia klastrów identycznych maszyn wirtualnych. Zestawy skalowania maszyny wirtualnej również obsługuje funkcję skalowania automatycznego, dzięki czemu nowe maszyny wirtualne można wdrożyć automatycznie na żądanie. Zestawy skalowania maszyny wirtualnej Dzięki temu idealną platformą do hosta wyższego poziomu mikrousługi klastrów obliczeniowych, takich jak sieć szkieletowa usług Azure i usługi kontenera platformy Azure.

## <a name="azure-services"></a>Usługi platformy Azure

System Azure oferuje wiele usług w jej platformy obliczeniowych w chmurze. Te usługi są następujące.

### <a name="compute-services"></a>Usługi obliczeniowe

Usługi hostingu oraz obciążeniu aplikacji:

-   Maszyny wirtualne platformy Azure — zarówno systemu Windows, jak i Linux

-   Usługi aplikacji (aplikacje sieci Web, aplikacji mobilnych, aplikacji logiki, aplikacji interfejsu API i funkcji aplikacji)

-   Partia zadań Azure (w przypadku równoległego na dużą skalę i zadań wsadowych obliczeniowe)

-   Azure Service Fabric

-   Azure Container Service

### <a name="data-services"></a>Data services

Usługi do przechowywania danych i zarządzanie nimi:

-   Usługa Azure Storage (obejmuje usługi obiektów Blob platformy Azure, kolejki, tabel i plików)

-   Azure SQL Database

-   Azure Cosmos DB

-   Microsoft Azure StorSimple

-   Azure Redis Cache

### <a name="application-services"></a>Usługi aplikacji

Usługi dotyczące tworzenia i obsługi aplikacji:

-   Azure Active Directory (Azure AD)

-   Usługa Azure Service Bus do łączenia systemów rozproszonych

-   Usługa Azure HDInsight do przetwarzania danych big data

-   Harmonogram systemu Azure

-   Azure Media Services

### <a name="network-services"></a>Usługi sieciowe

Usługi dotyczące sieci w obrębie platformy Azure i między Azure i lokalnymi centrami danych:

-   Azure Virtual Network

-   Usługa ExpressRoute systemu Azure

-   DNS platformy Azure

-   Azure Traffic Manager

-   Azure Content Delivery Network

Szczegółową dokumentację dotyczącą usług Azure, zobacz [dokumentacji usługi Azure](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Kluczowe założenia Azure

### <a name="datacenters-and-regions"></a>Centra danych i regionów


Azure to platforma chmury globalnej, która jest ogólnie dostępna w wielu regionach na całym świecie. Podczas obsługi administracyjnej usługi, aplikacji lub maszyny Wirtualnej na platformie Azure, należy wybrać region. Wybrany region reprezentuje datacenter speciﬁc, w którym jest uruchomiona aplikacja. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

Jednym z beneﬁts użycia Azure jest wdrożenie aplikacji w różnych centrach danych na całym świecie. Wybrany region można aﬀect wydajność aplikacji. Najlepiej wybrać region zbliżonej do najbardziej klientów, aby zmniejszyć opóźnienia w żądaniach sieci. Może również wybrać region w celu spełnienia wymagań prawnych dystrybucji aplikacji w niektórych krajach.

### <a name="azure-portal"></a>Azure Portal


Azure portal to aplikacji sieci web, który może służyć do tworzenia, zarządzania i usuwać zasoby platformy Azure i usługi. Portalu Azure znajduje się pod adresem https://portal.azure.com. Obejmuje on można dostosować pulpit nawigacyjny oraz narzędzi do zarządzania zasobami platformy Azure. Zawiera także informacje rozliczeń i subskrypcji. Aby uzyskać więcej informacji, zobacz [Przegląd portalu Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) i [zasobów Azure zarządzania za pośrednictwem portalu](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Zasoby

Zasoby platformy Azure są poszczególne obliczeniowych, sieci, danych lub aplikacji hosting usług, które zostały wdrożone w subskrypcji platformy Azure. Niektóre typowe zasoby są maszyny wirtualne, konta magazynu lub bazy danych SQL. Usług Azure często składają się z kilku powiązanych zasobów systemu Azure. Dla wystąpienia maszyny wirtualnej platformy Azure może obejmować maszyny Wirtualnej, konta magazynu, karta sieciowa i publiczny adres IP. Te resourcres można tworzyć, zarządzane i usuwane pojedynczo lub grupowo. Zasoby platformy Azure są opisane bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="resource-groups"></a>Grupy zasobów

Grupy zasobów platformy Azure jest kontenerem, który zawiera powiązane zasoby Azure rozwiązania. Grupa zasobów może zawierać wszystkich zasobów dla rozwiązania lub tylko tych zasobów, które mają być zarządzane jako grupa. Grup zasobów platformy Azure są opisane bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Szablonu usługi Azure Resource Manager to plik JavaScript Object Notation (JSON), który definiuje co najmniej jeden zasób do wdrożenia w grupie zasobów. Definiuje również zależności między zasobami wdrożone. Szablony Menedżera zasobów są opisane bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="automation"></a>Automatyzacja


Oprócz tworzenia, zarządzania i usuwania zasobów za pomocą portalu Azure te działania można zautomatyzować za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI).

**Azure PowerShell**

Program Azure PowerShell to zbiór modułów, które dostarczają poleceń cmdlet do zarządzania Azure. Poleceń cmdlet służy do tworzenia, zarządzania i usuwania usług Azure. Polecenia cmdlet mogą pomóc osiągnąć spójne, powtarzalnych i zdalne wdrożenia. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps).

**Interfejs wiersza polecenia platformy Azure**

Interfejs wiersza polecenia platformy Azure jest narzędziem, które służy do tworzenia, zarządzania i usunąć zasoby platformy Azure z poziomu wiersza polecenia. Interfejs wiersza polecenia platformy Azure jest dostępna dla systemu Linux, Mac OS X i Windows. Aby uzyskać więcej informacji oraz szczegółowe informacje techniczne, zobacz [instalowanie interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli.md).

**Interfejsów API REST** Azure jest zbudowany na zestaw interfejsów API REST, który obsługuje interfejsu użytkownika portalu Azure. Większość tych interfejsów API REST są również obsługiwane pozwala programowo udostępniania i zarządzania zasobami Azure i aplikacji z dowolnego urządzenia internetowe. Aby uzyskać więcej informacji, zobacz [odwołania do zestawu SDK REST Azure](https://docs.microsoft.com/rest/api/index).


## <a name="azure-subscriptions"></a>Subskrypcje platformy Azure


Logiczne grupowanie usług platformy Azure, która jest połączona z kontem platformy Azure jest subskrypcja. Wystąpieniu konto platformy Azure może zawierać wiele subskrypcji. Rozliczenia dotyczące usługi Azure odbywa się na zasadzie dla subskrypcji. Subskrypcje platformy Azure ma administratora konta, który ma pełną kontrolę nad subskrypcji i administratora usługi, kto ma kontrolę nad wszystkie usługi w subskrypcji. Oprócz administratorów, może zostać przydzielony indywidualnych kont dokładną kontrolę zasobów platformy Azure za pośrednictwem RBAC.

### <a name="select-and-enable-an-azure-subscription"></a>Wybierz i Włącz subskrypcji platformy Azure

Przed rozpoczęciem pracy z usługami Azure, musisz mieć subskrypcję. Dostępnych jest kilka typów subskrypcji.

**Wolne konta**: link, aby utworzyć bezpłatne konto znajduje się na [witryny sieci Web Azure](https://azure.microsoft.com/). Zapewnia to kredyt w ciągu 30 dni, aby spróbować dowolną kombinację zasobami na platformie Azure. Po przekroczeniu kwota środki, Twoje konto zostało zawieszone. Z końcem okresu próbnego usługi są zlikwidowana i nie będzie dłużej działać. Można uaktualnić do płatności obejmujące subskrypcję w dowolnym momencie.

**Subskrypcji MSDN**: Jeśli masz subskrypcję MSDN, możesz uzyskać określoną kwotę kredyt Azure w miesiącu. Na przykład, jeśli masz program Microsoft Visual Studio Enterprise z subskrypcją MSDN, możesz uzyskać \$150 USD miesięcznie w środki Azure.

Jeśli przekroczysz kredytu usługi są wyłączone do momentu rozpoczęcia następnego miesiąca. Możesz wyłączyć limit wydatków i dodać kartę kredytową do zastosowania w przypadku dodatkowych kosztów. Niektóre z tych kosztów są obniżonej cenie przez konta w witrynie MSDN. Na przykład płacisz Linux ceny dla maszyn wirtualnych systemem Windows Server i bez dodatkowych opłat dla serwerów firmy Microsoft, takich jak Microsoft SQL Server nie istnieje. Dzięki temu kont MSDN idealne rozwiązanie w przypadku scenariuszy tworzenia i testowania.

**Konta BizSpark**: program Microsoft BizSpark oferuje wiele korzyści do uruchomienia. Jedną z tych zalet jest dostęp do oprogramowania firmy Microsoft dla środowiska projektowania i testowania do pięciu kont MSDN. Otrzymasz kredyt Azure w 150 USD dla każdego z tych kont pięć MSDN i płacisz obniżonych stawek za kilka usług Azure, takich jak maszyny wirtualne.

**Płatność za rzeczywiste użycie**: Z tą subskrypcją płacisz do użycia przez dołączenie kartą kredytową lub debetową do konta. W przypadku organizacji mogą również zostać zatwierdzone do fakturowania.

**Umowa Enterprise**: Z umową enterprise zatwierdzeniu przy użyciu określonej liczby usług platformy Azure w przyszłym roku, dzięki czemu płacisz wielkość wcześniejsze. Zobowiązanie wprowadzone jest używany przez cały rok. Po przekroczeniu kwota zobowiązania, możesz zapłacić nadwyżkowe w zaległości. W zależności od kwota zobowiązania możesz uzyskać rabat na usługi na platformie Azure.



### <a name="grant-administrative-access-to-an-azure-subscription"></a>Przyznawaj dostęp administracyjny do subskrypcji platformy Azure


Wiele ról administratora konta są dostępne i można zmienić w dowolnym momencie. Dostępne są następujące dwie role klucza:

-   **Administrator usługi**: Ta rola jest autoryzowany do zarządzania usługami Azure. Domyślnie udzielony pełny dostęp do tego samego konta administratora konta.

-   **Współadministratorem**: Ta rola ma takie same prawa dostępu jako administratora usługi. Jednak ta rola nie można zmienić skojarzenia subskrypcji platformy Azure katalogi.

Aby uzyskać więcej informacji, zobacz [jak dodać lub zmienić role administratora platformy Azure](../../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Wyświetlanie informacji dotyczących rozliczeń w portalu Azure


Ważne składnik za pomocą platformy Azure jest możliwość wyświetlania informacji dotyczących rozliczeń. Azure portal udostępnia szczegółowy wgląd w Azure informacji dotyczących rozliczeń.

Aby uzyskać więcej informacji, zobacz [sposobu pobierania Azure rozliczeń faktury i dziennego użycia danych](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Uzyskanie informacji dotyczących rozliczeń rozliczeń interfejsów API


Oprócz wyświetlania rozliczeń w portalu, aby dostęp do informacji dotyczących rozliczeń, za pomocą skryptu lub programu za pośrednictwem interfejsów API Azure rozliczeń REST:

-   Użycie interfejsu API Azure służy do pobierania danych użycia. Aby precyzyjnie zdefiniować informacji dotyczących rozliczeń użycia przez znakowanie powiązanych zasobów systemu Azure. Możesz na przykład tag każdy z zasobów w grupie zasobów o nazwie działu lub nazwę projektu i śledzić koszty specjalnie z myślą o tym jeden tag.

-   Aby wyświetlić listę wszystkich dostępnych zasobów oraz metadanych i cenach informacji na temat każdego z tych zasobów, można użyć interfejsu API Azure szybkość karty.

Aby uzyskać więcej informacji, zobacz [uzyskać wgląd w Microsoft Azure użycia zasobów](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognozy kosztów z Kalkulator cen

Różni się ceny dla każdej usługi na platformie Azure. Wiele usług platformy Azure zapewniają warstw podstawowa, standardowa i Premium. Zwykle każda warstwa ma kilka poziomów cen i wydajności. Za pomocą [online kalkulator cen](http://azure.microsoft.com/pricing/calculator), można utworzyć cenową szacowania. Kalkulator obejmuje możliwość oszacowanie kosztów na pojedynczy zasób lub grupa zasobów.

### <a name="set-up-billing-alerts"></a>Ustawianie alertów dotyczących rozliczeń

Po wdrożeniu aplikacji lub rozwiązania na platformie Azure, można tworzyć alerty, które wysłanie wiadomości e-mail podczas definiowania podejście limitów wydatków w alercie. Aby uzyskać więcej informacji, zobacz [skonfigurować alerty dotyczące subskrypcji platformy Microsoft Azure billing](../../billing/billing-set-up-alerts.md).

## <a name="azure-resource-manager"></a>Azure Resource Manager

Usługa Azure Resource Manager jest mechanizm wdrażania, zarządzania i organizacji zasobów platformy Azure. Za pomocą Menedżera zasobów, można utworzyć wiele pojedynczych zasobów w grupie zasobów.

Menedżer zasobów zawiera również możliwości wdrożenia, które pozwalają dostosowania wdrożenia i konfiguracji powiązane zasoby. Na przykład za pomocą Menedżera zasobów, można wdrożyć aplikację, która składa się z wielu maszyn wirtualnych, usługi równoważenia obciążenia i bazy danych SQL jako pojedyncza jednostka. Te wdrożenia można utworzyć przy użyciu szablonu usługi Resource Manager.

Usługa Resource Manager zapewnia kilka korzyści:

-   Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).

-   Można wielokrotnie wdrażania rozwiązania w całym cyklu programistycznym i mieć pewność, że zasoby są wdrażane w spójnym stanie.

-   Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

-   Możliwość definiowania zależności między zasobami, aby wdrażać je w odpowiedniej kolejności.

-   Możliwość stosowania kontroli dostępu do wszystkich usług w grupie zasobów, ponieważ RBAC natywnie jest zintegrowana platforma zarządzania.

-   Możliwość dodawania tagów zasobów w celu logicznego uporządkowania wszystkich zasobów w Twojej subskrypcji.

-   Można również uprościć rozliczenia w organizacji, wyświetlając kosztów dla grupy zasobów, które mają ten sam tag.

### <a name="tips-for-creating-resource-groups"></a>Wskazówki dotyczące tworzenia grup zasobów


Podczas jest podejmowanie decyzji dotyczących grup zasobów, należy wziąć pod uwagę następujące wskazówki:

-   Wszystkie zasoby w grupie zasobów powinny mieć taki sam cykl życia.

-   Zasób można przypisać do tylko jednej grupy naraz.

-   Można dodać lub usunięcie zasobu z grupy zasobów w dowolnej chwili. Każdy zasób musi należeć do grupy zasobów. Dlatego jeśli usunięcie zasobu z jednej grupy, należy dodać go do innej.

-   Większość typów zasobów można przenieść do innej grupie zasobów, w dowolnym momencie.

-   Zasoby w grupie zasobów mogą znajdować się w różnych regionach.

-   Grupy zasobów można użyć do kontrolowania dostępu do zawartych w niej zasobów.

### <a name="building-resource-manager-templates"></a>Tworzenie szablonów usługi Resource Manager

Szablony Menedżera zasobów deklaratywnego definiowania zasobów i konfiguracje zasobów, które zostaną wdrożone do pojedynczej grupy zasobów. Szablony Menedżera zasobów służy do organizowania złożone wdrożenia, bez konieczności ręcznej konfiguracji lub nadmiarowe skryptów. Po opracowywanie szablonu, możesz go wdrożyć wiele razy — zawsze z wynikami, które są identyczne.

Szablon usługi Resource Manager obejmuje cztery sekcje:

-   **Parametry**: są to dane wejściowe do wdrożenia. Można podać wartości parametrów przez człowieka lub zautomatyzowanego procesu. Parametr przykład może być nazwa użytkownika administratora i hasła dla maszyny Wirtualnej systemu Windows. Wartości parametrów są używane w całej wdrożenia, gdy są one określone.

-   **Zmienne**: są one używane do przechowywania wartości, które są używane w całym wdrożeniu. W odróżnieniu od parametrów wartość zmiennej nie podano w czasie wdrażania. Zamiast tego jest twardych kodowanych lub generowane dynamicznie.

-   **Zasoby**: Ta sekcja szablonu definiuje zasobów do wdrożenia, takich jak maszyny wirtualne, konta magazynu i sieci wirtualnych.

-   **Dane wyjściowe**: po zakończeniu wdrażania Menedżera zasobów mogą zwracać dane, takie jak parametry połączenia dynamicznie generowanym.

Do automatyzacji wdrażania dostępne są następujące mechanizmy:

-   **Funkcje**: możesz użyć kilku funkcji w szablonach usługi Resource Manager. Obejmują one operacji, takich jak konwertowania ciągu na małe litery, wdrażanie wielu wystąpień określonych zasobów i dynamicznie zwracanie docelowa grupa zasobów. Funkcje Menedżera zasobów ułatwić wdrożeń dynamicznych.

-   **Zależności między zasobami**: gdy wdrażasz wiele zasobów, niektóre zasoby będą zależy od innych użytkowników. W celu ułatwienia wdrażania, można użyć deklaracji zależności, aby przed inne są wdrażane zasoby zależne.

-   **Łączenie szablonu**: od w obrębie jednego szablonu usługi Resource Manager, można połączyć z innego szablonu. Dzięki temu dekompozycji wdrożenia do zestawu docelowego, specyficzne dla celu szablonów.

Można tworzyć szablony Menedżera zasobów w edytorze tekstu. Jednak zestaw Azure SDK dla programu Visual Studio zawiera narzędzia ułatwiające. Za pomocą programu Visual Studio, możesz dodać zasoby do szablonu, za pomocą kreatora, wdrażanie i debugowanie szablonu bezpośrednio z poziomu programu Visual Studio. Aby uzyskać więcej informacji, zobacz [szablonów Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).

Na koniec można przekonwertować istniejących grup zasobów do wielokrotnego użytku szablonu z portalu Azure. Może to być przydatne, jeśli chcesz utworzyć szablonu można wdrożyć istniejącą grupę zasobów lub po prostu chcesz zbadać podstawowej JSON. Aby wyeksportować grupy zasobów, wybierz **skryptu automatyzacji** przycisk od ustawienia grupy zasobów.

## <a name="security-of-azure-resources-rbac"></a>Zabezpieczeń zasobów platformy Azure (RBAC)

Można przyznać operacyjne dostęp do kont użytkowników w określonym zakresie: subskrypcji, grupy zasobów lub pojedynczych zasobów. Oznacza to wdrożenie zestawu zasobów w grupie zasobów, takich jak maszynę wirtualną i wszystkie powiązane zasoby i udzielanie uprawnień do określonego użytkownika lub grupy. Takie podejście ogranicza dostęp do zasobów, które należą do docelowej grupy zasobów. Można również przyznać dostęp do pojedynczego zasobu, na przykład maszynę wirtualną lub sieci wirtualnej.

Aby udzielić dostępu, możesz przypisać rolę do użytkownika lub grupy użytkowników. Istnieje wiele wstępnie zdefiniowanych ról. Istnieje również możliwość definiowania własnych niestandardowych ról.

Poniżej przedstawiono kilka ról przykład wbudowanych w platformy Azure:

-   **Właściciel**: użytkownik z tą rolą mogą zarządzać wszystkim łącznie z dostępem.

-   **Czytnik**: użytkownik z tą rolą mogą odczytywać zasoby wszystkich typów (z wyjątkiem klucze tajne), ale nie można wprowadzić zmian.

-   **Współautora maszyny wirtualnej**: użytkownika z tą rolą mogą zarządzać maszynami wirtualnymi, ale nie można zarządzać sieci wirtualnej, do którego jest podłączony lub konta magazynu, w którym znajduje się plik dysku VHD.

-   **Współautor bazy danych SQL**: użytkownik z tą rolą mogą zarządzać baz danych, ale nie ich zasad związanych z zabezpieczeniami.

-   **Menedżer zabezpieczeń SQL**: użytkownik z tą rolą można zarządzać zasadami związanych z zabezpieczeniami serwerów SQL i baz danych.

-   **Współautor konta magazynu**: użytkownik z tą rolą mogą zarządzać kontami magazynu, ale nie może zarządzać dostępem do konta magazynu.

Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Maszyny wirtualne platformy Azure jest jednym z centralnej usługi IaaS na platformie Azure. Maszyny wirtualne platformy Azure obsługuje wdrażania systemu Windows lub Linux maszyn wirtualnych w centrum danych Microsoft Azure. Z maszyn wirtualnych Azure mają pełną kontrolę nad konfiguracji maszyny Wirtualnej i są zobowiązani do instalacji oprogramowania, konfiguracji i konserwacji.

Podczas wdrażania maszyny Wirtualnej platformy Azure, możesz wybrać obraz z portalu Azure Marketplace lub pozwalają własnych uogólniony obraz. Ten obraz jest używany do stosowania systemu operacyjnego i konfiguracji początkowej. Podczas wdrażania usługi Resource Manager będzie obsługiwać niektóre ustawienia konfiguracji, takich jak przypisanie nazwy komputera, poświadczeń administracyjnych i konfiguracji sieci. Rozszerzenia maszyny wirtualnej platformy Azure umożliwia dalsze zautomatyzować konfiguracje, takie jak Instalacja oprogramowania, konfiguracji oprogramowania antywirusowego i monitorowanie rozwiązań.

Maszyny wirtualne można tworzyć w wielu różnych rozmiarach. Rozmiar maszyny wirtualnej nakazują alokacji zasobów, takich jak przetwarzanie, pamięci i pojemność pamięci masowej. W niektórych przypadkach określonych funkcji, takich jak karty sieciowe z obsługą funkcji RDMA i dyski SSD są dostępne tylko w przypadku niektórych rozmiarów maszyn wirtualnych. Aby uzyskać listę wszystkich rozmiarów maszyn wirtualnych i możliwości, zobacz "Rozmiary maszyn wirtualnych na platformie Azure" dla [Windows](../../virtual-machines/windows/sizes.md) i [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Przypadki zastosowań

Ponieważ maszyn wirtualnych platformy Azure oferuje pełną kontrolę nad konfiguracji, są idealne rozwiązanie w przypadku wielu obciążeń serwera, które nie mieszczą się w modelu typu PaaS. Obciążenie serwera takich jak serwery bazy danych (SQL Server, Oracle lub bazy danych MongoDB), usługi Active Directory systemu Windows Server, Microsoft SharePoint i wiele więcej stanie się możliwe do uruchamiania na platformie Microsoft Azure. W razie potrzeby można przenosić takich obciążeń z lokalnego centrum danych do co najmniej jeden regiony platformy Azure, bez dużą ilość ponownej konfiguracji.

### <a name="deployment-of-virtual-machines"></a>Wdrażanie maszyn wirtualnych

Maszyny wirtualne platformy Azure można wdrożyć przy użyciu portalu Azure, przy użyciu automatyzacji z modułu Azure PowerShell lub przy użyciu automatyzacji interfejsu wiersza polecenia i platform.

**Portal**

Wdrażanie maszyny wirtualnej za pomocą portalu Azure wymaga aktywną subskrypcją platformy Azure i dostęp do przeglądarki sieci web. Możesz wybrać wiele obrazów systemów operacyjnych z różnymi konfiguracjami. Wszystkie magazynu i wymagania dotyczące sieci są konfigurowane podczas wdrażania. Aby uzyskać więcej informacji, zobacz temat "Create maszyny wirtualnej w portalu Azure" dla [Windows](../../virtual-machines/windows/quick-create-portal.md) i [Linux](../../virtual-machines/linux/quick-create-portal.md).

Oprócz wdrażania maszyny wirtualnej w portalu Azure, można wdrożyć szablonu usługi Azure Resource Manager z portalu. To wdroży i skonfiguruje wszystkie zasoby, zgodnie z definicją w szablonie. Aby uzyskać więcej informacji, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i portalu Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).


**Program PowerShell**

Wdrażanie maszyny wirtualnej platformy Azure przy użyciu programu PowerShell pozwala na automatyzację ukończenia wdrożenia wszystkich zasobów powiązanych maszyny wirtualnej, w tym magazynu i sieci. Aby uzyskać więcej informacji, zobacz [Utwórz maszynę Wirtualną z systemem Windows przy użyciu usługi Resource Manager i programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Oprócz wdrażania zasobów obliczeniowych Azure oddzielnie, moduł Azure PowerShell służy do wdrażania szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Interfejs wiersza polecenia (CLI)**

Podobnie jak w przypadku modułu programu PowerShell, interfejsu wiersza polecenia platformy Azure umożliwia automatyzację wdrożenia i może być używany z systemami Windows, OS X lub Linux. Jeśli używasz interfejsu wiersza polecenia Azure **szybkie — tworzenie maszyny wirtualnej** polecenia, wszystkie powiązane zasoby maszyny wirtualnej (w tym magazynu i sieci), a następnie wdrażane samej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Utwórz Maszynę wirtualną systemu Linux na platformie Azure przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/quick-create-cli.md).

Analogicznie można użyć wiersza polecenia platformy Azure do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i interfejsu wiersza polecenia Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Dostęp i większe bezpieczeństwo dla maszyn wirtualnych

Uzyskiwanie dostępu do maszyny wirtualnej z Internetu wymaga skojarzonej sieciowej interfejsu lub załadować równoważenia, jeśli to możliwe, można skonfigurować za pomocą publicznego adresu IP. Publiczny adres IP zawiera nazwę DNS, który będzie prowadzić do maszyny wirtualnej lub usługi równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [adresów IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Dostęp do maszyny wirtualnej można zarządzać za pośrednictwem publicznego adresu IP za pomocą zasobu grupa zabezpieczeń sieci. Grupy NSG działa jak zapory i zezwala lub nie zezwala na ruch przez interfejs sieciowy lub podsieci na zestaw określonych portów. Na przykład aby utworzyć sesję pulpitu zdalnego z maszyny Wirtualnej platformy Azure, należy skonfigurować NSG zezwalająca na ruch przychodzący na porcie 3389. Aby uzyskać więcej informacji, zobacz [Otwieranie portów dla maszyny Wirtualnej na platformie Azure przy użyciu portalu Azure](../../virtual-machines/windows/nsg-quickstart-portal.md).

Na koniec podobnie jak w przypadku zarządzania dowolnego komputera, możesz powinien zapewnić zabezpieczeń do maszyny wirtualnej platformy Azure na system operacyjny przy użyciu poświadczeń zabezpieczeń i zapór programowych.

## <a name="azure-storage"></a>Azure Storage

Magazyn Azure to usługa zarządzany przez firmę Microsoft, która umożliwia trwałe, skalowalna i nadmiarowego magazynu. Konto magazynu platformy Azure można dodać jako zasób do żadnej grupy zasobów przy użyciu dowolnej metody wdrażania zasobów. Azure obejmuje cztery typy magazynu: obiektu Blob magazynu, Magazyn plików, Magazyn tabel i magazyn kolejek. Podczas wdrażania konta magazynu, dwa typy kont są dostępne, ogólnego przeznaczenia i magazynu obiektów blob. Konto magazynu ogólnego przeznaczenia daje dostęp do wszystkich cztery typy magazynu. Konta magazynu obiektów blob są podobne do kont ogólnego przeznaczenia, ale zawiera specjalne obiektów blob, które obejmują warstw dostępu i zimnych dostępu. Aby uzyskać więcej informacji dotyczących magazynu obiektów blob, zobacz [magazynu obiektów Blob Azure](../../storage/blobs/storage-blob-storage-tiers.md).

Z różnymi poziomami nadmiarowości można skonfigurować konta magazynu Azure:

-   **Magazyn lokalnie nadmiarowy** zapewnia wysoką dostępność, zapewniając, że trzy kopie danych będą synchronicznie przed zapisu uznaje się pomyślnie. Te kopie są przechowywane w jednym obiekcie w pojedynczym regionie. Repliki znajdują się w domenach awarii oddzielne i uaktualnienia domen. Oznacza to, że dane są dostępne, nawet jeśli węzeł magazynu który posiada z danych kończy się niepowodzeniem lub do trybu offline, należy zaktualizować.

-   **Magazyn geograficznie nadmiarowy** udostępnia trzy synchronicznych kopii danych w regionie podstawowym wysokiej dostępności, a następnie asynchronicznie tworzy trzy repliki w regionie sparowanego odzyskiwania po awarii.

-   **Dostęp do odczytu magazynu geograficznie nadmiarowego** jest magazynu geograficznie nadmiarowego oraz możliwość odczytania danych w regionie pomocniczym. Ta możliwość sprawia, że odpowiedni w przypadku odzyskiwania po awarii częściowej. W przypadku problemu z regionu podstawowego, można zmienić aplikacji na dostęp tylko do odczytu do regionu sparowany.

### <a name="use-cases"></a>Przypadki zastosowań 

Każdy typ magazynu ma przypadek użycia innego.

**Blob Storage** 

Wyraz *obiektu blob* jest akronimem *dużego obiektu binarnego*. Obiekty BLOB są bez struktury plików, takich jak te, które są przechowywane na tym komputerze. Magazyn obiektów blob umożliwia przechowywanie dowolnego typu danych tekstowych lub binarnych, takich jak dokumenty, pliki multimedialne lub instalatory aplikacji. Magazyn obiektów blob jest również nazywany magazynem obiektów. Magazyn obiektów Blob Azure przechowuje także dysków danych maszyny wirtualnej platformy Azure.

Usługa Azure Storage obsługuje trzy rodzaje obiektów blob:

-   **Blokowe obiekty BLOB** są używane do przechowywania zwykłych pliki 195 GB (4 MB x 50 000 bloków) rozmiar. Pierwotnym zastosowaniem dla blokowych obiektów blob jest magazyn plików, które są odczytywane od początku do końca, takich jak pliki multimedialne lub pliki obrazów dla witryn sieci Web. Ponieważ pliki o rozmiarze większym niż 64 MB, należy przekazać jako małych bloki są nazywane blokowych obiektów blob. Te bloki są następnie skonsolidowane lub zatwierdzone do końcowego obiektu blob.

-   **Stronicowe obiekty BLOB** są używane do przechowywania dostępie swobodnym pliki rozmiarze do 1 TB. Stronicowe obiekty BLOB są używane przede wszystkim jako magazynu zapasowego dyskach VHD, które zapewniają trwały dyski maszyn wirtualnych platformy Azure, IaaS obliczeniowe usługi na platformie Azure. Stronicowe obiekty BLOB są nazywane ponieważ udostępniają one losowego odczytu/zapisu dostęp do stron 512-bajtowego.

-   **Uzupełnialne** składają się z bloków, takich jak blokowych obiektów blob, ale są one zoptymalizowane pod kątem operacji dołączania. Często są one używane do rejestrowania informacji z jednego lub więcej źródeł do tego samego obiektu blob. Na przykład może wszystkie Twoje rejestrowanie śledzenia zapisu do tego samego uzupełnialnego obiektu blob dla aplikacji, która działa na wiele maszyn wirtualnych. Dołącz pojedynczego obiektu blob może być 195 GB.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z magazynem obiektów Blob platformy Azure przy użyciu platformy .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

**Magazyn plików**

Magazyn plików Azure to usługa, która oferuje udziały plików w chmurze przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Usługa obsługuje protokół SMB 2.1 i 3.0 protokołu SMB. Magazyn plików Azure można migrować aplikacje korzystające z udziałów plików na platformę Azure szybko i bez kosztownych modyfikacji oprogramowania. Aplikacje działające na maszynach wirtualnych platformy Azure, usług w chmurze lub lokalnie klientów mogą zainstalować udział plików w chmurze. Jest to podobne do jak aplikacja na komputerze instalująca typowy udział SMB. Dowolna liczba składników aplikacji może następnie równocześnie zainstalować udział Magazynu plików i uzyskiwać do niego dostęp.

Ponieważ udział magazynu plików to standardowy udział plików SMB, aplikacje działające na platformie Azure można uzyskać dostępu do danych w udziale za pośrednictwem interfejsów API We/Wy systemu plików. Deweloperzy mogą więc używać istniejący kod i własne umiejętności aby zmigrować istniejące aplikacje. Informatycy mogą wykorzystywać poleceń cmdlet programu PowerShell do tworzenia, zainstalować i zarządzać nimi udziałów magazynu plików w ramach administracji aplikacjami platformy Azure.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z magazynem plików Azure w systemie Windows](../../storage/files/storage-how-to-use-files-windows.md) lub [jak używać magazynu plików Azure z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md).

**Table Storage**

Magazyn tabel Azure to usługa, która przechowuje dane strukturalne NoSQL w chmurze. Magazyn tabel jest magazynem kluczy/atrybutów z projektem bez schematu. Ponieważ Magazyn tabel jest bez schematu, jest łatwo zaadaptować dane rozwijających się potrzeb aplikacji. Dostęp do danych jest szybki i ekonomiczny dla wszystkich rodzajów aplikacji. Magazyn tabel jest zwykle znacznie tańszy niż tradycyjne bazy SQL dla podobnych ilości danych.

Magazyn tabel umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkownika dla aplikacji sieci Web, książki adresowe, informacje o urządzeniach i wszelkie inne metadane, których wymaga Twoja usługa. W tabeli można przechowywać dowolną liczbę jednostek. Konto magazynu może zawierać dowolną liczbę tabel w granicach pojemności konta magazynu.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z magazynem tabel Azure](../../cosmos-db/table-storage-how-to-use-dotnet.md).

**Queue Storage**

Usługa Azure Queue Storage umożliwia przesyłanie komunikatów za pomocą chmury między składnikami aplikacji. Podczas projektowania aplikacji pod kątem skalowania, składniki aplikacji są często rozłączane, dzięki czemu można skalować niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Magazyn kolejek obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z magazynem kolejek Azure](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Wdrażanie konta magazynu

Dostępnych jest kilka opcji wdrażania konta magazynu.

**Portal**

Wdrażanie konta magazynu przy użyciu portalu Azure wymaga tylko aktywną subskrypcją platformy Azure i dostęp do przeglądarki sieci web. Nowe konto magazynu można wdrożyć do grupy nowy lub istniejący zasób. Po utworzeniu konta magazynu obiektów blob kontenera lub udział plików można utworzyć za pomocą portalu. Można utworzyć tabeli i programowo kolejki jednostek magazynu. Aby uzyskać więcej informacji, zobacz [Utwórz konto magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

Oprócz wdrażania konto magazynu z portalu Azure, można wdrożyć szablonu usługi Azure Resource Manager z portalu. To wdroży i skonfiguruje wszystkie zasoby, zgodnie z definicją w szablonie, łącznie ze wszystkich kont magazynu. Aby uzyskać więcej informacji, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i portalu Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**Program PowerShell**

Wdrażanie konta magazynu platformy Azure przy użyciu programu PowerShell pozwala na automatyzację Zakończenie wdrażania konta magazynu. Aby uzyskać więcej informacji, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Oprócz indywidualnie wdrażania zasobów platformy Azure, moduł Azure PowerShell służy do wdrażania szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Interfejs wiersza polecenia (CLI)**

Podobnie jak w przypadku modułu programu PowerShell, interfejsu wiersza polecenia platformy Azure umożliwia automatyzację wdrożenia i może być używany z systemami Windows, OS X lub Linux. Można użyć interfejsu wiersza polecenia Azure **Tworzenie konta magazynu** polecenie, aby utworzyć konto magazynu. Aby uzyskać więcej informacji, zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage.](../../storage/common/storage-azure-cli.md)

Analogicznie można użyć wiersza polecenia platformy Azure do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i interfejsu wiersza polecenia Azure](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Dostęp i większe bezpieczeństwo usługi Azure Storage

Usługa Azure Storage jest dostępny na różne sposoby, w tym jednak portalu Azure, podczas tworzenia maszyny Wirtualnej i operacji i z bibliotek klienckich magazynu. 

**Dyski maszyny wirtualnej**

Podczas wdrażania maszyny wirtualnej, należy utworzyć konto magazynu do przechowywania dysku systemu operacyjnego maszyny wirtualnej i dowolnego dodatkowego dysku z danymi. Można wybrać istniejące konto magazynu lub Utwórz nową. Jednego dysku maszyny Wirtualnej, ponieważ maksymalny rozmiar obiektu blob jest 1024 GB, ma maksymalny rozmiar 1,023 GB. Aby skonfigurować większy dysk danych, można prezentować wiele dysków danych do maszyny wirtualnej i puli je razem jako pojedynczy dysk logiczny. Aby uzyskać więcej informacji, zobacz "Zarządzaj dyskach platformy Azure" dla [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) i [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

**Narzędzia do magazynu**

Za pomocą wielu eksploratorów innego magazynu, takich jak Visual Studio Cloud Explorer można uzyskać dostępu do konta magazynu platformy Azure. Te narzędzia umożliwiają przeglądanie konta magazynu i danych. Aby uzyskać więcej informacji oraz listę eksploratorów dostępny magazyn, zobacz [narzędzia klienta usługi Azure Storage](../../storage/common/storage-explorers.md).

**Interfejs API magazynu**

Za pomocą dowolnego języka, który może zgłaszać żądania HTTP i HTTPS można uzyskać dostępu do zasobów magazynu. Dodatkowo Magazyn Azure oferuje biblioteki programistyczne dla kilku popularnych języków. Te biblioteki upraszczają pracy z magazynem Azure, obsługując szczegóły takie jak wywołania synchroniczne i asynchroniczne, przetwarzanie wsadowe operacji, Zarządzanie wyjątkami i Automatyczne ponawianie prób. Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu API REST usługi Azure Storage](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

**Klucze dostępu do magazynu**

Każde konto magazynu ma dwa klucze uwierzytelniania, podstawowego i pomocniczego. Mogą zostać użyte dla operacji dostępu do magazynu. Te klucze magazynu są używane do zabezpieczania konta magazynu i są wymagane do uzyskiwania dostępu do danych. Istnieją dwa klucze, aby umożliwić okazjonalne przerzucania kluczy w celu zwiększenia bezpieczeństwa. Jest bardzo ważne bezpieczeństwo kluczy, ponieważ ich posiadania, oraz nazwę konta umożliwia nieograniczony dostęp do wszelkich danych na koncie magazynu.

**Sygnatury dostępu współdzielonego**

Jeśli potrzebujesz użytkownicy mogli mieć kontrolowany dostęp do zasobów magazynu, można utworzyć sygnatury dostępu współdzielonego. Sygnatury dostępu współdzielonego to token, który można dołączyć do adresu URL, który pozwala na delegowany dostęp do zasobów magazynu. Każdy, kto posiada token ma dostęp do zasobu, który wskazuje z uprawnieniami że Określa, podczas okresu jego prawidłowe. Aby uzyskać więcej informacji, zobacz [używanie sygnatury dostępu współdzielonego](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network


Sieci wirtualne są niezbędne do obsługi komunikacji między maszynami wirtualnymi. Możesz zdefiniować niestandardowy adres IP, ustawienia DNS, filtrowania zabezpieczeń w podsieci i równoważenie obciążenia. Przy użyciu bramy sieci VPN lub obwodu usługi ExpressRoute, można połączyć sieci wirtualnych platformy Azure z sieciami lokalnymi.

### <a name="use-cases"></a>Przypadki zastosowań

Brak innych przypadków użycia sieci platformy Azure.

**Sieci wirtualne tylko w chmurze**

Sieć wirtualna platformy Azure, domyślnie jest dostępny tylko dla zasobów przechowywanych na platformie Azure. Zasoby podłączone do tej samej sieci wirtualnej mogą komunikować się ze sobą. Można skojarzyć interfejsy sieciowe maszyny wirtualnej i usług z publicznym adresem IP, aby udostępnić maszynie wirtualnej za pośrednictwem Internetu. Bezpieczny dostęp do zasobów publicznie ujawnionych pozwalają przy użyciu grupy zabezpieczeń sieci.

**Między różnymi lokalizacjami, sieci wirtualnych**

Sieci lokalnej można połączyć sieć wirtualną platformy Azure, przy użyciu usługi ExpressRoute lub połączeń VPN lokacja lokacja. W tej konfiguracji sieci wirtualnej platformy Azure to zasadniczo oparte na chmurze rozszerzenie sieci lokalnej.

Ponieważ sieci wirtualnej platformy Azure jest podłączony do sieci lokalnej, między lokalizacjami się, że sieci wirtualne, należy użyć unikatowy część przestrzeni adresowej używanymi przez organizację. W taki sam sposób jak różnych lokalizacjach w firmie są przypisane do określonej podsieci IP Azure staje się innej lokalizacji poszerzają sieci.

### <a name="deploying-a-virtual-network"></a>Wdrażanie sieci wirtualnej

Dostępnych jest kilka opcji wdrażania sieci wirtualnej.

**Portal**

Wdrażanie sieci wirtualnej platformy Azure przy użyciu portalu Azure wymaga tylko aktywną subskrypcją platformy Azure i dostęp do przeglądarki sieci web. Można wdrożyć nową sieć wirtualną do grupy nowy lub istniejący zasób. Podczas tworzenia nowej maszyny wirtualnej z portalu, możesz wybrać istniejącej sieci wirtualnej lub Utwórz nową. Aby uzyskać więcej informacji, zobacz [utworzyć sieć wirtualną przy użyciu portalu Azure](../../virtual-network/quick-create-portal.md).

Oprócz wdrażania sieci wirtualnej platformy Azure w portalu Azure, można wdrożyć szablonu usługi Azure Resource Manager z portalu. To wdroży i skonfiguruje wszystkie zasoby, zgodnie z definicją w szablonie, w tym wszystkie zasoby sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i portalu Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**Program PowerShell**

Wdrażanie sieci wirtualnej platformy Azure przy użyciu programu PowerShell pozwala na automatyzację Zakończenie wdrażania konta magazynu. Aby uzyskać więcej informacji, zobacz [utworzyć sieć wirtualną przy użyciu programu PowerShell](../../virtual-network/quick-create-powershell.md).

Oprócz indywidualnie wdrażania zasobów platformy Azure, moduł Azure PowerShell służy do wdrażania szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Interfejs wiersza polecenia (CLI)**

Podobnie jak w przypadku modułu programu PowerShell, interfejsu wiersza polecenia platformy Azure umożliwia automatyzację wdrożenia i może być używany z systemami Windows, OS X lub Linux. Można użyć interfejsu wiersza polecenia Azure **tworzenie sieci wirtualnej sieci** polecenie, aby utworzyć sieć wirtualną. Aby uzyskać więcej informacji, zobacz [utworzyć sieć wirtualną przy użyciu interfejsu wiersza polecenia Azure](../../virtual-network/quick-create-cli.md).

Analogicznie można użyć wiersza polecenia platformy Azure do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i interfejsu wiersza polecenia Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-networks"></a>Dostęp i większe bezpieczeństwo sieci wirtualnej

Bezpieczne sieci wirtualnych platformy Azure może pomóc za pomocą grupy zabezpieczeń sieci. Grupy NSG zawierają listę reguł listę kontroli dostępu (ACL) kontroli dostępu, które akceptować lub odrzucać ruch sieciowy do wystąpień maszyn wirtualnych w sieci wirtualnej. Grupy NSG można skojarzyć z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w danej podsieci. Po skojarzeniu grupy NSG z podsiecią, reguły listy ACL mają zastosowanie do wszystkich wystąpień maszyn wirtualnych w tej podsieci. Ponadto można bardziej ograniczyć ruch do poszczególnych maszyn wirtualnych przez skojarzenie grupy NSG bezpośrednio z tej maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [filtrowania ruchu sieciowego z grup zabezpieczeń sieci](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie maszyny Wirtualnej systemu Windows](/virtual-machines/windows/quick-create-portal.md)
- [Utwórz Maszynę wirtualną systemu Linux](../../virtual-machines/linux/quick-create-portal.md)
