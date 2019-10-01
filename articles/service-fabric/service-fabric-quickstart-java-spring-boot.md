---
title: 'Szybki Start: Tworzenie aplikacji z rozruchem wiosny na platformie Azure Service Fabric'
description: W tym przewodniku szybki start wdrożono aplikację z rozruchem wiosny dla platformy Azure Service Fabric przy użyciu przykładowej aplikacji do rozruchu sprężynowego.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 4ec6addb348b8c13f124ec225e056d2003a93c38
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703519"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Szybki Start: wdrażanie aplikacji do rozruchu ze sprężyną Java na platformie Azure Service Fabric

Ten przewodnik Szybki Start przedstawia sposób wdrażania aplikacji do rozruchu ze sprężyną Java na platformie Azure Service Fabric. Azure Service Fabric to platforma systemów rozproszonych umożliwiająca wdrażanie mikrousług i kontenerów oraz zarządzanie nimi. 

Ten przewodnik Szybki Start używa przykładu [wprowadzenie](https://spring.io/guides/gs/spring-boot/) z witryny sieci Web sprężyny. Korzystając z znanych narzędzi wiersza polecenia, ten przewodnik Szybki Start przeprowadzi Cię przez proces wdrażania sprężynowego przykładu rozruchu jako aplikacji Service Fabric. Po zakończeniu będziesz mieć Wprowadzenie próbka rozruchowego ze sprężyną Service Fabric.

![Przykład Service Fabric rozruchu sprężynowego](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

W tym przewodniku szybki start dowiesz się, jak:

* Wdróż aplikację do rozruchu ze sprężyną do Service Fabric
* Wdrażanie aplikacji w klastrze lokalnym
* Skalowanie aplikacji w poziomie między wieloma węzłami
* Przeprowadź tryb failover usługi bez trafień do dostępności

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start:

1. Instalowanie Service Fabric SDK & Service Fabric wierszu polecenia (CLI)

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [System](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

1. [Instalowanie usługi git](https://git-scm.com/)
1. Zainstaluj narzędzia Yeoman

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [System](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
1. Konfigurowanie środowiska Java

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [System](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminalu uruchom następujące polecenie, aby sklonować przykładową aplikację z rozruchem sprężyny Wprowadzenie do komputera lokalnego.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Tworzenie aplikacji do rozruchu ze sprężyną 
1. W katalogu `gs-spring-boot/complete` uruchom poniższe polecenie, aby skompilować aplikację 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Pakowanie aplikacji do rozruchu sprężynowego 
1. W katalogu `gs-spring-boot` w klonie Uruchom polecenie `yo azuresfguest`. 

1. Wprowadź następujące szczegóły dla każdego monitu.

    ![Wpisy narzędzia Yeoman rozruchu sprężynowego](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. W folderze `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` Utwórz plik o nazwie `entryPoint.sh`. Dodaj następujący plik do pliku `entryPoint.sh`. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. Dodaj zasób **punktów końcowych** w pliku *GS-Spring-Boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/servicemanifest. XML*

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    **Plik servicemanifest. XML** wygląda teraz następująco: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

Na tym etapie utworzono aplikację Service Fabric dla przykładowego Wprowadzenie rozruchu sprężynowego, którą można wdrożyć w Service Fabric.

## <a name="run-the-application-locally"></a>Uruchamianie aplikacji lokalnie

1. Uruchom klaster lokalny na maszynach Ubuntu, uruchamiając następujące polecenie:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    W przypadku korzystania z komputera Mac Uruchom klaster lokalny z obrazu platformy Docker (zakładając, że zostały spełnione [wymagania wstępne](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) dotyczące konfigurowania lokalnego klastra dla komputerów Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Uruchamianie klastra lokalnego zajmuje trochę czasu. Aby upewnić się, że klaster jest w pełni skonfigurowany, uzyskaj dostęp do Service Fabric Explorer w **http://localhost:19080** . Pięć węzłów w dobrej kondycji wskazuje, że klaster lokalny jest uruchomiony. 
    
    ![Service Fabric Explorer wyświetla węzły w dobrej kondycji](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. Otwórz folder `gs-spring-boot/SpringServiceFabric`.
1. Uruchom następujące polecenie, aby połączyć się z lokalnym klastrem.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Uruchom skrypt `install.sh`.

    ```bash
    ./install.sh
    ```

1. Otwórz ulubioną przeglądarkę sieci Web i uzyskaj dostęp do aplikacji, uzyskując dostęp do `http://localhost:8080`.

    ![Przykład Service Fabric rozruchu sprężynowego](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Teraz możesz uzyskać dostęp do aplikacji sieci rozruchu ze sprężyną, która została wdrożona w klastrze Service Fabric.

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalowanie aplikacji i usług w klastrze

Usługi mogą być skalowane w klastrze w celu uwzględnienia zmiany obciążenia usług. Skalowanie usługi przez zmianę liczby wystąpień uruchomionych w klastrze. Istnieje wiele sposobów skalowania usług. na przykład można użyć skryptów lub poleceń z Service Fabric interfejsu wiersza polecenia (sfctl). Poniższe kroki używają Service Fabric Explorer.

Service Fabric Explorer działa we wszystkich klastrach Service Fabric i można uzyskać do nich dostęp z przeglądarki, przechodząc do portu HTTP zarządzania klastrami (19080). na przykład `http://localhost:19080`.

Aby skalować usługę internetową frontonu, wykonaj następujące czynności:

1. Otwórz Service Fabric Explorer w klastrze — na przykład `http://localhost:19080`.
1. Wybierz wielokropek ( **...** ) obok węzła **Sieć szkieletowa:/SpringServiceFabric/SpringGettingStarted** w widoku drzewa i wybierz pozycję **Skaluj usługę**.

    ![Przykład Service Fabric Explorer skalowania usługi](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Teraz możesz skalować liczbę wystąpień usługi.

1. Zmień liczbę na **3** i wybierz pozycję **Skaluj usługę**.

    Alternatywny sposób skalowania usługi przy użyciu wiersza polecenia znajduje się w następujący sposób.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Wybierz węzeł **Sieć szkieletowa:/SpringServiceFabric/SpringGettingStarted** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

    ![Zakończono Service Fabric Explorer skalowanie usługi](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    Usługa ma trzy wystąpienia, a w widoku drzewa są wyświetlane węzły, w których są uruchamiane wystąpienia.

Za pomocą tego prostego zadania zarządzania zostały podwojone zasoby dostępne dla usługi frontonu w celu przetworzenia obciążenia użytkownika. Ważne jest, aby zrozumieć, że nie musisz mieć wielu wystąpień usługi, aby mogła działać niezawodnie. W przypadku niepowodzenia usługi Service Fabric upewnij się, że w klastrze jest uruchomione nowe wystąpienie usługi.

## <a name="fail-over-services-in-a-cluster"></a>Przełączanie usług do trybu failover w klastrze

Aby zademonstrować przełączenie usługi do trybu failover, jest symulowane ponowne uruchomienie węzła przy użyciu Service Fabric Explorer. Upewnij się, że jest uruchomione tylko jedno wystąpienie usługi.

1. Otwórz Service Fabric Explorer w klastrze — na przykład `http://localhost:19080`.
1. Wybierz wielokropek ( **...** ) obok węzła, na którym działa wystąpienie usługi, a następnie uruchom ponownie węzeł.

    ![Service Fabric Explorer ponownie uruchom węzeł](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. Wystąpienie usługi jest przenoszone do innego węzła, a aplikacja nie ma żadnych przestojów.

    ![Ponowne uruchomienie węzła Service Fabric Explorer powiodło się](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób wykonywania tych instrukcji:

* Wdróż aplikację do rozruchu ze sprężyną do Service Fabric
* Wdrażanie aplikacji w klastrze lokalnym
* Skalowanie aplikacji w poziomie między wieloma węzłami
* Przeprowadź tryb failover usługi bez trafień do dostępności

Aby dowiedzieć się więcej na temat pracy z aplikacjami Java w Service Fabric, przejdź do samouczka dotyczącego aplikacji Java.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji Java](./service-fabric-tutorial-create-java-app.md)
