---
title: Wprowadzenie — przewodnik dla deweloperów na platformie Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera podstawowe informacje dla deweloperów chcących zacząć przy użyciu platformy Microsoft Azure dla swoich potrzeb deweloperskich.
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
ms.openlocfilehash: 99e043adeac9a43432fb1eba85527b561c477354
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570555"
---
# <a name="get-started-guide-for-azure-developers"></a>Przewodnik dla początkujących deweloperów platformy Azure

## <a name="what-is-azure"></a>Co to jest platforma Azure?

Platforma Azure to kompletnej platformie chmury można hostowanie istniejących aplikacji, Usprawnij tworzenie nowych aplikacji, a nawet udoskonalaj aplikacje w środowisku lokalnym. Azure integruje się usług w chmurze, które trzeba programowanie, testowanie, wdrażanie i zarządzanie aplikacjami — przy wykorzystaniu możliwości efektywność chmury obliczeniowej.

Hostowanie aplikacji na platformie Azure, możesz zacząć od małej i łatwo skalować swoją aplikację wraz ze wzrostem natężenia oczekiwanym przez klientów. Platforma Azure oferuje również niezawodności, które ma potrzebne dla aplikacji o wysokiej dostępności, nawet w tym tryb failover między różnymi regionami. [Witryny Azure portal](https://portal.azure.com) umożliwia łatwe zarządzanie wszystkich usług platformy Azure. Usługi można również zarządzać programowo przy użyciu interfejsów API i szablony specyficzne dla usługi.

**Kto powinien przeczytać**: Ten przewodnik stanowi wprowadzenie do platformy Azure dla deweloperów aplikacji. Zawiera również wskazówki i kierunku, w którym chcesz zacząć od tworzenia nowych aplikacji na platformie Azure, czy migrujesz istniejące aplikacje na platformie Azure.

## <a name="where-do-i-start"></a>Od czego zacząć?

W przypadku wszystkich usług, które są oferowane na platformie Azure może być czasochłonnym zadaniem, aby ustalić usługi, które muszą być obsługiwane architektury rozwiązania. W tej sekcji przedstawiono usług platformy Azure, które często używany przez deweloperów. Aby uzyskać listę wszystkich usług platformy Azure, zobacz [dokumentacji platformy Azure](../../index.md).

Najpierw należy zdecydować się na temat sposobu hostowania aplikacji na platformie Azure. Trzeba zarządzać całą infrastrukturą jako maszynę wirtualną (VM). Można użyć funkcji zarządzania platformy udostępnianych przez platformę Azure? Być może należy platforma bez użycia serwera na hoście tylko wykonanie kodu?

Twoja aplikacja potrzebuje magazynu w chmurze, które platforma Azure oferuje kilka opcji. Możesz korzystać z zalet platformy Azure uwierzytelnianie w przedsiębiorstwie. Dostępne są również narzędzia do programowania w chmurze oraz monitorowania i większość usług hostingu oferują integracja metodyki DevOps.

Teraz Spójrzmy na niektóre z określonych usług, które firma Microsoft zaleca badania dla aplikacji.

### <a name="application-hosting"></a>Hosting aplikacji

Platforma Azure udostępnia kilka oparte na chmurze obliczeniowej ofert, aby uruchomić aplikację, dzięki czemu nie trzeba się martwić o szczegóły infrastruktury. Możesz łatwo skalować w górę lub skalować swoje zasoby wzrostem użycia aplikacji.

Platforma Azure oferuje usługi, które obsługują do tworzenia aplikacji i potrzeb hostowania. System Azure stanowi infrastrukturę jako usługę (IaaS), aby zapewnić pełną kontrolę nad hosting aplikacji. Platforma Azure jako oferty usługi (PaaS), podaj w pełni zarządzane usługi potrzebne do zwiększanie możliwości aplikacji. Ma wartość true, nawet hosting bezserwerowy na platformie Azure wszystko, co należy zrobić w przypadku pisania kodu.

![Opcje hostowania aplikacji platformy Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Najszybszą metodą publikowanie projektów sieci web, należy wziąć pod uwagę usługi Azure App Service. App Service umożliwia łatwe rozszerzenie aplikacji sieci web do obsługi klientów mobilnych i publikować łatwo użyte interfejsów API REST. Ta platforma udostępnia uwierzytelniania przy użyciu dostawców sieci społecznościowych, na podstawie ruchu skalowania automatycznego testowania w produkcji i wdrożenia ciągłej i opartych na kontenerach.

Można utworzyć aplikacji sieci web, zaplecza aplikacji mobilnych i aplikacji API apps.

Ponieważ wszystkie trzema typami aplikacji używają środowiska uruchomieniowego usługi App Service, można hostować witrynę sieci Web, obsługę klientów mobilnych i udostępnianie interfejsów API na platformie Azure wszystko z tego samego projektu lub rozwiązania. Aby dowiedzieć się więcej na temat usługi App Service, zobacz [co to jest Azure Web Apps](../../app-service/overview.md).

Usługa App Service został zaprojektowany przy użyciu infrastruktury DevOps na uwadze. Obsługuje ona różne narzędzia wdrożeń publikowania i ciągłej integracji, takich jak GitHub elementów webhook, Jenkins, DevOps platformy Azure, TeamCity i inne.

Można migrować istniejące aplikacje do usługi App Service przy użyciu [narzędzia do migracji online](https://www.migratetoazure.net/).

> **Kiedy należy używać**: Usługa App Service podczas migrowania istniejących aplikacji sieci web na platformie Azure i gdy jest potrzebna w pełni zarządzana platforma hostingu dla aplikacji sieci web. Usługi App Service można użyć również, gdy potrzebujesz do obsługi klientów mobilnych lub udostępnianie interfejsów API REST wraz z aplikacją.
> 
> **Rozpoczynanie pracy**: Usługa App Service ułatwia tworzenie i wdrażanie pierwszej [aplikacji sieci web](../../app-service/app-service-web-get-started-dotnet.md), [aplikacji mobilnej](../../app-service-mobile/app-service-mobile-ios-get-started.md), lub [aplikacji interfejsu API](../../app-service/app-service-web-tutorial-rest-api.md).
> 
> **Wypróbuj teraz usługę**: Usługa App Service umożliwia inicjowanie obsługi administracyjnej krótkotrwałe aplikacji, aby wypróbować platformę bez konieczności Załóż konto platformy Azure. Wypróbuj platformę i [utworzyć aplikację usługi Azure App Service](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Jako infrastrukturę jako dostawca usługi (IaaS), platforma Azure umożliwia wdrażanie lub migracji aplikacji wirtualnych z systemem Linux lub Windows. Wraz z usługi Azure Virtual Network Azure Virtual Machines obsługuje wdrażanie systemu Windows lub maszyny wirtualne systemu Linux na platformie Azure. Z maszynami wirtualnymi masz całkowitej kontroli nad konfiguracji komputera. Korzystając z maszyn wirtualnych, jesteś odpowiedzialny za serwera instalacji, konfiguracji, konserwacja i systemu operacyjnego poprawek oprogramowania.

Ze względu na poziom kontroli, który maszyn wirtualnych możesz uruchomić szeroką gamę obciążeń serwera na platformie Azure, który nie mieści się w modelu PaaS. Te obciążenia obejmują serwery baz danych, usługi Active Directory systemu Windows Server i Microsoft SharePoint. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą maszyn wirtualnych dla dowolnego [Linux](/azure/virtual-machines/linux/) lub [Windows](/azure/virtual-machines/windows/).

> **Kiedy należy używać**: Użyj maszyn wirtualnych, gdy mają pełną kontrolę nad infrastruktury aplikacji lub migrować obciążenia aplikacji lokalnych na platformę Azure bez konieczności wprowadzania zmian.
> 
> **Rozpoczynanie pracy**: Tworzenie [maszyny Wirtualnej systemu Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) lub [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) w witrynie Azure portal.

#### <a name="azure-functions-serverless"></a>Usługa Azure Functions (bez użycia serwera)

A nie o kompilowania i zarządzanie całą aplikację ani infrastrukturę do uruchomienia kodu. Co zrobić, jeśli można po prostu pisanie kodu i jest uruchamiany w odpowiedzi na zdarzenia lub zgodnie z harmonogramem?  [Usługa Azure Functions](../../azure-functions/functions-overview.md) jest a "bezserwerowych"-oferty styl, który pozwala napisać tylko kod należy. Za pomocą funkcji wykonywanie kodu jest wyzwalana przez żądania HTTP, elementy webhook, zdarzeń usługi w chmurze lub zgodnie z harmonogramem. Możesz programować w języku programowania wyboru, takich jak C\#, F\#, Node.js, Python lub PHP. Za pomocą opartej na użyciu rozliczeń, płacisz tylko za czas, który wykonuje kodu i platformy Azure jest skalowana zgodnie z potrzebami.

> **Kiedy należy używać**: Użyj usługi Azure Functions, gdy masz kod, który jest wyzwalany przez inne usługi platformy Azure przez zdarzenia oparte na sieci web lub zgodnie z harmonogramem. Umożliwia także funkcje Jeśli nie potrzebujesz obciążenie kompletnego projektu hostowanej lub gdy chcesz zapłacić za czas, jaki kod jest wykonywany. Aby dowiedzieć się więcej, zobacz [Azure Functions — omówienie](../../azure-functions/functions-overview.md).
> 
> **Rozpoczynanie pracy**: Postępuj zgodnie z samouczkiem Szybki Start funkcje do [tworzenie pierwszej funkcji](../../azure-functions/functions-create-first-azure-function.md) z poziomu portalu.
> 
> **Wypróbuj teraz usługę**: Usługa Azure Functions umożliwia uruchamianie kodu bez konieczności Załóż konto platformy Azure. Wypróbuj teraz na i [tworzenie pierwszej funkcji platformy Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Usługa Azure Service Fabric to platforma systemów rozproszonych, która ułatwia tworzenie, pakowanie, wdrażanie i zarządzanie skalowalnych i niezawodnych mikrousług. Zapewnia również kompleksowe funkcje zarządzania aplikacjami dla inicjowania obsługi, wdrażanie, monitorowanie, uaktualnianie/poprawianie i usuwanie wdrożonych aplikacji. Wdrożenie aplikacji działających w ramach współużytkowanej puli maszyn, można zacząć od małej i skalowania do setek lub tysięcy maszyn, zgodnie z potrzebami.

Usługa Service Fabric obsługuje interfejs WebAPI z Open Web Interface for .NET (OWIN) i ASP.NET Core. Udostępnia zestawy SDK do tworzenie usług w systemie Linux w języku Java i .NET Core. Aby dowiedzieć się więcej na temat usługi Service Fabric, zobacz [dokumentacja usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

> **Kiedy stosować:** Service Fabric to dobry wybór, podczas tworzenia aplikacji lub ponownego tworzenia istniejącej aplikacji do opartych na architekturze mikrousług. Jeśli potrzebujesz więcej kontroli nad lub bezpośredni dostęp do podstawowej infrastruktury za pomocą usługi Service Fabric.
> 
> **Wprowadzenie:** [Tworzenie pierwszej aplikacji usługi Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Udoskonalaj swoje aplikacje z usługami platformy Azure

Oprócz hostowania aplikacji, system Azure oferuje ofert usług, zwiększające funkcjonalność, tworzenia i konserwacji aplikacji, zarówno w chmurze i lokalnych.

#### <a name="hosted-storage-and-data-access"></a>Hostowanej storage i dostęp do danych

Większość aplikacji muszą przechowywać dane, więc niezależnie od tego, jak użytkownik zdecyduje się hostowanie aplikacji na platformie Azure, należy wziąć pod uwagę co najmniej jedną z następujących usług magazynu i danych.

- **Azure Cosmos DB**: Usługa globalnie dystrybuowanej, wielomodelowej bazy danych, która umożliwia elastycznie Skaluj przepływność oraz Magazyn w dowolnej liczbie regionów geograficznych dzięki kompleksowym umowy SLA. 
  > **Kiedy stosować:** Gdy Twoja aplikacja potrzebuje dokumentów, tabela lub baz danych programu graph, w tym bazami danych MongoDB, o wiele dokładnie zdefiniowanych modeli spójności. 
  > 
  > **Rozpoczynanie pracy**: [Tworzenie aplikacji sieci web usługi Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Jeśli jesteś deweloperem bazy danych MongoDB, zobacz [tworzenie aplikacji internetowej bazy danych MongoDB w usłudze Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure Storage**: Udostępnia magazyn odporne i o wysokiej dostępności dla obiektów blob, kolejek, plików i innych rodzajów danych nierelacyjnych. Storage stanowi podstawę magazynu dla maszyn wirtualnych.

  > **Kiedy należy używać**: Kiedy aplikacja przechowuje danych nierelacyjnych, takich jak pary klucz wartość (tabele), blob, udziałów plików lub komunikatów (kolejki).
  > 
  > **Rozpoczynanie pracy**: Wybierz jedną z tych typów pamięci masowej: [obiektów blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabel](../../cosmos-db/table-storage-how-to-use-dotnet.md), [kolejek](../../storage/queues/storage-dotnet-how-to-use-queues.md), lub [pliki](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: Oparte na platformie Azure w wersji aparatu Microsoft SQL Server do przechowywania danych relacyjnych tabelarycznych w chmurze. SQL Database oferuje przewidywalną wydajność, skalowalność bez przestojów, ciągłość prowadzenia działalności biznesowej i ochrony danych.

  > **Kiedy należy używać**: Jeśli aplikacja wymaga magazyn danych przy użyciu więzów integralności transakcyjnej pomocy technicznej i Obsługa zapytania TSQL.
  > 
  > **Rozpoczynanie pracy**: [Tworzenie bazy danych SQL w ciągu kilku minut przy użyciu witryny Azure portal](../../sql-database/sql-database-get-started.md).


Możesz użyć [usługi Azure Data Factory](../../data-factory/introduction.md) przenoszenia istniejących lokalnych danych na platformie Azure. Jeśli nie jesteś gotowy do przenoszenia danych do chmury, [połączeń hybrydowych](../../biztalk-services/integration-hybrid-connection-overview.md) w usługi BizTalk Services umożliwia łączenie usługi App Service hostowanej aplikacji do zasobów lokalnych. Można również łączysz się dane platformy Azure i magazyn usługi z poziomu aplikacji w środowisku lokalnym.

#### <a name="docker-support"></a>Obsługę platformy docker

Kontenery platformy docker, formularz wirtualizacji systemu operacyjnego, pozwalają na wdrażanie aplikacji w sposób bardziej efektywne i przewidywalne. Konteneryzowanej aplikacji działa w środowisku produkcyjnym, taki sam sposób jak w systemów tworzenia i testowania. Za Zarządzanie kontenerami przy użyciu standardowych narzędzi platformy Docker. Twoje umiejętności i popularnych narzędzi typu open-source umożliwia wdrażanie aplikacji i zarządzanie nimi opartych na kontenerach na platformie Azure.

System Azure oferuje kilka sposobów na korzystanie z kontenerów w swoich aplikacjach.

- **Rozszerzenie maszyny Wirtualnej Docker Azure**: Umożliwia skonfigurowanie maszyny Wirtualnej za pomocą narzędzi platformy Docker, aby pełnić rolę hosta platformy Docker.

  > **Kiedy należy używać**: Gdy chcesz wygenerować wdrożeń kontenerów spójny dla aplikacji na maszynie Wirtualnej lub gdy chcesz używać [narzędzia Docker Compose](https://docs.docker.com/compose/overview/).
  > 
  > **Rozpoczynanie pracy**: [Tworzenie w środowisku platformy Docker na platformie Azure przy użyciu rozszerzenia maszyny Wirtualnej Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Usługa Azure Container Service**: Umożliwia tworzenie, konfigurowanie i Zarządzanie klastrem maszyn wirtualnych, które są wstępnie skonfigurowane do uruchamiania konteneryzowanych aplikacji. Aby dowiedzieć się więcej na temat usługi kontenera, zobacz [wprowadzenie do usługi Azure Container Service](../../container-service/container-service-intro.md).

  > **Kiedy należy używać**: Kiedy należy do narzędzia do zarządzania i środowisk gotowe do produkcji, skalowalne kompilacji, które zapewniają dodatkowe planowanie lub podczas wdrażania klastra Docker Swarm.
  > 
  > **Rozpoczynanie pracy**: [Wdrażanie klastra usługi kontenera](../../container-service/dcos-swarm/container-service-deployment.md).

- **Maszyny platformy docker**: Pozwala zainstalować i zarządzać aparat platformy Docker na hostów wirtualnych za pomocą poleceń maszyny platformy docker.

  >**Kiedy należy używać**: Gdy zachodzi potrzeba szybko prototypu aplikacji, tworząc jeden host platformy Docker.

- **Niestandardowy obraz platformy Docker dla usługi App Service**: Umożliwia korzystanie z kontenerów platformy Docker z rejestru kontenerów lub kontener klienta podczas wdrażania aplikacji sieci web w systemie Linux.

  > **Kiedy należy używać**: W przypadku wdrażania aplikacji sieci web w systemie Linux w obrazie platformy Docker.
  > 
  > **Rozpoczynanie pracy**: [Używanie niestandardowego obrazu platformy Docker dla usługi App Service w systemie Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

Bezwzględnie nie tylko wiedzieć, kto korzysta z aplikacji, ale także w celu uniemożliwienia nieupoważnionego dostępu do zasobów. System Azure oferuje kilka sposobów uwierzytelniania klientów aplikacji.

- **Azure Active Directory (Azure AD)**: Microsoft wielodostępna, oparta na chmurze tożsamościami i dostępem usługi management. Dzięki integracji z usługą Azure AD, można dodać logowania jednokrotnego (SSO) do aplikacji. Możesz uzyskać dostęp właściwości katalogu przy użyciu interfejsu API programu Graph usługi Azure AD, bezpośrednio lub interfejsu API programu Microsoft Graph. Możesz wprowadzić integrację z obsługą usługi Azure AD ramy autoryzacji OAuth 2.0 i Open ID Connect, przy użyciu bibliotek uwierzytelniania dla wielu platform w usłudze Azure AD i natywne punkty końcowe REST protokołu HTTP.

  > **Kiedy należy używać**: Jeśli chcesz zapewnić środowisko logowania jednokrotnego, pracować z danymi opartej na wykresach lub uwierzytelnianie oparte na domenie użytkowników.
  > 
  > **Rozpoczynanie pracy**: Aby dowiedzieć się więcej, zobacz [przewodnik dewelopera usługi Azure Active Directory](../../active-directory/develop/v1-overview.md).

- **Uwierzytelnianie usługi App Service**: W przypadku wybrania usługi App Service do hostowania aplikacji, możesz także uzyskać wbudowanego uwierzytelniania za pomocą techniczną dla usługi Azure AD wraz z dostawców tożsamości społecznościowych — w tym usługi Facebook, Google, Microsoft i Twitter.

  > **Kiedy należy używać**: Jeśli chcesz włączyć uwierzytelnianie w aplikacji usługi App Service przy użyciu usługi Azure AD, dostawców tożsamości społecznościowych i / lub.
  > 
  > **Rozpoczynanie pracy**: Aby dowiedzieć się więcej o uwierzytelnianiu w usłudze App Service, zobacz [uwierzytelnianie i autoryzacja w usłudze Azure App Service](../../app-service/overview-authentication-authorization.md).

Aby dowiedzieć się więcej o najlepszych rozwiązaniach zabezpieczeń platformy Azure, zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorowanie

Swoją aplikację wzwyż i działających na platformie Azure muszą mieć możliwość monitorowania wydajności, obejrzyj problemów i zobacz, jak klienci korzystają z aplikacji. System Azure oferuje kilka opcji monitorowania.

-   **Visual Studio Application Insights**: Hostowanymi na platformie Azure rozszerzalną usługą analizy integrującej się z programem Visual Studio do monitorowania aplikacji sieci web na żywo. Udostępnia dane, które należy ciągłym udoskonalaniu wydajności i użyteczności aplikacji, czy jest hostowany na platformie Azure lub nie.

    >**Rozpoczynanie pracy**: Postępuj zgodnie z [samouczka usługi Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: Usługa, która pomaga w wizualizacji, zapytania, trasy, archiwizować i działania dotyczące metryk i dzienników generowanych przez usługi infrastruktury platformy Azure i zasobów. Monitor zapewnia widoki danych, zobacz temat w witrynie Azure portal, a następnie jest jedno źródło monitorowania zasobów platformy Azure.
 
    >**Rozpoczynanie pracy**: [Rozpoczynanie pracy z usługą Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integracja metodyki DevOps

Czy jest on aprowizowanie maszyn wirtualnych lub publikowania aplikacji sieci web dzięki ciągłej integracji, Azure integruje się z najbardziej popularnych narzędzi DevOps. Obsługa narzędzi, takich jak Jenkins, GitHub, Puppet, Chef, TeamCity, Ansible, DevOps platformy Azure i innych użytkowników możesz pracować z narzędziami, które już masz i optymalnie wykorzystać istniejące.

> **Wypróbuj je teraz:** [Wypróbuj niektóre z integracji metodyki DevOps](https://azure.microsoft.com/try/devops/).
> 
> **Rozpoczynanie pracy**: Aby wyświetlić opcje DevOps dla aplikacji usługi App Service, zobacz [ciągłe wdrażanie w usłudze Azure App Service](../../app-service/deploy-continuous-deployment.md).


## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Azure to platforma chmury globalnej, która jest ogólnie dostępna w wielu regionach na całym świecie. Podczas aprowizowania usługi, aplikacji lub maszyny Wirtualnej na platformie Azure, zostanie wyświetlony monit wybierz region, który reprezentuje określonego centrum danych, której działa Twoja aplikacja lub gdy dane są przechowywane. Te regiony odpowiadają określonych lokalizacjach, które są publikowane w [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/) strony.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Wybierz region najlepsze dla aplikacji i danych

Jedną z zalet korzystania z systemu Azure jest wdrożenie aplikacji w różnych centrach danych na całym świecie. Region, który wybierzesz może mieć wpływ na wydajność aplikacji. Na przykład jest lepszym rozwiązaniem wybierz region, który jest bliżej większość klientami w celu zmniejszenia opóźnień w żądaniach sieci. Można również wybrać swój region, aby spełnić wymagania prawne dystrybucji aplikacji w niektórych krajach/regionach. Zawsze jest najlepszym rozwiązaniem do przechowywania danych aplikacji, w tym samym centrum danych lub w centrum danych jako niemal jako możliwe do centrum danych, który jest hostem aplikacji.

### <a name="multi-region-apps"></a>Aplikacje dla wielu regionów

Mimo że jest to mało prawdopodobne, nie jest możliwe dla całego centrum danych przejść do trybu offline z powodu zdarzenia, takiego jak klęski żywiołowe lub błąd z Internetem. Jest to najlepsze rozwiązanie do hostowania aplikacji ważnych w więcej niż jednym centrum danych w celu zapewnienia maksymalnej dostępności. Używanie wielu regionów również zmniejszenie opóźnień dla użytkowników globalnych i zapewnia dodatkowe możliwości, aby zapewnić elastyczność podczas aktualizowania aplikacji.

Niektóre usługi, takie jak maszyny wirtualne i usługi dla aplikacji używać [usługi Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) można włączyć obsługę wielu regionów za pomocą trybu failover między regionami, aby obsługiwać aplikacje dla przedsiębiorstw o wysokiej dostępności. Aby uzyskać przykład, zobacz [architektura referencyjna platformy Azure: Uruchamianie aplikacji sieci web w wielu regionach](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Kiedy należy używać**: Jeśli masz przedsiębiorstwa i aplikacji o wysokiej dostępności, które korzystają z trybu failover i replikacji.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Jak zarządzać własnymi aplikacjami i projektami?

System Azure oferuje bogaty zestaw funkcji służących do tworzenia i zarządzania zasobami platformy Azure, aplikacji i projektów — zarówno programowo i [witryny Azure portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfejsów z wierszem polecenia i programu PowerShell

Platforma Azure udostępnia dwa sposoby zarządzania swoje aplikacje i usługi z poziomu wiersza polecenia przy użyciu powłoki Bash, program Terminal, wiersza polecenia lub Twoje narzędzie wiersza polecenia, wyboru. Zazwyczaj te same zadania można wykonać z poziomu wiersza polecenia, tak jak w witrynie Azure portal — takie jak tworzenie i konfigurowanie maszyn wirtualnych, sieci wirtualne, aplikacje sieci web i innych usług.

-   [Interfejs wiersza polecenia platformy Azure (CLI)](../../xplat-cli-install.md): Umożliwia łączenie się z subskrypcją platformy Azure i program w różnych zadań w odniesieniu do zasobów platformy Azure z poziomu wiersza polecenia.

-   [Azure PowerShell](../../powershell-install-configure.md): Zawiera zestaw modułów z poleceniami cmdlet, które umożliwiają zarządzanie zasobami platformy Azure przy użyciu programu Windows PowerShell.

### <a name="azure-portal"></a>Azure Portal

Azure portal to aplikacji sieci web, która służy do tworzenia, zarządzania i usuwać zasoby platformy Azure i usługi. Witryna Azure portal znajduje się w <https://portal.azure.com>. Obejmuje dostosowywany pulpit nawigacyjny, narzędzia do zarządzania zasobami platformy Azure i uzyskać dostęp do ustawień subskrypcji oraz informacji dotyczących rozliczeń. Aby uzyskać więcej informacji, zobacz [Omówienie witryny Azure portal](../../azure-portal-overview.md).

### <a name="rest-apis"></a>Interfejsy API REST

Jest ona oparta na zestaw interfejsów API REST, która obsługuje interfejs użytkownika witryny Azure portal. Większość tych interfejsów API REST są również obsługiwane umożliwia programowe obsługi administracyjnej i zarządzanie zasobami platformy Azure i aplikacji z dowolnego urządzenia z włączoną funkcją łączności internetowej. Aby uzyskać pełny zestaw dokumentacji interfejsu API REST, zobacz [odwołanie do zestawu SDK REST platformy Azure](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>Interfejsy API

Oprócz interfejsów API REST wielu usług platformy Azure pozwalają również programowe zarządzanie zasobami z poziomu aplikacji przy użyciu zestawów SDK platformy Azure specyficzne dla platformy, m.in. zestawy SDK dla następujących platform programowania:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

Usługi takie jak [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) i [usługi Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) zestawy SDK po stronie klienta, co pozwala zapewnić dostęp do usług sieci web i aplikacje klienta mobilnego.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Uruchamianie aplikacji na platformie Azure, prawdopodobnie wiąże się z pracą z wieloma usługami platformy Azure, z których wszystkie postępuj zgodnie z takim samym cyklem życia i można traktować jako jednostki logicznej. Na przykład aplikacji sieci web może użyć aplikacji sieci Web i bazy danych SQL Database, Storage, pamięć podręczna systemu Azure dla usługi Redis i usługi Azure Content Delivery Network. [Usługa Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) umożliwia pracę z zasobami w aplikacji jako grupą. Można wdrożyć, zaktualizować lub usunąć wszystkie zasoby w jednej, skoordynowanej operacji.

Oprócz logicznego grupowania i zarządzania zasobami powiązane, usługi Azure Resource Manager obejmuje możliwości wdrażania, które umożliwiają dostosowanie wdrażania i konfigurowania powiązane zasoby. Na przykład za pomocą usługi Resource Manager, możesz wdrożyć i skonfigurować aplikację, która składa się z wielu maszyn wirtualnych, moduł równoważenia obciążenia i usługi Azure SQL database jako pojedyncza jednostka.

Możesz tworzyć te wdrożenia przy użyciu szablonu usługi Azure Resource Manager, czyli dokumentu w formacie JSON. Szablony umożliwiają definiowanie wdrożenia aplikacje i zarządzaj nimi przy użyciu szablonów deklaratywnych zamiast skryptów. Szablony można następnie używać w różnych środowiskach, takich jak testowania, przemieszczania i produkcji. Na przykład za pomocą szablonów, można dodać przycisk do repozytorium GitHub, który wdraża kod w repozytorium zestawu usług platformy Azure za pomocą jednego kliknięcia.

> **Kiedy należy używać**: Użyj szablonów usługi Resource Manager, wdrożenia oparte na szablonach dla twojej aplikacji, w którym można zarządzać programowo przy użyciu interfejsów API REST, wiersza polecenia platformy Azure i programu Azure PowerShell.
> 
> **Rozpoczynanie pracy**: Aby rozpocząć pracę, przy użyciu szablonów, zobacz [tworzenia usługi Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Omówienie kont, subskrypcji i rozliczeń

Jako programiści chcemy rzucić się na głęboką do kodu, a następnie spróbuj zacząć tak szybko, jak to możliwe w tworzeniu naszych działają aplikacje. Na pewno chcesz zachęcamy do rozpoczęcia pracy na platformie Azure równie łatwo, jak to możliwe. Aby sprawić, że jej łatwy w użyciu i platformy Azure oferuje [bezpłatna wersja próbna](https://azure.microsoft.com/free/). Niektóre usługi nawet mieć "Wypróbuj ją bezpłatnie" funkcje, takie jak [usługi Azure App Service](https://tryappservice.azure.com/), które nie wymagają nawet utworzyć konto. Zabawa ponieważ jest od razu do programowania i wdrażania aplikacji na platformie Azure, jest również ważne, aby zająć trochę czasu, aby zrozumieć, jak platforma Azure współpracuje z punktu widzenia użytkownika kont, subskrypcji i rozliczeń.

### <a name="what-is-an-azure-account"></a>Co to jest konto platformy Azure?

Aby można było utworzyć lub pracy z subskrypcją platformy Azure, musi mieć konto platformy Azure. Konto platformy Azure jest po prostu tożsamości w usłudze Azure AD, lub w katalogu, np. firmowego lub szkolnego organizacji, który jest zaufany przez usługę Azure AD. Jeśli nie należysz do tej organizacji, możesz zawsze utworzyć subskrypcję, za pomocą Account firmy Microsoft, co jest zaufany przez usługę Azure AD. Aby dowiedzieć się więcej na temat integracji usługi Active Directory lokalnego systemu Windows Server z usługą Azure AD, zobacz [integrowanie tożsamości lokalnych z usługą Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Każda subskrypcja platformy Azure jest połączona relacją zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Aby dowiedzieć się więcej, zobacz [jak subskrypcje platformy Azure są skojarzone z usługą Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Oprócz definiowania Azure indywidualne konta tożsamości, nazywany również *użytkowników*, można również definiować *grup* w usłudze Azure AD. Tworzenie grupy użytkowników jest dobrym sposobem na zarządzanie dostępem do zasobów w subskrypcji przy użyciu kontroli dostępu opartej na rolach (RBAC). Aby dowiedzieć się, jak tworzyć grupy, zobacz [utworzyć grupę w usłudze Azure Active Directory w wersji zapoznawczej](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Można również tworzyć i zarządzać grupami przez [przy użyciu programu PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Zarządzanie subskrypcjami

Subskrypcji to logiczna grupa usług platformy Azure, która jest połączona z kontem platformy Azure. Jedno konto platformy Azure może zawierać wiele subskrypcji. Opłaty za usługi platformy Azure odbywa się na podstawie każdej subskrypcji. Aby uzyskać listę ofert dostępnych subskrypcji według typu, zobacz [szczegółach oferty Azure Microsoft](https://azure.microsoft.com/support/legal/offer-details/). Subskrypcje platformy Azure ma Administrator konta, który ma pełną kontrolę nad subskrypcją, a Administrator usługi, kto ma kontrolę nad wszystkich usług w ramach subskrypcji. Aby uzyskać informacji na temat klasyczni Administratorzy, zobacz [Administratorzy subskrypcji platformy Azure Dodaj lub zmień](../../billing/billing-add-change-azure-subscription-administrator.md). Oprócz Administratorzy, mogą być udzielane indywidualnych kont szczegółową kontrolę zasobów platformy Azure przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Grupy zasobów

Podczas aprowizowania nowych usług platformy Azure, możesz to zrobić w ramach danej subskrypcji. Poszczególnych usług platformy Azure, które są również nazywane zasoby są tworzone w ramach grupy zasobów. Grupy zasobów ułatwiają wdrażanie i zarządzanie zasobami aplikacji. Grupa zasobów może zawierać wszystkie zasoby dla aplikacji, którą chcesz pracować jako jednostka. Zasoby można przenosić między grupami zasobów, a nawet różnych subskrypcjach. Aby uzyskać informacje dotyczące przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../resource-group-move-resources.md).

Usługi Azure Resource Explorer to doskonałe narzędzie do wizualizacji zasoby, które zostały utworzone w ramach subskrypcji. Aby dowiedzieć się więcej, zobacz [użycia usługi Azure Resource Explorer do wyświetlania i modyfikowania zasobów](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Udzielanie dostępu do zasobów

Jeśli zezwolisz na dostęp do zasobów platformy Azure, zawsze jest najlepszym rozwiązaniem, aby zapewnić użytkownikom najniższych uprawnień, które są wymagane do wykonywania danego zadania.

- **Kontrola dostępu oparta na rolach (RBAC)**: Na platformie Azure, możesz udzielić dostępu do kont użytkowników (jednostki) w określonym zakresie: subskrypcji, grupy zasobów lub poszczególnych zasobów. Kontrola RBAC umożliwia wdrażanie zestaw zasobów w grupie zasobów i przyznawanie uprawnień do określonego użytkownika lub grupy. Umożliwia on również ograniczanie dostępu do zasobów, które należą do docelowej grupy zasobów. Można również przyznać dostęp do jednego zasobu, np. maszyny wirtualnej lub sieci wirtualnej. Aby udzielić dostępu, przypisywanie roli do użytkownika, grupy lub jednostki usługi. Istnieje wiele wstępnie zdefiniowanych ról, a można również definiować własne niestandardowe role. Aby dowiedzieć się więcej, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC)?](../../role-based-access-control/overview.md).

  > **Kiedy należy używać**: Kiedy należy precyzyjne zarządzanie dostępem dla użytkowników i grup lub należy ustawić użytkownika jako właściciela, subskrypcji.
  > 
  > **Rozpoczynanie pracy**: Aby dowiedzieć się więcej, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).

- **Usługa obiekty główne**: Oprócz zapewniania dostępu do podmiotów zabezpieczeń użytkowników i grup, można przyznać taki sam dostęp do jednostki usługi.

  > **Kiedy należy używać**: Jeśli one programowo zarządzania zasobami platformy Azure lub udzielania dostępu do aplikacji. Aby uzyskać więcej informacji, zobacz [aplikacji Utwórz usługi Active Directory i jednostki usługi](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Tagi

Usługa Azure Resource Manager pozwala przypisać znaczniki niestandardowe do poszczególnych zasobów. Tagi, które są pary klucz wartość, może być przydatne, gdy trzeba zorganizować zasoby dotyczące rozliczeń lub monitorowania. Tagi umożliwiają możesz śledzić zasoby w wielu grupach zasobów. Można przypisać tagi w portalu, w szablonie usługi Azure Resource Manager lub programowo, za pomocą interfejsu API REST, wiersza polecenia platformy Azure lub programu PowerShell. Wiele tagów można przypisać do każdego zasobu. Aby dowiedzieć się więcej, zobacz [porządkowanie zasobów na platformie Azure za pomocą tagów](../../resource-group-using-tags.md).

### <a name="billing"></a>Rozliczenia

W przeniesienie ze środowiska lokalnego w środowisku usług hostowanych w chmurze śledzenia i Szacowanie usługi użycia i kosztów związanych są istotne kwestie. Jest ważne można było oszacować nowe zasoby kosztów do uruchamiania co miesiąc. Należy także być w stanie projektu, jak wygląda rozliczeń w danym miesiącu, w oparciu o bieżących wydatków.

#### <a name="get-resource-usage-data"></a>Pobieranie danych użycia zasobów

System Azure oferuje zestaw rozliczeń interfejsów API REST, które udzielić dostępu do zużycia zasobów i informacji o metadanych dla subskrypcji platformy Azure. Te interfejsy API rozliczeń zapewniają możliwość lepiej przewiduj i zarządzania kosztami platformy Azure. Można śledzić i analizować wydatków z podziałem na godziny, tworzyć alerty wydatków i przewidywania przyszłych rozliczeń na podstawie bieżącego trendów użycia.

>**Rozpoczynanie pracy**: Aby dowiedzieć się więcej na temat korzystania z interfejsów API rozliczeń, zobacz [API RateCard i użycie rozliczeń platformy Azure — omówienie](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Przewidywanie przyszłych kosztów

Mimo że jest trudne do szacowania kosztów wcześniej, platforma Azure ma [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) czy można używać podczas szacowania kosztów wdrożone zasoby. Blok rozliczeń w portalu i interfejsów API REST rozliczeń umożliwia również oszacować przyszłości oszczędność kosztów, w zależności od bieżącego użycia.

>**Rozpoczynanie pracy**: Zobacz [API RateCard i użycie rozliczeń platformy Azure — omówienie](../../billing-usage-rate-card-overview.md).
