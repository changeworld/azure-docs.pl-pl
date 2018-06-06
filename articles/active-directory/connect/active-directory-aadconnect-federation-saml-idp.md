---
title: 'Azure AD Connect: Należy użyć dostawcy SAML 2.0 tożsamości na logowanie jednokrotne na | Dokumentacja firmy Microsoft'
description: W tym dokumencie opisano przy użyciu zgodnych dostawców tożsamości SAML 2.0 na logowanie jednokrotne w.
services: active-directory
author: billmath
manager: mtillman
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6933d9f9951925888c92e35f6b1e2962cc29b0ce
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801783"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>W systemie dostawcy SAML 2.0 tożsamości (IdP) dla funkcji logowania jednokrotnego

Ten dokument zawiera informacje na temat używania SAML 2.0 zgodne SP Lite oparte na profilu dostawcy tożsamości jako preferowany zabezpieczeń tokenu usługi (STS) / dostawcy tożsamości. Ten scenariusz jest przydatne, jeśli masz już katalog użytkownika i hasła przechowywane lokalnie, które mogą uzyskać dostęp za pomocą SAML 2.0. Ten istniejącego katalogu użytkownika może służyć do logowania do usługi Office 365 i innych zasobów platformy Azure zabezpieczonej przez usługi AD. W profilu SAML 2.0 SP-Lite jest oparta na powszechnie używane standardowe tożsamości federacyjnych Security (Assertion Markup Language SAML) do zapewnienia logowania jednokrotnego i atrybut framework programu exchange.

>[!NOTE]
>Lista 3 strony Idps, które zostały przetestowane do użycia z usługą Azure AD [listę zgodności federacyjnych usługi Azure AD](active-directory-aadconnect-federation-compatibility.md)

Firma Microsoft zapewnia to środowisko logowania jako integracji usługi w chmurze firmy Microsoft, takich jak usługi Office 365 z sieci odpowiednio skonfigurowanego IdP oparte na profilu SAML 2.0. Produkty innych firm są dostawców tożsamości SAML 2.0 i dlatego firma Microsoft nie zapewnia obsługi wdrażania, konfiguracji, najlepszych rozwiązań dotyczących ich rozwiązywania. Raz prawidłowo skonfigurowane, integrację SAML 2.0 dostawcy tożsamości można przetestować dla właściwej konfiguracji przy użyciu narzędzia Analizator łączności firmy Microsoft, opisany bardziej szczegółowo poniżej. Aby uzyskać więcej informacji o dostawcy tożsamości oparte na profilu SAML 2.0 SP-Lite poproś organizacji, która dostarcza ona.

>[!IMPORTANT]
>Tylko ograniczony zestaw klientów są dostępne w tym scenariuszu logowania z dostawców tożsamości SAML 2.0, w tym:

>- Oparte na sieci Web klientów, takie jak program Outlook Web Access i usługi SharePoint Online
- Klienci bogate w wiadomości e-mail używający uwierzytelniania podstawowego i obsługiwanych — metoda dostępu do programu Exchange, takie jak IMAP POP, ActiveSync, MAPI, itp. (punkt końcowy rozszerzony protokół klienta jest wymagane do wdrożenia), w tym:
    - Program Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (różne wersje systemu iOS)
    - Różnych urządzeń systemu Google Android
    - Windows Phone 7, Windows Phone 7.8 i Windows Phone 8.0
    - Klient poczty systemu Windows 8 i Windows 8.1 klienta poczty E-mail
    - Klient poczty systemu Windows 10

Wszystkich pozostałych klientów nie są dostępne w tym scenariuszu logowania u dostawcy tożsamości SAML 2.0. Na przykład klient pulpitu Lync 2010 nie jest mogli się zalogować do usługi z dostawcą tożsamości SAML 2.0 skonfigurowany do logowania jednokrotnego.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Wymagania protokołu usługi Azure AD SAML 2.0
Ten dokument zawiera szczegółowe wymagania dla protokołu i formatowanie, że Twój dostawca tożsamości SAML 2.0 musi implementować możliwości utworzenia federacji z usługą Azure AD, aby włączyć logowanie do jednego lub więcej usług chmurowych firmy Microsoft (takich jak Office 365) do wiadomości. SAML 2.0 jednostki uzależnionej (SP STS) dla usługi w chmurze firmy Microsoft w tym scenariuszu jest usługi Azure AD.

Zaleca się, że zapewnia Twój dostawca tożsamości SAML 2.0 komunikaty wyjściowe był jak podobny do śledzenia dostarczona próbka jak to możliwe. Należy także użyć wartości określonych atrybutów z podane metadanych usługi Azure AD w miarę możliwości. Po przejściu wszystkiego wiadomości dane wyjściowe można przetestować z analizatora łączności firmy Microsoft zgodnie z poniższym opisem.

Można pobrać metadanych usługi Azure AD z tego adresu URL: [ https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml ](http://https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Klienci w Chinach, przy użyciu określonych Chin wystąpienie usługi Office 365, powinien być używany następujący punkt końcowy federacyjnej: [ https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml ](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Wymagania dotyczące protokołu SAML
W tej sekcji Szczegóły jak pary komunikatów żądań i odpowiedzi są wprowadzane razem, aby pomóc w wiadomości w formacie poprawnie.

Usługi Azure AD można skonfigurować do pracy z dostawców tożsamości, korzystających z profilu SAML 2.0 SP Lite z niektórych wymagań wymienione poniżej. Przy użyciu przykładowych SAML komunikatów żądań i odpowiedzi oraz zautomatyzowanej oraz ręcznej testowania, możesz pracować do osiągnięcia współdziałania z usługą Azure AD.

## <a name="signature-block-requirements"></a>Wymagania dotyczące blok podpisu
W komunikacie odpowiedzi SAML węzła podpis zawiera informacje o podpisu cyfrowego wiadomości. Blok podpisu ma następujące wymagania:

1. Sam węzeł potwierdzenie muszą być podpisane.
2.  Metoda DigestMethod musi stanowić algorytmu RSA sha1. Inne algorytmy podpisu cyfrowego nie są akceptowane.
   `<ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Możesz również uaktywnić dokumentu XML. 
4.  Algorytm przekształcania musi odpowiadać wartości w następującym przykładzie:    `<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Algorytm elementu SignatureMethod musi odpowiadać poniższym przykładzie:   `<ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Obsługiwane powiązania
Powiązania są parametry informacji dotyczących transportu, które są wymagane. Poniższe wymagania dotyczą powiązania

1. HTTPS jest wymagana transportu.
2.  Usługi Azure AD będzie wymagać HTTP POST składania tokenu podczas logowania
3.  Usługi Azure AD będzie używać protokołu HTTP POST żądanie uwierzytelnienia do dostawcy tożsamości i PRZEKIEROWANIA do dostawcy tożsamości wiadomości wylogowywania.

## <a name="required-attributes"></a>Wymaganych atrybutów
W tej tabeli przedstawiono wymagania dla określonych atrybutów w komunikacie SAML 2.0.
 
|Atrybut|Opis|
| ----- | ----- |
|NameID|Wartość ta asercja musi być taka sama jak nazwę ImmutableID użytkownika usługi Azure AD. Może być maksymalnie 64 znaki alfanumeryczne. Musi być zakodowany bezpieczne znaków innych niż html, na przykład znak "+" jest wyświetlany jako ".2B".|
|IDPEmail|Nazwa główna użytkownika (UPN) znajduje się w odpowiedzi SAML jako elementu o nazwie IDPEmail UserPrincipalName użytkownika (UPN) w usłudze Azure AD/Office 365. Nazwa UPN jest format adresu e-mail. Wartość nazwy UPN w usłudze Office 365 (Azure Active Directory) dla systemu Windows.|
|Wystawca|Musi być identyfikatorem URI dostawcy tożsamości. Nie należy używać ponownie wystawcy z przykładowej wiadomości. Jeśli masz wiele domen najwyższego poziomu w dzierżawcy usługi Azure AD Wystawca musi być zgodna z określonego identyfikatora URI ustawienia skonfigurowane w każdej domenie.|

>[!IMPORTANT]
>Obecnie usługa Azure AD obsługuje następujący identyfikator URI Format NameID SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid — format: trwałych.

## <a name="sample-saml-request-and-response-messages"></a>Przykładowe komunikaty żądań i odpowiedzi SAML
Para komunikatów żądań i odpowiedzi jest wyświetlany w wymianie wiadomości logowania jednokrotnego.
Poniżej przedstawiono przykładowy komunikat żądania, który jest wysyłany z usługi Azure AD do dostawcy tożsamości SAML 2.0 próbki. Dostawca tożsamości SAML 2.0 próbki jest Active Directory Federation Services (AD FS) skonfigurowany do używania protokołu SAML-P. Testowania współdziałania również została ukończona z innych dostawców tożsamości SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Poniżej przedstawiono przykładowy komunikat odpowiedzi, który jest wysyłany przez dostawcę tożsamości zgodne SAML 2.0 próbki do usługi Azure AD / usługi Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurowanie dostawcy tożsamości zgodne SAML 2.0
Ta sekcja zawiera wskazówki dotyczące konfigurowania dostawcy tożsamości SAML 2.0 w celu utworzenia federacji z usługą Azure AD, aby umożliwić dostęp znak pojedynczego jeden lub więcej usług chmurowych firmy Microsoft (takich jak Office 365) przy użyciu protokołu SAML 2.0. SAML 2.0 jednostki zależnej dla usługi w chmurze firmy Microsoft w tym scenariuszu jest usługi Azure AD.

## <a name="add-azure-ad-metadata"></a>Dodawanie metadanych usługi Azure AD
Twój dostawca tożsamości SAML 2.0 musi być odpowiednia informacji na temat usługi Azure AD jednostki zależnej. Usługi Azure AD publikuje metadane na https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Zalecane jest zawsze importowania najnowsze metadanych usługi Azure AD podczas konfigurowania Twój dostawca tożsamości SAML 2.0.

>[!NOTE]
>Usługi Azure AD nie odczytać metadanych od dostawcy tożsamości.

## <a name="add-azure-ad-as-a-relying-party"></a>Dodaj usługi Azure AD jako jednostki uzależnionej
Należy włączyć komunikację między dostawca tożsamości SAML 2.0 i usługi Azure AD. Ta konfiguracja będzie zależna od dostawcy określonego tożsamości i powinni zapoznać się z dokumentacją dla niego. Identyfikator jednostki uzależnionej strony można zwykle ustawić na taki sam jak identyfikator jednostki z metadanych usługi Azure AD.

>[!NOTE]
>Sprawdź, czy zegar na serwerze dostawcy tożsamości SAML 2.0 jest zsynchronizowany ze źródłem dokładnego czasu. Czas zegara niedokładne może spowodować federacyjnego logowania się niepowodzeniem.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Zainstaluj środowisko Windows PowerShell dla logowania jednokrotnego z dostawca tożsamości SAML 2.0
Po skonfigurowaniu dostawcy tożsamości SAML 2.0 do użytku z logowania jednokrotnego w usłudze Azure AD, następnym krokiem jest pobranie i zainstalowanie programu Azure Active Directory modułu dla Windows PowerShell. Po zakończeniu instalacji będzie używać tych poleceń cmdlet, skonfigurowanie usługi Azure AD jako Sfederowanych domen.

Azure Active Directory modułu dla środowiska Windows PowerShell jest pobierania zarządzania danych organizacji w usłudze Azure AD. Ten moduł instaluje zestaw poleceń cmdlet programu Windows PowerShell; Uruchom te polecenia cmdlet, aby skonfigurować pojedynczy znak na dostęp do usługi Azure AD, a więc do wszystkich usług w chmurze subskrybujesz. Aby dowiedzieć się, jak pobrać i zainstalować polecenia cmdlet zobacz [http://technet.microsoft.com/library/jj151815.aspx](http://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Konfigurowanie relacji zaufania między dostawca tożsamości SAML i usługa Azure AD
Przed rozpoczęciem konfigurowania Federacji dla domeny usługi Azure AD, trzeba dla niego skonfigurować własną domenę niestandardową. Nie można sfederować domeny domyślnej zapewnianej przez firmę Microsoft. Domena domyślna firmy Microsoft kończy się wyrazem "onmicrosoft.com".
Należy uruchomić serii poleceń cmdlet w interfejsie wiersza polecenia programu Windows PowerShell umożliwiają dodawanie lub przekonwertować domen dla logowania jednokrotnego.

Każdej domeny usługi Azure Active Directory, która ma zostać utworzona Federacja przy użyciu dostawcy tożsamości SAML 2.0 musi być dodany jako pojedynczą domenę logowania jednokrotnego lub przekonwertować na pojedynczą domenę logowania jednokrotnego od standardowej w domenę. Dodawanie lub Przekształcanie domeny ustanawia relację zaufania między dostawca tożsamości SAML 2.0 i usługi Azure AD.

Poniższa procedura przeprowadzi Cię przez Konwertowanie istniejącej domeny standardowa do domeny federacyjnej przy użyciu SAML 2.0 SP-Lite. 

>[!NOTE]
>Domeny mogą wystąpić awaria, która wpływa na użytkowników do 2 godzin po wykonaniu tego kroku.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Skonfigurowanie domeny w usłudze Azure AD Directory dla Federacji


1. Nawiązać połączenia z katalogiem Azure AD jako administrator dzierżawy: MsolService połączenia.
2.  Skonfiguruj żądane domeny usługi Office 365 do Federacji za pomocą SAML 2.0: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Plik metadanych IDP można uzyskać ciąg kodowany w standardzie base64 certyfikatu podpisywania. Dostarczono przykładem tej lokalizacji, ale mogą się nieznacznie różnić w implementacji.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Aby uzyskać więcej informacji na temat "Set-MsolDomainAuthentication", zobacz: [ http://technet.microsoft.com/library/dn194112.aspx ](http://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Należy uruchomić Użyj "$ecpUrl ="https://WS2012R2-0.contoso.com/PAOS"" tylko wtedy, gdy rozszerzenie ECP skonfigurowane dla dostawcy tożsamości. Klienci usługi Exchange Online, z wyjątkiem aplikacji sieci Web programu Outlook (OWA) polegać na ogłoszenie na podstawie aktywnego punktu końcowego. Jeśli z SAML 2.0 STS implementuje aktywny punkt końcowy podobne do implementacji ECP Shibboleth dla aktywnego punktu końcowego może być możliwe w dla tych klientów sformatowanego do interakcji z usługą Exchange Online.

Po skonfigurowano federacyjnego można przełączyć do "niefederacyjnych" (lub "zarządzany"), jednak ta zmiana zostanie do dwóch godzin i wymaga przypisywanie nowe hasła losowego oparte na chmurze logowaniu do poszczególnych użytkowników. Przełączanie do "zarządzany" mogą być wymagane w niektórych scenariuszach zresetować wystąpił błąd w ustawieniach. Aby uzyskać więcej informacji o konwersji domeny Zobacz: [ http://msdn.microsoft.com/library/windowsazure/dn194122.aspx ](http://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Udostępniania podmiotów zabezpieczeń użytkownika do usługi Azure AD / usługi Office 365
Zanim można uwierzytelniać użytkowników do usługi Office 365, należy udostępnić usługi Azure AD z głównych użytkownika, które odpowiadają potwierdzenia oświadczenia SAML 2.0. Jeśli te podmioty użytkownika nie są znane do usługi Azure AD z wyprzedzeniem, następnie one nie może służyć do federacyjnego logowania. Azure AD Connect lub środowiska Windows PowerShell, można go użyć do udostępnienia podmiotów zabezpieczeń użytkownika.

Azure AD Connect można go użyć do udostępnienia podmiotów zabezpieczeń do domen w katalogu usługi Azure AD z lokalnej usługi Active Directory. Aby uzyskać szczegółowe informacje, zobacz [integrację katalogów lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).

Środowisko Windows PowerShell można również zautomatyzować Dodawanie nowych użytkowników do usługi Azure AD i zsynchronizować zmiany z katalogu lokalnego. Aby używać poleceń cmdlet programu Windows PowerShell, należy pobrać [Azure Active Directory modułów](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

W tej procedurze pokazano, jak dodać pojedynczego użytkownika do usługi Azure AD.


1. Nawiązać połączenia z katalogiem Azure AD jako administrator dzierżawy: MsolService połączenia.
2.  Utwórz nowy podmiot użytkownika: ` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

Aby uzyskać więcej informacji o wyewidencjonowania "New-MsolUser" [http://technet.microsoft.com/library/dn194096.aspx](http://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>Wartość "UserPrinciplName" musi być zgodna wartość, która zostanie wysłana do "IDPEmail" w roszczenia SAML 2.0, a wartość "Nazwę ImmutableID" musi odpowiadać wartości wysyłane potwierdzenia Twojej "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Sprawdź rejestracji jednokrotnej z dostawców tożsamości użytkownika SAML 2.0
Jako administrator przed Sprawdź i zarządzanie rejestracji jednokrotnej (również o nazwie tożsamości federacyjnych), zapoznaj się z informacjami i wykonaj kroki w następujących artykułach, aby skonfigurować logowanie jednokrotne przy pomocy dostawcy usługi SAML 2.0 SP-Lite oparte na tożsamości:


1.  Należy przejrzeć wymagania protokołu usługi Azure AD SAML 2.0
2.  Skonfigurowano dostawcy tożsamości SAML 2.0
3.  Zainstaluj środowisko Windows PowerShell dla rejestracji jednokrotnej z dostawca tożsamości SAML 2.0
4.  Konfigurowanie relacji zaufania między dostawca tożsamości SAML 2.0 i usługi Azure AD
5.  Zainicjowano obsługę administracyjną głównej nazwy użytkownika znane testu do usługi Azure Active Directory (Office 365), za pomocą środowiska Windows PowerShell lub Azure AD Connect.
6.  Konfigurowanie przy użyciu synchronizacji katalogu [Azure AD Connect](active-directory-aadconnect.md).

Po skonfigurowaniu logowania jednokrotnego SAML 2.0 SP-Lite na podstawie dostawcy tożsamości, należy sprawdzić, czy działa prawidłowo.

>[!NOTE]
>Po przekonwertowaniu domeny, zamiast dodawania co może potrwać do 24 godzin, aby skonfigurować logowanie jednokrotne.
Przed sprawdzisz logowanie jednokrotne, należy zakończyć konfigurowanie synchronizacji usługi Active Directory, zsynchronizuj swoje katalogi i Aktywuj zsynchronizowanych użytkowników.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Sprawdź, czy tej rejestracji jednokrotnej nie został skonfigurowany poprawnie za pomocą narzędzia
Aby sprawdzić, czy tej rejestracji jednokrotnej nie został skonfigurowany prawidłowo, można wykonać następującą procedurę, aby potwierdzić, że będą mogli zalogować się do usługi w chmurze przy użyciu poświadczeń firmowych.

Firma Microsoft udostępnia narzędzia, którego można używać do testowania dostawcy oparte na tożsamości SAML 2.0. Przed uruchomieniem narzędzia test, należy skonfigurować dzierżawa usługi Azure AD możliwości utworzenia federacji z dostawcy tożsamości.

>[!NOTE]
>Analizator łączności wymaga programu Internet Explorer 10 lub nowszej.



1. Pobieranie Analyzer łączności z [ https://testconnectivity.microsoft.com/?tabid=Client ](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Kliknij przycisk Zainstaluj teraz, aby rozpocząć pobieranie i instalowanie narzędzia.
3.  Wybierz opcję "Nie może skonfigurować federacji z usługi Office 365, Azure lub innych usług używających usługi Azure Active Directory".
4.  Po pobraniu narzędzia i uruchomiony, pojawi się okno diagnostyki łączności. Narzędzie będzie przetwarzać testowania połączenia federacji.
5.  Analizator łączności spowoduje otwarcie dostawców tożsamości z SAML 2.0, można się zalogować, wprowadź poświadczenia dla głównej nazwy użytkownika podczas testowania: ![SAML](media/active-directory-aadconnect-federation-saml-idp/saml1.png)
6.  W federacyjnej logowania okna testów należy wprowadź nazwę konta i hasło dla dzierżawy usługi Azure AD, skonfigurowanego federacyjną u dostawcy tożsamości SAML 2.0. Narzędzie podejmie próbę Zaloguj się przy użyciu tych poświadczeń i szczegółowe wyniki testów wykonanych podczas próby logowania zostanie podana jako dane wyjściowe.
![SAML](media/active-directory-aadconnect-federation-saml-idp/saml2.png)
7. To okno zawiera nieudanych wyników testów. Kliknięcie przeglądu szczegółowe wyniki są wyświetlane informacje o wynikach dla każdego testu, która została wykonana. Można także zapisać wyniki na dysku w celu udostępnienia ich.
 
>[!NOTE]
>Analizator łączności również testów Active Federacji przy użyciu WS *-oparte na i ECP/PAOS protokołów. Jeśli nie używasz te można zignorować następujący błąd: testowanie Active przepływu logowania przy użyciu dostawcy tożsamości federacyjnej Active w punkcie końcowym.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ręcznie Sprawdź, czy tej rejestracji jednokrotnej nie został skonfigurowany poprawnie
Weryfikacja ręczna udostępnia dodatkowe kroki, które należy wykonać w celu zapewnienia, że dostawca tożsamości SAML 2.0 działa prawidłowo w wielu scenariuszach.
Aby sprawdzić, czy tej rejestracji jednokrotnej nie został skonfigurowany prawidłowo, wykonaj następujące czynności:


1. Na komputerze przyłączonym do domeny Zaloguj się do usługi w chmurze przy użyciu tej samej nazwie logowania, służące do poświadczenia firmowe.
2.  Kliknij wewnątrz pola hasła. Jeśli logowanie jednokrotne jest skonfigurowany, pole hasło będzie przyciemnione i zostanie wyświetlony następujący komunikat: "teraz musisz się zalogować na &lt;firmy&gt;."
3.  Kliknij, logowanie w &lt;firmy&gt; łącza. Jeśli masz możliwość logowania, następnie pojedynczy logowania jednokrotnego nie został skonfigurowany.

## <a name="next-steps"></a>Następne kroki


- [Dostosowywanie z programem Azure AD Connect i zarządzania w usłudze Active Directory Federation Services](active-directory-aadconnect-federation-management.md)
- [Lista zgodności usługi Azure AD z usługami federacyjnymi](active-directory-aadconnect-federation-compatibility.md)
- [Instalacja niestandardowa programu Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
