---
title: Azure Multi-Factor Auth Providers - Azure Active Directory
description: When should you use an Auth Provider with Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d4b89f7416847e01cad8cb4f9bc52248d09170d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382002"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>When to use an Azure Multi-Factor Authentication Provider

Weryfikacja dwuetapowa jest domyślnie dostępna dla administratorów globalnych, którzy zarządzają użytkownikami usług Azure Active Directory i Office 365. Jeśli jednak chcesz korzystać z [funkcji zaawansowanych](howto-mfa-mfasettings.md), musisz kupić pełną wersję usługi Azure Multi-Factor Authentication (MFA).

An Azure Multi-Factor Auth Provider is used to take advantage of features provided by Azure Multi-Factor Authentication for users who **do not have licenses**.

> [!NOTE]
> Effective September 1st, 2018 new auth providers may no longer be created. Existing auth providers may continue to be used and updated, but migration is no longer possible. Multi-factor authentication will continue to be available as a feature in Azure AD Premium licenses.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Caveats related to the Azure MFA SDK

Note the SDK has been deprecated and will only continue to work until November 14, 2018. Po tym czasie wywołania do zestawu SDK będą kończyć się niepowodzeniem.

## <a name="what-is-an-mfa-provider"></a>Co to jest dostawca usługi MFA?

There are two types of Auth providers, and the distinction is around how your Azure subscription is charged. W modelu opartym na uwierzytelnianiu jest uwzględniana liczba uwierzytelnień w dzierżawie w ciągu miesiąca. Ta opcja jest przydatna, jeśli masz pewną liczbę użytkowników, którzy uwierzytelniają się tylko czasami. W modelu opartym na użytkownikach jest uwzględniana liczba osób w dzierżawie, które w danym miesiącu korzystają z weryfikacji dwuetapowej. Ta opcja jest przydatna, jeśli masz użytkowników z licencjami, ale musisz objąć uwierzytelnianiem wieloskładnikowym dodatkowych użytkowników, którzy nie mają licencji.

## <a name="manage-your-mfa-provider"></a>Zarządzanie dostawcą usługi MFA

Po utworzeniu dostawcy usługi MFA nie możesz zmienić modelu użycia (na włączonego użytkownika lub na uwierzytelnienie).

If you purchased enough licenses to cover all users that are enabled for MFA, you can delete the MFA provider altogether.

Jeśli dostawca usługi MFA nie jest połączony z dzierżawą usługi Azure AD lub łączysz nowego dostawcę usługi MFA z inną dzierżawą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są przenoszone. Also, existing Azure MFA Servers need to be reactivated using activation credentials generated through the MFA Provider. Reactivating the MFA Servers to link them to the MFA Provider doesn't impact phone call and text message authentication, but mobile app notifications stop working for all users until they reactivate the mobile app.

### <a name="removing-an-authentication-provider"></a>Removing an authentication provider

> [!CAUTION]
> There is no confirmation when deleting an authentication provider. Selecting **Delete** is a permanent process.

Authentication providers can be found in the **Azure portal** > **Azure Active Directory** > **MFA** > **Providers**. Click on listed providers to see details and configurations associated with that provider.

Before removing an authentication provider, take note of any customized settings configured in your provider. Decide what settings need to be migrated to general MFA settings from your provider and complete the migration of those settings. 

Azure MFA Servers linked to providers will need to be reactivated using credentials generated under **Azure portal** > **Azure Active Directory** > **MFA** > **Server settings**. Before reactivating, the following files must be deleted from the `\Program Files\Multi-Factor Authentication Server\Data\` directory on Azure MFA Servers in your environment:

- caCert
- cert
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Delete an auth provider from the Azure portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

When you have confirmed that all settings have been migrated, you can browse to the **Azure portal** > **Azure Active Directory** > **MFA** > **Providers** and select the ellipses **...** and select **Delete**.

> [!WARNING]
> Deleting an authentication provider will delete any reporting information associated with that provider. You may want to save activity reports before deleting your provider.

> [!NOTE]
> Users with older versions of the Microsoft Authenticator app and Azure MFA Server may need to re-register their app.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie ustawień usługi Multi-Factor Authentication](howto-mfa-mfasettings.md)
