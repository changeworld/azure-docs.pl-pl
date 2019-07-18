---
title: Tworzenie aplikacji internetowej Java w systemie Linux — Azure App Service
description: W ramach tego podręcznika Szybki start wdrożysz swoją pierwszą aplikację Hello world w języku Java w usłudze Azure App Service w systemie Linux w ciągu kilku minut.
keywords: Azure, App Service, Web App, Linux, Java, Maven, szybki start
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 30689e05a2567646ff541818dc68a90c13da7a56
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297254"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>Szybki start: tworzenie aplikacji w języku Java w usłudze App Service w systemie Linux

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym przewodniku szybki start pokazano, jak używać [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) z [wtyczką Maven dla Azure App Service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) w celu wdrożenia pliku archiwum sieci Web (War) języka Java.
> [!NOTE]
>
> Ten sam element można także wykonać przy użyciu popularnych środowisk IDE, takich jak IntelliJ i zaćmienie. Zapoznaj się z naszymi dokumentami w [Azure Toolkit for IntelliJ przewodniku szybki start](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) lub [Azure Toolkit for Eclipse przewodnika Szybki Start](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Tworzenie aplikacji w języku Java

Wykonaj następujące polecenie narzędzia Maven w wierszu polecenia usługi Cloud Shell, aby utworzyć nową aplikację o nazwie `helloworld`:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Konfigurowanie wtyczki Maven

Aby dokonać wdrożenia z systemu Maven, użyj edytora kodu w usłudze Cloud Shell, aby otworzyć plik `pom.xml` projektu w katalogu `helloworld`. 

```bash
code pom.xml
```

Następnie dodaj następującą definicję wtyczki w elemencie `<build>` pliku `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
    </plugin>
</plugins>
```

Proces wdrażania do Azure App Service używa poświadczeń konta z interfejsu wiersza polecenia platformy Azure. Przed kontynuowaniem [Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```

Następnie możesz skonfigurować wdrożenie `mvn azure-webapp:config` , uruchomić polecenie Maven w wierszu polecenia i użyć konfiguracji domyślnych, naciskając klawisz **Enter** do momentu uzyskania monitu o **potwierdzenie (t/N)** , a następnie naciśnij klawisz **"Y"** , a konfiguracja zostanie ukończona. .

```cmd
~@Azure:~/helloworld$ mvn azure-webapp:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.7.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. jre8 [*]
2. java11
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. jre8
3. TOMCAT 8.5 [*]
4. WILDFLY 14
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> W tym artykule pracujemy tylko z aplikacjami w języku Java umieszczonych w pakietach w postaci plików WAR. Wtyczka obsługuje również aplikacje internetowe JAR. Odwiedź stronę [Deploy a Java SE JAR file to App Service on Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) (Wdrażanie pliku JAR języka Java SE do usługi App Service w systemie Linux), aby wypróbować tę funkcję.

`pom.xml` Przejdź ponownie, aby zobaczyć, że konfiguracja wtyczki została zaktualizowana, w razie potrzeby można zmodyfikować inne konfiguracje dla App Service bezpośrednio w pliku pliku pom:

 Właściwość | Wymagane | Opis | Version
---|---|---|---
`<schemaVersion>` | false | Określ wersję schematu konfiguracji. Obsługiwane są następujące wartości `v1`: `v2`,. | 1.5.2
`<resourceGroup>` | true | Grupa zasobów platformy Azure dla aplikacji sieci Web. | 0.1.0 +
`<appName>` | true | Nazwa aplikacji sieci Web. | 0.1.0 +
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | true | Określa region, w którym będzie hostowana aplikacja sieci Web; wartość domyślna to **zachodnie**. Wszystkie prawidłowe regiony w sekcji [Obsługiwane regiony](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) . | 0.1.0 +
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | Warstwa cenowa aplikacji sieci Web. Wartość domyślna to **P1V2**.| 0.1.0 +
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | true | Konfiguracja środowiska uruchomieniowego, w [tym miejscu](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting)można zobaczyć szczegóły. | 0.1.0 +
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | true | W konfiguracji wdrożenia można zobaczyć szczegóły [tutaj](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting). | 0.1.0 +

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdróż aplikację Java na platformie Azure za pomocą następującego polecenia:

```bash
mvn package azure-webapp:deploy
```

Po zakończeniu wdrażania w przeglądarce internetowej przejdź do wdrożonej aplikacji, używając następującego adresu URL, na przykład `http://<webapp>.azurewebsites.net`. 

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-in-browser.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku Java w usłudze App Service w systemie Linux.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli te zasoby nie będą raczej potrzebne w przyszłości, usuń grupę zasobów, uruchamiając następujące polecenie w usłudze Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Wykonanie tego polecenia może potrwać około minutę.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Aplikacja Java Enterprise z PostgreSQL](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Java](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Ciągła integracja/ciągłe wdrażanie przy użyciu narzędzia Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Inne zasoby platformy Azure dla deweloperów języka Java](/java/azure/)
