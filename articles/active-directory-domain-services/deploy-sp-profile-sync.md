---
title: Włączanie usługi profilu użytkownika programu SharePoint za pomocą usług Azure AD DS | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować domenę zarządzaną usług domenowych Usługi Active Directory platformy Azure do obsługi synchronizacji profilów dla programu SharePoint Server
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 9d983015927d2635f69a327a9c5b168056542519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613853"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Konfigurowanie usług domenowych usługi Azure Active Directory do obsługi synchronizacji profilu użytkownika dla programu SharePoint Server

Program SharePoint Server zawiera usługę synchronizowania profili użytkowników. Ta funkcja umożliwia przechowywanie profilów użytkowników w centralnej lokalizacji i dostęp do wielu witryn i farm programu SharePoint. Aby skonfigurować usługę profilu użytkownika programu SharePoint Server, należy udzielić odpowiednich uprawnień w domenie zarządzanej usług domenowych Usługi azure ad (Azure AD DS). Aby uzyskać więcej informacji, zobacz [synchronizacja profilu użytkownika w programie SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx).

W tym artykule pokazano, jak skonfigurować usługi Azure AD DS, aby umożliwić usługę synchronizacji profilu użytkownika programu SharePoint Server.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć i skonfigurować wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .
* Maszyna wirtualna z zarządzaniem systemem Windows Server, która jest przyłączona do domeny zarządzanej usług Azure AD DS.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć maszynę wirtualną do zarządzania][tutorial-create-management-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów usługi Azure AD DC* w dzierżawie usługi Azure AD.
* Konto usługi programu SharePoint dla usługi synchronizacji profilu użytkownika.
    * W razie potrzeby zobacz [Planowanie kont administracyjnych i usługowych w programie SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Omówienie kont usług

W domenie zarządzanej usług Azure AD DS istnieje grupa zabezpieczeń o nazwie **Konta usług kontrolera domeny usługi AAD** jako część jednostki organizacyjnej *Users* (OU). Członkom tej grupy zabezpieczeń są delegowane następujące uprawnienia:

- **Uprawnienie Zmiany repliki katalogu** w głównym systemie DSE.
- **Uprawnienie Zmiany replikacji katalogu** w kontekście nazewnictwa *konfiguracji* (kontener).`cn=configuration`

Grupa zabezpieczeń **Konta usług kontrolera domeny usługi AAD** jest również członkiem wbudowanej grupy **Dostęp zgodny z systemem Pre-Windows 2000**.

Po dodaniu do tej grupy zabezpieczeń konto usługi synchronizacji profilu użytkownika programu SharePoint Server ma wymagane uprawnienia do poprawnego działania.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Włączanie obsługi synchronizacji profilu użytkownika programu SharePoint Server

Konto usługi programu SharePoint Server wymaga odpowiednich uprawnień do replikowania zmian w katalogu i umożliwienia poprawnej synchronizacji profilu użytkownika programu SharePoint Server. Aby zapewnić te uprawnienia, dodaj konto usługi używane do synchronizacji profilu użytkownika programu SharePoint do grupy **Konta usług kontrolera domeny usługi AAD.**

Na maszynie wirtualnej zarządzania usługami Wirtualnymi usług AD DS wykonaj następujące kroki:

> [!NOTE]
> Aby edytować członkostwo w grupie w domenie zarządzanej usług Azure AD DS, musisz zalogować się do konta użytkownika, które jest członkiem grupy *Administratorzy kontrolera domeny kontrolera domeny usługi AAD.*

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, które zostały zainstalowane w samouczku w celu [utworzenia maszyny wirtualnej do zarządzania][tutorial-create-management-vm].
1. Aby zarządzać członkostwem w grupie, wybierz pozycję **Centrum administracyjne usługi Active Directory** z listy narzędzi administracyjnych.
1. W lewym okienku wybierz domenę zarządzana usługą Azure AD DS, taką jak *aaddscontoso.com*. Zostanie wyświetlona lista istniejących procesorów operacyjnych i zasobów.
1. Wybierz **pozycję Użytkownicy—** a następnie wybierz grupę zabezpieczeń *Konta usług kontrolera domeny usługi usługi AAD.*
1. Wybierz **pozycję Członkowie**, a następnie wybierz pozycję **Dodaj...**.
1. Wprowadź nazwę konta usługi programu SharePoint, a następnie wybierz przycisk **OK**. W poniższym przykładzie konto usługi programu SharePoint nosi nazwę *spadmin:*

    ![Dodawanie konta usługi programu SharePoint do grupy zabezpieczeń Konta usług kontrolera domeny usługi AAD](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Udzielanie uprawnień Usług domenowych Active Directory do synchronizacji profilu w programie SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
