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
ms.openlocfilehash: e286942f092d2e8c22824a18f5a6503d04a1be0c
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247559"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Szybki start: tworzenie aplikacji internetowej w języku Java w usłudze App Service w systemie Linux

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym przewodniku Szybki start pokazano, jak za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) z wtyczką [Maven Plugin for Azure Web Apps (wersja zapoznawcza)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) wdrożyć aplikację internetową w języku Java, korzystając z pliku Archiwum sieci Web (WAR).

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Tworzenie aplikacji w języku Java

Wykonaj następujące polecenie narzędzia Maven w wierszu polecenia usługi Cloud Shell, aby utworzyć nową aplikację internetową o nazwie `helloworld`:

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
        <version>1.4.0</version>
        <configuration>
   
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>
   
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> W tym artykule pracujemy tylko z aplikacjami w języku Java umieszczonymi w pakietach w postaci plików WAR. Wtyczka obsługuje również aplikacje internetowe JAR. Odwiedź stronę [Deploy a Java SE JAR file to App Service on Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) (Wdrażanie pliku JAR języka Java SE do usługi App Service w systemie Linux), aby wypróbować tę funkcję.


Zaktualizuj następujące symbole zastępcze w konfiguracji wtyczki:

| Symbol zastępczy | Opis |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja internetowa. Dzięki wprowadzeniu wszystkich zasobów dla aplikacji do grupy można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów spowodowałoby usunięcie wszystkich zasobów skojarzonych z aplikacją. Zaktualizuj tę wartość przy użyciu unikatowej nazwy nowej grupy zasobów, na przykład *TestResources*. Za pomocą tej nazwy grupy zasobów wyczyścisz wszystkie zasoby platformy Azure w późniejszej sekcji. |
| `WEBAPP_NAME` | Nazwa aplikacji będzie częścią nazwy hosta aplikacji internetowej po wdrożeniu na platformie Azure (APLIKACJA_INTERNETOWA.azurewebsites.net). Zaktualizuj tę wartość przy użyciu unikatowej nazwy nowej aplikacji internetowej platformy Azure, która będzie hostem aplikacji Java, na przykład *contoso*. |
| `REGION` | Region platformy Azure, w którym hostowana jest aplikacja internetowa, na przykład `westus2`. Listę regionów można uzyskać z usługi Cloud Shell lub interfejsu wiersza polecenia przy użyciu polecenia `az account list-locations`. |

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdróż aplikację Java na platformie Azure za pomocą następującego polecenia:

```bash
mvn package azure-webapp:deploy
```

Po zakończeniu wdrażania w przeglądarce internetowej przejdź do wdrożonej aplikacji, używając następującego adresu URL, na przykład `http://<webapp>.azurewebsites.net/helloworld`. 

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku Java w usłudze App Service w systemie Linux.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto narzędzia Maven do utworzenia aplikacji internetowej Java, skonfigurowano wtyczkę [Maven Plugin for Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), a następnie wdrożono aplikację Java w pakiecie archiwum internetowego do usługi App Service w systemie Linux. Aby dowiedzieć się, jak połączyć bazy danych, skonfigurować rejestrowanie i monitorowanie, skonfigurować zabezpieczenia oraz ustawić opcje środowiska uruchomieniowego, przejdź do przewodnik dla deweloperów języka Java dla usługi App Service w systemie Linux.

> [!div class="nextstepaction"]
> [Przewodnik dla deweloperów języka Java dla usługi App Service w systemie Linux](app-service-linux-java.md)

