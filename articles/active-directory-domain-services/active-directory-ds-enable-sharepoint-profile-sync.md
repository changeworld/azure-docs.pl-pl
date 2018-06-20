---
title: 'Azure Active Directory Domain Services: Włączanie obsługi usługi profilu użytkownika programu SharePoint | Dokumentacja firmy Microsoft'
description: Konfigurowanie domen zarządzanych usług domenowych Azure Active Directory w celu obsługi synchronizacji profilu programu SharePoint Server
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: e1162a3f87c9242e7366109302b3bd807b1fdcc8
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214379"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Konfigurowanie domeny zarządzanej w celu obsługi synchronizacji profilu programu SharePoint Server
Serwer programu SharePoint obejmuje usługi profilu użytkownika, który służy do synchronizacji profilu użytkownika. Aby skonfigurować usługi profilu użytkownika, odpowiednie uprawnienia muszą być przyznane domeny usługi Active Directory. Aby uzyskać więcej informacji, zobacz [uprawnienia usług domenowych w usłudze Active Directory synchronizacji profilów w programie SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

W tym artykule opisano, jak można skonfigurować usługi domenowe Azure AD domen zarządzanych w celu wdrożenia usługi synchronizacji profilu użytkownika z programem SharePoint Server.

## <a name="the-aad-dc-service-accounts-group"></a>Grupa "Konta usługi kontrolera domeny usługi AAD"
Grupy zabezpieczeń o nazwie "**konta usługi kontrolera domeny usługi AAD**" znajduje się w jednostce organizacyjnej 'Użytkownicy' domeny zarządzanej. Można wyświetlić tej grupy w **użytkownicy usługi Active Directory i komputery** przystawka programu MMC w domenie zarządzanej.

![Grupa zabezpieczeń konta usługi kontrolera domeny usługi AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Delegowane są członkami tej grupy zabezpieczeń następujące uprawnienia:
- Uprawnienie "Replikować zmiany katalogu" na komputerze głównego elementu DSE domeny zarządzanej.
- Uprawnienie 'Replikować zmiany katalogu' w kontekście nazewnictwa konfiguracji (cn = kontenera konfiguracji) do domeny zarządzanej.

Ta grupa zabezpieczeń jest również członkiem grupy wbudowane **dostęp zgodny z systemami starszymi niż Windows 2000**.

![Grupa zabezpieczeń konta usługi kontrolera domeny usługi AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Włącz domeny zarządzanej do obsługi synchronizacji profilu użytkownika programu SharePoint Server
Można dodać konto usługi używane do synchronizacji profilu użytkownika programu SharePoint do **konta usługi kontrolera domeny usługi AAD** grupy. W związku z tym konta synchronizacji pobiera odpowiednie uprawnienia, aby replikować zmiany do katalogu. Ten krok konfiguracji powoduje włączenie synchronizacji profilu użytkownika programu SharePoint Server działała prawidłowo.

![Konta usługi AAD kontrolera domeny — dodawanie członków](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Konta usługi AAD kontrolera domeny — dodawanie członków](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Powiązana zawartość
* [Dokumentacja techniczna - uprawnienia Grant usług domenowych Active Directory synchronizacji profilów w programie SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
