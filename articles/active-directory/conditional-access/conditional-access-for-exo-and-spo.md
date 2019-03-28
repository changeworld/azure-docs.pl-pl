---
title: Konfigurowanie usługi SharePoint Online i usługi Exchange Online dla dostępu warunkowego usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi SharePoint Online i Exchange Online dla dostępu warunkowego usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90cd381dbe3feaad110c7f10ae328915c051d0a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517729"
---
# <a name="how-to-set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Instrukcje: Konfigurowanie usługi SharePoint Online i usługi Exchange Online dla dostępu warunkowego usługi Azure Active Directory 

Za pomocą [dostępu warunkowego usługi Azure Active Directory (Azure AD)](overview.md), można kontrolować, jak użytkownicy uzyskują dostęp aplikacje w chmurze. Jeśli chcesz używać dostępu warunkowego do kontrolowania dostępu do usług SharePoint i Exchange online, należy:

- Sprawdź, czy jest obsługiwany scenariusz dostępu warunkowego
- Aplikacje klienckie uniemożliwić pomijanie wymuszania zasad dostępu warunkowego.   

W tym artykule wyjaśniono, jak wykonaniu obu przypadkach.


## <a name="what-you-need-to-know"></a>Co musisz wiedzieć

Dostęp warunkowy usługi Azure AD można użyć do ochrony aplikacji w chmurze, jeśli próba uwierzytelnienia pochodzi z:

- Przeglądarki sieci web

- Aplikacja kliencka, która używa [nowoczesnego uwierzytelniania](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Niektóre aplikacje w chmurze obsługują także protokoły uwierzytelniania w starszej wersji. Dotyczy to na przykład do usługi SharePoint Online i Exchange Online. Gdy aplikacja kliencka można użyć protokołu uwierzytelniania starszych umożliwiającego dostęp do aplikacji w chmurze, Azure AD nie wymusza zasad dostępu warunkowego, w tym próba dostępu. Aby zapobiec sytuacji, w której aplikacja kliencka pomijanie wymuszania zasad, należy sprawdzić, czy jest możliwe tylko włączenie nowoczesnego uwierzytelniania w aplikacji w chmurze dotyczy. 

Przykłady aplikacji, dostęp warunkowy nie ma zastosowania do klienta są:

- Pakiet Office 2010 i starszych

- Pakiet Office 2013, gdy nowoczesnego uwierzytelniania nie jest włączone.



 
## <a name="control-access-to-sharepoint-online"></a>Kontrola dostępu do usługi SharePoint Online

Oprócz nowoczesnego uwierzytelniania usługi SharePoint Online obsługuje również uwierzytelnianie starszych protokołów. Jeżeli są włączone protokoły uwierzytelniania starszej wersji, zasady dostępu warunkowego dla programu SharePoint nie są wymuszane dla klientów, którzy nie korzystają z nowoczesnego uwierzytelniania.

Protokoły uwierzytelniania starszych uzyskać dostęp do programu SharePoint można wyłączyć za pomocą **[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)** polecenia cmdlet: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Kontrola dostępu do usługi Exchange Online

Po skonfigurowaniu zasad dostępu warunkowego dla usługi Exchange Online, należy przejrzeć następujące czynności:

- Exchange ActiveSync

- Protokoły uwierzytelniania w starszej wersji



### <a name="exchange-activesync"></a>Exchange ActiveSync

Gdy program Exchange Active Sync obsługuje nowoczesnego uwierzytelniania, istnieją pewne ograniczenia dotyczące pomocy technicznej dla scenariuszy dostępu warunkowego:

- Podczas wybierania **klientów programu Exchange Active Sync** w zasadach, nie można skonfigurować inne warunki.  

    ![Platformy urządzeń](./media/conditional-access-for-exo-and-spo/05.png)

- Ustawianie wymogu uwierzytelniania wieloskładnikowego nie jest obsługiwane.  

    ![Dostęp warunkowy](./media/conditional-access-for-exo-and-spo/01.png)

Aby skutecznie chronić dostęp do usługi Exchange Online z programu Exchange ActiveSync, możesz wykonywać następujące czynności:

- Skonfiguruj zasady dostępu warunkowego obsługiwanych, wykonaj następujące czynności:

    a. Po prostu wybierz **Office 365 Exchange Online** jako aplikacja w chmurze.  

    ![Dostęp warunkowy](./media/conditional-access-for-exo-and-spo/04.png)

    b. Wybierz **programu Exchange Active Sync** jako **aplikację kliencką**.  

    ![Platformy urządzeń](./media/conditional-access-for-exo-and-spo/03.png)

- Blokuj program Exchange ActiveSync przy użyciu reguł usługi Active Directory Federation Services (AD FS).

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Protokoły uwierzytelniania w starszej wersji

Oprócz nowoczesnego uwierzytelniania usługi Exchange Online obsługuje również uwierzytelnianie starszych protokołów. Jeżeli są włączone protokoły uwierzytelniania w starszej wersji, zasady dostępu warunkowego dla usługi Exchange Online nie są wymuszane dla klientów, którzy nie korzystają z nowoczesnego uwierzytelniania.

Protokoły uwierzytelniania w starszej wersji dla usługi Exchange Online można wyłączyć przez ustawienie zasad usług AD FS. To blokuje dostęp z:

- Starsi klienci pakietu Office, takich jak pakiet Office 2013, które nie mają włączonego nowoczesnego uwierzytelniania 

- Starsze wersje pakietu Office


## <a name="set-up-ad-fs-rules"></a>Konfigurowanie zasad usług AD FS

Następujące reguły autoryzacji wystawiania umożliwia umożliwia lub blokuje ruch na poziomie usług AD FS. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Blokuj starsze ruch z ekstranetu

Dzięki zastosowaniu trzy następujące reguły: 

- Należy włączyć dostęp do:
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

### <a name="block-legacy-traffic-from-anywhere"></a>Blokuj starsze ruch z dowolnego miejsca

Dzięki zastosowaniu trzy następujące reguły:

- Należy włączyć dostęp do: 
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

Aby uzyskać więcej informacji, zobacz [co to jest dostęp warunkowy w usłudze Azure Active Directory](overview.md).

Aby uzyskać instrukcje dotyczące konfigurowania reguł oświadczeń, zobacz [Konfigurowanie reguł oświadczeń](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules). 






