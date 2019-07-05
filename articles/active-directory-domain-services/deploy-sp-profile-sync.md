---
title: 'Azure Active Directory Domain Services: Włącz obsługę usługi profilu użytkownika programu SharePoint | Dokumentacja firmy Microsoft'
description: Konfigurowanie domen zarządzanych usługi Azure Active Directory Domain Services w celu obsługi synchronizacji profilów dla programu SharePoint Server
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: iainfou
ms.openlocfilehash: 4293052f19ad883c9df7f177456d55c0997072e1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473487"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Konfigurowanie domeny zarządzanej w celu obsługi synchronizacji profilów dla programu SharePoint Server
Serwer programu SharePoint zawiera Usługa profilu użytkownika, który służy do synchronizacji profilu użytkownika. Aby skonfigurować usługi profilu użytkownika, należy nadać w domenie usługi Active Directory odpowiednie uprawnienia. Aby uzyskać więcej informacji, zobacz [udzielić uprawnień Active Directory Domain Services dla synchronizacji profilów w programie SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

W tym artykule opisano sposób konfigurowania domen zarządzanych usług domenowych Azure AD, aby wdrożyć usługę synchronizacji profilu użytkownika z programem SharePoint Server.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>Grupa "Kont usługi AAD DC"
Grupy zabezpieczeń o nazwie "**konta usługi kontrolera domeny usługi AAD**" znajduje się w jednostce organizacyjnej "Użytkownicy" w domenie zarządzanej. Możesz zobaczyć tej grupy w **użytkownicy usługi Active Directory i komputery** przystawki programu MMC w domenie zarządzanej.

![Grupy zabezpieczeń usługi AAD DC usług konta](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Są delegowane Członkowie tej grupy zabezpieczeń następujące uprawnienia:
- Uprawnienia "Replikuj zmiany katalogu" w katalogu głównym wpisem DSE domeny zarządzanej.
- Uprawnienie "Replikuj zmiany katalogu" w kontekście nazewnictwa konfiguracji (cn = Konfiguracja kontenera) z domeny zarządzanej.

Ta grupa zabezpieczeń jest również członkiem wbudowanej grupy **dostęp zgodny z systemami starszymi niż Windows 2000**.

![Grupy zabezpieczeń usługi AAD DC usług konta](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Włączyć domenę zarządzaną w celu obsługi synchronizacji profilów użytkownika programu SharePoint Server
Możesz dodać konto usługi używane do synchronizacji profilu użytkownika programu SharePoint do **konta usługi kontrolera domeny usługi AAD** grupy. W rezultacie konto synchronizacji pobiera odpowiednie uprawnienia, aby replikować zmiany w katalogu. Ten krok konfiguracji umożliwia synchronizacja profilu użytkownika programu SharePoint Server, do prawidłowego działania.

![Konta usługi AAD DC — dodawanie członków](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Konta usługi AAD DC — dodawanie członków](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Powiązana zawartość
* [Dokumentacja techniczna — uprawnienia Grant Active Directory Domain Services dla synchronizacji profilów w programie SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
