---
title: 'Szybki start: tworzenie aplikacji Java w sieci szkieletowej usług Azure'
description: W ramach tego przewodnika Szybki start utworzysz aplikację Java dla platformy Azure za pomocą aplikacji przykładowej niezawodnych usług usługi Service Fabric.
author: suhuruli
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: fc615149b092aebfdde767fb3b716fb897bfd551
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121504"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Szybki start: wdrażanie aplikacji Java w sieci szkieletowej usługi Azure w systemie Linux

W tym przewodniku Szybki start można wdrożyć aplikację Java do sieci szkieletowej usług Azure przy użyciu środowiska Eclipse IDE na komputerze deweloperskim systemu Linux. Po zakończeniu pracy będziesz mieć aplikację do głosowania z frontonem internetowym w języku Java, która zapisuje wyniki głosowania w stanowej usłudze zaplecza w klastrze.

Usługa Azure Service Fabric to platforma systemów rozproszonych umożliwiająca wdrażanie mikrousług i kontenerów, a także zarządzanie nimi.

## <a name="prerequisites"></a>Wymagania wstępne

- [Środowisko Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) i [Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse Neon (4.6)+](https://www.eclipse.org/downloads/packages/) i [Eclipse plug-in dla usługi Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#install-the-eclipse-plug-in-optional)
- [Interfejs SDK sieci szkieletowej usług i interfejs wiersza polecenia (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>Pobierz przykład

W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Uruchamianie aplikacji lokalnie

1. Uruchom klaster lokalny, uruchamiając następujące polecenie:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Uruchamianie klastra lokalnego zajmuje nieco czasu. Aby potwierdzić, że klaster jest w pełni uruchomiony, otwórz narzędzie Service Fabric Explorer dostępne pod adresem `http://localhost:19080`. Pięć węzłów w dobrej kondycji oznacza, że klaster lokalny jest uruchomiony.

    ![Eksplorator sieci szkieletowej usług Azure pokazuje zdrowe węzły](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Otwórz program Eclipse.
3. Wybierz **opcję** > **Import** > pliku**Gradle** > **Existing Gradle Project** i postępuj zgodnie z kreatorem.
4. Wybierz **pozycję Katalog** i wybierz katalog **głosowania** z folderu **service-fabric-java-Quickstart** sklonowany z gitHub. Wybierz **pozycję Zakończ**.

    ![Zaimportuj projekt Gradle do Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Teraz projekt `Voting` będzie dostępny w widoku Eksplorator pakietów programu Eclipse.
6. Kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Publikuj aplikację** w obszarze listy rozwijanej **Sieci szkieletowej usług.** Wybierz **pozycję Publikuj profil/Local.json** jako profil docelowy i wybierz pozycję **Publikuj**.

    ![Sieć szkieletowa usług Azure publikuje lokalnego JSON](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Uruchom przeglądarkę internetową i uzyskaj dostęp do aplikacji, przechodząc do adresu `http://localhost:8080`.

    ![Host lokalny usługi Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Teraz możesz dodać zestaw opcji głosowania i rozpocząć obsługę głosów. Aplikacja zostanie uruchomiona i będzie przechować wszystkie dane w klastrze usługi Service Fabric, bez konieczności używania oddzielnej bazy danych.

![Przykład głosowania w sieci szkieletowej usług Azure](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalowanie aplikacji i usług w klastrze

Usługi można skalować na klaster w celu dostosowania ich do zmiany obciążenia. Skalowanie usługi odbywa się przez zmienianie liczby wystąpień uruchomionych w klastrze. Istnieje wiele sposobów skalowania usług. Na przykład można użyć skryptów lub poleceń`sfctl`z interfejsu wiersza polecenia sieci szkieletowej usług ( ). W poniższych krokach będzie używane narzędzie Service Fabric Explorer.

Eksplorator sieci szkieletowej usług działa we wszystkich klastrach sieci szkieletowej usług i jest dostępny z przeglądarki, przeglądając port zarządzania HTTP klastra (19080). Na przykład `http://localhost:19080`.

Aby skalować usługę internetową frontonu, wykonaj następujące czynności:

1. Otwórz Eksploratora sieci szkieletowej usług w klastrze. Na przykład `https://localhost:19080`.
2. Wybierz wielokropek (**...**) obok **sieci szkieletowej:/Głosowanie/GłosowanieWędej** w widoku drzewa i wybierz **opcję Skaluj usługę**.

    ![Skalowanie usługi w sieci szkieletowej usług Azure](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Teraz możesz skalować liczbę wystąpień usługi internetowej frontonu.

3. Zmień liczbę na **2** i wybierz **pozycję Skaluj usługę**.
4. Wybierz **węzeł tkaniny:/Voting/VotingWeb** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

    ![Skalowana usługa w sieci szkieletowej usług Azure](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Teraz widać, że usługa ma dwa wystąpienia, a w widoku drzewa można dostrzec węzły, w których są one uruchomione.

Za pomocą tego prostego zadania zarządzania zostały podwojone zasoby dostępne dla usługi frontonu na potrzeby przetwarzania obciążenia użytkownika. Pamiętaj, że nie musisz mieć wielu wystąpień usługi, aby działała ona niezawodnie. W przypadku awarii usługa Service Fabric gwarantuje uruchomienie nowego wystąpienie usługi w klastrze.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Używanie programu Eclipse jako narzędzia dla aplikacji Java usługi Service Fabric
* Wdrażanie aplikacji Java w klastrze lokalnym
* Skalowanie aplikacji w poziomie na wiele węzłów

Aby dowiedzieć się więcej o pracy z aplikacjami Java w usłudze Service Fabric, przejdź do samouczka dotyczącego aplikacji Java.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji Java](./service-fabric-tutorial-create-java-app.md)
