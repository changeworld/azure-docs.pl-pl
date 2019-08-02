---
title: Uprawnienia roli administratora niestandardowego dotyczące zarządzania rejestracją aplikacji — Azure Active Directory | Microsoft Docs
description: Uprawnienia roli administratora niestandardowego do delegowania zarządzania tożsamościami.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ff6755f1391ff19e65df669fb51967a904f4f
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707567"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Podtypy rejestracji aplikacji i uprawnienia w Azure Active Directory

Ten artykuł zawiera aktualnie dostępne uprawnienia rejestracji aplikacji dla niestandardowych definicji ról w Azure Active Directory (Azure AD).

## <a name="single-tenant-v-multi-tenant-permissions"></a>Pojedyncza dzierżawa. uprawnienia dla wielu dzierżawców

Niestandardowe uprawnienia ról różnią się w przypadku aplikacji z jedną dzierżawą i z wieloma dzierżawcami. Aplikacje z jedną dzierżawą są dostępne tylko dla użytkowników w organizacji usługi Azure AD, w których zarejestrowano aplikację. Aplikacje wielodostępne są dostępne dla wszystkich organizacji usługi Azure AD. Aplikacje o pojedynczej dzierżawie są zdefiniowane jako mające **obsługiwane typy kont** ustawione na "tylko konta w tym katalogu organizacji". W interfejs API programu Graph aplikacje z jedną dzierżawą mają właściwość signInAudience ustawioną na wartość "AzureADMyOrg".

## <a name="application-registration-subtypes-and-permissions"></a>Podtypy rejestracji aplikacji i uprawnienia

Zobacz [Omówienie ról niestandardowych](roles-custom-overview.md) , aby uzyskać informacje na temat tego, co oznaczają ogólne warunki podtype, uprawnienie i właściwość. Poniższe informacje dotyczą rejestracji aplikacji.

### <a name="subtypes"></a>Podtypy

Istnieje tylko jeden podtyp rejestracji aplikacji. Moja organizacja. Na przykład Microsoft. Directory/Applications. weborganization/Basic/Update. Ten podtyp jest ustawiany na stronie **uwierzytelnianie** dla określonej rejestracji aplikacji i odpowiada ustawieniu właściwości signInAudience na "AzureADMyOrg" przy użyciu interfejs API programu Graph lub PowerShell. Podtyp ogranicza uprawnienia do rejestracji aplikacji, które są oznaczone jako dostępne tylko dla kont w organizacji (aplikacje pojedynczej dzierżawy).

Możesz użyć ograniczonego uprawnienia, aby przyznać uprawnienia do odczytu lub zarządzania tylko aplikacjom wewnętrznym bez udzielania uprawnień do odczytu i zarządzania aplikacjami dostępnymi dla kont w innych organizacjach.

Istnieją aplikacje. w organizacji są dostępne wersje wszystkich uprawnień odczytu i aktualizacji oraz uprawnienia do usuwania. Obecnie nie ma żadnych aplikacji. w tej chwili zostanie utworzona wersja organizacji. Uprawnienia standardowe (na przykład Microsoft. Directory/Applications/Basic/Update) przyznają uprawnienia do odczytu lub zarządzania dla wszystkich typów rejestracji aplikacji.

![Deklarowanie aplikacji z jedną dzierżawą lub aplikacji z wieloma dzierżawcami](./media/roles-custom-available-permissions/supported-account-types.png)

Szczegóły dotyczące następujących uprawnień do wersji zapoznawczej ról niestandardowych są wymienione w obszarze [dostępne uprawnienia roli niestandardowej w programie Azure Active Directory](roles-custom-available-permissions.md).

### <a name="create-and-delete"></a>Tworzenie i usuwanie

Dostępne są dwa uprawnienia do przyznawania możliwości tworzenia rejestracji aplikacji:

- **Microsoft. Directory/Applications/createAsOwner**
- **Microsoft. Directory/Applications/Create**

Jeśli przypisane są oba uprawnienia, pierwszeństwo ma uprawnienie Tworzenie. Mimo że uprawnienie createAsOwner nie dodaje tego twórcy jako pierwszego właściciela, właściciele mogą zostać określeni podczas tworzenia rejestracji aplikacji podczas korzystania z interfejsów API programu Graph lub poleceń cmdlet środowiska PowerShell.

Utwórz uprawnienia Udziel dostępu do **nowego polecenia rejestracji** .

[Te uprawnienia zapewniają dostęp do nowego portalu rejestracji](./media/roles-create-custom/new-custom-role.png)

Dostępne są dwa uprawnienia do przyznawania możliwości usuwania rejestracji aplikacji:

#### <a name="microsoftdirectoryapplicationsdelete"></a>Microsoft. katalog/aplikacje/usuwanie

Przyznaje możliwość usuwania rejestracji aplikacji niezależnie od podtypu; oznacza to zarówno aplikacje z jedną dzierżawą, jak i wieloma dzierżawcami.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. weborganization/Delete

Umożliwia usunięcie rejestracji aplikacji ograniczonych do tych, które są dostępne tylko dla kont w organizacji lub aplikacji z jedną dzierżawą (podtyp organizacji).

![Uprawnienia te umożliwiają dostęp do polecenia Usuń rejestrację aplikacji](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Podczas przypisywania roli, która zawiera uprawnienia do tworzenia, przypisanie roli należy wykonać w zakresie katalogu. Uprawnienie CREATE przypisane do zakresu zasobów nie pozwala na tworzenie rejestracji aplikacji.

### <a name="read"></a>Odczyt

Wszyscy użytkownicy będący członkami w organizacji mogą domyślnie odczytywać informacje o rejestracji aplikacji. Jednak użytkownicy-Goście i podmioty usługi aplikacji nie mogą. Jeśli planujesz przypisanie roli do użytkownika lub aplikacji gościa, musisz dołączyć odpowiednie uprawnienia do odczytu.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/Applications/allProperties/Read

Możliwość odczytywania wszystkich właściwości aplikacji z jedną dzierżawą i wielu dzierżawców poza poufnymi właściwościami, takimi jak poświadczenia.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. weborganization/allProperties/Read

Przyznaje takie same uprawnienia jak Microsoft. Directory/Applications/allProperties/Read, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsstandardread-grants-access-to-all-fields-on-the-application-registration-branding-page"></a>Microsoft. Directory/Applications/Standard/Read: Przyznaje dostęp do wszystkich pól na stronie znakowania rejestracji aplikacji

![To uprawnienie udziela dostępu do strony znakowania rejestracji aplikacji](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. weborganization/Standard/Read

Przyznaje takie same uprawnienia jak Microsoft. Directory/Applications/Standard/Read, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/Applications/Owners/Read

Przyznaje możliwość odczytywania właściwości właściciele w aplikacjach z jedną dzierżawą i wieloma dzierżawcami. Przyznaje dostęp do wszystkich pól na stronie właściciele rejestracji aplikacji:

![Te uprawnienia udzielają dostępu do strony właściciele rejestracji aplikacji](./media/roles-custom-available-permissions/app-registration-owners.png)

Przyznaje dostęp do następujących właściwości w jednostce aplikacji:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppCreatedDateTime
- Folder
- Identyfikator aplikacji
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

### <a name="update"></a>Aktualizacja

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/Applications/allProperties/Update

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. weborganization/allProperties/Update

Przyznaje takie same uprawnienia, jak Microsoft. Directory/Applications/allProperties/Update, ale tylko dla aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Przyznaje dostęp do wszystkich pól na stronie uwierzytelniania rejestracji aplikacji:

![To uprawnienie udziela dostępu do strony uwierzytelniania rejestracji aplikacji](./media/roles-custom-available-permissions/supported-account-types.png)

Przyznaje dostęp do następujących właściwości zasobu aplikacji:

- AvailableToOtherTenants
- SignInAudience

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. katalog/aplikacje. weborganization/odbiorcy/aktualizacja

Przyznaje takie same uprawnienia, jak Microsoft. Directory/Applications/odbiorca/aktualizacja, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Możliwość aktualizowania adresu URL odpowiedzi, adresu URL logowania, przepływu niejawnego i właściwości domeny wydawcy w aplikacjach z jedną dzierżawą i wieloma dzierżawcami. Przyznaje dostęp do wszystkich pól na stronie uwierzytelniania rejestracji aplikacji, z wyjątkiem typów obsługiwanych kont:

![Udziela dostępu do uwierzytelniania rejestracji aplikacji, ale nie obsługiwanych typów kont](./media/roles-custom-available-permissions/supported-account-types.png)

 Przyznaje dostęp do następujących właściwości zasobu aplikacji:

- AcceptMappedClaims
- AccessTokenAcceptedVersion
- AddIns
- GroupMembershipClaims
- IsDeviceOnlyAuthSupported
- OAuth2LegacyUrlPathMatching
- OauthOidcResponsePolicyBitmap
- OptionalClaims
- OrgRestrictions
- PublicClient
- UseCustomTokenSigningKey

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. weborganization/Authentication/Update

Przyznaje takie same uprawnienia, jak Microsoft. Directory/Applications/Authentication/Update, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>Microsoft. Directory/Applications/Basic/Update

Możliwość aktualizacji nazwy, logo, adresu URL strony głównej, adresu URL warunków użytkowania oraz właściwości adresu URL zasad zachowania poufności informacji w aplikacjach z jedną dzierżawą i wieloma dzierżawcami. Przyznaje dostęp do wszystkich pól na stronie znakowania rejestracji aplikacji:

![To uprawnienie udziela dostępu do strony znakowania rejestracji aplikacji](./media/roles-custom-available-permissions/app-registration-branding.png)

Przyznaje dostęp do następujących właściwości zasobu aplikacji:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- Folder
- Identyfikator aplikacji
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. weborganization/Basic/Update

Przyznaje takie same uprawnienia jak Microsoft. Directory/Applications/Basic/Update, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Możliwość aktualizowania certyfikatów i właściwości tajnych klienta w aplikacjach z jedną dzierżawą i wieloma dzierżawcami. Przyznaje dostęp do wszystkich pól na stronie certyfikatów rejestracji aplikacji & Secret:

![To uprawnienie udziela dostępu do certyfikatów rejestracji aplikacji & strony kluczy tajnych](./media/roles-custom-available-permissions/app-registration-secrets.png)

Przyznaje dostęp do następujących właściwości zasobu aplikacji:
- AsymmetricKey
- EncryptedSecretKey
- Opis
- SharedKeyReference
- TokenEncryptionKeyId

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. weborganization/Credentials/Update

Przyznaje takie same uprawnienia, jak Microsoft. Directory/Applications/Credentials/Update, ale tylko w przypadku aplikacji z jednym katalogiem.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>Microsoft. Directory/Applications/Owners/Update

Możliwość aktualizowania właściwości Owner w ramach jednej dzierżawy i wielu dzierżawców. Przyznaje dostęp do wszystkich pól na stronie właściciele rejestracji aplikacji:

![Te uprawnienia udzielają dostępu do strony właściciele rejestracji aplikacji](./media/roles-custom-available-permissions/app-registration-owners.png)

Przyznaje dostęp do następujących właściwości zasobu aplikacji:
- Właściciele

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. weborganizations/Owners/Update

Przyznaje takie same uprawnienia jak Microsoft. Directory/Applications/Owners/Update, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>Microsoft. Directory/Applications/Permissions/Update

Możliwość aktualizowania uprawnień delegowanych, uprawnień aplikacji, autoryzowanych aplikacji klienckich, wymaganych uprawnień i przyznawania właściwości zgody w aplikacjach z jedną dzierżawą i wieloma dzierżawcami. Nie przyznaje możliwości zgody. Przyznaje dostęp do wszystkich pól w uprawnieniach interfejsu API rejestracji aplikacji i uwidacznia strony interfejsu API:

![Te uprawnienia udzielają dostępu do strony uprawnień interfejsu API rejestracji aplikacji](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Te uprawnienia udzielają dostępu do rejestracji aplikacji Udostępnianie strony interfejsu API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

Przyznaje dostęp do następujących właściwości zasobu aplikacji:

- AppIdentifierUri
- Uprawnienie
- PreAuthorizedApplications
- RecordConsentConditions
- RequiredResourceAccess

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. katalog/aplikacje. weborganizations/Permissions/Update

Przyznaje takie same uprawnienia, jak Microsoft. Directory/Applications/Permissions/Update, ale tylko w przypadku aplikacji z jedną dzierżawą.

## <a name="required-license-plan"></a>Wymagany plan licencji

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie ról niestandardowych przy użyciu [Azure Portal, programu Azure AD PowerShell i interfejs API programu Graph](roles-create-custom.md)
- [Wyświetlanie przypisań roli niestandardowej](roles-view-assignments.md)
