---
title: Konfigurowanie klastra usługi Azure Service Fabric dla systemu Linux w Windows | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania klastrów usługi Service Fabric systemu Linux uruchamianych na maszynach rozwoju Windows. Jest to szczególnie przydatne w przypadku programowanie wieloplatformowych.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: e700250a6ebcdb82f99c1b460a510811d7ceb96c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719944"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Konfigurowanie klastra systemu Linux usługi Service Fabric na komputerze Windows dla deweloperów

W tym dokumencie opisano sposób konfigurowania lokalnego usługi Service Fabric systemu Linux na komputerach deweloperskich Windows. Konfigurowanie lokalnego klastra systemu Linux dzięki takiemu grupowaniu można szybko przetestować aplikacje przeznaczone dla klastrów systemu Linux, ale są tworzone na komputerze Windows.

## <a name="prerequisites"></a>Wymagania wstępne
Opartą na systemie Linux klastrów usługi Service Fabric nie działa natywnie w Windows. Aby uruchomić lokalny klaster usługi Service Fabric, znajduje się wstępnie skonfigurowany obraz kontenera Docker. Przed rozpoczęciem potrzebne są następujące elementy:

* Co najmniej 4 GB pamięci RAM
* Najnowsza wersja platformy [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Platformy docker musi być uruchomiona w trybie systemu Linux

>[!TIP]
> * Możesz wykonać kroki opisane w oficjalnej [dokumentacji](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) zainstalować platformę Docker na użytkownika Windows. 
> * Po zakończeniu instalacji upewnij się, że została ona przeprowadzona pomyślnie, wykonując kroki opisane [tutaj](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Tworzenie kontenera lokalnego i konfigurowanie usługi Service Fabric
Aby skonfigurować lokalny kontener platformy Docker i uruchomić na nim klaster usługi Service fabric, wykonaj następujące kroki w programie PowerShell:


1. Zaktualizuj konfigurację demona platformy Docker na swoim hoście za pomocą następujących ustawień i ponownie uruchom demona platformy Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Jest to zalecany sposób aktualizowania — wybierz pozycję ikona Docker > Ustawienia > demon > Zaawansowane i zaktualizować je ma. Następnie należy ponownie uruchomić demona platformy Docker, aby zmiany zaczęły obowiązywać. 

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

5. Klaster zostanie uruchomiony po krótkim czasie. Możesz wyświetlić dzienniki przy użyciu poniższego polecenia lub przejść do pulpitu nawigacyjnego, aby wyświetlić kondycję klastrów [http://localhost:19080](http://localhost:19080):

    ```powershell 
    docker logs sftestcluster
    ```

6. Po pomyślnym ukończeniu kroku 5 możesz przejść do ``http://localhost:19080`` z usługi Windows i będą mogli zobaczyć narzędzia Service Fabric explorer. W tym momencie możesz nawiązać połączenie z tego klastra przy użyciu dowolnych narzędzi z komputera dewelopera Windows i wdrażanie aplikacji, przeznaczone dla klastrów systemu Linux usługi Service Fabric. 

    > [!NOTE]
    > Wtyczka Eclipse obecnie nie jest obsługiwana w systemie Windows. 

7. Gdy wszystko będzie gotowe, zatrzymać i oczyścić kontener za pomocą następującego polecenia:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Znane ograniczenia 
 
 Poniżej przedstawiono znane ograniczenia dotyczące klastrów lokalnych działających w kontenerze na komputerach Mac: 
 
 * Usługa DNS nie działa i nie jest obsługiwana: [problem #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Kolejne kroki
* Rozpoczynanie pracy z usługą [środowiska Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Zapoznaj się z innymi [przykładów w języku Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
