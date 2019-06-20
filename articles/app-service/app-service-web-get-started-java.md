---
title: Tworzenie aplikacji sieci web Java na Windows — usłudze Azure App Service
description: W tym przewodniku Szybki Start wdrożysz swoją pierwszą Java Hello World w usłudze Azure App Service w Windows w ciągu kilku minut.
keywords: azure, app service, web app, windows, java, maven, quickstart
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jasonfreeberg
ms.custom: mvc
ms.openlocfilehash: bd11b5334fbffc28aa6869c9f37b2b9909692d56
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154794"
---
# <a name="quickstart-create-a-java-app-in-app-service"></a>Szybki start: Tworzenie aplikacji Java w usłudze App Service

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. Aby wdrożyć w usłudze App Service w systemie _Linux_, zobacz [Java tworzenie aplikacji sieci web w systemie Linux](./containers/quickstart-java.md).
>

Usługa [Azure App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie.  Ten przewodnik Szybki Start przedstawia sposób użycia [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) z [wtyczka Maven Plugin for Azure App Service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) do wdrożenia pliku archiwum (WAR) w sieci web Java.

> [!NOTE]
> To samo można również wykonać przy użyciu popularnych środowisk IDE, takie jak IntelliJ oraz Eclipse. Zapoznaj się z naszym podobne dokumenty w [Azure Toolkit for IntelliJ Przewodnik Szybki Start](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) lub [Azure Toolkit for Eclipse Przewodnik Szybki Start](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Przykładowa aplikacja działająca na platformie Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.6.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> W tym artykule pracujemy tylko z aplikacjami w języku Java umieszczonych w pakietach w postaci plików WAR. Wtyczka obsługuje również aplikacje internetowe JAR. Odwiedź stronę [Deploy a Java SE JAR file to App Service on Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) (Wdrażanie pliku JAR języka Java SE do usługi App Service w systemie Linux), aby wypróbować tę funkcję.


Zaktualizuj następujące symbole zastępcze w konfiguracji wtyczki:

| Symbol zastępczy | Opis |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | Unikatowy identyfikator subskrypcję, którą chcesz wdrożyć aplikację. Identyfikator domyślnej subskrypcji można znaleźć w usłudze Cloud Shell lub interfejsu wiersza polecenia przy użyciu `az account show` polecenia. Dla wszystkich dostępnych subskrypcji, użyj `az account list` polecenia.|
| `RESOURCEGROUP_NAME` | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja. Dzięki wprowadzeniu wszystkich zasobów dla aplikacji do grupy można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów spowodowałoby usunięcie wszystkich zasobów skojarzonych z aplikacją. Zaktualizuj tę wartość za pomocą unikatowa nazwa nowej grupy zasobów, na przykład *myResourceGroup*. Za pomocą tej nazwy grupy zasobów wyczyścisz wszystkie zasoby platformy Azure w późniejszej sekcji. |
| `WEBAPP_NAME` | Nazwa aplikacji będzie częścią nazwy hosta dla aplikacji po wdrożeniu na platformie Azure (WEBAPP_NAME.azurewebsites.net). Zaktualizuj tę wartość przy użyciu unikatowej nazwy nowej aplikacji usługi App Service, która będzie hostem aplikacji Java, na przykład *contoso*. |
| `REGION` | Region platformy Azure, w którym hostowana jest aplikacja, na przykład *westus2*. Listę regionów można uzyskać z usługi Cloud Shell lub interfejsu wiersza polecenia przy użyciu polecenia `az account list-locations`. |

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdróż aplikację Java na platformie Azure za pomocą następującego polecenia:

```bash
mvn package azure-webapp:deploy
```

Po zakończeniu wdrażania w przeglądarce internetowej przejdź do wdrożonej aplikacji, używając następującego adresu URL, na przykład `http://<webapp>.azurewebsites.net/`.

![Przykładowa aplikacja działająca na platformie Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

**Gratulacje!** Wdrożono pierwszej aplikacji Java w usłudze App Service w Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Platforma Azure dla zasobów deweloperów języka Java](/java/azure/)

> [!div class="nextstepaction"]
> [Mapowanie domeny niestandardowej](app-service-web-tutorial-custom-domain.md)