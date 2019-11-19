---
title: Uruchamianie interfejsu wiersza polecenia platformy Azure za pomocą rozwiązania Jenkins
description: Dowiedz się, jak przy użyciu interfejsu wiersza polecenia platformy Azure wdrożyć aplikację internetową Java na platformie Azure w potoku Jenkins
keywords: jenkins, azure, devops, usługa app service, cli
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bd9192974f6860d08d84a9028702ce2203f562e7
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158821"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Wdrażanie w usłudze Azure App Service za pomocą serwera Jenkins i interfejsu wiersza polecenia platformy Azure
Aby wdrożyć aplikację internetową Java na platformie Azure, można użyć interfejsu wiersza polecenia platformy Azure w [potoku Jenkins](https://jenkins.io/doc/book/pipeline/). W tym samouczku utworzysz potok CI/CD na maszynie wirtualnej platformy Azure. Wykonasz m.in. następujące czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej usługi Jenkins
> * Konfigurowanie usługi Jenkins
> * Tworzenie aplikacji internetowej na platformie Azure
> * Przygotowywanie repozytorium GitHub
> * Tworzenie potoku Jenkins
> * Uruchamianie potoku i weryfikowanie aplikacji internetowej

Dla tego samouczka wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Tworzenie i konfigurowanie wystąpienia serwera Jenkins
Jeśli nie masz jeszcze wzorca Jenkins, Zacznij od [szablonu rozwiązania](install-jenkins-solution-template.md), który domyślnie zawiera wymaganą wtyczkę [poświadczeń platformy Azure](https://plugins.jenkins.io/azure-credentials) . 

Wtyczka poświadczeń platformy Azure umożliwia przechowywanie poświadczeń głównych usługi Microsoft Azure w Jenkins. W wersji 1.2 dodaliśmy obsługę potoku Jenkins, aby mógł on uzyskiwać poświadczenia platformy Azure. 

Upewnij się, że masz wersję 1.2 lub nowszą:
* Na pulpicie nawigacyjnym serwera Jenkins kliknij pozycję **Manage Jenkins -> Plugin Manager ->** (Zarządzaj serwerem Jenkins -> Menedżer wtyczek ->) i wyszukaj ciąg **Azure Credential** (Poświadczenia platformy Azure). 
* Zaktualizuj wtyczkę, jeśli wersja jest wcześniejsza niż 1,2.

Wzorzec serwera Jenkins wymaga również zestawu Java JDK i narzędzia Maven. Aby je zainstalować, zaloguj się do wzorca serwera Jenkins przy użyciu protokołu SSH i uruchom następujące polecenia:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Dodawanie jednostki usługi platformy Azure do poświadczeń serwera Jenkins

Poświadczenia platformy Azure są potrzebne do uruchomienia interfejsu wiersza polecenia platformy Azure.

* Na pulpicie nawigacyjnym serwera Jenkins kliknij pozycję **Credentials -> System ->** (Poświadczenia -> System ->). Kliknij pozycję **Global credentials(unrestricted)** (Poświadczenia globalne (bez ograniczeń)).
* Kliknij pozycję **Add Credentials** (Dodaj poświadczenia), aby dodać [jednostkę usługi Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json), wypełniając pola: Subscription ID (Identyfikator subskrypcji), Client ID (Identyfikator klienta), Client Secret (Klucz tajny klienta) i OAuth 2.0 Token Endpoint (Punkt końcowy tokenu OAuth 2.0). Podaj identyfikator w celu użycia go w kolejnym kroku.

![Dodawanie poświadczeń](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Tworzenie usługi Azure App Service w celu wdrożenia aplikacji internetowej Java

Utwórz plan usługi Azure App Service w warstwie cenowej **BEZPŁATNA** za pomocą polecenia [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) interfejsu wiersza polecenia. Plan usługi App Service definiuje zasoby fizyczne używane do hostowania aplikacji. Wszystkie aplikacje przypisane do planu usługi App Service współdzielą te zasoby, ograniczając koszt hostowania wielu aplikacji. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Gdy plan jest gotowy, interfejs wiersza polecenia platformy Azure zawiera dane wyjściowe podobne do poniższego przykładu:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Tworzenie aplikacji internetowej platformy Azure

 Za pomocą polecenia [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) interfejsu wiersza polecenia utwórz definicję aplikacji internetowej w planie usługi App Service `myAppServicePlan`. Definicja aplikacji internetowej zawiera adres URL umożliwiający uzyskanie dostępu do aplikacji i konfiguruje kilka opcji wdrażania kodu na platformie Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Zastąp symbol zastępczy `<app_name>` własną unikatową nazwą aplikacji. Ta unikatowa nazwa jest częścią domyślnej nazwy domeny aplikacji internetowej, dlatego musi być unikatowa wśród wszystkich aplikacji na platformie Azure. Niestandardowy wpis nazwy domeny można zmapować na aplikację internetową przed udostępnieniem jej użytkownikom.

Gdy definicja aplikacji internetowej jest gotowa, w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Konfigurowanie środowiska Java 

Ustaw konfigurację środowiska uruchomieniowego języka Java wymaganą przez aplikację przy użyciu polecenia [az appservice web config update](/cli/azure/webapp/config).

Następujące polecenie konfiguruje aplikację internetową do uruchamiania razem z zestawem Java 8 JDK i środowiskiem [Apache Tomcat](https://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Przygotowywanie repozytorium GitHub
Otwórz repozytorium [prostej aplikacji internetowej Java dla platformy Azure](https://github.com/azure-devops/javawebappsample). Aby utworzyć rozwidlenie repozytorium na koncie usługi GitHub, kliknij przycisk **Rozwidlenie** w prawym górnym rogu.

* W internetowym interfejsie użytkownika usługi GitHub otwórz plik **Jenkinsfile**. Kliknij ikonę ołówka, aby edytować ten plik w celu zaktualizowania grupy zasobów i nazwy aplikacji internetowej w wierszach 20 i 21.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Zmień wiersz 23, aby zaktualizować identyfikator poświadczeń w Twoim wystąpieniu serwera Jenkins

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Tworzenie potoku Jenkins
Otwórz narzędzie Jenkins w przeglądarce internetowej i kliknij pozycję **New Item** (Nowy element). 

* Podaj nazwę zadania i wybierz pozycję **Pipeline** (Potok). Kliknij przycisk **OK**.
* Następnie kliknij kartę **Pipeline** (Potok). 
* W polu **Definition** (Definicja) wybierz wartość **Pipeline script from SCM** (Skrypt potoku z menedżera SCM).
* W polu **SCM** wybierz wartość **Git**.
* Wprowadź adres URL usługi GitHub dla rozwidlonego repozytorium: https:\<Twoje rozwidlone repozytorium\>.git
* Kliknij pozycję **Zapisz**

## <a name="test-your-pipeline"></a>Testowanie potoku
* Przejdź do utworzonego potoku i kliknij przycisk **Build Now** (Kompiluj teraz).
* Kompilacja powinna zostać zakończona pomyślnie w ciągu kilku sekund. Następnie możesz przejść do kompilacji i kliknąć pozycję **Console Output** (Dane wyjściowe konsoli), aby wyświetlić szczegóły.

## <a name="verify-your-web-app"></a>Weryfikowanie aplikacji internetowej
Sprawdź, czy plik WAR został pomyślnie wdrożony w aplikacji internetowej. Otwórz przeglądarkę internetową:

* Przejdź do adresu http://&lt;nazwa_aplikacji>.azurewebsites.net/api/calculator/ping  
Zobaczysz:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Przejdź do adresu http://&lt;nazwa_aplikacji>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (zastąp wartości &lt;x > i &lt;y > dowolnymi liczbami), aby uzyskać sumę wartości x i y.

![Calculator: add (Kalkulator: dodawanie)](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Wdrażanie w usłudze Azure Web App on Linux
Teraz, gdy wiesz już, jak używać interfejsu wiersza polecenia platformy Azure w potoku Jenkins, możesz zmodyfikować skrypt, aby przeprowadzić wdrożenie w usłudze Azure Web App on Linux.

Usługa Web App on Linux obsługuje inny sposób wdrażania, który wykorzystuje platformę Docker. Aby wdrożyć, należy podać plik Dockerfile, który pakuje aplikację internetową wraz ze środowiskiem uruchomieniowym usługi do obrazu platformy Docker. Wtyczka spowoduje skompilowanie obrazu, wypchnięcie go do rejestru platformy Docker i wdrożenie obrazu w aplikacji sieci Web.

* Postępuj zgodnie z instrukcjami opisanymi [tutaj](../app-service/containers/quickstart-nodejs.md), aby utworzyć aplikację internetową platformy Azure działającą w systemie Linux.
* Zainstaluj platformę Docker na wystąpieniu serwera Jenkins, wykonując instrukcje opisane w tym [artykule](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Utwórz rejestr kontenerów w witrynie Azure Portal, używając procedury opisanej [tutaj](/azure/container-registry/container-registry-get-started-azure-cli).
* W tym samym repozytorium [prostej aplikacji internetowej Java na platformie Azure](https://github.com/azure-devops/javawebappsample), które zostało przez Ciebie rozdzielone, edytuj plik **Jenkinsfile2**:
    * Wiersze 18-21: odpowiednio zaktualizuj do nazw swojej grupy zasobów, aplikacji internetowej i usługi ACR. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Wiersz 24: zaktualizuj ciąg \<azsrvprincipal\> do swojego identyfikatora poświadczeń.
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Utwórz nowy potok Jenkins, tak jak w przypadku wdrażania w aplikacji internetowej platformy Azure w systemie Windows, tylko tym razem użyj pliku **Jenkinsfile2**.
* Uruchom nowe zadanie.
* Aby zweryfikować, w interfejsie wiersza polecenia platformy Azure uruchom polecenie:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Uzyskasz następujący wynik:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Przejdź do adresu http://&lt;nazwa_aplikacji>.azurewebsites.net/api/calculator/ping. Zostanie wyświetlony komunikat: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Przejdź do adresu http://&lt;nazwa_aplikacji>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (zastąp wartości &lt;x > i &lt;y > dowolnymi liczbami), aby uzyskać sumę wartości x i y.
    
## <a name="next-steps"></a>Następne kroki
W tym samouczku skonfigurowaliśmy potok Jenkins, który wyewidencjonowuje kod źródłowy w repozytorium GitHub. Uruchamia narzędzie Maven, aby skompilować plik war, a następnie używa interfejsu wiersza polecenia platformy Azure w celu wdrożenia w usłudze Azure App Service. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej usługi Jenkins
> * Konfigurowanie usługi Jenkins
> * Tworzenie aplikacji internetowej na platformie Azure
> * Przygotowywanie repozytorium GitHub
> * Tworzenie potoku Jenkins
> * Uruchamianie potoku i weryfikowanie aplikacji internetowej
