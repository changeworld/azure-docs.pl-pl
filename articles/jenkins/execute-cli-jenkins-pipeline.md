---
title: Wykonanie interfejsu wiersza polecenia platformy Azure z Wpięć | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć aplikację sieci web Java na platformie Azure w potoku Wpięć za pomocą wiersza polecenia platformy Azure
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
ms.openlocfilehash: 2b568bd22858a42178e2821e0e97a3b4ebdfccd5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "28926934"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Wdrażanie usługi Azure App Service z Wpięć i interfejsu wiersza polecenia platformy Azure
Aby wdrożyć aplikację sieci web Java na platformie Azure, można użyć wiersza polecenia platformy Azure w [potoku Wpięć](https://jenkins.io/doc/book/pipeline/). W tym samouczku utworzysz potok CI/CD na maszynie wirtualnej platformy Azure. Wykonasz m.in. następujące czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej usługi Jenkins
> * Konfigurowanie usługi Jenkins
> * Tworzenie aplikacji sieci web na platformie Azure
> * Przygotowanie repozytorium GitHub
> * Tworzenie potoku Wpięć
> * Należy uruchomić proces i sprawdzić aplikacji sieci web

Dla tego samouczka wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Tworzenie i konfigurowanie Wpięć wystąpienia
Jeśli nie ma już wzorzec Wpięć, Rozpocznij od [szablon rozwiązania](install-jenkins-solution-template.md), w tym wymaganych [poświadczenia Azure](https://plugins.jenkins.io/azure-credentials) wtyczki domyślnie. 

Dodatek poświadczenia Azure umożliwia przechowywanie poświadczenia główne usługi Microsoft Azure w Wpięć. W wersji 1.2 dodano obsługę tak tego potoku Wpięć mogą uzyskać poświadczenia platformy Azure. 

Upewnij się, że w wersji 1.2 lub nowszej:
* W ramach Wpięć pulpitu nawigacyjnego, kliknij przycisk **Menedżera wtyczki -> Zarządzaj Wpięć ->** i wyszukaj **poświadczenia Azure**. 
* Zaktualizuj wtyczki, jeśli wersja jest starsza niż 1.2.

We wzorcu Wpięć są również wymagane Java JDK i Maven. Aby zainstalować, zaloguj się do wzorca Wpięć przy użyciu protokołu SSH i uruchom następujące polecenia:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Dodaj nazwę główną usługi Azure do Wpięć poświadczeń

Poświadczenia platformy Azure jest wymagany do wykonania wiersza polecenia platformy Azure.

* W pulpicie nawigacyjnym Wpięć kliknij **poświadczeń -> System ->**. Kliknij przycisk **credentials(unrestricted) globalne**.
* Kliknij przycisk **dodać poświadczenia** można dodać [nazwy głównej usługi Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) wypełniając identyfikator subskrypcji, Identyfikatora klienta, klucz tajny klienta i końcowym tokenów OAuth 2.0. Podaj identyfikator do użycia w kolejnym kroku.

![Dodawanie poświadczeń](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Tworzenie usługi Azure App Service dla wdrażania aplikacji sieci web Java

Tworzenie planu usługi aplikacji Azure z **wolne** ceny za pomocą warstwy [Tworzenie planu usług aplikacji az](/cli/azure/appservice/plan#az_appservice_plan_create) polecenia interfejsu wiersza polecenia. Planu usług aplikacji definiuje zasoby fizyczne, używana do hostowania aplikacji. Wszystkie aplikacje przypisane do planu usług aplikacji udostępniania tych zasobów, co umożliwia zapisywanie koszt odnośnie do hostowania wielu aplikacji. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Gdy plan jest gotowy, interfejsu wiersza polecenia Azure zawiera podobne dane wyjściowe do poniższego przykładu:

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

### <a name="create-an-azure-web-app"></a>Tworzenie aplikacji sieci Web platformy Azure

 Użyj [tworzenie aplikacji sieci Web az](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) polecenia interfejsu wiersza polecenia do tworzenia definicji aplikacji sieci web w `myAppServicePlan` planu usługi aplikacji. Definicja aplikacji sieci web zawiera adres URL w celu uzyskania dostępu do aplikacji z i konfiguruje kilka sposobów wdrażania kodu na platformie Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

SUBSTITUTE `<app_name>` symbol zastępczy własne unikatowej nazwy aplikacji. Ta nazwa jest częścią domyślna nazwa domeny dla aplikacji sieci web, nazwa musi być unikatowy w obrębie wszystkich aplikacji w usłudze Azure. Przed udostępnieniem użytkownikom, możesz mapować wpis nazwy domeny niestandardowej aplikacji sieci web.

Podczas definiowania aplikacji sieci web jest gotowy, interfejsu wiersza polecenia Azure zawiera informacje podobne do poniższego przykładu: 

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

### <a name="configure-java"></a>Konfigurowanie języka Java 

Ustawianie konfiguracji środowiska uruchomieniowego języka Java wymagające aplikacji z [aktualizacja konfiguracji sieci web appservice az](/cli/azure/appservice/web/config#az_appservice_web_config_update) polecenia.

Następujące polecenie konfiguruje aplikacji sieci web do uruchamiania na ostatnie JDK 8 Java i [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Przygotowanie repozytorium GitHub
Otwórz [prostej aplikacji sieci Web Java na platformie Azure](https://github.com/azure-devops/javawebappsample) repozytorium. Aby rozwidlania repozytorium na koncie usługi GitHub, kliknij przycisk **rozwidlenia** przycisk w prawym górnym rogu.

* W witrynie GitHub interfejsu użytkownika sieci web, otwórz **Jenkinsfile** pliku. Kliknij ikonę ołówka, aby edytować ten plik, aby zaktualizować grupy zasobów i nazwy aplikacji sieci web w wierszu 20 i 21 odpowiednio.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Zmień wiersz 23 można zaktualizować poświadczeń identyfikator wystąpienia Wpięć

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Tworzenie potoku Wpięć
Otwórz Wpięć w przeglądarce sieci web, kliknij pozycję **nowy element**. 

* Podaj nazwę zadania i wybierz **potoku**. Kliknij przycisk **OK**.
* Kliknij przycisk **potoku** karcie dalej. 
* Dla **definicji**, wybierz pozycję **potoku skryptu z SCM**.
* Aby uzyskać **SCM**, wybierz pozycję **Git**.
* Wprowadź adres URL usługi GitHub dla Twojego repozytorium rozwidlonych: https:\<Twojego repozytorium rozwidlonych\>.git
* Kliknij przycisk **Zapisz**

## <a name="test-your-pipeline"></a>Testowanie potoku
* Przejdź do potoku został utworzony, kliknij przycisk **kompilacji teraz**
* Kompilacja ma być pomyślnie wykonane w ciągu kilku sekund i można przejść do kompilacji i kliknij przycisk **dane wyjściowe konsoli** Aby wyświetlić szczegóły

## <a name="verify-your-web-app"></a>Sprawdź aplikację sieci web
Aby sprawdzić WAR plik został wdrożony pomyślnie na aplikację sieci web. Otwórz przeglądarkę sieci web:

* Przejdź do http://&lt;nazwa_aplikacji >.azurewebsites.net/api/calculator/ping  
Zobacz:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Przejdź do http://&lt;nazwa_aplikacji >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (Zastąp &lt;x > i &lt;y > z dowolnej liczby) można pobrać sumy x i y

![Kalkulator: Dodaj](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Wdrażanie aplikacji sieci Web platformy Azure w systemie Linux
Teraz, Znając sposób użycia interfejsu wiersza polecenia Azure planowaną Wpięć można zmodyfikować skrypt do wdrożenia aplikacji sieci Web platformy Azure w systemie Linux.

Aplikacji w systemie Linux sieci Web obsługuje inny sposób, aby wykonać wdrożenie, w którym jest użycie Docker. Do wdrożenia, należy dostarczyć plik Dockerfile, które pakiety aplikacji sieci web za pomocą usługi czasu wykonywania w obrazie Docker. Wtyczka następnie utworzyć obraz, wypchnąć go do rejestru Docker i wdrożenia obrazu do aplikacji sieci web.

* Wykonaj kroki [tutaj](../app-service/containers/quickstart-nodejs.md) do tworzenia aplikacji sieci Web platformy Azure, uruchomiony w systemie Linux.
* Zainstaluj Docker na wystąpienie Wpięć zgodnie z instrukcjami w tym [artykułu](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Tworzenie rejestru kontenera w portalu Azure przy użyciu kroków [tutaj](/azure/container-registry/container-registry-get-started-azure-cli).
* W tym samym [prostej aplikacji sieci Web Java na platformie Azure](https://github.com/azure-devops/javawebappsample) rozwidlone repozytorium, Edytuj **Jenkinsfile2** pliku:
    * Wiersz 18-21, zaktualizuj odpowiednio do nazwy grupy zasobów, aplikacji sieci web i ACR. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Wiersz 24, aktualizacja \<azsrvprincipal\> do Identyfikatora poświadczeń
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Utwórz nowy potok Wpięć, podczas wdrażania aplikacji sieci web platformy Azure w systemie Windows, ten czas, używanego **Jenkinsfile2** zamiast tego.
* Uruchom nowe zadanie.
* Aby sprawdzić, w wiersza polecenia platformy Azure, uruchom polecenie:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Można uzyskać następujące wyniki:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Przejdź do http://&lt;nazwa_aplikacji >.azurewebsites.net/api/calculator/ping. Zostanie wyświetlony komunikat: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Przejdź do http://&lt;nazwa_aplikacji >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (Zastąp &lt;x > i &lt;y > z dowolnej liczby) można pobrać sumy x i y
    
## <a name="next-steps"></a>Kolejne kroki
W tym samouczku należy skonfigurować Wpięć potok, który umożliwia sprawdzenie kodu źródłowego w repozytorium GitHub. Uruchamia Maven, aby utworzyć plik war, a następnie używa interfejsu wiersza polecenia Azure do wdrożenia w usłudze Azure App Service. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej usługi Jenkins
> * Konfigurowanie usługi Jenkins
> * Tworzenie aplikacji sieci web na platformie Azure
> * Przygotowanie repozytorium GitHub
> * Tworzenie potoku Wpięć
> * Należy uruchomić proces i sprawdzić aplikacji sieci web
