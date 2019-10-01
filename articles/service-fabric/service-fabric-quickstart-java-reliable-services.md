---
title: 'Szybki Start: Tworzenie aplikacji Java na platformie Azure Service Fabric'
description: W tym przewodniku szybki start utworzysz aplikację Java dla platformy Azure za pomocą aplikacji przykładowej niezawodnych usług Service Fabric.
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
ms.openlocfilehash: 4ef0db5f72f5849942bb043261f1166cf7c046b1
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703296"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Szybki Start: wdrażanie aplikacji Java na platformie Azure Service Fabric w systemie Linux

W tym przewodniku szybki start pokazano, jak wdrożyć pierwszą aplikację w języku Java na platformie Azure Service Fabric przy użyciu IDE na komputerze dewelopera systemu Linux. Po zakończeniu będziesz mieć aplikację do głosowania z frontonem sieci Web w języku Java, która zapisuje wyniki głosowania w stanowej usłudze zaplecza w klastrze.

Azure Service Fabric to platforma systemów rozproszonych umożliwiająca wdrażanie mikrousług i kontenerów oraz zarządzanie nimi.

![Przykład głosowania Service Fabric platformy Azure](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

W tym przewodniku szybki start dowiesz się, jak:

* Korzystanie z programu zaService Fabricj do obsługi aplikacji Java
* Wdrażanie aplikacji w klastrze lokalnym
* Skalowanie aplikacji w poziomie między wieloma węzłami

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start:

1. [Instalowanie Service Fabric SDK & Service Fabric wierszu polecenia (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Instalowanie usługi git](https://git-scm.com/)
3. [Zainstaluj przezaćmienie](https://www.eclipse.org/downloads/)
4. [Skonfiguruj środowisko Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)i upewnij się, że wykonaj kroki opcjonalne, aby zainstalować wtyczkę programu zaćmienie

## <a name="download-the-sample"></a>Pobierz przykład

W oknie polecenia Uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na maszynę lokalną.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Uruchamianie aplikacji lokalnie

1. Uruchom klaster lokalny, uruchamiając następujące polecenie:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Uruchamianie klastra lokalnego zajmuje trochę czasu. Aby upewnić się, że klaster jest w pełni skonfigurowany, uzyskaj dostęp do Service Fabric Explorer w **http://localhost:19080** . Pięć węzłów w dobrej kondycji wskazuje, że klaster lokalny jest uruchomiony.

    ![Usługa Azure Service Fabric Explorer wyświetla w dobrej kondycji węzły](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Otwórz przezaćmienie.
3. Wybierz pozycję **plik** > **Importuj** > **Gradle** > **istniejący projekt Gradle** i postępuj zgodnie z instrukcjami kreatora.
4. Wybierz pozycję **katalog** i wybierz katalog `Voting` z folderu `service-fabric-java-quickstart`, który został sklonowany z usługi GitHub. Wybierz pozycję **Zakończ**.

    ![Importuj projekt Gradle do przezaćmienia](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Masz teraz projekt `Voting` w Eksploratorze pakietów do przeszukania.
6. Kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Opublikuj aplikację** na liście rozwijanej **Service Fabric** . Wybierz pozycję **PublishProfiles/Local. JSON** jako profil docelowy, a następnie wybierz pozycję **Publikuj**.

    ![Usługa Azure Service Fabric publikowanie lokalnego pliku JSON](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Otwórz ulubioną przeglądarkę sieci Web i uzyskaj dostęp do aplikacji, uzyskując dostęp do `http://localhost:8080`.

    ![Host lokalny Service Fabric platformy Azure](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Teraz możesz dodać zestaw opcji głosowania i zacząć uczestniczyć w głosowaniu. Aplikacja działa i przechowuje wszystkie dane w klastrze Service Fabric, bez potrzeby oddzielenia bazy danych.

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalowanie aplikacji i usług w klastrze

Usługi mogą być skalowane w klastrze w celu uwzględnienia zmiany obciążenia usług. Skalowanie usługi przez zmianę liczby wystąpień uruchomionych w klastrze. Istnieje wiele sposobów skalowania usług. można na przykład użyć skryptów lub poleceń w interfejsie wiersza polecenia Service Fabric (sfctl). Poniższe kroki używają Service Fabric Explorer.

Service Fabric Explorer działa we wszystkich klastrach Service Fabric i można uzyskać do nich dostęp z przeglądarki, przechodząc do portu HTTP zarządzania klastrami (19080). na przykład `http://localhost:19080`.

Aby skalować usługę internetową frontonu, wykonaj następujące czynności:

1. Otwórz Service Fabric Explorer w klastrze — na przykład `https://localhost:19080`.
2. Wybierz wielokropek ( **...** ) obok węzła **Sieć szkieletowa:/głosu/VotingWeb** w elemencie TreeView i wybierz pozycję **Skaluj usługę**.

    ![Skalowanie usługi w usłudze Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Teraz możesz skalować liczbę wystąpień usługi frontonu sieci Web.

3. Zmień liczbę na **2** i wybierz pozycję **Skaluj usługę**.
4. Wybierz węzeł **Sieć szkieletowa:/głosowania/VotingWeb** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

    ![Usługa skalowana w Service Fabric na platformie Azure](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Teraz można zobaczyć, że usługa ma dwa wystąpienia, a w widoku drzewa widoczne są węzły, w których są uruchamiane wystąpienia.

Za pomocą tego prostego zadania zarządzania zostały podwojone zasoby dostępne dla usługi frontonu w celu przetworzenia obciążenia użytkownika. Ważne jest, aby zrozumieć, że nie musisz mieć wielu wystąpień usługi, aby mogła działać niezawodnie. W przypadku niepowodzenia usługi Service Fabric upewnij się, że w klastrze jest uruchomione nowe wystąpienie usługi.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób wykonywania tych instrukcji:

* Korzystanie z programu zaService Fabricj do obsługi aplikacji Java
* Wdrażanie aplikacji Java w klastrze lokalnym
* Skalowanie aplikacji w poziomie między wieloma węzłami

Aby dowiedzieć się więcej na temat pracy z aplikacjami Java w Service Fabric, przejdź do samouczka dotyczącego aplikacji Java.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji Java](./service-fabric-tutorial-create-java-app.md)
