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
ms.date: 07/12/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: 601742d82c1bd9a0e691de28ff9c4a09f12b538e
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186383"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Samouczek: wdrażanie aplikacji usługi Service Fabric w klastrze na platformie Azure

Ten samouczek to druga część serii. Przedstawiono w nim sposób wdrażania aplikacji usługi Azure Service Fabric w nowym klastrze na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie klastra testowego.
> * Wdrażanie aplikacji w klastrze zdalnym przy użyciu programu Visual Studio.

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Kompilowanie aplikacji .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Wdrażanie aplikacji w klastrze zdalnym
> * [Dodawanie punktu końcowego HTTPS do usługi frontonu platformy ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Konfigurowanie procesu ciągłej integracji/ciągłego wdrażania za pomocą usługi Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zainstaluj program Visual Studio 2017](https://www.visualstudio.com/), a następnie zainstaluj obciążenia **Programowanie na platformie Azure** i **Tworzenie aplikacji na platformie ASP.NET i tworzenie aplikacji internetowych**.
* [Zainstaluj zestaw SDK usługi Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania

Jeśli nie skompilowano przykładowej aplikacji do głosowania w [pierwszej części tej serii samouczków](service-fabric-tutorial-create-dotnet-app.md), można ją pobrać. W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="publish-to-a-service-fabric-cluster"></a>Publikowanie w klastrze usługi Service Fabric

Kiedy aplikacja jest gotowa, można wdrożyć ją w klastrze bezpośrednio z programu Visual Studio. [Klaster usługi Service Fabric](https://docs.microsoft.com/en-gb/azure/service-fabric/service-fabric-deploy-anywhere) jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym wdraża się mikrousługi i nimi zarządza.

W tym samouczku przedstawiono dwie opcje wdrażania aplikacji Voting do klastra usługi Service Fabric przy użyciu programu Visual Studio:

* Publikowanie w klastrze testowym (próbnym).
* Opublikuj w istniejącym klastrze w ramach subskrypcji.  Klastry usługi Service Fabric można utworzyć za pośrednictwem [witryny Azure Portal](https://portal.azure.com), przy użyciu skryptów programu [PowerShel](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) lub [interfejsu wiersza polecenia platformy Azure](./scripts/cli-create-cluster.md), albo za pomocą [szablonu usługi Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> Wiele usług korzysta ze zwrotnego serwera proxy, aby się ze sobą komunikować. Klastry utworzone z poziomu programu Visual Studio i klastry testowe mają domyślnie włączoną opcję korzystania ze zwrotnego serwera proxy.  Jeśli używasz istniejącego klastra, musisz najpierw [włączyć zwrotny serwer proxy w klastrze](service-fabric-reverseproxy.md#setup-and-configuration).


### <a name="find-the-votingweb-service-endpoint-for-your-azure-subscription"></a>Znajdowanie punktu końcowego usługi VotingWeb dla subskrypcji platformy Azure

Jeśli zamierzasz opublikować aplikację Voting w swojej własnej subskrypcji platformy Azure, znajdź punkt końcowy usługi internetowej frontonu. Jeśli używasz klastra testowego, zostanie automatycznie otwarty port 8080 używany przez przykładową aplikację Voting i nie ma potrzeby konfigurowania go w module równoważenia obciążenia klastra testowego.

Usługa internetowa frontonu nasłuchuje na określonym porcie.  Gdy aplikacja jest wdrażana w klastrze na platformie Azure, klaster i aplikacja są uruchamiane za modułem równoważenia obciążenia platformy Azure.  Port aplikacji musi być otwarty za pomocą reguły w module równoważenia obciążenia platformy Azure dla tego klastra, tak aby ruch przychodzący mógł przechodzić do usługi internetowej.  Port (na przykład 8080) znajduje się w pliku *VotingWeb/PackageRoot/ServiceManifest.xml* w elemencie **Endpoint**:

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

W przypadku subskrypcji platformy Azure otwórz ten port za pomocą reguły modułu równoważenia obciążenia na platformie Azure za pośrednictwem [skryptu programu PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) lub za pośrednictwem modułu równoważenia obciążenia dla tego klastra w witrynie [Azure Portal](https://portal.azure.com).

### <a name="join-a-party-cluster"></a>Dołączanie do klastra testowego

> [!NOTE]
> Przejdź do sekcji Wdrażanie aplikacji przy użyciu programu Visual Studio, jeśli masz zamiar opublikować aplikację we własnym klastrze w ramach subskrypcji platformy Azure.

Klastry testowe to bezpłatne, ograniczone czasowo klastry usługi Service Fabric hostowane na platformie Azure i uruchamiane przez zespół usługi Service Fabric, w których każdy może wdrażać aplikacje i poznawać platformę. Klaster używa jednego certyfikatu z podpisem własnym w przypadku zabezpieczeń między węzłami, jak i zabezpieczeń między klientem i węzłem.

Zaloguj się i [dołącz do klastra z systemem Windows](http://aka.ms/tryservicefabric). Pobierz certyfikat PFX na komputer, klikając link **PFX**. Kliknij link **Jak nawiązać połączenie z zabezpieczonym klastrem testowym?** i skopiuj hasło certyfikatu. Certyfikat, hasło certyfikatu oraz wartość pola **Punkt końcowy połączenia** będą używane w kolejnych krokach.

![Plik PFX i punkt końcowy połączenia](./media/service-fabric-quickstart-dotnet/party-cluster-cert.png)

> [!Note]
> Liczba klastrów testowych dostępnych przez godzinę jest ograniczona. Jeśli wystąpi błąd podczas próby tworzenia konta umożliwiającego korzystanie z klastra testowego, możesz poczekać, a następnie spróbować ponownie. Możesz też wykonać kroki opisane w samouczku [Wdrażanie aplikacji .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application), aby utworzyć klaster usługi Service Fabric w subskrypcji platformy Azure i wdrożyć w nim aplikację. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
>

Na komputerze z systemem Windows zainstaluj plik PFX w magazynie certyfikatów *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Zapamiętaj odcisk palca na potrzeby następnego kroku.

> [!Note]
> Domyślnie usługa internetowa frontonu jest skonfigurowana do nasłuchiwania ruchu przychodzącego na porcie 8080. Port 8080 jest otwarty w klastrze testowym.  Jeśli musisz zmienić port aplikacji, zmień go na jeden z portów, które są otwarte w klastrze testowym.
>

### <a name="publish-the-application-using-visual-studio"></a>Publikowanie aplikacji przy użyciu programu Visual Studio

Kiedy aplikacja jest gotowa, można wdrożyć ją w klastrze bezpośrednio z programu Visual Studio.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **Voting (Głosowanie)** i wybierz polecenie **Publikuj**. Zostanie wyświetlone okno dialogowe Publikowanie.

2. Skopiuj **punkt końcowy połączenia** ze strony klastra testowego lub subskrypcji platformy Azure do pola **Punkt końcowy połączenia**. Na przykład `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Kliknij pozycję **Zaawansowane parametry połączenia** i upewnij się, że wartości *FindValue* i *ServerCertThumbprint* są zgodne z odciskiem palca certyfikatu zainstalowanego w poprzednim kroku dla klastra testowego lub certyfikatu pasującego do subskrypcji platformy Azure.

    ![Okno dialogowe Publikowanie](./media/service-fabric-quickstart-dotnet/publish-app.png)

    Każda aplikacja w klastrze musi mieć unikatową nazwę.  Klastry testowe są jednak publicznym, udostępnionym środowiskiem i może wystąpić konflikt z istniejącą aplikacją.  Jeśli występuje konflikt nazw, zmień nazwę projektu programu Visual Studio i wdróż ponownie.

3. Kliknij przycisk **Opublikuj**.

4. Otwórz przeglądarkę i wpisz adres klastra, a po nim ciąg „:8080” (lub inny skonfigurowany port), aby uzyskać dostęp do aplikacji w klastrze — na przykład `http://zwin7fh14scd.westus.cloudapp.azure.com:8080`. Aplikacja powinna zostać teraz wyświetlona jako uruchomiona w klastrze na platformie Azure. Na stronie aplikacji internetowej Voting spróbuj dodać lub usunąć opcje głosowania oraz zagłosować na co najmniej jedną z tych opcji.

    ![Fronton aplikacji](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra testowego.
> * Wdrażanie aplikacji w klastrze zdalnym przy użyciu programu Visual Studio.

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Włączanie protokołu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
