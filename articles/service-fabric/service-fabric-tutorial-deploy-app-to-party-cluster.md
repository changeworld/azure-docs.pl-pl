---
title: Wdrażanie aplikacji usługi Azure Service Fabric w klastrze z poziomu programu Visual Studio | Microsoft Docs
description: Dowiedz się, jak wdrożyć aplikację w klastrze z poziomu programu Visual Studio
services: service-fabric
documentationcenter: .net
-author: mikkelhegn
-manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 1d8f8d903046f1d471f7abbe08a957b81522e391
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>Samouczek: wdrażanie aplikacji w klastrze usługi Service Fabric na platformie Azure
Ten samouczek to druga część serii. Przedstawiono w nim sposób wdrażania aplikacji usługi Azure Service Fabric w nowym klastrze na platformie Azure bezpośrednio z poziomu programu Visual Studio.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie klastra z poziomu programu Visual Studio
> * Wdrażanie aplikacji w klastrze zdalnym przy użyciu programu Visual Studio


Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Kompilowanie aplikacji .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Wdrażanie aplikacji w klastrze zdalnym
> * [Konfigurowanie procesu CI/CD za pomocą usługi Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka:
- Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zainstaluj program Visual Studio 2017](https://www.visualstudio.com/), a następnie zainstaluj obciążenia **Programowanie na platformie Azure** i **Tworzenie aplikacji na platformie ASP.NET i tworzenie aplikacji internetowych**.
- [Zainstaluj zestaw SDK usługi Service Fabric.](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania
Jeśli nie skompilowano przykładowej aplikacji do głosowania w [pierwszej części tej serii samouczków](service-fabric-tutorial-create-dotnet-app.md), można ją pobrać. W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="deploy-the-sample-application"></a>Wdrażanie aplikacji przykładowej

### <a name="select-a-service-fabric-cluster-to-which-to-publish"></a>Wybierz klaster usługi Service Fabric, w którym ma zostać opublikowana aplikacja
Kiedy aplikacja jest gotowa, można wdrożyć ją w klastrze bezpośrednio z programu Visual Studio.

Są dostępne dwie opcje wdrożenia:
- Utwórz klaster z poziomu programu Visual Studio. Ta opcja służy do tworzenia bezpiecznego klastra bezpośrednio z poziomu programu Visual Studio z preferowaną konfiguracją. Ten typ klastra jest idealny dla scenariuszy testowych, w których możesz utworzyć klaster, a następnie publikować w nim bezpośrednio z programu Visual Studio.
- Opublikuj w istniejącym klastrze w ramach subskrypcji.

Ten samouczek zawiera kroki, które należy wykonać, aby utworzyć klaster z poziomu programu Visual Studio. W przypadku innych opcji możesz skopiować i wkleić punkt końcowy połączenia lub wybrać go w ramach subskrypcji.
> [!NOTE]
> Wiele usług korzysta ze zwrotnego serwera proxy, aby się ze sobą komunikować. Klastry utworzone z poziomu programu Visual Studio i klastry testowe mają domyślnie włączoną opcję korzystania ze zwrotnego serwera proxy.  Jeśli używasz istniejącego klastra, musisz najpierw [włączyć zwrotny serwer proxy w klastrze](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Wdrażanie aplikacji w klastrze usługi Service Fabric
1. Kliknij prawym przyciskiem myszy projekt aplikacji w Eksploratorze rozwiązań i wybierz polecenie **Publikuj**.

2. Zaloguj się przy użyciu konta platformy Azure, aby uzyskać dostęp do subskrypcji. Ten krok jest opcjonalny, jeśli używasz klastra testowego.

3. Wybierz listę rozwijaną **Punkt końcowy połączenia** i wybierz opcję „<Create New Cluster...>”.
    
    ![Okno dialogowe Publikowanie](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
4. W oknie dialogowym „Tworzenie klastra” zmodyfikuj następujące ustawienia:

    1. Określ nazwę klastra w polu „Nazwa klastra”, a także subskrypcję i lokalizację, których chcesz użyć.
    2. Opcjonalnie możesz zmodyfikować liczbę węzłów. Domyślne ustawienie to trzy węzły, czyli minimalna liczba wymagana do testowania scenariuszy usługi Service Fabric.
    3. Wybierz kartę „Certyfikat”. Na tej karcie wpisz hasło zabezpieczające certyfikat klastra. Ten certyfikat pomaga zabezpieczyć klaster. Możesz również zmodyfikować ścieżkę, w której ma zostać zapisany certyfikat. Program Visual Studio może również automatycznie zaimportować certyfikat, ponieważ jest to krok wymagany do opublikowania aplikacji w klastrze.
    4. Wybierz kartę „Szczegóły maszyny wirtualnej”. Określ hasło, którego chcesz używać dla maszyn wirtualnych wchodzących w skład klastra. Za pomocą nazwy użytkownika i hasła można zdalnie łączyć się z maszynami wirtualnymi. Należy również wybrać rozmiar maszyny wirtualnej oraz zmienić obraz maszyny wirtualnej, jeśli jest to konieczne.
    5. Opcjonalnie na karcie „Zaawansowane” można zmodyfikować listę portów, które mają zostać otwarte w module równoważenia obciążenia utworzonym wraz z klastrem. Można również dodać istniejący klucz usługi Application Insights, do którego mają być kierowane pliki dziennika aplikacji.
    6. Po zakończeniu modyfikowania ustawień kliknij przycisk „Utwórz”. Tworzenie klastra może zająć kilka minut. Informacja o ukończeniu tworzenia klastra pojawi się w oknie danych wyjściowych.
    
    ![Okno dialogowe Tworzenie klastra](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

4. Gdy klaster, który ma być używany, będzie gotowy, kliknij prawym przyciskiem myszy projekt aplikacji i wybierz polecenie **Publikuj**.

    Po zakończeniu publikowania powinno być możliwe wysłanie żądania do aplikacji za pośrednictwem przeglądarki.

5. Otwórz preferowaną przeglądarkę i wpisz adres klastra (punkt końcowy połączenia bez informacji o porcie — na przykład win1kw5649s.westus.cloudapp.azure.com).

    Powinien być teraz widoczny ten sam wynik co po lokalnym uruchomieniu aplikacji.

    ![Odpowiedź interfejsu API z klastra](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra z poziomu programu Visual Studio
> * Wdrażanie aplikacji w klastrze zdalnym przy użyciu programu Visual Studio

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Konfigurowanie ciągłej integracji za pomocą usługi Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
