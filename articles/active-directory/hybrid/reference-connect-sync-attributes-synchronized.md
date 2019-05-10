---
title: Atrybuty synchronizowane z programu Azure AD Connect | Dokumentacja firmy Microsoft
description: Wyświetla listę atrybutów, które są synchronizowane z usługą Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6120637bdf52db4b6863fb87feb1d52c78ca088
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508109"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Synchronizacja programu Azure AD Connect: Atrybuty synchronizowane z usługą Azure Active Directory
Ten temat zawiera listę atrybutów, które są synchronizowane przez program Azure AD Connect sync.  
Atrybuty są pogrupowane według pokrewne usługi Azure AD aplikacji.

## <a name="attributes-to-synchronize"></a>Atrybuty na potrzeby synchronizacji
Często zadawane pytania jest *co to jest lista minimalnych atrybutów do synchronizacji*. Domyślnym i zalecanym podejściem jest zapewnienie atrybutów domyślnych, dzięki czemu można skonstruować pełne usługi GAL (globalna lista adresów) w chmurze i uzyskać dostęp do wszystkich funkcji w obciążeniach usługi Office 365. W niektórych przypadkach istnieją niektóre atrybuty, które Twoja organizacja nie chce zsynchronizowane z chmurą, ponieważ te atrybuty zawierają poufne lub dane osobowe (identyfikowalne dane osobowe) dane, takie jak w tym przykładzie:  
![nieprawidłowe atrybuty](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

W tym przypadku rozpoczynać lista atrybutów, w tym temacie i zidentyfikować te atrybuty, które będzie zawierać poufne lub dane osobowe i nie można zsynchronizować. Następnie usuń zaznaczenie tych atrybutów podczas instalacji przy użyciu [filtrowanie atrybutów i aplikacji usługi Azure AD](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> W przypadku zaznaczenia atrybutów, należy należy zachować ostrożność i tylko usunąć zaznaczenie tych atrybutów absolutnie nie można zsynchronizować. Unselecting innych atrybutów może mieć negatywny wpływ na funkcje.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Nazwa atrybutu | Użytkownik | Komentarz |
| --- |:---:| --- |
| accountEnabled |X |Określa, czy konto jest włączone. |
| cn |X | |
| displayName |X | |
| atrybuty objectSID |X |tych właściwości. Usługi AD identyfikator użytkownika, używany w celu zachowania synchronizacji między platformą Azure AD i AD. |
| pwdLastSet |X |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez synchronizacji skrótów haseł, uwierzytelniania przekazywanego i federacji. |
|samAccountName|X| |
| sourceAnchor |X |tych właściwości. Niemodyfikowalny identyfikator do utrzymania relacji między usług AD DS i Azure AD. |
| usageLocation |X |tych właściwości. Użytkownika kraj/region. Używane w celu przypisania licencji. |
| userPrincipalName |X |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków wartość taka sama jak [pocztą]. |

## <a name="exchange-online"></a>Exchange Online
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| Asystenta ustawień |X |X | | |
| altRecipient |X | | |Wymaga usługi Azure AD Connect kompilacji 1.1.552.0 lub po. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| Firmy |X |X | | |
| countryCode |X |X | | |
| Dział |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| informacje |X |X |X |Ten atrybut nie jest obecnie używane w grupach. |
| Inicjały |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| Zarządzane | | |X | |
| menedżer |X |X | | |
| składowa | | |X | |
| Telefon komórkowy |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Dostępne w usłudze Azure AD Connect w wersji w 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Ten atrybut nie jest obecnie używane przez usługi Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Ten atrybut nie jest obecnie używane przez usługi Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Ten atrybut nie jest obecnie używane przez usługi Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Ten atrybut nie jest obecnie używane przez usługi Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Ten atrybut nie jest obecnie używane przez usługi Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| atrybuty objectSID |X | |X |tych właściwości. Usługi AD identyfikator użytkownika, używany w celu zachowania synchronizacji między platformą Azure AD i AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez synchronizacji haseł i federacji. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| numery seryjne |X |X | | |
| sourceAnchor |X |X |X |tych właściwości. Niemodyfikowalny identyfikator do utrzymania relacji między usług AD DS i Azure AD. |
| St |X |X | | |
| Adres |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| tytuł |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |tych właściwości. Użytkownika kraj/region. Używane w celu przypisania licencji. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków wartość taka sama jak [pocztą]. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| Firmy |X |X | | |
| countryCode |X |X | | |
| Dział |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homephone |X |X | | |
| informacje |X |X |X | |
| Inicjały |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| poczta |X |X |X | |
| mailnickname |X |X |X | |
| Zarządzane | | |X | |
| menedżer |X |X | | |
| składowa | | |X | |
| middleName |X |X | | |
| Telefon komórkowy |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| atrybuty objectSID |X | |X |tych właściwości. Usługi AD identyfikator użytkownika, używany w celu zachowania synchronizacji między platformą Azure AD i AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Ten atrybut nie jest obecnie używane przez usługi SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez synchronizacji skrótów haseł, uwierzytelniania przekazywanego i federacji. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| numery seryjne |X |X | | |
| sourceAnchor |X |X |X |tych właściwości. Niemodyfikowalny identyfikator do utrzymania relacji między usług AD DS i Azure AD. |
| St |X |X | | |
| Adres |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| tytuł |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |tych właściwości. Użytkownika kraj/region
. Używane w celu przypisania licencji. |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków wartość taka sama jak [pocztą]. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Lync Online (później znane jako usługi Skype dla firm)
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| Firmy |X |X | | |
| Dział |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homephone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| poczta |X |X |X | |
| mailNickname |X |X |X | |
| Zarządzane | | |X | |
| menedżer |X |X | | |
| składowa | | |X | |
| Telefon komórkowy |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| atrybuty objectSID |X | |X |tych właściwości. Usługi AD identyfikator użytkownika, używany w celu zachowania synchronizacji między platformą Azure AD i AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez synchronizacji skrótów haseł, uwierzytelniania przekazywanego i federacji. |
| numery seryjne |X |X | | |
| sourceAnchor |X |X |X |tych właściwości. Niemodyfikowalny identyfikator do utrzymania relacji między usług AD DS i Azure AD. |
| St |X |X | | |
| Adres |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| tytuł |X |X | | |
| usageLocation |X | | |tych właściwości. Użytkownika kraj/region. Używane w celu przypisania licencji. |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków wartość taka sama jak [pocztą]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| cn |X | |X |Typowe nazwy lub aliasu. W większości przypadków prefiks wartości [pocztą]. |
| displayName |X |X |X |Ciąg, który reprezentuje nazwę często wyświetlany jako przyjazna nazwa (imię nazwisko). |
| poczta |X |X |X |pełny adres e-mail. |
| składowa | | |X | |
| atrybuty objectSID |X | |X |tych właściwości. Usługi AD identyfikator użytkownika, używany w celu zachowania synchronizacji między platformą Azure AD i AD. |
| proxyAddresses |X |X |X |tych właściwości. Używane przez usługę Azure AD. Zawiera wszystkie adresy pomocniczego adresu e-mail użytkownika. |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. |
| sourceAnchor |X |X |X |tych właściwości. Niemodyfikowalny identyfikator do utrzymania relacji między usług AD DS i Azure AD. |
| usageLocation |X | | |tych właściwości. Użytkownika kraj/region. Używane w celu przypisania licencji. |
| userPrincipalName |X | | |Ta nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków wartość taka sama jak [pocztą]. |

## <a name="intune"></a>Intune
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| cn |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| poczta |X |X |X | |
| mailnickname |X |X |X | |
| składowa | | |X | |
| atrybuty objectSID |X | |X |tych właściwości. Usługi AD identyfikator użytkownika, używany w celu zachowania synchronizacji między platformą Azure AD i AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez synchronizacji skrótów haseł, uwierzytelniania przekazywanego i federacji. |
| sourceAnchor |X |X |X |tych właściwości. Niemodyfikowalny identyfikator do utrzymania relacji między usług AD DS i Azure AD. |
| usageLocation |X | | |tych właściwości. Użytkownika kraj/region. Używane w celu przypisania licencji. |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków wartość taka sama jak [pocztą]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| Firmy |X |X | | |
| countryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| Zarządzane | | |X | |
| menedżer |X |X | | |
| składowa | | |X | |
| Telefon komórkowy |X |X | | |
| atrybuty objectSID |X | |X |tych właściwości. Usługi AD identyfikator użytkownika, używany w celu zachowania synchronizacji między platformą Azure AD i AD. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez synchronizacji skrótów haseł, uwierzytelniania przekazywanego i federacji. |
| numery seryjne |X |X | | |
| sourceAnchor |X |X |X |tych właściwości. Niemodyfikowalny identyfikator do utrzymania relacji między usług AD DS i Azure AD. |
| St |X |X | | |
| Adres |X |X | | |
| telephoneNumber |X |X | | |
| tytuł |X |X | | |
| usageLocation |X | | |tych właściwości. Użytkownika kraj/region. Używane w celu przypisania licencji. |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków wartość taka sama jak [pocztą]. |

## <a name="3rd-party-applications"></a>3 aplikacje innych firm
Ta grupa jest zestaw atrybutów pełniących rolę atrybuty minimalne wymagane dla ogólnych obciążeń lub aplikacji. Może służyć w przypadku obciążeń niewymienionych w innej sekcji lub aplikacji firm innych niż Microsoft. Jawnie służy do wykonania poniższych czynności:

* Usługi Yammer (używane tylko użytkownika)
* [Hybrydowe Business-to-Business (B2B) dla wielu organizacji scenariuszy obejmujących współpracę oferowane przez zasobów, takich jak SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Ta grupa jest zestaw atrybutów, których można użyć, jeśli katalog usługi Azure AD nie jest używany do obsługi usługi Office 365, Dynamics lub usługi Intune. Ma niewielki zestaw podstawowych atrybutów.

| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| cn |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| poczta |X | |X | |
| Zarządzane | | |X | |
| mailNickName |X |X |X | |
| składowa | | |X | |
| atrybuty objectSID |X | | |tych właściwości. Usługi AD identyfikator użytkownika, używany w celu zachowania synchronizacji między platformą Azure AD i AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez synchronizacji skrótów haseł, uwierzytelniania przekazywanego i federacji. |
| numery seryjne |X |X | | |
| sourceAnchor |X |X |X |tych właściwości. Niemodyfikowalny identyfikator do utrzymania relacji między usług AD DS i Azure AD. |
| usageLocation |X | | |tych właściwości. Użytkownika kraj/region. Używane w celu przypisania licencji. |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków wartość taka sama jak [pocztą]. |

## <a name="windows-10"></a>Windows 10
Usługi systemu Windows 10 przyłączonych do domeny computer(device) synchronizuje niektóre atrybuty do usługi Azure AD. Aby uzyskać więcej informacji na temat scenariuszy, zobacz [łączenie urządzeń przyłączonych do domeny do usługi Azure AD, dla środowisk systemu Windows 10](../active-directory-azureadjoin-devices-group-policy.md). Te atrybuty zawsze synchronizacji i systemu Windows 10 nie jest wyświetlany jako aplikację, którą możesz usunąć zaznaczenie. Usługi systemu Windows 10 przyłączonych do domeny komputera jest identyfikowany przez atrybut userCertificate, wypełnione.

| Nazwa atrybutu | Urządzenie | Komentarz |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Zapisane na stałe wartości dla komputerów przyłączonych do domeny. |
| displayName |X | |
| ms-DS-CreatorSID |X |Skrót registeredOwnerReference. |
| objectGUID |X |Skrót deviceID. |
| atrybuty objectSID |X |Skrót onPremisesSecurityIdentifier. |
| operatingSystem |X |Skrót deviceOSType. |
| operatingSystemVersion |X |Skrót deviceOSVersion. |
| userCertificate |X | |

Te atrybuty **użytkownika** czy oprócz innych aplikacji, które zostały wybrane.  

| Nazwa atrybutu | Użytkownik | Komentarz |
| --- |:---:| --- |
| domainFQDN |X |Skrót NazwaDomenyDNS. Na przykład "contoso.com". |
| domainNetBios |X |Skrót netBiosName. Na przykład CONTOSO. |
| msDS-KeyCredentialLink |X |Gdy użytkownik jest zarejestrowany w Windows Hello dla firm. | 

## <a name="exchange-hybrid-writeback"></a>Zapisywanie zwrotne hybrydowego programu Exchange
Te atrybuty będą zwrotnie zapisywane z usługi Azure AD w usłudze Active Directory w środowisku lokalnym po wybraniu umożliwiające **wdrożenie hybrydowe programu Exchange**. W zależności od używanej wersji programu Exchange mniej atrybutów może być synchronizowane.

| Atrybut nazwy (lokalnej usługi AD) | Atrybut nazwy (Connect interfejsu użytkownika) | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Tworzony na podstawie atrybutu cloudAnchor w usłudze Azure AD. Ten atrybut jest nowa w programie Exchange 2016 i Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Archiwum online: Umożliwia klientom archiwum wiadomości e-mail. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtrowanie: Zapisuje z powrotem filtrowanie lokalnych i danych online nadawcy bezpieczne i zablokowane od klientów. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtrowanie: Zapisuje z powrotem filtrowanie lokalnych i danych online nadawcy bezpieczne i zablokowane od klientów. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtrowanie: Zapisuje z powrotem filtrowanie lokalnych i danych online nadawcy bezpieczne i zablokowane od klientów. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Włącz Unified Messaging (UM) — Poczta głosowa Online: Używane przez program Microsoft Lync Server integracji w celu wskazania Lync Server w środowisku lokalnym czy użytkownik ma poczta głosowa w usługach online. |
| msExchUserHoldPolicies| ms-Exc-hUserHoldPolicies |X | | |Wstrzymanie postępowań prawnych: Włącza usługi cloud services do ustalania użytkowników w ramach postępowań prawnych przechowywania. |
| proxyAddresses| proxyAddresses |X |X |X |Tylko x500 adresu z usługi Exchange Online zostanie wstawiona. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Umożliwia pocztową usługi Exchange Online, należy przyznać prawa SendOnBehalfTo użytkownikom ze skrzynki pocztowej programu Exchange w środowisku lokalnym. Wymaga usługi Azure AD Connect kompilacji 1.1.552.0 lub po. |

## <a name="exchange-mail-public-folder"></a>Folder publiczne poczty programu Exchange
Te atrybuty są synchronizowane z usługi Active Directory środowiska lokalnego do usługi Azure AD po wybraniu umożliwiające **folderu publicznego poczty Exchange**.

| Nazwa atrybutu | PublicFolder | Komentarz |
| --- | :---:| --- |
| displayName | X |  |
| poczta | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Zapisywanie zwrotne urządzeń
Obiekty urządzeń są tworzone w usłudze Active Directory. Te obiekty mogą być urządzeń przyłączonych do usługi Azure AD lub komputerów przyłączonych do domeny systemu Windows 10.

| Nazwa atrybutu | Urządzenie | Komentarz |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Tylko w przypadku systemu Windows Server 2016 AD schematu |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Uwagi
* Podczas korzystania z alternatywnego Identyfikatora, w środowisku lokalnym atrybut userPrincipalName jest zsynchronizowany z onPremisesUserPrincipalName atrybut usługi Azure AD. Atrybut alternatywny identyfikator, na przykład wiadomości e-mail, jest synchronizowany z atrybutu userPrincipalName usługi Azure AD.
* Na liście powyżej, typ obiektu **użytkownika** typ obiektu dotyczy także **iNetOrgPerson**.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
