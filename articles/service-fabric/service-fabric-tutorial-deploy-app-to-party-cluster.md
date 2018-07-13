---
title: Wdrażanie aplikacji usługi Service Fabric w klastrze na platformie Azure | Microsoft Docs
description: Dowiedz się, jak wdrożyć aplikację w klastrze z poziomu programu Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/28/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: f83ebcce68a7abe53d7b8eaeff5913a907e3df9a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344193"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Samouczek: wdrażanie aplikacji usługi Service Fabric w klastrze na platformie Azure

Ten samouczek to druga część serii. Przedstawiono w nim sposób wdrażania aplikacji usługi Azure Service Fabric w nowym klastrze na platformie Azure bezpośrednio z poziomu programu Visual Studio.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie klastra z poziomu programu Visual Studio
> * Wdrażanie aplikacji w klastrze zdalnym przy użyciu programu Visual Studio

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Kompilowanie aplikacji .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Wdrażanie aplikacji w klastrze zdalnym
> * [Dodawanie punktu końcowego HTTPS do usługi frontonu platformy ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Konfigurowanie procesu CI/CD za pomocą usługi Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zainstaluj program Visual Studio 2017](https://www.visualstudio.com/), a następnie zainstaluj obciążenia **Programowanie na platformie Azure** i **Tworzenie aplikacji na platformie ASP.NET i tworzenie aplikacji internetowych**.
* [Zainstaluj zestaw SDK usługi Service Fabric.](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania

Jeśli nie skompilowano przykładowej aplikacji do głosowania w [pierwszej części tej serii samouczków](service-fabric-tutorial-create-dotnet-app.md), można ją pobrać. W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="create-a-service-fabric-cluster"></a>Tworzenie klastra usługi Service Fabric

Kiedy aplikacja jest gotowa, można wdrożyć ją w klastrze bezpośrednio z programu Visual Studio. [Klaster usługi Service Fabric](/service-fabric/service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym wdraża się mikrousługi i nimi zarządza.

Dostępne są dwie opcje wdrażania w programie Visual Studio:

* Utworzenie klastra na platformie Azure z poziomu programu Visual Studio. Ta opcja służy do tworzenia bezpiecznego klastra bezpośrednio z poziomu programu Visual Studio z preferowaną konfiguracją. Ten typ klastra jest idealny dla scenariuszy testowych, w których możesz utworzyć klaster, a następnie publikować w nim bezpośrednio z programu Visual Studio.
* Opublikuj w istniejącym klastrze w ramach subskrypcji.  Klastry usługi Service Fabric można utworzyć za pośrednictwem [witryny Azure Portal](https://portal.azure.com), przy użyciu skryptów programu [PowerShel](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) lub [interfejsu wiersza polecenia platformy Azure](./scripts/cli-create-cluster.md), albo za pomocą [szablonu usługi Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Ten samouczek przedstawia sposób tworzenia klastra z poziomu programu Visual Studio. Jeśli masz już wdrożony klaster, możesz skopiować i wkleić punkt końcowy połączenia lub wybrać go w ramach subskrypcji.
> [!NOTE]
> Wiele usług korzysta ze zwrotnego serwera proxy, aby się ze sobą komunikować. Klastry utworzone z poziomu programu Visual Studio i klastry testowe mają domyślnie włączoną opcję korzystania ze zwrotnego serwera proxy.  Jeśli używasz istniejącego klastra, musisz najpierw [włączyć zwrotny serwer proxy w klastrze](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="find-the-votingweb-service-endpoint"></a>Znajdowanie punktu końcowego usługi VotingWeb

Najpierw należy znaleźć punkt końcowy usługi internetowej frontonu.  Usługa internetowa frontonu nasłuchuje na określonym porcie.  Gdy aplikacja jest wdrażana w klastrze na platformie Azure, klaster i aplikacja są uruchamiane za modułem równoważenia obciążenia platformy Azure.  Port aplikacji musi być otwarty w module równoważenia obciążenia platformy Azure, tak aby ruch przychodzący mógł przechodzić do usługi internetowej.  Port (na przykład 8080) znajduje się w pliku *VotingWeb/PackageRoot/ServiceManifest.xml* w elemencie **Endpoint**:

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

W następnym kroku określ ten port na karcie **Zaawansowane** okna dialogowego **Tworzenie klastra**.  W przypadku wdrażania aplikacji w istniejącym klastrze możesz otworzyć ten port w module równoważenia obciążenia platformy Azure za pomocą [skryptu programu PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) lub [witryny Azure Portal](https://portal.azure.com).

### <a name="create-a-cluster-in-azure-through-visual-studio"></a>Tworzenie klastra na platformie Azure za pomocą programu Visual Studio

Kliknij prawym przyciskiem myszy projekt aplikacji w Eksploratorze rozwiązań i wybierz polecenie **Publikuj**.

Zaloguj się przy użyciu konta platformy Azure, aby uzyskać dostęp do subskrypcji. Ten krok jest opcjonalny, jeśli używasz klastra testowego.

Wybierz listę rozwijaną **Punkt końcowy połączenia** i wybierz opcję **<Create New Cluster...>**.

![Okno dialogowe Publikowanie](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

W oknie dialogowym **Tworzenie klastra** zmodyfikuj następujące ustawienia:

1. Określ nazwę klastra w polu **Nazwa klastra**, a także subskrypcję i lokalizację, których chcesz użyć.
2. Opcjonalnie możesz zmodyfikować liczbę węzłów. Domyślne ustawienie to trzy węzły, czyli minimalna liczba wymagana do testowania scenariuszy usługi Service Fabric.
3. Wybierz kartę **Certyfikat**. Na tej karcie wpisz hasło zabezpieczające certyfikat klastra. Ten certyfikat pomaga zabezpieczyć klaster. Możesz również zmodyfikować ścieżkę, w której ma zostać zapisany certyfikat. Program Visual Studio może również automatycznie zaimportować certyfikat, ponieważ jest to krok wymagany do opublikowania aplikacji w klastrze.
4. Wybierz kartę **Szczegóły maszyny wirtualnej**. Określ hasło, którego chcesz używać dla maszyn wirtualnych wchodzących w skład klastra. Za pomocą nazwy użytkownika i hasła można zdalnie łączyć się z maszynami wirtualnymi. Należy również wybrać rozmiar maszyny wirtualnej oraz zmienić obraz maszyny wirtualnej, jeśli jest to konieczne.
5. Na karcie **Zaawansowane** można zmodyfikować listę portów, które mają zostać otwarte w module równoważenia obciążenia platformy Azure utworzonym wraz z klastrem.  Dodaj punkt końcowy usługi VotingWeb odnaleziony w poprzednim kroku. Możesz również dodać istniejący klucz usługi Application Insights, do którego mają być kierowane pliki dziennika aplikacji.
6. Po zakończeniu modyfikowania ustawień wybierz przycisk **Utwórz**. Tworzenie klastra może zająć kilka minut. Informacja o ukończeniu tworzenia klastra pojawi się w oknie danych wyjściowych.

![Okno dialogowe Tworzenie klastra](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

## <a name="deploy-the-sample-application"></a>Wdrażanie aplikacji przykładowej

Gdy klaster, który ma być używany, będzie gotowy, kliknij prawym przyciskiem myszy projekt aplikacji i wybierz polecenie **Publikuj**.

Po zakończeniu publikowania powinno być możliwe wysłanie żądania do aplikacji za pośrednictwem przeglądarki.

Otwórz preferowaną przeglądarkę i wpisz adres klastra (punkt końcowy połączenia bez informacji o porcie — na przykład win1kw5649s.westus.cloudapp.azure.com).

Powinien być teraz widoczny ten sam wynik co po lokalnym uruchomieniu aplikacji.

![Odpowiedź interfejsu API z klastra](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra z poziomu programu Visual Studio
> * Wdrażanie aplikacji w klastrze zdalnym przy użyciu programu Visual Studio

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Włączanie protokołu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
