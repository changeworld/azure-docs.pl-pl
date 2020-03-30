---
title: Konfigurowanie klastra sieci szkieletowej usługi Azure w systemie Windows
description: W tym artykule opisano sposób konfigurowania klastrów sieci szkieletowej sieci szkieletowej systemu Linux działających na komputerach deweloperskich z systemem Windows. Jest to szczególnie przydatne w przypadku tworzenia między platformami.
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 806e77a928d25e30aed24147525f74507bc32795
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462981"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Konfigurowanie klastra sieci szkieletowej usług systemu Linux na komputerze deweloperskim systemu Windows

W tym dokumencie opisano sposób konfigurowania lokalnej sieci szkieletowej usług systemu Linux na komputerach deweloperskich systemu Windows. Konfigurowanie lokalnego klastra linuksa jest przydatne do szybkiego testowania aplikacji przeznaczonych dla klastrów systemu Linux, ale są opracowywane na komputerze z systemem Windows.

## <a name="prerequisites"></a>Wymagania wstępne
Klastry sieci szkieletowej usług oparte na systemie Linux nie działają natywnie w systemie Windows. Aby uruchomić lokalny klaster sieci szkieletowej usług, udostępniany jest wstępnie skonfigurowany obraz kontenera platformy Docker. Przed rozpoczęciem potrzebne są następujące elementy:

* Co najmniej 4 GB pamięci RAM
* Najnowsza wersja platformy [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker musi działać w trybie Linux

>[!TIP]
> * Możesz wykonać kroki wymienione w oficjalnej [dokumentacji](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) platformy Docker, aby zainstalować program Docker w systemie Windows. 
> * Po zakończeniu instalacji upewnij się, że została ona przeprowadzona pomyślnie, wykonując kroki opisane [tutaj](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Tworzenie kontenera lokalnego i konfigurowanie usługi Service Fabric
Aby skonfigurować lokalny kontener platformy Docker i uruchomić na nim klaster sieci szkieletowej usług, wykonaj następujące czynności w programie PowerShell:


1. Zaktualizuj konfigurację demona platformy Docker na swoim hoście za pomocą następujących ustawień i ponownie uruchom demona platformy Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Zalecany sposób aktualizacji jest - przejdź do Docker Icon > Ustawienia > Daemon > Zaawansowane i zaktualizować go tam. Następnie uruchom ponownie demona platformy Docker, aby zmiany zostały wprowadzone. 

2. W nowym katalogu utwórz plik o nazwie `Dockerfile` w celu skompilowania obrazu usługi Service Fabric:

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Możesz dostosować ten plik, aby dodać dodatkowe programy lub zależności do kontenera.
    >Na przykład dodanie elementu `RUN apt-get install nodejs -y` umożliwi obsługę aplikacji `nodejs` jako plików wykonywalnych gościa.
    
    >[!TIP]
    > Domyślnie zostanie ściągnięty obraz z najnowszą wersją usługi Service Fabric. Poszczególne wersje są dostępne na stronie [usługi Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

3. Aby skompilować obraz do wielokrotnego użytku na podstawie pliku `Dockerfile`, otwórz terminal i element `cd` w lokalizacji bezpośrednio przechowującej plik `Dockerfile`, a następnie uruchom kod:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Ta operacja może zająć pewien czas, ale trzeba ją wykonać tylko raz.

4. Teraz możesz szybko w razie potrzeby uruchomić kopię lokalną usługi Service Fabric, uruchamiając kod:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Określ nazwę wystąpienia kontenera, aby usprawnić jego obsługę. 
    >
    >Jeśli Twoja aplikacja nasłuchuje na określonych portach, należy je określić za pomocą dodatkowych tagów `-p`. Jeśli na przykład aplikacja nasłuchuje na porcie 8080, dodaj następujący tag `-p`:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Uruchomienie klastra zajmie trochę czasu, można wyświetlać dzienniki za pomocą następującego polecenia lub przejść [http://localhost:19080](http://localhost:19080)do pulpitu nawigacyjnego, aby wyświetlić kondycję klastrów:

    ```powershell 
    docker logs sftestcluster
    ```

6. Po pomyślnym zakończeniu kroku 5 ``http://localhost:19080`` można przejść do systemu Windows i wyświetlić Eksploratora sieci szkieletowej usług. W tym momencie można połączyć się z tym klastrem przy użyciu dowolnych narzędzi z komputera deweloperskiego systemu Windows i wdrożyć aplikację przeznaczoną dla klastrów sieci szkieletowej usług systemu Linux. 

    > [!NOTE]
    > Wtyczka Eclipse obecnie nie jest obsługiwana w systemie Windows. 

7. Po zakończeniu zatrzymaj i oczyść kontener za pomocą tego polecenia:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Znane ograniczenia 
 
 Poniżej przedstawiono znane ograniczenia dotyczące klastrów lokalnych działających w kontenerze na komputerach Mac: 
 
 * Usługa DNS nie działa i nie jest obsługiwana: [problem #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do programu [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Zapoznaj się z innymi [przykładami java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
