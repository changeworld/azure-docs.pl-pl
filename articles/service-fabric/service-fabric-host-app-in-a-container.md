---
title: Wdrażanie aplikacji .NET w kontenerze w usłudze Azure Service Fabric | Microsoft Docs
description: Przedstawiono tu sposób pakowania aplikacji .NET w kontenerze platformy Docker w programie Visual Studio. Ta nowa aplikacja „kontenerowa” jest następnie wdrażana w klastrze usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 11bb34939e5fa3699973051664e85f9ef2147ff7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Wdrażanie aplikacji .NET w kontenerze systemu Windows w usłudze Azure Service Fabric

W tym samouczku przedstawiono sposób wdrażania istniejącej aplikacji ASP.NET w kontenerze systemu Windows na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie projektu platformy Docker w programie Visual Studio
> * Konteneryzowanie istniejącej aplikacji
> * Konfigurowanie ciągłej integracji z programem Visual Studio i usługą VSTS

## <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj program [Docker CE dla systemu Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), który umożliwia uruchamianie kontenerów w systemie Windows 10.
2. Zapoznaj się z przewodnikiem [Szybki start z kontenerami systemu Windows 10][link-container-quickstart].
3. Pobierz aplikację przykładową [Fabrikam Fiber CallCenter][link-fabrikam-github].
4. Zainstaluj program [Azure PowerShell][link-azure-powershell-install].
5. Zainstaluj [rozszerzenie Continuous Delivery Tools dla programu Visual Studio 2017][link-visualstudio-cd-extension].
6. Utwórz [subskrypcję platformy Azure][link-azure-subscription] i [konto usługi Visual Studio Team Services][link-vsts-account]. 
7. [Tworzenie klastra na platformie Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="create-a-cluster-on-azure"></a>Tworzenie klastra na platformie Azure
Aplikacje usługi Service Fabric działają w klastrze — połączonym z siecią zestawie maszyn wirtualnych lub fizycznych. Przed utworzeniem i wdrożeniem aplikacji [skonfiguruj klaster usługi Service Fabric działający na platformie Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md). Podczas tworzenia klastra wybierz jednostkę SKU obsługującą uruchamianie kontenerów (np. Windows Server Datacenter 2016 z kontenerami).

## <a name="containerize-the-application"></a>Konteneryzowanie aplikacji

Teraz, gdy klaster usługi Service Fabric działa na platformie Azure, możesz przystąpić do utworzenia i wdrożenia skonteneryzowanej aplikacji. Aby nasza aplikacja zaczęła działać w kontenerze, do projektu programu Visual Studio musimy dodać **obsługę platformy Docker**. Po dodaniu **obsługi platformy Docker** do aplikacji dzieją się dwie rzeczy. Najpierw do projektu jest dodawany plik _Dockerfile_. W tym nowym pliku jest opisany sposób kompilacji obrazu kontenera. Następnie do rozwiązania jest dodawany nowy projekt _docker-compose_. Ten nowy projekt zawiera kilka plików docker-compose. Pliki docker-compose mogą być używane do opisywania sposobu uruchamiania kontenera.

Więcej informacji na temat pracy z [narzędziami kontenerów programu Visual Studio][link-visualstudio-container-tools].

### <a name="add-docker-support"></a>Dodawanie obsługi platformy Docker

Otwórz plik [FabrikamFiber.CallCenter.sln][link-fabrikam-github] w programie Visual Studio.

Kliknij prawym przyciskiem myszy projekt **FabrikamFiber.Web** i wybierz pozycję **Dodaj** > **Obsługa platformy Docker**.

### <a name="add-support-for-sql"></a>Dodawanie obsługi języka SQL

Ta aplikacja używa języka SQL jako dostawcy danych, dlatego do jej uruchomienia wymagany jest program SQL Server. Przywołaj obraz kontenera programu SQL Server w naszym pliku docker-compose.override.yml.

W programie Visual Studio otwórz **Eksploratora rozwiązań**, znajdź projekt **docker-compose** i otwórz plik **docker-compose.override.yml**.

Przejdź do węzła `services:` i dodaj węzeł o nazwie `db:` definiujący wpis programu SQL Server dla kontenera.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>Na potrzeby lokalnego debugowania można używać dowolnego programu SQL Server, ale musi on być dostępny z hosta. Baza danych **localdb** nie obsługuje jednak komunikacji typu `container -> host`.

>[!WARNING]
>Podczas uruchamiania programu SQL Server w kontenerze utrwalanie danych nie jest obsługiwane. Po zatrzymaniu kontenera dane są usuwane. Nie należy używać tej konfiguracji w środowisku produkcyjnym.

Przejdź do węzła `fabrikamfiber.web:` i dodaj węzeł podrzędny o nazwie `depends_on:`. Zagwarantuje to, że usługa `db` (kontener programu SQL Server) uruchomi się przed naszą aplikacją internetową (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Aktualizowanie konfiguracji internetowej

W projekcie **FabrikamFiber.Web** zaktualizuj parametry połączenia w pliku **web.config**, aby wskazywały na program SQL Server w kontenerze.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Jeśli podczas tworzenia kompilacji wydania aplikacji internetowej ma zostać użyty inny program SQL Server, dodaj następne parametry połączenia do pliku web.release.config.

### <a name="test-your-container"></a>Testowanie kontenera

Naciśnij klawisz **F5**, aby uruchomić i zdebugować aplikację w kontenerze.

W przeglądarce Edge zostanie otwarta zdefiniowana strona uruchamiania Twojej aplikacji przy użyciu adresu IP kontenera w sieci wewnętrznej translatora adresów sieciowych (zazwyczaj 172.x.x.x). Aby dowiedzieć się więcej na temat debugowania aplikacji w kontenerach przy użyciu programu Visual Studio 2017, zobacz [ten artykuł][link-debug-container].

![Przykładowa aplikacja Fabrikam w kontenerze][image-web-preview]

Kontener jest teraz gotowy do skompilowania i spakowania w aplikację usługi Service Fabric. Po skompilowaniu obrazu kontenera na komputerze możesz wypchnąć go do dowolnego rejestru kontenerów, a następnie ściągnąć na dowolny host w celu uruchomienia.

## <a name="get-the-application-ready-for-the-cloud"></a>Przygotowywanie aplikacji do działania w chmurze

Aby przygotować aplikację do uruchamiania w usłudze Service Fabric na platformie Azure, musimy wykonać dwie czynności:

1. Uwidocznić port, na którym ma być dostępna nasza aplikacja internetowa w klastrze usługi Service Fabric.
2. Udostępnić bazę danych SQL gotową na środowisko produkcyjne naszej aplikacji.

### <a name="expose-the-port-for-the-app"></a>Uwidacznianie portu dla aplikacji
Skonfigurowany przez nas klaster usługi Service Fabric ma domyślnie otwarty port *80* w usłudze Azure Load Balancer, która równoważy ruch przychodzący do klastra. Możemy uwidocznić nasz kontener na tym porcie za pośrednictwem pliku docker-compose.yml.

W programie Visual Studio otwórz **Eksploratora rozwiązań**, znajdź projekt **docker-compose** i otwórz plik **docker-compose.yml**.

Zmodyfikuj węzeł `fabrikamfiber.web:` przez dodanie węzła podrzędnego o nazwie `ports:`.

Dodaj wpis ciągu `- "80:80"`. Plik docker-compose.yml powinien wyglądać następująco:

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>Korzystanie z produkcyjnej bazy danych SQL
Kiedy aplikacja działa w środowisku produkcyjnym, chcemy, aby dane były utrwalane w naszej bazie danych. Obecnie nie ma możliwości zagwarantowania trwałości danych w kontenerze, dlatego nie można zapisywać danych produkcyjnych w programie SQL Server w kontenerze.

Zalecamy korzystanie z usługi Azure SQL Database. Aby uzyskać informacje na temat konfigurowania i uruchamiania zarządzanego serwera SQL na platformie Azure, zapoznaj się z [przewodnikami Szybki start usługi Azure SQL Database][link-azure-sql].

>[!NOTE]
>Należy pamiętać, aby zmienić parametry połączenia z serwerem SQL w pliku **web.release.config** w projekcie **FabrikamFiber.Web**.
>
>Działanie tej aplikacji nie powiedzie się, jeśli nie będzie dostępna żadna baza danych SQL. Można kontynuować i wdrożyć aplikację bez serwera SQL.

## <a name="deploy-with-visual-studio-team-services"></a>Wdrażanie w usłudze Visual Studio Team Services

Aby skonfigurować wdrożenie przy użyciu usługi Visual Studio Team Services, musisz zainstalować [rozszerzenie Continuous Delivery Tools dla programu Visual Studio 2017][link-visualstudio-cd-extension]. To rozszerzenie ułatwia wdrażanie na platformie Azure przez skonfigurowanie usługi Visual Studio Team Services i wdrożenie aplikacji w klastrze usługi Service Fabric.

Aby rozpocząć, kod musi być hostowany w kontroli kodu źródłowego. W pozostałej części tej sekcji przyjęto założenie, że używane jest repozytorium **git**.

### <a name="set-up-a-vsts-repo"></a>Konfigurowanie repozytorium VSTS
W prawym dolnym rogu ekranu programu Visual Studio kliknij opcję **Dodaj do kontroli źródła** > **Git** (lub inną wybraną opcję).

![Naciśnięcie przycisku kontroli źródła][image-source-control]

W okienku _Team Explorer_ naciśnij przycisk **Publikuj repozytorium Git**.

Wybierz nazwę repozytorium usługi VSTS i naciśnij przycisk **Repozytorium**.

![Publikowanie repozytorium do usługi VSTS][image-publish-repo]

Po zsynchronizowaniu kodu z repozytorium źródłowym usługi VSTS można skonfigurować ciągłą integrację i ciągłe dostarczanie.

### <a name="setup-continuous-delivery"></a>Konfigurowanie ciągłego dostarczania

W _Eksploratorze rozwiązań_ kliknij prawym przyciskiem myszy **rozwiązanie** > **Konfiguruj ciągłe dostarczanie**.

Wybierz subskrypcję platformy Azure.

Ustaw **Typ hosta** na wartość **Klaster usługi Service Fabric**.

Ustaw **Host docelowy** na utworzony w poprzedniej sekcji klaster usługi Service Fabric.

Wybierz **Rejestr kontenerów**, w którym ma zostać opublikowany kontener.

>[!TIP]
>Użyj przycisku **Edytuj**, aby utworzyć rejestr kontenerów.

Naciśnij przycisk **OK**.

![Konfigurowanie ciągłej integracji usługi Service Fabric][image-setup-ci]
   
   Zakończenie konfiguracji oznacza wdrożenie kontenera do usługi Service Fabric. Każdorazowo po wypchnięciu aktualizacji do repozytorium wykonywana jest nowa kompilacja i wydanie.
   
   >[!NOTE]
   >Kompilowanie obrazów kontenerów trwa około 15 minut.
   >Pierwsze wdrożenie w klastrze usługi Service Fabric powoduje pobranie podstawowych obrazów kontenerów systemu Windows Server Core. Pobieranie trwa dodatkowe 5–10 minut.

Przejdź do aplikacji Fabrikam Call Center przy użyciu adresu URL klastra, na przykład: *http://mycluster.westeurope.cloudapp.azure.com*

Teraz, po umieszczeniu w kontenerze i wdrożeniu rozwiązania Fabrikam Call Center, możesz otworzyć witrynę [Azure Portal][link-azure-portal] i zobaczyć aplikację uruchomioną w usłudze Service Fabric. Aby wypróbować aplikację, otwórz przeglądarkę internetową i przejdź do adresu URL Twojego klastra usługi Service Fabric.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie projektu platformy Docker w programie Visual Studio
> * Konteneryzowanie istniejącej aplikacji
> * Konfigurowanie ciągłej integracji z programem Visual Studio i usługą VSTS

W następnej części tego samouczka dowiesz się, jak skonfigurować [monitorowanie kontenera](service-fabric-tutorial-monitoring-wincontainers.md).

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
