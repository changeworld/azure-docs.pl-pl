---
title: Wdrażanie aplikacji sieci szkieletowej usług w klastrze na platformie Azure
description: Dowiedz się, jak wdrożyć istniejącą aplikację w nowo utworzonym klastrze usługi Azure Service Fabric z programu Visual Studio.
author: athinanthny
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 9951610732cbb1c5884a7b7e830033f427db0ab1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75646011"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Samouczek: wdrażanie aplikacji usługi Service Fabric w klastrze na platformie Azure

Ten samouczek jest drugą częścią serii. Przedstawiono w nim sposób wdrażania aplikacji usługi Azure Service Fabric w nowym klastrze na platformie Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie klastra.
> * Wdrażanie aplikacji w klastrze zdalnym przy użyciu programu Visual Studio.

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie aplikacji sieci szkieletowej usług .NET](service-fabric-tutorial-create-dotnet-app.md).
> * Wdrażanie aplikacji w klastrze zdalnym.
> * [Dodawanie punktu końcowego HTTPS do usługi front-end ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Skonfiguruj monitorowanie i diagnostykę aplikacji](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zainstaluj program Visual Studio 2019](https://www.visualstudio.com/)i zainstaluj obciążenia deweloperskie i **ASP.NET i tworzenia sieci Web** platformy **Azure.**
* [Zainstaluj pakiet SDK sieci szkieletowej usług](service-fabric-get-started.md).

> [!NOTE]
> Bezpłatne konto może nie spełniać wymagań dotyczących tworzenia maszyny wirtualnej. Uniemożliwi to ukończenie samouczka. Ponadto konto niezwiązane z pracą lub pozaszkole może napotkać problemy z uprawnieniami podczas tworzenia certyfikatu w łączu klucza skojarzonym z klastrem. Jeśli wystąpi błąd związany z tworzeniem certyfikatów, użyj portalu, aby zamiast tego utworzyć klaster. 

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania

Jeśli nie skompilowano przykładowej aplikacji do głosowania w [pierwszej części tej serii samouczków](service-fabric-tutorial-create-dotnet-app.md), można ją pobrać. W oknie polecenia uruchom następujący kod, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Otwórz aplikację w programie Visual Studio w trybie administratora i skompiluj aplikację.

## <a name="create-a-cluster"></a>Tworzenie klastra

Teraz, gdy aplikacja jest gotowa, utwórz klaster usługi Service Fabric, a następnie wdróż aplikację w klastrze. [Klaster sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane.

W ramach tego samouczka utworzysz nowy klaster testowy z trzema węzłami w środowisku IDE programu Visual Studio, a następnie opublikujesz aplikację w tym klastrze. Aby uzyskać informacje dotyczące tworzenia klastra produkcyjnego zobacz [Samouczek dotyczący tworzenia klastra i zarządzania nim](service-fabric-tutorial-create-vnet-and-windows-cluster.md). Możesz również wdrożyć aplikację w istniejącym klastrze, który został utworzony wcześniej za pośrednictwem witryny [Azure Portal](https://portal.azure.com), przy użyciu skryptów programu [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) lub [interfejsu wiersza polecenia platformy Azure](./scripts/cli-create-cluster.md) albo za pomocą [szablonu usługi Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> Aplikacja Voting, jak wiele innych aplikacji, używa zwrotnego serwera proxy usługi Service Fabric do komunikacji między usługami. Klastry utworzone z poziomu programu Visual Studio mają domyślnie włączoną opcję korzystania ze zwrotnego serwera proxy. Jeśli przeprowadzasz wdrożenie w istniejącym klastrze, musisz [włączyć zwrotny serwer proxy w klastrze](service-fabric-reverseproxy-setup.md), aby aplikacja Voting mogła działać.


### <a name="find-the-votingweb-service-endpoint"></a>Znajdowanie punktu końcowego usługi VotingWeb

Usługa internetowa frontonu aplikacji Voting nasłuchuje na określonym porcie (8080, jeśli zostały wykonane kroki podane w [pierwszej części tej serii samouczków](service-fabric-tutorial-create-dotnet-app.md)). Gdy aplikacja jest wdrażana w klastrze na platformie Azure, klaster i aplikacja są uruchamiane za modułem równoważenia obciążenia platformy Azure. Port aplikacji musi być otwarty w module równoważenia obciążenia platformy Azure przy użyciu reguły. Reguła wysyła ruch przychodzący za pośrednictwem modułu równoważenia obciążenia do usługi internetowej. Port znajduje się w pliku **VotingWeb/PackageRoot/ServiceManifest.xml** w elemencie **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Zanotuj punkt końcowy usługi, który będzie potrzebny w kolejnym kroku.  Jeśli wdrażasz w istniejącym klastrze, otwórz ten port, tworząc regułę równoważenia obciążenia i sondując w modułu równoważenia obciążenia platformy Azure przy użyciu [skryptu programu PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) lub za pośrednictwem modułu równoważenia obciążenia dla tego klastra w [witrynie Azure portal.](https://portal.azure.com)

### <a name="create-a-test-cluster-in-azure"></a>Tworzenie klastra testowego na platformie Azure
W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **Voting (Głosowanie)** i wybierz polecenie **Opublikuj**.

W polu **Punkt końcowy połączenia** wybierz pozycję **Tworzenie nowego klastra**.  Jeśli wdrażasz w istniejącym klastrze, wybierz punkt końcowy klastra z listy.  Zostanie otwarte okno dialogowe Tworzenie klastra usługi Service Fabric.

Na karcie **Klaster** wprowadź wartość w polu **Nazwa klastra** (na przykład „mytestcluster”), wybierz swoją subskrypcję, wybierz region dla klastra (na przykład Południowo-środkowe stany USA), wprowadź liczby węzłów klastra (na potrzeby klastra testowego zalecamy trzy węzły), a następnie wprowadź grupę zasobów (na przykład „mytestclustergroup”). Kliknij przycisk **alej**.

![Tworzenie klastra](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

Na karcie **Certyfikat** wprowadź hasło i ścieżkę danych wyjściowych dla certyfikatu klastra. Certyfikat z podpisem własnym zostanie utworzony jako plik PFX i zapisany w określonej ścieżce danych wyjściowych.  Certyfikat jest używany zarówno w przypadku zabezpieczeń między węzłami, jak i zabezpieczeń między klientem i węzłem.  Nie używaj certyfikatu z podpisem własnym dla klastrów produkcyjnych.  Ten certyfikat jest używany przez program Visual Studio do uwierzytelniania w klastrze i wdrażania aplikacji. Zaznacz pozycję **Importuj certyfikat**, aby zainstalować plik PFX w folderze CurrentUser\My certificate store na Twoim komputerze.  Kliknij przycisk **alej**.

![Tworzenie klastra](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

Na karcie **Szczegóły maszyny wirtualnej** wprowadź wartości **Nazwa użytkownika** i **Hasło** dla konta administratora klastra.  Wybierz pozycję **Obraz maszyny wirtualnej** dla węzłów klastra i **Rozmiar maszyny wirtualnej** dla każdego węzła klastra.  Kliknij kartę **Zaawansowane**.

![Tworzenie klastra](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

W polu **Porty** wprowadź punkt końcowy usługi VotingWeb z poprzedniego kroku (na przykład 8080).  Po utworzeniu klastra te porty aplikacji będą otwierane w module równoważenia obciążenia platformy Azure w celu przekazywania ruchu do klastra.  Kliknij przycisk **Utwórz**, aby utworzyć klaster, co zajmie kilka minut.

![Tworzenie klastra](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publikowanie aplikacji w klastrze

Gdy nowy klaster jest gotowy, możesz wdrożyć aplikację Voting bezpośrednio z programu Visual Studio.

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **Voting (Głosowanie)** i wybierz polecenie **Opublikuj**. Zostanie wyświetlone okno dialogowe **Publikowanie**.

W polu **Punkt końcowy połączenia** wybierz punkt końcowy klastra utworzonego w poprzednim kroku.  Na przykład „mytestcluster.southcentral.cloudapp.azure.com:19000”. Jeśli wybierzesz pozycję **Zaawansowane parametry połączenia**, informacje o certyfikacie powinny być automatycznie wypełnione.  
![Publikowanie aplikacji usługi Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Wybierz pozycję **Publikuj**.

Po wdrożeniu aplikacji otwórz przeglądarkę i wprowadź adres klastra, a następnie wartość **:8080**. Ewentualnie wpisz inny numer portu, jeśli został skonfigurowany. Może to być na przykład `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Aplikacja zostanie teraz wyświetlona jako uruchomiona w klastrze na platformie Azure. Na stronie internetowej aplikacji do głosowania spróbuj dodać lub usunąć opcje głosowania oraz zagłosować na co najmniej jedną z tych opcji.

![Service Fabric voting sample (Przykład głosowania usługi Service Fabric)](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Następne kroki
W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra.
> * Wdrażanie aplikacji w klastrze zdalnym przy użyciu programu Visual Studio.

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Włączanie protokołu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
