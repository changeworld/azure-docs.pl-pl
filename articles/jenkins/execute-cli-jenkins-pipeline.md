---
title: Wykonania wiersza polecenia platformy Azure przy użyciu narzędzia Jenkins | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażanie aplikacji sieci web Java na platformie Azure w potoku usługi Jenkins za pomocą wiersza polecenia platformy Azure
services: app-service\web
documentationcenter: ''
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 1796e9f76e39334c8bbdd03463a0f91e9b47cb17
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421308"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Wdrażanie w usłudze Azure App Service z usługami Jenkins i interfejs wiersza polecenia platformy Azure
Aby wdrożyć aplikację sieci web Java na platformie Azure, można użyć wiersza polecenia platformy Azure w [potoku Jenkins](https://jenkins.io/doc/book/pipeline/). W tym samouczku utworzysz potok CI/CD na maszynie wirtualnej platformy Azure. Wykonasz m.in. następujące czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej usługi Jenkins
> * Konfigurowanie usługi Jenkins
> * Tworzenie aplikacji sieci web na platformie Azure
> * Przygotowanie repozytorium GitHub
> * Instrukcje tworzenia potoku usługi Jenkins
> * Uruchamianie potoku i zweryfikować aplikacji sieci web

Dla tego samouczka wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Tworzenie i Konfigurowanie narzędzia Jenkins wystąpienia
Jeśli nie masz jeszcze wzorca usługi Jenkins, skorzystaj z [szablon rozwiązania](install-jenkins-solution-template.md), która obejmuje wymagana [Azure Credentials](https://plugins.jenkins.io/azure-credentials) wtyczki domyślnie. 

Wtyczka poświadczeń platformy Azure umożliwia przechowywanie poświadczeń jednostki usługi Microsoft Azure w usłudze Jenkins. W wersji 1.2 Dodaliśmy obsługę tak to potoku Jenkins można uzyskać poświadczeń platformy Azure. 

Upewnij się, że w wersji 1.2 lub nowszej:
* Na pulpicie nawigacyjnym usługi Jenkins kliknij **Zarządzaj serwerem Jenkins -> Menedżer wtyczki ->** i wyszukaj **Azure Credential**. 
* Należy zaktualizować wtyczkę, jeśli wersja jest wcześniejsza niż 1.2.

Zestaw JDK języka Java i Maven, są również wymagane w wzorca usługi Jenkins. Aby zainstalować, zaloguj się do serwera głównego Jenkins przy użyciu protokołu SSH i uruchom następujące polecenia:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Dodaj nazwę główną usługi Azure poświadczenia usługi Jenkins

Poświadczeń platformy Azure jest potrzebny do wykonania wiersza polecenia platformy Azure.

* Na pulpicie nawigacyjnym usługi Jenkins kliknij **poświadczeń -> System ->**. Kliknij przycisk **globalnego credentials(unrestricted)**.
* Kliknij przycisk **Dodaj poświadczenia** dodać [nazwy głównej usługi Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) , wypełniając Identyfikatora subskrypcji, identyfikator klienta, klucza tajnego klienta i punkt końcowy protokołu OAuth 2.0 Token. Podaj identyfikator do użycia w kolejnym kroku.

![Dodaj poświadczenia](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Tworzenie usługi Azure App Service do wdrażania aplikacji sieci web Java

Utwórz plan usługi Azure App Service za pomocą **bezpłatna** ceny warstwy za pomocą [az appservice plan tworzenia](/cli/azure/appservice/plan#az-appservice-plan-create) interfejsu wiersza polecenia. Plan usługi App Service definiuje zasoby fizyczne używane do hostowania aplikacji. Wszystkie aplikacje przypisane do planu usługi App Service współdzielą te zasoby, ograniczając koszt hostowania wielu aplikacji. 

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

 Użyj [tworzenie az webapp](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) interfejsu wiersza polecenia Utwórz definicję aplikacji internetowej w `myAppServicePlan` planu usługi App Service. Definicja aplikacji internetowej zawiera adres URL umożliwiający uzyskanie dostępu do aplikacji i konfiguruje kilka opcji wdrażania kodu na platformie Azure. 

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

Ustaw konfigurację środowiska uruchomieniowego języka Java wymaganą przez aplikację za pomocą [az appservice web config update](/cli/azure/appservice/web/config#az-appservice-web-config-update) polecenia.

Następujące polecenie konfiguruje aplikację internetową do uruchamiania razem z zestawem Java 8 JDK i środowiskiem [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Przygotowanie repozytorium GitHub
Otwórz [prostej aplikacji sieci Web Java na platformie Azure](https://github.com/azure-devops/javawebappsample) repozytorium. Aby utworzyć rozwidlenie repozytorium na koncie usługi GitHub, kliknij przycisk **rozwidlenia** przycisk w prawym górnym rogu.

* W witrynie GitHub interfejsu użytkownika sieci web, otwórz **pliku Jenkins** pliku. Kliknij ikonę ołówka, aby edytować ten plik, aby odpowiednio zaktualizować grupy zasobów i nazwę aplikacji sieci web w taki sposób, w wierszu 20 i 21.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Zmień wiersz 23 można zaktualizować Identyfikatora poświadczeń do wystąpienia usługi Jenkins

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Instrukcje tworzenia potoku usługi Jenkins
Otwórz narzędzia Jenkins w przeglądarce sieci web, kliknij pozycję **nowy element**. 

* Podaj nazwę zadania, a następnie wybierz pozycję **potoku**. Kliknij przycisk **OK**.
* Kliknij przycisk **potoku** obok karty. 
* Dla **definicji**, wybierz opcję **potoku skrypt z SCM**.
* Aby uzyskać **SCM**, wybierz opcję **Git**.
* Wprowadź adres URL usługi GitHub do rozwidlonego repozytorium: https:\<rozwidlonego repozytorium\>.git
* Kliknij pozycję **Zapisz**

## <a name="test-your-pipeline"></a>Testowanie potoku
* Przejdź do utworzonego potoku, kliknij przycisk **Kompiluj teraz**
* Kompilacja ma być pomyślnie wykonane w ciągu kilku sekund i można przejść do kompilacji i kliknij przycisk **dane wyjściowe konsoli** Aby wyświetlić szczegóły

## <a name="verify-your-web-app"></a>Sprawdź aplikację sieci web
Aby sprawdzić WAR plik został wdrożony pomyślnie na aplikację sieci web. Otwórz przeglądarkę sieci web:

* Przejdź do http://&lt;nazwa_aplikacji >.azurewebsites.net/api/calculator/ping  
Zobacz:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Przejdź do http://&lt;nazwa_aplikacji >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;t > (Zastąp &lt;x > i &lt;y > z dowolnej liczby) do uzyskania sumy x i y

![Kalkulator: Dodaj](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Wdrażanie aplikacji sieci Web platformy Azure w systemie Linux
Teraz gdy wiesz, jak używać wiersza polecenia platformy Azure w potoku usługi Jenkins, możesz zmodyfikować skrypt, aby wdrożyć aplikację internetową platformy Azure w systemie Linux.

Usługa Web App on Linux obsługuje inny sposób na wdrożenie, które mają korzystać z aparatu Docker. Aby wdrożyć, musisz podać plik Dockerfile, które pakiety aplikacji sieci web ze środowiskiem uruchomieniowym usługi do obrazu platformy Docker. Wtyczka następnie utworzyć obraz, Wypchnij go do rejestru platformy Docker i wdrożenie obrazu do aplikacji sieci web.

* Postępuj zgodnie z instrukcjami [tutaj](../app-service/containers/quickstart-nodejs.md) utworzyć aplikację internetową platformy Azure z systemem Linux.
* Zainstalować platformę Docker na wystąpienia usługi Jenkins, wykonując instrukcje podane w tym [artykułu](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Tworzenie rejestru kontenerów w witrynie Azure portal wykonując kroki [tutaj](/azure/container-registry/container-registry-get-started-azure-cli).
* W tym samym [prostej aplikacji sieci Web Java na platformie Azure](https://github.com/azure-devops/javawebappsample) repozytorium utworzony rozwidlenie, Edytuj **Jenkinsfile2** pliku:
    * Wiersz 18-21, odpowiednio zaktualizować nazwy grupy zasobów, aplikacji sieci web i usługi ACR. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Wiersz 24, aktualizacja \<azsrvprincipal\> do Identyfikatora poświadczeń
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Utwórz nowy potok Jenkins, jak w przypadku wdrażania aplikacji sieci web platformy Azure w Windows, tylko tym razem użyto **Jenkinsfile2** zamiast tego.
* Uruchamianie nowego zadania.
* Aby sprawdzić, w interfejsie wiersza polecenia platformy Azure, uruchom polecenie:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Możesz uzyskać następujące wyniki:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Przejdź do http://&lt;nazwa_aplikacji >.azurewebsites.net/api/calculator/ping. Zostanie wyświetlony komunikat: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Przejdź do http://&lt;nazwa_aplikacji >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;t > (Zastąp &lt;x > i &lt;y > z dowolnej liczby) do uzyskania sumy x i y
    
## <a name="next-steps"></a>Kolejne kroki
W tym samouczku skonfigurowano potok Jenkins, który wyewidencjonuje kod źródłowy w repozytorium GitHub. Uruchamia narzędzie Maven, aby utworzyć plik war, a następnie używa wiersza polecenia platformy Azure do wdrożenia w usłudze Azure App Service. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej usługi Jenkins
> * Konfigurowanie usługi Jenkins
> * Tworzenie aplikacji sieci web na platformie Azure
> * Przygotowanie repozytorium GitHub
> * Instrukcje tworzenia potoku usługi Jenkins
> * Uruchamianie potoku i zweryfikować aplikacji sieci web
