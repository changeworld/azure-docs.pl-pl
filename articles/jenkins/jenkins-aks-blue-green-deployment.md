---
title: Wdrażanie do usługi Azure Kubernetes Service (AKS) przy użyciu narzędzia Jenkins i wzorzec wdrożenie niebieski i zielony
description: Dowiedz się, jak wdrożyć do usługi Azure Kubernetes Service (AKS) przy użyciu narzędzia Jenkins i wzorzec wdrożenie niebieski i zielony.
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
ms.openlocfilehash: 384681ae0ba212b485022ac81743528f96075ec8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716463"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Wdrażanie do usługi Azure Kubernetes Service (AKS) przy użyciu narzędzia Jenkins i wzorzec wdrożenie niebieski i zielony

Usługa Azure Kubernetes Service (AKS) zarządza hostowanym środowiskiem Kubernetes, pozwalając szybko i łatwo wdrażać konteneryzowane aplikacje i zarządzać. Nie wymaga doświadczenia z w aranżacji kontenerów. AKS oprócz tego zwalnia trwające operacje i konserwacja inicjowania obsługi, uaktualnianie i skalowanie zasobów na żądanie. Nie musisz przełączyć w tryb offline aplikacji. Aby uzyskać więcej informacji o usłudze AKS, zobacz [dokumentację dotyczącą usługi AKS](/azure/aks/).

Wdrażanie niebieskie/zielone jest wzorzec Azure DevOps ciągłego dostarczania, który polega na utrzymywaniu działającej istniejącej wersji (niebieski) na żywo podczas wdrażania nową (zielony). Zazwyczaj ten wzorzec wykorzystuje równoważenia w celu kierowania coraz większego ruchu do zielonego wdrożenia. Jeśli monitorowanie wykryte zdarzenie, ruch może zostać skierowany do niebieskiego wdrożenia, który jest nadal uruchomiona. Aby uzyskać więcej informacji na temat ciągłe dostarczanie zobacz [co to jest ciągłe dostarczanie](/azure/devops/what-is-continuous-delivery).

W tym samouczku nauczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Dowiedz się więcej o wzorcu wdrożenie niebieski i zielony
> * Tworzenie zarządzanych klastrów Kubernetes
> * Uruchom przykładowy skrypt, aby skonfigurować klaster usługi Kubernetes
> * Ręczne konfigurowanie klastra Kubernetes
> * Tworzenie i uruchamianie zadania usługi Jenkins

## <a name="prerequisites"></a>Wymagania wstępne
- [Konto usługi GitHub](https://github.com) :, musisz mieć konto usługi GitHub w celu sklonowania przykładowego repozytorium.
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) : interfejs wiersza polecenia platformy Azure w wersji 2.0 umożliwia tworzenie klastra Kubernetes.
- [Chocolatey](https://chocolatey.org): Menedżer pakietów, możesz użyć do zainstalowania interfejsu kubectl.
- [narzędzia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): interfejs wiersza polecenia, użyj do uruchamiania poleceń względem klastrów Kubernetes.
- [jq](https://stedolan.github.io/jq/download/): procesor JSON lekki, wiersza polecenia.

## <a name="clone-the-sample-app-from-github"></a>Sklonuj przykładową aplikację z usługi GitHub

Repozytorium firmy Microsoft w serwisie GitHub można znaleźć przykładową aplikację, która przedstawia sposób wdrażania w usłudze AKS za pomocą systemu Jenkins i wzorzec niebieski i zielony. W tej sekcji możesz tworzyć rozwidlenia tego repozytorium w Twojej witrynie GitHub i klonowanie aplikacji do systemu lokalnego.

1. Przejdź do repozytorium GitHub dla [todo aplikacji java-środowiska platformę azure](https://github.com/microsoft/todo-app-java-on-azure.git) przykładową aplikację.

    ![Zrzut ekranu przedstawiający przykładową aplikację w repozytorium Microsoft GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Utwórz rozwidlenie repozytorium, wybierając **rozwidlenia** w prawym górnym rogu strony i postępuj zgodnie z instrukcjami, aby utworzyć rozwidlenie repozytorium na swoim koncie usługi GitHub.

    ![Zrzut ekranu z usługi GitHub możliwość rozwidlenia repozytorium](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Po Rozgałęzienie, zobaczysz, że nazwa konta zmienia się na nazwę swojego konta i notatki wskazuje, z której została forked repozytorium (Microsoft).

    ![Zrzut ekranu z usługi GitHub, nazwę konta i notatki](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Wybierz **klonowania lub pobierania**.

    ![Zrzut ekranu z usługi GitHub możliwość Klonuj lub Pobierz repozytorium](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. W **klonowania przy użyciu protokołu HTTPS** wybierz **kopiowania** ikony.

    ![Zrzut ekranu z usługi GitHub opcję, aby skopiować adres URL klonowania do Schowka](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Otwórz terminal lub powłokę Git Bash okna.

1. Zmień katalogi na lokalizację, w którym chcesz przechowywać kopię lokalną (klonuje) repozytorium.

1. Za pomocą `git clone` poleceń, adres URL został wcześniej skopiowany klonowania.

    ![Zrzut ekranu z powłoki Git Bash polecenia klonowania usługi git](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Naciśnij klawisz Enter, aby rozpocząć proces klonowania.

    ![Zrzut ekranu z powłoki Git Bash wyniki polecenia klonowania usługi git](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Przejdź do nowo utworzonego katalogu, który zawiera klonowanie źródła aplikacji.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Tworzenie i konfigurowanie zarządzanych klastrów Kubernetes

W tej sekcji należy wykonać następujące czynności:

- Interfejs wiersza polecenia platformy Azure w wersji 2.0 umożliwia tworzenie zarządzanych klastrów Kubernetes.
- Dowiedz się, jak skonfigurować klaster, przy użyciu skryptu Instalatora lub ręcznie.
- Utwórz wystąpienie usługi Azure Container Registry.

> [!NOTE]   
> AKS jest obecnie w wersji zapoznawczej. Aby uzyskać informacje na temat włączania dla subskrypcji platformy Azure w wersji zapoznawczej, zobacz [Szybki Start: wdrażanie klastra usługi Azure Kubernetes Service (AKS)](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription).

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0 do tworzenia zarządzanych klastrów Kubernetes
Aby można było utworzyć zarządzanego klastra Kubernetes za pomocą [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), upewnij się, że używasz interfejsu wiersza polecenia platformy Azure w wersji 2.0.25 lub nowszej.

1. Zaloguj się do konta platformy Azure. Po wprowadzeniu następujące polecenie, otrzymasz instrukcje, które wyjaśniają, jak zakończyć logowanie. 
    
    ```bash
    az login
    ```

1. Po uruchomieniu `az login` polecenia w poprzednim kroku, zostanie wyświetlona lista wszystkich subskrypcji platformy Azure (wraz z ich identyfikatory subskrypcji). W tym kroku ustawisz wartość domyślna subskrypcja platformy Azure. Zastąp &lt;your-subscription-id > Symbol zastępczy, podając identyfikator odpowiedniej subskrypcji platformy Azure. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Utwórz grupę zasobów. Zastąp &lt;your-— Nazwa grupy zasobów > Nazwa nowej grupy zasobów, i Zastąp symbol zastępczy &lt;usługi lokalizacji > symbolu zastępczego lokalizacją. Polecenie `az account list-locations` Wyświetla wszystkich lokalizacji platformy Azure. W trakcie okresu zapoznawczego usługi AKS nie wszystkie lokalizacje są dostępne. Jeśli wprowadzasz lokalizacji, która nie jest prawidłowy w tym momencie, komunikat o błędzie zawiera listę dostępnych lokalizacji.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Tworzenie klastra Kubernetes. Zastąp &lt;your-resource grupy name > nazwą grupy zasobów utworzonych w poprzednim kroku, a następnie zastąp &lt;your-— nazwa klastra kubernetes — > nazwą nowego klastra. (Ten proces może potrwać kilka minut).

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Konfigurowanie klastra usługi Kubernetes

Możesz skonfigurować wdrażanie niebieskie/zielone, w usłudze AKS ręcznie lub z konfiguracją skryptu dostarczonego w przykładzie klonowany wcześniej. W tej sekcji dowiesz się jak wykonać obie czynności.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Konfigurowanie klastra Kubernetes za pomocą przykładowego skryptu Instalatora
1. Edytuj **deploy/aks/setup/setup.sh** pliku, zastępując następujące symbole zastępcze odpowiednimi wartościami dla danego środowiska: 

    - **&lt;Twój — — Nazwa grupy zasobów >**
    - **&lt;usługi kubernetes klastra nazwa->**
    - **&lt;usługi lokalizacji >**
    - **&lt;Your-name sufiks dns >**

    ![Zrzut ekranu setup.sh skryptów w powłoce bash przy użyciu kilku symbole zastępcze wyróżniony](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

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

1. Aktualizacja nazwy DNS dla publicznego i test punktów końcowych. Podczas tworzenia klastra Kubernetes, możesz również utworzyć [dodatkowych zasobów grupy](https://github.com/Azure/AKS/issues/3), przy użyciu wzorca nazewnictwa **MC_&lt;your-— Nazwa grupy zasobów >_ &lt; usługi kubernetes klastra nazwa->_&lt;usługi lokalizacji >**.

    Znajdź publicznych adresów IP w grupie zasobów.

    ![Zrzut ekranu przedstawiający publicznych adresów IP w grupie zasobów](./media/jenkins-aks-blue-green-deployment/publicip.png)

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

    Nazwa DNS musi być unikatowa w ramach subskrypcji. Aby zapewnić unikatowość, można użyć `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>Utwórz wystąpienie obiektu Container Registry

1. Uruchom `az acr create` polecenie, aby utworzyć wystąpienie rejestru kontenerów. W następnej sekcji można następnie użyć `login server` jako adres URL rejestru platformy Docker.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Uruchom `az acr credential` polecenie, aby wyświetlić poświadczenia rejestru kontenerów. Należy pamiętać użytkownika rejestru platformy Docker i hasło, ponieważ będą potrzebne w następnej sekcji.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Przygotowanie serwera Jenkins

W tej sekcji zobaczysz, jak przygotować serwer Jenkins kompilację, która jest dobrym rozwiązaniem do testowania. Jednakże należy używać [agenta maszyny Wirtualnej platformy Azure](https://plugins.jenkins.io/azure-vm-agents) lub [agenta usługi Azure Container](https://plugins.jenkins.io/azure-container-agents) nad ułatwieniem wdrażania agenta w systemie Azure do uruchomienia kompilacji. Aby uzyskać więcej informacji, zobacz artykuł Jenkins [skutki dla bezpieczeństwa opracowywanie rozwiązań na wzorzec](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Wdrażanie [wzorca usługi Jenkins na platformie Azure](https://aka.ms/jenkins-on-azure).

1. Połączenie z serwerem za pośrednictwem protokołu SSH i zainstalować narzędzia do kompilacji na serwerze, na którym uruchamiasz kompilacji.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Zainstalować platformę Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Upewnij się, że użytkownik `jenkins` ma uprawnienia do uruchamiania `docker` poleceń.

1. [Instalowanie narzędzia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Pobierz jq](https://stedolan.github.io/jq/download/).

1. Zainstaluj jq za pomocą następującego polecenia:

   ```bash
   sudo apt-get install jq
   ```
   
1. Instalowanie wtyczki w usłudze Jenkins, wykonując następujące czynności w ramach pulpitu nawigacyjnego narzędzia Jenkins:

    1. Wybierz **Zarządzanie Jenkins > Zarządzaj wtyczkami > dostępne**.
    1. Wyszukiwanie i instalowanie wtyczki usługi Azure Container Service.

1. Dodawanie poświadczeń do zarządzania zasobami na platformie Azure. Jeśli nie masz jeszcze dodatku typu plug-in, zainstaluj **Azure Credential** wtyczki.

1. Dodaj swoje poświadczenia jednostki usługi platformy Azure jako typ **Microsoft Azure Service Principal**.

1. Dodawanie użytkownika i hasła użytkownika rejestru platformy Docker platformy Azure (po uzyskaniu w sekcji "Utwórz wystąpienie obiektu Container Registry") jako typ **nazwy użytkownika z hasłem**.

## <a name="edit-the-jenkinsfile"></a>Edytowanie pliku Jenkins

1. W własnego repozytorium, przejdź do `/deploy/aks/`, a następnie otwórz `Jenkinsfile`.

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
    
    Zaktualizuj identyfikator poświadczeń rejestru kontenerów:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Utwórz zadanie
1. Dodaj nowe zadanie w typie **potoku**.

1. Wybierz **potoku** > **definicji** > **potoku skrypt z SCM**.

1. Wprowadź adres URL repozytorium SCM za pomocą usługi &lt;your repozytorium forked >.

1. Wprowadź ścieżkę skryptu jako `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Uruchamianie zadania

1. Sprawdź, czy projekt można uruchomić pomyślnie w środowisku lokalnym. Oto jak: [uruchamianie projektu na komputerze lokalnym](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Uruchamianie zadania usługi Jenkins. Podczas pierwszego uruchomienia zadania, system Jenkins wdraża aplikację zadań do wykonania na niebieski środowisko, w którym jest domyślnym środowiskiem nieaktywne. 

1. Aby sprawdzić, czy uruchomiono zadanie, przejdź do następujących adresów URL:
    - Publiczny punkt końcowy: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Punkt końcowy niebieski- `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Zielony punktu końcowego — `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Publicznie i punktów końcowych niebieski testu mają tę samą aktualizację, a zielony punkt końcowy jest wyświetlany domyślny tomcat. 

Jeśli kompilacja zostanie uruchomione więcej niż jeden raz, przewijać wdrożeń niebieski i zielony. Innymi słowy Jeśli bieżące środowisko jest niebieskie, zadanie służy do wdrażania i testów w środowisku zielony. Następnie Jeśli testy są dobre, zadanie aktualizacji publiczny punkt końcowy aplikacji na potrzeby kierowania ruchu do zielonego środowiska.

## <a name="additional-information"></a>Dodatkowe informacje

Więcej informacji dotyczących wdrażania bez jakichkolwiek przestojów zobacz [szablon szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych w ramach tego samouczka, możesz je usunąć.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wdrażania w usłudze AKS za pomocą systemu Jenkins i wzorzec wdrożenie niebieski i zielony. Aby dowiedzieć się więcej na temat dostawcy programu Jenkins na platformie Azure, zobacz narzędzia Jenkins w witrynie platformy Azure.

> [!div class="nextstepaction"]
> [Jenkins na platformie Azure](/azure/jenkins/)