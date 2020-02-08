---
title: Microsoft Identity platform Java Internet Web App — Szybki Start | Azure
description: Dowiedz się, jak zaimplementować logowanie firmy Microsoft w aplikacji sieci Web Java za pomocą OpenID Connect Connect
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: 3f5e8c76fcf5f6088698e785e3203ab65bf04de1
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084485"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Szybki Start: Dodawanie logowania do aplikacji sieci Web w języku Java przez firmę Microsoft

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

W tym przewodniku szybki start dowiesz się, jak zintegrować aplikację sieci Web Java z platformą tożsamości firmy Microsoft. Twoja aplikacja zaloguje użytkownika, uzyskaj token dostępu, aby wywołać interfejs API Microsoft Graph i przetworzyć żądanie do interfejsu API Microsoft Graph.

Po ukończeniu tego przewodnika Szybki Start aplikacja będzie akceptować logowania do osobistych kont Microsoft (w tym outlook.com, live.com i innych) oraz kont służbowych z dowolnej firmy lub organizacji korzystającej z Azure Active Directory.

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Wymagania wstępne

Do uruchomienia tego przykładu potrzebne są:

- [Zestaw Java Development Kit (JDK)](https://openjdk.java.net/) 8 lub nowszy oraz [Maven](https://maven.apache.org/).
- Dzierżawa usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat uzyskiwania dzierżawy usługi Azure AD, zobacz [jak uzyskać dzierżawę usługi Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-howto-tenant/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Dostępne są dwie opcje uruchomienia aplikacji szybkiego startu: Express (opcja 1) lub ręczne (opcja 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do [Rejestracje aplikacji Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> Aby zarejestrować aplikację i ręcznie dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
>
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](/azure/active-directory/develop/) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>    - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `java-webapp`.
>    - Pozostaw teraz pustą wartość **URI przekierowania** , a następnie wybierz pozycję **zarejestruj**.
> 1. Na stronie **Przegląd** Znajdź wartość **Identyfikator aplikacji (klienta)** i **Identyfikator katalogu (dzierżawcy)** aplikacji. Skopiuj te wartości później.
> 1. Wybierz **uwierzytelnianie** z menu, a następnie Dodaj następujące informacje:
>    - W obszarze **identyfikatory URI przekierowania**Dodaj `https://localhost:8080/msal4jsample/secure/aad` i `https://localhost:8080/msal4jsample/graph/me`.
>    - Wybierz pozycję **Zapisz**.
> 1. Wybierz pozycję **certyfikaty & wpisy tajne** z menu i w sekcji wpisy **tajne klienta** kliknij **nowy klucz tajny klienta**:
>
>    - Wpisz opis klucza (dla wpisu tajnego aplikacji wystąpienia).
>    - Wybierz czas trwania klucza **w 1 roku**.
>    - Wartość klucza zostanie wyświetlona po wybraniu opcji **Dodaj**.
>    - Skopiuj wartość klucza w późniejszym czasie. Ta wartość klucza nie będzie ponownie wyświetlana ani nie można jej pobrać z innych metod, dlatego Zapisz ją tak szybko, jak to będzie widoczne w Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
>
> Aby uzyskać przykładowy kod dla tego przewodnika Szybki Start, należy wykonać następujące czynności:
>
> 1. Dodaj adresy URL odpowiedzi jako `https://localhost:8080/msal4jsamples/secure/aad` i `https://localhost:8080/msal4jsamples/graph/me`.
> 1. Utwórz klucz tajny klienta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-aspnet-webapp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-code-sample"></a>Krok 2. Pobieranie przykładu kodu

 [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

#### <a name="step-3-configure-the-code-sample"></a>Krok 3. Konfigurowanie przykładu kodu

 1. Wyodrębnij plik zip do folderu lokalnego.
 1. Jeśli używasz zintegrowanego środowiska programistycznego, Otwórz przykład w ulubionym środowisku IDE (opcjonalnie).
 1. Otwórz plik Application. Properties, który znajduje się w folderze src/Main/sources/folder i Zastąp wartości pól *AAD. clientId*, *AAD. Authority* i *AAD. secretKey* odpowiednimi wartościami **identyfikatora aplikacji**, **identyfikatora dzierżawy** i **wpisu tajnego klienta** w następujący sposób:

    ```file
    aad.clientId=Enter_the_Application_Id_here
    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
    aad.secretKey=Enter_the_Client_Secret_Here
    aad.redirectUriSignin=https://localhost:8080/msal4jsample/secure/aad
    aad.redirectUriGraph=https://localhost:8080/msal4jsample/graph/me
    ```

    > [!div renderon="docs"]
    > Gdzie:
    >
    > - `Enter_the_Application_Id_here` jest identyfikatorem dla zarejestrowanej aplikacji.
    > - `Enter_the_Client_Secret_Here` — jest **kluczem tajnym klienta** utworzonym w **certyfikatach & wpisy tajne** dla zarejestrowanej aplikacji.
    > - `Enter_the_Tenant_Info_Here` — jest wartością **identyfikatora katalogu (dzierżawy)** zarejestrowanej aplikacji.

 1. Aby używać protokołu HTTPS z localhost, Wypełnij właściwości Server. SSL. Key. Aby wygenerować certyfikat z podpisem własnym, użyj narzędzia klucza (dołączonego do środowiska JRE).

   ```
   Example: 
   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password

   server.ssl.key-store-type=PKCS12  
   server.ssl.key-store=classpath:keystore.p12  
   server.ssl.key-store-password=password  
   server.ssl.key-alias=testCert 
   ```

   Umieść wygenerowany plik magazynu kluczy w folderze "Resources" (zasoby).

#### <a name="step-4-run-the-code-sample"></a>Krok 4. Uruchamianie przykładu kodu

Aby uruchomić projekt, można wykonać jedną z:

Uruchom ją bezpośrednio z poziomu środowiska IDE przy użyciu osadzonego serwera rozruchu sprężynowego lub spakuj go do pliku WAR przy użyciu [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) i Wdróż go w rozwiązaniu kontenera J2EE, takim jak [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Uruchamianie z IDE

Jeśli aplikacja sieci Web jest uruchamiana z poziomu środowiska IDE, kliknij pozycję Uruchom, a następnie przejdź do strony głównej projektu. W tym przykładzie adres URL standardowej strony głównej jest https://localhost:8080.

1. Na stronie frontonu wybierz przycisk **Zaloguj** , aby przekierować do Azure Active Directory i monitować użytkownika o ich poświadczenia.

1. Po uwierzytelnieniu użytkownik zostanie przekierowany do *https://localhost:8080/msal4jsample/secure/aad* . Są one teraz zalogowane, a na stronie zostaną wyświetlone informacje o zalogowanym koncie. Przykładowy interfejs użytkownika ma następujące przyciski:
    - *Wyloguj*: podpisuje bieżącego użytkownika poza aplikacją i przekierowuje je do strony głównej.
    - *Pokaż informacje o użytkowniku*: uzyskuje token dla Microsoft Graph i wywołuje Microsoft Graph z żądaniem zawierającym token, który zwraca podstawowe informacje o zalogowanym użytkowniku.

> [!IMPORTANT]
> Aplikacja w tym przewodniku Szybki start używa klucza tajnego klienta do identyfikowania się jako klienta poufnego. Ponieważ klucz tajny klienta jest dodawany jako zwykły tekst do plików projektu, ze względów bezpieczeństwa zaleca się użycie certyfikatu zamiast klucza tajnego klienta przed uwzględnieniem aplikacji jako aplikacji produkcyjnej. Aby uzyskać więcej informacji na temat korzystania z certyfikatu, zobacz [poświadczenia certyfikatu na potrzeby uwierzytelniania aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Więcej informacji

### <a name="getting-msal"></a>Pobieranie MSAL

MSAL for Java (MSAL4J) to biblioteka języka Java służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft.

Dodaj MSAL4J do aplikacji za pomocą Maven lub Gradle, aby zarządzać zależnościami, wprowadzając następujące zmiany w pliku pliku pom. XML (Maven) lub Build. Gradle (Gradle) aplikacji.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Dodaj odwołanie do MSAL for Java, dodając następujący kod na początku pliku, w którym będziesz używać MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat uprawnień i wyrażania zgody:

> [!div class="nextstepaction"]
> [Uprawnienia i wyrażanie zgody](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Aby dowiedzieć się więcej o przepływie uwierzytelniania dla tego scenariusza, zobacz przepływ kodu autoryzacji OAuth 2,0:

> [!div class="nextstepaction"]
> [Przepływ OAuth kodu autoryzacji](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
