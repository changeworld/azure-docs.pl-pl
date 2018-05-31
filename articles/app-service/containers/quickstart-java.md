---
title: Podręcznik Szybki start omawiający tworzenie aplikacji internetowej w języku Java w usłudze Azure App Service w systemie Linux
description: W ramach tego podręcznika Szybki start wdrożysz swoją pierwszą aplikację Hello world w języku Java w usłudze Azure App Service w systemie Linux w ciągu kilku minut.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/07/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 2018f5b7051f2b6906372dad3319c763974b93b1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355189"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Szybki start: tworzenie aplikacji internetowej w języku Java w usłudze App Service w systemie Linux

Usługa App Service w systemie Linux udostępnia obecnie funkcję w wersji zapoznawczej do obsługi aplikacji internetowych w języku Java. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym przewodniku Szybki start pokazano, jak za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) z [wtyczką Maven Plugin for Azure Web Apps (wersja zapoznawcza)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) wdrożyć aplikację internetową w języku Java, korzystając z wbudowanego obrazu systemu Linux.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-in-browser.png)

[Wdrażanie aplikacji internetowych w języku Java w kontenerze systemu Linux w chmurze przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-hello-world-web-app-linux) to alternatywny sposób wdrażania aplikacji w języku Java we własnym kontenerze.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start: 

* Zainstalowany lokalnie [interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Apache Maven](http://maven.apache.org/).



## <a name="create-a-java-app"></a>Tworzenie aplikacji w języku Java

Wykonaj następujące polecenie, używając narzędzia Maven do utworzenia nowej aplikacji internetowej *helloworld*:  

    mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp

Przejdź do nowego katalogu projektu *helloworld* i skompiluj wszystkie moduły za pomocą następującego polecenia:

    mvn verify

To polecenie zweryfikuje i utworzy wszystkie moduły, w tym plik *helloworld.war* w podkatalogu *helloworld/target*.


## <a name="deploying-the-java-app-to-app-service-on-linux"></a>Wdrażanie aplikacji w języku Java w usłudze App Service w systemie Linux

Istnieje wiele opcji wdrażania aplikacji internetowych w języku Java w usłudze App Service w systemie Linux. Dostępne są następujące opcje:

* [Wdrażanie za pomocą wtyczki Maven Plugin for Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)
* [Wdrażanie za pośrednictwem pliku ZIP albo WAR](https://docs.microsoft.com/azure/app-service/app-service-deploy-zip)
* [Wdrażanie za pośrednictwem protokołu FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)

W tym przewodniku Szybki start użyjesz wtyczki Maven Plugin for Azure Web Apps. Jej zalety to łatwość użycia z poziomu narzędzia Maven oraz tworzenie za Ciebie niezbędnych zasobów platformy Azure (grupy zasobów, planu usługi App Service i aplikacji internetowej).

### <a name="deploy-with-maven"></a>Wdrażanie za pomocą narzędzia Maven

Aby przeprowadzić wdrożenie z poziomu narzędzia Maven, dodaj następującą definicję wtyczki w elemencie `<build>` pliku *pom.xml*:

```xml
    <plugins>
      <plugin>
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-webapp-maven-plugin</artifactId> 
        <version>1.1.0</version>
        <configuration> 
          <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup> 
          <appName>YOUR_WEB_APP</appName> 
          <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>
          <deploymentType>ftp</deploymentType> 
          <resources> 
              <resource> 
                  <directory>${project.basedir}/target</directory> 
                  <targetPath>webapps</targetPath> 
                  <includes> 
                      <include>*.war</include> 
                  </includes> 
                  <excludes> 
                      <exclude>*.xml</exclude> 
                  </excludes> 
              </resource> 
          </resources> 
        </configuration>
      </plugin>
    </plugins>
```    

Zaktualizuj następujące symbole zastępcze w konfiguracji wtyczki:

| Symbol zastępczy | Opis |
| ----------- | ----------- |
| `YOUR_RESOURCE_GROUP` | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja internetowa. Dzięki wprowadzeniu wszystkich zasobów dla aplikacji do grupy można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów spowodowałoby usunięcie wszystkich zasobów skojarzonych z aplikacją. Zaktualizuj tę wartość przy użyciu unikatowej nazwy nowej grupy zasobów, na przykład *TestResources*. Za pomocą tej nazwy grupy zasobów wyczyścisz wszystkie zasoby platformy Azure w późniejszej sekcji. |
| `YOUR_WEB_APP` | Nazwa aplikacji będzie częścią nazwy hosta aplikacji internetowej po wdrożeniu na platformie Azure (APLIKACJA_INTERNETOWA.azurewebsites.net). Zaktualizuj tę wartość przy użyciu unikatowej nazwy nowej aplikacji internetowej platformy Azure, która będzie hostem aplikacji Java, na przykład *contoso*. |

Element `linuxRuntime` konfiguracji określa wbudowany obraz systemu Linux używany z aplikacją.

Uruchom następujące polecenie i wykonaj wszystkie instrukcje, aby uwierzytelnić się w interfejsie wiersza polecenia platformy Azure:

    az login

Wdróż aplikację Java w aplikacji internetowej za pomocą następującego polecenia:

    mvn clean package azure-webapp:deploy


Po zakończeniu wdrażania w przeglądarce internetowej przejdź do wdrożonej aplikacji, używając następującego adresu URL.

```bash
http://<app_name>.azurewebsites.net/helloworld
```

Przykładowy kod w języku Java jest uruchamiany w aplikacji internetowej z wbudowanym obrazem.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku Java w usłudze App Service w systemie Linux.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto narzędzia Maven do utworzenia aplikacji internetowej w języku Java, a następnie wdrożono tę aplikację w usłudze App Service w systemie Linux. Aby dowiedzieć się więcej o używaniu języka Java na platformie Azure, kliknij poniższy link.

> [!div class="nextstepaction"]
> [Azure dla deweloperów języka Java](https://docs.microsoft.com/java/azure/)

