---
title: Configure password writeback for SSPR - Azure Active Directory
description: Use Azure AD and Azure AD Connect to writeback passwords to an on-premises directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1acda877ecadc8ad0abd09b78d5453743e2470b1
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381154"
---
# <a name="how-to-configure-password-writeback"></a>How-to: Configure password writeback

The following steps assume you have already configured Azure AD Connect in your environment by using the [Express](../hybrid/how-to-connect-install-express.md) or [Custom](../hybrid/how-to-connect-install-custom.md) settings.

1. Aby skonfigurować i włączyć zapisywanie zwrotne haseł, zaloguj się na serwerze programu Azure AD Connect i uruchom kreatora konfiguracji programu **Azure AD Connect**.
2. Na **stronie powitalnej** wybierz pozycję **Konfiguruj**.
3. Na stronie **Zadania dodatkowe** wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie wybierz pozycję **Dalej**.
4. Na stronie **Łączenie z usługą Azure AD** wprowadź poświadczenia administratora globalnego, a następnie wybierz pozycję **Dalej**.
5. Na stronach filtrowania **Łączenie katalogów** i **Domena/jednostka OU** wybierz pozycję **Dalej**.
6. Na stronie **Funkcje opcjonalne** zaznacz pole obok pozycji **Zapisywanie zwrotne haseł** i wybierz pozycję **Dalej**.
   ![Enable password writeback in Azure AD Connect][Writeback]
7. Na stronie **Wszystko gotowe do skonfigurowania** wybierz pozycję **Konfiguruj** i poczekaj na zakończenie procesu.
8. Po ukończeniu konfiguracji wybierz pozycję **Zakończ**.

For common troubleshooting tasks related to password writeback, see the section [Troubleshoot password writeback](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) in our troubleshooting article.

> [!WARNING]
> Password writeback will stop working for customers who are using Azure AD Connect versions 1.0.8641.0 and older when the [Azure Access Control service (ACS) is retired on November 7th, 2018](../develop/active-directory-acs-migration.md). Azure AD Connect versions 1.0.8641.0 and older will no longer allow password writeback at that time because they depend on ACS for that functionality.
>
> To avoid a disruption in service, upgrade from a previous version of Azure AD Connect to a newer version, see the article [Azure AD Connect: Upgrade from a previous version to the latest](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Licensing requirements for password writeback

**Self-Service Password Reset/Change/Unlock with on-premises writeback is a premium feature of Azure AD**. For more information about licensing, see the [Azure Active Directory pricing site](https://azure.microsoft.com/pricing/details/active-directory/).

To use password writeback, you must have one of the following licenses assigned on your tenant:

* Usługa Azure AD — warstwa Premium P1
* Usługa Azure AD — warstwa Premium P2
* Enterprise Mobility + Security E3 or A3
* Enterprise Mobility + Security E5 or A5
* Microsoft 365 E3 or A3
* Microsoft 365 E5 or A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Standalone Office 365 licensing plans *don't support "Self-Service Password Reset/Change/Unlock with on-premises writeback"* and require that you have one of the preceding plans for this functionality to work.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Active Directory permissions and on-premises password complexity policies 

The account specified in the Azure AD Connect utility must have the following items set if you want to be in scope for SSPR:

* **Resetowanie hasła** 
* **Zmień hasło** 
* **Write permissions** on `lockoutTime`
* **Write permissions** on `pwdLastSet`
* **Extended rights** on either:
   * The root object of *each domain* in that forest
   * The user organizational units (OUs) you want to be in scope for SSPR

If you're not sure what account the described account refers to, open the Azure Active Directory Connect configuration UI and select the **View current configuration** option. The account that you need to add permission to is listed under **Synchronized Directories**.

If you set these permissions, the MA service account for each forest can manage passwords on behalf of the user accounts within that forest. 

> [!IMPORTANT]
> If you neglect to assign these permissions, then, even though writeback appears to be configured correctly, users will encounter errors when they attempt to manage their on-premises passwords from the cloud.
>

> [!NOTE]
> It might take up to an hour or more for these permissions to replicate to all the objects in your directory.
>

To set up the appropriate permissions for password writeback to occur, complete the following steps:

1. Open Active Directory Users and Computers with an account that has the appropriate domain administration permissions.
2. From the **View** menu, make sure **Advanced features** is turned on.
3. In the left panel, right-click the object that represents the root of the domain and select **Properties** > **Security** > **Advanced**.
4. From the **Permissions** tab, select **Add**.
5. Pick the account that permissions are being applied to (from the Azure AD Connect setup).
6. In the **Applies to** drop-down list, select **Descendant User objects**.
7. Under **Permissions**, select the boxes for the following options:
    * **Zmień hasło**
    * **Resetowanie hasła**
8. Under **Properties**, select the boxes for the following options:
    * **Write lockoutTime**
    * **Write pwdLastSet**
9. Select **Apply/OK** to apply the changes and exit any open dialog boxes.

Since the source of authority is on premises, the password complexity policies apply from the same connected data source. Make sure you've changed the existing group policies for "Minimum password age". The group policy shouldn't be set to 1, which means password should be at least a day old before it can be updated. You need make sure it's set to 0. These settings can be found in `gpmc.msc` under **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies**. Run `gpupdate /force` to ensure that the change takes effect. 

## <a name="next-steps"></a>Następne kroki

[What is password writeback?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Enable password writeback in Azure AD Connect"
