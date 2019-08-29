---
title: Microsoft Identity platform Java Internet Web App — Szybki Start | Azure
description: Dowiedz się, jak zaimplementować logowanie firmy Microsoft w aplikacji sieci Web Java za pomocą OpenID Connect Connect
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/11/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: 549b4457ee38504001e83c4b831cd321e1542068
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125475"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Szybki start: Dodawanie logowania do aplikacji sieci Web w języku Java firmy Microsoft

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

W tym przewodniku szybki start dowiesz się, jak zintegrować aplikację sieci Web Java z platformą tożsamości firmy Microsoft. Twoja aplikacja zaloguje użytkownika, uzyskaj token dostępu, aby wywołać interfejs API Microsoft Graph i przetworzyć żądanie do interfejsu API Microsoft Graph. 

Po ukończeniu tego przewodnika aplikacja będzie akceptować logowania do osobistych kont Microsoft (w tym outlook.com, live.com i innych) oraz kont służbowych z dowolnej firmy lub organizacji korzystającej z Azure Active Directory.

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-java-webapp/java-quickstart.svg)

> ## <a name="prerequisites"></a>Wymagania wstępne
> Do uruchomienia tego przykładu potrzebne są: 
> - Zestaw Java Development Kit (JDK) 8 lub nowszy i Maven.
> - Dzierżawa usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat uzyskiwania dzierżawy usługi Azure AD, zobacz [jak uzyskać dzierżawę usługi Azure AD.](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-create-new-tenant)
>
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * Utwórz [Opcja 1: Zarejestruj i automatycznie Skonfiguruj aplikację, a następnie Pobierz przykład kodu.](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Ręcznie: [Opcja 2: Zarejestruj i ręcznie skonfiguruj aplikację oraz przykład kodu.](#option-2-register-and-manually-configure-your-application-and-code-sample)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do [Rejestracje aplikacji Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
> 
>
> #### <a name="step-1-download-the-code-sample"></a>Krok 1: Pobierz przykład kodu
> 
> - [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
>
> #### <a name="step-2-open-applicationproperties"></a>Krok 2: Otwórz aplikację. właściwości
>
> 1. Wyodrębnij plik zip do folderu lokalnego.
> 1. Obowiązkowe Jeśli używasz zintegrowanego środowiska programistycznego, Otwórz przykład w ulubionym środowisku IDE.
> 1. Otwórz plik *Application. Properties* . W następnym kroku zostaną wstawione `aad.clientId`wartości `aad.authority`dla, `aad.secretKey` i podczas rejestrowania aplikacji.


> #### <a name="step-3-register-your-application"></a>Krok 3: Zarejestruj swoją aplikację
> Aby zarejestrować aplikację i ręcznie dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>    - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `java-webapp`.
>    - Pozostaw teraz pustą wartość **URI przekierowania** , a następnie wybierz pozycję **zarejestruj**.
> 1. Znajdź wartość **identyfikatora aplikacji (klienta)** aplikacji. Zaktualizuj wartość `Enter_the_Application_Id_here` w pliku *Application. Properties* .
> 1. Znajdź wartość **identyfikatora katalogu (dzierżawy)** aplikacji. Zaktualizuj wartość `Enter_the_Tenant_Info_Here` w pliku *Application. Properties* . 
> 1. Wybierz menu **Uwierzytelnianie**, a następnie dodaj następujące informacje:
>    - W obszarze **identyfikatory URI**przekierowania `https://localhost:8080/msal4jsamples/graph/users`Dodaj `http://localhost:8080/msal4jsamples/secure/aad` i.
>    - Wybierz pozycję **Zapisz**.
> 1. W menu po lewej stronie wybierz pozycję **certyfikaty &** wpisy tajne i kliknij pozycję **Nowy wpis tajny klienta** w sekcji wpisy **tajne klienta** :
>     
>    - Wpisz opis klucza (dla wpisu tajnego aplikacji wystąpienia).
>    - Wybierz kluczowy czas trwania wynoszący **1 rok**.
>    - Po kliknięciu przycisku **Dodaj**zostanie wyświetlona wartość klucza. 
>    - Skopiuj wartość klucza. Otwórz pobrany wcześniej plik *Application. Properties* i zaktualizuj wartość `Enter_the_Client_Secret_Here` przy użyciu wartości klucza. 
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurowanie aplikacji w witrynie Azure Portal
> Aby uzyskać przykładowy kod dla tego przewodnika Szybki Start, należy wykonać następujące czynności:
> 1. Dodaj adresy URL odpowiedzi `http://localhost:8080/msal4jsamples/secure/aad` jako `https://localhost:8080/msal4jsamples/graph/users`i.
> 1. Utwórz klucz tajny klienta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-aspnet-webapp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.
> 
> #### <a name="step-2-download-the-code-sample"></a>Krok 2: Pobierz przykład kodu
> 
> - [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> #### <a name="step-3-configure-the-code-sample"></a>Krok 3: Konfigurowanie przykładu kodu 
> 
> 1. Wyodrębnij plik zip do folderu lokalnego.
> 1. Jeśli używasz zintegrowanego środowiska programistycznego, Otwórz przykład w ulubionym środowisku IDE (opcjonalnie).
> 1. Otwórz plik **Application. Properties** , który można znaleźć w temacie *src/Main/Resources/* .
> 1. Zamień właściwości aplikacji.
>   1. Znajdź `aad.clientId` i zaktualizuj `Enter_the_Application_Id_here` wartość przy użyciu wartości **identyfikatora aplikacji (klienta)** zarejestrowanej aplikacji. 
>   1. Znajdź `aad.authority` i zaktualizuj `Enter_the_Tenant_Name_Here` wartość z wartością **identyfikatora katalogu (dzierżawa)** zarejestrowanej aplikacji.
>   1. Znajdź `aad.secretKey` i zaktualizuj `Enter_the_Client_Secret_Here` wartość przy użyciu **klucza tajnego klienta** utworzonego w obszarze **Certyfikaty &** wpisy tajne dla zarejestrowanej aplikacji.

#### <a name="step-4-run-the-code-sample"></a>Krok 4: Uruchamianie przykładu kodu
1. Uruchom przykładowy kod, a następnie otwórz przeglądarkę i przejdź do *http://localhost:8080* .
1. Strona frontonu zawiera przycisk **logowania** . Kliknij przycisk **Zaloguj** , aby przekierować do Azure Active Directory. Użytkownik zostanie poproszony o podanie swoich poświadczeń.  
1. Po pomyślnym uwierzytelnieniu w Azure Active Directory nastąpi przekierowanie *http://localhost:8080/msal4jsamples/secure/aad* do. Są one oficjalnie zalogowane do aplikacji, a strona powinna zawierać informacje dotyczące konta zalogowanego. Zawiera również przyciski dla: 
    - *Wyloguj się*: Program wyloguje bieżącego użytkownika z aplikacji i przekieruje je do strony głównej.
    - *Pokaż użytkowników*: Uzyska token dla Microsoft Graph, a następnie Wywołaj Microsoft Graph z tokenem dołączonym do żądania, aby uzyskać wszystkich użytkowników w dzierżawie.


## <a name="more-information"></a>Więcej informacji

### <a name="getting-msal"></a>Pobieranie MSAL
MSAL4J to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Możesz dodać MSAL4J do aplikacji za pomocą Maven lub Gradle, aby zarządzać zależnościami, wprowadzając następujące zmiany w pliku pliku pom. XML lub Build. Gradle w aplikacji. 

```
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>Inicjalizacja Msal
Można dodać odwołanie do MSAL4J, dodając następujący kod na początku pliku, w którym będzie używany MSAL4J: 

```
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat uprawnień i wyrażania zgody:

> [!div class="nextstepaction"]
> [Uprawnienia i wyrażanie zgody](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-permissions-and-consent)

Aby dowiedzieć się więcej o przepływie uwierzytelniania dla tego scenariusza, zobacz przepływ kodu autoryzacji OAuth 2,0:

> [!div class="nextstepaction"]
> [Przepływ OAuth kodu autoryzacji](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
