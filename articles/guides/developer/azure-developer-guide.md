---
title: Wprowadzenie — Przewodnik dla deweloperów na platformie Azure | Microsoft Docs
description: Ten temat zawiera podstawowe informacje dla deweloperów, którzy chcą rozpocząć korzystanie z platformy Microsoft Azure na potrzeby projektowania.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: 78c126559c4b919ebc81b49ed5567dd0d0196039
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971576"
---
# <a name="get-started-guide-for-azure-developers"></a>Przewodnik dla początkujących deweloperów platformy Azure

## <a name="what-is-azure"></a>Co to jest platforma Azure?

Azure to kompletna platforma w chmurze, która może obsługiwać istniejące aplikacje, usprawnia rozwój nowych aplikacji, a nawet ulepsza aplikacje lokalne. Platforma Azure integruje się z usługami w chmurze, które są potrzebne do tworzenia, testowania i wdrażania aplikacji oraz zarządzania nimi, przy jednoczesnym wykorzystaniu wydajności chmury obliczeniowej.

Hostowanie aplikacji na platformie Azure pozwala na szybkie i łatwe skalowanie aplikacji wraz ze wzrostem zapotrzebowania klientów. Platforma Azure oferuje również niezawodność, która jest wymagana w przypadku aplikacji o wysokiej dostępności, nawet w tym tryb failover między różnymi regionami. [Azure Portal](https://portal.azure.com) pozwala łatwo zarządzać wszystkimi usługami platformy Azure. Usługami można także zarządzać programowo przy użyciu interfejsów API i szablonów specyficznych dla usługi.

**Kto powinien przeczytać ten element**: Ten przewodnik zawiera wprowadzenie do platformy Azure dla deweloperów aplikacji. Zawiera ona wskazówki i kierunek, które należy zacząć tworzyć nowe aplikacje na platformie Azure lub migrować istniejące aplikacje na platformę Azure.

## <a name="where-do-i-start"></a>Od czego zacząć?

Dzięki wszystkim usługom oferowanym przez platformę Azure może być zniechęcające zadanie pozwalające ustalić, które usługi są potrzebne do obsługi architektury rozwiązania. W tej sekcji przedstawiono usługi platformy Azure powszechnie używane przez deweloperów. Aby zapoznać się z listą wszystkich usług platformy Azure, zobacz [dokumentację platformy Azure](../../index.md).

Najpierw należy zdecydować, jak hostować aplikację na platformie Azure. Czy musisz zarządzać całą infrastrukturą jako maszyną wirtualną. Czy można korzystać z funkcji zarządzania platformą udostępnianych przez platformę Azure? Być może potrzebujesz platformy bezserwerowej tylko do hostowania wykonywania kodu?

Twoja aplikacja wymaga magazynu w chmurze, w którym platforma Azure oferuje kilka opcji. Możesz skorzystać z zalet uwierzytelniania przedsiębiorstwa na platformie Azure. Istnieją także narzędzia do programowania i monitorowania w chmurze, a większość usług hostingu oferuje integrację DevOps.

Teraz przyjrzyjmy się niektórym konkretnym usługom, które zalecamy badanie aplikacji.

### <a name="application-hosting"></a>Hosting aplikacji

Platforma Azure udostępnia kilka opartych na chmurze ofert obliczeniowych do uruchamiania aplikacji, dzięki czemu nie trzeba martwić się o szczegóły infrastruktury. Zasoby można łatwo skalować w górę lub w poziomie w miarę wzrostu użycia aplikacji.

Platforma Azure oferuje usługi obsługujące potrzeby projektowania aplikacji i hostingu. Platforma Azure udostępnia infrastrukturę jako usługę (IaaS) w celu zapewnienia pełnej kontroli nad hostingiem aplikacji. Oferty platformy Azure jako usługi (PaaS) zapewniają w pełni zarządzane usługi, które są używane do włączania aplikacji. Istnieje nawet prawdziwe hosting bez serwera na platformie Azure, gdzie wszystko, co trzeba zrobić, napisanie kodu.

![Opcje hostingu aplikacji platformy Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Usługa Azure App Service 

Aby uzyskać najszybszą ścieżkę publikowania projektów opartych na sieci Web, należy rozważyć Azure App Service. App Service ułatwia rozbudowanie aplikacji sieci Web w celu obsługi klientów mobilnych i publikowanie łatwo używanych interfejsów API REST. Ta platforma zapewnia uwierzytelnianie przy użyciu dostawców społecznościowych, automatycznego skalowania opartego na ruchu, testowania w środowisku produkcyjnym oraz wdrożeń ciągłych i opartych na kontenerach.

Możesz tworzyć aplikacje sieci Web, zaplecza aplikacji mobilnych i aplikacje interfejsu API.

Ponieważ wszystkie trzy typy aplikacji współdzielą środowisko uruchomieniowe App Service, można hostować witrynę sieci Web, obsługiwać klientów mobilnych i udostępniać interfejsy API na platformie Azure, a wszystko to w tym samym projekcie lub w tym samym rozwiązaniu. Aby dowiedzieć się więcej na temat App Service, zobacz [co to jest usługa Azure Web Apps](../../app-service/overview.md).

App Service został zaprojektowany z myślą o DevOps. Obsługuje on różne narzędzia do publikowania i ciągłej integracji wdrożeń, w tym elementy webhook usługi GitHub, Jenkins, Azure DevOps, TeamCity i inne.

Istniejące aplikacje można migrować do App Service za pomocą [Narzędzia migracji online](https://www.migratetoazure.net/).

> **Kiedy używać**: Użyj App Service podczas migrowania istniejących aplikacji sieci Web na platformę Azure, gdy potrzebna jest w pełni zarządzana platforma hostingu dla aplikacji sieci Web. Możesz również użyć App Service, gdy musisz obsługiwać klientów mobilnych lub udostępniać interfejsy API REST w aplikacji.
> 
> **Rozpocznij**: App Service ułatwia tworzenie i wdrażanie pierwszej [aplikacji sieci Web](../../app-service/app-service-web-get-started-dotnet.md), [aplikacji mobilnej](../../app-service-mobile/app-service-mobile-ios-get-started.md)lub [aplikacji interfejsu API](../../app-service/app-service-web-tutorial-rest-api.md).
> 
> **Wypróbuj teraz**: App Service umożliwia obsługę krótkoterminowej aplikacji do wypróbowania platformy bez konieczności rejestrowania się w celu utworzenia konta platformy Azure. Wypróbuj platformę i [Utwórz aplikację Azure App Service](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Usługa Azure Virtual Machines

Jako dostawca infrastruktury jako usługi (IaaS), platforma Azure umożliwia wdrażanie aplikacji lub Migrowanie jej na maszyny wirtualne z systemem Windows lub Linux. Wraz z usługą Azure Virtual Network platforma Azure Virtual Machines obsługuje wdrażanie maszyn wirtualnych z systemem Windows lub Linux na platformie Azure. W przypadku maszyn wirtualnych masz całkowitą kontrolę nad konfiguracją maszyny. W przypadku korzystania z maszyn wirtualnych użytkownik jest odpowiedzialny za wszystkie instalacje oprogramowania serwera, konfigurację, konserwację i poprawki systemu operacyjnego.

Ze względu na poziom kontroli z maszynami wirtualnymi można uruchamiać szeroką gamę obciążeń serwera na platformie Azure, które nie mieszczą się w modelu PaaS. Te obciążenia obejmują serwery baz danych, Active Directory systemu Windows Server i program Microsoft SharePoint. Aby uzyskać więcej informacji, zobacz dokumentację Virtual Machines dla systemu [Linux](/azure/virtual-machines/linux/) lub [Windows](/azure/virtual-machines/windows/).

> **Kiedy używać**: Użyj Virtual Machines, gdy chcesz mieć pełną kontrolę nad infrastrukturą aplikacji lub przeprowadzić migrację lokalnych obciążeń aplikacji na platformę Azure bez konieczności wprowadzania zmian.
> 
> **Rozpocznij**: Tworzenie [maszyny wirtualnej systemu Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) lub [maszyny wirtualnej z systemem Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) na podstawie Azure Portal.

#### <a name="azure-functions-serverless"></a>Azure Functions (bezserwerowo)

Nie trzeba martwić się o tworzenie całej aplikacji lub infrastruktury do uruchamiania kodu i zarządzania nią. Co zrobić, jeśli udało Ci się napisać kod i uruchomić go w odpowiedzi na zdarzenia lub zgodnie z harmonogramem?  [Azure Functions](../../azure-functions/functions-overview.md) to oferta w stylu "bezserwerowa", która pozwala pisać tylko kod, którego potrzebujesz. Dzięki funkcjom wykonywanie kodu jest wyzwalane przez żądania HTTP, elementy webhook, zdarzenia usługi w chmurze lub zgodnie z harmonogramem. Możesz zakodować w wybranym języku programistycznym, takim jak C\#, F\#, Node. js, Python lub php. Korzystając z rozliczeń, płacisz tylko za czas wykonywania kodu, a platforma Azure skaluje się w razie potrzeby.

> **Kiedy używać**: Użyj Azure Functions, gdy masz kod, który jest wyzwalany przez inne usługi platformy Azure, za pośrednictwem zdarzeń opartych na sieci Web lub zgodnie z harmonogramem. Możesz również użyć funkcji, jeśli nie potrzebujesz nakładu pracy kompletnego hostowanego projektu lub jeśli chcesz zanieść tylko za czas działania kodu. Aby dowiedzieć się więcej, zobacz [Azure Functions Omówienie](../../azure-functions/functions-overview.md).
> 
> **Rozpocznij**: Postępuj zgodnie z samouczkiem Szybki Start, aby [utworzyć pierwszą funkcję](../../azure-functions/functions-create-first-azure-function.md) z portalu.
> 
> **Wypróbuj teraz**: Azure Functions umożliwia uruchamianie kodu bez konieczności rejestrowania się w celu uzyskania konta platformy Azure. Wypróbuj teraz i [Utwórz pierwszą funkcję platformy Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric to platforma systemów rozproszonych, która ułatwia tworzenie, pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Zapewnia również kompleksowe funkcje zarządzania aplikacjami do aprowizacji, wdrażania, monitorowania, uaktualniania/stosowania poprawek i usuwania wdrożonych aplikacji. Aplikacje, które są uruchamiane w udostępnionej puli maszyn, mogą być uruchamiane w miarę potrzeby na setkach lub tysiącach maszyn.

Service Fabric obsługuje WebAPI z otwartym interfejsem sieci Web dla platformy .NET (OWIN) i ASP.NET Core. Udostępnia zestawy SDK do kompilowania usług w systemie Linux w oprogramowaniu .NET Core i Java. Aby dowiedzieć się więcej na temat Service Fabric, zapoznaj się z [dokumentacją Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

> **Kiedy używać:** Service Fabric jest dobrym wyborem podczas tworzenia aplikacji lub zapisywania istniejącej aplikacji w celu użycia architektury mikrousług. Należy używać Service Fabric, gdy potrzebna jest większa kontrola nad podstawową infrastrukturą lub bezpośredni dostęp do niej.
> 
> **Wprowadzenie:** [Utwórz swoją pierwszą aplikację usługi Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Ulepszanie aplikacji dzięki usługom platformy Azure

Oprócz hostingu aplikacji platforma Azure oferuje oferty usług, które mogą zwiększyć funkcjonalność, rozwój i konserwację aplikacji, zarówno w chmurze, jak i lokalnie.

#### <a name="hosted-storage-and-data-access"></a>Hostowany magazyn i dostęp do danych

Większość aplikacji musi przechowywać dane, dlatego niezależnie od sposobu, w jaki użytkownik zdecyduje się na hostowanie aplikacji na platformie Azure, należy wziąć pod uwagę co najmniej jedną z następujących usług magazynu i danych.

- **Azure Cosmos DB**: Globalnie dystrybuowana, wielomodelowa usługa bazy danych, która pozwala elastycznie skalować przepływność i magazyn w dowolnej liczbie regionów geograficznych z kompleksową umową SLA. 
  > **Kiedy używać:** Gdy aplikacja wymaga baz danych dokumentów, tabel lub grafów, w tym baz danych MongoDB, z wieloma dobrze zdefiniowanymi modelami spójności. 
  > 
  > **Rozpocznij**: [Tworzenie aplikacji sieci web Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Jeśli jesteś deweloperem programu MongoDB, zobacz [Tworzenie aplikacji sieci Web MongoDB za pomocą Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure Storage**: Oferuje trwały magazyn o wysokiej dostępności dla obiektów blob, kolejek, plików i innych rodzajów danych nierelacyjnych. Magazyn udostępnia podstawę magazynu dla maszyn wirtualnych.

  > **Kiedy używać**: Gdy aplikacja przechowuje dane nierelacyjne, takie jak pary klucz-wartość (tabele), obiekty blob, udziały plików lub komunikaty (kolejki).
  > 
  > **Rozpocznij**: Wybierz jeden z następujących typów magazynów: [obiekty blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabele](../../cosmos-db/table-storage-how-to-use-dotnet.md), [kolejki](../../storage/queues/storage-dotnet-how-to-use-queues.md)lub [pliki](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: Oparta na platformie Azure wersja aparatu Microsoft SQL Server do przechowywania relacyjnych danych tabelarycznych w chmurze. SQL Database zapewnia przewidywalną wydajność, skalowalność bez przestojów, ciągłość działania i ochronę danych.

  > **Kiedy używać**: Gdy aplikacja wymaga magazynu danych z integralnością referencyjną, obsługą transakcyjną i obsługą zapytań TSQL.
  > 
  > **Rozpocznij**: [Utwórz bazę danych SQL w ciągu kilku minut, używając Azure Portal](../../sql-database/sql-database-get-started.md).


Za pomocą [Azure Data Factory](../../data-factory/introduction.md) można przenieść istniejące dane lokalne na platformę Azure. Jeśli nie jesteś gotowy do przenoszenia danych do chmury, [połączenia hybrydowe](../../biztalk-services/integration-hybrid-connection-overview.md) w BizTalk Services umożliwia połączenie App Service hostowanej aplikacji z zasobami lokalnymi. Możesz również nawiązać połączenie z usługami Azure Data and Storage z aplikacji lokalnych.

#### <a name="docker-support"></a>Obsługa platformy Docker

Kontenery platformy Docker, forma wirtualizacji systemu operacyjnego, umożliwiają wdrażanie aplikacji w bardziej wydajny i przewidywalny sposób. Aplikacja kontenera działa w taki sam sposób jak w systemach deweloperskich i testowych. Kontenerami można zarządzać za pomocą standardowych narzędzi platformy Docker. Korzystając z posiadanych umiejętności i popularnych narzędzi open source, można wdrażać aplikacje oparte na kontenerach i zarządzać nimi na platformie Azure.

Platforma Azure oferuje kilka sposobów korzystania z kontenerów w aplikacjach.

- **Rozszerzenie maszyny wirtualnej platformy Azure Docker**: Umożliwia skonfigurowanie maszyny wirtualnej za pomocą narzędzi platformy Docker do działania jako host platformy Docker.

  > **Kiedy używać**: Gdy chcesz generować spójne wdrożenia kontenerów dla aplikacji na maszynie wirtualnej lub gdy chcesz używać [Docker Compose](https://docs.docker.com/compose/overview/).
  > 
  > **Rozpocznij**: [Utwórz środowisko platformy Docker na platformie Azure przy użyciu rozszerzenia maszyny wirtualnej](../../virtual-machines/virtual-machines-linux-dockerextension.md)platformy Docker.

- **Azure Container Service**: Umożliwia tworzenie i Konfigurowanie klastra maszyn wirtualnych, które są wstępnie skonfigurowane do uruchamiania aplikacji kontenera, oraz zarządzanie nim. Aby dowiedzieć się więcej o usłudze Container Service, zobacz [wprowadzenie Azure Container Service](../../container-service/container-service-intro.md).

  > **Kiedy używać**: Gdy trzeba utworzyć gotowe do użycia środowisko produkcyjne, które zapewnia dodatkowe narzędzia do planowania i zarządzania, lub gdy wdrażasz klaster Docker Swarm.
  > 
  > **Rozpocznij**: [Wdróż klaster usługi kontenera](../../container-service/dcos-swarm/container-service-deployment.md).

- **Maszyna platformy Docker**: Umożliwia zainstalowanie aparatu platformy Docker na hostach wirtualnych i zarządzanie nim przy użyciu poleceń platformy Docker-Machine.

  >**Kiedy używać**: Gdy musisz szybko utworzyć prototyp aplikacji, tworząc pojedynczy Host platformy Docker.

- **Niestandardowy obraz platformy Docker dla App Service**: Umożliwia korzystanie z kontenerów platformy Docker z rejestru kontenerów lub kontenera klienta podczas wdrażania aplikacji sieci Web w systemie Linux.

  > **Kiedy używać**: Podczas wdrażania aplikacji sieci Web w systemie Linux do obrazu platformy Docker.
  > 
  > **Rozpocznij**: [Użyj niestandardowego obrazu platformy Docker dla App Service w systemie Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

Niezwykle ważne jest, aby nie tylko wiedzieć, kto korzysta z aplikacji, ale również aby uniemożliwić nieautoryzowany dostęp do zasobów. Platforma Azure oferuje kilka metod uwierzytelniania klientów aplikacji.

- **Azure Active Directory (Azure AD)** : Wielodostępna usługa zarządzania tożsamościami i dostępem w chmurze firmy Microsoft. Możesz dodać Logowanie jednokrotne (SSO) do aplikacji przez integrację z usługą Azure AD. Dostęp do właściwości katalogu można uzyskać za pomocą interfejs API programu Graph usługi Azure AD bezpośrednio lub Microsoft Graph interfejsu API. Program można zintegrować z obsługą usługi Azure AD w ramach platformy autoryzacji OAuth 2.0 oraz z usługą Open ID Connect przy użyciu natywnych punktów końcowych HTTP/REST i wieloplatformowych bibliotek uwierzytelniania usługi Azure AD.

  > **Kiedy używać**: Aby zapewnić obsługę logowania jednokrotnego, należy korzystać z danych opartych na grafach lub uwierzytelniać użytkowników opartych na domenie.
  > 
  > **Rozpocznij**: Aby dowiedzieć się więcej, zobacz [przewodnik dewelopera Azure Active Directory](../../active-directory/develop/v1-overview.md).

- **Uwierzytelnianie App Service**: W przypadku wybrania App Service do hostowania aplikacji uzyskasz również wbudowaną obsługę uwierzytelniania dla usługi Azure AD oraz dostawców tożsamości społecznościowych, takich jak Facebook, Google, Microsoft i Twitter.

  > **Kiedy używać**: Jeśli chcesz włączyć uwierzytelnianie w aplikacji App Service przy użyciu usługi Azure AD, dostawców tożsamości społecznościowych lub obu tych programów.
  > 
  > **Rozpocznij**: Aby dowiedzieć się więcej o uwierzytelnianiu w App Service, zobacz [uwierzytelnianie i autoryzacja w Azure App Service](../../app-service/overview-authentication-authorization.md).

Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń na platformie Azure, zobacz [najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy Azure](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorowanie

Gdy aplikacja działa na platformie Azure, musisz mieć możliwość monitorowania wydajności, śledzenia problemów i sprawdzenia, jak klienci korzystają z aplikacji. Platforma Azure oferuje kilka opcji monitorowania.

-   **Application Insights programu Visual Studio**: Hostowana na platformie Azure usługa analizy rozszerzalnej zintegrowana z programem Visual Studio do monitorowania aktywnych aplikacji sieci Web. Zapewnia to dane, które są potrzebne do ciągłego ulepszania wydajności i użyteczności aplikacji, niezależnie od tego, czy są one hostowane na platformie Azure, czy nie.

    >**Rozpocznij**: Postępuj zgodnie z [samouczkiem Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure monitor**: Usługa, która ułatwia wizualizowanie, wykonywanie zapytań, kierowanie i archiwizowanie metryk i dzienników generowanych przez infrastrukturę i zasoby platformy Azure. Monitor udostępnia widoki danych widoczne w Azure Portal i jest jednym źródłem do monitorowania zasobów platformy Azure.
 
    >**Rozpocznij**: [Rozpocznij pracę z Azure monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integracja metodyki DevOps

Niezależnie od tego, czy udostępniasz maszyny wirtualne, czy publikujesz aplikacje sieci Web z ciągłą integracją, platforma Azure integruje się z większością popularnych narzędzi DevOps. Dzięki obsłudze narzędzi takich jak Jenkins, GitHub, Puppet, Chef, TeamCity, rozwiązania ansible, Azure DevOps i innych, możesz korzystać z narzędzi, które już masz i maksymalizują istniejące środowisko.

> **Wypróbuj teraz:** [Wypróbuj kilka integracji DevOps](https://azure.microsoft.com/try/devops/).
> 
> **Rozpocznij**: Aby wyświetlić opcje DevOps aplikacji App Service, zobacz [ciągłe wdrażanie do Azure App Service](../../app-service/deploy-continuous-deployment.md).


## <a name="azure-regions"></a>Regiony platformy Azure

Azure to globalna platforma w chmurze, która jest ogólnie dostępna w wielu regionach na całym świecie. Podczas aprowizacji usługi, aplikacji lub maszyny wirtualnej na platformie Azure zostanie wyświetlony monit o wybranie regionu, który reprezentuje konkretne centrum danych, w którym jest uruchomiona aplikacja lub gdzie przechowywane są dane. Regiony te odpowiadają konkretnym lokalizacjom publikowanym na stronie [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/) .

### <a name="choose-the-best-region-for-your-application-and-data"></a>Wybieranie najlepszego regionu dla aplikacji i danych

Jedną z zalet korzystania z platformy Azure jest możliwość wdrażania aplikacji w różnych centrach danych na całym świecie. Wybrany region może mieć wpływ na wydajność aplikacji. Na przykład lepiej jest wybrać region, który jest bliżej większości klientów, aby zmniejszyć opóźnienia w żądaniach sieci. Możesz również wybrać region spełniający wymagania prawne dotyczące dystrybucji aplikacji w określonych krajach/regionach. Najlepszym rozwiązaniem jest przechowywanie w tym samym centrum danych aplikacji, które znajdują się na tym samym serwerze, w tym samym, jak to możliwe, w tym samym obszarze, w którym znajduje się aplikacja.

### <a name="multi-region-apps"></a>Aplikacje wieloregionowe

Chociaż jest to mało prawdopodobne, nie jest możliwe, aby całe centrum danych przeszedł do trybu offline z powodu zdarzenia, takiego jak klęska żywiołowa lub błąd internetowy. Najlepszym rozwiązaniem jest hostowanie ważnych aplikacji firmowych w więcej niż jednym centrum danych w celu zapewnienia maksymalnej dostępności. Korzystanie z wielu regionów może również ograniczyć opóźnienia dla użytkowników globalnych i zapewnić dodatkowe możliwości w zakresie elastyczności podczas aktualizowania aplikacji.

W przypadku niektórych usług, takich jak maszyna wirtualna i App Services, użyj [usługi Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) , aby umożliwić obsługę wieloregionowej pracy w trybie failover między regionami w celu obsługi aplikacji dla przedsiębiorstw o wysokiej dostępności. Aby zapoznać się z przykładem, zobacz [architektura referencyjna platformy Azure: Uruchom aplikację sieci Web w wielu regionach](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Kiedy używać**: Jeśli masz aplikacje korporacyjne i o wysokiej dostępności, które korzystają z trybu failover i replikacji.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Jak mogę zarządzać moimi aplikacjami i projektami?

Platforma Azure oferuje bogaty zestaw środowisk umożliwiających tworzenie zasobów platformy Azure, aplikacji i projektów oraz zarządzanie nimi — zarówno programowo, jak i w [Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfejsy wiersza polecenia i program PowerShell

Platforma Azure oferuje dwa sposoby zarządzania aplikacjami i usługami z poziomu wiersza polecenia przy użyciu bash, terminalu, wiersza polecenia lub narzędzia wiersza polecenia. Zazwyczaj można wykonać te same zadania z poziomu wiersza polecenia, tak jak w Azure Portal, takich jak tworzenie i Konfigurowanie maszyn wirtualnych, sieci wirtualnych, aplikacji sieci Web i innych usług.

-   [Interfejs wiersza polecenia platformy Azure (CLI)](../../xplat-cli-install.md): Pozwala połączyć się z subskrypcją platformy Azure i programować różnymi zadaniami z zasobami platformy Azure z wiersza polecenia.

-   [Azure PowerShell](../../powershell-install-configure.md): Zawiera zestaw modułów z poleceniami cmdlet, które umożliwiają zarządzanie zasobami platformy Azure przy użyciu programu Windows PowerShell.

### <a name="azure-portal"></a>Azure Portal

Azure Portal to aplikacja oparta na sieci Web, za pomocą której można tworzyć i usuwać zasoby i usługi platformy Azure oraz zarządzać nimi. Azure Portal znajduje się w lokalizacji <https://portal.azure.com>. Obejmuje to dostosowywalny pulpit nawigacyjny, narzędzia do zarządzania zasobami platformy Azure oraz dostęp do ustawień subskrypcji i informacji dotyczących rozliczeń. Aby uzyskać więcej informacji, zobacz [omówienie Azure Portal](../../azure-portal-overview.md).

### <a name="rest-apis"></a>Interfejsy API REST

Platforma Azure jest oparta na zestawie interfejsów API REST, które obsługują interfejs użytkownika Azure Portal. Większość z tych interfejsów API REST jest również obsługiwana, aby umożliwić programistyczne Inicjowanie obsługi zasobów i aplikacji platformy Azure oraz zarządzanie nimi z dowolnego urządzenia z obsługą Internetu. Aby zapoznać się z kompletnym zestawem dokumentacji interfejsu API REST, zobacz Dokumentacja [zestawu SDK REST platformy Azure](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>interfejsy API

Oprócz interfejsów API REST wiele usług platformy Azure umożliwia również programistyczne zarządzanie zasobami z aplikacji przy użyciu zestawów SDK platformy Azure specyficznych dla platformy, w tym zestawów SDK dla następujących platform deweloperskich:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

Usługi, takie jak [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) i [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) udostępniają zestawy SDK po stronie klienta, aby umożliwić dostęp do usług z aplikacji klienckich dla sieci Web i urządzeń przenośnych.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Uruchomienie aplikacji na platformie Azure może wymagać pracy z wieloma usługami platformy Azure, z których wszystkie są zgodne z tym samym cyklem życia i mogą być traktowane jako jednostki logiczne. Na przykład aplikacja sieci Web może używać Web Apps, SQL Database, magazynu, pamięci podręcznej platformy Azure dla Redis i usług Azure Content Delivery Network. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) pozwala na współpracę z zasobami w aplikacji jako grupą. Można wdrożyć, zaktualizować lub usunąć wszystkie zasoby w jednej, skoordynowanej operacji.

Oprócz logicznego grupowania i zarządzania powiązanymi zasobami Azure Resource Manager obejmuje możliwości wdrażania umożliwiające dostosowanie wdrożenia i konfiguracji powiązanych zasobów. Na przykład za pomocą Menedżer zasobów można wdrożyć i skonfigurować aplikację, która składa się z wielu maszyn wirtualnych, modułu równoważenia obciążenia i bazy danych SQL Azure jako pojedynczej jednostki.

Te wdrożenia są opracowywane przy użyciu szablonu Azure Resource Manager, który jest dokumentem w formacie JSON. Szablony umożliwiają zdefiniowanie wdrożenia i zarządzanie aplikacjami przy użyciu szablonów deklaratywnych zamiast skryptów. Szablony mogą współpracować z różnymi środowiskami, takimi jak testowanie, przemieszczanie i produkcja. Na przykład za pomocą szablonów można dodać przycisk do repozytorium GitHub, które wdraża kod w repozytorium w ramach zestawu usług platformy Azure za pomocą jednego kliknięcia.

> **Kiedy używać**: Użyj szablonów Menedżer zasobów, jeśli chcesz, aby wdrożenie oparte na szablonie dla aplikacji było zarządzane programowo przy użyciu interfejsów API REST, interfejsu wiersza polecenia platformy Azure i Azure PowerShell.
> 
> **Rozpocznij**: Aby rozpocząć korzystanie z szablonów, zobacz [Tworzenie szablonów Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Informacje na temat kont, subskrypcji i rozliczeń

Jako deweloperzy chcemy szczegółowe bezpośrednio do kodu i próbować szybko rozpocząć pracę, jak to możliwe dzięki uruchomieniu naszych aplikacji. Mamy na pewno zachęcamy do rozpoczęcia pracy na platformie Azure tak szybko, jak to możliwe. Aby ułatwić korzystanie z tej funkcji, platforma Azure oferuje [bezpłatną wersję próbną](https://azure.microsoft.com/free/). Niektóre usługi mają nawet funkcje "Wypróbuj bezpłatnie", takie jak [Azure App Service](https://tryappservice.azure.com/), które nie wymagają nawet utworzenia konta. Tak długo, jak szczegółowe się na kodowanie i wdrażanie aplikacji na platformie Azure, ważne jest również, aby zrozumieć, jak platforma Azure współpracuje z punktu widzenia kont użytkowników, subskrypcji i rozliczeń.

### <a name="what-is-an-azure-account"></a>Co to jest konto platformy Azure?

Aby można było utworzyć subskrypcję platformy Azure lub korzystać z niej, musisz mieć konto platformy Azure. Konto platformy Azure jest po prostu tożsamością w usłudze Azure AD lub w katalogu, takim jak organizacja służbowa, która jest zaufana przez usługę Azure AD. Jeśli nie należysz do takiej organizacji, możesz zawsze utworzyć subskrypcję za pomocą konta Microsoft, które jest zaufane przez usługę Azure AD. Aby dowiedzieć się więcej o integrowaniu lokalnych Active Directory systemu Windows Server z usługą Azure AD, zobacz [integrowanie tożsamości lokalnych z Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Każda subskrypcja platformy Azure jest połączona relacją zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Aby dowiedzieć się więcej, zobacz [jak subskrypcje platformy Azure są skojarzone z Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Oprócz definiowania pojedynczych tożsamości kont platformy Azure, nazywanych również *użytkownikami*, można także definiować *grupy* w usłudze Azure AD. Tworzenie grup użytkowników jest dobrym sposobem na zarządzanie dostępem do zasobów w ramach subskrypcji przy użyciu kontroli dostępu opartej na rolach (RBAC). Aby dowiedzieć się, jak tworzyć grupy, zobacz [Tworzenie grupy w programie Azure Active Directory Preview](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Można także tworzyć grupy i zarządzać nimi za [pomocą programu PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Zarządzaj subskrypcjami

Subskrypcja to logiczna Grupa usług platformy Azure, która jest połączona z kontem platformy Azure. Pojedyncze konto platformy Azure może zawierać wiele subskrypcji. Rozliczanie usług platformy Azure odbywa się na podstawie subskrypcji. Aby uzyskać listę dostępnych ofert subskrypcji według typu, zobacz [Microsoft Azure Szczegóły oferty](https://azure.microsoft.com/support/legal/offer-details/). Subskrypcje platformy Azure mają administratora konta, który ma pełną kontrolę nad subskrypcją, oraz administratora usługi, który ma kontrolę nad wszystkimi usługami w ramach subskrypcji. Aby uzyskać informacje na temat klasycznych administratorów subskrypcji, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Oprócz administratorów do poszczególnych kont można udzielić szczegółowej kontroli nad zasobami platformy Azure przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Grupy zasobów

W przypadku inicjowania obsługi administracyjnej nowych usług platformy Azure należy to zrobić w ramach danej subskrypcji. Poszczególne usługi platformy Azure, które są nazywane również zasobami, są tworzone w kontekście grupy zasobów. Grupy zasobów ułatwiają wdrażanie zasobów aplikacji i zarządzanie nimi. Grupa zasobów powinna zawierać wszystkie zasoby dla aplikacji, które mają być używane jako jednostka. Zasoby można przenosić między grupami zasobów, a nawet w różnych subskrypcjach. Aby dowiedzieć się więcej o przenoszeniu zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../resource-group-move-resources.md).

Azure Resource Explorer to doskonałe narzędzie do wizualizacji zasobów, które zostały już utworzone w ramach subskrypcji. Aby dowiedzieć się więcej, zobacz [używanie Azure Resource Explorer do wyświetlania i modyfikowania zasobów](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Udzielanie dostępu do zasobów

Gdy zezwolisz na dostęp do zasobów platformy Azure, zawsze najlepszym rozwiązaniem jest zapewnienie użytkownikom najniższych uprawnień, które są wymagane do wykonania danego zadania.

- **Kontrola dostępu oparta na rolach (RBAC)** : Na platformie Azure można udzielić dostępu do kont użytkowników (podmiotów zabezpieczeń) w określonym zakresie: subskrypcji, grupy zasobów lub poszczególnych zasobów. RBAC pozwala wdrożyć zestaw zasobów w grupie zasobów i przyznać uprawnienia do określonego użytkownika lub grupy. Pozwala również ograniczyć dostęp tylko do zasobów należących do docelowej grupy zasobów. Możesz również udzielić dostępu do pojedynczego zasobu, na przykład maszyny wirtualnej lub sieci wirtualnej. Aby udzielić dostępu, należy przypisać rolę do użytkownika, grupy lub nazwy głównej usługi. Istnieje wiele wstępnie zdefiniowanych ról i można także definiować własne role niestandardowe. Aby dowiedzieć się więcej, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC)?](../../role-based-access-control/overview.md)

  > **Kiedy używać**: Jeśli potrzebujesz szczegółowego zarządzania dostępem dla użytkowników i grup lub chcesz, aby użytkownik był właścicielem subskrypcji.
  > 
  > **Rozpocznij**: Aby dowiedzieć się więcej, zobacz [Zarządzanie dostępem przy użyciu RBAC i Azure Portal](../../role-based-access-control/role-assignments-portal.md).

- **Obiekty główne usługi**: Oprócz zapewniania dostępu do podmiotów zabezpieczeń i grup użytkowników można przyznać ten sam dostęp do jednostki usługi.

  > **Kiedy używać**: W przypadku programistycznego zarządzania zasobami platformy Azure lub przyznawania dostępu do aplikacji. Aby uzyskać więcej informacji, zobacz [tworzenie Active Directory aplikacji i nazwy głównej usługi](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>`Tags`

Azure Resource Manager pozwala przypisywać niestandardowe znaczniki do poszczególnych zasobów. Tagi, które są parami klucz-wartość, mogą być przydatne, gdy trzeba zorganizować zasoby na potrzeby rozliczeń lub monitorowania. Tagi umożliwiają śledzenie zasobów między wieloma grupami zasobów. Możesz przypisywać Tagi w portalu, w szablonie Azure Resource Manager lub programowo przy użyciu interfejsu API REST, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Do każdego zasobu można przypisać wiele tagów. Aby dowiedzieć się więcej, zobacz [Używanie tagów do organizowania zasobów platformy Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Rozliczenia

W przypadku przenoszenia z zasobów lokalnych do usług hostowanych w chmurze śledzenie i ocenianie użycia usługi oraz kosztów pokrewnych ma duże znaczenie. Ważne jest, aby można było oszacować, jakie nowe zasoby są uruchamiane co miesiąc. Musisz również mieć możliwość projektu, w jaki sposób rozliczenia są sprawdzane w danym miesiącu, na podstawie bieżących wydatków.

#### <a name="get-resource-usage-data"></a>Pobierz dane użycia zasobów

Platforma Azure udostępnia zestaw interfejsów API REST rozliczeń, które zapewniają dostęp do zasobów i informacji o metadanych dla subskrypcji platformy Azure. Te interfejsy API rozliczeń zapewniają możliwość lepszego przewidywania kosztów platformy Azure i zarządzania nimi. Możesz śledzić i analizować wydatki w przyrostach godzinowych, tworzyć alerty wydatków i przewidywania przyszłych rozliczeń na podstawie bieżących trendów użycia.

>**Rozpocznij**: Aby dowiedzieć się więcej o korzystaniu z interfejsy API rozliczeń, zobacz [Omówienie interfejsów API usługi Azure rozliczeń i RateCard](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Przewidywanie przyszłych kosztów

Chociaż jest trudne do oszacowania kosztów z wyprzedzeniem, platforma Azure ma [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) , którego można użyć w przypadku oszacowania kosztów wdrożonych zasobów. Możesz również użyć bloku rozliczenia w portalu i interfejsów API REST rozliczeń, aby oszacować przyszłe koszty na podstawie bieżącego zużycia.

>**Rozpocznij**: Zobacz [Omówienie użycia rozliczeń platformy Azure i interfejsów API RateCard](../../billing-usage-rate-card-overview.md).
