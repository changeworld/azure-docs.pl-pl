---
title: Przerzucanie klucza podpisywania w usłudze Azure AD
description: W tym artykule omówiono najlepsze rozwiązania przerzucania klucza podpisywania usługi Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2018
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f809fa856d39096a85dcc205d8211ba3551eeb48
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962858"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Przerzucanie klucza w usłudze Azure Active Directory podpisywania
W tym artykule opisano, co musisz wiedzieć o publiczne klucze, które są używane w usłudze Azure Active Directory (Azure AD) do podpisywania tokenów zabezpieczających. Należy pamiętać, że te klucze przechodzą w regularnych odstępach czasu i w takiej sytuacji, może być przenoszone natychmiast. Wszystkie aplikacje, które używają usługi Azure AD powinien móc programowo obsługuje procesu przerzucania klucza lub ustanowienia okresowe ręczne Przerzucanie procesu. Kontynuuj czytanie, aby zrozumieć, jak działają klawisze jak ocenić wpływ przerzucania do aplikacji i jak zaktualizować aplikację lub ustanowienia procesu okresowe ręczne Przerzucanie do obsługi Przerzucanie klucza, jeśli to konieczne.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Omówienie kluczy podpisywania w usłudze Azure AD
Usługa Azure AD używa kryptografii klucza publicznego, w oparciu o standardy branżowe, aby ustanowić zaufanie między sobą i aplikacje, które go używają. W praktyce to działa w następujący sposób: Usługa Azure AD używa klucza podpisywania, który składa się z pary kluczy publicznych i prywatnych. Po zalogowaniu się użytkownika do aplikacji, która używa usługi Azure AD do uwierzytelniania usługi Azure AD tworzy token zabezpieczający, który zawiera informacje o użytkowniku. Ten token jest podpisany przez usługę Azure AD za pomocą jego klucza prywatnego, przed wysłaniem go do aplikacji. Aby sprawdzić, czy token jest prawidłowy i pochodzącej z usługi Azure AD, aplikacja musi go zweryfikować podpisu tokenu przy użyciu klucza publicznego, udostępniane przez usługę Azure AD, który znajduje się w ramach dzierżawy [dokument odnajdywania protokołu OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html) lub SAML / WS-Fed [dokument metadanych Federacji](azure-ad-federation-metadata.md).

Ze względów bezpieczeństwa usługa Azure AD podpisywania kluczy ustala w regularnych odstępach czasu, a w przypadku sytuacji awaryjnej można jest przenoszone natychmiast. Każda aplikacja, która integruje się z usługą Azure AD powinna być przygotowana do obsługi zdarzenia Przerzucanie klucza, niezależnie od tego, jak często występuje. Jeśli nie, a aplikacja podejmują próbę użycia wygasłych klucza można zweryfikować podpisu tokenu, żądanie logowania nie powiedzie się.

Istnieje więcej niż jeden prawidłowy klucz dostępnych w dokumencie odnajdywania protokołu OpenID Connect i dokumentu metadanych federacji. Aplikacja powinna być przygotowane użyć dowolnego z określonych w dokumencie kluczy, ponieważ jeden z kluczy mogła zostać wycofana wkrótce, inny może być zamiennikach i tak dalej.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Jak ocenić, czy aplikacja będzie mieć wpływ na dodanie i co należy zrobić
Jak aplikacja obsługuje przerzucania klucza jest zależna od zmiennych, takich jak typ aplikacji lub użyto jakiego protokołu tożsamości i biblioteki. W poniższych sekcjach ocenić, czy najbardziej typowych aplikacji ma wpływ przerzucania klucza i wytyczne dotyczące sposobu aktualizowania aplikacji do obsługi automatycznego przerzucania lub ręcznie zaktualizować klucza.

* [Natywne aplikacje klienckie uzyskiwania dostępu do zasobów](#nativeclient)
* [Aplikacje sieci Web / interfejsów API, uzyskiwaniu dostępu do zasobów](#webclient)
* [Aplikacje sieci Web / interfejsów API chroni zasoby oraz utworzone przy użyciu usługi Azure App Services](#appservices)
* [Aplikacje sieci Web / ochrona zasobów przy użyciu programu .NET OWIN OpenID Connect, WS-Fed i oprogramowania pośredniczącego WindowsAzureActiveDirectoryBearerAuthentication interfejsów API](#owin)
* [Aplikacje sieci Web / interfejsów API, ochrona zasobów przy użyciu platformy .NET Core OpenID Connect lub JwtBearerAuthentication oprogramowania pośredniczącego](#owincore)
* [Aplikacje sieci Web / interfejsów API, ochrona zasobów przy użyciu modułu passport-azure-ad środowiska Node.js](#passport)
* [Aplikacje sieci Web / API ochrona zasobów i utworzony za pomocą programu Visual Studio 2015 lub nowszym](#vs2015)
* [Aplikacje sieci Web chroni zasoby oraz utworzone przy użyciu programu Visual Studio 2013](#vs2013)
* Interfejsy API sieci Web chroni zasoby oraz utworzone przy użyciu programu Visual Studio 2013
* [Aplikacje sieci Web chroni zasoby oraz utworzone przy użyciu programu Visual Studio 2012](#vs2012)
* [Aplikacje sieci Web chroni zasoby oraz utworzone przy użyciu programu Visual Studio 2010, o 2008 za pomocą programu Windows Identity Foundation](#vs2010)
* [Aplikacje sieci Web / interfejsów API, ochrona zasobów przy użyciu innych bibliotek lub ręcznego wdrażania każdego z obsługiwanych protokołów](#other)

Ten przewodnik jest **nie** dotyczy:

* Aplikacje dodane z aplikacji galerii usługi Azure AD (w tym niestandardowych) mają oddzielne wytyczne w odniesieniu do kluczy podpisywania. [Więcej informacji.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Lokalne aplikacje opublikowane za pośrednictwem serwera proxy aplikacji nie musisz martwić się o kluczy podpisywania.

### <a name="nativeclient"></a>Natywne aplikacje klienckie uzyskiwania dostępu do zasobów
Aplikacje, które uzyskują dostęp tylko do zasobów (tj.) Program Microsoft Graph, magazynu kluczy, interfejsu API programu Outlook i innych APIs firmy Microsoft) jest ogólnie tylko uzyskania tokenu i przekazywanie ich wzdłuż do właściciela zasobu. Biorąc pod uwagę, że nie jest chroniony wszelkie zasoby, nie kontrolują token i dlatego nie trzeba upewnić się, że jest prawidłowo podpisana.

Natywne aplikacje klienckie, czy komputerze lub urządzeniu przenośnym, należą do tej kategorii, a więc nie ma wpływ przerzucania.

### <a name="webclient"></a>Aplikacje sieci Web / interfejsów API, uzyskiwaniu dostępu do zasobów
Aplikacje, które uzyskują dostęp tylko do zasobów (tj.) Program Microsoft Graph, magazynu kluczy, interfejsu API programu Outlook i innych APIs firmy Microsoft) jest ogólnie tylko uzyskania tokenu i przekazywanie ich wzdłuż do właściciela zasobu. Biorąc pod uwagę, że nie jest chroniony wszelkie zasoby, nie kontrolują token i dlatego nie trzeba upewnić się, że jest prawidłowo podpisana.

Aplikacje sieci Web i internetowych interfejsów API, który jest używany tylko do aplikacji flow (poświadczeń klienta / certyfikatu klienta), należą do tej kategorii i w związku z tym nie ma wpływ przerzucania.

### <a name="appservices"></a>Aplikacje sieci Web / interfejsów API chroni zasoby oraz utworzone przy użyciu usługi Azure App Services
Usługi Azure App Services uwierzytelniania / autoryzacji (EasyAuth) funkcji ma już logikę potrzebną do obsługi automatycznego przerzucania klucza.

### <a name="owin"></a>Aplikacje sieci Web / ochrona zasobów przy użyciu programu .NET OWIN OpenID Connect, WS-Fed i oprogramowania pośredniczącego WindowsAzureActiveDirectoryBearerAuthentication interfejsów API
Jeśli aplikacja wykorzystuje .NET OWIN OpenID Connect, WS-Fed lub WindowsAzureActiveDirectoryBearerAuthentication oprogramowanie pośredniczące, już logikę potrzebną do obsługi automatycznego przerzucania klucza.

Możesz potwierdzić, że aplikacja korzysta z któregoś z powyższych, wyszukując dowolne poniższe fragmenty kodu w pliku Startup.cs lub Startup.Auth.cs usługi aplikacji

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Aplikacje sieci Web / interfejsów API, ochrona zasobów przy użyciu platformy .NET Core OpenID Connect lub JwtBearerAuthentication oprogramowania pośredniczącego
Jeśli aplikacja korzysta z platformy .NET Core OWIN OpenID Connect lub JwtBearerAuthentication oprogramowania pośredniczącego, już logikę potrzebną do obsługi automatycznego przerzucania klucza.

Możesz potwierdzić, że aplikacja korzysta z któregoś z powyższych, wyszukując dowolne poniższe fragmenty kodu w pliku Startup.cs lub Startup.Auth.cs usługi aplikacji

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="passport"></a>Aplikacje sieci Web / interfejsów API, ochrona zasobów przy użyciu modułu passport-azure-ad środowiska Node.js
Jeśli aplikacja używa modułu passport-ad Node.js, już logikę potrzebną do obsługi automatycznego przerzucania klucza.

Potwierdzić, że aplikacja usługi passport-ad, wyszukując poniższy fragment kodu w aplikacji app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Aplikacje sieci Web / API ochrona zasobów i utworzony za pomocą programu Visual Studio 2015 lub nowszym
Jeśli aplikacja została skompilowana przy użyciu szablonu aplikacji sieci web w programie Visual Studio 2015 lub nowszego, a następnie wybrania **lub kont służbowych** z **Zmień uwierzytelnianie** menu jest już niezbędne Logika obsługi automatycznego przerzucania klucza. Tę logikę osadzone w pośredniczącym OWIN OpenID Connect, pobiera i umieszcza w pamięci podręcznej kluczy z dokumentu odnajdywania protokołu OpenID Connect i okresowo odświeża je.

Jeśli ręcznie dodano uwierzytelniania do rozwiązania, aplikacja może nie mieć logiki potrzeby przerzucania klucza. Musisz napisać samodzielnie lub postępuj zgodnie z instrukcjami w [aplikacji sieci Web / interfejsów API przy użyciu innych bibliotek lub ręcznego wdrażania każdego z obsługiwanych protokołów](#other).

### <a name="vs2013"></a>Aplikacje sieci Web chroni zasoby oraz utworzone przy użyciu programu Visual Studio 2013
Jeśli aplikacja została skompilowana przy użyciu szablonu aplikacji sieci web w programie Visual Studio 2013, a następnie wybrania **kont organizacyjnych** z **Zmień uwierzytelnianie** menu jest już logikę potrzebną do obsługi automatycznego przerzucania klucza. Tę logikę przechowuje unikatowy identyfikator w Twojej organizacji i dane klucza podpisywania w dwóch tabelach bazy danych skojarzony z projektem. Parametry połączenia dla bazy danych można znaleźć w pliku Web.config projektu.

Jeśli ręcznie dodano uwierzytelniania do rozwiązania, aplikacja może nie mieć logiki potrzeby przerzucania klucza. Musisz napisać samodzielnie lub postępuj zgodnie z instrukcjami w [aplikacji sieci Web / interfejsów API przy użyciu innych bibliotek lub ręcznego wdrażania każdego z obsługiwanych protokołów.](#other).

Poniższe kroki pomogą możesz sprawdzić, czy logika działa prawidłowo w aplikacji.

1. W programie Visual Studio 2013, otwórz rozwiązanie, a następnie kliknij pozycję **Eksploratora serwera** karty w oknie po prawej stronie.
2. Rozwiń **połączeń danych**, **DefaultConnection**, a następnie **tabel**. Znajdź **IssuingAuthorityKeys** tabelę, kliknij go prawym przyciskiem myszy, a następnie kliknij **Pokaż dane tabeli**.
3. W **IssuingAuthorityKeys** tabeli, będzie istnieć co najmniej jeden wiersz, który odpowiada wartość odcisku palca dla klucza. Usuń wszystkie wiersze w tabeli.
4. Kliknij prawym przyciskiem myszy **dzierżaw** tabeli, a następnie kliknij przycisk **Pokaż dane tabeli**.
5. W **dzierżaw** tabeli, będzie istnieć co najmniej jeden wiersz, który odpowiada identyfikatorowi dzierżawcy unikatowego katalogu. Usuń wszystkie wiersze w tabeli. Jeśli nie usuwaj wiersze w obu **dzierżaw** tabeli i **IssuingAuthorityKeys** tabeli, otrzymasz błąd w czasie wykonywania.
6. Skompiluj i uruchom aplikację. Po zalogowaniu się do swojego konta, można zatrzymać aplikacji.
7. Wróć do **Eksploratora serwera** i spójrz na wartości w **IssuingAuthorityKeys** i **dzierżaw** tabeli. Można zauważyć, że mają one automatycznie wypełniona przy użyciu odpowiednich informacji z dokumentu metadanych federacji.

### <a name="vs2013"></a>Interfejsy API sieci Web chroni zasoby oraz utworzone przy użyciu programu Visual Studio 2013
Jeśli utworzona aplikacja interfejsu API sieci web w programie Visual Studio 2013 za pomocą szablonu interfejsu API sieci Web, a następnie wybrany **kont organizacyjnych** z **Zmień uwierzytelnianie** menu, możesz już mieć niezbędne Logika w aplikacji.

Jeśli ręcznie skonfigurowano uwierzytelnianie, postępuj zgodnie z poniższymi instrukcjami, aby dowiedzieć się, jak skonfigurować interfejs API sieci Web można automatycznie zaktualizować swoje informacje o kluczu.

Poniższy fragment kodu pokazuje, jak pobrać najnowsze kluczy z dokumentu metadanych Federacji, a następnie użyj [rozszerzenie JWT Token Handler](https://msdn.microsoft.com/library/dn205065.aspx) można zweryfikować tokenu. Fragment kodu zakłada, że użytkownik użyje własny mechanizm buforowania przechowywanie klucza do weryfikacji przyszłości tokenów z usługi Azure AD, czy jest to, że w bazie danych, pliku konfiguracji lub gdzie indziej.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Aplikacje sieci Web chroni zasoby oraz utworzone przy użyciu programu Visual Studio 2012
Jeśli aplikacja została skompilowana w programie Visual Studio 2012, prawdopodobnie używane narzędzie tożsamość i dostęp do skonfigurowania aplikacji. Jest również prawdopodobieństwo, że używasz [sprawdzania poprawności wystawcy Name Registry (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). Rozszerzenie VINR jest odpowiedzialna za informacje o zaufanych dostawców tożsamości (Azure AD) oraz klucze używane do weryfikacji tokenów wystawionych przez nich. Rozszerzenie VINR ułatwia także do automatycznego aktualizowania kluczowe informacje, które są przechowywane w pliku Web.config, pobierając najnowsze dokumentu metadanych Federacji, skojarzone z katalogiem, sprawdzania, czy konfiguracja jest nieaktualna w najnowszych dokumentów i aktualizowanie aplikacji w celu używania nowego klucza, zgodnie z potrzebami.

Jeśli utworzono aplikację przy użyciu dowolnej z przykładów kodu lub dokumentacja przewodnika obsługiwane przez firmę Microsoft, logiki przerzucania klucza znajduje się już w projekcie. Zauważysz, że poniższy kod już istnieje w projekcie. Jeśli aplikacja nie ma już tę logikę, wykonaj następujące czynności, aby dodać go i sprawdź, czy działa poprawnie.

1. W **Eksploratora rozwiązań**, Dodaj odwołanie do **System.IdentityModel** zestawu dla odpowiedniego projektu.
2. Otwórz **Global.asax.cs** pliku i dodaj następujące dyrektywy using:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Dodaj następującą metodę do **Global.asax.cs** pliku:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Wywoływanie **RefreshValidationSettings()** method in Class metoda **Application_Start()** method in Class metoda **Global.asax.cs** jak pokazano:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Po wykonaniu tych kroków pliku Web.config aplikacji zostaną zaktualizowane przy użyciu najnowszych informacji z dokumentu metadanych Federacji, w tym najnowsze kluczy. Ta aktualizacja zostanie przeprowadzona za każdym razem, gdy pula aplikacji jest odtwarzana w usługach IIS; Domyślnie usługi IIS ustawiono odtwarzanie aplikacji co 29 godziny.

Wykonaj poniższe kroki, aby sprawdzić, czy działa logiki przerzucania klucza.

1. Po upewnieniu się, że aplikacja używa powyższy kod, otwórz **Web.config** plików i przejdź do  **\<issuerNameRegistry >** bloku, w szczególności wyszukiwanie Po kilku wierszy:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. W  **\<dodać odcisk palca = "" >** Zmień wartość odcisku palca, zastępując inny dowolny znak. Zapisz **Web.config** pliku.
3. Kompiluj aplikację, a następnie uruchom go. Jeśli można ukończyć procesu logowania, aplikacja pomyślnie aktualizuje klucz pobierając wymaganych informacji z dokumentu metadanych federacji w Twoim katalogu. Jeśli występują problemy z logowaniem, upewnij się, zmiany w aplikacji są poprawne, czytając [Dodawanie logowania jednokrotnego do usługi sieci Web Application Using usługi Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) artykułu, lub pobierania i zapoznanie się poniższy przykładowy kod: [Aplikacja w chmurze wielu dzierżaw usługi Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Windows Identity Foundation (WIF) w wersji 1.0 programu .NET 3.5 i aplikacji sieci Web chroni zasoby oraz utworzone przy użyciu programu Visual Studio 2008 lub 2010
Jeśli utworzono aplikację w wersji 1.0 programu WIF, nie ma podanego mechanizmu będzie automatycznie odświeżać konfigurację aplikacji w celu użycia nowego klucza.

* *Najprostszym sposobem* użyć narzędzi FedUtil zawarte w zestawie SDK programu WIF, który można pobrać najnowszą wersję dokumentu metadanych i zaktualizowanie konfiguracji.
* Aktualizowanie aplikacji .NET 4.5, która zawiera najnowszą wersję programu WIF, znajduje się w przestrzeni nazw systemu. Następnie można użyć [sprawdzania poprawności wystawcy Name Registry (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) do wykonywania automatycznych aktualizacji konfiguracji aplikacji.
* Wykonaj ręczne Przerzucanie zgodnie z instrukcjami na końcu tego dokumentu wskazówki.

Instrukcje dotyczące korzystania z FedUtil można zaktualizować konfiguracji:

1. Sprawdź, czy program WIF v1.0 SDK zainstalowany na komputerze deweloperskim dla programu Visual Studio 2008 lub 2010. Możesz [Pobierz go stąd](https://www.microsoft.com/en-us/download/details.aspx?id=4451) Jeśli nie zainstalowano jeszcze je.
2. W programie Visual Studio, otwórz rozwiązanie, a następnie kliknij prawym przyciskiem myszy odpowiednie projektu i wybierz **aktualizacji metadanych Federacji**. Jeśli ta opcja nie jest dostępna, FedUtil i/lub 1.0 programu WIF, zestaw SDK nie został zainstalowany.
3. W wierszu polecenia, wybierz **aktualizacji** do rozpoczęcia aktualizowania metadanych federacji. Jeśli masz dostęp do środowiska serwera, gdzie aplikacja jest obsługiwana, można używać w FedUtil [harmonogram aktualizacji automatycznych metadanych](https://msdn.microsoft.com/library/ee517272.aspx).
4. Kliknij przycisk **Zakończ** do ukończenia procesu aktualizacji.

### <a name="other"></a>Aplikacje sieci Web / interfejsów API, ochrona zasobów przy użyciu innych bibliotek lub ręcznego wdrażania każdego z obsługiwanych protokołów
Jeśli używasz niektóre inne biblioteki lub ręcznie zaimplementowane każdego z obsługiwanych protokołów, należy przejrzeć biblioteki lub implementacji w taki sposób, aby upewnić się, że klucz jest pobierana z dokumentu odnajdywania protokołu OpenID Connect lub metadanych Federacji dokument. Jednym ze sposobów, aby sprawdzić, w tym jest wykonaj wyszukiwanie w kodzie lub kod biblioteki wszelkie wywołania się dokument odnajdywania protokołu OpenID lub dokumentu metadanych federacji.

Jeśli klucz jest magazynowana gdzieś lub umieszczone w kodzie aplikacji, można ręcznie pobrać klucza i zaktualizować go przez wykonywanie ręczne Przerzucanie zgodnie z instrukcjami na końcu tego dokumentu wskazówki. **Zdecydowanie zaleca się, że ulepszanie aplikacji do obsługi automatycznego przerzucania** przy użyciu dowolnej konspektu podejścia w tym artykule w celu uniknięcia zakłócenia w przyszłości i obciążenie usługi Azure AD ma nagłych lub zwiększa jego kadencji przerzucania Przerzucanie poza pasmem.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Jak przetestować aplikację, aby określić, jeśli będzie miała wpływ
Możesz sprawdzić, czy aplikacja obsługuje automatyczne Przerzucanie klucza, pobierając skrypty i postępując zgodnie z instrukcjami w [to repozytorium serwisu GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Jak przeprowadzić ręczne przerzucanie, jeśli aplikacja nie obsługuje automatycznego przerzucania
Jeśli Twoja aplikacja wykonuje **nie** obsługują automatyczne przerzucanie, musisz ustanowić procesu, który okresowo podpisywania monitory usługi Azure AD kluczy i wykonuje ręczne Przerzucanie odpowiednio. [To repozytorium serwisu GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) zawiera skrypty oraz instrukcje, jak to zrobić.

