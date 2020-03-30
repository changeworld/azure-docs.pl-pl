---
title: Przewodnik dla deweloperów na platformie Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera podstawowe informacje dla deweloperów, którzy chcą rozpocząć korzystanie z platformy Microsoft Azure dla ich potrzeb programistycznych.
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
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: d74fd2e3f6b5cc090c6313aba67a1e139c713b85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245007"
---
# <a name="get-started-guide-for-azure-developers"></a>Przewodnik po rozpoczęciu pracy dla deweloperów platformy Azure

## <a name="what-is-azure"></a>Czym jest platforma Azure?

Platforma Azure to kompletna platforma w chmurze, która może obsługiwać istniejące aplikacje i usprawniać tworzenie nowych aplikacji. Platforma Azure może nawet ulepszać aplikacje lokalne. Platforma Azure integruje usługi w chmurze, które są potrzebne do tworzenia, testowania, wdrażania i zarządzania aplikacjami, a jednocześnie wykorzystuje wydajność przetwarzania w chmurze.

Obsługując aplikacje na platformie Azure, można rozpocząć małe i łatwe skalowanie aplikacji w miarę wzrostu zapotrzebowania klientów. Platforma Azure oferuje również niezawodność, która jest potrzebna dla aplikacji o wysokiej dostępności, nawet w tym pracy awaryjnej między różnymi regionami. Portal [Azure](https://portal.azure.com) umożliwia łatwe zarządzanie wszystkimi usługami platformy Azure. Usługi można również programowo zarządzać za pomocą interfejsów API specyficznych dla usługi i szablonów.

Ten przewodnik jest wprowadzeniem do platformy Azure dla deweloperów aplikacji. Zawiera wskazówki i kierunek, który należy rozpocząć tworzenie nowych aplikacji na platformie Azure lub migracji istniejących aplikacji na platformę Azure.

## <a name="where-do-i-start"></a>Od czego zacząć?

Ze wszystkimi usługami, które oferuje platforma Azure, może to być zastraszające zadanie, aby dowiedzieć się, które usługi są potrzebne do obsługi architektury rozwiązania. W tej sekcji przedstawiono usługi platformy Azure, które deweloperzy często używają. Aby uzyskać listę wszystkich usług platformy Azure, zobacz [dokumentację platformy Azure](../../index.yml).

Najpierw należy zdecydować, jak hostować aplikację na platformie Azure. Czy musisz zarządzać całą infrastrukturą jako maszyną wirtualną (VM). Czy można korzystać z usług zarządzania platformą, które zapewnia platforma Azure? Może potrzebujesz struktury bezserwerowej do hosta tylko wykonanie kodu?

Aplikacja wymaga magazynu w chmurze, dla którego platforma Azure udostępnia kilka opcji. Możesz skorzystać z uwierzytelniania korporacyjnego platformy Azure. Istnieją również narzędzia do tworzenia i monitorowania w chmurze, a większość usług hostingowych oferuje integrację DevOps.

Teraz przyjrzyjmy się niektórym określonym usługom, które zalecamy zbadanie twoich aplikacji.

### <a name="application-hosting"></a>Hosting aplikacji

Platforma Azure oferuje kilka ofert obliczeniowych opartych na chmurze do uruchamiania aplikacji, dzięki czemu nie musisz się martwić o szczegóły infrastruktury. Można łatwo skalować w górę lub skalować w poziomie zasobów w miarę wzrostu użycia aplikacji.

Platforma Azure oferuje usługi, które obsługują potrzeby w zakresie tworzenia i hostingu aplikacji. Platforma Azure udostępnia infrastrukturę jako usługę (IaaS), aby zapewnić pełną kontrolę nad hostingiem aplikacji. Oferty platformy Azure Platform as a Service (PaaS) zapewniają w pełni zarządzane usługi potrzebne do zasilania aplikacji. Istnieje nawet prawdziwy hosting bezserwerowy na platformie Azure, gdzie wszystko, co musisz zrobić, to napisać kod.

![Opcje hostingu aplikacji platformy Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service

Jeśli chcesz, aby najszybsza ścieżka do publikowania projektów opartych na sieci Web, należy wziąć pod uwagę usługi Azure App Service. Usługa App Service ułatwia rozszerzanie aplikacji internetowych w celu obsługi klientów mobilnych i publikowania łatwo zużywanych interfejsów API REST. Ta platforma zapewnia uwierzytelnianie przy użyciu dostawców społecznościowych, skalowanie automatyczne oparte na ruchu drogowym, testowanie w produkcji oraz ciągłe i oparte na kontenerach wdrożenia.

Możesz tworzyć aplikacje internetowe, tylne końce aplikacji mobilnych i aplikacje interfejsu API.

Ponieważ wszystkie trzy typy aplikacji współużytkują środowisko uruchomieniowe usługi App Service, można hostować witrynę sieci Web, obsługiwać klientów mobilnych i udostępniać interfejsy API na platformie Azure, wszystkie z tego samego projektu lub rozwiązania. Aby dowiedzieć się więcej o usłudze App Service, zobacz [Co to jest usługa Azure Web Apps](../../app-service/overview.md).

Usługa App Service została zaprojektowana z myślą o DevOps. Obsługuje różne narzędzia do publikowania i ciągłej integracji wdrożeń. Narzędzia te obejmują elementów webhook GitHub, Jenkins, Azure DevOps, TeamCity i innych.

Istniejące aplikacje można migrować do usługi App Service za pomocą [narzędzia migracji online](https://appmigration.microsoft.com/).

> **Kiedy używać:** Użyj usługi App Service podczas migracji istniejących aplikacji sieci web na platformę Azure i gdy potrzebujesz w pełni zarządzanej platformy hostingowej dla aplikacji sieci web. Usługi App Service można również używać, gdy trzeba obsługiwać klientów mobilnych lub uwidaczniać interfejsy API REST za pomocą aplikacji.
>
> **Wprowadzenie:** Usługa App Service ułatwia tworzenie i wdrażanie pierwszej [aplikacji internetowej,](../../app-service/app-service-web-get-started-dotnet.md) [aplikacji mobilnej](../../app-service-mobile/app-service-mobile-ios-get-started.md)lub [aplikacji interfejsu API.](../../app-service/app-service-web-tutorial-rest-api.md)
>
> **Wypróbuj teraz:** Usługa App Service umożliwia aprowizowanie aplikacji krótkotrwałej, aby wypróbować platformę bez konieczności tworzenia konta platformy Azure. Wypróbuj platformę i [utwórz aplikację usługi Azure App Service](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Jako dostawca infrastruktury jako usługi (IaaS) platforma Azure umożliwia wdrażanie lub migrację aplikacji na maszyny wirtualne z systemem Windows lub Linux. Wraz z usługą Azure Virtual Network maszyny wirtualne platformy Azure obsługują wdrażanie maszyn wirtualnych z systemem Windows lub Linux na platformie Azure. W maszynach wirtualnych masz całkowitą kontrolę nad konfiguracją maszyny. Podczas korzystania z maszyn wirtualnych, jesteś odpowiedzialny za wszystkie poprawki oprogramowania serwera, konfiguracji, konserwacji i systemu operacyjnego.

Ze względu na poziom kontroli, który masz z maszynami wirtualnymi, można uruchomić szeroki zakres obciążeń serwera na platformie Azure, które nie pasują do modelu PaaS. Obciążenia te obejmują serwery baz danych, usługę Active Directory systemu Windows Server i program Microsoft SharePoint. Aby uzyskać więcej informacji, zobacz dokumentację maszyn wirtualnych dla [systemu Linux](/azure/virtual-machines/linux/) lub [Windows](/azure/virtual-machines/windows/).

> **Kiedy używać:** Użyj maszyn wirtualnych, gdy chcesz mieć pełną kontrolę nad infrastrukturą aplikacji lub do migracji lokalnych obciążeń aplikacji na platformę Azure bez konieczności wprowadzania zmian.
>
> **Wprowadzenie:** Tworzenie [maszyny Wirtualnej systemu Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) lub [maszyny Wirtualnej systemu Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) z witryny Azure portal.

#### <a name="azure-functions-serverless"></a>Usługi Azure Functions (bezserwerowe)

Zamiast martwić się o tworzenie i zarządzanie całą aplikacją lub infrastrukturą do uruchomienia kodu, co zrobić, jeśli można po prostu napisać kod i uruchomić go w odpowiedzi na zdarzenia lub zgodnie z harmonogramem?  [Usługa Azure Functions](../../azure-functions/functions-overview.md) to oferta w stylu "bezserwerowym", która umożliwia pisanie tylko kodu, którego potrzebujesz. Za pomocą funkcji można wyzwolić wykonanie kodu za pomocą żądań HTTP, elementów webhook, zdarzeń usługi w chmurze lub zgodnie z harmonogramem. Możesz kodować w wybranym języku programowania, takim jak C\#, F\#, Node.js, Python lub PHP. W przypadku rozliczeń opartych na zużyciu płacisz tylko za czas wykonywania kodu, a platforma Azure jest skalowana zgodnie z potrzebami.

> **Kiedy używać:** Użyj usługi Azure Functions, gdy masz kod, który jest wyzwalany przez inne usługi platformy Azure, przez zdarzenia oparte na sieci Web lub zgodnie z harmonogramem. Funkcji można również użyć, gdy nie potrzebujesz obciążenie całego hostowanego projektu lub gdy chcesz zapłacić tylko za czas, który uruchamia kod. Aby dowiedzieć się więcej, zobacz [Omówienie usług Azure Functions](../../azure-functions/functions-overview.md).
>
> **Wprowadzenie:** Postępuj zgodnie z funkcjami szybki start samouczek, aby [utworzyć pierwszą funkcję](../../azure-functions/functions-create-first-azure-function.md) z portalu.
>
> **Wypróbuj teraz:** usługa Azure Functions umożliwia uruchamianie kodu bez konieczności konfigurowania konta platformy Azure. Wypróbuj go teraz i [utwórz pierwszą funkcję platformy Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Usługa Azure Service Fabric to platforma systemów rozproszonych. Ta platforma ułatwia tworzenie, pakowanie, wdrażanie i zarządzanie skalowalnymi i niezawodnymi mikrousługami. Zapewnia również kompleksowe możliwości zarządzania aplikacjami, takie jak:

* Inicjowanie obsługi
* Wdrażanie
* Monitorowanie
* Uaktualnianie/łatanie
* Usuwanie

Aplikacje, które działają na wspólnej puli maszyn, można uruchomić małe i skalowane do setek lub tysięcy maszyn w razie potrzeby.

Sieć szkieletowa usług obsługuje interfejs WebAPI z interfejsem open web dla platformy .NET (OWIN) i ASP.NET Core. Zapewnia sdk do tworzenia usług w systemie Linux zarówno w .NET Core i Java. Aby dowiedzieć się więcej o sieci szkieletowej usług, zobacz [dokumentację sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/).

> **Kiedy stosować:** Sieci szkieletowej usług jest dobrym wyborem podczas tworzenia aplikacji lub przepisywania istniejącej aplikacji do korzystania z architektury mikrousług. Użyj sieci szkieletowej usług, gdy potrzebujesz większej kontroli nad podstawową infrastrukturą lub do niej ma bezpośredni dostęp.
>
> **Wprowadzenie:** [utwórz pierwszą aplikację sieci szkieletowej usług Azure](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Ulepszanie aplikacji dzięki usługom platformy Azure

Wraz z hostingiem aplikacji platforma Azure oferuje oferty usług, które mogą zwiększyć funkcjonalność. Platforma Azure może również usprawnić tworzenie i konserwację aplikacji, zarówno w chmurze, jak i lokalnie.

#### <a name="hosted-storage-and-data-access"></a>Hostowana pamięć masowa i dostęp do danych

Większość aplikacji musi przechowywać dane, więc jednak zdecydujesz się hostować aplikację na platformie Azure, należy wziąć pod uwagę co najmniej jedną z następujących usług magazynu i danych.

- **Usługa Azure Cosmos DB:** globalnie rozproszona usługa bazy danych z wieloma modelami. Ta baza danych umożliwia elastyczne skalowanie przepływności i magazynu w dowolnej liczbie regionów geograficznych za pomocą kompleksowej umowy SLA.

  > **Kiedy stosować:** Gdy aplikacja potrzebuje bazy danych dokumentów, tabel lub wykresów, w tym baz danych MongoDB, z wieloma dobrze zdefiniowanymi modelami spójności.
  >
  > **Wprowadzenie:** [Tworzenie aplikacji sieci Web usługi Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Jeśli jesteś deweloperem MongoDB, zobacz [Tworzenie aplikacji sieci Web MongoDB za pomocą usługi Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Usługa Azure Storage:** oferuje trwałe, wysokiej dostępności magazynu dla obiektów blob, kolejek, plików i innych rodzajów danych niezwiązanych. Magazyn zapewnia podstawę magazynu dla maszyn wirtualnych.

  > **Kiedy używać:** Gdy aplikacja przechowuje dane nierewiązacyjne, takie jak pary klucz-wartość (tabele), obiekty blob, udziały plików lub wiadomości (kolejki).
  >
  > **Wprowadzenie**: Wybierz jeden z tych typów magazynu: [obiekty blob,](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) [tabele,](../../cosmos-db/table-storage-how-to-use-dotnet.md) [kolejki](../../storage/queues/storage-dotnet-how-to-use-queues.md)lub [pliki](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Usługa Azure SQL Database:** oparta na platformie Azure wersja aparatu programu Microsoft SQL Server do przechowywania relacyjnych danych tabelaryczne w chmurze. Baza danych SQL zapewnia przewidywalną wydajność, skalowalność bez przestojów, ciągłość działania i ochronę danych.

  > **Kiedy używać:** Gdy aplikacja wymaga przechowywania danych z więzów integralności, obsługi transakcyjnej i obsługi zapytań TSQL.
  >
  > **Wprowadzenie:** [Tworzenie bazy danych SQL w ciągu kilku minut przy użyciu portalu Azure](../../sql-database/sql-database-get-started.md).


Usługi [Azure Data Factory](../../data-factory/introduction.md) można użyć do przenoszenia istniejących danych lokalnych na platformę Azure. Jeśli nie jesteś gotowy do przenoszenia danych do chmury, [połączenia hybrydowe](../../app-service/app-service-hybrid-connections.md) w usłudze Azure App Service umożliwia połączenie aplikacji hostowane usługi App Service do zasobów lokalnych. Można również połączyć się z usługami azure danych i magazynu z aplikacji lokalnych.

#### <a name="docker-support"></a>Pomoc techniczna firmy Docker

Kontenery platformy Docker, forma wirtualizacji systemu operacyjnego, umożliwiają wdrażanie aplikacji w bardziej wydajny i przewidywalny sposób. Konteneryzowana aplikacja działa w produkcji w taki sam sposób, jak w systemach deweloperskich i testowych. Kontenery można zarządzać przy użyciu standardowych narzędzi platformy Docker. Można użyć istniejących umiejętności i popularnych narzędzi open source do wdrażania aplikacji opartych na kontenerach i zarządzania nimi na platformie Azure.

Platforma Azure udostępnia kilka sposobów używania kontenerów w aplikacjach.

- **Rozszerzenie maszyny Wirtualnej platformy Azure Docker:** umożliwia skonfigurowanie maszyny Wirtualnej za pomocą narzędzi platformy Docker do działania jako host platformy Docker.

  > **Kiedy używać:** Jeśli chcesz wygenerować spójne wdrożenia kontenerów dla aplikacji na maszynie Wirtualnej lub gdy chcesz użyć [dokceny compose](https://docs.docker.com/compose/overview/).
  >
  > **Wprowadzenie:** [Tworzenie środowiska platformy Docker na platformie Azure przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Usługa Azure Kubernetes:** umożliwia tworzenie, konfigurowanie i zarządzanie klastrem maszyn wirtualnych, które są wstępnie skonfigurowane do uruchamiania aplikacji konteneryzowanych. Aby dowiedzieć się więcej o usłudze Azure Kubernetes, zobacz [Wprowadzenie usługi Azure Kubernetes.](../../aks/intro-kubernetes.md)

  > **Kiedy należy używać:** Gdy trzeba utworzyć gotowe do produkcji, skalowalne środowiska, które zapewniają dodatkowe narzędzia do planowania i zarządzania lub podczas wdrażania klastra Docker Swarm.
  >
  > **Wprowadzenie:** [Wdrażanie klastra usługi Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md).

- **Docker Machine:** Umożliwia instalowanie aparatu platformy Docker na hostach wirtualnych i zarządzanie nim przy użyciu poleceń platformy docker-machine.

  >**Kiedy używać:** Gdy trzeba szybko prototyp aplikacji, tworząc jeden host platformy Docker.

- **Niestandardowy obraz platformy Docker dla usługi App Service:** Umożliwia używanie kontenerów platformy Docker z rejestru kontenerów lub kontenera klienta podczas wdrażania aplikacji sieci web w systemie Linux.

  > **Kiedy używać:** Podczas wdrażania aplikacji sieci web w systemie Linux do obrazu platformy Docker.
  >
  > **Wprowadzenie:** [Użyj niestandardowego obrazu platformy Docker dla usługi App Service w systemie Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Uwierzytelnianie

Ważne jest, aby nie tylko wiedzieć, kto korzysta z aplikacji, ale także aby zapobiec nieautoryzowanemu dostępowi do zasobów. Platforma Azure udostępnia kilka sposobów uwierzytelniania klientów aplikacji.

- **Usługa Azure Active Directory (Azure AD)**: usługa zarządzania tożsamością i dostępem firmy Microsoft w wielu usługach opartych na chmurze. Możesz dodać logowanie jednokrotne do aplikacji, integrując się z usługą Azure AD. Dostęp do właściwości katalogu można uzyskać bezpośrednio przy użyciu interfejsu API programu Azure AD Graph lub interfejsu API programu Microsoft Graph. Można zintegrować z obsługą usługi Azure AD dla struktury autoryzacji OAuth2.0 i Open ID Connect przy użyciu natywnych punktów końcowych HTTP/REST i wieloplatformowych bibliotek uwierzytelniania usługi Azure AD.

  > **Kiedy używać:** Jeśli chcesz zapewnić środowisko jednokrotne, pracować z danymi opartymi na grafie lub uwierzytelniać użytkowników opartych na domenie.
  >
  > **Wprowadzenie:** Aby dowiedzieć się więcej, zobacz [przewodnik dla deweloperów usługi Azure Active Directory](../../active-directory/develop/v2-overview.md).

- **Uwierzytelnianie usługi aplikacji:** Gdy wybierzesz usługę App Service do hostowania aplikacji, otrzymasz również wbudowaną obsługę uwierzytelniania dla usługi Azure AD, a także dostawców tożsamości społecznościowych , w tym Facebooka, Google, Microsoft i Twittera.

  > **Kiedy używać:** Jeśli chcesz włączyć uwierzytelnianie w aplikacji usługi App Service przy użyciu usługi Azure AD, dostawców tożsamości społecznościowych lub obu.
  >
  > **Wprowadzenie:** Aby dowiedzieć się więcej o uwierzytelnianiu w usłudze App Service, zobacz [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](../../app-service/overview-authentication-authorization.md).

Aby dowiedzieć się więcej na temat najlepszych rozwiązań dotyczących zabezpieczeń na platformie Azure, zobacz [Najważniejsze wskazówki i wzorce dotyczące zabezpieczeń platformy Azure.](../../security/fundamentals/best-practices-and-patterns.md)

### <a name="monitoring"></a>Monitorowanie

Po uruchomieniu aplikacji na platformie Azure musisz monitorować wydajność, obserwować problemy i zobaczyć, jak klienci korzystają z aplikacji. Platforma Azure udostępnia kilka opcji monitorowania.

-   **Usługa Application Insights:** usługa rozszerzalnej analizy hostowana przez platformę Azure, która integruje się z programem Visual Studio w celu monitorowania aplikacji sieci web na żywo. Zapewnia dane potrzebne do ciągłego zwiększania wydajności i użyteczności aplikacji. To ulepszenie występuje niezależnie od tego, czy hostujesz aplikacje na platformie Azure, czy nie.

    >**Wprowadzenie:** Postępuj zgodnie z [samouczkiem Aplikacji Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Usługa Azure Monitor:** usługa, która ułatwia wizualizację, kwerendy, trasę, archiwizowanie i działanie na metryki i dzienniki generowane za pomocą infrastruktury platformy Azure i zasobów. Monitor jest pojedynczym źródłem monitorowania zasobów platformy Azure i udostępnia widoki danych widoczne w witrynie Azure portal.

    >**Wprowadzenie:** [Wprowadzenie do usługi Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integracja z DevOps

Niezależnie od tego, czy jest to inicjowanie obsługi administracyjnej maszyn wirtualnych, czy publikowanie aplikacji sieci web z ciągłą integracją, platforma Azure integruje się z większością popularnych narzędzi DevOps. Możesz pracować z narzędziami, które już masz i zmaksymalizować istniejące doświadczenie, obsługując narzędzia takie jak:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity ( TeamCity )
* Ansible
* Azure DevOps

> **Wprowadzenie:** Aby wyświetlić opcje DevOps dla aplikacji usługi App Service, zobacz [Ciągłe wdrażanie w usłudze Azure App Service](../../app-service/deploy-continuous-deployment.md).
>
> **Wypróbuj teraz:** [Wypróbuj kilka integracji DevOps](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Platforma Azure to globalna platforma w chmurze, która jest ogólnie dostępna w wielu regionach na całym świecie. Podczas inicjowania obsługi administracyjnej usługi, aplikacji lub maszyny wirtualnej na platformie Azure, zostaniesz poproszony o wybranie regionu. Ten region reprezentuje określone centrum danych, w którym aplikacja jest uruchamiana lub gdzie są przechowywane dane. Te regiony odpowiadają określonym lokalizacjom, które są publikowane na stronie [regiony platformy Azure.](https://azure.microsoft.com/regions/)

### <a name="choose-the-best-region-for-your-application-and-data"></a>Wybierz najlepszy region dla twojej aplikacji i danych

Jedną z zalet korzystania z platformy Azure jest to, że można wdrożyć aplikacje w różnych centrach danych na całym świecie. Region, który wybierzesz może mieć wpływ na wydajność aplikacji. Na przykład lepiej wybrać region, który jest bliżej większości klientów, aby zmniejszyć opóźnienia w żądaniach sieciowych. Możesz też wybrać region, aby spełnić wymagania prawne dotyczące dystrybucji aplikacji w niektórych krajach/regionach. Zawsze najlepszym rozwiązaniem jest przechowywanie danych aplikacji w tym samym centrum danych lub w centrum danych jak najbliżej centrum danych, które hostuje aplikację.

### <a name="multi-region-apps"></a>Aplikacje wieloregionowe

Chociaż mało prawdopodobne, nie jest niemożliwe dla całego centrum danych, aby przejść do trybu offline z powodu zdarzenia, takie jak klęska żywiołowa lub awaria Internetu. Jest najlepszym rozwiązaniem do hostowania ważnych aplikacji biznesowych w więcej niż jednym centrum danych, aby zapewnić maksymalną dostępność. Korzystanie z wielu regionów może również zmniejszyć opóźnienia dla użytkowników globalnych i zapewnić dodatkowe możliwości elastyczności podczas aktualizowania aplikacji.

Niektóre usługi, takie jak maszyny wirtualne i usługi aplikacji, używają [usługi Azure Traffic Manager,](../../traffic-manager/traffic-manager-overview.md) aby włączyć obsługę wielu regionów z obsługą awaryjną między regionami w celu obsługi aplikacji korporacyjnych o wysokiej dostępności. Na przykład zobacz [architektura odwołania platformy Azure: Uruchamianie aplikacji sieci web w wielu regionach](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Kiedy używać:** Jeśli masz aplikacje przedsiębiorstwa i wysokiej dostępności, które korzystają z pracy awaryjnej i replikacji.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Jak zarządzać własnymi aplikacjami i projektami?

Platforma Azure zapewnia bogaty zestaw środowisk do tworzenia zasobów, aplikacji i projektów platformy Azure oraz zarządzania nimi — zarówno programowo, jak i w [witrynie Azure portal.](https://portal.azure.com/)

### <a name="command-line-interfaces-and-powershell"></a>Interfejsy wiersza polecenia i programu PowerShell

Platforma Azure oferuje dwa sposoby zarządzania aplikacjami i usługami z wiersza polecenia. Można użyć narzędzi, takich jak Bash, Terminal, wiersz polecenia lub wybrane narzędzie wiersza polecenia. Zazwyczaj można wykonywać te same zadania z wiersza polecenia, jak w witrynie Azure portal — takie jak tworzenie i konfigurowanie maszyn wirtualnych, sieci wirtualnych, aplikacji sieci web i innych usług.

-   [Interfejs wiersza polecenia platformy Azure (CLI)](../../xplat-cli-install.md): Umożliwia łączenie się z subskrypcją platformy Azure i programowanie różnych zadań względem zasobów platformy Azure z wiersza polecenia.

-   [Azure PowerShell:](../../powershell-install-configure.md)Zawiera zestaw modułów z poleceniami cmdlet, które umożliwiają zarządzanie zasobami platformy Azure przy użyciu programu Windows PowerShell.

### <a name="azure-portal"></a>Portal Azure

[Portal Azure](https://portal.azure.com) jest aplikacją internetową. Za pomocą witryny Azure Portal można tworzyć zasoby i usługi platformy Azure, zarządzać nimi i usuwać je. Obejmuje ona:

* Konfigurowalny pulpit nawigacyjny
* Narzędzia do zarządzania zasobami platformy Azure
* Dostęp do ustawień subskrypcji i informacji rozliczeniowych. Aby uzyskać więcej informacji, zobacz [omówienie portalu Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>Interfejsy API REST

Platforma Azure jest oparta na zestawie interfejsów API REST, które obsługują interfejs użytkownika witryny azure portal. Większość z tych interfejsów API REST są również obsługiwane, aby umożliwić programowo aprowizowania i zarządzania zasobów platformy Azure i aplikacji z dowolnego urządzenia z dostępem do Internetu. Aby uzyskać pełny zestaw dokumentacji interfejsu API REST, zobacz [odwołanie do zestawu SDK rest platformy Azure](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>Interfejsy API

Wraz z interfejsami API REST wiele usług platformy Azure umożliwia również programowe zarządzanie zasobami z aplikacji przy użyciu zestawów SDK platformy Azure specyficznych dla platformy, w tym zestawów SDK dla następujących platform deweloperskich:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Przejdź](https://docs.microsoft.com/azure/go)

Usługi takie jak [aplikacje mobilne](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) i [usługi Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) zapewniają zestawy SDK po stronie klienta, aby umożliwić dostęp do usług z aplikacji klienckich sieci Web i mobilnych.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Uruchamianie aplikacji na platformie Azure prawdopodobnie wymaga pracy z wieloma usługami platformy Azure. Usługi te są zgodne z tym samym cyklem życia i mogą być traktowane jako jednostka logiczna. Na przykład aplikacja sieci web może używać aplikacji sieci Web, bazy danych SQL, magazynu, pamięci podręcznej azure dla usługi Redis i usług Azure Content Delivery Network. [Usługa Azure Resource Manager](../../azure-resource-manager/management/overview.md) umożliwia pracę z zasobami w aplikacji jako grupa. Można wdrożyć, zaktualizować lub usunąć wszystkie zasoby w jednej, skoordynowanej operacji.

Oprócz logicznego grupowania powiązanych zasobów i zarządzania nimi usługa Azure Resource Manager zawiera funkcje wdrażania, które umożliwiają dostosowanie wdrażania i konfiguracji powiązanych zasobów. Na przykład można użyć Menedżera zasobów wdrożyć i skonfigurować aplikację. Ta aplikacja może składać się z wielu maszyn wirtualnych, moduł równoważenia obciążenia i bazy danych SQL platformy Azure jako pojedynczej jednostki.

Te wdrożenia można opracować przy użyciu szablonu usługi Azure Resource Manager, który jest dokumentem w formacie JSON. Szablony umożliwiają definiowanie wdrażania i zarządzanie aplikacjami przy użyciu szablonów deklaratywnych, a nie skryptów. Szablony mogą działać w różnych środowiskach, takich jak testowanie, przemieszczania i produkcji. Na przykład można użyć szablonów, aby dodać przycisk do repozytorium GitHub, który wdraża kod w repozytorium do zestawu usług platformy Azure za pomocą jednego kliknięcia.

> **Kiedy używać:** Użyj szablonów Menedżera zasobów, gdy chcesz wdrożenia opartego na szablonach dla aplikacji, które można zarządzać programowo przy użyciu interfejsów API REST, interfejsu wiersza polecenia platformy Azure i programu Azure PowerShell.
>
> **Wprowadzenie:** Aby rozpocząć korzystanie z szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Omówienie kont, subskrypcji i rozliczeń

Jako deweloperzy lubimy zagłębiać się w kod i starać się rozpocząć tak szybko, jak to możliwe, aby nasze aplikacje były uruchamiane. Z pewnością chcemy zachęcić Cię do jak najprostszej pracy na platformie Azure. Aby ułatwić, platforma Azure oferuje [bezpłatną wersję próbną.](https://azure.microsoft.com/free/) Niektóre usługi mają nawet funkcję "Wypróbuj za darmo", taką jak [usługa Azure App Service,](https://tryappservice.azure.com/)która nie wymaga nawet utworzenia konta. Równie zabawne, jak to jest, aby przejść do kodowania i wdrażania aplikacji na platformie Azure, ważne jest również, aby poświęcić trochę czasu, aby zrozumieć, jak działa platforma Azure. W szczególności należy zrozumieć, jak to działa z punktu widzenia kont użytkowników, subskrypcji i rozliczeń.

### <a name="what-is-an-azure-account"></a>Co to jest konto platformy Azure?

Aby utworzyć subskrypcję platformy Azure lub pracować z nią, musisz mieć konto platformy Azure. Konto platformy Azure to po prostu tożsamość w usłudze Azure AD lub w katalogu, takim jak organizacja szkolna lub szkolna, którym ufa usługa Azure AD. Jeśli nie należysz do takiej organizacji, zawsze możesz utworzyć subskrypcję przy użyciu konta Microsoft, które jest zaufane przez usługę Azure AD. Aby dowiedzieć się więcej na temat integrowania lokalnej usługi Windows Server Active Directory z usługą Azure AD, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Każda subskrypcja platformy Azure jest połączona relacją zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Aby dowiedzieć się więcej, zobacz [Jak subskrypcje platformy Azure są skojarzone z usługą Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Oprócz definiowania poszczególnych tożsamości kont platformy Azure, nazywanych również *użytkownikami,* można zdefiniować *grupy* w usłudze Azure AD. Tworzenie grup użytkowników jest dobrym sposobem zarządzania dostępem do zasobów w ramach subskrypcji przy użyciu kontroli dostępu opartej na rolach (RBAC). Aby dowiedzieć się, jak tworzyć grupy, zobacz [Tworzenie grupy w wersji zapoznawczej usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Grupy można również tworzyć i zarządzać nimi [za pomocą programu PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Zarządzanie subskrypcjami

Subskrypcja to logiczne grupowanie usług platformy Azure, które jest połączone z kontem platformy Azure. Jedno konto platformy Azure może zawierać wiele subskrypcji. Rozliczenia za usługi platformy Azure są wykonywane na podstawie subskrypcji. Aby uzyskać listę dostępnych ofert subskrypcji według typu, zobacz [Szczegóły oferty platformy Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Subskrypcje platformy Azure mają administratora konta, który ma pełną kontrolę nad subskrypcją. Mają również administratora usługi, który ma kontrolę nad wszystkimi usługami w ramach subskrypcji. Aby uzyskać informacje o administratorach subskrypcji klasycznych, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). Poszczególnym kontom można uzyskać szczegółową kontrolę zasobów platformy Azure przy użyciu [kontroli dostępu opartej na rolach (RBAC).](../../role-based-access-control/overview.md)

#### <a name="resource-groups"></a>Grupy zasobów

Podczas aprowizowania nowych usług platformy Azure, należy to zrobić w danej subskrypcji. Poszczególne usługi platformy Azure, które są również nazywane zasobami, są tworzone w kontekście grupy zasobów. Grupy zasobów ułatwiają wdrażanie zasobów aplikacji i zarządzanie nimi. Grupa zasobów powinna zawierać wszystkie zasoby dla aplikacji, z którą chcesz pracować jako jednostka. Można przenosić zasoby między grupami zasobów, a nawet do różnych subskrypcji. Aby dowiedzieć się więcej o przenoszeniu zasobów, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Eksplorator zasobów platformy Azure to doskonałe narzędzie do wizualizacji zasobów, które zostały już utworzone w ramach subskrypcji. Aby dowiedzieć się więcej, zobacz [Wyświetlanie i modyfikowanie zasobów za pomocą Eksploratora zasobów platformy Azure.](../../resource-manager-resource-explorer.md)

#### <a name="grant-access-to-resources"></a>Przyznanie dostępu do zasobów

Po zezwoleniu na dostęp do zasobów platformy Azure, zawsze jest najlepszym rozwiązaniem, aby zapewnić użytkownikom najmniejsze uprawnienia, które są wymagane do wykonania danego zadania.

- **Kontrola dostępu oparta na rolach (RBAC)**: Na platformie Azure można udzielić dostępu do kont użytkowników (podmiotów) w określonym zakresie: subskrypcji, grupie zasobów lub poszczególnych zasobach. RBAC umożliwia wdrażanie zasobów w grupie zasobów i udzielanie uprawnień określonej użytkownikowi lub grupie. Umożliwia również ograniczenie dostępu tylko do zasobów należących do docelowej grupy zasobów. Można również udzielić dostępu do pojedynczego zasobu, takiego jak maszyna wirtualna lub sieć wirtualna. Aby udzielić dostępu, należy przypisać rolę do użytkownika, grupy lub jednostki usługi. Istnieje wiele wstępnie zdefiniowanych ról i można również zdefiniować własne role niestandardowe. Aby dowiedzieć się więcej, zobacz [Co to jest kontrola dostępu oparta na rolach (RBAC)?](../../role-based-access-control/overview.md).

  > **Kiedy używać:** Gdy potrzebujesz szczegółowego zarządzania dostępem dla użytkowników i grup lub gdy musisz uczynić użytkownika właścicielem subskrypcji.
  >
  > **Wprowadzenie:** Aby dowiedzieć się więcej, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).

- **Obiekty jednostki usługi:** Wraz z zapewnieniem dostępu do podmiotów i grup użytkowników, można udzielić tego samego dostępu do jednostki usługi.

  > **Kiedy używać:** Podczas programowego zarządzania zasobami platformy Azure lub udzielania dostępu dla aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji i jednostki usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Tagi

Usługa Azure Resource Manager umożliwia przypisywanie tagów niestandardowych do poszczególnych zasobów. Tagi, które są parami klucz-wartość, mogą być pomocne, gdy trzeba zorganizować zasoby do rozliczeń lub monitorowania. Tagi umożliwiają śledzenie zasobów w wielu grupach zasobów. Tagi można przypisać w następujący sposób:

* W portalu
* W szablonie usługi Azure Resource Manager
* Korzystanie z interfejsu API REST
* Korzystanie z interfejsu wiersza polecenia platformy Azure
* Korzystanie z programu PowerShell

Do każdego zasobu można przypisać wiele tagów. Aby dowiedzieć się więcej, zobacz [Organizowanie zasobów platformy Azure za pomocą tagów](../../resource-group-using-tags.md).

### <a name="billing"></a>Rozliczenia

W przejściu z komputerów lokalnych do usług hostowanych w chmurze, śledzenie i szacowanie użycia usług i związanych z nimi kosztów są istotnymi problemami. Ważne jest, aby oszacować, jaki koszt nowych zasobów kosztuje uruchamianie co miesiąc. Można również prognozować, jak wygląda rozliczenie dla danego miesiąca na podstawie bieżących wydatków.

#### <a name="get-resource-usage-data"></a>Pobierz dane użycia zasobów

Platforma Azure udostępnia zestaw interfejsów API odpoczynku rozliczeń, które dają dostęp do informacji o zużyciu zasobów i metadanych dla subskrypcji platformy Azure. Te interfejsy API rozliczeń umożliwiają lepsze przewidywanie kosztów platformy Azure i zarządzanie nimi. Możesz śledzić i analizować wydatki w odstępach godzinowych i tworzyć alerty dotyczące wydatków. Można również przewidzieć przyszłe rozliczenia na podstawie bieżących trendów użycia.

>**Wprowadzenie:** Aby dowiedzieć się więcej na temat korzystania z interfejsów API rozliczeń, zobacz [Omówienie użycia rozliczeń platformy Azure i interfejsów API ratecard](../../cost-management-billing/manage/usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Przewidywanie przyszłych kosztów

Chociaż oszacowanie kosztów z wyprzedzeniem jest trudne, platforma Azure ma narzędzia, które mogą ci pomóc. Posiada [kalkulator cen,](https://azure.microsoft.com/pricing/calculator/) aby pomóc oszacować koszt wdrożonych zasobów. Można również użyć zasobów rozliczeniowych w portalu i interfejsów API odpoczynku rozliczeń do szacowania przyszłych kosztów na podstawie bieżącego zużycia.

>**Wprowadzenie:** Zobacz [Omówienie użycia rozliczeń platformy Azure i interfejsów API ratecard](../../cost-management-billing/manage/usage-rate-card-overview.md).
