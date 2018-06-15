---
title: Konfigurowanie usługi Azure Service Fabric Linux klastra w systemie Windows | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania klastry z systemem Linux sieci szkieletowej usług uruchomionych na maszynach rozwoju systemu Windows. Jest to szczególnie przydatne dla wielu aplikacji na wiele platform.
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
ms.openlocfilehash: ad0d383888c173ece5a7fbd3b0de690ed13074f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206297"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Konfigurowanie klastra sieci szkieletowej usług systemu Linux na komputerze dewelopera systemu Windows

W tym dokumencie opisano, jak skonfigurować lokalne sieci szkieletowej usług systemu Linux, na komputerach rozwoju systemu Windows. Konfigurowanie lokalnego klastra Linux przydaje się szybko przetestować aplikacje przeznaczone dla klastrów systemu Linux, ale są tworzone na komputerze z systemem Windows.

## <a name="prerequisites"></a>Wymagania wstępne
Opartych na systemie Linux klastrów sieci szkieletowej usług nie należy uruchamiać natywnie w systemie Windows. Uruchomienie lokalnego klastra usługi sieć szkieletowa podano wstępnie skonfigurowane obrazu kontenera Docker. Przed rozpoczęciem potrzebne są następujące elementy:

* Co najmniej 4 GB pamięci RAM
* Najnowsza wersja platformy [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)

>[!TIP]
> * Możesz wykonać czynności wymienionych w oficjalnego Docker [dokumentacji](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) do zainstalowania platformy Docker na systemu Windows. 
> * Po zakończeniu instalacji upewnij się, że została ona przeprowadzona pomyślnie, wykonując kroki opisane [tutaj](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Tworzenie kontenera lokalnego i konfigurowanie usługi Service Fabric
Konfigurowanie lokalnego kontenera Docker i klaster sieci szkieletowej usług na nim uruchomione, wykonaj następujące czynności w programie PowerShell:

1. Ściągnij obraz z repozytorium platformy Docker:

    ```powershell
    docker pull microsoft/service-fabric-onebox
    ```

2. Zaktualizuj konfigurację demona platformy Docker na swoim hoście za pomocą następujących ustawień i ponownie uruchom demona platformy Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Jest to zaleca sposób aktualizowania - przejdź do ikony Docker > Ustawienia > demon > Zaawansowane i zaktualizować go brak. Następnie uruchom ponownie demona Docker, aby zmiany zaczęły obowiązywać. 

3. Uruchom jednopunktowe wystąpienie kontenera Docker usługi Service Fabric przy użyciu obrazu:

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    > * Określenie nazwy wystąpienia kontenera pomoże usprawnić jego obsługę. 
    > * Jeśli Twoja aplikacja nasłuchuje na określonych portach, należy to określić za pomocą dodatkowych tagów -p. Na przykład jeśli aplikacja nasłuchuje na porcie 8080, uruchom docker Uruchom 8080:8080 -p 19080:19080 - itd -p — nazwę sfonebox microsoft/service — sieci szkieletowej — onebox

4. Zaloguj się do kontenera Docker w trybie interaktywnym ssh:

    ```powershell
    docker exec -it sfonebox bash
    ```

5. Uruchom skrypt instalacji, który pobierze wymagane zależności, a następnie uruchom klaster w kontenerze.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Po pomyślnym ukończeniu kroku 5, można przejść do ``http://localhost:19080`` z systemu Windows i będą mogli wyświetlić Eksploratora usługi sieć szkieletowa usług. W tym momencie można połączyć z tym klastrem przy użyciu dowolnego narzędzia z komputera developer i wdrażanie aplikacji dla sieci szkieletowej usług w systemie Linux klastrów. 

    > [!NOTE]
    > Wtyczka Eclipse obecnie nie jest obsługiwana w systemie Windows. 

## <a name="next-steps"></a>Kolejne kroki
* Rozpoczynanie pracy z [programu Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Zapoznaj się z innymi [przykłady Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png