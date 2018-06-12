---
title: Podpisywanie przerzucania kluczy w usłudze Azure AD
description: W tym artykule omówiono podpisywania Przerzucanie klucza najlepsze rozwiązania dotyczące usługi Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: 02d7cb28411e0baec20d334994b385dcd3b06451
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293385"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Podpisywanie przerzucania kluczy w usłudze Azure Active Directory
W tym artykule opisano, co należy wiedzieć o kluczy publicznych, które są używane w usłudze Azure Active Directory (Azure AD) do podpisywania tokenów zabezpieczających. Należy pamiętać, że te przerzucania kluczy w regularnych odstępach czasu i w razie zagrożenia, może być przerzuceniem natychmiast. Wszystkie aplikacje, które używają usługi Azure AD powinno być możliwe do programowego obsługuje procesu Przerzucanie klucza lub ustanowić proces okresowej ręczne przerzucania. Materiały, aby zrozumieć, jak działają kluczy ocenić wpływ Przerzucanie aplikacji oraz sposobu aktualizacji aplikacji lub ustanawiania proces okresowej przerzucania ręcznej obsługi Przerzucanie klucza, jeśli jest to konieczne.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Omówienie kluczy podpisywania w usłudze Azure AD
Usługi Azure AD używa oparte na standardach branżowych kryptografii klucza publicznego, aby ustanowić zaufanie między sobą i aplikacje, które go używają. W praktyce, to działa w następujący sposób: klucz podpisujący, który składa się z pary kluczy publicznych i prywatnych używa usługi Azure AD. Po zalogowaniu się do aplikacji, która używa usługi Azure AD do uwierzytelniania usługi Azure AD tworzy token zabezpieczający, który zawiera informacje o użytkowniku. Token ten jest podpisany przez usługę Azure AD przy użyciu jego klucz prywatny, przed wysłaniem do aplikacji. Aby sprawdzić, czy token jest prawidłowy i zdalnych z usługi Azure AD, aplikacja musi zweryfikować podpisu tokenu przy użyciu klucza publicznego udostępnianych przez usługi Azure AD, który jest zawarty w dzierżawie programu [OpenID Connect dokument](http://openid.net/specs/openid-connect-discovery-1_0.html) lub SAML / WS-Fed [dokument metadanych usług federacyjnych](active-directory-federation-metadata.md).

Ze względów bezpieczeństwa klucza przedstawia w regularnych odstępach czasu i, w przypadku wystąpienia sytuacji awaryjnych podpisywania Azure AD może być przerzuceniem natychmiast. Każda aplikacja, która integruje się z usługą Azure AD powinna być przygotowana do obsługi zdarzenia przerzucania kluczy niezależnie od tego, jak często występuje. Jeśli nie, a aplikacja próbuje użyć wygasły klucz można zweryfikować podpisu tokenu, żądanie logowania nie powiedzie się.

Istnieje więcej niż jeden prawidłowy klucz dostępnych w dokumencie odnajdywania OpenID Connect i dokument metadanych federacji. Aplikacja powinna być przygotowane pod kątem używania dowolnych kluczy określona w dokumencie, ponieważ jeden klucz mogła zostać wycofana wkrótce, innego może się zastąpienia itd.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Ocenia, czy aplikacja będzie mieć wpływ na sposób i co należy zrobić informacji na ten temat
Jak aplikacja obsługuje Przerzucanie klucza zależy od zmienne, takie jak typ aplikacji lub użyto jakiego protokołu tożsamości i biblioteki. W poniższych rozdziałach oceny, czy najbardziej typowych aplikacji ma wpływ na Przerzucanie klucza i zawierają wskazówki dotyczące aktualizacji aplikacji do obsługi automatycznego przerzucania lub ręcznie zaktualizować klucza.

* [Aplikacja Native client aplikacji dostęp do zasobów](#nativeclient)
* [Aplikacje sieci Web / interfejsów API uzyskiwania dostępu do zasobów](#webclient)
* [Aplikacje sieci Web / interfejsy API ochrony zasobów i utworzony za pomocą usługi aplikacji Azure](#appservices)
* [Aplikacje sieci Web / interfejsy API ochrony zasobów przy użyciu platformy .NET OWIN OpenID Connect, WS-Fed lub WindowsAzureActiveDirectoryBearerAuthentication oprogramowania pośredniczącego](#owin)
* [Aplikacje sieci Web / interfejsy API ochrony zasobów przy użyciu platformy .NET Core OpenID Connect lub JwtBearerAuthentication oprogramowania pośredniczącego](#owincore)
* [Aplikacje sieci Web / interfejsy API ochrony zasobów za pomocą modułu passport-azure-ad Node.js](#passport)
* [Aplikacje sieci Web / interfejsy API ochrony zasobów i utworzone za pomocą programu Visual Studio 2015 lub Visual Studio 2017 r.](#vs2015)
* [Aplikacje sieci Web, ochrona zasobów i utworzone za pomocą programu Visual Studio 2013](#vs2013)
* [Interfejsy API sieci Web ochrona zasobów i utworzone za pomocą programu Visual Studio 2013](#vs2013_webapi)
* [Aplikacje sieci Web, ochrona zasobów i utworzone za pomocą programu Visual Studio 2012](#vs2012)
* [Aplikacje sieci Web, ochrona zasobów i utworzone za pomocą programu Visual Studio 2010, o 2008 przy użyciu programu Windows Identity Foundation](#vs2010)
* [Aplikacje sieci Web / interfejsy API ochrony zasobów przy użyciu innych bibliotek lub ręcznego wykonania dowolnego z obsługiwanych protokołów](#other)

Niniejsze wskazówki **nie** dotyczy:

* Aplikacje dodane z usługi Azure AD galerii aplikacji (w tym niestandardowe) mają oddzielne wytyczne w odniesieniu do kluczy podpisywania. [Więcej informacji.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Lokalnego nie trzeba martwić kluczy podpisywania aplikacji opublikowanych przy użyciu serwera proxy aplikacji.

### <a name="nativeclient"></a>Aplikacja Native client aplikacji dostęp do zasobów
Aplikacje, które uzyskują dostęp tylko do zasobów (tj. Program Microsoft Graph, KeyVault, interfejsu API programu Outlook i inne APIs firmy Microsoft) zwykle tylko uzyskania tokenu i przekaż ją wzdłuż do właściciela zasobów. Biorąc pod uwagę, że nie są one chronione żadnych zasobów, nie kontrolują token i dlatego nie trzeba upewnić się, że jest poprawnie podpisany.

Aplikacje klienckie natywnego, czy desktop lub mobile, do tej kategorii, jak i w związku z tym nie dotyczy Przerzucanie.

### <a name="webclient"></a>Aplikacje sieci Web / interfejsów API uzyskiwania dostępu do zasobów
Aplikacje, które uzyskują dostęp tylko do zasobów (tj. Program Microsoft Graph, KeyVault, interfejsu API programu Outlook i inne APIs firmy Microsoft) zwykle tylko uzyskania tokenu i przekaż ją wzdłuż do właściciela zasobów. Biorąc pod uwagę, że nie są one chronione żadnych zasobów, nie kontrolują token i dlatego nie trzeba upewnić się, że jest poprawnie podpisany.

Aplikacje sieci Web i interfejsów API używanym przepływu tylko do aplikacji sieci web (poświadczenia klienta / certyfikatu klienta), do tej kategorii i w związku z tym nie dotyczy przerzucania.

### <a name="appservices"></a>Aplikacje sieci Web / interfejsy API ochrony zasobów i utworzony za pomocą usługi aplikacji Azure
Usługa Azure App Service Authentication / funkcje autoryzacji (EasyAuth) ma już logikę niezbędną do obsługi automatycznego przerzucania klucza.

### <a name="owin"></a>Aplikacje sieci Web / interfejsy API ochrony zasobów przy użyciu platformy .NET OWIN OpenID Connect, WS-Fed lub WindowsAzureActiveDirectoryBearerAuthentication oprogramowania pośredniczącego
Jeśli aplikacja używa .NET OWIN OpenID Connect, WS-Fed lub WindowsAzureActiveDirectoryBearerAuthentication oprogramowanie pośredniczące, już logikę niezbędną do obsługi automatycznego przerzucania klucza.

Można potwierdzić, że aplikacja używa któregoś z powyższych, wyszukując dowolne poniższe fragmenty kodu w pliku Startup.cs lub Startup.Auth.cs aplikacji

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

### <a name="owincore"></a>Aplikacje sieci Web / interfejsy API ochrony zasobów przy użyciu platformy .NET Core OpenID Connect lub JwtBearerAuthentication oprogramowania pośredniczącego
Jeśli aplikacja używa oprogramowania pośredniczącego .NET Core OWIN OpenID Connect lub JwtBearerAuthentication, jest już konieczne logiki do obsługi automatycznego przerzucania klucza.

Można potwierdzić, że aplikacja używa któregoś z powyższych, wyszukując dowolne poniższe fragmenty kodu w pliku Startup.cs lub Startup.Auth.cs aplikacji

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

### <a name="passport"></a>Aplikacje sieci Web / interfejsy API ochrony zasobów za pomocą modułu passport-azure-ad Node.js
Jeśli aplikacja używa modułu passport-ad Node.js, jest już konieczne logiki do obsługi automatycznego przerzucania klucza.

Sprawdź, czy aplikacja usługi passport-ad przez wyszukiwanie następujący fragment kodu w aplikacji app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Aplikacje sieci Web / interfejsy API ochrony zasobów i utworzone za pomocą programu Visual Studio 2015 lub Visual Studio 2017 r.
Jeśli aplikacja została skompilowana przy użyciu szablonu aplikacji sieci web w programie Visual Studio 2015 lub Visual Studio 2017 i wybrano **konta służbowego i pracy** z **Zmień uwierzytelnianie** menu, jest już Logika niezbędne do obsługi automatycznego przerzucania klucza. Tę logikę osadzone w pośredniczącym OWIN OpenID Connect pobiera i przechowuje klucze z OpenID Connect dokument i okresowo odświeża je.

Ręcznie dodane uwierzytelniania do rozwiązania, aplikacja może nie mieć logiki niezbędne przerzucania klucza. Musisz zapisać samodzielnie lub postępuj zgodnie z instrukcjami [aplikacji sieci Web / interfejsów API przy użyciu innych bibliotek lub ręcznego wykonania dowolnego z obsługiwanych protokołów](#other).

### <a name="vs2013"></a>Aplikacje sieci Web, ochrona zasobów i utworzone za pomocą programu Visual Studio 2013
Jeśli aplikacja została skompilowana przy użyciu szablonu aplikacji sieci web w programie Visual Studio 2013 i wybrano **konta organizacyjne** z **Zmień uwierzytelnianie** menu, jest już konieczne logiki do obsługi automatycznego przerzucania klucza. Istotą takiej logiki przechowuje unikatowy identyfikator organizacji i podpisywania kluczowych informacji w dwóch tabelach bazy danych skojarzony z projektem. Parametry połączenia dla bazy danych można znaleźć w pliku Web.config dla projektu.

Ręcznie dodane uwierzytelniania do rozwiązania, aplikacja może nie mieć logiki niezbędne przerzucania klucza. Musisz zapisać samodzielnie lub postępuj zgodnie z instrukcjami [aplikacji sieci Web / interfejsów API przy użyciu innych bibliotek lub ręcznego wykonania dowolnego z obsługiwanych protokołów.](#other).

Poniższe kroki pomoże Ci zweryfikować, że logika działa prawidłowo w aplikacji.

1. W programie Visual Studio 2013, otwórz rozwiązanie, a następnie kliknij na **Eksploratora serwera** karty w prawym okienku.
2. Rozwiń węzeł **połączenia danych**, **połączenia DefaultConnection**, a następnie **tabel**. Zlokalizuj **IssuingAuthorityKeys** tabeli, kliknij go prawym przyciskiem myszy, a następnie kliknij przycisk **Pokaż dane tabeli**.
3. W **IssuingAuthorityKeys** tabeli, będzie istnieć co najmniej jeden wiersz, który odpowiada wartości odcisku palca dla klucza. Usuń wszystkie wiersze w tabeli.
4. Kliknij prawym przyciskiem myszy **dzierżawców** tabeli, a następnie kliknij przycisk **Pokaż dane tabeli**.
5. W **dzierżawców** tabeli, będzie istnieć co najmniej jeden wiersz, co odpowiada identyfikator dzierżawy unikatowego katalogu. Usuń wszystkie wiersze w tabeli. Jeśli nie usuniesz wierszy w obu **dzierżawców** tabeli i **IssuingAuthorityKeys** tabeli, wystąpi błąd w czasie wykonywania.
6. Skompiluj i uruchom aplikację. Po zarejestrowaniu na koncie, można zatrzymać aplikacji.
7. Wróć do **Eksploratora serwera** i przyjrzyj się wartości w **IssuingAuthorityKeys** i **dzierżawców** tabeli. Można zauważyć, że zostały one automatycznie zapełnienia odpowiednie informacje z dokumentu metadanych federacji.

### <a name="vs2013"></a>Interfejsy API sieci Web ochrona zasobów i utworzone za pomocą programu Visual Studio 2013
Jeśli utworzono aplikacji interfejsu API sieci web w programie Visual Studio 2013, przy użyciu szablonu interfejsu API sieci Web, a następnie wybrać **konta organizacyjne** z **Zmień uwierzytelnianie** menu, zostały już ma logikę w aplikacji.

Jeśli ręcznie skonfigurowano uwierzytelnianie, postępuj zgodnie z instrukcjami poniżej, aby dowiedzieć się, jak skonfigurować interfejs API sieci Web można automatycznie zaktualizować informacje o jego kluczu.

Poniższy fragment kodu pokazano, jak pobrać najnowsze kluczy z dokumentu metadanych Federacji, a następnie użyj [programu obsługi tokenów JWT](https://msdn.microsoft.com/library/dn205065.aspx) do sprawdzania poprawności tokenu. Fragment kodu zakłada, że korzystasz z własnego mechanizm buforowania dla przechowywanie klucza do sprawdzania poprawności tokenów przyszłych z usługi Azure AD, czy należeć bazy danych, w pliku konfiguracji lub w innym miejscu.

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

### <a name="vs2012"></a>Aplikacje sieci Web, ochrona zasobów i utworzone za pomocą programu Visual Studio 2012
Jeśli aplikacja został utworzony w programie Visual Studio 2012, prawdopodobnie używany tożsamości i dostępu do narzędzia do konfigurowania aplikacji. Jest także prawdopodobne, że używasz [sprawdzania poprawności wystawcy nazwa rejestru (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). VINR jest odpowiedzialny za konserwację klucze używany do sprawdzania poprawności tokenów wystawionych przez nich i informacji o zaufanych dostawców tożsamości (Azure AD). VINR ułatwia także można automatycznie zaktualizować informacje o kluczu przechowywane w pliku Web.config przez pobieranie najnowszych dokumentu metadanych Federacji skojarzone z katalogiem, sprawdzania, czy konfiguracja jest nieaktualna z najnowszą wersję dokumentu i aktualizowanie aplikacji do użycia nowego klucza zgodnie z potrzebami.

Jeśli utworzono aplikację za pomocą przykłady i wskazówki z dokumentacją dostarczoną przez firmę Microsoft, logiki Przerzucanie klucza jest już uwzględniony w projekcie. Można zauważyć, że kod poniżej już istnieje w projekcie. Jeśli aplikacja nie ma już tę logikę, wykonaj następujące czynności, aby dodać go i sprawdź, czy działa poprawnie.

1. W **Eksploratora rozwiązań**, Dodaj odwołanie do **System.IdentityModel** zestawu dla odpowiedniego projektu.
2. Otwórz **Global.asax.cs** pliku i dodaj następującą dyrektyw using:
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
4. Wywołanie **RefreshValidationSettings()** metody w **Application_Start()** metody w **Global.asax.cs** pokazany:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Po wykonaniu tych kroków pliku Web.config aplikacji zostaną zaktualizowane przy użyciu najnowszych informacji z dokumentu metadanych Federacji, w tym klucze najnowszą. Ta aktualizacja nastąpi za każdym razem, gdy puli aplikacji jest odtwarzana w usługach IIS; Domyślnie usługi IIS ustawiono odtwarzać aplikacje co 29 godzin.

Wykonaj poniższe kroki, aby zweryfikować logiki przerzucania klucza.

1. Po upewnieniu się, że aplikacja korzysta z kodu powyżej, otwórz **Web.config** pliku, a następnie przejdź do **<issuerNameRegistry>** bloku, w szczególności szukasz następujących kilka wierszy:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. W **<add thumbprint=””>** Zmień ustawienie wartości odcisku palca przez zamianę dowolny znak inny. Zapisz **Web.config** pliku.
3. Tworzenie aplikacji, a następnie uruchom go. Jeśli można ukończyć procesu logowania, aplikacja jest pomyślnie aktualizowanie klucza pobierając wymaganych informacji z dokument metadanych usług federacyjnych w Twoim katalogu. Jeśli występują problemy dotyczące logowania, upewnij się, zmiany w aplikacji są poprawne, odczytując [Dodawanie logowania jednokrotnego w sieci Web aplikacji używanie usługi Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) artykułu, lub pobieranie i zapoznanie się poniższy przykładowy kod: [ Chmury wielodostępne aplikacji dla usługi Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Aplikacje sieci Web ochrona zasobów i utworzone za pomocą programu Visual Studio 2008 lub 2010 i .NET 3.5 w wersji 1.0 systemu Windows Identity Foundation (WIF)
Jeśli utworzono aplikację na 1.0 WIF nie istnieje mechanizm podany na automatyczne odświeżanie konfiguracji aplikacji do użycia nowego klucza.

* *Najprostszym sposobem* użyj narzędzi FedUtil dołączony WIF zestawu SDK, który można pobrać najnowszą wersję dokumentu metadanych i zaktualizowanie konfiguracji.
* Aktualizowanie aplikacji .NET 4.5, który zawiera najnowszą wersję programu WIF znajduje się w przestrzeni nazw systemu. Następnie można użyć [sprawdzania poprawności wystawcy nazwa rejestru (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) do wykonywania automatycznych aktualizacji konfiguracji aplikacji.
* Wykonaj ręcznie przerzucania zgodnie z instrukcjami znajdującymi się na końcu niniejszego dokumentu wskazówki.

Instrukcje dotyczące korzystania z FedUtil o zaktualizowanie konfiguracji:

1. Sprawdź, czy masz 1.0 WIF SDK zainstalowany na komputerze deweloperskim dla programu Visual Studio 2008 lub 2010. Możesz [go pobrać stąd](https://www.microsoft.com/en-us/download/details.aspx?id=4451) Jeśli nie został jeszcze zainstalowany go.
2. W programie Visual Studio Otwórz rozwiązanie, a następnie kliknij prawym przyciskiem myszy odpowiednie projektu i wybierz **aktualizacji metadanych Federacji**. Jeśli ta opcja nie jest dostępna, FedUtil i/lub 1.0 WIF zestawu SDK nie została zainstalowana.
3. W wierszu, wybierz **aktualizacji** do rozpoczęcia aktualizowania metadanych federacji. Jeśli masz dostęp do środowiska serwera, gdzie jest hostowana aplikacja można używać w FedUtil [harmonogram aktualizacji automatycznych metadanych](https://msdn.microsoft.com/library/ee517272.aspx).
4. Kliknij przycisk **Zakończ** aby ukończyć proces aktualizacji.

### <a name="other"></a>Aplikacje sieci Web / interfejsy API ochrony zasobów przy użyciu innych bibliotek lub ręcznego wykonania dowolnego z obsługiwanych protokołów
Jeśli używasz niektóre inne biblioteki lub ręcznie zaimplementowana dowolną z obsługiwanych protokołów, należy sprawdzić w bibliotece lub implementacji, aby upewnić się, że klucz jest pobierana z metadanych Federacji lub dokument odnajdywania OpenID Connect dokument. Jednym ze sposobów sprawdzenia tego jest przeprowadzenie wyszukiwania w kodzie lub kod biblioteki wszelkie wywołania limit dokumencie odnajdywania OpenID lub dokument metadanych federacji.

Jeśli klucz jest magazynowana gdzieś lub zapisane na stałe w aplikacji, można ręcznie pobrać klucza i zaktualizować go przez wykonanie ręczne przerzucania zgodnie z instrukcjami znajdującymi się na końcu niniejszego dokumentu wskazówki. **Zdecydowanie zaleca się, że ulepszanie aplikacji do obsługi automatycznego przerzucania** przy użyciu dowolnej z metod konspektu w tym artykule Aby uniknąć zakłócenia w przyszłości i koszty usługi Azure AD ma nagłych lub zwiększa jego okresach przerzucania Przerzucanie poza pasmem.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Jak przetestować aplikację, aby określić, czy będzie miała wpływ
Można sprawdzić, czy aplikacja obsługuje automatyczne Przerzucanie klucza, pobierając skrypty i instrukcje podane w następujących [to repozytorium GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Jak wykonać przerzucania ręcznie, jeśli aplikacja nie obsługuje automatycznego przerzucania
Jeśli aplikacja obsługuje **nie** obsługują automatyczne przerzucanie, należy ustanowić proces, który okresowo podpisywania monitory usługi Azure AD kluczy i odpowiednio wykonuje ręczne przerzucania. [To repozytorium GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) zawiera skrypty oraz instrukcje, jak to zrobić.

