---
title: Porównanie usług App Service, Virtual Machines, Service Fabric i Cloud Services — Azure | Microsoft Docs
description: Dowiedz się, jak wybrać spośród usług Azure App Service, Virtual Machines, Service Fabric i Cloud Services na potrzeby hostowania aplikacji internetowych.
services: app-service\web, virtual-machines, cloud-services
documentationcenter: ''
author: cephalin
manager: jeconnoc
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/07/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: aac2a0b102d50c8d3f0506c2cc1469a838706703
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793859"
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Porównanie usług App Service, Virtual Machines, Service Fabric i Cloud Services

Platforma Azure oferuje kilka sposobów hostowania witryn internetowych: [Azure App Service][Azure App Service], [Virtual Machines][Virtual Machines], [Service Fabric][Service Fabric] i [Cloud Services][Cloud Services]. Ten artykuł ułatwia poznanie dostępnych opcji i dokonanie dobrego wyboru dla aplikacji internetowej.

Usługa Azure App Service to najlepszy wybór w przypadku większości aplikacji internetowych. Wdrażanie i zarządzanie są zintegrowane z platformą, witryny można szybko skalować na potrzeby obsługi dużych obciążeń generowanych przez ruch sieciowy, a wbudowane równoważenie obciążenia i usługa Traffic Manager zapewniają wysoką dostępność. Możesz w ławy sposób przenieść istniejące witryny do usługi Azure App Service za pomocą [narzędzia do migracji online][migrate-tool], użyć aplikacji open source z galerii aplikacji internetowych lub utworzyć nową witrynę za pomocą wybranej struktury i wybranych narzędzi. Zadania [WebJob][ WebJobs] upraszczają dodawanie przetwarzania zadań w tle do aplikacji internetowej usługi App Service.

Usługa Service Fabric to dobry wybór w przypadku tworzenia nowej aplikacji lub ponownego tworzenia istniejącej aplikacji w architekturze mikrousług. Wdrożenie aplikacji działających w ramach współużytkowanej puli maszyn początkowo może być niewielkie i później rosnąć na wielką skalę w razie potrzeby — do setek lub tysięcy maszyn. Usługi stanowe ułatwiają spójne i niezawodne przechowywanie stanu aplikacji, a usługa Service Fabric automatycznie zarządza partycjonowaniem usługi, skalowaniem i dostępnością.  Usługa Service Fabric obsługuje także interfejs WebAPI z interfejsem Open Web Interface for .NET (OWIN) oraz platformę ASP.NET Core.  W porównaniu do usługi App Service usługa Service Fabric udostępnia więcej kontroli nad bazową infrastrukturą oraz bezpośredni dostęp do niej. Możesz zalogować się do serwerów z lokalizacji zdalnej lub skonfigurować zadania uruchamiania serwera. Usługa Cloud Services jest podobna do usługi Service Fabric w zakresie stopnia kontroli i łatwości użycia, lecz teraz jest to starsza usługa i do nowych projektów zaleca się użycie usługi Service Fabric.

Jeśli masz istniejącą aplikację, która wymagałaby istotnych zmian do działania w usłudze App Service lub Service Fabric, możesz wybrać usługę Virtual Machines w celu uproszczenia migracji do chmury. Jednak poprawne skonfigurowanie, zabezpieczenie i konserwowanie maszyn wirtualnych wymaga znacznie więcej czasu i doświadczenia w zakresie IT niż w przypadku usług Azure App Service i Service Fabric. Jeśli planujesz użycie usługi Azure Virtual Machines, uwzględnij przyszłe nakłady na konserwację wymagane do wdrażania poprawek i aktualizowania środowiska maszyn wirtualnych oraz zarządzania nim. Usługa Azure Virtual Machines to infrastruktura jako usługa (IaaS), a usługi App Service i Service Fabric to platformy jako usługa (Paas).

## <a name="features"></a>Porównanie funkcji
W poniższej tabeli porównano możliwości usług App Service, Cloud Services, Virtual Machines i Service Fabric, aby ułatwić dokonanie najlepszego wyboru. Aby uzyskać aktualne informacje na temat umowy SLA dla każdej opcji, zobacz temat [Umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/) dla platformy Azure.

| Cecha | App Service (aplikacje internetowe) | Cloud Services (role Internet) | Maszyny wirtualne | Service Fabric | Uwagi |
| --- | --- | --- | --- | --- | --- |
| Niemal natychmiastowe wdrażanie |X | | |X |Wdrażanie lub aktualizowanie aplikacji w ramach usługi Cloud Service albo tworzenie maszyny wirtualnej trwa co najmniej kilka minut. Wdrożenie aplikacji internetowej trwa sekundy. |
| Skalowanie na większe maszyny bez ponownego wdrażania |X | | |X | |
| Wystąpienia serwera internetowego współużytkują zawartość i konfigurację, co oznacza, że nie trzeba ponownie wdrażać ani ponownie konfigurować podczas skalowania. |X | | |X | |
| Wiele środowisk wdrażania (produkcyjne i przejściowe) |X |X | |X |Usługa Service Fabric umożliwia korzystanie z wielu środowisk na potrzeby aplikacji oraz równoległe wdrożenie różnych wersji aplikacji. |
| Automatyczne zarządzanie aktualizacjami systemu operacyjnego |X |X | | |Częściowe za pomocą aplikacji POA (Patch Orchestration Application) i pełne w przyszłości. |
| Bezproblemowe przełączanie platformy (łatwe przenoszenie między środowiskami 32-bitowymi i 64-bitowymi) |X |X | | | |
| Wdrażanie kodu za pomocą narzędzia GIT i klienta FTP |X | |X | | |
| Wdrażanie kodu za pomocą narzędzia Web Deploy |X | |X | |Usługa Cloud Services obsługuje narzędzie Web Deploy na potrzeby wdrażania aktualizacji do poszczególnych wystąpień roli. Jednak nie można go użyć do początkowego wdrożenia roli, a aktualizowanie za pomocą narzędzia Web Deploy wymaga wdrażania osobno do każdego wystąpienia roli. Wiele wystąpień jest wymaganych do objęcia umową SLA usługi Cloud Service dla środowisk produkcyjnych. |
| Obsługa programu WebMatrix |X | |X | | |
| Dostęp do usług takich jak Service Bus, Storage, SQL Database |X |X |X |X | |
| Hostowanie warstwy internetowej lub usług internetowych na potrzeby architektury wielowarstwowej |X |X |X |X | |
| Hostowanie warstwy środkowej na potrzeby architektury wielowarstwowej |X |X |X |X |Aplikacje internetowe usługi App Service mogą bezproblemowo hostować warstwę środkową interfejsu API REST, a zadania [WebJob](https://go.microsoft.com/fwlink/?linkid=390226) umożliwiają hostowanie zadań przetwarzania w tle. Zadania WebJob możesz uruchamiać w ramach dedykowanej witryny internetowej, aby osiągnąć niezależną skalowalność warstwy. |
| Zintegrowana obsługa programu MySQL jako usługi |X |X | | | |
| Obsługa technologii ASP.NET, klasycznych stron ASP, Node.js, PHP, Python |X |X |X |X |Usługa Service Fabric obsługuje tworzenie frontonu internetowego za pomocą programu [ASP.NET 5](../service-fabric/service-fabric-reliable-services-communication-aspnetcore.md). Możesz także wdrożyć aplikację dowolnego typu (Node.js, Java itp.) jako [plik wykonywalny gościa](../service-fabric/service-fabric-guest-executables-introduction.md). |
| Skalowanie w poziomie do wielu wystąpień bez konieczności ponownego wdrażania |X |X |X |X |Usługa Virtual Machines umożliwia skalowanie w poziomie do wielu wystąpień, lecz usługi działające w niej muszą być napisane pod kątem obsługi takiego skalowania. Należy skonfigurować moduł równoważenia obciążenia, który będzie kierować żądania między maszynami, i upewnić się, że w [zestawie dostępności](../virtual-machines/windows/manage-availability.md) istnieją co najmniej dwa wystąpienia maszyny wirtualnej. |
| Obsługa protokołu SSL |X |X |X |X |W przypadku aplikacji internetowych usługi App Service protokół SSL dla niestandardowych nazw domeny jest obsługiwany tylko w trybach Podstawowy i Standardowy. Informacje na temat używania protokołu SSL z aplikacjami internetowymi zawiera temat [Konfigurowanie certyfikatu SSL dla witryny internetowej platformy Azure](app-service-web-tutorial-custom-ssl.md). |
| Integracja z programem Visual Studio |X |X |X |X | |
| Debugowanie zdalne |X |X |X |X | |
| Wdrażanie kodu za pomocą serwera TFS |X |X |X |X | |
| Izolacja sieciowa za pomocą usługi [Azure Virtual Network](/azure/virtual-network/) |X |X |X |X |Zobacz też temat [Integracja usługi Azure Websites z usługą Virtual Network](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| Obsługa usługi [Azure Traffic Manager](/azure/traffic-manager/) |X |X |X |X | |
| Zintegrowane monitorowanie punktów końcowych |X |X |X | | |
| Dostęp do serwerów za pomocą pulpitu zdalnego | |X |X |X | |
| Instalowanie dowolnego niestandardowego pakietu MSI | |X |X |X |Usługa Service Fabric umożliwia hostowanie dowolnego pliku wykonywalnego jako [pliku wykonywalnego gościa](../service-fabric/service-fabric-guest-executables-introduction.md). Na maszynach wirtualnych możesz zainstalować dowolną aplikację. |
| Możliwość definiowania i wykonywania zadań uruchamiania | |X |X |X | |
| Może nasłuchiwać zdarzeń ETW | |X |X |X | |

## <a name="scenarios"></a>Scenariusze i zalecenia
Poniżej przedstawiono kilka typowych scenariuszy dla aplikacji wraz z zaleceniami co do tego, która opcja hostingu internetowego na platformie Azure może być najbardziej odpowiednia w danym przypadku.

* [Potrzebuję frontonu internetowego z funkcją przetwarzania w tle i bazą danych zaplecza do uruchamiania aplikacji biznesowych zintegrowanych z zasobami lokalnymi.](#onprem)
* [Potrzebuję niezawodnej metody hostowania, która umożliwia efektywne skalowanie i oferuje globalny zasięg, dla mojej internetowej witryny firmowej.](#corp)
* [Mam aplikację usług IIS 6 działającą w systemie Windows Server 2003.](#iis6)
* [Jestem właścicielem małej firmy i potrzebuję niedrogiego sposobu hostowania mojej witryny z możliwością jej rozbudowania w przyszłości.](#smallbusiness)
* [Jestem projektantem internetowym lub grafikiem oraz chcę projektować i tworzyć witryny internetowe dla moich klientów.](#designer)
* [Migruję moją wielowarstwową aplikację z frontonem internetowym do chmury.](#multitier)
* [Moja aplikacja zależy od wysoce dostosowanego środowiska systemu Windows lub Linux i chcę przenieść je do chmury.](#custom)
* [Moja witryna korzysta z oprogramowania open source i chcę ją hostować na platformie Azure.](#oss)
* [Mam aplikację biznesową, która potrzebuję połączenia z siecią firmową.](#lob)
* [Chcę hostować interfejs API REST lub usługę internetową dla klientów mobilnych.](#mobile)

### <a id="onprem"></a>Potrzebuję frontonu internetowego z funkcją przetwarzania w tle i bazą danych zaplecza do uruchamiania aplikacji biznesowych zintegrowanych z zasobami lokalnymi.
Usługa Azure App Service to doskonałe rozwiązanie dla złożonych aplikacji biznesowych. Umożliwia tworzenie aplikacji skalowanych automatycznie na platformie z równoważeniem obciążenia, które są zabezpieczone przy użyciu usługi Active Directory i łączą się z zasobami lokalnymi. Zarządzanie tymi aplikacjami jest łatwe dzięki portalowi światowej klasy i interfejsom API. Istnieje możliwość analizowania ich użycia przez klientów za pomocą narzędzia do analizy aplikacji. [Webjobs] [ Webjobs] umożliwiają uruchamianie procesów w tle i zadań w ramach warstwy internetowej, gdy połączenia hybrydowego i sieci wirtualnej funkcje ułatwiają łączenie z powrotem do zasobów lokalnych. Usługa Azure App Service udostępnia umowę SLA dla aplikacji internetowych typu 999 i umożliwia:

* Niezawodne uruchamianie aplikacji na platformie chmury z możliwościami samonaprawiania i automatycznego stosowania poprawek.
* Automatyczne skalowanie na globalną sieć centrów danych.
* Tworzenie i przywracanie kopii zapasowej na potrzeby odzyskiwania po awarii.
* Zachowanie zgodności ze standardami ISO, SOC2 i PCI.
* Integrację z usługą Active Directory

### <a id="corp"></a>Potrzebuję niezawodnej metody hostowania, która umożliwia efektywne skalowanie i oferuje globalny zasięg, dla mojej internetowej witryny firmowej.
Usługa Azure App Service to doskonałe rozwiązanie do hostowania internetowych witryn firmowych. Umożliwia szybkie i łatwe skalowanie aplikacji internetowych w celu spełnienia wymagań dzięki globalnej sieci centrów danych. Oferuje zasięg lokalny, odporność na uszkodzenia i inteligentne zarządzanie ruchem. Wszystko to na platformie, która udostępnia narzędzia do zarządzania światowej klasy, umożliwiając szybkie i łatwe analizowanie kondycji witryny i generowanego dla niej ruchu. Usługa Azure App Service udostępnia umowę SLA dla aplikacji internetowych typu 999 i umożliwia:

* Niezawodne uruchamianie witryn internetowych na platformie chmury z możliwościami samonaprawiania i automatycznego stosowania poprawek.
* Automatyczne skalowanie na globalną sieć centrów danych.
* Tworzenie i przywracanie kopii zapasowej na potrzeby odzyskiwania po awarii.
* Zarządzanie dziennikami i ruchem za pomocą zintegrowanych narzędzi.
* Zachowanie zgodności ze standardami ISO, SOC2 i PCI.
* Integrację z usługą Active Directory

### <a id="iis6"></a>Mam aplikację usług IIS 6 działającą w systemie Windows Server 2003.
Usługa Azure App Service umożliwia uniknięcie kosztów infrastruktury powiązanych z migrowaniem starszych aplikacji usług IIS 6. Firma Microsoft opracowała [łatwe w użyciu narzędzia do migracji i szczegółowy przewodnik migracji][migrate-tool], które umożliwiają sprawdzenie zgodności i zidentyfikowanie wszelkich zmian, które należy wprowadzić. Integracja z programem Visual Studio, serwerem TFS i popularnymi narzędziami CMS ułatwia wdrażanie aplikacji usług IIS 6 bezpośrednio do chmury. Po wdrożeniu witryna Azure Portal udostępnia niezawodne narzędzia do zarządzania, które umożliwiają skalowanie w dół na potrzeby zarządzania kosztami i w górę odpowiednio do wymagań. Narzędzie do migracji umożliwia:

* Szybkie i łatwe migrowanie starszych aplikacji internetowych dla systemu Windows Server 2003 do chmury.
* Pozostawienie dołączonej bazy danych SQL w lokalizacji lokalnej w celu utworzenia aplikacji hybrydowej.
* Automatyczne przeniesienie bazy danych SQL razem ze starszą aplikacją.

### <a id="smallbusiness"></a>Jestem właścicielem małej firmy i potrzebuję niedrogiego sposobu hostowania mojej witryny z możliwością jej rozbudowania w przyszłości.
Usługa Azure App Service to doskonałe rozwiązanie dla tego scenariusza, ponieważ możesz rozpocząć korzystanie z niej bezpłatnie, a następnie dodać więcej możliwości w miarę potrzeb. Każda bezpłatna aplikacja internetowa jest dostarczana wraz z domeną udostępnianą przez platformę Azure (*twoja_firma*.azurewebsites.net), a ta platforma obejmuje zintegrowane narzędzia do wdrażania i zarządzania, a także galerię aplikacji, która ułatwia rozpoczęcie pracy. Istnieje wiele innych usług i opcji skalowania, które pozwalają witrynie na ewolucję wraz z rosnącymi potrzebami użytkowników. Usługa Azure App Service:

* Zapewnia rozpoczęcie od bezpłatnej warstwy i skalowanie w górę odpowiednio do potrzeb.
* Udostępnia galerię aplikacji na potrzeby szybkiego konfigurowania popularnych aplikacji internetowych, takich jak WordPress.
* Umożliwia dodanie kolejnych usług i funkcji platformy Azure w miarę potrzeb.
* Pozwala na zabezpieczenie aplikacji internetowej za pomocą protokołu HTTPS.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a id="designer"></a>Jestem projektantem internetowym lub grafikiem oraz chcę projektować i tworzyć witryny internetowe dla moich klientów
Dla deweloperów i projektantów internetowych usługa Azure App Service zapewnia łatwą integrację z różnymi platformami i narzędziami, w tym Git i FTP, oraz oferuje ścisłą integrację z narzędziami i usługami takimi jak program Visual Studio i usługa SQL Database. Możliwości zapewniane przez usługę App Service:

* Korzystanie z narzędzi wiersza polecenia na potrzeby [zadań wykonywanych automatycznie][scripting].
* Praca z popularnymi językami, takimi jak [.Net][dotnet], [PHP][PHP], [Node.js][nodejs] i [Python][Python].
* Wybór trzech różnych poziomów skalowania na potrzeby skalowania do bardzo dużych pojemności.
* Integracja z innymi usługami Azure, takimi jak [SQL Database][sqldatabase], [Service Bus][ servicebus] i [Storage][Storage], oraz ofertami partnerów ze sklepu [Azure Store][azurestore], takimi jak programy MySQL i MongoDB.
* Integracja z narzędziami takimi jak Visual Studio, Git, WebMatrix, WebDeploy, TFS i FTP.

### <a id="multitier"></a>Migruję moją aplikację wielowarstwową z frontonem internetowym do chmury
Jeśli używasz aplikacji wielowarstwowej, takiej jak serwer internetowy łączący się z bazą danych, usługa Azure App Service jest dobrym rozwiązaniem, które oferuje ścisłą integrację z usługą Azure SQL Database. Możesz także używać zadań WebJob do uruchamiania procesów zaplecza.

Wybierz usługę Service Fabric dla jednej lub wielu warstw, jeśli potrzebujesz większej kontroli nad środowiskiem serwera, na przykład możliwości połączenia się z serwerem z lokalizacji zdalnej lub skonfigurowania zadań uruchamiania serwera.

Wybierz usługę Virtual Machines dla co najmniej jednej warstwy, jeśli chcesz użyć własnego obrazu maszyny lub uruchamiać oprogramowanie serwerowe lub usługi, których nie można skonfigurować w ramach usługi Service Fabric.

### <a id="custom"></a>Moja aplikacja zależy od wysoce dostosowanego środowiska systemu Windows lub Linux i chcę przenieść je do chmury.
Jeśli aplikacja wymaga złożonej instalacji lub konfiguracji oprogramowania i systemu operacyjnego, najlepszym rozwiązaniem jest prawdopodobnie usługa Virtual Machines. Usługa Virtual Machines umożliwia:

* Użycie galerii usługi Virtual Machines do rozpoczęcia od systemu operacyjnego, takiego jak Windows lub Linux, a następnie dostosowanie go do wymagań aplikacji.
* Utworzenie i przekazanie niestandardowego obrazu istniejącego serwera lokalnego w celu uruchomienia go w ramach maszyny wirtualnej na platformie Azure.

### <a id="oss"></a>Moja witryna korzysta z oprogramowania open source i chcę ją hostować na platformie Azure
Jeśli Twoja platforma open source jest obsługiwana przez usługę App Service, języki i platformy wymagane przez aplikację zostaną skonfigurowane automatycznie. Usługa App Service umożliwia:

* Używanie wielu popularnych języków open source, takich jak [.Net][dotnet], [PHP][PHP], [Node.js][nodejs] i [Python][Python].
* Konfigurowanie aplikacji WordPress, Drupal, Umbraco, DNN i wielu innych aplikacji internetowych różnych firm.
* Zmigrowanie istniejącej aplikacji lub utworzenie nowej za pomocą galerii aplikacji.

Jeśli Twoja platforma open source nie jest obsługiwana przez usługę App Service, możesz uruchomić ją za pomocą jednej z pozostałych opcji hostingu internetowego na platformie Azure. Usługa Virtual Machines umożliwia zainstalowanie i skonfigurowanie oprogramowania w obrazie maszyny opartym na systemie Windows lub Linux.

### <a id="lob"></a>Mam aplikację biznesową, która potrzebuje połączenia z siecią firmową
Jeśli chcesz stworzyć aplikację biznesową, witryna internetowa może wymagać bezpośredniego dostępu do usług lub danych w sieci firmowej. Jest to możliwe w przypadku usług App Service, Service Fabric i Virtual Machines korzystających z [usługi Azure Virtual Network](/azure/virtual-network/). W usłudze App Service można użyć [funkcji integracji sieci wirtualnej](/azure/app-service/web-sites-integrate-with-vnet), co umożliwia aplikacji systemu Azure do uruchamiania tak, jakby znajdowały się w sieci firmowej.

### <a id="mobile"></a>Chcę hostować interfejs API REST lub usługę internetową dla klientów mobilnych
Usługi internetowe oparte na protokole HTTP umożliwiają obsługę szerokiego zakresu klientów, w tym klientów mobilnych. Platformy, takie jak ASP.NET Web API, integrują się z programem Visual Studio, aby ułatwić tworzenie i korzystanie z usług REST.  Te usługi są uwidaczniane z internetowego punktu końcowego, więc można użyć dowolnej techniki hostingu internetowego na platformie Azure do obsługi tego scenariusza. Jednak doskonałym wyborem dla hostowania interfejsów API REST jest usługa App Service. Możliwości zapewniane przez usługę App Service:

* Szybkie tworzenie [aplikacji mobilnych](../app-service-mobile/app-service-mobile-value-prop.md) lub aplikacji interfejsu API w celu hostowania internetowej usługi HTTP w jednym z rozproszonych globalnie centrów danych platformy Azure.
* Migrowanie istniejących usług lub tworzenie nowych.
* Umowa SLA dotycząca dostępności dla jednego wystąpienia lub skalowanie w poziomie na wiele dedykowanych maszyn.
* Użycie opublikowanej witryny do udostępniania interfejsów API REST dowolnym klientom HTTP, w tym klientom mobilnym.

> [!NOTE]
> Jeśli chcesz rozpocząć korzystanie z usługi Azure App Service przed utworzeniem konta, przejdź na stronę <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, gdzie możesz od razu utworzyć bezpłatnie aplikację startową o krótkim czasie życie w usłudze Azure App Service. Bez karty kredytowej i bez zobowiązań.

## <a id="nextsteps"></a> Następne kroki
Aby uzyskać więcej informacji na temat trzech opcji hostingu internetowego, zobacz temat [Wprowadzenie do platformy Azure](../fundamentals-introduction-to-azure.md).

Aby zacząć pracę za pomocą opcji wybranej dla aplikacji, zobacz następujące zasoby:

* [Azure App Service](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Azure Virtual Machines](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
[WebJobs]: https://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: app-service-web-tutorial-custom-ssl.md
[azurestore]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps
[scripting]: https://azure.microsoft.com/documentation/scripts/?services=web-sites
[dotnet]: https://azure.microsoft.com/develop/net/
[nodejs]: https://azure.microsoft.com/develop/nodejs/
[PHP]: https://azure.microsoft.com/develop/php/
[Python]: https://azure.microsoft.com/develop/python/
[servicebus]: /azure/service-bus/
[sqldatabase]: /azure/sql-database/
[Storage]: /azure/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
[migrate-tool]: https://www.movemetothecloud.net/
