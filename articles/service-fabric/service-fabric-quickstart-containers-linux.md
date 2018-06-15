---
title: Tworzenie aplikacji kontenera usługi Azure Service Fabric w systemie Linux | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki start utworzysz pierwszą aplikację kontenera systemu Linux w usłudze Azure Service Fabric.  Zbuduj obraz Docker za pomocą własnej aplikacji, wypchnij obraz do rejestru kontenerów, skompiluj i wdróż aplikację kontenera usługi Service Fabric.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 65f048d67ef5f250691700a382e781814c57e8a8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31416374"
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Szybki start: wdrażanie aplikacji kontenera systemu Linux w usłudze Azure Service Fabric na platformie Azure
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych oraz niezawodnych mikrousług i kontenerów, a także zarządzanie nimi. 

W tym przewodniku Szybki start przedstawiono sposób wdrażania kontenerów systemu Linux w klastrze usługi Service Fabric. Po wykonaniu tych czynności będziesz mieć gotową aplikację do głosowania składającą się z frontonu internetowego w języku Python oraz zaplecza Redis działającego w klastrze usługi Service Fabric. Dowiesz się też, jak przenieść aplikację do trybu failover oraz jak skalować aplikację w klastrze.

![Strona internetowa aplikacji do głosowania][quickstartpic]

W tym przewodniku Szybki start użyjesz środowiska Bash w usłudze Azure Cloud Shell do uruchamiania poleceń interfejsu wiersza polecenia usługi Service Fabric. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli po raz pierwszy uruchamiasz usługę Cloud Shell, zostanie wyświetlona prośba o skonfigurowanie Twojego udziału plików `clouddrive`. Możesz zaakceptować wartości domyślne lub dołączyć istniejący udział plików. Aby dowiedzieć się więcej, zobacz [Konfigurowanie udziału plików `clouddrive`](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share).

## <a name="get-the-application-package"></a>Pobieranie pakietu aplikacji
Do wdrożenia kontenerów w usłudze Service Fabric potrzebujesz zestawu plików manifestu (definicja aplikacji), które opisują poszczególne kontenery i aplikację.

W usłudze Cloud Shell użyj usługi Git, aby sklonować kopię definicji aplikacji, a następnie przejdź do katalogu `Voting` w klonie. 

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Tworzenie klastra usługi Service Fabric
Aby wdrożyć aplikację na platformie Azure, potrzebujesz klastra usługi Service Fabric używanego do uruchamiania aplikacji. Klastry testowe oferują prosty sposób szybkiego tworzenia klastra usługi Service Fabric. Klastry testowe to bezpłatne, ograniczone czasowo klastry usługi Service Fabric hostowane na platformie Azure i obsługiwane przez zespół usługi Service Fabric. Klastry testowe pozwalają wdrażać aplikacje i uzyskiwać informacje o platformie. Klaster używa jednego certyfikatu z podpisem własnym na potrzeby zabezpieczeń między węzłami oraz między klientem a węzłem.

Zaloguj się i dołącz do [klastra systemu Linux](http://aka.ms/tryservicefabric). Pobierz certyfikat PFX na komputer, klikając link **PFX**. Kliknij link **ReadMe**, aby znaleźć hasło certyfikatu i instrukcje dotyczące konfigurowania różnych środowisk do użycia certyfikatu. Pozostaw otwarte strony **Powitalna** i **ReadMe**, ponieważ będziesz korzystać z niektórych instrukcji w poniższej procedurze. 

> [!Note]
> Liczba klastrów testowych dostępnych na godzinę jest ograniczona. Jeśli wystąpi błąd podczas próby tworzenia konta umożliwiającego korzystanie z klastra testowego, możesz poczekać, a następnie spróbować ponownie. Możesz też wykonać kroki opisane w artykule [Create a Service Fabric cluster on Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) (Tworzenie klastra usługi Service Fabric na platformie Azure), aby utworzyć klaster usługi w swojej subskrypcji. 
> 
>Jeśli tworzysz własny klaster, pamiętaj, że usługa internetowa frontonu jest skonfigurowana do nasłuchiwania ruchu przychodzącego na porcie 80. Upewnij się, że port w klastrze został otwarty. (Jeśli używasz klastra testowego, ten port jest otwarty).
>

## <a name="configure-your-environment"></a>Konfigurowanie środowiska
Usługa Service Fabric udostępnia kilka narzędzi, których możesz używać do zarządzania klastrem i jego aplikacjami:

- Service Fabric Explorer — narzędzie oparte na przeglądarce.
- Interfejs wiersza polecenia (CLI) usługi Service Fabric, który korzysta z interfejsu wiersza polecenia platformy Azure 2.0.
- Polecenia programu PowerShell. 

W tym przewodniku Szybki start używasz usługi Service Fabric w usłudze Cloud Shell i narzędzia Service Fabric Explorer. Poniższe sekcje pokazują, jak zainstalować certyfikat wymagany w celu nawiązania połączenia z bezpiecznym klastrem za pomocą tych narzędzi.

### <a name="configure-certificate-for-the-service-fabric-cli"></a>Konfigurowanie certyfikatu dla interfejsu wiersza polecenia usługi Service Fabric
Aby użyć interfejsu wiersza polecenia w usłudze Cloud Shell, należy przekazać plik PFX certyfikatu do usługi Cloud Shell, a następnie użyć go do utworzenia pliku PEM.

1. Aby przekazać certyfikat do Twojego bieżącego katalogu roboczego w usłudze Cloud Shell, przeciągnij plik PFX certyfikatu z folderu na komputerze, do którego został pobrany, a następnie upuść go w oknie usługi Cloud Shell.  

2. Aby przekonwertować plik PFX na plik PEM, użyj następującego polecenia. (W przypadku klastrów testowych możesz skopiować polecenie specyficzne dla Twojego pliku PFX i hasło z instrukcji na stronie **ReadMe**).

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

### <a name="configure-certificate-for-service-fabric-explorer"></a>Konfigurowanie certyfikatu dla narzędzia Service Fabric Explorer
Aby użyć narzędzia Service Fabric Explorer, musisz zaimportować plik PFX certyfikatu pobrany z witryny internetowej klastra testowego do swojego magazynu certyfikatów (w systemie Windows lub Mac) lub do samej przeglądarki (w systemie Ubuntu). Będzie potrzebne hasło klucza prywatnego PFX, które możesz pobrać ze strony **ReadMe**.

Aby zaimportować certyfikat w swoim systemie, użyj dowolnej, preferowanej przez siebie metody. Na przykład:

- W systemie Windows: kliknij dwukrotnie plik PFX i postępuj zgodnie z monitami, aby zainstalować certyfikat w magazynie osobistym `Certificates - Current User\Personal\Certificates`. Alternatywnie możesz użyć polecenia programu PowerShell podanego w instrukcjach na stronie **ReadMe**.
- Na komputerach Mac: kliknij dwukrotnie plik PFX, a następnie postępuj zgodnie z monitami, aby zainstalować certyfikat w pęku kluczy.
- W systemie Ubuntu: domyślną przeglądarką w systemie Ubuntu 16.04 jest Mozilla Firefox. Aby zaimportować certyfikat w przeglądarce Firefox, kliknij przycisk menu w prawym górnym rogu przeglądarki, a następnie kliknij pozycję **Opcje**. Na stronie **Preferencje** użyj pola wyszukiwania, aby wyszukać ciąg „certyfikaty”. Kliknij przycisk **Wyświetl certyfikaty**, wybierz kartę **Twoje certyfikaty**, kliknij pozycję **Importuj** i postępuj zgodnie z monitami, aby zaimportować certyfikat.
 
   ![Instalowanie certyfikatu w programie Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png) 

## <a name="deploy-the-service-fabric-application"></a>Wdrażanie aplikacji usługi Service Fabric 
1. W usłudze Cloud Shell połącz się z klastrem usługi Service Fabric na platformie Azure przy użyciu interfejsu wiersza polecenia. Punkt końcowy to punkt końcowy zarządzania klastrem. Plik PEM został utworzony w poprzedniej sekcji. (W przypadku klastrów testowych możesz skopiować polecenie specyficzne dla Twojego pliku PEM i punkt końcowy zarządzania z instrukcji na stronie **ReadMe**).

    ```bash
    sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
    ```

2. Użyj skryptu instalacji, aby skopiować definicję aplikacji do głosowania do klastra, zarejestrować typ aplikacji i utworzyć wystąpienie aplikacji.

    ```bash
    ./install.sh
    ```

2. Otwórz przeglądarkę internetową i przejdź do punktu końcowego narzędzia Service Fabric Explorer dla Twojego klastra. Punkt końcowy ma następujący format: https://\<adres-URL-mojego-klastra-Service-Fabric>: 19080/Explorer, na przykład `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. </br>(W przypadku klastrów testowych możesz znaleźć punkt końcowy narzędzia Service Fabric Explorer dla swojego klastra na stronie **powitalnej**). 

3. Rozwiń węzeł **Aplikacje**, aby sprawdzić, czy istnieje teraz wpis dla typu aplikacji do głosowania i utworzonego wystąpienia.

    ![Service Fabric Explorer][sfx]

3. Aby nawiązać połączenie z uruchomionym kontenerem, otwórz przeglądarkę internetową i przejdź do adresu URL swojego klastra, na przykład `http://linh1x87d1d.westus.cloudapp.azure.com:80`. W przeglądarce powinna zostać wyświetlona aplikacja do głosowania.

    ![Strona internetowa aplikacji do głosowania][quickstartpic]


> [!NOTE]
> Aplikacje usługi Service Fabric możesz także wdrożyć przy użyciu narzędzia Docker Compose. Na przykład następujące polecenie może służyć do wdrażania i instalowania aplikacji w klastrze przy użyciu narzędzia Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Przenoszenie kontenera do trybu failover w klastrze
Usługa Service Fabric zapewnia, że Twoje wystąpienia kontenera zostaną automatycznie przeniesione do innych węzłów w klastrze, jeśli wystąpi awaria. Można również ręcznie opróżnić węzeł na potrzeby kontenerów, a następnie bezproblemowo przejść do innych węzłów w klastrze. Usługa Service Fabric udostępnia kilka metod skalowania usług. W poniższych krokach będziesz używać narzędzia Service Fabric Explorer.

Aby przenieść kontener frontonu do trybu failover, wykonaj następujące czynności:

1. Otwórz narzędzie Service Fabric Explorer w klastrze, na przykład `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Kliknij węzeł **fabric:/Voting/azurevotefront** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID). W widoku drzewa zwróć uwagę na nazwę węzła przedstawiającą węzły, w których obecnie uruchomiono kontener, na przykład `_nodetype_4`.
3. Rozwiń węzeł **Węzły** w widoku drzewa. Kliknij wielokropek (...) obok węzła działającego w kontenerze.
4. Wybierz pozycję **Uruchom ponownie**, aby ponownie uruchomić ten węzeł, i potwierdź akcję ponownego uruchomienia. Ponowne uruchomienie powoduje przeniesienie kontenera do trybu failover w innym węźle klastra.

    ![Widok węzła w narzędziu Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalowanie aplikacji i usług w klastrze
Usługi Service Fabric można łatwo skalować w klastrze w celu uwzględnienia obciążenia w usługach. Skalowanie usługi odbywa się przez zmienianie liczby wystąpień uruchomionych w klastrze.

Aby skalować usługę internetową frontonu, wykonaj następujące czynności:

1. Otwórz narzędzie Service Fabric Explorer w klastrze, na przykład `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Kliknij wielokropek (trzy kropki) obok węzła **fabric:/Voting/azurevotefront** w widoku drzewa i wybierz pozycję **Skaluj usługę**.

    ![Rozpoczynanie skalowania usługi w narzędziu Service Fabric Explorer][containersquickstartscale]

  Teraz możesz skalować liczbę wystąpień usługi internetowej frontonu.

3. Zmień liczbę na **2** i kliknij pozycję **Skaluj usługę**.
4. Kliknij węzeł **fabric:/Voting/azurevotefront** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

    ![Zakończenie skalowania usługi w narzędziu Service Fabric Explorer][containersquickstartscaledone]

    Teraz możesz zobaczyć, że usługa ma dwa wystąpienia. W widoku drzewa możesz zobaczyć, w których węzłach uruchomiono wystąpienia.

Za pomocą tego prostego zadania zarządzania zostały podwojone zasoby dostępne dla usługi frontonu na potrzeby przetwarzania obciążenia użytkownika. Pamiętaj, że nie musisz mieć wielu wystąpień usługi, aby działała ona niezawodnie. W przypadku awarii usługa Service Fabric gwarantuje uruchomienie nowego wystąpienie usługi w klastrze.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
1. Użyj skryptu odinstalowywania (uninstall.sh) udostępnionego w szablonie, aby usunąć wystąpienie aplikacji z klastra i wyrejestrować typ aplikacji. Ten skrypt wymaga pewnego czasu, aby wyczyścić wystąpienie, dlatego nie należy uruchamiać skryptu instalacji natychmiast po tym skrypcie. Narzędzia Service Fabric Explorer możesz użyć do określenia typu wyrejestrowanej aplikacji i tego, kiedy wystąpienie zostało usunięte. 

    ```bash
    ./uninstall.sh
    ```

2. Po zakończeniu pracy z klastrem, możesz usunąć certyfikat ze swojego magazynu certyfikatów. Na przykład:
   - W systemie Windows: użyj [przystawki MMC certyfikatów](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Pamiętaj, aby wybrać pozycję **Moje konto użytkownika** podczas dodawania przystawki. Przejdź do pozycji `Certificates - Current User\Personal\Certificates` i usuń certyfikat.
   - Na komputerze Mac: użyj aplikacji pęku kluczy.
   - W systemie Ubuntu: wykonaj czynności, które zostały użyte do wyświetlenia certyfikatów, i usuń certyfikat.

3. Jeśli nie chcesz dalej używać usługi Cloud Shell, możesz usunąć skojarzone z nią konto magazynu, aby uniknąć naliczania opłat. Zamknij sesję usługi Cloud Shell. W witrynie Azure Portal kliknij konto magazynu skojarzone z usługą Cloud Shell, a następnie kliknij przycisk **Usuń** u góry strony i odpowiadaj na monity.

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start aplikacja kontenera systemu Linux została wdrożona w klastrze usługi Service Fabric na platformie Azure i przeniesiona do trybu failover oraz przeskalowano ją w klastrze. Aby dowiedzieć się więcej o pracy z kontenerami systemu Linux w usłudze Service Fabric, przejdź do samouczka dla aplikacji kontenera systemu Linux.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji kontenera systemu Linux](./service-fabric-tutorial-create-container-images.md)


[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
