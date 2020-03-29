---
title: Niestandardowe weryfikacje wiadomości e-mail
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować weryfikacyjną wiadomość e-mail wysłaną do klientów podczas rejestracji w celu korzystania z aplikacji obsługujących usługę Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671631"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Niestandardowa weryfikacja poczty e-mail w usłudze Azure Active Directory B2C

Użyj niestandardowej poczty e-mail w usłudze Azure Active Directory B2C (Azure AD B2C), aby wysłać niestandardową wiadomość e-mail do użytkowników, którzy zarejestrują się, aby korzystać z aplikacji. Korzystając z [funkcji DisplayControls](display-controls.md) (obecnie w wersji zapoznawczej) i dostawcy poczty e-mail innej firmy, można użyć własnego szablonu poczty e-mail i *od:* adresu i tematu, a także obsługi lokalizacji i niestandardowych ustawień hasła jednorazowego (OTP).

Niestandardowa weryfikacja poczty e-mail wymaga użycia zewnętrznego dostawcy poczty e-mail, takiego jak [SendGrid](https://sendgrid.com) lub [SparkPost,](https://sparkpost.com)niestandardowego interfejsu API REST lub dowolnego dostawcy poczty e-mail opartego na protokosze HTTP (w tym własnego). W tym artykule opisano konfigurowanie rozwiązania korzystającego z sendgrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Tworzenie konta SendGrid

Jeśli jeszcze go nie masz, zacznij od skonfigurowania konta SendGrid (klienci platformy Azure mogą odblokować 25 000 bezpłatnych wiadomości e-mail każdego miesiąca). Aby uzyskać instrukcje dotyczące konfiguracji, zobacz [sekcję Tworzenie konta SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) w sekcji [Jak wysyłać wiadomości e-mail przy użyciu sendgridu za pomocą platformy Azure](../sendgrid-dotnet-how-to-send-email.md).

Pamiętaj, aby ukończyć sekcję, w której [utworzysz klucz interfejsu API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Zapisz klucz interfejsu API do użycia w późniejszym kroku.

## <a name="create-azure-ad-b2c-policy-key"></a>Tworzenie klucza zasad usługi Azure AD B2C

Następnie należy przechowywać klucz interfejsu API SendGrid w kluczu zasad usługi Azure AD B2C dla zasad do odwołania.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **subskrypcja Katalogu +** w górnym menu i wybierz katalog usługi Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
1. Wybierz pozycję **Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
1. W **Options**przypadku `Manual`opcji wybierz opcję .
1. Wprowadź **nazwę** klucza zasad. Na przykład `SendGridSecret`. Prefiks `B2C_1A_` zostanie automatycznie dodany do nazwy klucza.
1. W **pliku Secret**wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. Dla **użycia klucza**wybierz opcję `Signature`.
1. Wybierz **pozycję Utwórz**.

## <a name="create-sendgrid-template"></a>Tworzenie szablonu SendGrid

Po utworzeniu konta SendGrid i kluczu interfejsu API SendGrid przechowywanym w kluczu zasad usługi Azure AD B2C należy utworzyć [szablon transakcyjny dynamiczny](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid .

1. W witrynie SendGrid otwórz stronę [szablony transakcyjne](https://sendgrid.com/dynamic_templates) i wybierz pozycję **Utwórz szablon**.
1. Wprowadź unikatową nazwę `Verification email` szablonu, a następnie wybierz pozycję **Zapisz**.
1. Aby rozpocząć edycję nowego szablonu, wybierz pozycję **Dodaj wersję**.
1. Wybierz **pozycję Edytor kodu,** a następnie **kontynuuj**.
1. W edytorze HTML wklej następujący szablon HTML lub użyj własnego. Parametry `{{otp}}` `{{email}}` i parametry zostaną zastąpione dynamicznie wartością hasła jednorazowego i adresem e-mail użytkownika.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Rozwiń **ustawienia** po lewej stronie, `{{subject}}`a w przypadku **tematu wiadomości e-mail**wprowadź .
1. Wybierz **pozycję Zapisz szablon**.
1. Wróć do strony **Szablony transakcyjne,** wybierając strzałkę wstecz.
1. Zapisz **identyfikator** szablonu utworzonego do użycia w późniejszym kroku. Na przykład `d-989077fbba9746e89f3f6411f596fb96`. Ten identyfikator należy określić podczas [dodawania transformacji oświadczeń](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Dodawanie typów oświadczeń usługi Azure AD B2C

W zasadach dodaj następujące typy `<ClaimsSchema>` oświadczeń `<BuildingBlocks>`do elementu w programie .

Te typy oświadczeń są niezbędne do generowania i weryfikowania adresu e-mail przy użyciu kodu hasła jednorazowego (OTP).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Dodawanie transformacji oświadczeń

Następnie potrzebujesz transformacji oświadczeń do wysiedlić oświadczenie ciągu JSON, które będzie treścią żądania wysłanego do SendGrid.

Struktura obiektu JSON jest zdefiniowana przez identyfikatory w notacji punktowej InputParameters i TransformationClaimTypes z InputClaims. Liczby w notacji kropkowej implikują tablice. Wartości pochodzą z InputClaims' wartości i InputParameters "Value" właściwości. Aby uzyskać więcej informacji na temat przekształceń oświadczeń JSON, zobacz [Przekształcenia oświadczeń JSON](json-transformations.md).

Dodaj następujące przekształcenia `<ClaimsTransformations>` oświadczeń `<BuildingBlocks>`do elementu w ramach . Wprowadzać następujące aktualizacje xml transformacji oświadczeń:

* Zaktualizuj `template_id` wartość InputParameter o identyfikator szablonu transakcyjnego SendGrid utworzonego wcześniej w [szablonie Utwórz sendgrid](#create-sendgrid-template).
* Zaktualizuj `from.email` wartość adresu. Użyj prawidłowego adresu e-mail, aby zapobiec oznaczaniu wiadomości e-mail weryfikacyjnej jako spamu.
* Zaktualizuj `personalizations.0.dynamic_template_data.subject` wartość parametru wejściowego wiersza tematu za pomocą wiersza tematu odpowiedniego dla twojej organizacji.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Dodawanie definicji zawartości DataUri

Poniżej przekształcenia `<BuildingBlocks>`oświadczeń w ramach , dodać następujące [ContentDefinition](contentdefinitions.md) do odwołania się do identyfikatora URI danych w wersji 2.0.0:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Tworzenie displaycontrol

Formant wyświetlania weryfikacji służy do weryfikacji adresu e-mail za pomocą kodu weryfikacyjnego, który jest wysyłany do użytkownika.

W tym przykładzie kontrolka wyświetlania jest skonfigurowana w taki sposób:

1. Zbieranie `email` typu oświadczenia adresu od użytkownika.
1. Poczekaj, aż użytkownik `verificationCode` poda typ oświadczenia z kodem wysłanym do użytkownika.
1. `email` Powrót do samodzielnie potwierdzonego profilu technicznego, który ma odwołanie do tego formantu wyświetlania.
1. Korzystając `SendCode` z akcji, wygeneruj kod OTP i wyślij do użytkownika wiadomość e-mail z kodem OTP.

![Wyślij akcję e-mail z kodem weryfikacyjnym](media/custom-email/display-control-verification-email-action-01.png)

W definicje zawartości, `<BuildingBlocks>`nadal w ramach , dodać następujące [DisplayControl](display-controls.md) typu [VerificationControl](display-control-verification.md) do zasad.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Dodawanie profili technicznych OTP

Profil `GenerateOtp` techniczny generuje kod adresu e-mail. Profil `VerifyOtp` techniczny weryfikuje kod skojarzony z adresem e-mail. Można zmienić konfigurację formatu i wygaśnięcie hasła jednorazowego. Aby uzyskać więcej informacji na temat profili technicznych OTP, zobacz [Definiowanie profilu technicznego hasła jednorazowego](one-time-password-technical-profile.md).

Dodaj następujące profile techniczne `<ClaimsProviders>` do elementu.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Dodawanie profilu technicznego interfejsu API REST

Ten profil techniczny interfejsu API REST generuje zawartość wiadomości e-mail (przy użyciu formatu SendGrid). Aby uzyskać więcej informacji na temat profili technicznych RESTful, zobacz [Definiowanie profilu technicznego RESTful](restful-technical-profile.md).

Podobnie jak w profilach technicznych OTP, dodaj `<ClaimsProviders>` do elementu następujące profile techniczne.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Odwoływanie się do DisplayControl

W ostatnim kroku dodaj odwołanie do displaycontrol utworzonego. Zastąp istniejący `LocalAccountSignUpWithLogonEmail` samodzielnie potwierdzony profil techniczny następującym, jeśli użyto wcześniejszej wersji zasad usługi Azure AD B2C. Ten profil techniczny `DisplayClaims` używa z odwołaniem do DisplayControl.

Aby uzyskać więcej informacji, zobacz [Samodzielnie potwierdzony profil techniczny](restful-technical-profile.md) i [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Opcjonalnie] Lokalizowanie poczty e-mail

Aby zlokalizować wiadomość e-mail, należy wysłać zlokalizowane ciągi do SendGrid lub dostawcy poczty e-mail. Na przykład, aby zlokalizować temat wiadomości e-mail, treść, wiadomość kod lub podpis wiadomości e-mail. Aby to zrobić, można użyć [GetLocalizedStringsTransformation](string-transformations.md) roszczeń transformacji do kopiowania zlokalizowanych ciągów do typów oświadczeń. W `GenerateSendGridRequestBody` transformacji oświadczeń, która generuje ładunek JSON, używa oświadczeń wejściowych, które zawierają zlokalizowane ciągi.

1. W zasadach zdefiniuj następujące oświadczenia ciągów: temat, wiadomość, codeIntro i podpis.
1. Zdefiniuj transformację oświadczeń [GetLocalizedStringsTransformation,](string-transformations.md) aby zastąpić zlokalizowane wartości ciągów w oświadczeniach z kroku 1.
1. Zmień `GenerateSendGridRequestBody` transformację oświadczeń, aby używać oświadczeń wejściowych z następującym fragmentem kodu XML.
1. Zaktualizuj szablon SendGrind, aby używać parametrów dynamicznych zamiast wszystkich ciągów, które będą zlokalizowane przez usługę Azure AD B2C.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
    <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
    <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
    <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>Następne kroki

Możesz znaleźć przykład niestandardowych zasad weryfikacji poczty e-mail w usłudze GitHub:

[Niestandardowa weryfikacja poczty e-mail — DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Aby uzyskać informacje dotyczące korzystania z niestandardowego interfejsu API REST lub dowolnego dostawcy poczty E-mail SMTP opartego na protok lub protok/i, zobacz [Definiowanie profilu technicznego restful w zasadach niestandardowych usługi Azure AD B2C](restful-technical-profile.md).
