---
title: Wdrażanie w usłudze Azure Kubernetes Service (AKS) przy użyciu serwera Jenkins i niebieskiego/zielonego wzorca wdrażania
description: Dowiedz się, w jaki sposób przeprowadzić wdrażanie w usłudze Azure Kubernetes Service (AKS) przy użyciu serwera Jenkins i niebieskiego/zielonego wzorca wdrażania.
ms.service: jenkins
keywords: jenkins, azure, devops, kubernetes, k8s, aks, wdrażanie niebieskie zielone, ciągłe dostarczanie, cd
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/11/2018
ms.openlocfilehash: 93f2ac284931ba664e0965e537e515c824e6f7a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60642136"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Wdrażanie w usłudze Azure Kubernetes Service (AKS) przy użyciu serwera Jenkins i niebieskiego/zielonego wzorca wdrażania

Usługa Azure Kubernetes Service (AKS) zarządza hostowanym środowiskiem Kubernetes, dzięki czemu można szybko i łatwo wdrażać konteneryzowane aplikacje i zarządzać nimi. Nie wymaga to specjalistycznej wiedzy z zakresu aranżacji kontenerów. Usługa AKS eliminuje również uciążliwości związane z bieżącą obsługą i konserwacją dzięki aprowizowaniu, uaktualnianiu i skalowaniu zasobów na żądanie. Nie ma potrzeby przełączania aplikacji do trybu offline. Więcej informacji o usłudze AKS można znaleźć w [dokumentacji usługi AKS](/azure/aks/).

Wdrażanie niebieskie/zielone to wzorzec ciągłego dostarczania usługi Azure DevOps, który polega na utrzymywaniu działającej istniejącej wersji (niebieska) podczas wdrażania nowej (zielona). Zazwyczaj ten wzorzec wykorzystuje równoważenie obciążenia, aby skierować zwiększający się ruch do zielonego wdrożenia. Jeśli funkcja monitorowania wykryje zdarzenie, ruch może zostać skierowany do niebieskiego wdrożenia, które nadal działa. Aby uzyskać więcej informacji na temat ciągłego dostarczania, zobacz [Czym jest ciągłe dostarczanie?](/azure/devops/what-is-continuous-delivery).

W tym samouczku wyjaśniono, jak wykonać następujące zadania:

> [!div class="checklist"]
> * Uzyskiwanie informacji o niebieskim/zielonym wzorcu wdrażania
> * Tworzenie zarządzanego klastra Kubernetes
> * Uruchamianie przykładowego skryptu w celu skonfigurowania klastra Kubernetes
> * Ręczne konfigurowanie klastra Kubernetes
> * Tworzenie i uruchamianie zadania serwera Jenkins

## <a name="prerequisites"></a>Wymagania wstępne
- [Konto usługi GitHub](https://github.com): musisz mieć konto usługi GitHub, aby móc sklonować przykładowe repozytorium.
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest): interfejs wiersza polecenia platformy Azure w wersji 2.0 jest używany do tworzenia klastra Kubernetes.
- [Chocolatey](https://chocolatey.org): menedżer pakietów używany do zainstalowania interfejsu kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): interfejs wiersza polecenia używany do uruchamiania poleceń w klastrach Kubernetes.
- [jq](https://stedolan.github.io/jq/download/): uproszczony procesor JSON wiersza polecenia.

## <a name="clone-the-sample-app-from-github"></a>Klonowanie przykładowej aplikacji z usługi GitHub

W repozytorium firmy Microsoft w serwisie GitHub można znaleźć przykładową aplikację, która przedstawia sposób wdrażania w usłudze AKS za pomocą serwera Jenkins i wzorca niebieski/zielony. W tej sekcji utworzysz rozwidlenie tego repozytorium w Twojej witrynie GitHub i sklonujesz aplikacji do systemu lokalnego.

1. Przejdź do repozytorium GitHub aplikacji przykładowej [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Zrzut ekranu przedstawiający przykładową aplikację w repozytorium GitHub firmy Microsoft](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Utwórz rozwidlenie repozytorium, wybierając pozycję **Fork** (Rozwidlenie) w prawym górnym rogu strony, i postępuj zgodnie z instrukcjami, aby utworzyć rozwidlenie repozytorium na swoim koncie usługi GitHub.

    ![Zrzut ekranu przedstawiający opcję rozwidlenia repozytorium GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Po utworzeniu rozwidlenia repozytorium zobaczysz, że nazwa konta zmieni się na nazwę Twojego konta. Zostanie również wyświetlona informacja wskazująca, skąd repozytorium zostało rozwidlone (Microsoft).

    ![Zrzut ekranu przedstawiający nazwę konta GitHub i informację](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).

    ![Zrzut ekranu przedstawiający opcję sklonowania lub pobrania repozytorium w usłudze GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. W oknie **Clone with HTTPS** (Klonowanie przy użyciu protokołu HTTPS) wybierz ikonę **kopiowania**.

    ![Zrzut ekranu przedstawiający opcję skopiowania do schowka adresu URL klonowania w usłudze GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Otwórz okno terminalu lub powłoki Git Bash.

1. Przejdź do katalogu, w którym ma być przechowywana lokalna kopia (klon) repozytorium.

1. Za pomocą polecenia `git clone` sklonuj adres URL, który został wcześniej skopiowany.

    ![Zrzut ekranu powłoki Git Bash z poleceniem git clone](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Naciśnij klawisz Enter, aby rozpocząć proces klonowania.

    ![Zrzut ekranu powłoki Git Bash z wynikami polecenia git clone](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Przejdź do nowo utworzonego katalogu, który zawiera sklonowane źródło aplikacji.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Tworzenie i konfigurowanie zarządzanego klastra Kubernetes

W tej sekcji wykonasz następujące kroki:

- Użyjesz interfejsu wiersza polecenia platformy Azure w wersji 2.0 w celu utworzenia zarządzanego klastra Kubernetes.
- Dowiesz się, jak skonfigurować klaster przy użyciu skryptu konfiguracji lub ręcznie.
- Utworzysz wystąpienie usługi Azure Container Registry.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Używanie interfejsu wiersza polecenia platformy Azure w wersji 2.0 w celu utworzenia zarządzanego klastra Kubernetes
Aby można było utworzyć zarządzany klaster Kubernetes za pomocą [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), należy używać interfejsu wiersza polecenia platformy Azure w wersji 2.0.25 lub nowszej.

1. Zaloguj się do swojego konta platformy Azure. Po wprowadzeniu następującego polecenia otrzymasz instrukcje z wyjaśnieniem, jak zakończyć proces logowania. 
    
    ```bash
    az login
    ```

1. Po uruchomieniu polecenia `az login` z poprzedniego kroku zostanie wyświetlona lista wszystkich Twoich subskrypcji platformy Azure (wraz z ich identyfikatorami subskrypcji). W tym kroku ustawisz domyślną subskrypcję platformy Azure. Zastąp symbol zastępczy &lt;your-subscription-id> identyfikatorem odpowiedniej subskrypcji platformy Azure. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Utwórz grupę zasobów. Zastąp symbol zastępczy &lt;your-resource-group-name> nazwą nowej grupy zasobów, a symbol zastępczy &lt;your-location> swoja lokalizacją. Polecenie `az account list-locations` wyświetla wszystkie lokalizacje platformy Azure. W okresie obowiązywania wersji zapoznawczej usługi AKS nie wszystkie lokalizacje są dostępne. Jeśli wprowadzisz lokalizację, która nie jest prawidłowa w tym momencie, zostanie wyświetlony komunikat o błędzie zawierający listę dostępnych lokalizacji.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Utwórz klaster Kubernetes. Zastąp symbol zastępczy &lt;your-resource-group-name> nazwą grupy zasobów utworzonej w poprzednim kroku, a symbol zastępczy &lt;your-kubernetes-cluster-name> nazwą Twojego nowego klastra. Ten proces może potrwać kilka minut.

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Konfigurowanie klastra usługi Kubernetes

Niebieskie/zielone wdrożenie można skonfigurować w usłudze AKS ręcznie lub za pomocą skryptu konfiguracji dostarczonego w sklonowanym wcześniej przykładzie. W tej sekcji opisano obie te metody.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Konfigurowanie klastra Kubernetes za pomocą przykładowego skryptu konfiguracji
1. Zmodyfikuj plik **deploy/aks/setup/setup.sh**, zastępując następujące symbole zastępcze odpowiednimi wartościami dla danego środowiska: 

   - **&lt;your-resource-group-name>**
   - **&lt;your-kubernetes-cluster-name>**
   - **&lt;your-location>**
   - **&lt;your-dns-name-suffix>**

     ![Zrzut ekranu przedstawiający skrypt setup.sh w powłoce bash z wyróżnionymi kilkoma symbolami zastępczymi](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Uruchom skrypt konfiguracji.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Ręczne konfigurowanie klastra Kubernetes 
1. Pobierz konfigurację Kubernetes do folderu profilu.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Przejdź do katalogu **deploy/aks/setup**. 

1. Uruchom następujące polecenia **kubectl**, aby skonfigurować usługi na potrzeby publicznego punktu końcowego i dwóch testowych punktów końcowych.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Zaktualizuj nazwy DNS dla publicznego i testowych punktów końcowych. Podczas tworzenia klastra Kubernetes tworzona jest również [dodatkowa grupa zasobów](https://github.com/Azure/AKS/issues/3), a jej nazwa jest tworzona za pomocą wzorca nazewnictwa **MC_&lt;nazwa-grupy-zasobów>_&lt;nazwa-klastra-kubernetes>_&lt;lokalizacja>**.

    Znajdź publiczne adresy IP w grupie zasobów.

    ![Zrzut ekranu przedstawiający publiczne adresy IP w grupie zasobów](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Dla każdej usługi znajdź zewnętrzny adres IP, uruchamiając następujące polecenie:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Zaktualizuj nazwę DNS dla odpowiedniego adresu IP za pomocą następującego polecenia:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Powtórz wywołanie dla `todoapp-test-blue` i `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Nazwa DNS musi być unikatowa w ramach subskrypcji. Aby zapewnić unikatowość, można użyć symbolu zastępczego `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>Tworzenie wystąpienia usługi Container Registry

1. Uruchom polecenie `az acr create`, aby utworzyć wystąpienie usługi Container Registry. W następnej sekcji możesz następnie użyć polecenia `login server` jako adresu URL rejestru platformy Docker.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Uruchom polecenie `az acr credential`, aby wyświetlić poświadczenia usługi Container Registry. Zanotuj nazwę użytkownika i hasło rejestru platformy Docker, ponieważ będą one potrzebne w następnej sekcji.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Przygotowywanie serwera Jenkins

W tej sekcji zobaczysz, jak przygotować serwer Jenkins do uruchomienia kompilacji na potrzeby testowania. Należy jednak użyć [agenta maszyny wirtualnej platformy Azure](https://plugins.jenkins.io/azure-vm-agents) lub [agenta usługi Azure Container](https://plugins.jenkins.io/azure-container-agents), aby wdrożyć agenta na platformie Azure w celu uruchomienia kompilacji. Aby uzyskać więcej informacji, zobacz artykuł w witrynie Jenkins dotyczący [zagadnień bezpieczeństwa kompilowania w węźle głównym](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Wdróż [serwer główny Jenkins na platformie Azure](https://aka.ms/jenkins-on-azure).

1. Nawiąż połączenie z serwerem za pośrednictwem protokołu SSH i zainstaluj narzędzia do kompilacji na serwerze, na którym uruchamiasz kompilację.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Zainstaluj platformę Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Upewnij się, że użytkownik `jenkins` ma uprawnienia do uruchamiania poleceń `docker`.

1. [Zainstaluj narzędzie kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Pobierz procesor jq](https://stedolan.github.io/jq/download/).

1. Zainstaluj procesor jq przy użyciu następującego polecenia:

   ```bash
   sudo apt-get install jq
   ```
   
1. Zainstaluj wtyczki na serwerze Jenkins, wykonując następujące czynności na pulpicie nawigacyjnym serwera Jenkins:

    1. Wybierz pozycję **Manage Jenkins > Manage Plugins > Available** (Zarządzanie usługą Jenkins > Zarządzanie wtyczkami > Dostępne).
    1. Wyszukaj i zainstaluj wtyczkę usługi Azure Container Service.

1. Dodaj poświadczenia umożliwiające zarządzanie zasobami na platformie Azure. Jeśli nie masz jeszcze wtyczki, zainstaluj **wtyczkę poświadczeń platformy Azure**.

1. Dodaj poświadczenia jednostki usługi platformy Azure jako typ **Microsoft Azure Service Principal** (Jednostka usługi Microsoft Azure).

1. Dodaj nazwę użytkownika i hasło rejestru platformy Docker na platformie Azure (uzyskane w sekcji „Tworzenie wystąpienia usługi Container Registry”) jako typ **Username with password** (Nazwa użytkownika z hasłem).

## <a name="edit-the-jenkinsfile"></a>Edytowanie pliku Jenkinsfile

1. Z poziomu własnego repozytorium przejdź do katalogu `/deploy/aks/` i otwórz plik `Jenkinsfile`.

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
    
    Zaktualizuj identyfikator poświadczeń usługi Container Registry:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Tworzenie zadania
1. Dodaj nowe zadanie o typie **Pipeline** (Potok).

1. Wybierz kolejno pozycje **Pipeline** > **Definition** > **Pipeline script from SCM** (Potok > Definicja > Skrypt potoku z SCM).

1. Wprowadź adres URL repozytorium SCM, zastępując symbol zastępczy &lt;your-forked-repo> nazwą rozwidlonego repozytorium.

1. Wprowadź ścieżkę skryptu w postaci `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Uruchamianie zadania

1. Upewnij się, że możesz pomyślnie uruchomić projekt w środowisku lokalnym. Oto kroki tej procedury: [Run project on local machine](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it) (Uruchamianie projektu na komputerze lokalnym).

1. Uruchom zadanie serwera Jenkins. Podczas pierwszego uruchomienia zadania serwer Jenkins wdraża aplikację z listą zadań do wykonania w niebieskim środowisku, które domyślnie jest środowiskiem nieaktywnym. 

1. Aby sprawdzić, czy zadanie zostało uruchomione, przejdź do następujących adresów URL:
    - Publiczny punkt końcowy: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Niebieski punkt końcowy — `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Zielony punkt końcowy — `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Publiczny i niebieski testowy punkt końcowy mają tę samą aktualizację, a w zielonym punkcie końcowym jest wyświetlany domyślny obraz tomcat. 

Jeśli kompilacja zostanie uruchomiona więcej niż jeden raz, jest ona umieszczana na przemian w niebieskim i zielonym wdrożeniu. Innymi słowy, jeśli bieżące środowisko jest niebieskie, zadanie przeprowadzi wdrożenie i testowanie w zielonym środowisku. Następnie, jeśli testy przebiegną pomyślnie, zadanie zaktualizuje publiczny punkt końcowy aplikacji w taki sposób, aby ruch był kierowany do zielonego środowiska.

## <a name="additional-information"></a>Dodatkowe informacje

Więcej informacji dotyczących wdrażania bez jakichkolwiek przestojów można uzyskać z tego [szablon szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeżeli nie potrzebujesz już zasobów utworzonych w ramach tego samouczka, możesz je usunąć.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Kolejne kroki

W ramach tego samouczka wyjaśniono, w jaki sposób można przeprowadzić wdrażanie w usłudze AKS przy użyciu serwera Jenkins i niebieskiego/zielonego wzorca wdrażania. Aby dowiedzieć się więcej na temat dostawcy serwera Jenkins na platformie Azure, przejdź do witryny usługi Jenkins na platformie Azure.

> [!div class="nextstepaction"]
> [Usługa Jenkins na platformie Azure](/azure/jenkins/)