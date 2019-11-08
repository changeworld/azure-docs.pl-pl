---
title: Wykrywanie zagrożeń w przypadku natywnego przetwarzania danych w chmurze w Azure Security Center | Microsoft Docs
description: W tym artykule przedstawiono natywne alerty obliczeniowe w chmurze dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: memildin
ms.openlocfilehash: 6b6acb0ae1452795fe02906779b920e4b41f9a55
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748392"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Wykrywanie zagrożeń w przypadku natywnego przetwarzania danych w chmurze w Azure Security Center

Jako rozwiązanie natywne Azure Security Center ma unikatowy wgląd w dzienniki wewnętrzne na potrzeby identyfikacji metodologii ataków w wielu celach. W tym artykule przedstawiono alerty dostępne dla następujących usług platformy Azure:

* [Azure App Service](#app-services)
* [Kontenery platformy Azure](#azure-containers) 

> [!NOTE]
> Te usługi nie są obecnie dostępne w regionach platformy Azure dla instytucji rządowych i suwerennych.

## Azure App Service<a name="app-services"></a>

Security Center używa skali chmury, aby identyfikować ataki ukierunkowane na aplikacje działające przez App Service. Osoby atakujące mogą wykrywać słabe i luki w zabezpieczeniach aplikacji sieci Web. Przed skierowaniem do określonych środowisk żądania do aplikacji uruchomionych na platformie Azure przechodzą przez kilka bram, gdzie są kontrolowane i rejestrowane. Te dane są następnie używane do identyfikowania luk w zabezpieczeniach i ataków oraz do uczenia się nowych wzorców, które będą używane później.

Korzystając z widoczności platformy Azure jako dostawcy chmury, Security Center analizuje App Service dzienników wewnętrznych w celu zidentyfikowania metodologii ataków na wiele obiektów docelowych. Na przykład metodologia obejmuje szerokie skanowanie i rozproszone ataki. Ten typ ataku zwykle pochodzi z małego podzbioru adresów IP i pokazuje wzorce przeszukiwania do podobnych punktów końcowych na wielu hostach. Ataki są wyszukiwaniem zagrożonej strony lub wtyczki i nie mogą być zidentyfikowane z punktu widzenia jednego hosta.

Security Center również ma dostęp do podstawowych piaskownic i maszyn wirtualnych. Wraz z dowodowych pamięci infrastruktura może informować o tym o tym scenariuszu, od nowego ataku rozprowadzonego na komputerach klienckich. W związku z tym nawet jeśli Security Center zostanie wdrożona po wykorzystaniu aplikacji sieci Web, może być możliwe wykrycie trwających ataków.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Wykryto podejrzane wywołanie w ramach platformy WordPress**|App Service dziennik aktywności wskazuje możliwe działanie iniekcji kodu w zasobie App Service.<br/> To podejrzane działanie przypomina aktywność, która manipuluje kompozycją WordPress w celu obsługi wykonywania kodu po stronie serwera, a następnie bezpośredniego żądania sieci Web w celu wywołania pliku motywu, który został przekierowany. Ten typ działania może być częścią kampanii ataków za pośrednictwem platformy WordPress.|
|**Wykryto połączenie ze stroną internetową z nietypowego adresu IP**|App Service dziennik aktywności wskazuje połączenie z poufną stroną sieci Web na podstawie adresu źródłowego, który nigdy nie jest z nim połączony. To połączenie może wskazywać, że ktoś próbuje przeprowadzić atak na strony administrowania aplikacjami sieci Web. Może być również wynikiem wiarygodnego użytkownika korzystającego z nowego adresu IP.|
|**W analizie zagrożeń znaleziono adres IP połączony z interfejsem FTP Azure App Service.**|App Service Analiza dzienników FTP wykryła połączenie z adresu źródłowego, który został znaleziony w kanale informacyjnym analizy zagrożeń. Podczas tego połączenia użytkownik uzyskał dostęp do wyświetlanych stron.|
|**Wykryto odciskiem palca sieci Web**|App Service dziennik aktywności wskazuje możliwe działanie odciskiem palca sieci Web w zasobie App Service. <br/>To podejrzane działanie jest skojarzone z narzędziem o nazwie Blind słoni. Na serwerach sieci Web odciski palców narzędzia i próby wykrycia zainstalowanych aplikacji i ich wersji. Osoby atakujące często używają tego narzędzia do sondowania aplikacji sieci Web w celu znalezienia luk w zabezpieczeniach.|
|**Wykryto podejrzany dostęp do potencjalnie narażonej strony sieci Web**|Dziennik aktywności App Service wskazuje, że dostęp do strony sieci Web, która jest niezależna. <br/>To podejrzane działanie pochodzi z adresu źródłowego, którego wzorzec dostępu jest podobny do skanera sieci Web. Ten rodzaj działania jest często związany z próbą skanowania sieci przez osobę atakującą w celu uzyskania dostępu do poufnych lub narażonych stron sieci Web.|
|**Plik PHP w folderze przekazywania**|Dziennik aktywności App Service wskazuje, że w folderze przekazywania uzyskano dostęp do podejrzanej strony PHP. <br/>Ten typ folderu zwykle nie zawiera plików PHP. Istnienie tego typu pliku może wskazywać na wykorzystanie zalet dowolnych luk w zabezpieczeniach przekazywania plików.|
|**Próba uruchomienia poleceń systemu Linux w systemie Windows App Service**|Analiza procesów App Service wykryła próbę uruchomienia polecenia systemu Linux na App Service Windows. Ta akcja była uruchomiona przez aplikację sieci Web. Takie zachowanie jest często widoczne podczas kampanii wykorzystujących luki w zabezpieczeniach w typowej aplikacji sieci Web.|
|**Wykryto podejrzane wykonanie w języku PHP**|Dzienniki maszyn wskazują, że jest uruchomiony podejrzany proces PHP. Akcja obejmowała próbę uruchomienia poleceń systemu operacyjnego lub kodu PHP z wiersza polecenia przy użyciu procesu PHP. Chociaż to zachowanie może być wiarygodne, w aplikacjach sieci Web takie zachowanie może wskazywać na złośliwe działania, takie jak próby zainfekowania witryn sieci Web za pomocą powłok internetowych.|
|**Wykonywanie procesu z folderu tymczasowego**|Analiza procesów App Service wykryła wykonywanie procesu z folderu tymczasowego aplikacji. Chociaż to zachowanie może być prawdziwe, w aplikacjach sieci Web takie zachowanie może wskazywać na złośliwe działania.|
|**Wykryto próbę uruchomienia polecenia o wysokim poziomie uprawnień**|Analiza procesów App Service wykryła próbę uruchomienia polecenia wymagającego wysokiego poziomu uprawnień. Polecenie zostało uruchomione w kontekście aplikacji sieci Web. Chociaż to zachowanie może być prawdziwe, w aplikacjach sieci Web takie zachowanie może wskazywać na złośliwe działania.|

## Kontenery platformy Azure<a name="azure-containers"></a>

Security Center zapewnia wykrywanie zagrożeń w czasie rzeczywistym w środowiskach kontenerów i generuje alerty dla podejrzanych działań. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

Wykrywamy zagrożenia na różnych poziomach: 

* Agent Security Center na **poziomie hosta** (dostępny w warstwie Standardowa, zobacz [Cennik](security-center-pricing.md) dla szczegółów) monitory Linux dla podejrzanych działań. Agent wyzwala alerty dotyczące podejrzanych działań pochodzących z węzła lub kontenera, w którym jest uruchomiona. Przykłady takich działań obejmują wykrywanie i nawiązywanie powłoki sieci Web przy użyciu znanych podejrzanych adresów IP.

    Aby uzyskać dokładniejszy wgląd w zabezpieczenia środowiska kontenerowego, agent monitoruje analizę specyficzną dla kontenera. Spowoduje to wyzwolenie alertów dotyczących zdarzeń, takich jak tworzenie kontenera uprzywilejowanego, podejrzany dostęp do serwerów interfejsu API i serwery Secure Shell (SSH) działające wewnątrz kontenera Docker.

    >[!NOTE]
    > Jeśli zdecydujesz się nie instalować agentów na hostach, będziesz otrzymywać tylko podzbiór korzyści i alertów dotyczących wykrywania zagrożeń. Nadal będziesz otrzymywać alerty dotyczące analizy sieci i komunikacji ze złośliwymi serwerami.

* Na **poziomie klastra AKS**monitorowanie wykrywania zagrożeń opiera się na analizie dzienników inspekcji Kubernetes. Aby włączyć monitorowanie **bez wykorzystania agentów** , Dodaj opcję Kubernetes do subskrypcji na stronie **ustawień & cenowej** (zobacz [Cennik](security-center-pricing.md)). Aby generować alerty na tym poziomie, Security Center monitoruje usługi zarządzane przez AKS przy użyciu dzienników pobranych przez AKS. Przykłady zdarzeń na tym poziomie obejmują uwidocznione pulpity nawigacyjne Kubernetes, tworzenie uprzywilejowanych ról i tworzenie poufnych instalacji.

    >[!NOTE]
    > Security Center generuje alerty wykrywania dla akcji i wdrożeń usługi Azure Kubernetes, które występują po włączeniu opcji Kubernetes w ustawieniach subskrypcji. 

Ponadto nasz globalny zespół badaczy ds. zabezpieczeń stale monitoruje zagrożenie w poziomie. Po ich odnalezieniu są dodawane alerty i luki specyficzne dla kontenera.


### <a name="aks-cluster-level-alerts"></a>Alerty na poziomie klastra AKS

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**WERSJA zapoznawcza — wykryto powiązanie roli z rolą administratora klastra**|Analiza dzienników inspekcji Kubernetes wykryła nowe powiązanie z rolą administratora klastra, które wynikają z uprawnień administratora. Niepotrzebnie zapewnianie uprawnień administratora może spowodować problemy z eskalacją uprawnień w klastrze.|
|**Wykryto narażony pulpit nawigacyjny Kubernetes**|Analiza dzienników inspekcji Kubernetes wykryła narażenie pulpitu nawigacyjnego Kubernetes przez usługę równoważenia obciążenia. Uwidocznione pulpity nawigacyjne umożliwiają nieuwierzytelnionego dostępu do zarządzania klastrem i stanowią zagrożenie bezpieczeństwa.|
|**WERSJA zapoznawcza — Wykryto nową rolę o wysokim poziomie uprawnień**|Analiza dzienników inspekcji Kubernetes wykryła nową rolę o wysokim poziomie uprawnień. Powiązanie z rolą o wysokim poziomie uprawnień daje użytkownikowi/grupie podwyższone uprawnienia w klastrze. Niepotrzebne zapewnianie podwyższonych uprawnień może spowodować problemy z eskalacją uprawnień w klastrze.|
|**WERSJA zapoznawcza — wykryto nowy kontener w przestrzeni nazw polecenia-system**|Analiza dziennika inspekcji Kubernetes wykryła nowy kontener w przestrzeni nazw polecenia-system, który nie należy do kontenerów, które normalnie działają w tej przestrzeni nazw. Przestrzenie nazw systemu polecenia nie powinny zawierać zasobów użytkownika. Osoby atakujące mogą użyć tej przestrzeni nazw, aby ukryć złośliwe składniki.|
|**WERSJA zapoznawcza — wykryto kontener wyszukiwania w walucie cyfrowej**|Analiza dziennika inspekcji Kubernetes wykryła kontener, który ma obraz skojarzony z narzędziem wyszukiwania waluty cyfrowej.|
|**Wykryto zapoznawczy kontener uprzywilejowany**|Analiza dziennika inspekcji Kubernetes wykryła nowy kontener uprzywilejowany. Kontener uprzywilejowany ma dostęp do zasobów węzła i przerywa izolację między kontenerami. W przypadku naruszenia zabezpieczeń, osoba atakująca może korzystać z kontenera uprzywilejowanego w celu uzyskania dostępu do węzła.|
|**WERSJA zapoznawcza — wykryto kontener z rozinstalowaną wrażliwą objętością**|Analiza dziennika inspekcji Kubernetes wykryła nowy kontener z instalowanym woluminem wrażliwym. Wykryty wolumin jest typem hostPath, który instaluje poufne pliki lub foldery z węzła do kontenera. Jeśli naruszyć bezpieczeństwo kontenera, osoba atakująca może użyć tej instalacji do uzyskania dostępu do węzła.|



### <a name="host-level-alerts"></a>Alerty na poziomie hosta

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Wykryto uprzywilejowany kontener**|Dzienniki maszyn wskazują, że jest uruchomiony uprzywilejowany kontener platformy Docker. Kontener uprzywilejowany ma pełny dostęp do zasobów hosta. W przypadku naruszenia zabezpieczeń, osoba atakująca może korzystać z kontenera uprzywilejowanego w celu uzyskania dostępu do komputera hosta.|
|**Uruchomione uprzywilejowane polecenie w kontenerze**|Dzienniki maszyn wskazują, że polecenie uprzywilejowane zostało uruchomione w kontenerze platformy Docker. Uprzywilejowane polecenie ma rozszerzone uprawnienia na komputerze-hoście.|
|**Wykryto uwidocznioną demona platformy Docker**|Dzienniki maszyn wskazują, że demon platformy Docker (dockerd) uwidacznia gniazdo TCP. Domyślnie konfiguracja platformy Docker nie korzysta z szyfrowania ani uwierzytelniania, gdy gniazdo TCP jest włączone. Każda osoba mająca dostęp do odpowiedniego portu może uzyskać pełen dostęp do demona platformy Docker.|
|**Serwer SSH jest uruchomiony wewnątrz kontenera**|Dzienniki maszyn wskazują, że serwer SSH jest uruchomiony wewnątrz kontenera Docker. Chociaż takie zachowanie może być zamierzone, często wskazuje, że kontener jest niepoprawnie skonfigurowany lub naruszony.|
|**Wykryto kontener z obrazem Miner**|Dzienniki maszyn wskazują wykonywanie kontenera Docker z obrazem skojarzonym z wyszukiwaniem w walucie cyfrowej. Takie zachowanie może wskazywać na to, że zasoby są nadmiarowe.|
|**Podejrzane żądanie do interfejsu API Kubernetes**|Dzienniki maszyn wskazują, że zostało wysłane podejrzane żądanie do interfejsu API Kubernetes. Żądanie zostało wysłane z węzła Kubernetes, prawdopodobnie z jednego z kontenerów uruchomionych w węźle. Chociaż takie zachowanie może być zamierzone, może to wskazywać na to, że węzeł korzysta z uszkodzonego kontenera.|
|**Podejrzane żądanie do pulpitu nawigacyjnego Kubernetes**|Dzienniki maszyn wskazują, że zostało wysłane podejrzane żądanie do pulpitu nawigacyjnego Kubernetes. Żądanie zostało wysłane z węzła Kubernetes, prawdopodobnie z jednego z kontenerów uruchomionych w węźle. Chociaż takie zachowanie może być zamierzone, może to wskazywać na to, że węzeł korzysta z uszkodzonego kontenera.|