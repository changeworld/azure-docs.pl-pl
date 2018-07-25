---
title: Wdrażanie do usługi Azure Kubernetes Service (AKS) przy użyciu narzędzia Jenkins i wzorzec wdrożenie niebieski i zielony
description: Dowiedz się, jak wdrożyć do usługi Azure Kubernetes Service (AKS) przy użyciu narzędzia Jenkins i wzorzec wdrożenie niebieski i zielony
services: app-service\web
documentationcenter: ''
author: tomarcher
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 07/23/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 472622f78303593b7a4d5d5136aa47f34a1f44b1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228098"
---
# <a name="deploy-to-azure-kubernetes-service-aks-using-jenkins-and-bluegreen-deployment-pattern"></a>Wdrażanie do usługi Azure Kubernetes Service (AKS) przy użyciu narzędzia Jenkins i wzorzec wdrożenie niebieski i zielony

Usługa Azure Kubernetes Service (AKS) zarządza hostowanym środowiskiem Kubernetes, dzięki czemu można szybko i łatwo wdrażać konteneryzowane aplikacje i zarządzać nimi bez specjalistycznej wiedzy z zakresu aranżacji kontenerów. AKS również zwalnia z konieczności i konserwacją dzięki inicjowania obsługi administracyjnej, aktualizowaniu i skalowaniu zasobów na żądanie bez przełączania aplikacji w trybie offline. Aby uzyskać więcej informacji o usłudze AKS, zobacz Aby [dokumentację dotyczącą usługi AKS](/azure/aks/).

Wdrażanie niebieskie/zielone jest wzorzec infrastruktury DevOps ciągłe dostarczanie (CD), który polega na utrzymywaniu działającej istniejącej wersji (niebieski) na żywo podczas wdrażania nową (zielony). Zazwyczaj ten wzorzec wykorzystuje równoważenia w celu kierowania coraz większego ruchu do zielonego wdrożenia. Jeśli monitorowanie wykryte zdarzenie, ruch może zostać skierowany do niebieskiego wdrożenia, które są nadal działa. Aby uzyskać więcej informacji na temat ciągłego dostarczania, zobacz w artykule [co to jest ciągłe dostarczanie](/azure/devops/what-is-continuous-delivery).

W tym samouczku nauczysz się wykonywać następujące zadania, jak wdrażanie w usłudze AKS za pomocą systemu Jenkins i wzorzec wdrożenie niebieski i zielony:

> [!div class="checklist"]
> * Dowiedz się więcej o wzorcu wdrożenie niebieski i zielony
> * Utwórz zarządzany klaster Kubernetes.
> * Uruchom przykładowy skrypt, aby skonfigurować klaster usługi Kubernetes
> * Ręczne konfigurowanie klastra Kubernetes
> * Tworzenie i uruchamianie zadania usługi Jenkins

## <a name="prerequisites"></a>Wymagania wstępne
- [Konto usługi GitHub](https://github.com) :, musisz mieć konto usługi GitHub w celu sklonowania przykładowego repozytorium.
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) : The Azure CLI 2.0 służy do tworzenia klastra Kubernetes.
- [Chocolatey](https://chocolatey.org) — Menedżer pakietów używane do zainstalowania interfejsu kubectl.
- [narzędzia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) : interfejs wiersza polecenia do uruchamiania poleceń względem klastrów Kubernetes.
- [jq](https://stedolan.github.io/jq/download/) : uproszczone wiersza polecenia procesora JSON.

## <a name="clone-the-sample-app-from-github"></a>Sklonuj przykładową aplikację z usługi GitHub

Przykładową aplikację, która przedstawia sposób wdrażania usługi AKS przy użyciu narzędzia Jenkins i wzorzec niebieskie/zielone znajduje się na repozytorium firmy Microsoft w witrynie GitHub. W tej sekcji możesz tworzyć rozwidlenia tego repozytorium w Twojej witrynie GitHub i klonowanie aplikacji do systemu lokalnego.

1. Przejdź do repozytorium GitHub dla [todo aplikacji java-środowiska platformę azure](https://github.com/microsoft/todo-app-java-on-azure.git) przykładową aplikację.

    ![Przykładowa aplikacja w repozytorium Microsoft GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Utwórz rozwidlenie repozytorium, wybierając **rozwidlenia** w prawym górnym rogu strony i postępuj zgodnie z instrukcjami, aby utworzyć rozwidlenie repozytorium na swoim koncie usługi GitHub.

    ![Rozwidlenia repozytorium przykładowej aplikacji do konta usługi GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Po Rozgałęzienie, zobaczysz, że nazwa konta zmienia się na nazwę swojego konta i notatki wskazuje, z której została forked repozytorium (Microsoft).

    ![Przykładowa aplikacja po trwa forked do innego konta usługi GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Wybieranie **klonowania lub pobierania**.

    ![GitHub pozwala na szybkie Klonuj lub Pobierz repozytorium.](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. W **klonowania przy użyciu protokołu HTTPS** okna, wybierz ikonę kopiowania.

    ![Skopiuj adres URL klonowania do Schowka.](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Otwórz terminal lub powłokę Bash okna.

1. Zmień katalogi na lokalizację, w którym chcesz przechowywać kopię lokalną (klonuje) repozytorium.

1. Za pomocą `git clone` poleceń, adres URL został wcześniej skopiowany klonowania.

    ![Wpisz "git clone" i adres URL klonowania, aby sklonować repozytorium.](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Wybierz &lt;Enter > klucz, aby rozpocząć proces klonowania.

    ![Polecenie "git clone" tworzy osobistą kopią repozytorium, w którym można przetestować](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Przejdź do nowo utworzonego katalogu, który zawiera klonowanie źródła aplikacji.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Tworzenie i konfigurowanie zarządzanych klastrów Kubernetes

W tej sekcji należy wykonać następujące czynności:

- Interfejs wiersza polecenia platformy Azure w wersji 2.0 umożliwia tworzenie zarządzanych klastrów Kubernetes.
- Dowiedz się, jak skonfigurować klaster za pomocą skryptu Instalatora lub ręcznie.
- Tworzenie rejestru kontenerów platformy Azure.

> [!NOTE]   
> AKS jest obecnie w wersji zapoznawczej. Aby uzyskać informacje na temat włączania dla subskrypcji platformy Azure w wersji zapoznawczej. zobacz [Szybki Start: wdrażanie klastra usługi Azure Kubernetes Service (AKS) ](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription) Aby uzyskać więcej informacji.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0 do tworzenia zarządzanych klastrów Kubernetes
Aby można było utworzyć zarządzanego klastra Kubernetes za pomocą [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), upewnij się, że używasz wiersza polecenia platformy Azure w wersji 2.0.25 lub nowszej.

1. Zaloguj się do konta platformy Azure. Po wprowadzeniu następujących `az login` polecenia i instrukcje zostały przedstawione, które wyjaśniają, jak zakończyć logowanie. 
    
    ```bash
    az login
    ```

1. Po uruchomieniu `az login` polecenia w poprzednim kroku, listę wszystkich subskrypcji platformy Azure przedstawia (wraz z ich identyfikatory subskrypcji). W tym kroku ustawisz wartość domyślna subskrypcja platformy Azure. Zastąp &lt;your-subscription-id > Symbol zastępczy, podając identyfikator odpowiedniej subskrypcji platformy Azure. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Utwórz grupę zasobów. Zastąp &lt;your-— Nazwa grupy zasobów > Nazwa nowej grupy zasobów, i Zastąp symbol zastępczy &lt;usługi lokalizacji > symbolu zastępczego lokalizacją. Polecenie `az account list-locations` Wyświetla wszystkich lokalizacji platformy Azure. W trakcie okresu zapoznawczego usługi AKS nie wszystkie lokalizacje są dostępne. Jeśli wprowadzasz lokalizacji, która nie jest prawidłowy w tym momencie, komunikat o błędzie wyświetli listę dostępnych lokalizacji.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Tworzenie klastra Kubernetes. Zastąp &lt;your-resource grupy name > nazwą grupy zasobów utworzonych w poprzednim kroku, a następnie zastąp &lt;możesz — — nazwa klastra kubernetes — > nazwą nowego klastra. (Ten proces może potrwać kilka minut).

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Konfigurowanie klastra usługi Kubernetes

Konfigurowanie wdrażania niebieskie/zielone, w usłudze AKS może odbywać się przy użyciu skryptu Instalatora dostarczonego w przykładzie klonowany wcześniej lub ręcznie. W tej sekcji dowiesz się jak wykonać obie czynności.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Konfigurowanie klastra Kubernetes za pomocą przykładowego skryptu Instalatora
1. Edytuj **deploy/aks/setup/setup.sh** pliku, zastępując następujące symbole zastępcze odpowiednimi wartościami dla danego środowiska: 

    - **&lt;Twój — — Nazwa grupy zasobów >**
    - **&lt;usługi kubernetes klastra nazwa->**
    - **&lt;usługi lokalizacji >**
    - **&lt;Your-name sufiks dns >**

    ![Skrypt setup.sh zawiera kilka symboli zastępczych, które mogą być modyfikowane w danym środowisku.](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Uruchom skrypt instalacji.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Ręczne konfigurowanie klastra Kubernetes 
1. Pobierz konfigurację Kubernetes do folderu profilu.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Zmień katalog na **wdrażanie/aks/Konfiguracja** katalogu. 

1. Uruchom następujące polecenie **kubectl** polecenia, aby skonfigurować usługi dla publicznego punktu końcowego i dwa punkty końcowe testu.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Aktualizacja nazwy DNS dla publicznego i test punktów końcowych. Podczas tworzenia klastra Kubernetes [dodatkowych zasobów grupy](https://github.com/Azure/AKS/issues/3) jest tworzona przy użyciu wzorzec nazewnictwa, z **MC_&lt;your-— Nazwa grupy zasobów > _&lt; usługi kubernetes klastra nazwa->_&lt;usługi lokalizacji >**.

    Znajdź publiczny adres ip w grupie zasobów

    ![Publiczny adres IP w grupie zasobów](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Dla każdej usługi należy znaleźć zewnętrzny adres IP, uruchamiając następujące polecenie:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Aktualizacja nazwy DNS, aby uzyskać adres IP za pomocą następującego polecenia:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Powtórz wywołanie `todoapp-test-blue` i `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Nazwa DNS musi być unikatowa w ramach subskrypcji. `<your-dns-name-suffix>` może służyć do zapewnienia unikatowości.

### <a name="create-azure-container-registry"></a>Tworzenie rejestru kontenerów platformy Azure

1. Uruchom `az acr create` polecenie, aby utworzyć rejestr Azure Container Registry. Po utworzeniu usługi Azure Container Registry, użyj `login server` jako adres URL rejestru platformy Docker w następnej sekcji.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Uruchom `az acr credential` polecenie, aby wyświetlić swoje poświadczenia usługi Azure Container Registry. Należy zauważyć użytkownika rejestru platformy Docker i hasło, ponieważ są one używane w następnej sekcji.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Przygotowanie serwera Jenkins

W tej sekcji zobaczysz, jak przygotować serwer Jenkins kompilację, która jest dobrym rozwiązaniem do testowania. Jednak, tak jak wyjaśniono w artykule Jenkins na [skutki dla bezpieczeństwa opracowywanie rozwiązań na wzorzec](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master), zaleca się używać [agenta maszyny Wirtualnej platformy Azure](https://plugins.jenkins.io/azure-vm-agents) lub [agenta usługi Azure Container](https://plugins.jenkins.io/azure-container-agents) do Uruchom agenta na platformie Azure do uruchomienia kompilacji. 

1. Wdrażanie [wzorca usługi Jenkins na platformie Azure](https://aka.ms/jenkins-on-azure).

1. Połączenie z serwerem za pośrednictwem protokołu SSH i zainstalować narzędzia do kompilacji na serwerze, na którym uruchamiasz kompilacji.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Zainstalować platformę Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Upewnij się, użytkownik `jenkins` ma uprawnienia do uruchamiania `docker` poleceń.

1. [Instalowanie narzędzia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Pobierz jq](https://stedolan.github.io/jq/download/).

1. Zainstaluj jq za pomocą następującego polecenia:

   ```bash
   sudo apt-get install jq
   ```
   
1. Instalowanie wtyczki w usłudze Jenkins, wykonując następujące czynności w ramach pulpitu nawigacyjnego narzędzia Jenkins:

    1. Wybierz **Zarządzanie Jenkins > Zarządzaj wtyczkami > dostępne**.
    1. Wyszukiwanie i instalowanie wtyczka usługi kontenera platformy Azure.

1. Musisz dodać poświadczenia, które będą używane do zarządzania zasobami na platformie Azure. Jeśli nie masz jeszcze wtyczki, należy zainstalować **Azure Credential** wtyczki.

1. Dodaj swoje poświadczenia jednostki usługi platformy Azure jako rodzaj typu/ **Microsoft Azure Service Principal**.

1. Dodaj usługę Azure Docker Registry nazwy użytkownika i hasła (zgodnie z uzyskanymi w sekcji **Tworzenie usługi Azure Container Registry**) jako rodzaj typu/ **nazwy użytkownika z hasłem**.

## <a name="edit-the-jenkinsfile"></a>Edytowanie pliku Jenkins

1. W własnego repozytorium, przejdź do `/deploy/aks/` , a następnie otwórz `Jenkinsfile`

2. Zaktualizuj plik w następujący sposób:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    I zaktualizuj identyfikator poświadczeń rejestru Azure container Registry:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Utwórz zadanie
1. Dodaj nowe zadanie w typie **potoku**.

1. Wybierz **potoku > definicji > potoku skrypt z SCM**.

1. Wprowadź adres url repozytorium SCM za pomocą usługi &lt;your repozytorium forked >

1. Wprowadź ścieżkę skryptu jako `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Uruchamianie zadania

1. Sprawdź, czy projekt można uruchomić pomyślnie w środowisku lokalnym. [Uruchamianie projektu na komputerze lokalnym](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it)

1. Uruchamianie zadania usługi Jenkins. Podczas uruchamiania zadania narzędzia Jenkins po raz pierwszy, Jenkins wdroży aplikację zadań do wykonania do niebieskiego środowisko, w którym jest domyślnym środowiskiem nieaktywne. 

1. Aby sprawdzić, czy uruchomiono zadanie, przejdź do adresów URL:
    - Publiczny punkt końcowy: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Punkt końcowy niebieski- `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Zielony punktu końcowego — `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Publicznie i punktów końcowych niebieski testu mają tę samą aktualizację, a zielony punkt końcowy jest wyświetlany domyślny tomcat. 

Jeśli kompilacja zostanie uruchomione więcej niż jeden raz, przewijać wdrożeń niebieski i zielony. Innymi słowy Jeśli bieżące środowisko jest niebieski, zadanie będzie wdrażania i testowania w środowisku zielony i następnie zaktualizuj publiczny punkt końcowy aplikacji na potrzeby kierowania ruchu do zielonego środowiska, jeśli wszystko jest w porządku z testowaniem.

## <a name="additional-information"></a>Dodatkowe informacje

Więcej informacji dotyczących wdrażania bez jakichkolwiek przestojów, zapoznaj się z tą [szablon szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń zasoby platformy Azure, który utworzono w ramach tego samouczka.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli występują jakiekolwiek z wtyczek Jenkins, Prześlij zgłoszenie do [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wdrażania do usługi Azure Kubernetes Service (AKS) przy użyciu narzędzia Jenkins i wzorzec wdrożenie niebieski i zielony. Aby dowiedzieć się więcej na temat dostawcy programu Jenkins na platformie Azure, zobacz narzędzia Jenkins w witrynie platformy Azure.

> [!div class="nextstepaction"]
> [Jenkins na platformie Azure](/azure/jenkins/)