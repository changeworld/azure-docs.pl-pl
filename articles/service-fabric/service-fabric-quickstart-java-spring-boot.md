---
title: 'Szybki start: tworzenie aplikacji Spring Boot w sieci szkieletowej usług Azure'
description: W tym przewodniku Szybki start wdrożysz aplikację Spring Boot dla usługi Azure Service Fabric, korzystając z przykładowej aplikacji Spring Boot.
author: suhuruli
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: eb96989b4a2731e78471b848d690b48352408d1c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121481"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Szybki start: wdrażanie aplikacji Java Spring Boot w sieci szkieletowej usług Azure

W tym przewodniku Szybki start można wdrożyć aplikację Java Spring Boot w sieci szkieletowej usługi Azure przy użyciu znanych narzędzi wiersza polecenia w systemie Linux lub MacOS. Usługa Azure Service Fabric to platforma systemów rozproszonych umożliwiająca wdrażanie mikrousług i kontenerów, a także zarządzanie nimi. 

## <a name="prerequisites"></a>Wymagania wstępne

#### <a name="linux"></a>[Linux](#tab/linux)

- [Środowisko Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) i [Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Interfejs wiersza polecenia sieci szkieletowej sieci szkieletowej usług & usługi (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[Macos](#tab/macos)

- [Środowisko Java i Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
- [Interfejs wiersza polecenia sieci szkieletowej sieci szkieletowej usług & usługi (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminala uruchom następujące polecenie, aby sklonować [przykładową](https://github.com/spring-guides/gs-spring-boot) aplikację Wprowadzenie do rozruchu wiosennego na komputerze lokalnym.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Kompilowanie aplikacji Spring Boot 
Wewnątrz *katalogu gs-spring-boot/complete* uruchom poniższe polecenie, aby utworzyć aplikację 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>Tworzenie pakietu aplikacji Spring Boot 
1. Wewnątrz katalogu *gs-spring-boot* w klonu uruchom `yo azuresfguest` polecenie. 

1. Wprowadź następujące szczegóły dla każdego monitu.

    ![Wiosenne wpisy Yeoman Boot](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. W folderze *SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code* utwórz plik o nazwie *entryPoint.sh*. Dodaj następujący kod do pliku *entryPoint.sh.* 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. Dodawanie zasobu **punkty końcowe** w pliku *gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml*

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    Plik *ServiceManifest.xml* wygląda teraz następująco: 

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

    Uruchamianie klastra lokalnego zajmuje nieco czasu. Aby potwierdzić, że klaster jest w pełni uruchomiony, otwórz narzędzie Service Fabric Explorer dostępne pod adresem `http://localhost:19080`. Pięć węzłów w dobrej kondycji oznacza, że klaster lokalny jest uruchomiony. 
    
    ![Eksplorator sieci szkieletowej usług pokazuje zdrowe węzły](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. Otwórz folder *gs-spring-boot/SpringServiceFabric.*
1. Uruchom następujące polecenie, aby połączyć się z klastrem lokalnym.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Uruchom skrypt *install.sh.*

    ```bash
    ./install.sh
    ```

1. Uruchom przeglądarkę internetową i uzyskaj dostęp do aplikacji, przechodząc do adresu `http://localhost:8080`.

    ![Przykład tkaniny usługi rozruchu sprężynowego](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Teraz możesz uzyskiwać dostęp do aplikacji Spring Boot, która została wdrożona w klastrze usługi Service Fabric.

Aby uzyskać więcej informacji, zobacz przykład [wprowadzenie rozruchu](https://spring.io/guides/gs/spring-boot/) wiosennego w witrynie wiosennej.

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalowanie aplikacji i usług w klastrze

Usługi można skalować na klaster w celu dostosowania ich do zmiany obciążenia. Skalowanie usługi odbywa się przez zmienianie liczby wystąpień uruchomionych w klastrze. Istnieje wiele sposobów skalowania usług. Na przykład można użyć skryptów lub poleceń interfejsu wiersza polecenia usługi Service Fabric (sfctl). W poniższych krokach będzie używane narzędzie Service Fabric Explorer.

Narzędzie Service Fabric Explorer działa we wszystkich klastrach usługi Service Fabric i można uzyskać do niego dostęp z przeglądarki, przechodząc do portu HTTP zarządzania klastrami (19080), na przykład `http://localhost:19080`.

Aby skalować usługę internetową frontonu, wykonaj następujące czynności:

1. Otwórz narzędzie Service Fabric Explorer w klastrze — na przykład `http://localhost:19080`.
1. Wybierz wielokropek (**...**) obok **tkaniny:/SpringServiceFabric/SpringGettingStarted** węzeł w widoku drzewa i wybierz **opcję Skaluj usługę**.

    ![Przykład usługi Skalowanie usługi Eksploratora sieci szkieletowej usług](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Teraz możesz skalować liczbę wystąpień usługi.

1. Zmień liczbę na **3** i wybierz **pozycję Skaluj usługę**.

    Alternatywny sposób skalowania usługi to użycie wiersza polecenia w następujący sposób.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Wybierz **węzeł szkieletowy:/SpringServiceFabric/SpringGettingStarted** węzeł w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

    ![Ukończono usługę skalowania usługi Eksploratora sieci szkieletowej](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    Usługa ma trzy wystąpienia, a widok drzewa pokazuje węzły, na których wystąpienia są uruchomione.

Za pomocą tego prostego zadania zarządzania zostały podwojone zasoby dostępne dla usługi frontonu na potrzeby przetwarzania obciążenia użytkownika. Pamiętaj, że nie musisz mieć wielu wystąpień usługi, aby działała ona niezawodnie. W przypadku awarii usługa Service Fabric gwarantuje uruchomienie nowego wystąpienie usługi w klastrze.

## <a name="fail-over-services-in-a-cluster"></a>Przenoszenie usług do trybu failover w klastrze

Aby przedstawić przenoszenie usługi w tryb failover, ponowne uruchomienie węzła jest symulowane przy użyciu narzędzia Service Fabric Explorer. Upewnij się, że jest uruchomione tylko jedno wystąpienie usługi.

1. Otwórz narzędzie Service Fabric Explorer w klastrze — na przykład `http://localhost:19080`.
1. Wybierz wielokropek (**...**) obok węzła uruchomionego wystąpienia usługi i Uruchom ponownie węzeł.

    ![Węzeł ponownego uruchomienia Eksploratora sieci szkieletowej usług](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. Wystąpienie usługi zostanie przeniesione do innego węzła bez przestoju w działaniu aplikacji.

    ![Węzeł ponownego uruchomienia usługi Fabric Explorer powiódł się](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Wdrażanie aplikacji Spring Boot w usłudze Service Fabric
* Wdrażanie aplikacji w klastrze lokalnym
* Skalowanie aplikacji w poziomie na wiele węzłów
* Przenoszenie usługi w tryb failover bez wywierania wpływu na jej dostępność

Aby dowiedzieć się więcej o pracy z aplikacjami Java w usłudze Service Fabric, przejdź do samouczka dotyczącego aplikacji Java.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji Java](./service-fabric-tutorial-create-java-app.md)
