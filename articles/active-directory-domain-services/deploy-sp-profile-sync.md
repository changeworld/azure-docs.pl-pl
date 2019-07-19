---
title: 'Azure Active Directory Domain Services: Włącz usługę profilu użytkownika programu SharePoint | Microsoft Docs'
description: Konfigurowanie Azure Active Directory Domain Services domen zarządzanych do obsługi synchronizacji profilu dla programu SharePoint Server
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
ms.openlocfilehash: 4a9ee05b37a69927d70269dccef2b74a2c251722
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234102"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Konfigurowanie domeny zarządzanej do obsługi synchronizacji profilu dla programu SharePoint Server
Program SharePoint Server zawiera usługę profilu użytkownika, która jest używana do synchronizacji profilu użytkownika. Aby skonfigurować usługę profilu użytkownika, należy przyznać odpowiednie uprawnienia w domenie Active Directory. Aby uzyskać więcej informacji, zobacz [Przyznawanie uprawnień Active Directory Domain Services dla synchronizacji profilu w programie SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

W tym artykule wyjaśniono, jak można skonfigurować Azure AD Domain Services domeny zarządzane do wdrożenia usługi synchronizacji profilu użytkownika programu SharePoint Server.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>Grupa "konta usługi AAD DC"
Grupa zabezpieczeń o nazwie "**konta usługi AAD DC**" jest dostępna w ramach jednostki organizacyjnej "Użytkownicy" w domenie zarządzanej. Tę grupę można wyświetlić w przystawce MMC **Użytkownicy i komputery Active Directory** w domenie zarządzanej.

![Grupa zabezpieczeń kont usługi AAD DC](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Członkowie tej grupy zabezpieczeń mają delegowane następujące uprawnienia:
- Uprawnienie "Replikuj zmiany katalogu" w katalogu głównym DSE domeny zarządzanej.
- Uprawnienie "Replikuj zmiany katalogu" w kontekście nazewnictwa konfiguracji (CN = Kontener konfiguracji) domeny zarządzanej.

Ta grupa zabezpieczeń jest również członkiem wbudowanej grupy **systemu sprzed Windows 2000**.

![Grupa zabezpieczeń kont usługi AAD DC](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Włącz obsługę synchronizacji profilu użytkownika programu SharePoint Server w domenie zarządzanej
Można dodać konto usługi używane na potrzeby synchronizacji profilu użytkownika programu SharePoint z grupą **kont usługi AAD DC** . W związku z tym konto synchronizacji uzyskuje odpowiednie uprawnienia do replikowania zmian w katalogu. Ten krok konfiguracji umożliwia poprawne działanie synchronizacji profilu użytkownika serwera programu SharePoint.

![Konta usługi AAD DC — Dodawanie członków](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Konta usługi AAD DC — Dodawanie członków](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Powiązana zawartość
* [Dokumentacja techniczna — Przyznawanie uprawnień Active Directory Domain Services dla synchronizacji profilu w programie SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
