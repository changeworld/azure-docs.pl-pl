---
title: 'Program Azure AD Connect: Użyj dostawcy tożsamości SAML 2.0 dla logowania jednokrotnego na | Dokumentacja firmy Microsoft'
description: W tym dokumencie opisano przy użyciu zgodnych dostawcy tożsamości SAML 2.0 do rejestracji jednokrotnej w.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1870137505b3d00ee6ed31595050908c970c444
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878098"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>W systemie dostawcy tożsamości SAML 2.0 (IdP) do rejestracji jednokrotnej

Ten dokument zawiera informacje na temat korzystania z języka SAML 2.0 zgodne SP Lite oparte na profilu dostawcy tożsamości jako preferowany Usługa tokenu zabezpieczającego (STS) / dostawcy tożsamości. Ten scenariusz jest przydatne, gdy masz już katalog użytkownika i hasła przechowywane w środowisku lokalnym, które można uzyskać dostęp przy użyciu protokołu SAML 2.0. Ten istniejący katalog użytkowników może służyć do logowania jednokrotnego do usługi Office 365 i innych zasobów platformy Azure zabezpieczonych przez usługi AD. Profilu SAML 2.0 SP-Lite opiera się na powszechnie używanych standardowych tożsamości federacyjnych zabezpieczeń Assertion Markup Language (SAML) udostępnia platformę exchange logowania jednokrotnego i atrybutów.

>[!NOTE]
>Aby uzyskać listę firm 3 dostawców tożsamości, które zostały przetestowane do użycia z usługą Azure AD, zobacz [listę zgodności federacyjnych usługi Azure AD](how-to-connect-fed-compatibility.md)

Firma Microsoft obsługuje to środowisko logowania jednokrotnego jako Integracja usługi w chmurze firmy Microsoft, takich jak Office 365 za pomocą usługi prawidłowo skonfigurowane SAML 2.0 na podstawie profilu dostawcy tożsamości. Dostawcy tożsamości SAML 2.0 są produktów innych firm i w związku z tym firma Microsoft nie zapewnia obsługi wdrażania, konfiguracji, rozwiązywanie problemów z najlepszych rozwiązań dotyczących ich. Po poprawnym skonfigurowaniu integracji z języka SAML 2.0, t dostawcy tożsamości dla odpowiedniej konfiguracji za pomocą narzędzia Analizator łączności firmy Microsoft, który został opisany bardziej szczegółowo poniżej. Aby uzyskać więcej informacji na temat dostawcą tożsamości na podstawie profilu SAML 2.0 SP-Lite poproś organizacji, która dostarcza on.

> [!IMPORTANT]
> Tylko w ograniczonym zestawie klientów są dostępne w tym scenariuszu logowania jednokrotnego przy użyciu dostawcy tożsamości SAML 2.0, dane te obejmują:
> 
> - Oparte na sieci Web klientów, takich jak program Outlook Web Access i SharePoint Online
> - Bogate w wiadomości e-mail, używają uwierzytelnianie podstawowe i obsługiwane metody dostępu programu Exchange, takich jak IMAP, POP, ActiveSync, MAPI itp. (punkt końcowy rozszerzony protokół klienta jest wymagany do wdrożenia), w tym:
>     - Outlook 2016-Microsoft Outlook 2010/Outlook 2013, firmy Apple dla telefonu iPhone (różne wersje systemu iOS)
>     - Różne urządzenia systemu Android firmy Google
>     - Windows Phone 8.0, Windows Phone 7 i Windows Phone 7.8
>     - Klient poczty systemu Windows 8 i Windows 8.1 klienta poczty E-mail
>     - Klient poczty systemu Windows 10

Inni klienci nie są dostępne w tym scenariuszu logowania jednokrotnego przy użyciu dostawcy tożsamości SAML 2.0. Na przykład na pulpicie klienta Lync 2010 nie będzie mógł zalogować się do usługi za pomocą dostawcy tożsamości SAML 2.0 skonfigurowane na potrzeby logowania jednokrotnego.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Wymagania protokołu SAML AD w wersji 2.0 platformy Azure
Ten dokument zawiera szczegółowe wymagania dotyczące protokołu i komunikat formatowania, że musi implementować dostawcy tożsamości SAML 2.0, aby utworzyć Federację z usługą Azure AD, aby włączyć logowanie jednokrotne jeden lub więcej usług chmurowych firmy Microsoft (takich jak Office 365). SAML 2.0 jednostki uzależnionej ze stron (SP STS) dla usługi w chmurze firmy Microsoft używane w tym scenariuszu jest usługi Azure AD.

Zalecane jest, że zapewnia dostawcy tożsamości SAML 2.0 komunikaty wyjściowe jest podobne do śledzenia podanych przykładowych jak to możliwe. Także użyć wartości określonych atrybutów z podanym metadanych usługi Azure AD w przypadku, gdy to możliwe. Po przejściu wszystkiego wiadomości danych wyjściowych można przetestować za pomocą analizatora łączności firmy Microsoft zgodnie z poniższym opisem.

Metadane usługi Azure AD można pobrać spod tego adresu URL: [ https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml ](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Klienci w Chinach, przy użyciu wystąpienia specjalne usługi Office 365, powinien być używany następujący punkt końcowy Federacji: [ https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml ](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Wymagania protokołu SAML
W tej sekcji Szczegóły jak pary komunikatów żądań i odpowiedzi zostały zbudowane, aby ułatwić Ci wiadomości w formacie poprawnie.

Usługa Azure AD można skonfigurować do pracy z dostawcami tożsamości, które za pomocą profilu SAML 2.0 SP Lite niektóre szczególne wymagania wymienione poniżej. Korzystając z przykładowych SAML komunikatów żądań i odpowiedzi oraz zautomatyzowane i ręczne testowanie, możesz podjąć osiągnąć współdziałanie z usługą Azure AD.

## <a name="signature-block-requirements"></a>Wymagania dotyczące blok podpisu
W komunikacie odpowiedzi SAML węzła podpis zawiera informacje o podpisu cyfrowego dla samej wiadomości. Blok podpisu ma następujące wymagania:

1. Sam węzeł potwierdzenia musi być podpisany.
2.  Algorytm RSA sha1 muszą być używane jako Metoda DigestMethod. Inne algorytmy podpisu cyfrowego nie są akceptowane.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Mogą również podpisać dokument XML. 
4.  Przekształcanie algorytm musi odpowiadać wartości w następującym przykładzie:    `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Algorytm elementu SignatureMethod musi odpowiadać następującym przykładzie:   `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Obsługiwane powiązania
Powiązania są parametry informacji dotyczących transportu, które są wymagane. Poniższe wymagania dotyczą powiązania

1. Protokół HTTPS jest wymagana transportu.
2.  Usługa Azure AD będzie wymagać żądania HTTP POST do przesłania tokenu podczas logowania
3.  Usługi Azure AD będzie używać żądania HTTP POST dla żądania uwierzytelniania do dostawcy tożsamości i Przekierowanie wiadomości wylogowywania dostawcy tożsamości.

## <a name="required-attributes"></a>Wymaganych atrybutów
W poniższej tabeli przedstawiono wymagania dotyczące określonych atrybutów w komunikacie SAML 2.0.
 
|Atrybut|Opis|
| ----- | ----- |
|NameID|Wartość ta asercja musi być taka sama jak wartość ImmutableID użytkownika usługi Azure AD. Może być maksymalnie 64 znaki alfanumeryczne. Bezpieczne znaków innych niż html, które muszą zostać zakodowane, na przykład znak "+" jest wyświetlany jako ".2B".|
|IDPEmail|Nazwa główna użytkownika (UPN) znajduje się w odpowiedzi SAML jako elementu o nazwie IDPEmail UserPrincipalName użytkownika (UPN) w usłudze Azure AD/Office 365. Nazwa UPN jest w formacie adresu e-mail. Wartość nazwy UPN w usłudze Office 365 Windows (Azure Active Directory).|
|Wystawca|Musi być identyfikatorem URI dostawcy tożsamości. Nie należy używać ponownie wystawcy z wiadomości próbki. Jeśli masz wiele domen najwyższego poziomu w dzierżawcy usługi Azure AD wystawcy musi być zgodna z określonego ustawienia identyfikatora URI skonfigurowane dla domeny.|

>[!IMPORTANT]
>Obecnie usługa Azure AD obsługuje następujący identyfikator URI formatu NameID SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-format: trwałe.

## <a name="sample-saml-request-and-response-messages"></a>Przykładowe komunikaty żądań i odpowiedzi SAML
Para komunikatów żądań i odpowiedzi jest wyświetlany w wymianie wiadomości logowania jednokrotnego.
Oto przykładowy komunikat żądania, który są wysyłane z usługi Azure AD do dostawcy tożsamości SAML 2.0 próbki. Dostawcy tożsamości SAML 2.0 próbki jest Active Directory Federation Services (AD FS) skonfigurowany do używania protokołu SAML-P. Współdziałanie część testowania została ukończona również z innymi dostawcami tożsamości SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Poniżej przedstawiono przykładowy komunikat odpowiedzi, który jest wysyłany z dostawcy zgodne tożsamości SAML 2.0 próbki do usługi Azure AD / usługi Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
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

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurowanie dostawcy tożsamości zgodnych SAML 2.0
Ta sekcja zawiera wskazówki dotyczące konfigurowania dostawcy tożsamości SAML 2.0 można utworzyć Federację z usługą Azure AD, aby umożliwić dostęp pojedynczego logowania jednokrotnego jeden lub więcej usług chmurowych firmy Microsoft (takich jak Office 365) przy użyciu protokołu SAML 2.0. SAML 2.0 jednostki zależnej dla usługi w chmurze firmy Microsoft używane w tym scenariuszu jest usługa Azure AD.

## <a name="add-azure-ad-metadata"></a>Dodaj metadane do usługi Azure AD
Dostawcy tożsamości SAML 2.0 musi stosować się do informacji na temat usługi Azure AD jednostki zależnej. Usługa Azure AD publikuje metadane na https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Zalecane jest, zawsze zaimportować najnowszych metadane usługi Azure AD podczas konfigurowania dostawcy tożsamości SAML 2.0.

>[!NOTE]
>Usługa Azure AD nie odczytuje metadanych od dostawcy tożsamości.

## <a name="add-azure-ad-as-a-relying-party"></a>Dodawanie usługi Azure AD jako jednostki uzależnionej
Należy włączyć komunikację między dostawcy tożsamości SAML 2.0 i usługi Azure AD. Ta konfiguracja będzie zależny od dostawcy określonego tożsamości i powinni zapoznać się z dokumentacją dla niego. Identyfikator jednostki uzależnionej strona będzie zazwyczaj ustawiona na taki sam jak identyfikator jednostki, z metadanych usługi Azure AD.

>[!NOTE]
>Sprawdź, czy zegar na serwerze dostawcy tożsamości SAML 2.0 jest synchronizowany ze źródłem dokładnego czasu. Niedokładny czas może spowodować federacyjnego logowania nie powiedzie się.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instalowanie programu Windows PowerShell dla logowania jednokrotnego za pomocą dostawcy tożsamości SAML 2.0
Po skonfigurowaniu dostawcy tożsamości SAML 2.0 do użytku z logowania jednokrotnego usługi Azure AD, następnym krokiem jest pobranie i zainstalowanie usługi Azure Active Directory modułu dla Windows PowerShell. Po zainstalowaniu użyje tych poleceń cmdlet, skonfigurowanie usługi Azure AD jako domen federacyjnych.

Azure Active Directory Module for Windows PowerShell to pobierany program do zarządzania danymi organizacji w usłudze Azure AD. Ten moduł instaluje zestaw poleceń cmdlet programu Windows PowerShell; Uruchom te polecenia cmdlet, aby skonfigurować pojedynczy znak na dostęp do usługi Azure AD, a następnie pozycji do wszystkich usług w chmurze subskrybowanym przez Ciebie. Aby uzyskać instrukcje dotyczące sposobu pobierania i instalowania poleceń cmdlet zobacz [https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Konfigurowanie relacji zaufania między dostawcy tożsamości SAML i usługa Azure AD
Przed rozpoczęciem konfigurowania Federacji dla domeny usługi Azure AD, musi istnieć w skonfigurowanej domenie niestandardowej. Nie można sfederować domeny domyślnej, dostarczanej przez firmę Microsoft. Domyślna domena firmy Microsoft kończy się ciągiem "onmicrosoft.com".
Uruchom serię poleceń cmdlet w interfejsie wiersza polecenia programu Windows PowerShell do dodawania lub przekonwertować domen dla logowania jednokrotnego.

Każdej domeny usługi Azure Active Directory, który chcesz utworzyć Federację przy użyciu dostawcy tożsamości SAML 2.0 muszą być dodane to pojedyncza domena logowania jednokrotnego lub przekonwertować na jednej domenie logowania jednokrotnego z standardowej w domenę. Dodawanie lub Przekształcanie domeny ustanawia relację zaufania między dostawcy tożsamości SAML 2.0 i usługi Azure AD.

Poniższa procedura przeprowadzi Cię przez Konwertowanie istniejącej domeny standardowy do domeny federacyjnej przy użyciu protokołu SAML 2.0 SP-Lite. 

>[!NOTE]
>Domeny może wystąpić awaria, który wpływa na użytkowników do 2 godzin po wykonaniu tego kroku.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Skonfigurowanie domeny w katalogu usługi Azure AD dla Federacji


1. Łączenie do katalogu usługi Azure AD jako administrator dzierżawy: Connect-MsolService.
2.  Skonfiguruj domenę usługi Office 365 żądaną Federacji za pomocą języka SAML 2.0: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Ciąg zakodowany w formacie base64 podpisywania certyfikatu można uzyskać od dostawcy tożsamości pliku metadanych. Przykładem tej lokalizacji został dostarczony, ale może różnić się nieco w zależności od implementacji.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Aby uzyskać więcej informacji na temat "Set-MsolDomainAuthentication", zobacz: [ https://technet.microsoft.com/library/dn194112.aspx ](https://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Należy uruchomić użyj `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` tylko wtedy, gdy Konfigurowanie rozszerzenia ECP dla dostawcy tożsamości. Klienci usługi Exchange Online, z wyjątkiem aplikacji sieci Web programu Outlook (OWA) polegają na ogłoszenie na podstawie aktywnego punktu końcowego. Jeśli z SAML 2.0 STS implementuje aktywny punkt końcowy podobne do wykonania ECP firmy Shibboleth aktywny punkt końcowy może być możliwe w przypadku tych zaawansowanych klientów do interakcji z usługą Exchange Online.

Po skonfigurowaniu Federacji możesz przełączyć się ponownie do "inne niż federacyjne" (lub "zarządzany"), jednak ta zmiana ta zajmuje nawet do dwóch godzin, i wymaga, przypisywanie nowego losowych haseł oparte na chmurze logowania do poszczególnych użytkowników. Przełączanie do "zarządzany" może być konieczne w niektórych scenariuszach resetowania błąd w ustawieniach. Aby uzyskać więcej informacji o konwersji domeny Zobacz: [ https://msdn.microsoft.com/library/windowsazure/dn194122.aspx ](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Aprowizowanie podmiotami zabezpieczeń użytkownika do usługi Azure AD / usługi Office 365
Zanim można uwierzytelniać użytkowników do usługi Office 365, możesz aprowizować usługi Azure AD z podmiotami zabezpieczeń użytkownika, które odpowiadają potwierdzenie oświadczenia języka SAML 2.0. Jeśli te podmiotami zabezpieczeń użytkownika nie są znane z usługą Azure AD z wyprzedzeniem, następnie nie można ich używać do logowania federacyjnego. Program Azure AD Connect lub środowiska Windows PowerShell może służyć do udostępniania podmiotami zabezpieczeń użytkownika.

Program Azure AD Connect może służyć do udostępniania podmiotów zabezpieczeń do domen w katalogu usługi Azure AD z lokalnej usługi Active Directory. Aby uzyskać więcej informacji, zobacz [integrowanie katalogów lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

Program Windows PowerShell można również zautomatyzować Dodawanie nowych użytkowników do usługi Azure AD i zsynchronizować zmiany z katalogu lokalnego. Aby używać poleceń cmdlet programu Windows PowerShell, należy pobrać [usługi Azure Active Directory modułów](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Ta procedura pokazuje, jak dodać pojedynczego użytkownika do usługi Azure AD.


1. Łączenie do katalogu usługi Azure AD jako administrator dzierżawy: Connect-MsolService.
2.  Utwórz nową nazwę główną użytkownika:
    ```powershell
    New-MsolUser
      -UserPrincipalName elwoodf1@contoso.com
      -ImmutableId ABCDEFG1234567890
      -DisplayName "Elwood Folk"
      -FirstName Elwood 
      -LastName Folk 
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
      -UsageLocation "US" 
    ```

Aby uzyskać więcej informacji na temat wyewidencjonowania "New-MsolUser" [https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>Wartość "UserPrinciplName" musi być zgodna wartość, która zostanie wysłana do "IDPEmail" w swojej oświadczenia języka SAML 2.0, a wartość "ImmutableID" musi być zgodna wartość wysyłana w swojej asercji "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Sprawdź logowanie jednokrotne za pomocą dostawcy tożsamości SAML 2.0
Jako administrator można było sprawdzić i zarządzanie logowania jednokrotnego (zwanego Federacją tożsamości), zapoznaj się z informacjami i wykonaj kroki się z następującymi artykułami, aby skonfigurować logowanie jednokrotne przy użyciu protokołu SAML 2.0 SP-Lite dostawcy tożsamości oparte na:


1.  Należy przejrzeć wymagania protokołu SAML programu Azure AD w wersji 2.0
2.  Skonfigurowano dostawcy tożsamości SAML 2.0
3.  Instalowanie programu Windows PowerShell dla logowania jednokrotnego za pomocą dostawcy tożsamości SAML 2.0
4.  Konfigurowanie relacji zaufania między dostawcy tożsamości SAML 2.0 i usługi Azure AD
5.  Zainicjowano obsługę administracyjną głównej nazwy użytkownika znanych testu do usługi Azure Active Directory (Usługa Office 365), za pomocą programu Windows PowerShell lub Azure AD Connect.
6.  Konfigurowanie synchronizacji katalogu przy użyciu [program Azure AD Connect](whatis-hybrid-identity.md).

Po skonfigurowaniu logowania jednokrotnego za pomocą dostawcy tożsamości SAML 2.0 SP-Lite na podstawie, należy sprawdzić, czy działa poprawnie.

>[!NOTE]
>Przekonwertowanie domenę, a nie dodanie jednego może potrwać do 24 godzin, aby skonfigurować logowanie jednokrotne.
Przed werytfikacją logowanie jednokrotne, należy zakończyć konfigurowanie synchronizacji usługi Active Directory, synchronizacji katalogów i Aktywuj zsynchronizowanych użytkowników.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Użyj narzędzia, aby sprawdzić, czy ten logowanie jednokrotne zostało skonfigurowane prawidłowo
Aby sprawdzić, czy ten logowanie jednokrotne zostało skonfigurowane prawidłowo, można wykonać następującą procedurę, aby potwierdzić, że jesteś w stanie zalogować się do usługi w chmurze przy użyciu swoich poświadczeń firmowych.

Firma Microsoft udostępnia narzędzia, którego można używać do testowania dostawcy na podstawie tożsamości SAML 2.0. Przed uruchomieniem narzędzia test, należy skonfigurować dzierżawy usługi Azure AD w celu sfederowania u dostawcy tożsamości.

>[!NOTE]
>Connectivity Analyzer wymaga programu Internet Explorer 10 lub nowszym.



1. Pobierz Connectivity Analyzer, [ https://testconnectivity.microsoft.com/?tabid=Client ](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Kliknij przycisk Zainstaluj teraz, aby rozpocząć pobieranie i instalowanie narzędzia.
3.  Wybierz opcję "Nie można skonfigurować Federacja z usługą Office 365, Azure lub innych usług używających usługi Azure Active Directory".
4.  Po pobraniu narzędzia i uruchomiona, zostanie wyświetlony w oknie diagnostyki łączności. Narzędzie będzie przejrzeć Trwa testowanie połączenia usługi federacyjnej.
5.  Analizator połączeń zostanie otwarty dostawcy tożsamości SAML 2.0 można się zalogować, wprowadź poświadczenia dla głównej nazwy użytkownika, które testujesz: ![SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  Test logowania federacyjnego okna należy wprowadzić nazwę konta i hasło dla dzierżawy usługi Azure AD, który jest skonfigurowany do sfederowania u dostawcy tożsamości SAML 2.0. Narzędzie spróbuje Zaloguj się przy użyciu tych poświadczeń, a szczegółowe wyniki testów wykonywanych podczas próby logowania, zostanie podana jako dane wyjściowe.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. To okno zawiera zakończonych niepowodzeniem wyników testów. Kliknięcie na przeglądanie szczegółowe wyniki są wyświetlane informacje o wyniki dla każdego testu, który zostało wykonane. Można także zapisać wyniki na dysku w celu ich udostępnienia.
 
>[!NOTE]
>Connectivity analyzer sprawdza również aktywne w ramach federacji przy użyciu protokołu WS *-zależności i ECP/PAOS protokołów. Jeśli nie używasz tych możesz pominąć następujący błąd: Testowanie aktywny przepływ logowania przy użyciu punktu końcowego aktywne w ramach federacji dostawcy tożsamości.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ręcznie Sprawdź, czy ten logowanie jednokrotne zostało skonfigurowane prawidłowo
Weryfikacja ręczna udostępnia dodatkowe kroki, które można podjąć w celu zapewnienia, że dostawca tożsamości SAML 2.0 działa prawidłowo w wielu scenariuszach.
Aby sprawdzić, czy ten logowanie jednokrotne zostało skonfigurowane prawidłowo, wykonaj następujące czynności:


1. Na komputerze przyłączonym do domeny Zaloguj się do usługi w chmurze przy użyciu tej samej nazwie logowania, której użyjesz dla poświadczeń firmowych.
2.  Kliknij wewnątrz pola hasła. Jeśli logowanie jednokrotne jest skonfigurowana, pole hasło będzie przyciemnione i zostanie wyświetlony następujący komunikat: "Teraz należy zalogować się na &lt;firmie&gt;."
3.  Kliknij przycisk Zaloguj się na &lt;firmie&gt; łącza. Jeśli jest to możliwe do logowania, następnie pojedynczego logowania jednokrotnego została prawidłowo skonfigurowana.

## <a name="next-steps"></a>Następne kroki


- [Zarządzania w usłudze Active Directory Federation Services i dostosowywania za pomocą usługi Azure AD Connect](how-to-connect-fed-management.md)
- [Lista zgodności usługi Azure AD z usługami federacyjnymi](how-to-connect-fed-compatibility.md)
- [Instalację niestandardową programu Azure AD Connect](how-to-connect-install-custom.md)
