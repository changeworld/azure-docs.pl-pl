---
title: Get przewodnika dla deweloperów na platformie Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera ważne informacje dla deweloperów chcących rozpocząć korzystanie z platformy Microsoft Azure dla swoich potrzeb.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: 3d4b93a604170cb3b8396562a15483923330f467
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754684"
---
# <a name="get-started-guide-for-azure-developers"></a>Przewodnik dla początkujących deweloperów platformy Azure

## <a name="what-is-azure"></a>Co to jest system Azure?

Azure to platforma pełnej chmury hosta istniejących aplikacji, upraszcza programowanie nowych aplikacji i nawet zwiększenia lokalnych aplikacji. Azure integruje usługi w chmurze, które trzeba tworzenie, testowanie, wdrażania i zarządzania aplikacji — wykorzystując efektywność chmury obliczeniowej.

Obsługując aplikacji na platformie Azure, możesz rozpoczęcie od czegoś małego i łatwego skalowania aplikacji wraz z rozwojem Twoje żądanie klienta. System Azure oferuje również niezawodność potrzebne dla aplikacji wysokiej dostępności, nawet w tym tryb failover między różnych regionach. [Portalu Azure](https://portal.azure.com) umożliwia łatwe zarządzanie wszystkich usług platformy Azure. Usługi można również zarządzać programowo przy użyciu interfejsów API i szablony specyficzne dla usługi.

**Kto powinien przeczytać**: ten przewodnik jest wprowadzenie do platformy Azure dla deweloperów aplikacji. Zawiera również wskazówki i kierunku, w którym należy rozpocząć tworzenie nowej aplikacji na platformie Azure lub Migrowanie istniejących aplikacji na platformie Azure.

## <a name="where-do-i-start"></a>Od czego zacząć?

Wszystkie usługi Azure oferuje może być lada wyzwanie, aby dowiedzieć się, których usług wymaganych do obsługi architektury rozwiązania. W tej sekcji opisano często używających deweloperów usług Azure. Aby uzyskać listę wszystkich usług platformy Azure, zobacz [dokumentacji platformy Azure](../../index.md).

Najpierw należy zdecydować, na temat sposobu hostowania aplikacji na platformie Azure. Trzeba zarządzać całej infrastruktury jako maszynę wirtualną (VM). Można użyć funkcji zarządzania platformy, które platforma Azure udostępnia? Może być konieczne niekorzystającą framework do hosta wyłącznie wykonania kodu?

Aplikacja wymaga magazynu w chmurze, która Azure zapewnia kilka opcji. Można korzystać z uwierzytelniania enterprise platformy Azure. Dostępne są również narzędzia do programowania opartego na chmurze i monitorowania i większość usług hostingu oferują DevOps integracji.

Teraz przyjrzymy określonych usług, które są zalecane do badania dla aplikacji.

### <a name="application-hosting"></a>Obsługa aplikacji

Azure zapewnia kilka oparte na chmurze obliczeniowe ofert do uruchamiania aplikacji, dzięki czemu nie trzeba martwić się o szczegóły infrastruktury. Możesz łatwo skalowanie w górę lub skalowania zasobów wraz z rozwojem korzystania z aplikacji.

System Azure oferuje usług, które obsługują programowania aplikacji i hostingu potrzeb. Platforma Azure udostępnia infrastrukturę jako usługę (IaaS), aby mieć pełną kontrolę nad hosting aplikacji. Platforma Azure jako ofert usługi (PaaS) świadczenia usług pełni zarządzana potrzebne do aplikacji. Istnieje nawet true niekorzystającą hostingu na platformie Azure gdzie to wszystko, co należy zrobić, wpisz swój kod.

![Hosting Opcje aplikacji Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Ścieżka najszybszy publikowanie projektów sieci web, należy wziąć pod uwagę usłudze Azure App Service. Usługi aplikacji ułatwia to rozszerzenie aplikacji sieci web do obsługi klientów mobilnych i opublikować łatwo wykorzystanych interfejsów API REST. Ta platforma umożliwia uwierzytelnianie przy użyciu dostawców sieci społecznościowych, na podstawie ruchu Skalowanie automatyczne testowanie w produkcji i wdrożeń ciągły i na podstawie kontenera.

Można utworzyć aplikacji sieci web, zaplecza aplikacji mobilnych i aplikacji API apps.

Ponieważ wszystkie typy aplikacji trzy udział środowiska uruchomieniowego usługi aplikacji, można hosta witryny sieci Web, obsługę klientów mobilnych i ujawnia swoje interfejsy API na platformie Azure, z tego samego projektu lub rozwiązania. Aby dowiedzieć się więcej na temat usługi aplikacji, zobacz [co to jest Azure Web Apps](../../app-service/app-service-web-overview.md).

Usługi aplikacji został zaprojektowany z DevOps zdanie. Obsługuje ona różnych narzędzi do publikowania, jak i ciągłych wdrożeń integracji, w tym GitHub elementów webhook, Wpięć usługi Visual Studio Team Services, TeamCity i inne.

Przeprowadzić migrację istniejących aplikacji do usługi App Service przy użyciu [narzędzia migracji w trybie online](https://www.migratetoazure.net/).

>**Kiedy należy używać**: Użyj podczas migracji istniejącej usługi aplikacji sieci web aplikacji na platformie Azure, a jeśli potrzebujesz pełni zarządzana Platforma macierzysta dla aplikacji sieci web. Usługi aplikacji można użyć również, gdy potrzebne do obsługi klientów mobilnych lub uwidacznia interfejsów API REST z aplikacją.

>**Rozpoczynanie pracy**: App Service ułatwia tworzenie i wdrażanie pierwszej [aplikacji sieci web](../../app-service/app-service-web-get-started-dotnet.md), [aplikacji mobilnej](../../app-service-mobile/app-service-mobile-ios-get-started.md), lub [aplikacji interfejsu API](../../app-service/app-service-web-tutorial-rest-api.md).

>**Wypróbuj teraz**: Usługa App Service umożliwia udostępnianie aplikację o krótkim okresie próby platformy bez zalogowania się do konta platformy Azure. Spróbuj platformy i [Utwórz aplikację usługi aplikacji Azure](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Jako infrastruktura jako usługa (IaaS) dostawcy usługi Azure pozwala wdrożyć lub migracji aplikacji systemu Windows lub maszyn wirtualnych systemu Linux. Wraz z sieci wirtualnej platformy Azure maszyny wirtualne Azure obsługuje do wdrażania systemu Windows lub maszyn wirtualnych systemu Linux na platformie Azure. Z maszynami wirtualnymi masz pełną kontrolę nad konfiguracji komputera. Podczas korzystania z maszyn wirtualnych, wszystko jest odpowiedzialny za serwer instalacji, konfiguracji, obsługi i systemu operacyjnego poprawek oprogramowania.

Ze względu na poziom kontroli maszyn wirtualnych można uruchomić wielu obciążeń serwera na platformie Azure, która nie pasuje do modelu PaaS. Takie obciążenia zawierają serwery baz danych, usługi Active Directory systemu Windows Server i Microsoft SharePoint. Aby uzyskać więcej informacji, zobacz dokumentację maszyn wirtualnych albo [Linux](/azure/virtual-machines/linux/) lub [Windows](/azure/virtual-machines/windows/).

>**Kiedy należy używać**: Użyj maszyn wirtualnych, gdy mają pełną kontrolę nad za pośrednictwem infrastruktury aplikacji i przeprowadzić migrację obciążeń aplikacji lokalnych do platformy Azure bez wprowadzania zmian.

>**Rozpoczynanie pracy**: tworzenie [maszyny Wirtualnej systemu Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) lub [maszyny Wirtualnej systemu Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) z portalu Azure.

#### <a name="azure-functions-serverless"></a>Funkcje platformy Azure (bez serwera)

A nie z martwiąc się o tworzenie wychodzących i zarządzanie nią całą aplikację ani infrastrukturę do uruchomienia kodu. Co zrobić, jeśli można po prostu wpisz swój kod i go uruchomić w odpowiedzi na zdarzenia lub zgodnie z harmonogramem?  [Środowisko Azure Functions](../../azure-functions/functions-overview.md) jest a "niekorzystającą"-oferty stylu, która umożliwia zapisanie tylko kod należy. Za pomocą funkcji wykonywanie kodu zostanie wywołany przez żądania HTTP, elementów webhook, zdarzenia usługi chmury, lub zgodnie z harmonogramem. Można kodu w języku programowania wyboru, takich jak C\#, F\#, Node.js, Python lub PHP. Z na podstawie zużycia rozliczeń, płacisz tylko za czas, który wykonuje kodu i Azure skaluje zgodnie z potrzebami.

>**Kiedy należy używać**: Użyj funkcji Azure, jeśli masz kod, który zostanie wywołany przez innych usług Azure zdarzeń opartych na sieci web, lub zgodnie z harmonogramem. Możesz również użyć funkcji gdy obciążenie kompletnego projektu hostowanej lub gdy chcesz płacić za czas działania kodu nie jest konieczne. Aby dowiedzieć się więcej, zobacz [Azure Functions — omówienie](../../azure-functions/functions-overview.md).

>**Rozpoczynanie pracy**: postępuj zgodnie z funkcji szybkiego startu w samouczku [tworzenie pierwszej funkcji](../../azure-functions/functions-create-first-azure-function.md) z portalu.

>**Wypróbuj teraz**: usługi Azure Functions umożliwia uruchamianie kodu bez zalogowania się do konta platformy Azure. Wypróbuj teraz na i [tworzenie pierwszej funkcji platformy Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Sieć szkieletowa usług Azure to platforma systemów rozproszonych, która ułatwia kompilacji, pakietów, wdrażanie i zarządzanie mikrousług skalowalne i niezawodne. Zapewnia także możliwości zarządzania kompleksowej aplikacji do udostępniania, wdrażanie, monitorowanie, uaktualnianie poprawki i usuwanie wdrożone aplikacje. Aplikacje, które działają w udostępnionej puli maszyn, można rozpoczęcie od czegoś małego i skalować do setek lub tysięcy komputerów, zgodnie z potrzebami.

Sieć szkieletowa usług obsługuje WebAPI z interfejsu Open Web dla platformy .NET (OWIN) i ASP.NET Core. Udostępnia zestawy SDK do tworzenia usług w systemie Linux w .NET Core i Java. Aby dowiedzieć się więcej na temat sieci szkieletowej usług, zobacz [ścieżka szkoleniowa platformy Service Fabric](https://azure.microsoft.com/documentation/learning-paths/service-fabric/).

>**Kiedy należy używać:** usługi sieć szkieletowa jest dobrym rozwiązaniem, podczas tworzenia aplikacji lub ponowne zapisywanie istniejącą aplikację do używania architektury mikrousługi. Jeśli potrzebujesz więcej kontroli nad lub bezpośredni dostęp do podstawowej infrastruktury za pomocą sieci szkieletowej usług.

>**Wprowadzenie:** [tworzenie pierwszej aplikacji usługi Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Ulepszanie aplikacji przy użyciu usług Azure

Oprócz hosting aplikacji, platforma Azure udostępnia oferty usług, zwiększające funkcjonalność, projektowanie i utrzymanie aplikacji, zarówno w chmurze i lokalnie.

#### <a name="hosted-storage-and-data-access"></a>Hostowanej magazynu i dostępem do danych

Większość aplikacji muszą być przechowywane dane, tak niezależnie od tego, jak zdecydujesz się na potrzeby hostowania aplikacji na platformie Azure, należy wziąć pod uwagę jedną lub więcej z następujących usług magazynu i danych.

-   **Azure DB rozwiązania Cosmos**: usługą globalnie rozproszone i wiele modeli bazy danych, która umożliwia elastycznie skalować przepływność i Magazyn przez dowolną liczbę regionów geograficznych z kompleksowe umowy SLA. 
    >**Kiedy należy używać:** gdy aplikacja wymaga dokumentu, tabeli lub baz danych wykresu, w tym baz danych MongoDB, wielu modeli dobrze zdefiniowany spójności. 

    >**Rozpoczynanie pracy**: [tworzenie aplikacji sieci web platformy Azure DB rozwiązania Cosmos](../../cosmos-db/create-sql-api-dotnet.md). Jeśli jesteś deweloperem bazy danych MongoDB, zobacz [tworzenie aplikacji sieci web bazy danych MongoDB z bazy danych Azure rozwiązania Cosmos](../../cosmos-db/create-mongodb-dotnet.md).

-   **Usługa Azure Storage**: oferuje trwałe, wysokiej dostępności magazynu obiektów blob, kolejek, plików oraz innych typów danych nonrelational. Magazynu stanowi podstawę magazynu dla maszyn wirtualnych.

    >**Kiedy należy używać**: gdy aplikacja przechowuje nonrelational dane, takie jak pary klucz wartość (tabele), obiektów blob, udziałów plików lub wiadomości (kolejek).

    >**Rozpoczynanie pracy**: Wybierz jedną z tych typów pamięci masowej: [obiekty BLOB](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabel](../../cosmos-db/table-storage-how-to-use-dotnet.md), [kolejek](../../storage/queues/storage-dotnet-how-to-use-queues.md), lub [pliki](../../storage/files/storage-dotnet-how-to-use-files.md).

-   **Baza danych SQL Azure**: wersja aparatu programu Microsoft SQL Server do przechowywania danych relacyjnych tabelarycznych w chmurze platformy Azure. Baza danych SQL oferuje przewidywalną wydajność, skalowalność bez przestojów, ciągłość prowadzenia działalności biznesowej i ochronę danych.

    >**Kiedy należy używać**: Jeśli aplikacja wymaga magazynu danych z integralności referencyjnej, transakcyjne pomocy i obsługi zapytania TSQL.

    >**Rozpoczynanie pracy**: [Utwórz bazę danych SQL w ciągu minut przy użyciu portalu Azure](../../sql-database/sql-database-get-started.md).


Można użyć [fabryki danych Azure](../../data-factory/introduction.md) przenoszenia istniejących danych lokalnych do platformy Azure. Jeśli nie są gotowe do przeniesienia danych do chmury, [połączeń hybrydowych](../../biztalk-services/integration-hybrid-connection-overview.md) w umożliwia usługi BizTalk Services można połączyć z usługą aplikacji hostowanej aplikacji do zasobów lokalnych. Można również łączysz danych platformy Azure i magazynem usług z aplikacjami lokalnymi.

#### <a name="docker-support"></a>Obsługa docker

Kontenery docker, formę wirtualizacji systemu operacyjnego, umożliwiają wdrażanie aplikacji w sposób bardziej wydajne i przewidywalne. Konteneryzowanych aplikacja działa w środowisku produkcyjnym taki sam sposób jak w używanych systemach prac deweloperskich i testowych. Kontenery można zarządzać przy użyciu standardowych narzędzi Docker. Twoje posiadane umiejętności i popularne narzędzia open source umożliwia wdrażanie aplikacji i zarządzanie nimi na podstawie kontenera na platformie Azure.

Platforma Azure udostępnia kilka sposobów użycia kontenery w aplikacji.

-   **Rozszerzenie Azure Docker VM**: umożliwia skonfigurowanie maszyny Wirtualnej przy użyciu narzędzi platformy Docker na działanie jako Docker host.

    >**Kiedy należy używać**: gdy chcesz wygenerować wdrożeń spójne kontener dla aplikacji na maszynie Wirtualnej lub gdy chcesz użyć [rozwiązania Docker Compose](https://docs.docker.com/compose/overview/).

    >**Rozpoczynanie pracy**: [utworzyć środowisko Docker na platformie Azure przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

-   **Usługa kontenera platformy Azure**: umożliwia tworzenie, konfigurowanie i Zarządzanie klastrem maszyn wirtualnych, które są wstępnie skonfigurowane do uruchamiania aplikacji konteneryzowanych. Aby dowiedzieć się więcej na temat usługi kontenera, zobacz [wprowadzenie usługi kontenera platformy Azure](../../container-service/container-service-intro.md).

    >**Kiedy należy używać**: gdy są potrzebne do tworzenia środowisk gotowe do produkcji, skalowalnej, które zapewniają dodatkowe planowanie i narzędzia do zarządzania, lub gdy wdrażana klaster Docker Swarm.

    >**Rozpoczynanie pracy**: [wdrażanie klastra usługi kontenera](../../container-service/dcos-swarm/container-service-deployment.md).

-   **Maszyna docker**: pozwala instalować i zarządzać aparatem platformy Docker na hostów wirtualnych za pomocą polecenia docker maszyny.

    >**Kiedy należy używać**: kiedy trzeba szybko prototypu aplikację przez utworzenie jednego hosta Docker.

-   **Niestandardowy obraz Docker dla aplikacji usługi**: umożliwia używanie kontenerów Docker z rejestru kontenera lub kontener klienta podczas wdrażania aplikacji sieci web w systemie Linux.

    >**Kiedy należy używać**: w przypadku wdrażania aplikacji sieci web w systemie Linux na obraz Docker.

    >**Rozpoczynanie pracy**: [użyć niestandardowego obrazu Docker dla usługi App Service w systemie Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

Jest niezwykle ważny, nie tylko wiedzieć, kto korzysta z aplikacji, ale także w celu uniemożliwienia nieupoważnionego dostępu do zasobów. Platforma Azure udostępnia kilka metod uwierzytelniania klientów aplikacji.

-   **Azure Active Directory (Azure AD)**: Microsoft wielodostępnym, oparte na chmurze tożsamościami i dostępem Usługa zarządzania. Dzięki integracji z usługą Azure AD, można dodać jednokrotnego (SSO) do aplikacji. Można uzyskać dostępu do właściwości katalogu przy użyciu usługi Azure AD interfejsu API programu Graph bezpośrednio lub interfejsu API programu Microsoft Graph. Za pomocą natywnego punkty końcowe HTTP/REST i biblioteki uwierzytelniania usługi Azure AD multiplatform można zintegrować z obsługą usługi Azure AD OAuth2.0 framework autoryzacji i Open ID Connect.

    >**Kiedy należy używać**: gdy chcesz zapewnić środowisko logowania jednokrotnego, pracować z danymi na podstawie wykres lub uwierzytelnianie oparte na domenie użytkowników.

    >**Rozpoczynanie pracy**: Aby dowiedzieć się więcej, zobacz [przewodnik dewelopera usługi Azure Active Directory](../../active-directory/develop/active-directory-developers-guide.md).

-   **Uwierzytelnianie usługi aplikacji**: po wybraniu aplikacji usługi do obsługi aplikacji umożliwia również wyświetlenie obsługę wbudowanego uwierzytelniania za pomocą usługi Azure AD, wraz z dostawców tożsamości społecznościowych — łącznie z usługi Facebook, Google, Microsoft i Twitter.

    >**Kiedy należy używać**: Jeśli chcesz włączyć uwierzytelnianie w aplikacji usługi app Service przy użyciu usługi Azure AD, dostawców tożsamości społecznościowych lub oba.

    >**Rozpoczynanie pracy**: Aby dowiedzieć się więcej na temat uwierzytelniania w usłudze App Service, zobacz [uwierzytelnianie i autoryzację w usłudze Azure App Service](../../app-service/app-service-authentication-overview.md).

Aby dowiedzieć się więcej o najlepszych rozwiązaniach zabezpieczeń w usłudze Azure, zobacz [Azure najlepsze rozwiązania i wzorce](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorowanie

Aplikację w górę i działających na platformie Azure należy monitorować wydajność, obejrzyj problemów i sprawdzić, jak klienci korzystają z aplikacji. Platforma Azure oferuje kilka możliwości monitorowania.

-   **Visual Studio Application Insights**: usługi analytics extensible hostowanymi na platformie Azure, która integruje się z programem Visual Studio do monitorowania aplikacji sieci web na żywo. Udostępnia dane, które należy ciągle je usprawniać, wydajność oraz poziom użyteczności aplikacji, czy jest hostowana na platformie Azure lub nie.

    >**Rozpoczynanie pracy**: wykonaj [samouczek usługi Application Insights](../../application-insights/app-insights-overview.md).

-   **Azure Monitor**: usługa, która pomaga zwizualizować, zapytania trasy, archiwizacji i działają na metryki i dzienniki, które są generowane przez infrastrukturę platformy Azure i zasobów. Monitor zawiera widoki danych w portalu Azure i jest jedynym źródłem monitorowania zasobów platformy Azure.
 
    >**Rozpoczynanie pracy**: [Rozpoczynanie pracy z monitorem Azure](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integracja metodyki DevOps

Czy jest obsługa administracyjna maszyn wirtualnych lub publikowania aplikacji sieci web z ciągłej integracji, Azure integruje się z większością popularnych narzędzi do opracowywania oprogramowania. Obsługę narzędzi, takich jak Wpięć, GitHub, Puppet, Chef, TeamCity, Ansible, VSTS i inne osoby może współpracować z narzędzia już mieć i zmaksymalizować istniejącego środowiska.

>**Wypróbuj teraz:** [kilka integracji DevOps wypróbuj](https://azure.microsoft.com/try/devops/).

>**Rozpoczynanie pracy**: Aby wyświetlić opcje opracowywania oprogramowania dla aplikacji usługi app Service, zobacz [ciągłe wdrażanie w usłudze Azure App Service](../../app-service/app-service-continuous-deployment.md).


## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Azure to platforma chmury globalnej, która jest ogólnie dostępna w wielu regionach na całym świecie. Podczas obsługi administracyjnej usługi, aplikacji lub maszyny Wirtualnej na platformie Azure, zostanie wyświetlona prośba o wybierz region, reprezentujący określonego centrum danych, którym jest uruchomiona aplikacja lub gdy dane są przechowywane. Tych regionów odpowiadają w określonych lokalizacjach, które są publikowane w [regiony platformy Azure](https://azure.microsoft.com/regions/) strony.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Wybierz region najlepsze dla aplikacji i danych

Jedną z korzyści wynikające ze stosowania Azure jest wdrożenie aplikacji do różnych centrów danych na całym świecie. Obszarze, w którym możesz wybrać mogą wpływać na wydajność aplikacji. Na przykład warto wybrać region zbliżonej do większości klientów do zmniejszenia opóźnień w żądaniach sieci. Można również wybrać obszar w celu spełnienia wymagań prawnych dystrybucji aplikacji w niektórych krajach. Zawsze jest najlepszym rozwiązaniem jest przechowywanie danych aplikacji, w tym samym centrum danych lub w centrum danych jak blisko możliwie najbliżej do centrum danych hostującego aplikację.

### <a name="multi-region-apps"></a>W przypadku aplikacji

Chociaż mało prawdopodobne, nie jest niemożliwe całe centrum danych do przejścia do trybu offline z powodu zdarzenia, takie jak klęski żywiołowej lub awaria internetowego. Jest najlepszym rozwiązaniem, umożliwia obsługę aplikacji ważnych w więcej niż jednego centrum danych do zapewnienia dostępności maksymalna. Przy użyciu wielu regionach można zmniejszenia opóźnień użytkowników globalne i zapewniają dodatkowe możliwości elastyczność podczas aktualizacji aplikacji.

Niektóre usługi, takie jak maszyny wirtualne i usługi aplikacji, użyj [usługi Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) można włączyć obsługę wielu region z trybem failover między regionami do obsługi aplikacji przedsiębiorstwa wysokiej dostępności. Na przykład zobacz [architektura referencyjna Azure: uruchomienia aplikacji sieci web w wielu regionach](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Kiedy należy używać**: Jeśli masz aplikacje przedsiębiorstwa o wysokiej dostępności, korzystających z trybu failover i replikacji.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Jak zarządzać Moje aplikacje i projektów

Platforma Azure oferuje bogaty zestaw środowiska do tworzenia i zarządzania zasobami Azure, aplikacji i projekty — zarówno programowo i [portalu Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfejsy wiersza polecenia i programu PowerShell

Platforma Azure udostępnia dwa sposoby zarządzania usług i aplikacji z poziomu wiersza polecenia przy użyciu Bash, terminali, wiersza polecenia lub narzędzie wiersza polecenia wyboru. Zwykle, można wykonać te same zadania z poziomu wiersza polecenia, jak w portalu Azure, takich jak tworzenie i konfigurowanie maszyn wirtualnych, sieci wirtualnych, aplikacje sieci web i innych usług.

-   [Azure interfejsu wiersza polecenia (CLI)](../../xplat-cli-install.md): umożliwia połączenie z subskrypcją platformy Azure i programu różne zadania przed zasobów platformy Azure z poziomu wiersza polecenia.

-   [Program Azure PowerShell](../../powershell-install-configure.md): udostępnia zestaw modułów za pomocą poleceń cmdlet, które umożliwiają zarządzanie zasobami platformy Azure przy użyciu programu Windows PowerShell.

### <a name="azure-portal"></a>Azure Portal

Azure portal to aplikacji sieci web, który służy do tworzenia, zarządzania i usuwać zasoby platformy Azure i usługi. Portalu Azure znajduje się pod adresem <https://portal.azure.com>. Obejmuje on można dostosować pulpit nawigacyjny, narzędzia do zarządzania zasobami platformy Azure i uzyskać dostęp do ustawień subskrypcji i informacji dotyczących rozliczeń. Aby uzyskać więcej informacji, zobacz [omówienie portalu Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>Interfejsy API REST

Azure jest oparty na zestaw interfejsów API REST, który obsługuje interfejsu użytkownika portalu Azure. Większość tych interfejsów API REST są również obsługiwane pozwala programowo udostępniania i zarządzania zasobami Azure i aplikacji z dowolnego urządzenia internetowe. Aby uzyskać pełny zestaw dokumentacji interfejsu API REST, zobacz [odwołania do zestawu SDK REST Azure](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>Interfejsy API

Oprócz interfejsów API REST wielu usług Azure pozwalają również programowego zarządzania zasobów z aplikacji przy użyciu specyficzne dla platformy Azure SDK, w tym zestawy SDK dla następujących platform programowanie:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

Usług takich jak [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) i [usługi Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) Podaj zestawów SDK klienta, aby umożliwić dostęp do usług sieci web i aplikacji klientów urządzeń przenośnych.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Uruchamianie aplikacji na platformie Azure mogą obejmuje pracę z wieloma usługami Azure, które należy wykonać tego samego cyklu życia i można traktować jako jednostki logicznej. Na przykład aplikacja sieci web może użyć aplikacji sieci Web, bazy danych SQL, magazynu, pamięć podręczna Redis Azure i usługi Azure Content Delivery Network. [Usługa Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) umożliwia pracę z zasobami w aplikacji jako grupa. Można wdrożyć, zaktualizować lub usunąć wszystkie zasoby w jednej, skoordynowanej operacji.

Oprócz logicznego grupowania zasobów i zarządzania nimi powiązane, usługi Azure Resource Manager obejmuje możliwości wdrożenia, które pozwalają dostosować ustawienia wdrażania i konfiguracji powiązane zasoby. Można na przykład za pomocą Menedżera zasobów, wdrażanie i konfigurowanie aplikacji, która składa się z wielu maszyn wirtualnych, usługi równoważenia obciążenia i bazy danych Azure SQL jako pojedyncza jednostka.

Te wdrożenia jest opracowanie przy użyciu szablonu usługi Azure Resource Manager, który jest dokumentu w formacie JSON. Szablony umożliwiają definiowanie wdrożenia i Zarządzaj aplikacjami za pomocą szablonów deklaratywnych zamiast skryptów. Szablonów można następnie używać w różnych środowiskach, takich jak testowania, przemieszczania i produkcji. Na przykład przy użyciu szablonów, należy dodać przycisk do repozytorium GitHub, która wdraża kod w repozytorium do zestawu usług platformy Azure za pomocą jednego kliknięcia.

>**Kiedy należy używać**: szablonów Użyj Resource Manager, należy na podstawie szablonu wdrożenia dla aplikacji, którą można zarządzać programowo przy użyciu interfejsów API REST, wiersza polecenia platformy Azure i programu Azure PowerShell.

>**Rozpoczynanie pracy**: Aby rozpocząć korzystanie z szablonów, zobacz [szablonów Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Opis kont, subskrypcje i rozliczeń

Jako deweloperów firma Microsoft, takich jak prawo Poznaj kod i spróbuj rozpocząć tak szybko jak to możliwe w tworzeniu naszych działają aplikacje. Na pewno chcemy się rozpocząć pracę w prosty sposób na platformie Azure. Aby pomóc oferuje łatwe, Azure [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/). Niektóre usługi tak samo, jak nawet korzystać z funkcji "Wypróbuj bezpłatnie" [usłudze Azure App Service](https://tryappservice.azure.com/), która nie wymaga nawet utworzyć konto. Jako fun jako Poznaj kodowania i wdrażanie aplikacji na platformie Azure, należy także zająć trochę czasu, aby zrozumieć, jak Azure działa z punktu widzenia kont użytkowników, subskrypcje i rozliczeń.

### <a name="what-is-an-azure-account"></a>Co to jest konto platformy Azure?

Aby można było utworzyć lub pracy z subskrypcją platformy Azure, musi mieć konto platformy Azure. Konto platformy Azure jest po prostu tożsamości w usłudze Azure AD lub w katalogu, takie jak służbowego organizacji, który jest zaufany przez usługę Azure AD. Jeśli użytkownik nie należy do tej organizacji, zawsze można utworzyć subskrypcji przy użyciu Account Microsoft, który jest zaufany przez usługę Azure AD. Aby dowiedzieć się więcej na temat integracji lokalnego systemu Windows Server Active Directory z usługą Azure AD, zobacz [integrowanie tożsamości lokalnych z usługą Azure Active Directory](../../active-directory/active-directory-aadconnect.md).

Każda subskrypcja platformy Azure jest połączona relacją zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Aby dowiedzieć się więcej, zobacz [jak subskrypcje platformy Azure są kojarzone z usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Oprócz Definiowanie poszczególnych Azure konta tożsamości, nazywany również *użytkowników*, można również zdefiniować *grup* w usłudze Azure AD. Tworzenie grup użytkowników jest sposób zarządzania dostępem do zasobów w subskrypcji przy użyciu kontroli dostępu opartej na rolach (RBAC). Aby dowiedzieć się, jak tworzyć grupy, zobacz [utworzyć grupę w wersji zapoznawczej usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Można również tworzyć i zarządzania grupami przez [przy użyciu programu PowerShell](../../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Zarządzanie subskrypcjami

Subskrypcja jest jednostki logicznej usług platformy Azure, która jest połączona z kontem platformy Azure. Każdy skojarzone konto ma rolę w ramach subskrypcji. Rozliczenia dotyczące usługi Azure odbywa się na zasadzie dla subskrypcji. Lista ofert dostępnych subskrypcji według typu, zobacz [szczegółach oferty usługi Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/).

#### <a name="administrator-roles"></a>Role administratora

Subskrypcja platformy Azure ma wiele ról administratora konta, które można przypisać w dowolnym momencie.

-   **Konto administratora**: Ta rola ma pełną kontrolę nad subskrypcji i to konto, które jest odpowiedzialny za rozliczeń.

-   **Administrator usługi**: Ta rola ma kontrolę nad wszystkie usługi w subskrypcji. Domyślnie jest to samo konto jako konto administratora.

-   **Współadministratorem**: Ta rola ma takie same prawa dostępu jako Administrator usługi z tą różnicą, że skojarzenie subskrypcji nie może zmieniać się do usługi Azure directory.

Aby dowiedzieć się więcej o rolach administratora, zobacz [jak dodać lub zmienić role administratora platformy Azure](../../billing/billing-add-change-azure-subscription-administrator.md#add-an-admin-for-a-subscription).

#### <a name="resource-groups"></a>Grupy zasobów

Podczas obsługi administracyjnej nowych usług Azure, możesz to zrobić w ramach danej subskrypcji. Poszczególnych usług Azure, które są nazywane również zasobów, są tworzone w ramach grupy zasobów. Grupy zasobów ułatwiają wdrażanie i zarządzanie zasobami aplikacji. Grupa zasobów powinna zawierać wszystkie zasoby aplikacji, które chcesz używać jako jednostka. Zasoby można przenosić między grupami zasobów, a nawet do różnych subskrypcji. Aby dowiedzieć się więcej na temat przenoszenia zasobów, zobacz [przenoszenia zasobów do nowej grupy zasobów lub subskrypcji](../../resource-group-move-resources.md).

Eksploratora zasobów Azure to doskonałe narzędzie do wizualizacji zasoby, które zostały już utworzone w ramach subskrypcji. Aby dowiedzieć się więcej, zobacz [Eksploratora zasobów Azure używana do wyświetlania i modyfikowania zasobów](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Udziel dostępu do zasobów

Jeśli zezwolisz na dostęp do zasobów platformy Azure, zawsze jest najlepszym rozwiązaniem, aby zapewnić użytkownikom najniższych uprawnień, które są wymagane do wykonania określonego zadania.

-   **Kontrola dostępu oparta na rolach (RBAC)**: W usłudze Azure można przyznać dostęp do kont użytkowników (główne) w określonym zakresie: subskrypcji, grupy zasobów lub pojedynczych zasobów. RBAC umożliwia wdrażanie zestaw zasobów w grupie zasobów i udzielanie uprawnień do określonego użytkownika lub grupy. Pozwalają on również ograniczyć dostęp do zasobów, które należą do docelowej grupy zasobów. Można również przyznać dostęp do pojedynczego zasobu, takiego jak sieci wirtualnej lub maszyny wirtualnej. Aby udzielić dostępu, możesz przypisać rolę do użytkownika, grupy lub nazwy głównej usługi. Istnieje wiele wstępnie zdefiniowanych ról, a istnieje również możliwość definiowania własnych niestandardowych ról.

    >**Kiedy należy używać**: Jeśli wymagane jest precyzyjne zarządzanie dostępem dla użytkowników i grup.

    >**Rozpoczynanie pracy**: Aby dowiedzieć się więcej, zobacz [wprowadzenie do zarządzania dostępem w portalu Azure](../../role-based-access-control/overview.md).

-   **Obiekty główne usługi**: oprócz zapewnienia dostępu do podmiotów zabezpieczeń użytkowników i grup, można udzielić dostępu tej samej nazwy głównej usługi.

    > **Kiedy należy używać**: w przypadku gdy programowo zarządzania zasobami platformy Azure lub udzielania dostępu dla aplikacji. Aby uzyskać więcej informacji, zobacz [aplikacji usługi Active Directory Utwórz i nazwę główną usługi](../../resource-group-create-service-principal-portal.md).

#### <a name="tags"></a>Tagi

Usługa Azure Resource Manager można przypisać znaczniki niestandardowe do pojedynczych zasobów. Tagi, które są pary klucz wartość, mogą być przydatne podczas należy zorganizować zasoby dotyczące rozliczeń lub monitorowania. Tagi zapewnia możliwości śledzenia zasobów przez wiele grup zasobów. Można przypisać znaczniki w portalu w szablonie usługi Azure Resource Manager lub modyfikacji programistycznej przy użyciu interfejsu API REST, wiersza polecenia platformy Azure lub programu PowerShell. Wiele tagów można przypisać do każdego zasobu. Aby dowiedzieć się więcej, zobacz [organizowania zasobów na platformie Azure przy użyciu tagów](../../resource-group-using-tags.md).

### <a name="billing"></a>Rozliczenia

W przeprowadzenia migracji z usług hostowanych w chmurze w środowisku lokalnym śledzenia i Szacowanie usługi użycia i koszty są istotne problemy. Jest ważne można było oszacować nowych zasobów kosztu na miesiąc. Należy również mieć możliwość projektu, jak wygląda rozliczeń dla danego miesiąca, w oparciu o bieżący wydatków.

#### <a name="get-resource-usage-data"></a>Pobierz dane o użyciu zasobów

Platforma Azure oferuje zestaw rozliczeń REST interfejsów API, które zapewniają dostęp do zużycia zasobów i informacji o metadanych dla subskrypcji platformy Azure. Te interfejsy API rozliczeń zapewniają możliwość lepiej prognozowania i zarządzać kosztami platformy Azure. Można śledzić i analizować wydatków z podziałem na godziny, tworzyć alerty wydatków i przewidywania przyszłych rozliczeń na podstawie bieżącego trendów użycia.

>**Rozpoczynanie pracy**: Aby dowiedzieć się więcej na temat korzystania z interfejsów API rozliczeń, zobacz [omówienie użycia rozliczenia Azure i interfejsów API RateCard](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Przewidywania przyszłych kosztów

Mimo że utrudnione jest szacowanie kosztów wcześniejsze, platforma Azure ma [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) których można używać podczas szacowania kosztów wdrożonych zasobów. Umożliwia także bloku rozliczenia w portalu i interfejsów API REST rozliczeń oszacowanie kosztów przyszłych, na podstawie bieżącego zużycia.

>**Rozpoczynanie pracy**: zobacz [omówienie użycia rozliczenia Azure i interfejsów API RateCard](../../billing-usage-rate-card-overview.md).

#### <a name="set-up-billing-alerts"></a>Ustawianie alertów dotyczących rozliczeń

Po wdrożeniu aplikacji lub rozwiązania na platformie Azure, można tworzyć alerty, które wysyłają możesz wiadomość e-mail, gdy próbują limitu wydatków, które są zdefiniowane w alercie.

>**Rozpoczynanie pracy**: Aby dowiedzieć się więcej, zobacz [skonfigurować alerty dotyczące subskrypcji platformy Microsoft Azure billing](../../billing-set-up-alerts.md).
