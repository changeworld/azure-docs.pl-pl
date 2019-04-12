---
title: Łączenie usługi Azure Media Services v3 API — Java
description: Dowiedz się, jak nawiązać połączenie usługi Media Services v3 z interfejsem API za pomocą języka Java.
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
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 9177a1ae1f2939979d1f824c98b6018a83c2779f
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502266"
---
# <a name="connect-to-media-services-v3-api---java"></a>Łączenie z Media Services v3 API — Java

W tym artykule pokazano, jak połączyć się z zestawu SDK usługi Azure Media Services v3 Java przy użyciu znaku nazwy głównej usługi, w metodzie.

W tym artykule Visual Studio Code jest używany do tworzenia aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Postępuj zgodnie z [pisania Java za pomocą programu Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) do zainstalowania:

   - Zestaw JDK
   - Apache Maven
   - Pakiet rozszerzenia języka Java
- Upewnij się ustawić `JAVA_HOME` i `PATH` zmiennych środowiskowych.
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Należy pamiętać, nazwę grupy zasobów i nazwę konta usługi Media Services.
- Postępuj zgodnie z instrukcjami w [dostęp do interfejsów API](access-api-cli-how-to.md) tematu. Zapisz identyfikator subskrypcji, identyfikator aplikacji (identyfikator klienta), klucz uwierzytelniania (klucz tajny) i identyfikator dzierżawy, które są potrzebne w kolejnym kroku.

Sprawdź również:

- [Język Java w programie Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Zarządzanie projektami środowiska Java w programie VS Code](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>Utwórz projekt narzędzia Maven

Otwórz narzędzie wiersza polecenia i `cd` do katalogu, w którym chcesz utworzyć projekt.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Po uruchomieniu polecenia, `pom.xml`, `App.java`, i inne pliki są tworzone. 

## <a name="add-dependencies"></a>Dodaj zależności

1. W programie Visual Studio Code Otwórz folder, w której projekt jest. 
1. Znajdowanie i otwieranie `pom.xml`. 
1. Dodaj wymagane zależności. Jeden z nich jest [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar).

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
      <artifactId>azure-mgmt-media</artifactId>
      <version>1.0.0-beta</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.rest</groupId>
      <artifactId>client-runtime</artifactId>
      <version>1.6.5</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-client-authentication</artifactId>
      <version>1.6.5</version>
    </dependency>
    </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Nawiązywać połączenia z klientem języka Java

1. Otwórz `App.java` plik `src\main\java\com\azure\ams` i upewnij się, że pakiet jest uwzględniane w górnej:

    ```java
    package com.azure.ams;
    ```
2. W obszarze instrukcji pakietu, dodaj je zaimportować instrukcji:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
2. Aby utworzyć poświadczenia usługi Active Directory, które należy wprowadzić żądań, Dodaj następujący kod do głównej metody klasy aplikacji i ustaw wartości, które masz od [dostęp do interfejsów API](access-api-cli-how-to.md):
   
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

## <a name="see-also"></a>Zobacz także

- [Pojęcia dotyczące usługi Media Services](concepts-overview.md)
- [Zestaw SDK Java](https://aka.ms/ams-v3-java-sdk)
- [Dokumentacja języka Java](https://aka.ms/ams-v3-java-ref)
- [https://search.maven.org/](https://search.maven.org/)

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz uwzględnić `import com.microsoft.azure.management.mediaservices.v2018_07_01.Asset;` i rozpocząć manipulowanie jednostek.<br/>
Na przykład pobrać wszystkie zasoby w ramach Twojego konta: `Observable<Asset> asyncAssets = 
                    manager.assets().listAsync(groupId, accountId).last();`
