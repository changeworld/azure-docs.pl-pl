---
title: 'Szybki start: Tworzenie aplikacji z rozruchem wiosny na platformie Azure Service Fabric'
description: W tym przewodniku Szybki start wdrożysz aplikację Spring Boot dla usługi Azure Service Fabric, korzystając z przykładowej aplikacji Spring Boot.
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
ms.openlocfilehash: 2aa5879ee3960bd5d26855ac7e7c3e12994ee54e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861333"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Szybki start: Wdrażanie aplikacji rozruchowej ze sprężyną Java na platformie Azure Service Fabric

Ten przewodnik Szybki Start przedstawia sposób wdrażania aplikacji do rozruchu ze sprężyną Java na platformie Azure Service Fabric. Usługa Azure Service Fabric to platforma systemów rozproszonych umożliwiająca wdrażanie mikrousług i kontenerów, a także zarządzanie nimi. 

W tym przewodniku Szybki start użyto przykładu [Wprowadzenie](https://spring.io/guides/gs/spring-boot/) z witryny internetowej platformy Spring. W tym przewodniku Szybki start objaśniono wdrażanie przykładu ze środowiska Spring Boot jako aplikacji usługi Service Fabric za pomocą znanych narzędzi wiersza polecenia. Po zakończeniu w usłudze Service Fabric będzie działać przykład Wprowadzenie środowiska Spring Boot.

![Zrzut ekranu aplikacji](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Wdrażanie aplikacji Spring Boot w usłudze Service Fabric
* Wdrażanie aplikacji w klastrze lokalnym
* Skalowanie aplikacji w poziomie na wiele węzłów
* Przenoszenie usługi w tryb failover bez wywierania wpływu na jej dostępność

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

1. Zainstaluj zestaw SDK i interfejs wiersza polecenia usługi Service Fabric

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
1. Instalowanie narzędzia Yeoman

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
1. Skonfiguruj środowisko Java

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminala uruchom następujące polecenie, aby sklonować przykładową aplikację Wprowadzenie środowiska Spring Boot na komputer lokalny.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Kompilowanie aplikacji Spring Boot 
1. W katalogu `gs-spring-boot/complete` uruchom poniższe polecenie, aby skompilować aplikację 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Tworzenie pakietu aplikacji Spring Boot 
1. W sklonowanym katalogu `gs-spring-boot` uruchom polecenie `yo azuresfguest`. 

1. Wprowadź następujące szczegóły dla każdego monitu.

    ![Wpisy narzędzia Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

1. W folderze `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` utwórz plik o nazwie `entryPoint.sh`. Dodaj następującą zawartość do pliku `entryPoint.sh`. 

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

    Plik **ServiceManifest.xml** wygląda teraz następująco: 

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

Na tym etapie utworzono aplikację usługi Service Fabric dla przykładu Wprowadzenie środowiska Spring Boot, którą można wdrożyć w usłudze Service Fabric.

## <a name="run-the-application-locally"></a>Uruchamianie aplikacji lokalnie

1. Uruchom klaster lokalny na maszynach z systemem Ubuntu, uruchamiając następujące polecenie:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Jeśli korzystasz z komputera Mac, uruchom klaster lokalny z obrazu Docker (przy założeniu, że zostały spełnione [wymagania wstępne](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) konfiguracji klastra lokalnego dla komputerów Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Uruchamianie klastra lokalnego zajmuje nieco czasu. Aby potwierdzić, że klaster jest w pełni uruchomiony, otwórz narzędzie Service Fabric Explorer dostępne pod adresem **http://localhost:19080** . Pięć węzłów w dobrej kondycji oznacza, że klaster lokalny jest uruchomiony. 
    
    ![Klaster lokalny w dobrej kondycji](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

1. Otwórz folder `gs-spring-boot/SpringServiceFabric`.
1. Uruchom następujące polecenie, aby połączyć się z klastrem lokalnym.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Uruchom skrypt `install.sh`.

    ```bash
    ./install.sh
    ```

1. Otwórz ulubioną przeglądarkę internetową i uzyskaj dostęp do aplikacji `http://localhost:8080`przez uzyskanie dostępu do programu.

    ![Fronton aplikacji — lokalny](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Teraz możesz uzyskiwać dostęp do aplikacji Spring Boot, która została wdrożona w klastrze usługi Service Fabric.

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalowanie aplikacji i usług w klastrze

Usługi można skalować na klaster w celu dostosowania ich do zmiany obciążenia. Skalowanie usługi odbywa się przez zmienianie liczby wystąpień uruchomionych w klastrze. Istnieje wiele sposobów skalowania usług. Na przykład można użyć skryptów lub poleceń interfejsu wiersza polecenia usługi Service Fabric (sfctl). W poniższych krokach będzie używane narzędzie Service Fabric Explorer.

Narzędzie Service Fabric Explorer działa we wszystkich klastrach usługi Service Fabric i można uzyskać do niego dostęp z przeglądarki, przechodząc do portu HTTP zarządzania klastrami (19080), na przykład `http://localhost:19080`.

Aby skalować usługę internetową frontonu, wykonaj następujące czynności:

1. Otwórz narzędzie Service Fabric Explorer w klastrze — na przykład `http://localhost:19080`.
1. Wybierz wielokropek ( **...** ) obok węzła **Sieć szkieletowa:/SpringServiceFabric/SpringGettingStarted** w widoku drzewa i wybierz pozycję **Skaluj usługę**.

    ![Usługa skalowania narzędzia Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Teraz możesz skalować liczbę wystąpień usługi.

1. Zmień liczbę na **3** i wybierz pozycję **Skaluj usługę**.

    Alternatywny sposób skalowania usługi to użycie wiersza polecenia w następujący sposób.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Wybierz węzeł **Sieć szkieletowa:/SpringServiceFabric/SpringGettingStarted** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

    ![Usługa skalowania narzędzia Service Fabric Explorer — zakończone](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    Usługa ma trzy wystąpienia, a widok drzewa pokazuje węzły, na których wystąpienia są uruchomione.

Za pomocą tego prostego zadania zarządzania zostały podwojone zasoby dostępne dla usługi frontonu na potrzeby przetwarzania obciążenia użytkownika. Pamiętaj, że nie musisz mieć wielu wystąpień usługi, aby działała ona niezawodnie. W przypadku awarii usługa Service Fabric gwarantuje uruchomienie nowego wystąpienie usługi w klastrze.

## <a name="fail-over-services-in-a-cluster"></a>Przenoszenie usług do trybu failover w klastrze

Aby przedstawić przenoszenie usługi w tryb failover, ponowne uruchomienie węzła jest symulowane przy użyciu narzędzia Service Fabric Explorer. Upewnij się, że jest uruchomione tylko jedno wystąpienie usługi.

1. Otwórz narzędzie Service Fabric Explorer w klastrze — na przykład `http://localhost:19080`.
1. Wybierz wielokropek ( **...** ) obok węzła, na którym działa wystąpienie usługi, a następnie uruchom ponownie węzeł.

    ![Service Fabric Explorer — ponowne uruchamianie węzła](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
1. Wystąpienie usługi zostanie przeniesione do innego węzła bez przestoju w działaniu aplikacji.

    ![Service Fabric Explorer — ponowne uruchamianie węzła zakończone powodzeniem](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Wdrażanie aplikacji Spring Boot w usłudze Service Fabric
* Wdrażanie aplikacji w klastrze lokalnym
* Skalowanie aplikacji w poziomie na wiele węzłów
* Przenoszenie usługi w tryb failover bez wywierania wpływu na jej dostępność

Aby dowiedzieć się więcej o pracy z aplikacjami Java w usłudze Service Fabric, przejdź do samouczka dotyczącego aplikacji Java.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji Java](./service-fabric-tutorial-create-java-app.md)
