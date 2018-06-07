---
title: Lokalne wdrażanie i uaktualnianie mikrousług platformy Azure | Microsoft Docs
description: Dowiedz się, jak skonfigurować lokalny klaster usługi Service Fabric, wdrożyć w nim istniejącą aplikację, a następnie uaktualnić tę aplikację.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/03/2017
ms.author: ryanwi
ms.openlocfilehash: 244a26df25e39fd4000a8a2692edd8205e354582
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643247"
---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Pierwsze kroki wdrażania i aktualizowania aplikacji w klastrze lokalnym
Zestaw SDK usługi Azure Service Fabric zawiera pełne lokalne środowisko deweloperskie, którego można użyć, aby szybko rozpocząć wdrażanie aplikacji i zarządzanie nimi w klastrze lokalnym. Z tego artykułu dowiesz się, jak utworzyć klaster lokalny, wdrożyć w nim istniejącą aplikację, a następnie uaktualnić ją do nowej wersji — a wszystko to z programu Windows PowerShell.

> [!NOTE]
> W tym artykule przyjęto, że czynności [konfigurowania środowiska deweloperskiego](service-fabric-get-started.md) zostały już ukończone.
> 
> 

## <a name="create-a-local-cluster"></a>Tworzenie klastra lokalnego
Klaster usługi Service Fabric stanowi zestaw zasobów sprzętowych, w którym można wdrażać aplikacje. Zazwyczaj klaster składa się z dowolnej liczby maszyn z zakresu od pięciu sztuk do wielu tysięcy. Jednak zestaw SDK usługi Service Fabric obejmuje konfigurację klastra, która może działać na jednej maszynie.

Należy zrozumieć, że klaster lokalny usługi Service Fabric nie jest emulatorem ani symulatorem. Jest na nim wykonywany ten sam kod platformy, który można znaleźć w klastrach obejmujących wiele maszyn. Jedyna różnica polega na tym, że na jednej maszynie realizuje on procesy platformowe, które standardowo są rozmieszczone na pięciu maszynach.

Zestaw SDK udostępnia dwa sposoby instalacji klastra lokalnego: skrypt programu Windows PowerShell i aplikacja Local Cluster Manager dostępna na pasku zadań. W tym samouczku używany jest skrypt programu PowerShell.

> [!NOTE]
> Jeśli utworzono już klaster lokalny poprzez wdrożenie aplikacji z programu Visual Studio, tę sekcję można pominąć.
> 
> 

1. Uruchom nowe okno programu PowerShell jako administrator.
2. Uruchom skrypt instalacji klastra z folderu zestawu SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    Instalacja klastra trwa kilka chwil. Po zakończeniu instalacji powinny być widoczne dane wyjściowe podobne do poniższych:
   
    ![Dane wyjściowe instalacji klastra][cluster-setup-success]
   
    Teraz możesz spróbować wdrożyć aplikację na swoim klastrze.

## <a name="deploy-an-application"></a>Wdrażanie aplikacji
Zestaw SDK usługi Service Fabric zawiera bogaty zestaw struktur i narzędzi programistycznych przeznaczonych do tworzenia aplikacji. Jeśli chcesz się dowiedzieć, jak tworzyć aplikacje w programie Visual Studio, zobacz [Tworzenie pierwszej aplikacji usługi Service Fabric w programie Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

W tym samouczku użyto istniejącej aplikacji przykładowej (o nazwie WordCount), dzięki czemu można skupić się na kwestiach związanych zarządzaniem na platformie, takich jak wdrażanie, monitorowanie i uaktualnianie.

1. Uruchom nowe okno programu PowerShell jako administrator.
2. Zaimportuj moduł programu PowerShell zestawu SDK usługi Service Fabric.
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. Utwórz katalog do przechowywania aplikacji, która zostanie pobrana i wdrożona, na przykład C:\ServiceFabric.
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [Pobierz aplikację WordCount](http://aka.ms/servicefabric-wordcountapp) do utworzonej lokalizacji.  Uwaga: przeglądarka Microsoft Edge zapisuje plik z rozszerzeniem *zip*.  Zmień rozszerzenie pliku na *sfpkg*.
5. Nawiąż połączenie z klastrem lokalnym:
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. Utwórz nową aplikację za pomocą polecenia wdrożenia zestawu SDK z nazwą pakietu aplikacji i ścieżką do niego.
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    Jeśli wszystko przebiegnie poprawnie, powinny pojawić się następujące dane wyjściowe:
   
    ![Wdrażanie aplikacji w klastrze lokalnym][deploy-app-to-local-cluster]
7. Aby zobaczyć aplikację w akcji, uruchom przeglądarkę i przejdź pod adres [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Powinien zostać wyświetlony następujący ekran:
   
    ![Interfejs użytkownika wdrożonej aplikacji][deployed-app-ui]
   
    Aplikacja WordCount jest prosta. Zawiera kliencki kod JavaScript generujący losowe pięcioznakowe „słowa”, które są następnie przekazywane do aplikacji za pośrednictwem interfejsu API ASP.NET Web. Usługa stanowa śledzi liczbę zliczonych słów. Są one przydzielane do partycji na podstawie pierwszego znaku słowa. Kod źródłowy aplikacji WordCount można znaleźć w [klasycznych przykładach wprowadzających](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount).
   
    Aplikacja, którą wdrożyliśmy, zawiera cztery partycje. Słowa zaczynające się na litery od A do G są przechowywane w pierwszej partycji, słowa zaczynające się na litery od H do N są przechowywane w drugiej partycji i tak dalej.

## <a name="view-application-details-and-status"></a>Wyświetlanie szczegółów i stanu aplikacji
Po wdrożeniu aplikacji przyjrzymy się części jej szczegółów w programie PowerShell.

1. Wygeneruj zapytanie dotyczące wszystkich aplikacji wdrożonych w klastrze:
   
    ```powershell
    Get-ServiceFabricApplication
    ```
   
    Jeśli została wdrożona tylko aplikacja WordCount, powinny pojawić się dane podobne do poniższych:
   
    ![Zapytanie dotyczące wszystkich wdrożonych aplikacji w powłoce PowerShell][ps-getsfapp]
2. Przejdź do następnego poziomu, generując zapytanie dotyczące zestawu usług zawartych w aplikacji WordCount.
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Lista usług dla aplikacji w programie PowerShell][ps-getsfsvc]
   
    Aplikacja składa się z dwóch usług — frontonu sieci Web i usługi stanowej, która zarządza słowami.
3. Na koniec przyjrzyj się liście partycji dla usługi WordCountService:
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![Widok partycji usługi w programie PowerShell][ps-getsfpartitions]
   
    Użyty zestaw poleceń, podobnie jak wszystkie polecenia programu PowerShell usługi Service Fabric, jest dostępny dla dowolnego klastra, z którym można nawiązać połączenie — lokalnego lub zdalnego.
   
    Interakcje z klastrem w sposób bardziej wizualny można realizować przy użyciu internetowego narzędzia Service Fabric Explorer, które jest dostępne po przejściu w przeglądarce pod adres [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
   
    ![Wyświetlanie szczegółów aplikacji w narzędziu Service Fabric Explorer][sfx-service-overview]
   
   > [!NOTE]
   > Aby uzyskać więcej informacji na temat narzędzia Service Fabric Explorer, zobacz [Visualizing your cluster with Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) (Wizualizacja klastra przy użyciu narzędzia Service Fabric Explorer).
   > 
   > 

## <a name="upgrade-an-application"></a>Uaktualnianie aplikacji
Usługa Service Fabric realizuje uaktualnienia bez przestojów, ponieważ monitoruje stan aplikacji wdrażanej w klastrze. Przeprowadź uaktualnienie aplikacji WordCount.

Nowa wersja aplikacji zlicza teraz tylko słowa zaczynające się od samogłosek. Podczas wdrażania tego uaktualnienia będzie można zaobserwować dwie zmiany w zachowaniu aplikacji. Po pierwsze szybkości narastania licznika powinna być mniejsza, ponieważ liczba zliczanych słów będzie mniejsza. Po drugie w pierwszej partycji znajdują się dwie samogłoski (A i E), a wszystkie pozostałe partycje zawierają po jednej sylabie, dlatego licznik pierwszej partycji powinien po pewnym czasie narastać szybciej niż pozostałych.

1. [Pobierz pakiet WordCount w wersji 2](http://aka.ms/servicefabric-wordcountappv2) do tej samej lokalizacji, do której pobrano pakiet w wersji 1.
2. Wróć do okna programu PowerShell i użyj w zestawie SDK polecenia uaktualniania, aby zarejestrować nową wersję w klastrze. Następnie rozpocznij uaktualnianie aplikacji fabric:/WordCount.
   
    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```
   
    Po rozpoczęciu uaktualnienia w programie PowerShell powinny zostać wyświetlone poniższe dane wyjściowe.
   
    ![Postęp uaktualniania w programie PowerShell][ps-appupgradeprogress]
3. Podczas uaktualniania łatwiejsze może być monitorowanie stanu tego procesu z narzędzia Service Fabric Explorer. Uruchom okno przeglądarki i przejdź pod adres [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Rozwiń węzeł **Aplikacje** w drzewie po lewej stronie, wybierz pozycję **WordCount**, a na koniec wybierz pozycję **fabric:/WordCount**. Na karcie danych podstawowych jest widoczny stan uaktualnienia obejmującego domeny uaktualnienia klastra.
   
    ![Postęp uaktualniania w narzędziu Service Fabric Explorer][sfx-upgradeprogress]
   
    W trakcie uaktualnienia w poszczególnych domenach wykonywane jest sprawdzanie kondycji w celu zapewnienia, że aplikacja zachowuje się prawidłowo.
4. Jeśli uruchomisz ponownie wcześniejsze zapytanie względem zestawu usług zawartych w aplikacji fabric:/WordCount, zauważysz, że wersja usługi WordCountService uległa zmianie, ale wersja usługi WordCountWebService nie zmieniła się:
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Zapytanie dotyczące usług aplikacji po uaktualnieniu][ps-getsfsvc-postupgrade]
   
    W tym przykładzie podkreślono sposób, w jaki usługa Service Fabric zarządza uaktualnieniami aplikacji. Działania związane z uaktualnieniami są realizowane tylko względem zestawu usług (albo pakietów kodu/konfiguracji w tych usługach), które uległy zmianie, co sprawia, że proces uaktualniania przebiega szybciej i bardziej niezawodnie.
5. Na koniec wróć do przeglądarki, aby przyjrzeć się zachowaniu nowej wersji aplikacji. Zgodnie z oczekiwaniami liczniki narastają wolniej, a ilość w pierwszej partycji jest nieznacznie większa.
   
    ![Widok nowej wersji aplikacji w przeglądarce][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Czyszczenie
Przed zakończeniem należy pamiętać, że klaster lokalny jest prawdziwy. Aplikacje pozostaną uruchomione w tle, dopóki nie zostaną usunięte.  W zależności od charakteru działające aplikacje mogą wykorzystywać znaczące ilości zasobów na maszynie. Istnieje kilka możliwości zarządzania aplikacjami i klastrem:

1. Aby usunąć pojedynczą aplikację i wszystkie jej dane, uruchom następujące polecenie:
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    Możesz również usunąć aplikację z poziomu menu **AKCJE** narzędzia Service Fabric Explorer lub menu kontekstowego w widoku listy aplikacji po lewej stronie.
   
    ![Usuwanie aplikacji w narzędziu Service Fabric Explorer][sfe-delete-application]
2. Po usunięciu aplikacji z klastra wyrejestruj wersje 1.0.0 i 2.0.0 typu aplikacji WordCount. Spowoduje to usunięcie z magazynu obrazów klastra pakietów aplikacji, w tym kodu i konfiguracji.
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    W narzędziu Service Fabric Explorer można również wybrać pozycję **Cofnij aprowizację typu** dla aplikacji.
3. Aby zamknąć klaster, zachowując dane i ślady aplikacji, kliknij opcję **Zatrzymaj klaster lokalny** na pasku zadań systemu.
4. Aby całkowicie usunąć klaster, kliknij opcję **Usuń klaster lokalny** na pasku zadań systemu. Zastosowanie tej opcji spowoduje powolne wdrożenie po następnym naciśnięciu klawisza F5 w programie Visual Studio. Klaster lokalny należy usuwać tylko wtedy, gdy nie będzie planowane używanie klastra lokalnego przez pewien czas lub konieczne jest odzyskanie zasobów.

## <a name="one-node-and-five-node-cluster-mode"></a>Tryb klastra z jednym węzłem i pięcioma węzłami
Podczas opracowywania aplikacji często są wykonywane szybkie iteracje podczas pisania kodu, debugowania, zmiany kodu i debugowania. Aby pomóc zoptymalizować ten proces, klaster lokalny można uruchomić w dwóch trybach: z jednym węzłem lub z pięcioma węzłami. Oba tryby klastra mają swoje zalety. Tryb klastra z pięcioma węzłami umożliwia pracę z rzeczywistym klastrem. Można przetestować scenariusze pracy awaryjnej i pracować z większą liczbą wystąpień i replik usług. Tryb klastra z jednym węzłem jest zoptymalizowany do szybkiego wdrażania i rejestrowania usług, co ułatwia szybkie weryfikowanie kodu za pomocą środowiska uruchomieniowego usługi Service Fabric.

Tryby klastra z jednym węzłem i z pięcioma węzłami nie są emulatorami ani symulatorami. W lokalnym klastrze projektowy jest wykonywany ten sam kod platformy, który można znaleźć w klastrach obejmujących wiele maszyn.

> [!WARNING]
> Zmiana trybu klastra polega na usunięciu bieżącego klastra z systemu i utworzeniu nowego klastra. Dane przechowywane w klastrze są usuwane podczas zmiany trybu klastra.
> 
> 

Aby zmienić tryb na klaster z jednym węzłem, wybierz pozycję **Przełącz tryb klastra** w menedżerze klastra lokalnego usługi Service Fabric.

![Przełączanie trybu klastra][switch-cluster-mode]

Możesz też zmienić tryb klastra przy użyciu programu PowerShell:

1. Uruchom nowe okno programu PowerShell jako administrator.
2. Uruchom skrypt instalacji klastra z folderu zestawu SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    Instalacja klastra trwa kilka chwil. Po zakończeniu instalacji powinny być widoczne dane wyjściowe podobne do poniższych:
   
    ![Dane wyjściowe instalacji klastra][cluster-setup-success-1-node]

## <a name="next-steps"></a>Kolejne kroki
* Po wdrożeniu i uaktualnieniu wstępnie przygotowanych aplikacji możesz [spróbować utworzyć własne aplikacje w programie Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
* Wszystkie akcje wykonane w tym artykule w klastrze lokalnym można również wykonać w [klastrze platformy Azure](service-fabric-cluster-creation-via-portal.md).
* Uaktualnienie wykonane w tym artykule było podstawowe. Aby dowiedzieć się więcej o możliwościach i elastyczności uaktualnień w usłudze Service Fabric, zobacz [dokumentację uaktualniania](service-fabric-application-upgrade.md).

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
