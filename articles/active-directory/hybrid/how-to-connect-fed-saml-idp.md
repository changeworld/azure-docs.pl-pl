---
title: 'Usługa Azure AD Connect: używanie dostawcy tożsamości SAML 2.0 do logowania jednokrotnego — platforma Azure'
description: W tym dokumencie opisano przy użyciu SAML 2.0 zgodny identyfikator Idp dla logowania jednokrotnego na.
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
ms.openlocfilehash: ff4f1b81f476159c13d3725cd3cb1766aec7dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70305111"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Korzystanie z dostawcy tożsamości SAML 2.0 na potrzeby logowania jednokrotnego

Ten dokument zawiera informacje na temat korzystania z dostawcy tożsamości opartego na profilu SP-Lite zgodnego ze standardem SAML 2.0 jako preferowanej usługi tokenów zabezpieczających (STS) / dostawcy tożsamości. Ten scenariusz jest przydatny, gdy masz już katalog użytkowników i przechowywanie haseł lokalnie, do których można uzyskać dostęp przy użyciu saml 2.0. Ten istniejący katalog użytkowników może służyć do logowania do usługi Office 365 i innych zasobów zabezpieczonych usługą Azure AD. Profil SAML 2.0 SP-Lite jest oparty na powszechnie używanym standardzie tożsamości federacyjnej języka sfederowanej (SAML) zabezpieczeń, aby zapewnić platformę wymiany logowania i atrybutów.

>[!NOTE]
>Aby uzyskać listę identyfikatorów innych firm, które zostały przetestowane do użycia z usługą Azure AD, zobacz [listę zgodności federacji usługi Azure AD](how-to-connect-fed-compatibility.md)

Firma Microsoft obsługuje to środowisko logowania jako integrację usługi w chmurze firmy Microsoft, takiej jak Office 365, z prawidłowo skonfigurowanym idP opartym na profilu SAML 2.0. Dostawcy tożsamości SAML 2.0 są produktami innych firm i dlatego firma Microsoft nie zapewnia pomocy technicznej dotyczącej wdrażania, konfiguracji, rozwiązywania problemów z najlepszymi rozwiązaniami dotyczącymi tych usług. Po prawidłowym skonfigurowaniu integracja z dostawcą tożsamości SAML 2.0 może zostać przetestowana pod kątem prawidłowej konfiguracji za pomocą narzędzia Microsoft Connectivity Analyzer Tool, które jest opisane bardziej szczegółowo poniżej. Aby uzyskać więcej informacji na temat dostawcy tożsamości opartego na profilu SAML 2.0 SP-Lite, zapytaj organizację, która go dostarczyła.

> [!IMPORTANT]
> Tylko ograniczony zestaw klientów są dostępne w tym scenariuszu logowania z SAML 2.0 dostawców tożsamości, obejmuje to:
> 
> - Klienci sieci Web, tacy jak Outlook Web Access i SharePoint Online
> - Klienci bogaci w wiadomości e-mail, którzy używają uwierzytelniania podstawowego i obsługiwanej metody dostępu programu Exchange, takiej jak IMAP, POP, Active Sync, MAPI itp.
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (różne wersje systemu iOS)
>     - Różne urządzenia Google z Androidem
>     - Windows Phone 7, Windows Phone 7.8 i Windows Phone 8.0
>     - Klient poczty systemu Windows 8 i klient poczty systemu Windows 8.1
>     - Klient poczty systemu Windows 10

Wszyscy inni klienci nie są dostępne w tym scenariuszu logowania z dostawcą tożsamości SAML 2.0. Na przykład klient pulpitu programu Lync 2010 nie może zalogować się do usługi za pomocą dostawcy tożsamości SAML 2.0 skonfigurowanym do logowania jednokrotnego.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Wymagania dotyczące protokołu saml usługi Azure AD 2.0
Ten dokument zawiera szczegółowe wymagania dotyczące formatowania protokołu i wiadomości, które dostawca tożsamości SAML 2.0 musi zaimplementować w celu federacji z usługą Azure AD, aby włączyć logowanie do jednej lub więcej usług w chmurze firmy Microsoft (takich jak Office 365). Jednostka uzależniająca SAML 2.0 (SP-STS) dla usługi w chmurze firmy Microsoft używanej w tym scenariuszu to usługa Azure AD.

Zaleca się, aby upewnić się, że komunikaty wyjściowe dostawcy tożsamości SAML 2.0 są tak podobne do podanych śladów przykładu, jak to możliwe. Ponadto należy użyć określonych wartości atrybutów z dostarczonych metadanych usługi Azure AD, jeśli to możliwe. Gdy będziesz zadowolony z komunikatów wyjściowych, możesz przetestować za pomocą analizatora łączności firmy Microsoft, jak opisano poniżej.

Metadane usługi Azure AD można pobrać [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml)z tego adresu URL: .
W przypadku klientów w Chinach korzystających z wystąpienia usługi Office 365 [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml)dla specyficznych dla Chin należy użyć następującego punktu końcowego federacji: .

## <a name="saml-protocol-requirements"></a>Wymagania dotyczące protokołu SAML
W tej sekcji opisano, jak pary żądań i odpowiedzi wiadomości są połączone w celu pomocy w prawidłowym formatowaniu wiadomości.

Usługi Azure AD można skonfigurować do pracy z dostawcami tożsamości, którzy używają profilu SAML 2.0 SP Lite z pewnymi określonymi wymaganiami, jak podano poniżej. Przy użyciu przykładowych komunikatów żądania i odpowiedzi SAML wraz z automatycznym i ręcznym testowaniem, można pracować, aby osiągnąć współdziałanie z usługą Azure AD.

## <a name="signature-block-requirements"></a>Wymagania dotyczące bloków podpisu
W komunikacie odpowiedzi SAML węzeł Podpis zawiera informacje o podpisie cyfrowym dla samej wiadomości. Blok podpisu ma następujące wymagania:

1. Sam węzeł potwierdzenia musi być podpisany
2.  Algorytm RSA-sha1 musi być używany jako Metody DigestM. Inne algorytmy podpisu cyfrowego nie są akceptowane.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Można również podpisać dokument XML. 
4.  Algorytm przekształcania musi być zgodny z wartościami w poniższym przykładzie:`<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Algorytm metody podpisu musi być zgodny z następującym przykładem:`<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Obsługiwane powiązania
Powiązania są parametry komunikacji związane z transportem, które są wymagane. Następujące wymogi mają zastosowanie do wiązań

1. HTTPS jest wymagany transport.
2.  Usługa Azure AD będzie wymagać protokołu HTTP POST do przesłania tokenu podczas logowania.
3.  Usługa Azure AD użyje protokołu HTTP POST dla żądania uwierzytelniania do dostawcy tożsamości i przekierowania dla komunikatu wylogowania do dostawcy tożsamości.

## <a name="required-attributes"></a>Wymagane atrybuty
W tej tabeli przedstawiono wymagania dotyczące określonych atrybutów w komunikacie SAML 2.0.
 
|Atrybut|Opis|
| ----- | ----- |
|NazwaID|Wartość tego potwierdzenia musi być taka sama jak immutableID użytkownika usługi Azure AD. Może to być maksymalnie 64 znaki alfanumeryczne. Wszystkie znaki inne niż html muszą być zakodowane, na przykład znak "+" jest wyświetlany jako ".2B".|
|Poczta elektroniczna IDPE|Główna nazwa użytkownika (UPN) jest wymieniona w odpowiedzi SAML jako element o nazwie IDPEmail UserPrincipalName (UPN) w usłudze Azure AD/Office 365. Upn jest w formacie adresu e-mail. Wartość UPN w usłudze Windows Office 365 (usługa Azure Active Directory).|
|Wystawca|Wymagane do identyfikatora URI dostawcy tożsamości. Nie należy ponownie używać Emitenta z przykładowych komunikatów. Jeśli masz wiele domen najwyższego poziomu w dzierżawach usługi Azure AD, wystawca musi być zgodny z określonym ustawieniem identyfikatora URI skonfigurowanym dla domeny.|

>[!IMPORTANT]
>Usługa Azure AD obsługuje obecnie następujący identyfikator URI formatu NameID dla SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-format:persistent.

## <a name="sample-saml-request-and-response-messages"></a>Przykładowe komunikaty o żądaniu i odpowiedzi SAML
Para wiadomości żądania i odpowiedzi jest wyświetlana dla wymiany wiadomości logowania.
Poniżej przedstawiono przykładowy komunikat żądania, który jest wysyłany z usługi Azure AD do przykładowego dostawcy tożsamości SAML 2.0. Przykładowym dostawcą tożsamości SAML 2.0 są usługi federacyjne Active Directory (AD FS) skonfigurowane do używania protokołu SAML-P. Testy współdziałania zostały również zakończone z innymi dostawcami tożsamości SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Poniżej przedstawiono przykładowy komunikat odpowiedzi, który jest wysyłany z przykładowego dostawcy tożsamości zgodnych z SAML 2.0 do usługi Azure AD / Office 365.

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

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurowanie dostawcy tożsamości zgodnego ze standardem SAML 2.0
Ta sekcja zawiera wskazówki dotyczące konfigurowania dostawcy tożsamości SAML 2.0 do łączeniu z usługą Azure AD, aby umożliwić dostęp do logowania jednokrotnego do jednej lub więcej usług w chmurze firmy Microsoft (takich jak Office 365) przy użyciu protokołu SAML 2.0. Jednostka uzależniająca SAML 2.0 dla usługi w chmurze firmy Microsoft używanej w tym scenariuszu to Usługa Azure AD.

## <a name="add-azure-ad-metadata"></a>Dodawanie metadanych usługi Azure AD
Dostawca tożsamości SAML 2.0 musi przestrzegać informacji o stronie uzależniającej usługi Azure AD. Usługa Azure AD publikuje https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlmetadane w pliku .

Zaleca się, aby zawsze importować najnowsze metadane usługi Azure AD podczas konfigurowania dostawcy tożsamości SAML 2.0.

>[!NOTE]
>Usługa Azure AD nie odczytuje metadanych od dostawcy tożsamości.

## <a name="add-azure-ad-as-a-relying-party"></a>Dodawanie usługi Azure AD jako jednostki uzależniającej
Należy włączyć komunikację między dostawcą tożsamości SAML 2.0 a usługą Azure AD. Ta konfiguracja będzie zależeć od określonego dostawcy tożsamości i należy zapoznać się z dokumentacją dla niego. Zazwyczaj można ustawić identyfikator jednostki uzależniona na taki sam jak entityID z metadanych usługi Azure AD.

>[!NOTE]
>Sprawdź, czy zegar na serwerze dostawcy tożsamości SAML 2.0 jest zsynchronizowany z dokładnym źródłem czasu. Niedokładny czas zegara może spowodować niepowodzenie logowania federacyjnego.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instalowanie programu Windows PowerShell do logowania się za pomocą dostawcy tożsamości SAML 2.0
Po skonfigurowaniu dostawcy tożsamości SAML 2.0 do użytku z loganiem usługi Azure AD następnym krokiem jest pobranie i zainstalowanie modułu usługi Azure Active Directory dla programu Windows PowerShell. Po zainstalowaniu te polecenia cmdlet będą używane do konfigurowania domen usługi Azure AD jako domen federacyjnej.

Moduł usługi Azure Active Directory dla programu Windows PowerShell jest pobieranie do zarządzania danymi organizacji w usłudze Azure AD. Ten moduł instaluje zestaw poleceń cmdlet do programu Windows PowerShell; uruchamiasz te polecenia cmdlet, aby skonfigurować dostęp do logowania jednokrotnego do usługi Azure AD, a z kolei do wszystkich usług w chmurze, które są subskrybowane. Aby uzyskać instrukcje dotyczące pobierania i instalowania poleceń cmdlet, zobacz[https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Konfigurowanie zaufania między dostawcą tożsamości SAML a usługą Azure AD
Przed skonfigurowaniem federacji w domenie usługi Azure AD musi ona mieć skonfigurowaną domenę niestandardową. Nie można połączyć domeny domyślnej dostarczonej przez firmę Microsoft. Domyślna domena firmy Microsoft kończy się na "onmicrosoft.com".
Uruchomisz serię poleceń cmdlet w interfejsie wiersza polecenia programu Windows PowerShell, aby dodać lub przekonwertować domeny do logowania jednokrotnego.

Każda domena usługi Azure Active Directory, którą chcesz sferaniować przy użyciu dostawcy tożsamości SAML 2.0, musi zostać dodana jako domena logowania jednokrotnego lub przekonwertowana na domenę logowania jednokrotnego z domeny standardowej. Dodawanie lub konwertowanie domeny konfiguruje zaufanie między dostawcą tożsamości SAML 2.0 a usługą Azure AD.

Poniższa procedura przeprowadzi Cię przez konwersję istniejącej domeny standardowej do domeny federacyjnej przy użyciu SAML 2.0 SP-Lite. 

>[!NOTE]
>Twoja domena może wystąpić awaria, która wpływa na użytkowników do 2 godzin po tym kroku.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Konfigurowanie domeny w katalogu usługi Azure AD dla federacji


1. Połącz się z katalogiem usługi Azure AD jako administrator dzierżawy: Connect-MsolService .
2.  Skonfiguruj żądaną domenę usługi Office 365 tak, aby używała federacji z saml 2.0:`$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $BrandName -Authentication Federated -PassiveLogOnUri $LogOnUrl -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $MyURI -LogOffUri $LogOffUrl -PreferredAuthenticationProtocol $Protocol` 

3.  Ciąg kodowany certyfikatu 64 można uzyskać z pliku metadanych protokołu IDP. Przykład tej lokalizacji został dostarczony, ale może się nieznacznie różnić w zależności od implementacji.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Aby uzyskać więcej informacji na temat "Set-MsolDomainAuthentication", zobacz: [https://technet.microsoft.com/library/dn194112.aspx](https://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Należy uruchomić `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` używać tylko wtedy, gdy skonfigurowane rozszerzenie ECP dla dostawcy tożsamości. Klienci usługi Exchange Online, z wyłączeniem aplikacji Outlook Web Application (OWA), polegają na aktywnym punkcie końcowym opartym na post. Jeśli saml 2.0 STS implementuje aktywny punkt końcowy podobny do implementacji ECP Shibboleth aktywnego punktu końcowego może być możliwe dla tych bogatych klientów do interakcji z usługą Exchange Online.

Po skonfigurowaniu federacji można przełączyć się z powrotem do "niefederowanych" (lub "zarządzane"), jednak ta zmiana trwa do dwóch godzin, aby zakończyć i wymaga przypisania nowych losowych haseł do logowania w chmurze do każdego użytkownika. W niektórych scenariuszach może być wymagane przełączenie z powrotem na "zarządzane", aby zresetować błąd w ustawieniach. Aby uzyskać więcej informacji [https://msdn.microsoft.com/library/windowsazure/dn194122.aspx](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx)na temat konwersji domeny, zobacz: .

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Aprowizuj podmioty zabezpieczeń użytkowników do usługi Azure AD / Office 365
Aby można było uwierzytelnić użytkowników do usługi Office 365, należy aprowizować usługę Azure AD z podmiotami zabezpieczeń użytkownika, które odpowiadają twierdzeniu w żądaniu SAML 2.0. Jeśli te podmioty użytkownika nie są znane usługi Azure AD z wyprzedzeniem, nie można ich używać do logowania federacyjnego. Usługi Azure AD Connect lub programu Windows PowerShell mogą być używane do inicjowania obsługi administracyjnej podmiotów zabezpieczeń.

Usługa Azure AD Connect może służyć do inicjowania obsługi administracyjnej domen w katalogu usługi Azure AD z lokalnej usługi Active Directory. Aby uzyskać bardziej szczegółowe informacje, zobacz [Integrowanie katalogów lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

Program Windows PowerShell może również służyć do automatyzacji dodawania nowych użytkowników do usługi Azure AD i synchronizowania zmian z katalogu lokalnego. Aby korzystać z poleceń cmdlet programu Windows PowerShell, należy pobrać [moduły usługi Azure Active Directory](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

W tej procedurze pokazano, jak dodać jednego użytkownika do usługi Azure AD.


1. Połącz się z katalogiem usługi Azure AD jako administrator dzierżawy: Connect-MsolService.
2.  Utwórz nowego podmiotu zabezpieczeń użytkownika:
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

Aby uzyskać więcej informacji na temat kasy "New-MsolUser",[https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>Wartość "UserPrinciplName" musi być zgodna z wartością, którą wyślesz dla "IDPEmail" w swoim żądaniu SAML 2.0, a wartość "ImmutableID" musi być zgodna z wartością wysłaną w asanżsacji "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Weryfikowanie logowania jednokrotnego za pomocą protokołu TYLKOG SAML 2.0
Jako administrator, zanim sprawdzisz i zarządzasz logiem jednokrotnym (nazywanym także federacją tożsamości), przejrzyj informacje i wykonaj kroki opisane w następujących artykułach, aby skonfigurować logowanie jednokrotne za pomocą dostawcy tożsamości opartego na dodatku SP-Lite:


1.  Zapoznałeś się z wymaganiami protokołu SAML 2.0 usługi Azure AD
2.  Skonfigurowano dostawcę tożsamości SAML 2.0
3.  Instalowanie programu Windows PowerShell do logowania jednokrotnego za pomocą dostawcy tożsamości SAML 2.0
4.  Konfigurowanie zaufania między dostawcą tożsamości SAML 2.0 a usługą Azure AD
5.  Aprowizowana znana jednostkowa użytkowniczka testowa do usługi Azure Active Directory (Office 365) za pośrednictwem programu Windows PowerShell lub usługi Azure AD Connect.
6.  Konfigurowanie synchronizacji katalogów przy użyciu [usługi Azure AD Connect](whatis-hybrid-identity.md).

Po skonfigurowaniu logowania jednokrotnego u dostawcy tożsamości opartego na SP-Lite saml 2.0 należy sprawdzić, czy działa poprawnie.

>[!NOTE]
>Jeśli domena została przekonwertowana, a nie dodajesz ją, skonfigurowanie logowania jednokrotnego może potrwać do 24 godzin.
Przed weryfikacją logowania jednokrotnego należy zakończyć konfigurowanie synchronizacji usługi Active Directory, zsynchronizować katalogi i aktywować zsynchronizowanych użytkowników.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Użyj narzędzia, aby sprawdzić, czy logowanie jednokrotne zostało poprawnie skonfigurowane
Aby sprawdzić, czy logowanie jednokrotne zostało poprawnie skonfigurowane, można wykonać następującą procedurę, aby potwierdzić, że można zalogować się do usługi w chmurze przy użyciu poświadczeń firmowych.

Firma Microsoft dostarczyła narzędzie, którego można użyć do testowania dostawcy tożsamości opartego na saml 2.0. Przed uruchomieniem narzędzia testowego musisz skonfigurować dzierżawę usługi Azure AD, aby ujednolicić z dostawcą tożsamości.

>[!NOTE]
>Analizator łączności wymaga programu Internet Explorer 10 lub nowszego.



1. Pobierz analizator łączności z [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Kliknij przycisk Zainstaluj teraz, aby rozpocząć pobieranie i instalowanie narzędzia.
3.  Wybierz opcję "Nie mogę skonfigurować federacji przy użyciu usługi Office 365, platformy Azure lub innych usług korzystających z usługi Azure Active Directory".
4.  Po pobraniu i uruchomieniu narzędzia zostanie wyświetlone okno Diagnostyka łączności. Narzędzie przejdzie do testowania połączenia federacyjnego.
5.  Analizator łączności otworzy dpd SAML 2.0, aby się zalogować, wprowadź poświadczenia dla testowana ![przez Ciebie jednostki użytkownika: SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  W oknie logowania testu federacji należy wprowadzić nazwę konta i hasło dla dzierżawy usługi Azure AD, która jest skonfigurowana do federacyjnego z dostawcą tożsamości SAML 2.0. Narzędzie podejmie próbę zalogowania się przy użyciu tych poświadczeń i szczegółowe wyniki testów przeprowadzonych podczas próby logowania zostaną dostarczone jako dane wyjściowe.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. W tym oknie przedstawiono nieudany wynik testów. Kliknięcie na Przejrzyj szczegółowe wyniki pokaże informacje o wynikach każdego testu, który został wykonany. Można również zapisać wyniki na dysku, aby je udostępnić.
 
>[!NOTE]
>Analizator łączności testuje również aktywną federację przy użyciu protokołów opartych na ws*i ECP/PAOS. Jeśli nie używasz tych można pominąć następujący błąd: Testowanie aktywnego przepływu logowania przy użyciu punktu końcowego aktywnej federacji dostawcy tożsamości.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ręczna weryfikacja, czy logowanie jednokrotne zostało poprawnie skonfigurowane
Ręczna weryfikacja zapewnia dodatkowe kroki, które można wykonać, aby upewnić się, że dostawca tożsamości SAML 2.0 działa poprawnie w wielu scenariuszach.
Aby sprawdzić, czy logowanie jednokrotne zostało poprawnie skonfigurowane, wykonaj następujące kroki:


1. Na komputerze przyłączanym do domeny zaloguj się do usługi w chmurze przy użyciu tej samej nazwy logowania, której używasz dla poświadczeń firmowych.
2.  Kliknij wewnątrz pola hasła. Jeśli skonfigurowano logowanie jednokrotne, pole hasła zostanie zacieniowane, a zostanie wyświetlony następujący komunikat: &lt;"Musisz&gt;teraz zalogować się w firmie."
3.  Kliknij link Zaloguj &lt;się&gt; w firmie. Jeśli możesz się zalogować, skonfigurowano logowanie jednokrotne.

## <a name="next-steps"></a>Następne kroki


- [Zarządzanie usługami federacyjnymi Active Directory i dostosowywanie za pomocą usługi Azure AD Connect](how-to-connect-fed-management.md)
- [Lista zgodności usługi Azure AD z usługami federacyjnymi](how-to-connect-fed-compatibility.md)
- [Instalacja niestandardowa usługi Azure AD Connect](how-to-connect-install-custom.md)
