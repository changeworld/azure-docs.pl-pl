---
title: Zarządzanie zasadami niestandardowymi za pomocą programu PowerShell
titleSuffix: Azure AD B2C
description: Użyj polecenia cmdlet programu PowerShell usługi Azure Active Directory (Azure AD) do programowania zarządzania niestandardowymi zasadami usługi Azure AD B2C. Tworzenie, odczytywanie, aktualizowanie i usuwanie zasad niestandardowych za pomocą programu PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187410"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Zarządzanie niestandardowymi zasadami usługi Azure AD B2C za pomocą programu Azure PowerShell

Program Azure PowerShell udostępnia kilka poleceń cmdlet do zarządzania niestandardowymi wierszami polecenia i skryptami w dzierżawie usługi Azure AD B2C. Dowiedz się, jak użyć modułu programu Azure AD PowerShell do:

* Wyświetlanie listy zasad niestandardowych w dzierżawie usługi Azure AD B2C
* Pobieranie zasad od dzierżawy
* Aktualizowanie istniejącej zasady przez zastępowanie jej zawartości
* Przekazywanie nowych zasad do dzierżawy usługi Azure AD B2C
* Usuwanie zasad niestandardowych z dzierżawy

## <a name="prerequisites"></a>Wymagania wstępne

* [Dzierżawa usługi Azure AD B2C](tutorial-create-tenant.md)i poświadczenia dla użytkownika w katalogu z rolą [administratora zasad IEF B2C](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Zasady niestandardowe](custom-policy-get-started.md) przesłane do dzierżawy
* [Moduł programu Azure AD PowerShell for Graph **w wersji zapoznawczej**](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Łączenie sesji programu PowerShell z dzierżawą usługi B2C

Aby pracować z niestandardowymi zasadami w dzierżawie usługi Azure AD B2C, należy najpierw połączyć sesję programu PowerShell z dzierżawą przy użyciu polecenia [Connect-AzureAD.][Connect-AzureAD]

Wykonaj następujące polecenie, zastępując `{b2c-tenant-name}` nazwą dzierżawy usługi Azure AD B2C. Zaloguj się przy za pomocą konta, do które przypisano rolę [administratora zasad IEF B2C](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) w katalogu.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Przykładowe polecenie wyjściowe przedstawiające pomyślne logowanie:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Wyświetlanie listy wszystkich zasad niestandardowych w dzierżawie

Odnajdowanie zasad niestandardowych umożliwia administratorowi usługi Azure AD B2C przeglądanie, zarządzanie i dodawanie logiki biznesowej do swoich operacji. Użyj polecenia [Get-AzureADMSTrustFrameworkPolicy,][Get-AzureADMSTrustFrameworkPolicy] aby zwrócić listę identyfikatorów zasad niestandardowych w dzierżawie usługi Azure AD B2C.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Przykładowe polecenie wyjściowe:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Pobieranie zasad

Po przejrzeniu listy identyfikatorów zasad można kierować określone zasady za pomocą [get-AzureADMSTrustFrameworkPolicy,][Get-AzureADMSTrustFrameworkPolicy] aby pobrać jej zawartość.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

W tym przykładzie zasady z *identyfikatorem B2C_1A_signup_signin* są pobierane:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Aby lokalnie edytować zawartość zasad, należy przesuń polecenie do pliku z argumentem, `-OutputFilePath` a następnie otwórz plik w ulubionym edytorze.

Przykładowe polecenie wysyłające dane wyjściowe do pliku:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Aktualizowanie istniejących zasad

Po edycji pliku zasad, który został utworzony lub pobrany, można opublikować zaktualizowane zasady do usługi Azure AD B2C przy użyciu [polecenia Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

Jeśli wydasz `Set-AzureADMSTrustFrameworkPolicy` polecenie o identyfikatorze zasad, które już istnieje w dzierżawie usługi Azure AD B2C, zawartość tej zasady zostanie zastąpiona.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Przykładowe polecenie:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Aby uzyskać dodatkowe przykłady, zobacz [Set-AzureADMSTrustFrameworkPolcy][Set-AzureADMSTrustFrameworkPolicy] odwołanie do polecenia.

## <a name="upload-a-new-policy"></a>Przekazywanie nowych zasad

Po wprowadzeniu zmian w zasadach niestandardowych, który jest uruchomiony w wersji produkcyjnej, można opublikować wiele wersji zasad dla scenariuszy awaryjnych lub testowania A/B. Możesz też wykonać kopię istniejącej zasady, zmodyfikować ją za pomocą kilku małych zmian, a następnie przekazać ją jako nową zasadę do użycia przez inną aplikację.

Użyj [polecenia New-AzureADMSTrustFrameworkPolicy,][New-AzureADMSTrustFrameworkPolicy] aby przekazać nową zasadę:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Przykładowe polecenie:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Usuwanie zasad niestandardowych

Aby zachować czysty cykl życia operacji, zaleca się okresowe usuwanie nieużywanych zasad niestandardowych. Na przykład można usunąć stare wersje zasad po przeprowadzeniu migracji do nowego zestawu zasad i weryfikacji nowych zasad. Ponadto jeśli spróbujesz opublikować zestaw zasad niestandardowych i pojawi się błąd, może mieć sens usunięcie zasad, które zostały utworzone w ramach wersji nie powiodło się.

Użyj polecenia [Usuń AzureADMSTrustFrameworkPolicy,][Remove-AzureADMSTrustFrameworkPolicy] aby usunąć zasady z dzierżawy.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Przykładowe polecenie:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Rozwiązywanie problemów z przekazywaniem zasad

Podczas próby opublikowania nowej zasady niestandardowej lub aktualizacji istniejących zasad nieprawidłowe formatowanie XML i błędy w łańcuchu dziedziczenia plików zasad mogą powodować błędy sprawdzania poprawności.

Na przykład w tym przypadku jest próba aktualizacji zasad z zawartością, która zawiera nieprawidłowo sformułowany kod XML (dane wyjściowe są obcinane dla zwięzłości):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Aby uzyskać informacje dotyczące rozwiązywania problemów z zasadami niestandardowymi, zobacz [Rozwiązywanie problemów z zasadami niestandardowymi usługi Azure AD B2C i platformą środowiska tożsamości](active-directory-b2c-guide-troubleshooting-custom.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje dotyczące używania programu PowerShell do wdrażania zasad niestandardowych w ramach potoku ciągłej integracji/ciągłego dostarczania (CI/CD), zobacz [Wdrażanie zasad niestandardowych z potoku programu Azure DevOps.](deploy-custom-policies-devops.md)

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
