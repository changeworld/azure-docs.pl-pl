---
title: Konfigurowanie programu SharePoint Online i Exchange Online dla dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi SharePoint Online i Exchange Online dla usługi Azure Active Directory dostępu warunkowego.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 398b69769cebb9d54121e357faed06294499bd61
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723783"
---
# <a name="set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Konfigurowanie programu SharePoint Online i Exchange Online dla usługi Azure Active Directory dostępu warunkowego 

Z [dostępu warunkowego w usłudze Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), można kontrolować, jak użytkownicy uzyskują dostęp do aplikacji w chmurze. Jeśli chcesz kontrolować dostęp do programu SharePoint i Exchange online korzystanie z dostępu warunkowego należy:

- Sprawdź, czy jest obsługiwany scenariusz dostępu warunkowego
- Blokowanie aplikacji klienta z pominięciem wymuszania zasad dostępu warunkowego.   

W tym artykule opisano, jak można rozwiązać obu przypadkach.


## <a name="what-you-need-to-know"></a>Co należy wiedzieć

Dostęp warunkowy do usługi Azure AD umożliwia chronić aplikacje w chmurze, jeśli próba uwierzytelnienia pochodzi z:

- Przeglądarki sieci web

- Aplikacji klienta, który używa [nowoczesnego uwierzytelniania](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Niektóre aplikacje w chmurze również obsługiwać protokoły uwierzytelniania starszej wersji. Dotyczy to na przykład do usługi SharePoint Online i Exchange Online. Gdy aplikacja kliencka umożliwia dostęp do aplikacji w chmurze protokołem uwierzytelniania starszej wersji, usługi Azure AD nie można wymusić zasady dostępu warunkowego na ta próba dostępu. Aby zapobiec pomijanie wymuszania zasad aplikacji klienta, należy sprawdzić, czy jest możliwe tylko włączenie nowoczesnego uwierzytelniania w aplikacjach w chmurze dotyczy. 

Przykłady aplikacji dostępu warunkowego nie ma zastosowania do klienta są:

- Office 2010 lub starszy

- Pakiet Office 2013, gdy nowoczesnego uwierzytelniania nie jest włączone.



 
## <a name="control-access-to-sharepoint-online"></a>Kontrola dostępu do usługi SharePoint Online

Oprócz nowoczesne uwierzytelnianie usługi SharePoint Online obsługuje również protokoły uwierzytelniania starszej wersji. Jeśli są włączone protokoły uwierzytelniania starszej wersji, zasad dostępu warunkowego dla programu SharePoint nie są wymuszane dla klientów, którzy nie używają nowoczesnego uwierzytelniania.

Protokoły uwierzytelniania starszych dostępu programu SharePoint można wyłączyć za pomocą **[SPOTenant zestaw](https://technet.microsoft.com/library/fp161390.aspx)** polecenia cmdlet: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Kontrola dostępu do usługi Exchange Online

Po skonfigurowaniu zasad dostępu warunkowego dla usługi Exchange Online, należy zapoznać się z następującymi:

- Exchange ActiveSync

- Protokoły uwierzytelniania starszej wersji



### <a name="exchange-activesync"></a>Exchange ActiveSync

Natomiast programu Exchange Active Sync obsługuje nowoczesnego uwierzytelniania, istnieją pewne ograniczenia dotyczące pomocy technicznej dla scenariuszy dostępu warunkowego:

- Można skonfigurować tylko warunek platformy urządzenia  

    ![Platformy urządzeń](./media/active-directory-conditional-access-no-modern-authentication/05.png)

- Ustawianie wymagania dotyczące uwierzytelniania wieloskładnikowego nie jest obsługiwane.  

    ![Dostęp warunkowy](./media/active-directory-conditional-access-no-modern-authentication/01.png)

Aby skutecznie chronić dostępu do usługi Exchange Online za pomocą programu Exchange ActiveSync, można:

- Konfigurowanie zasad dostępu warunkowego obsługiwanych wykonaj następujące czynności:

    a. Wybierz tylko **Office 365 usługi Exchange Online** jako aplikacji w chmurze.  

    ![Dostęp warunkowy](./media/active-directory-conditional-access-no-modern-authentication/04.png)

    b. Wybierz **programu Exchange Active Sync** jako **aplikacji klienckiej**, a następnie wybierz **stosowania zasad tylko dla obsługiwanych platform**.  

    ![Platformy urządzeń](./media/active-directory-conditional-access-no-modern-authentication/03.png)

- Blok programu Exchange ActiveSync przy użyciu reguł usługi Active Directory Federation Services (AD FS).

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Protokoły uwierzytelniania starszej wersji

Oprócz nowoczesne uwierzytelnianie usługi Exchange Online obsługuje również protokoły uwierzytelniania starszej wersji. Jeśli są włączone protokoły uwierzytelniania starszej wersji, zasad dostępu warunkowego dla usługi Exchange Online nie są wymuszane dla klientów, którzy nie używają nowoczesnego uwierzytelniania.

Protokoły uwierzytelniania starszej wersji dla usługi Exchange Online można wyłączyć przez ustawienie zasad usług AD FS. To blokuje dostęp z:

- Starsze klienty pakietu Office, takich jak pakiet Office 2013, która nie ma nowoczesne uwierzytelnianie jest włączone 

- Starsze wersje pakietu Office


## <a name="set-up-ad-fs-rules"></a>Skonfiguruj reguły usług AD FS

Aby umożliwić lub zablokować ruch na poziomie usług AD FS, można użyć następujących reguł autoryzacji wystawiania. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Blokuje starszych ruch z ekstranetu

Stosując trzy następujące reguły: 

- Możesz włączyć dostęp do:
    - Ruch protokołu Exchange ActiveSync
    - Ruch przeglądarki
    - Ruch nowoczesnego uwierzytelniania
- Możesz zablokować dostęp do: 
    - Aplikacje ze starszej wersji klienta z ekstranetu

**Reguła 1:**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Reguła 2:**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Reguła 3:**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>Blokuje starszych ruch z dowolnego miejsca

Stosując trzy następujące reguły:

- Możesz włączyć dostęp do: 
    - Ruch protokołu Exchange ActiveSync
    - Ruch przeglądarki
    - Ruch nowoczesnego uwierzytelniania
- Możesz zablokować dostęp do: 
    - Starszych aplikacji z dowolnego miejsca.

##### <a name="rule-1"></a>Reguła 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Reguła 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Reguła 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md).

Aby uzyskać instrukcje dotyczące konfigurowania reguł oświadczeń, zobacz [Konfiguruj reguły oświadczeń](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules). 






