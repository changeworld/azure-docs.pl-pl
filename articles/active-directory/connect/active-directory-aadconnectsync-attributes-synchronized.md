---
title: Atrybuty są synchronizowane przy użyciu usługi Azure AD Connect | Dokumentacja firmy Microsoft
description: Wyświetla listę atrybutów, które są synchronizowane z usługą Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: billmath
ms.openlocfilehash: 506206d3e0a28f2ffc0e9e029ec27546bc332754
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Synchronizacja programu Azure AD Connect: atrybuty synchronizowane z usługą Azure Active Directory
Ten temat zawiera listę atrybutów, które są synchronizowane przez synchronizacja programu Azure AD Connect.  
Atrybuty są pogrupowane według pokrewne usługi Azure AD aplikacji.

## <a name="attributes-to-synchronize"></a>Atrybuty do synchronizacji
Często zadawane pytania jest *co to jest lista minimalnych atrybutów do synchronizacji*. Domyślnym i zalecanym podejściem jest zachowanie domyślne atrybuty, można utworzyć pełne usługi GAL (globalna lista adresów) w chmurze i pobrać wszystkich funkcji w zadaniami usługi Office 365. W niektórych przypadkach są niektóre atrybuty, które Twojej organizacji mają być synchronizowane z chmurą zawierać te atrybuty poufnych lub wrażliwych danych osobowych () danych osobowych, takich jak w tym przykładzie:  
![Złe atrybuty](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

W takim przypadku zaczynać się na liście atrybutów w tym temacie i zidentyfikować te atrybuty, które może zawierać wrażliwe lub dane osobowe danych i nie można zsynchronizować. Usuń zaznaczenie opcji te atrybuty podczas instalacji, używając [filtrowanie atrybutów i aplikacji usługi Azure AD](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> W przypadku zaznaczenia atrybuty, należy należy zachować ostrożność i tylko Anuluj wybór tych atrybutów absolutnie nie można zsynchronizować. Inne atrybuty unselecting może mieć negatywny wpływ na funkcje.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Nazwa atrybutu | Użytkownik | Komentarz |
| --- |:---:| --- |
| accountEnabled |X |Określa, czy konto jest włączone. |
| cn |X | |
| Nazwa wyświetlana |X | |
| objectSID |X |tych właściwości. Identyfikator użytkownika AD służy do utrzymywania synchronizacji między Azure AD i usługi AD. |
| pwdLastSet |X |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez funkcje synchronizacji skrótów haseł, uwierzytelnianie i federacji. |
| sourceAnchor |X |tych właściwości. Niezmienne identyfikator, aby utrzymać relację między DODAJE i Azure AD. |
| usageLocation |X |tych właściwości. Kraju użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków taki sam jak [poczty] wartości. |

## <a name="exchange-online"></a>Exchange Online
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| Asystent |X |X | | |
| altRecipient |X | | |Wymaga usługi Azure AD Connect kompilacji 1.1.552.0 lub po. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| Firmy |X |X | | |
| countryCode |X |X | | |
| Dział |X |X | | |
| description |X |X |X | |
| Nazwa wyświetlana |X |X |X | |
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
| HomePhone |X |X | | |
| Informacje o |X |X |X |Ten atrybut nie jest aktualnie używane dla grup. |
| Inicjały |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Menedżer |X |X | | |
| Element członkowski | | |X | |
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
| msExchBypassModerationLink | | |X |Dostępne w programie Azure AD Connect wersji 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Ten atrybut nie jest aktualnie używane przez usługę Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Ten atrybut nie jest aktualnie używane przez usługę Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Ten atrybut nie jest aktualnie używane przez usługę Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Ten atrybut nie jest aktualnie używane przez usługę Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Ten atrybut nie jest aktualnie używane przez usługę Exchange Online. |
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
| objectSID |X | |X |tych właściwości. Identyfikator użytkownika AD służy do utrzymywania synchronizacji między Azure AD i usługi AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używana przez synchronizacji haseł i federacji. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Pochodne groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |tych właściwości. Niezmienne identyfikator, aby utrzymać relację między DODAJE i Azure AD. |
| St |X |X | | |
| Adres |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| TelephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| tytuł |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |tych właściwości. Kraju użytkownika. Używany do przypisywania licencji. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków taki sam jak [poczty] wartości. |
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
| Nazwa wyświetlana |X |X |X | |
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
| Informacje o |X |X |X | |
| Inicjały |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| Poczty |X |X |X | |
| mailnickname |X |X |X | |
| managedBy | | |X | |
| Menedżer |X |X | | |
| Element członkowski | | |X | |
| middleName |X |X | | |
| Telefon komórkowy |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |tych właściwości. Identyfikator użytkownika AD służy do utrzymywania synchronizacji między Azure AD i usługi AD. |
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
| postOfficeBox |X |X | |Ten atrybut jest obecnie nie są używane przez usługi SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez funkcje synchronizacji skrótów haseł, uwierzytelnianie i federacji. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Pochodne groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |tych właściwości. Niezmienne identyfikator, aby utrzymać relację między DODAJE i Azure AD. |
| St |X |X | | |
| Adres |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| TelephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| tytuł |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |tych właściwości. Kraju użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków taki sam jak [poczty] wartości. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Usługi Lync Online (zwaną następnie Skype dla firm)
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| Firmy |X |X | | |
| Dział |X |X | | |
| description |X |X |X | |
| Nazwa wyświetlana |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homephone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| Poczty |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Menedżer |X |X | | |
| Element członkowski | | |X | |
| Telefon komórkowy |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |tych właściwości. Identyfikator użytkownika AD służy do utrzymywania synchronizacji między Azure AD i usługi AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez funkcje synchronizacji skrótów haseł, uwierzytelnianie i federacji. |
| securityEnabled | | |X |Pochodne groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |tych właściwości. Niezmienne identyfikator, aby utrzymać relację między DODAJE i Azure AD. |
| St |X |X | | |
| Adres |X |X | | |
| TelephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| tytuł |X |X | | |
| usageLocation |X | | |tych właściwości. Kraju użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków taki sam jak [poczty] wartości. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| cn |X | |X |Typowe nazwy ani aliasu. W większości przypadków prefiks wartość [poczty]. |
| Nazwa wyświetlana |X |X |X |Ciąg, który reprezentuje nazwę często są wyświetlane jako przyjaznej nazwy (imię nazwisko). |
| Poczty |X |X |X |pełny adres e-mail. |
| Element członkowski | | |X | |
| objectSID |X | |X |tych właściwości. Identyfikator użytkownika AD służy do utrzymywania synchronizacji między Azure AD i usługi AD. |
| proxyAddresses |X |X |X |tych właściwości. Używane przez usługę Azure AD. Zawiera wszystkie adresy pomocniczego poczty e-mail dla użytkownika. |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. |
| securityEnabled | | |X |Pochodne groupType. |
| sourceAnchor |X |X |X |tych właściwości. Niezmienne identyfikator, aby utrzymać relację między DODAJE i Azure AD. |
| usageLocation |X | | |tych właściwości. Kraju użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Ta nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków taki sam jak [poczty] wartości. |

## <a name="intune"></a>Usługa Intune
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| cn |X | |X | |
| description |X |X |X | |
| Nazwa wyświetlana |X |X |X | |
| Poczty |X |X |X | |
| mailnickname |X |X |X | |
| Element członkowski | | |X | |
| objectSID |X | |X |tych właściwości. Identyfikator użytkownika AD służy do utrzymywania synchronizacji między Azure AD i usługi AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez funkcje synchronizacji skrótów haseł, uwierzytelnianie i federacji. |
| securityEnabled | | |X |Pochodne groupType |
| sourceAnchor |X |X |X |tych właściwości. Niezmienne identyfikator, aby utrzymać relację między DODAJE i Azure AD. |
| usageLocation |X | | |tych właściwości. Kraju użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków taki sam jak [poczty] wartości. |

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
| Nazwa wyświetlana |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| Menedżer |X |X | | |
| Element członkowski | | |X | |
| Telefon komórkowy |X |X | | |
| objectSID |X | |X |tych właściwości. Identyfikator użytkownika AD służy do utrzymywania synchronizacji między Azure AD i usługi AD. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez funkcje synchronizacji skrótów haseł, uwierzytelnianie i federacji. |
| securityEnabled | | |X |Pochodne groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |tych właściwości. Niezmienne identyfikator, aby utrzymać relację między DODAJE i Azure AD. |
| St |X |X | | |
| Adres |X |X | | |
| TelephoneNumber |X |X | | |
| tytuł |X |X | | |
| usageLocation |X | | |tych właściwości. Kraju użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków taki sam jak [poczty] wartości. |

## <a name="3rd-party-applications"></a>aplikacje firm 3
Ta grupa jest zestaw atrybutów używane jako atrybuty minimalne wymagane dla ogólnego obciążenia lub aplikacji. Można użyć, dla obciążenia niewymienionych w innej sekcji lub aplikacji firmy Microsoft. Jawnie służy do wykonania poniższych czynności:

* Yammer (używane tylko użytkownika)
* [Oferowane przez zasoby hybrydowego między firmami (B2B) całej organizacji współpracy scenariuszy, takich jak SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Ta grupa jest zestaw atrybutów, które mogą być używane, jeśli katalog usługi Azure AD nie jest używana do obsługi usługi Office 365, Dynamics lub usługi Intune. Ma niewielki zestaw Podstawowe atrybuty.

| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| cn |X | |X | |
| Nazwa wyświetlana |X |X |X | |
| givenName |X |X | | |
| Poczty |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| Element członkowski | | |X | |
| objectSID |X | | |tych właściwości. Identyfikator użytkownika AD służy do utrzymywania synchronizacji między Azure AD i usługi AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |tych właściwości. Używane do ustalenia, kiedy unieważnienie już wystawionych tokenów. Używane przez funkcje synchronizacji skrótów haseł, uwierzytelnianie i federacji. |
| SN |X |X | | |
| sourceAnchor |X |X |X |tych właściwości. Niezmienne identyfikator, aby utrzymać relację między DODAJE i Azure AD. |
| usageLocation |X | | |tych właściwości. Kraju użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN jest identyfikator logowania dla użytkownika. W większości przypadków taki sam jak [poczty] wartości. |

## <a name="windows-10"></a>Windows 10
Windows 10 przyłączonych do domeny computer(device) synchronizuje niektóre atrybuty do usługi Azure AD. Aby uzyskać więcej informacji na temat scenariuszy, zobacz [łączenie urządzeń przyłączonych do domeny do usługi Azure AD dla systemu Windows 10 napotyka](../active-directory-azureadjoin-devices-group-policy.md). Te atrybuty zawsze synchronizacji i Windows 10 nie będzie wyświetlane w aplikacji, które możesz usunąć zaznaczenie. Windows 10 przyłączonych do domeny komputera jest identyfikowany przez o userCertificate atrybutu wypełnione.

| Nazwa atrybutu | Urządzenie | Komentarz |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Wartość zapisane na stałe dla komputerów przyłączonych do domeny. |
| Nazwa wyświetlana |X | |
| ms-DS-CreatorSID |X |Skrót registeredOwnerReference. |
| Atrybut objectGUID |X |Skrót deviceID. |
| objectSID |X |Skrót onPremisesSecurityIdentifier. |
| operatingSystem |X |Skrót deviceOSType. |
| operatingSystemVersion |X |Skrót deviceOSVersion. |
| userCertificate |X | |

Te atrybuty **użytkownika** są oprócz wybrano inne aplikacje.  

| Nazwa atrybutu | Użytkownik | Komentarz |
| --- |:---:| --- |
| domainFQDN |X |Skrót NazwaDomenyDNS. Na przykład contoso.com. |
| domainNetBios |X |Skrót netBiosName. Na przykład CONTOSO. |

## <a name="exchange-hybrid-writeback"></a>Zapisywanie zwrotne hybrydowego programu Exchange
Te atrybuty są zapisywane z usługi Azure AD do lokalnej usługi Active Directory po wybraniu umożliwiające **hybrydowym programu Exchange**. W zależności od używanej wersji programu Exchange mogą być synchronizowani mniej atrybutów.

| Atrybut Name (łączenie interfejsu użytkownika) |Atrybut Name (lokalnej usłudze AD) | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Pochodną cloudAnchor w usłudze Azure AD. Ten atrybut jest nowa w programie Exchange 2016 i Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Archiwum online: Umożliwia klientom archiwum poczty. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtrowanie: Zapisuje ponownie filtrowania lokalne i dane online nadawcy bezpieczne i zablokowane od klientów. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtrowanie: Zapisuje ponownie filtrowania lokalne i dane online nadawcy bezpieczne i zablokowane od klientów. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtrowanie: Zapisuje ponownie filtrowania lokalne i dane online nadawcy bezpieczne i zablokowane od klientów. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Włącz Unified Messaging (UM) — Poczta głosowa Online: używany przez program Microsoft Lync Server integracji w celu wskazania Lync Server lokalnie czy użytkownik ma poczty głosowej w usługach online. |
| msExchUserHoldPolicies| ms-Exc-hUserHoldPolicies |X | | |Postępowań blokady: Usługi w chmurze umożliwia ustalenie użytkowników, którzy są w ramach postępowań przechowywania. |
| proxyAddresses| proxyAddresses |X |X |X |Tylko x500 dodaje się adres w usłudze Exchange Online. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Umożliwia pocztowa usługi Exchange Online otrzymać SendOnBehalfTo prawa do użytkowników z lokalnej skrzynek pocztowych programu Exchange. Wymaga usługi Azure AD Connect kompilacji 1.1.552.0 lub po. |

## <a name="exchange-mail-public-folder"></a>Folder publiczny poczty programu Exchange
Te atrybuty są synchronizowane z lokalnej usługi Active Directory do usługi Azure AD po wybraniu opcji Włącz **folderu publicznego poczty programu Exchange**.

| Nazwa atrybutu | PublicFolder | Komentarz |
| --- | :---:| --- |
| Nazwa wyświetlana | X |  |
| Poczty | X |  |
| msExchRecipientTypeDetails | X |  |
| Atrybut objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Zapisywanie zwrotne urządzeń
Obiekty urządzeń są tworzone w usłudze Active Directory. Te obiekty mogą być urządzeń przyłączonych do usługi Azure AD lub komputerów przyłączonych do domeny systemu Windows 10.

| Nazwa atrybutu | Urządzenie | Komentarz |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| Nazwa wyświetlana |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| atrybut msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| atrybut msDS-DeviceOSVersion |X | |
| atrybut msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Tylko ze schematem systemu Windows Server 2016 AD |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| atrybut msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Uwagi
* Używając alternatywnego Identyfikatora lokalnej atrybut userPrincipalName jest zsynchronizowany z onPremisesUserPrincipalName atrybutów usługi Azure AD. Atrybutu alternatywnego Identyfikatora, na przykład poczty, jest zsynchronizowany z usługi Azure AD atrybut userPrincipalName.
* Na liście powyżej, typ obiektu **użytkownika** mają zastosowanie również do typu obiektu **iNetOrgPerson**.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
