---
title: Azure AD Java wiersza polecenia wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację wiersza polecenia języka Java, która loguje użytkowników można uzyskać dostępu do interfejsu API.
services: active-directory
documentationcenter: java
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 51e1a8f9-6ff0-4643-a350-0ba794e26fd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/23/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: a0e12711e4a7e67861d61ae4575c4956531cf841
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Dostęp do interfejsu API za pomocą usługi Azure AD za pomocą aplikacji Java wiersza polecenia
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Usługi Azure AD umożliwia proste i bezpośrednie do zewnętrznej obsługi zarządzania tożsamościami aplikacji sieci web, zapewniając jednego logowania i wylogowywania przy tylko kilku wierszy kodu.  W aplikacjach sieci web Java można to zrobić za pomocą implementacji firmy Microsoft ADAL4J społeczność.

  W tym miejscu użyjemy ADAL4J do:

* Zaloguj się użytkownika do aplikacji przy użyciu usługi Azure AD jako dostawcy tożsamości.
* Wyświetlane informacje o użytkowniku.
* Zaloguj użytkownika poza aplikacją.

W tym celu należy:

1. Rejestrowanie aplikacji w usłudze Azure AD
2. Konfigurowanie aplikacji do korzystania z biblioteki ADAL4J.
3. Biblioteka ADAL4J służy do wysyłania żądań zalogowania się i wylogowania do usługi Azure AD.
4. Wydrukować dane o użytkowniku.

Aby rozpocząć, [pobrać szkielet aplikacji](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) lub [Pobieranie ukończone próbki](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\\/archive/complete.zip).  Należy również dzierżawa usługi Azure AD, w którym można zarejestrować aplikacji.  Jeśli nie masz już, [Dowiedz się, jak kupić](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1.  Rejestrowanie aplikacji w usłudze Azure AD
Aby włączyć swoją aplikację w celu uwierzytelniania użytkowników, musisz najpierw zarejestrować nową aplikację w dzierżawie.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku, kliknij na Twoim koncie i w obszarze **katalogu** wybierz dzierżawy usługi Active Directory, w którym chcesz zarejestrować aplikację.
3. Polecenie **wszystkie usługi** w nawigacji po lewej stronie i wybierz polecenie **usługi Azure Active Directory**.
4. Polecenie **rejestracji aplikacji** i wybierz polecenie **Dodaj**.
5. Postępuj zgodnie z monitami i utworzyć nową **aplikacji sieci Web i/lub WebAPI**.
  * **Nazwa** aplikacji będzie opisywać aplikację dla użytkowników końcowych
  * **Adres URL logowania** jest podstawowy adres URL aplikacji.  Domyślnie szkielet `http://localhost:8080/adal4jsample/`.
6. Po zakończeniu rejestracji usługi AAD przypisze aplikacji Unikatowy identyfikator aplikacji.  Ta wartość jest potrzebny w kolejnych sekcjach, dlatego skopiuj go na karcie aplikacji.
7. Z **ustawienia** -> **właściwości** strony dla aplikacji, zaktualizuj identyfikator URI aplikacji. **Identyfikator URI aplikacji** to unikatowy identyfikator aplikacji.  Konwencji jest użycie `https://<tenant-domain>/<app-name>`, np. `http://localhost:8080/adal4jsample/`.

Raz w portalu dla aplikacji należy utworzyć **klucza** z **ustawienia** strony dla aplikacji i skopiuj go.  Będziesz potrzebować go za chwilę.

## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. Konfigurowanie aplikacji do użycia biblioteki ADAL4J i wymagania wstępne za pomocą programu Maven
W tym miejscu skonfigurujemy ADAL4J do używania protokołu uwierzytelniania OpenID Connect.  ADAL4J będzie służyć do wysyłania żądań zalogowania się i wylogowania, zarządzania sesji użytkownika i uzyskać informacje o użytkowniku, między innymi.

* W katalogu głównym projektu otworzyć/utworzyć `pom.xml` i Znajdź `// TODO: provide dependencies for Maven` i zastąp go następującym łańcuchem:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. Utwórz plik Java PublicClient
Jak wspomniano powyżej, firma Microsoft będzie przy użyciu interfejsu API programu Graph uzyskać dane o zalogowanego użytkownika. Dla tego celu ułatwienia firmie Microsoft możemy utworzyć zarówno plik, do reprezentowania **obiektu katalogu** i pojedynczego pliku do reprezentowania **użytkownika** tak, aby mógł być używany wzorzec OO Java.

* Utwórz plik o nazwie `DirectoryObject.java` użyjemy do przechowywania danych podstawowych o wszelkich DirectoryObject (możesz później użycia innych zapytań wykresu, możesz to zrobić). Użytkownik może wycinania/wklejania to poniżej:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


## <a name="compile-and-run-the-sample"></a>Kompilowanie i uruchamianie próbki
Zmień ponownie się do katalogu głównego i uruchom następujące polecenie, aby utworzyć przykład możesz umieścić tylko ze sobą przy użyciu `maven`. Zostaną użyte `pom.xml` pliku zapisano zależności.

`$ mvn package`

Po wykonaniu `adal4jsample.war` plików w sieci `/targets` katalogu. Można wdrożyć który w Twojej kontenera Tomcat i odwiedź adres URL 

`http://localhost:8080/adal4jsample/`

> [!NOTE]
> Jest bardzo łatwa do wdrożenia WAR z najnowsze serwery Tomcat. Po prostu przejdź do `http://localhost:8080/manager/` i postępuj zgodnie z instrukcjami na przekazywanie Twojej `adal4jsample.war` pliku. Autodeploy go zostanie automatycznie z właściwego punktu końcowego.
> 
> 

## <a name="next-steps"></a>Następne kroki
Gratulacje! Masz teraz pracy aplikacji Java, która umożliwia uwierzytelnianie użytkowników w bezpieczny sposób wywoływania interfejsów API sieci Web przy użyciu protokołu OAuth 2.0 i uzyskać podstawowe informacje o użytkowniku.  Jeśli nie jest jeszcze nadszedł czas do wypełnienia dzierżawy z niektórych użytkowników.

Próbka ukończone (bez wartości konfiguracji) [jest dostarczane jako zip w tym miejscu](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), lub można ją sklonować z serwisu GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

