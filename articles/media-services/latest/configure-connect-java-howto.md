---
title: Łączenie się z interfejsem API usługi Azure Media Services w wersji 3 — Java
description: W tym artykule opisano sposób łączenia się z interfejsem API usługi Azure Media Services w wersji 3 w języku Java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888499"
---
# <a name="connect-to-media-services-v3-api---java"></a>Łączenie się z interfejsem API usługi Media Services w wersji 3 — Java

W tym artykule pokazano, jak połączyć się z zestawem Java Azure Services w wersji 3 przy użyciu metody logowania jednostki usługi.

W tym artykule visual studio kod jest używany do tworzenia przykładowej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Aby zainstalować, wykonaj [zapis w języku Java za pomocą programu Visual Studio Code:](https://code.visualstudio.com/docs/java/java-tutorial)

   - Zestaw JDK.
   - Apache Maven
   - Pakiet rozszerzeń Java
- Upewnij się, `JAVA_HOME` `PATH` że ustawisz zmienne środowiskowe.
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta usługi Media Services.
- Wykonaj kroki opisane w temacie [Interfejsy API programu Access.](access-api-cli-how-to.md) Zarejestruj identyfikator subskrypcji, identyfikator aplikacji (identyfikator klienta), klucz uwierzytelniania (klucz tajny) i identyfikator dzierżawy, który jest potrzebny w późniejszym kroku.

Również przegląd:

- [Java w programie Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Zarządzanie projektami Java w programie VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Przejrzyj [konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Tworzenie projektu Maven

Otwórz narzędzie wiersza `cd` polecenia i katalog, w którym chcesz utworzyć projekt.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Po uruchomieniu polecenia tworzone `pom.xml` `App.java`są pliki , i inne pliki. 

## <a name="add-dependencies"></a>Dodawanie zależności

1. W programie Visual Studio Code otwórz folder, w którym znajduje się projekt
1. Znajdź i otwórz`pom.xml`
1. Dodawanie potrzebnych zależności

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Łączenie się z klientem Java

1. Otwórz `App.java` plik `src\main\java\com\azure\ams` w obszarze i upewnij się, że pakiet jest dołączony u góry:

    ```java
    package com.azure.ams;
    ```
1. W instrukcji pakietu dodaj następujące instrukcje importu:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Aby utworzyć poświadczenia usługi Active Directory, które należy wykonać żądania, dodaj następujący kod do głównej metody klasy Aplikacji i ustaw wartości otrzymane z [interfejsów API programu Access:](access-api-cli-how-to.md)
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Uruchom aplikację.

## <a name="see-also"></a>Zobacz też

- [Pojęcia dotyczące usług multimedialnych](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Dokumentacja języka Java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Następne kroki

Teraz można `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` dołączyć i rozpocząć manipulowanie encjami.

Aby uzyskać więcej przykładów kodu, zobacz [java SDK przykłady repozytorium.](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/)
