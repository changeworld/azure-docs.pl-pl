---
title: Przewodnik po rozpoczęciu pracy dla operatorów IT platformy Azure | Dokumenty firmy Microsoft
description: Przewodnik wprowadzenie dla operatorów IT platformy Azure
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 4f9da6cbfe8d1e6b92c39148b275de193730c8f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77623575"
---
# <a name="get-started-for-azure-it-operators"></a>Wprowadzenie do operatorów IT platformy Azure

W tym przewodniku przedstawiono podstawowe pojęcia związane z wdrażaniem infrastruktury platformy Microsoft Azure i zarządzanie nią. Jeśli jesteś nowy w chmurze obliczeniowej lub samej platformy Azure, ten przewodnik pomaga szybko rozpocząć pracę z pojęciami, wdrożeniami i szczegółami zarządzania. Wiele sekcji tego przewodnika omówiono operację, taką jak wdrażanie maszyny wirtualnej, a następnie udostępnić łącze do szczegółowych szczegółów technicznych.

## <a name="cloud-computing-overview"></a>Omówienie obliczeń w chmurze

Chmura obliczeniowa stanowi nowoczesną alternatywę dla tradycyjnego lokalnego centrum danych. Dostawcy chmury publicznej zapewniają i zarządzają całą infrastrukturą obliczeniową i podstawowym oprogramowaniem do zarządzania. Dostawcy ci zapewniają szeroką gamę usług w chmurze. Usługa w chmurze w tym przypadku może być maszyną wirtualną, serwerem sieci web lub aparatem bazy danych hostowanych w chmurze. Jako klient dostawcy chmury dzierżawisz te usługi w chmurze zgodnie z potrzebami. W ten sposób należy przekonwertować koszt kapitałowy konserwacji sprzętu na koszty operacyjne. Usługa w chmurze zapewnia również następujące korzyści:

- Szybkie wdrażanie dużych środowisk obliczeniowych

- Szybkie przemieszczanie systemów, które nie są już potrzebne

- Łatwe wdrażanie tradycyjnie złożonych systemów, takich jak moduły równoważenia obciążenia

- Możliwość zapewnienia elastycznej pojemności obliczeniowej lub skali w razie potrzeby

- Bardziej ekonomiczne środowiska obliczeniowe

- Dostęp z dowolnego miejsca za pomocą portalu internetowego lub automatyzacji programowej

- Usługi oparte na chmurze, aby zaspokoić większość potrzeb obliczeniowych i aplikacji

Dzięki infrastrukturze lokalnej masz pełną kontrolę nad sprzętem i oprogramowaniem, które są wdrażane. Historycznie doprowadziło to do decyzji o zaopatrzeniu w sprzęt, które koncentrują się na zwiększaniu skali. Przykładem jest zakup serwera z większą liczą bazami, aby sprostać wymaganiom szczytowej wydajności. Niestety, ta infrastruktura może być niedostatecznie zaauchowany poza oknem popytu. Za pomocą platformy Azure można wdrożyć tylko infrastrukturę, której potrzebujesz, i dostosować tę tę tę usługę w górę lub w dół w dowolnym momencie. Prowadzi to do skupienia się na skalowaniu w poziomie poprzez wdrożenie dodatkowych węzłów obliczeniowych w celu zaspokojenia potrzeb w zakresie wydajności. Skalowanie w poziomie usług w chmurze jest bardziej opłacalne niż skalowanie za pomocą drogiego sprzętu.

Firma Microsoft wdrożyła wiele centrów danych platformy Azure na całym świecie, z bardziej zaplanowanymi. Ponadto Microsoft zwiększa suwerenne chmury w regionach takich jak Chiny i Niemcy. Tylko największe globalne przedsiębiorstwa mogą wdrażać centra danych w ten sposób, więc korzystanie z platformy Azure ułatwia przedsiębiorstwom dowolnej wielkości wdrażanie swoich usług blisko swoich klientów.

W przypadku małych firm platforma Azure umożliwia korzystanie z taniego punktu wejścia z możliwością szybkiego skalowania w miarę wzrostu popytu na obliczenia. Zapobiega to dużym inwestycjom kapitałowym z góry w infrastrukturę i zapewnia elastyczność systemów architektów i re-architektów w razie potrzeby. Korzystanie z chmury obliczeniowej dobrze pasuje do szybkiego i szybkiego modelu uruchamiania.

Aby uzyskać więcej informacji na temat dostępnych regionów platformy Azure, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Model przetwarzania w chmurze

Platforma Azure korzysta z modelu przetwarzania w chmurze opartego na kategoriach usług świadczonych klientom. Trzy kategorie usług obejmują infrastrukturę jako usługę (IaaS), platformę jako usługę (PaaS) i oprogramowanie jako usługę (SaaS). Dostawcy współużytkują część lub całość odpowiedzialności za składniki w stosie obliczeniowym w każdej z tych kategorii. Przyjrzyjmy się każdej z kategorii przetwarzania w chmurze.
![Porównanie stosu przetwarzania w chmurze](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktura jako usługa

Dostawca chmury IaaS uruchamia i zarządza wszystkimi fizycznymi zasobami obliczeniowymi i wymaganym oprogramowaniem, aby umożliwić wirtualizację komputera. Klient tej usługi wdraża maszyny wirtualne w tych hostowanych centrach danych. Mimo że maszyny wirtualne znajdują się w centrum danych poza siedzibą firmy, konsument usługi IaaS ma kontrolę nad konfiguracją i zarządzaniem systemem operacyjnym, pozostawiając podstawową infrastrukturę dostawcy chmury.

Platforma Azure zawiera kilka rozwiązań IaaS, w tym maszyny wirtualne, zestawy skalowania maszyn wirtualnych i powiązaną infrastrukturę sieciową. Maszyny wirtualne są popularnym wyborem dla początkowo migracji usług na platformę Azure, ponieważ umożliwia model migracji "lift and shift". Maszynę Wirtualną można skonfigurować, podobnie jak infrastrukturę aktualnie uruchamianą usługi w centrum danych, a następnie przeprowadzić migrację oprogramowania do nowej maszyny Wirtualnej. Może być konieczne dokonanie aktualizacji konfiguracji, takich jak adresy URL do innych usług lub magazynu, ale można migrować wiele aplikacji w ten sposób.

Zestawy skalowania maszyny wirtualnej są oparte na maszynach wirtualnych platformy Azure i umożliwiają łatwe wdrażanie klastrów identycznych maszyn wirtualnych. Zestawy skalowania maszyny wirtualnej obsługują również skalowanie automatyczne, dzięki czemu nowe maszyny wirtualne mogą być wdrażane automatycznie, gdy jest to wymagane. Dzięki temu skala maszyny wirtualnej ustawia idealną platformę do obsługi klastrów obliczeniowych mikrousług wyższego poziomu, takich jak sieć szkieletowa azure i usługa Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platforma jako usługa

Dzięki paaS wdrożyć aplikację w środowisku, które zapewnia dostawca usługi w chmurze. Dostawca wykonuje wszystkie zarządzanie infrastrukturą, dzięki czemu można skupić się na rozwoju aplikacji i zarządzania danymi.

Platforma Azure oferuje kilka ofert obliczeniowych PaaS, w tym funkcję aplikacji sieci Web usługi Azure App Service i usług w chmurze Azure (role sieci web i procesu roboczego). W obu przypadkach deweloperzy mają wiele sposobów, aby wdrożyć swoją aplikację, nie wiedząc nic o nakrętki i śruby, które go obsługują. Deweloperzy nie muszą tworzyć maszyn wirtualnych,używać protokołu RDP (Remote Desktop Protocol), aby zalogować się do każdej z nich lub zainstalować aplikację. Po prostu nacisnąć przycisk (lub blisko niego), a narzędzia dostarczone przez firmę Microsoft aprowizować maszyny wirtualne, a następnie wdrożyć i zainstalować aplikację na nich.

#### <a name="saas-software-as-a-service"></a>SaaS: Oprogramowanie jako usługa

SaaS to oprogramowanie, które jest centralnie hostowane i zarządzane. Zazwyczaj jest on oparty na architekturze wielodostępnej — pojedyncza wersja aplikacji jest używana dla wszystkich klientów. Można go skalować w poziomie do wielu wystąpień, aby zapewnić najlepszą wydajność we wszystkich lokalizacjach. Oprogramowanie SaaS jest zazwyczaj licencjonowane w ramach miesięcznej lub rocznej subskrypcji. Dostawcy oprogramowania SaaS są odpowiedzialni za wszystkie składniki stosu oprogramowania, więc wszystko, co zarządzasz, to świadczone usługi.

Microsoft Office 365 jest dobrym przykładem oferty SaaS. Subskrybenci płacą miesięczną lub roczną opłatę subskrypcyjną, a jako usługę otrzymują program Microsoft Exchange, Microsoft OneDrive i pozostałą część pakietu Microsoft Office. Subskrybenci zawsze otrzymują najnowszą wersję, a serwer Exchange jest zarządzany za Ciebie. W porównaniu do instalowania i uaktualniania pakietu Office każdego roku, jest to tańsze i wymaga mniej wysiłku.

## <a name="azure-services"></a>Usługi platformy Azure

Platforma Azure oferuje wiele usług na swojej platformie przetwarzania w chmurze. Usługi te obejmują następujące czynności.

### <a name="compute-services"></a>Usługi obliczeniowe

Usługi hostingu i uruchamiania obciążenia aplikacji:

- Maszyny wirtualne platformy Azure — zarówno w systemie Linux, jak i Windows

- Usługi aplikacji (aplikacje sieci Web, aplikacje mobilne, aplikacje logiki, aplikacje interfejsu API i aplikacje funkcyjne)

- Usługa Azure Batch (dla dużych równoległych i wsadowych zadań obliczeniowych)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Usługi danych

Usługi przechowywania danych i zarządzania nimi:

- Usługa Azure Storage (obejmuje usługi Azure Blob, Queue, Table i File services)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>Usługi aplikacyjne

Usługi w zakresie budownictwa i eksploatacji:

- Azure Active Directory (Azure AD)

- Usługa Azure Service Bus do łączenia systemów rozproszonych

- Usługa Azure HDInsight do przetwarzania dużych zbiorów danych

- Usługi Azure Logic Apps do integracji i aranżacji przepływów pracy

- Usługi multimediów platformy Azure

### <a name="network-services"></a>Usługi sieciowe

Usługi do tworzenia sieci zarówno na platformie Azure, jak i między platformą Azure i lokalnymi centrami danych:

- Azure Virtual Network

- Azure ExpressRoute

- Usługa DNS dostarczona przez platformę Azure

- Azure Traffic Manager

- Azure Content Delivery Network

Aby uzyskać szczegółową dokumentację dotyczącą usług platformy Azure, zobacz [dokumentację usługi platformy Azure.](https://docs.microsoft.com/azure)

## <a name="azure-key-concepts"></a>Kluczowe koncepcje platformy Azure

### <a name="datacenters-and-regions"></a>Centra danych i regiony

Platforma Azure to globalna platforma w chmurze, która jest ogólnie dostępna w wielu regionach na całym świecie. Podczas inicjowania obsługi administracyjnej usługi, aplikacji lub maszyny wirtualnej na platformie Azure, zostaniesz poproszony o wybranie regionu. Wybrany region reprezentuje określone centrum danych, w którym działa aplikacja. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

Jedną z zalet korzystania z platformy Azure jest to, że można wdrożyć aplikacje w różnych centrach danych na całym świecie. Region, który wybierzesz może mieć wpływ na wydajność aplikacji. Optymalne jest wybranie regionu, który jest bliżej większości klientów, aby zmniejszyć opóźnienia w żądaniach sieciowych. Możesz też wybrać region, aby spełnić wymagania prawne dotyczące dystrybucji aplikacji w niektórych krajach/regionach.

### <a name="azure-portal"></a>Portal Azure

Portal Azure to aplikacja oparta na sieci Web, która może służyć do tworzenia zasobów i usług platformy Azure, zarządzania nimi i usuwania ich. Portal Azure znajduje się w [portal.azure.com](https://portal.azure.com). Zawiera konfigurowalny pulpit nawigacyjny i narzędzia do zarządzania zasobami platformy Azure. Zawiera również informacje rozliczeniowe i subskrypcyjne. Aby uzyskać więcej informacji, zobacz [Omówienie portalu platformy Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) i zarządzanie [zasobami platformy Azure za pośrednictwem portalu](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Resources

Zasoby platformy Azure to indywidualne usługi obliczeniowe, sieciowe, danych lub hosting aplikacji, które zostały wdrożone w ramach subskrypcji platformy Azure. Niektóre typowe zasoby to maszyny wirtualne, konta magazynu lub bazy danych SQL. Usługi platformy Azure często składają się z kilku powiązanych zasobów platformy Azure. Na przykład maszyna wirtualna platformy Azure może zawierać maszynę wirtualną, konto magazynu, kartę sieciową i publiczny adres IP. Zasoby te można tworzyć, zarządzać i usuwać indywidualnie lub jako grupa. Zasoby platformy Azure są omówione bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="resource-groups"></a>Grupy zasobów

Grupa zasobów platformy Azure to kontener, który przechowuje powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko zasoby, którymi chcesz zarządzać jako grupa. Grupy zasobów platformy Azure są omówione bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Szablon usługi Azure Resource Manager to plik JSON (JavaScript Object Notation), który definiuje jeden lub więcej zasobów do wdrożenia w grupie zasobów. Definiuje również zależności między wdrożonymi zasobami. Szablony Menedżera zasobów są omówione bardziej szczegółowo w dalszej części tego przewodnika.

### <a name="automation"></a>Automatyzacja

Oprócz tworzenia, zarządzania i usuwania zasobów przy użyciu witryny Azure portal, można zautomatyzować te działania przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell to zestaw modułów, które zapewniają polecenia cmdlet do zarządzania platformą Azure. Polecenia cmdlet służy do tworzenia, zarządzania i usuwania usług platformy Azure. Polecenia cmdlet mogą pomóc w osiągnięciu spójnych, powtarzalnych i praktycznych wdrożeń. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to narzędzie, za pomocą którego można tworzyć zasoby platformy Azure, zarządzać nimi i usuwać je z wiersza polecenia. Interfejs wiersza polecenia platformy Azure jest dostępny dla systemów Linux, Mac OS X i Windows. Aby uzyskać więcej informacji i szczegółów technicznych, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>Interfejsy API REST

Platforma Azure jest oparta na zestawie interfejsów API REST, które obsługują interfejs użytkownika witryny azure portal. Większość z tych interfejsów API REST są również obsługiwane, aby umożliwić programowo aprowizowania i zarządzania zasobów platformy Azure i aplikacji z dowolnego urządzenia z dostępem do Internetu. Aby uzyskać więcej informacji, zobacz [odwołanie do zestawu SDK usługi Azure REST](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Administratorzy mogą uzyskać dostęp do usługi Azure PowerShell i interfejsu wiersza polecenia platformy Azure za pośrednictwem środowiska dostępnego dla przeglądarki o nazwie Usługa Azure Cloud Shell. Ten interaktywny interfejs zapewnia elastyczne narzędzie dla administratorów systemów Linux i Windows do korzystania z wybranego interfejsu wiersza polecenia, Bash lub PowerShell. Usługa Azure Cloud Shell może być dostępna za pośrednictwem portalu, jako autonomiczny interfejs sieci web w [shell.azure.com](https://shell.azure.com)lub z wielu innych punktów dostępu. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Subskrypcje platformy Azure

Subskrypcja to logiczne grupowanie usług platformy Azure, które jest połączone z kontem platformy Azure. Jedno konto platformy Azure może zawierać wiele subskrypcji. Rozliczenia za usługi platformy Azure są wykonywane na podstawie subskrypcji. Subskrypcje platformy Azure mają administratora konta, który ma pełną kontrolę nad subskrypcją, i administratora usługi, który ma kontrolę nad wszystkimi usługami w ramach subskrypcji. Aby uzyskać informacje o administratorach subskrypcji klasycznych, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). Oprócz administratorów poszczególnym kontom można uzyskać szczegółową kontrolę zasobów platformy Azure przy użyciu [kontroli dostępu opartej na rolach (RBAC).](../../role-based-access-control/overview.md)

### <a name="select-and-enable-an-azure-subscription"></a>Wybieranie i włączanie subskrypcji platformy Azure

Aby można było pracować z usługami platformy Azure, potrzebujesz subskrypcji. Dostępnych jest kilka typów subskrypcji.

**Darmowe konta**: Link do utworzenia bezpłatnego konta znajduje się w [witrynie azure.](https://azure.microsoft.com/) Daje to kredyt w ciągu 30 dni, aby wypróbować dowolną kombinację zasobów na platformie Azure. Jeśli przekroczysz kwotę kredytu, twoje konto zostanie zawieszone. Po zakończeniu okresu próbnego usługi są likwidowane i nie będą już działać. W każdej chwili możesz przejść na subskrypcję płatną zgodnie z rzeczywistym.You can upgrade to a pay-as-you-go subscription at any time.

**Subskrypcje MSDN:** Jeśli masz subskrypcję MSDN, otrzymasz określoną kwotę w kredytach platformy Azure każdego miesiąca. Na przykład jeśli masz Microsoft Visual Studio Enterprise z subskrypcją MSDN, otrzymasz \$150 miesięcznie w kredytach platformy Azure.

Jeśli przekroczysz kwotę kredytu, usługa zostanie wyłączona do następnego miesiąca. Możesz wyłączyć limit wydatków i dodać kartę kredytową, która ma być używana do dodatkowych kosztów. Niektóre z tych kosztów są dyskontowane dla kont MSDN. Na przykład płacisz cenę systemu Linux za maszyny wirtualne z systemem Windows Server i nie ma żadnych dodatkowych opłat dla serwerów firmy Microsoft, takich jak Microsoft SQL Server. Dzięki temu konta MSDN są idealne dla scenariuszy programowania i testowania.

**Konta BizSpark:** Program Microsoft BizSpark zapewnia wiele korzyści dla startupów. Jedną z tych korzyści jest dostęp do całego oprogramowania firmy Microsoft dla środowisk deweloperskich i testowych dla maksymalnie pięciu kont MSDN. Otrzymasz $150 w kredytach platformy Azure dla każdego z tych pięciu kont MSDN i płacisz obniżone stawki za kilka usług platformy Azure, takich jak maszyny wirtualne.

Płatność zgodnie z rzeczywistym **użyciem**: Dzięki tej subskrypcji płacisz za to, czego używasz, dołączając kartę kredytową lub debetową do konta. Jeśli jesteś organizacją, możesz również zostać zatwierdzony do fakturowania.

**Umowy enterprise:** Dzięki umowie enterprise agreement zobowiązujesz się do korzystania z określonej liczby usług na platformie Azure w ciągu następnego roku i płacisz tę kwotę z wyprzedzeniem. Zobowiązanie, które podejmujesz, jest konsumowane przez cały rok. Jeśli przekroczysz kwotę zobowiązania, możesz zapłacić zaległe zaległości. W zależności od kwoty zobowiązania otrzymasz zniżkę na usługi na platformie Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Udzielanie dostępu administracyjnego do subskrypcji platformy Azure

RBAC ma kilka wbudowanych ról, których można użyć do przypisania uprawnień. Aby użytkownik był administratorem subskrypcji platformy Azure, przypisz mu rolę [Właściciela](../../role-based-access-control/built-in-roles.md#owner) w zakresie subskrypcji. Rola właściciela daje użytkownikowi pełen dostęp do wszystkich zasobów w subskrypcji, w tym prawo do przydzielania dostępu innym osobom.

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Wyświetlanie informacji rozliczeniowych w witrynie Azure portal

Ważnym składnikiem korzystania z platformy Azure jest możliwość wyświetlania informacji rozliczeniowych. Portal platformy Azure zapewnia szczegółowy wgląd w informacje rozliczeniowe platformy Azure.

Aby uzyskać więcej informacji, zobacz [Jak pobrać fakturę rozliczeniową platformy Azure i dane o codziennym użytkowaniu](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Pobierz informacje o rozliczeniach z interfejsów API rozliczeń

Oprócz wyświetlania rozliczeń w portalu, można uzyskać dostęp do informacji rozliczeniowych przy użyciu skryptu lub programu za pośrednictwem interfejsu API REST rozliczeń platformy Azure:

- Za pomocą interfejsu API użycia platformy Azure można pobrać dane użycia. Informacje o użyciu rozliczeń można dostosować, oznaczając powiązane zasoby platformy Azure. Na przykład można oznaczyć każdy z zasobów w grupie zasobów o nazwie działu lub nazwie projektu, a następnie śledzić koszty specjalnie dla tego jednego tagu.

- Za pomocą interfejsu API karty szybkości platformy Azure można wyświetlić listę wszystkich dostępnych zasobów wraz z metadanymi i informacjami o cenach dla każdego z tych zasobów.

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie szczegółowych informacji o użyciu zasobów platformy Microsoft Azure](../../cost-management-billing/manage/usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognozowany koszt za pomocą kalkulatora cen

Ceny dla każdej usługi na platformie Azure są różne. Wiele usług platformy Azure zapewnia warstwy Podstawowe, Standardowe i Premium. Zazwyczaj każdy poziom ma kilka poziomów cen i wydajności. Korzystając z [kalkulatora cen online,](https://azure.microsoft.com/pricing/calculator)można utworzyć wartości szacunkowe cen. Kalkulator zawiera elastyczność szacowania kosztów pojedynczego zasobu lub grupy zasobów.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Usługa Azure Resource Manager to mechanizm wdrażania, zarządzania i organizacji dla zasobów platformy Azure. Za pomocą Menedżera zasobów, można umieścić wiele pojedynczych zasobów razem w grupie zasobów.

Menedżer zasobów zawiera również możliwości wdrażania, które umożliwiają dostosowywanie wdrażania i konfigurowania powiązanych zasobów. Na przykład za pomocą Menedżera zasobów, można wdrożyć aplikację, która składa się z wielu maszyn wirtualnych, moduł równoważenia obciążenia i bazy danych SQL jako pojedynczej jednostki. Te wdrożenia można opracować przy użyciu szablonu Menedżera zasobów.

Usługa Resource Manager zapewnia kilka korzyści:

- Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).

- Można wielokrotnie wdrażać rozwiązanie w całym cyklu życia rozwoju i mieć pewność, że zasoby są wdrażane w spójnym stanie.

- Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

- Możliwość definiowania zależności między zasobami, aby wdrażać je w odpowiedniej kolejności.

- Kontrola dostępu można zastosować do wszystkich usług w grupie zasobów, ponieważ RBAC jest natywnie zintegrowany z platformą zarządzania.

- Tagi można zastosować do zasobów, aby logicznie zorganizować wszystkie zasoby w ramach subskrypcji.

- Rozliczenia organizacji można wyjaśnić, wyświetlając koszty dla grupy zasobów, które mają ten sam tag.

### <a name="tips-for-creating-resource-groups"></a>Wskazówki dotyczące tworzenia grup zasobów

Podejmując decyzje dotyczące grup zasobów, rozważ następujące wskazówki:

- Wszystkie zasoby w grupie zasobów powinny mieć ten sam cykl życia.

- Zasób można przypisać tylko do jednej grupy naraz.

- Zasób z grupy zasobów można w dowolnym momencie dodać lub usunąć z grupy zasobów. Każdy zasób musi należeć do grupy zasobów. Jeśli więc usuniesz zasób z jednej grupy, musisz dodać go do innej.

- Większość typów zasobów można przenieść do innej grupy zasobów w dowolnym momencie.

- Zasoby w grupie zasobów mogą znajdować się w różnych regionach.

- Za pomocą grupy zasobów można kontrolować dostęp do zasobów w niej.

### <a name="building-resource-manager-templates"></a>Szablony Menedżera zasobów budynku

Szablony Menedżera zasobów deklaratywnie definiują konfiguracje zasobów i zasobów, które zostaną wdrożone w jednej grupie zasobów. Szablony Menedżera zasobów służy do organizowania złożonych wdrożeń bez konieczności nadmiaru skryptów lub konfiguracji ręcznej. Po opracowaniu szablonu można go wdrożyć wiele razy — za każdym razem z identycznym wynikiem.

Szablon Menedżera zasobów składa się z czterech sekcji:

- **Parametry:** Są to dane wejściowe do wdrożenia. Wartości parametrów mogą być dostarczane przez człowieka lub zautomatyzowany proces. Przykładowym parametrem może być nazwa użytkownika administratora i hasło dla maszyny Wirtualnej systemu Windows. Wartości parametrów są używane w całym wdrożeniu, gdy są one określone.

- **Zmienne:** Są one używane do przechowywania wartości, które są używane w całym wdrożeniu. W przeciwieństwie do parametrów wartość zmiennej nie jest podana w czasie wdrażania. Zamiast tego jest zakodowany na czas lub dynamicznie generowany.

- **Zasoby:** Ta sekcja szablonu definiuje zasoby, które mają zostać wdrożone, takie jak maszyny wirtualne, konta magazynu i sieci wirtualne.

- **Dane wyjściowe:** Po zakończeniu wdrażania Menedżer zasobów może zwracać dane, takie jak dynamicznie generowane parametry połączenia.

Następujące mechanizmy są dostępne dla automatyzacji wdrażania:

- **Funkcje**: W szablonach Menedżera zasobów można użyć kilku funkcji. Należą do nich operacje, takie jak konwertowanie ciągu na małe litery, wdrażanie wielu wystąpień zdefiniowanego zasobu i dynamiczne zwracanie docelowej grupy zasobów. Funkcje Menedżera zasobów pomagają w tworzeniu dynamicznych wdrożeń.

- **Zależności zasobów:** Podczas wdrażania wielu zasobów, niektóre zasoby będą miały zależność od innych. Aby ułatwić wdrożenie, można użyć deklaracji zależności, tak aby zasoby zależne są wdrażane przed innymi.

- **Łączenie szablonów:** Z jednego szablonu Menedżera zasobów można utworzyć łącze do innego szablonu. Umożliwia to rozkład wdrożenia do zestawu szablonów ukierunkowanych, specyficzne dla celu.

Szablony Menedżera zasobów można tworzyć w dowolnym edytorze tekstu. Jednak zestaw SDK platformy Azure dla programu Visual Studio zawiera narzędzia, które pomogą Ci. Za pomocą programu Visual Studio, można dodać zasoby do szablonu za pomocą kreatora, a następnie wdrożyć i debugować szablon bezpośrednio z poziomu programu Visual Studio. Aby uzyskać więcej informacji, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md).

Na koniec można przekonwertować istniejące grupy zasobów na szablon wielokrotnego użycia z witryny Azure portal. Może to być przydatne, jeśli chcesz utworzyć szablon można wdrożyć istniejącej grupy zasobów lub po prostu chcesz zbadać podstawowej JSON. Aby wyeksportować grupę zasobów, wybierz przycisk **Skrypt automatyzacji** z ustawień grupy zasobów.

## <a name="security-of-azure-resources-rbac"></a>Bezpieczeństwo zasobów platformy Azure (RBAC)

Można udzielić dostępu operacyjnego do kont użytkowników w określonym zakresie: subskrypcji, grupie zasobów lub poszczególnych zasobach. Oznacza to, że można wdrożyć zestaw zasobów w grupie zasobów, takich jak maszyna wirtualna i wszystkie powiązane zasoby, i udzielić uprawnień określonemu użytkownikowi lub grupie. Takie podejście ogranicza dostęp tylko do zasobów, które należą do docelowej grupy zasobów. Można również udzielić dostępu do pojedynczego zasobu, takiego jak maszyna wirtualna lub sieć wirtualna.

Aby udzielić dostępu, należy przypisać rolę do użytkownika lub grupy użytkowników. Istnieje wiele wstępnie zdefiniowanych ról. Można również zdefiniować własne role niestandardowe.

Oto kilka przykładowych [wbudowanych ról na platformie Azure:](../../role-based-access-control/built-in-roles.md)

- **Właściciel**: Użytkownik z tą rolą może zarządzać wszystkim, w tym dostępem.

- **Czytnik:** Użytkownik z tą rolą może odczytywać zasoby wszystkich typów (z wyjątkiem wpisów tajnych), ale nie może wprowadzać zmian.

- **Współautor maszyny wirtualnej:** użytkownik z tą rolą może zarządzać maszynami wirtualnymi, ale nie może zarządzać siecią wirtualną, z którą są połączeni, ani kontem magazynu, na którym znajduje się plik VHD.

- **Współautor bazy danych SQL:** Użytkownik z tą rolą może zarządzać bazami danych SQL, ale nie ich zasadami związanymi z zabezpieczeniami.

- **Menedżer zabezpieczeń SQL:** Użytkownik z tą rolą może zarządzać zasadami dotyczącymi zabezpieczeń serwerów i baz danych SQL.

- **Współautor konta magazynu:** użytkownik z tą rolą może zarządzać kontami magazynu, ale nie może zarządzać dostępem do kont magazynu.

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Maszyny wirtualne platformy Azure to jedna z centralnych usług IaaS na platformie Azure. Maszyny wirtualne platformy Azure obsługuje wdrażanie maszyn wirtualnych systemu Windows lub Linux w centrum danych platformy Microsoft Azure. Dzięki platformie Azure Virtual Machines masz całkowitą kontrolę nad konfiguracją maszyny wirtualnej i jesteś odpowiedzialny za całą instalację oprogramowania, konfigurację i konserwację.

Podczas wdrażania maszyny Wirtualnej platformy Azure, można wybrać obraz z portalu Azure Marketplace lub można podać własny obraz uogólniony. Ten obraz jest używany do zastosowania systemu operacyjnego i konfiguracji początkowej. Podczas wdrażania Menedżer zasobów będzie obsługiwać niektóre ustawienia konfiguracji, takie jak przypisywanie nazwy komputera, poświadczeń administracyjnych i konfiguracji sieci. Rozszerzenia maszyn wirtualnych platformy Azure umożliwiają dalszą automatyzację konfiguracji, takich jak instalacja oprogramowania, konfiguracja antywirusowa i rozwiązania do monitorowania.

Maszyny wirtualne można tworzyć w wielu różnych rozmiarach. Rozmiar maszyny wirtualnej dyktuje alokacji zasobów, takich jak przetwarzanie, pamięci i pojemności magazynu. W niektórych przypadkach określone funkcje, takie jak karty sieciowe obsługujące technologię RDMA i dyski SSD, są dostępne tylko w przypadku niektórych rozmiarów maszyn wirtualnych. Aby uzyskać pełną listę rozmiarów i możliwości maszyn wirtualnych, zobacz "Rozmiary maszyn wirtualnych na platformie Azure" dla [systemów Windows](../../virtual-machines/windows/sizes.md) i [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Przypadki zastosowań

Ponieważ maszyny wirtualne platformy Azure oferują pełną kontrolę nad konfiguracją, są one idealne dla szerokiego zakresu obciążeń serwerów, które nie pasują do modelu PaaS. Obciążenia serwerów, takie jak serwery baz danych (SQL Server, Oracle lub MongoDB), usługa Windows Server Active Directory, microsoft sharepoint i wiele innych stają się możliwe do uruchomienia na platformie Microsoft Azure. W razie potrzeby można przenieść takie obciążenia z lokalnego centrum danych do jednego lub więcej regionów platformy Azure, bez dużej ilości ponownej konfiguracji.

### <a name="deployment-of-virtual-machines"></a>Wdrażanie maszyn wirtualnych

Maszyny wirtualne platformy Azure można wdrożyć przy użyciu witryny Azure portal, przy użyciu automatyzacji z modułem azure powershell lub przy użyciu automatyzacji z wieloplatformowym interfejsu wiersza polecenia.

#### <a name="portal"></a>Portal

Wdrażanie maszyny wirtualnej przy użyciu witryny Azure portal wymaga tylko aktywnej subskrypcji platformy Azure i dostępu do przeglądarki sieci web. Można wybrać wiele różnych obrazów systemu operacyjnego o różnych konfiguracjach. Wszystkie wymagania dotyczące magazynu i sieci są konfigurowane podczas wdrażania. Aby uzyskać więcej informacji, zobacz "Tworzenie maszyny wirtualnej w portalu Azure" dla [systemów Windows](../../virtual-machines/windows/quick-create-portal.md) i [Linux](../../virtual-machines/linux/quick-create-portal.md).

Oprócz wdrażania maszyny wirtualnej z witryny Azure portal, można wdrożyć szablon usługi Azure Resource Manager z portalu. Spowoduje to wdrożenie i skonfigurowanie wszystkich zasobów zgodnie z definicją w szablonie. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i witryny Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Wdrażanie maszyny wirtualnej platformy Azure przy użyciu programu PowerShell umożliwia pełną automatyzację wdrażania wszystkich powiązanych zasobów maszyny wirtualnej, w tym magazynu i sieci. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny Wirtualnej systemu Windows przy użyciu Menedżera zasobów i programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Oprócz indywidualnego wdrażania zasobów obliczeniowych platformy Azure można użyć modułu programu Azure PowerShell do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Interfejs wiersza polecenia

Podobnie jak w przypadku modułu Programu PowerShell, interfejs wiersza polecenia platformy Azure zapewnia automatyzację wdrażania i może być używany w systemach Windows, OS X lub Linux. Podczas korzystania z polecenia **szybkiego tworzenia maszyny wirtualnej** interfejsu wiersza polecenia platformy Azure są wdrażane wszystkie powiązane zasoby maszyny wirtualnej (w tym magazyn i sieć) oraz sama maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny Wirtualnej z systemem Linux na platformie Azure przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/quick-create-cli.md).

Podobnie można użyć interfejsu wiersza polecenia platformy Azure do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Dostęp i zabezpieczenia maszyn wirtualnych

Uzyskiwanie dostępu do maszyny wirtualnej z Internetu wymaga skonfigurowania skojarzonego interfejsu sieciowego lub modułu równoważenia obciążenia z publicznym adresem IP. Publiczny adres IP zawiera nazwę DNS, która zostanie rozpoznana na maszynie wirtualnej lub modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [adresy IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Można zarządzać dostępem do maszyny wirtualnej za pośrednictwem publicznego adresu IP przy użyciu zasobu sieciowej grupy zabezpieczeń (NSG). Grupa sieciowej sieciowej działa jak zapora i zezwala lub odrzuca ruch w interfejsie sieciowym lub podsieci na zestawie zdefiniowanych portów. Na przykład, aby utworzyć sesję pulpitu zdalnego z maszyną wirtualną platformy Azure, należy skonfigurować sieć sieciową sieciową, aby zezwolić na ruch przychodzący na porcie 3389. Aby uzyskać więcej informacji, zobacz [Otwieranie portów na maszynie Wirtualnej na platformie Azure przy użyciu witryny Azure portal.](../../virtual-machines/windows/nsg-quickstart-portal.md)

Na koniec, podobnie jak w przypadku zarządzania dowolnego systemu komputerowego, należy zapewnić bezpieczeństwo maszyny wirtualnej platformy Azure w systemie operacyjnym przy użyciu poświadczeń zabezpieczeń i zapory oprogramowania.

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage to usługa zarządzana przez firmę Microsoft, która zapewnia magazyn trwałości, skalowalne i nadmiarowe. Konto magazynu platformy Azure można dodać jako zasób do dowolnej grupy zasobów przy użyciu dowolnej metody wdrażania zasobów. Platforma Azure zawiera cztery typy magazynu: magazyn obiektów Blob, magazyn plików, magazyn tabel i magazyn kolejek. Podczas wdrażania konta magazynu dostępne są dwa typy kont, ogólnego przeznaczenia i magazynu obiektów blob. Ogólne konto magazynu daje dostęp do wszystkich czterech typów magazynu. Konta magazynu obiektów Blob są podobne do kont ogólnego przeznaczenia, ale zawierają wyspecjalizowane obiekty blob, które zawierają warstwy dostępu na gorąco i na zimno. Aby uzyskać więcej informacji na temat magazynu obiektów blob, zobacz [magazyn obiektów Blob platformy Azure](../../storage/blobs/storage-blob-storage-tiers.md).

Konta magazynu platformy Azure można skonfigurować z różnymi poziomami nadmiarowości:

- **Lokalnie nadmiarowy magazyn** zapewnia wysoką dostępność, zapewniając, że trzy kopie wszystkich danych są wykonane synchronicznie przed zapisu zostanie uznany za pomyślny. Te kopie są przechowywane w jednym obiekcie w jednym regionie. Repliki znajdują się w oddzielnych domenach błędów i domenach uaktualnienia. Oznacza to, że dane są dostępne, nawet jeśli węzeł magazynu, który przechowuje dane nie powiedzie się lub zostanie przesuń do trybu offline, aby zaktualizować.

- **Magazyn geograficznie nadmiarowy** tworzy trzy synchroniczne kopie danych w regionie podstawowym dla wysokiej dostępności, a następnie asynchronicznie tworzy trzy repliki w sparowanym regionie do odzyskiwania po awarii.

- **Magazyn geograficzny dostępu do odczytu** jest magazynem geograficznym oraz możliwością odczytu danych w regionie pomocniczym. Ta umiejętność sprawia, że nadaje się do częściowego odzyskiwania po awarii. Jeśli występuje problem z regionem podstawowym, można zmienić aplikację, aby mieć dostęp tylko do odczytu do sparowanego regionu.

### <a name="use-cases"></a>Przypadki zastosowań

Każdy typ magazynu ma inny przypadek użycia.

#### <a name="blob-storage"></a>Blob Storage

Słowo *obiekt blob* jest skrótem od *binarnego dużego obiektu*. Obiekty BLOB to pliki nieustrukturyzowane, takie jak te przechowywane na komputerze. Magazyn obiektów blob umożliwia przechowywanie dowolnego typu danych tekstowych lub binarnych, takich jak dokumenty, pliki multimedialne lub instalatory aplikacji. Magazyn obiektów blob jest również nazywany magazynem obiektów. Usługa Azure Blob storage przechowuje również dyski danych maszyn wirtualnych platformy Azure.

Usługa Azure Storage obsługuje trzy typy obiektów blob:

- **Blokowe obiekty blob** są używane do przechowywania zwykłych plików o rozmiarze do 195 GB (4 MB × 50 000 bloków). Głównym zastosowaniem dla blokowych obiektów blob jest przechowywanie plików, które są odczytywane od początku do końca, np. plików multimedialnych lub plików obrazów na potrzeby witryn internetowych. Są one nazywane blokowymi obiektami blob, ponieważ pliki większe niż 64 MB muszą być przekazywane jako małe bloki. Bloki te są następnie konsolidowane (lub zatwierdzone) w ostatecznej obiekcie blob.

- **Obiekty BLOB** strony są używane do przechowywania plików o dostępie losowym o rozmiarze do 1 TB. Obiekty BLOB strony są używane głównie jako magazyn zapasowy dla dysków VHD, które zapewniają trwałe dyski dla maszyn wirtualnych platformy Azure, usługi obliczeniowej IaaS na platformie Azure. Stronicowe obiekty blob są tak nazywane, ponieważ zapewniają swobodny dostęp do odczytu/zapisu do 512-bajtowych stron.

- **Dołączanie obiektów blob** składa się z bloków, takich jak blok obiektów blob, ale są one zoptymalizowane pod kątem operacji dołączania. Są one często używane do rejestrowania informacji z jednego lub więcej źródeł do tego samego obiektu blob. Na przykład można zapisać wszystkie rejestrowania śledzenia do tego samego obiektu blob dołączania dla aplikacji, która jest uruchomiona na wielu maszynach wirtualnych. Pojedynczy uzupełniany obiekt blob może mieć rozmiar do 195 GB.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do magazynu obiektów Blob platformy Azure przy użyciu platformy .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>File Storage

Usługa Azure File Storage to usługa, która oferuje udziały plików w chmurze przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Usługa obsługuje zarówno SMB 2.1 i SMB 3.0. Dzięki usługi Azure File Storage można szybko i bez kosztownych przeróbek aplikacji, które opierają się na udziałach plików na platformie Azure. Aplikacje działające na maszynach wirtualnych platformy Azure, w usługach w chmurze lub z klientów lokalnych mogą zainstalować udział plików w chmurze. Jest to podobne do sposobu, w jaki aplikacja klasyczna montuje typowy udział SMB. Dowolna liczba składników aplikacji może następnie równocześnie zainstalować udział Magazynu plików i uzyskiwać do niego dostęp.

Ponieważ udział magazynu plików jest standardowym udziałem plików SMB, aplikacje działające na platformie Azure mogą uzyskiwać dostęp do danych w udziale za pośrednictwem interfejsów API we/wy systemu plików. Deweloperzy mogą zatem używać istniejącego kodu i umiejętności do migracji istniejących aplikacji. Profesjonalistów IT można użyć poleceń cmdlet programu PowerShell do tworzenia, instalowania i zarządzania udziałami magazynu plików w ramach administrowania aplikacjami platformy Azure.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do magazynu plików platformy Azure w systemie Windows](../../storage/files/storage-how-to-use-files-windows.md) lub Jak [korzystać z magazynu plików platformy Azure w systemie Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Magazyn tabel

Magazyn tabel Azure to usługa, która przechowuje dane strukturalne NoSQL w chmurze. Magazyn tabel jest magazynem kluczy/atrybutów o projekcie bez schematu. Ponieważ magazyn tabel jest bez schematu, łatwo jest dostosować dane w miarę rozwoju potrzeb aplikacji. Dostęp do danych jest szybki i ekonomiczny dla wszystkich rodzajów aplikacji. Magazyn tabel jest zwykle znacznie tańszy niż tradycyjne bazy SQL dla podobnych ilości danych.

Magazyn tabel umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkownika dla aplikacji internetowych, książki adresowe, informacje o urządzeniach i wszelkie inne metadane, których wymaga Twoja usługa. W tabeli można przechowywać dowolną liczbę encji. Konto magazynu może zawierać dowolną liczbę tabel, do limitu pojemności konta magazynu.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Table Storage](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Usługa Azure Queue Storage umożliwia przesyłanie komunikatów za pomocą chmury między składnikami aplikacji. Podczas projektowania aplikacji do skalowania składniki aplikacji są często oddzielone, dzięki czemu można je skalować niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Magazyn kolejek obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Queue storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Wdrażanie konta magazynu

Istnieje kilka opcji wdrażania konta magazynu.

#### <a name="portal"></a>Portal

Wdrażanie konta magazynu przy użyciu witryny Azure portal wymaga tylko aktywnej subskrypcji platformy Azure i dostępu do przeglądarki sieci web. Nowe konto magazynu można wdrożyć w nowej lub istniejącej grupie zasobów. Po utworzeniu konta magazynu można utworzyć kontener obiektów blob lub udział plików za pomocą portalu. Jednostki magazynu tabel i kolejek można tworzyć programowo. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](../../storage/common/storage-account-create.md).

Oprócz wdrażania konta magazynu z witryny Azure portal, można wdrożyć szablon usługi Azure Resource Manager z portalu. Spowoduje to wdrożenie i skonfigurowanie wszystkich zasobów zdefiniowanych w szablonie, w tym wszystkich kont magazynu. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i witryny Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Wdrażanie konta magazynu platformy Azure przy użyciu programu PowerShell umożliwia pełną automatyzację wdrażania konta magazynu. Aby uzyskać więcej informacji, zobacz [Korzystanie z programu Azure PowerShell z usługą Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Oprócz indywidualnego wdrażania zasobów platformy Azure można użyć modułu programu Azure PowerShell do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Interfejs wiersza polecenia

Podobnie jak w przypadku modułu Programu PowerShell, interfejs wiersza polecenia platformy Azure zapewnia automatyzację wdrażania i może być używany w systemach Windows, OS X lub Linux. Aby utworzyć konto magazynu platformy **Azure,** można użyć polecenia tworzenia konta magazynu platformy Azure. Aby uzyskać więcej informacji, zobacz [Korzystanie z interfejsu wiersza polecenia platformy Azure z usługą Azure Storage.](../../storage/common/storage-azure-cli.md)

Podobnie można użyć interfejsu wiersza polecenia platformy Azure do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Dostęp i zabezpieczenia usługi Azure Storage

Usługa Azure Storage jest dostępna na różne sposoby, w tym w witrynie Azure portal, podczas tworzenia i działania maszyny Wirtualnej oraz z bibliotek klienta magazynu.

#### <a name="virtual-machine-disks"></a>Dyski maszyn wirtualnych

Podczas wdrażania maszyny wirtualnej należy również utworzyć konto magazynu, aby pomieścić dysk systemu operacyjnego maszyny wirtualnej i wszelkie dodatkowe dyski danych. Można wybrać istniejące konto magazynu lub utworzyć nowe. Ponieważ maksymalny rozmiar obiektu blob wynosi 1024 GB, pojedynczy dysk maszyny Wirtualnej ma maksymalny rozmiar 1023 GB. Aby skonfigurować większy dysk danych, można przedstawić wiele dysków danych na maszynie wirtualnej i połączyć je razem jako jeden dysk logiczny. Aby uzyskać więcej informacji, zobacz "Zarządzanie dyskami platformy Azure" dla [systemów Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) i [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Narzędzia pamięci masowej

Dostęp do kont magazynu platformy Azure można uzyskać za pośrednictwem wielu różnych eksploratorów magazynu, takich jak Visual Studio Cloud Explorer. Te narzędzia umożliwiają przeglądanie kont magazynu i danych. Aby uzyskać więcej informacji i listę dostępnych eksploratorów magazynu, zobacz [Narzędzia klienta usługi Azure Storage](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Interfejs API magazynu

Zasoby magazynu są dostępne w dowolnym języku, który może tworzyć żądania HTTP/HTTPS. Dodatkowo Magazyn Azure oferuje biblioteki programistyczne dla kilku popularnych języków. Te biblioteki upraszczają pracę z usługą Azure Storage, obsługując szczegóły, takie jak wywołanie synchroniczne i asynchroniczne, przetwarzanie wsadowe operacji, zarządzanie wyjątkami i automatyczne ponownych prób. Aby uzyskać więcej informacji, zobacz [Odwołanie interfejsu API rest usługi usługi Azure Storage](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Klucze dostępu do magazynu

Każde konto magazynu ma dwa klucze uwierzytelniania, podstawowy i pomocniczy. Albo może służyć do operacji dostępu do magazynu. Te klucze magazynu są używane do zabezpieczania konta magazynu i są wymagane do programowego uzyskiwania dostępu do danych. Istnieją dwa klucze, aby umożliwić sporadyczne przerzucie kluczy w celu zwiększenia bezpieczeństwa. Ważne jest, aby zachować bezpieczeństwo kluczy, ponieważ ich posiadanie, wraz z nazwą konta, umożliwia nieograniczony dostęp do dowolnych danych na koncie magazynu.

#### <a name="shared-access-signatures"></a>Podpisy dostępu współdzielonego

Jeśli musisz zezwolić użytkownikom na kontrolowany dostęp do zasobów magazynu, możesz utworzyć podpis dostępu współdzielonego. Podpis dostępu współdzielonego to token, który można dołączyć do adresu URL, który umożliwia delegowany dostęp do zasobu magazynu. Każdy, kto posiada token może uzyskać dostęp do zasobu, który wskazuje do z uprawnieniami, które określa, na okres, który jest prawidłowy. Aby uzyskać więcej informacji, zobacz [Korzystanie z podpisów dostępu współdzielonego](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

Sieci wirtualne są niezbędne do obsługi komunikacji między maszynami wirtualnymi. Można zdefiniować podsieci, niestandardowy adres IP, ustawienia DNS, filtrowanie zabezpieczeń i równoważenie obciążenia. Platforma Azure obsługuje różne zastosowania: sieci tylko w chmurze lub hybrydowe sieci wirtualne.

### <a name="cloud-only-virtual-networks"></a>Sieci wirtualne tylko w chmurze

Domyślnie sieć wirtualna platformy Azure jest dostępna tylko dla zasobów przechowywanych na platformie Azure. Zasoby podłączone do tej samej sieci wirtualnej mogą komunikować się ze sobą. Interfejsy sieciowe maszyny wirtualnej i moduły równoważenia obciążenia można skojarzyć z publicznym adresem IP, aby maszyna wirtualna była dostępna przez Internet. Możesz pomóc w zabezpieczeniu dostępu do publicznie dostępnych zasobów przy użyciu sieciowej grupy zabezpieczeń.

![Wirtualna sieć azure dla dwuwarstwowej aplikacji sieci Web](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybrydowe sieci wirtualne

Sieć lokalną można połączyć z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute lub połączenia sieci VPN między lokacjami. W tej konfiguracji sieci wirtualnej platformy Azure jest zasadniczo rozszerzenie oparte na chmurze sieci lokalnej.

Ponieważ sieć wirtualna platformy Azure jest połączona z siecią lokalną, sieci wirtualne międzylokaturami muszą używać unikatowej części przestrzeni adresowej używanej przez organizację. W ten sam sposób, w jaki różne lokalizacje firmowe są przypisywane określonej podsieci IP, platforma Azure staje się inną lokalizacją w miarę rozszerzania sieci.
Istnieje kilka opcji wdrażania sieci wirtualnej.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [Powershell](../../virtual-network/quick-create-powershell.md)

- [Interfejs wiersza polecenia](../../virtual-network/quick-create-cli.md)

- Szablony usługi Azure Resource Manager

> **Kiedy używać:** Za każdym razem, gdy pracujesz z maszynami wirtualnymi na platformie Azure, będziesz pracować z sieciami wirtualnymi. Umożliwia to segmentowanie maszyn wirtualnych do podsieci publicznych i prywatnych podobnych lokalnych centrów danych.
>
> **Wprowadzenie:** Wdrażanie sieci wirtualnej platformy Azure przy użyciu witryny Azure portal wymaga tylko aktywnej subskrypcji platformy Azure i dostępu do przeglądarki sieci web. Nową sieć wirtualną można wdrożyć w nowej lub istniejącej grupie zasobów. Podczas tworzenia nowej maszyny wirtualnej z portalu można wybrać istniejącą sieć wirtualną lub utworzyć nową. Rozpocznij i [utwórz sieć wirtualną za pomocą portalu Azure](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Dostęp i bezpieczeństwo sieci wirtualnych

Możesz pomóc w zabezpieczeniu sieci wirtualnych platformy Azure przy użyciu sieciowej grupy zabezpieczeń. Sieciowe sieciowe sieciowe zawierają listę reguł listy kontroli dostępu (ACL), które zezwalają lub odmawiają ruchu sieciowego do wystąpień maszyn wirtualnych w sieci wirtualnej. Sieci zabezpieczeń można skojarzyć z podsieciami lub pojedynczymi wystąpieniami maszyn wirtualnych w tej podsieci. Po skojarzeniu sieciowej sieciowej z podsieci reguły listy ACL mają zastosowanie do wszystkich wystąpień maszyn wirtualnych w tej podsieci. Ponadto można dodatkowo ograniczyć ruch do pojedynczej maszyny Wirtualnej, kojarząc sieci nsg bezpośrednio z tej maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie maszyny wirtualnej z systemem Windows](../../virtual-machines/windows/quick-create-portal.md)
- [Tworzenie maszyny wirtualnej z systemem Linux](../../virtual-machines/linux/quick-create-portal.md)
