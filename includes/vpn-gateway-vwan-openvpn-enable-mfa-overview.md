---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471553"
---
Jeśli chcesz, aby użytkownicy są monitowani o drugi współczynnik uwierzytelniania przed udzieleniem dostępu, można skonfigurować usługi Azure Multi-Factor Authentication (MFA). Usługi MFA można skonfigurować na podstawie usługi mfa lub można korzystać z usługi MFA za pośrednictwem [dostępu warunkowego](../articles/active-directory/conditional-access/overview.md).

* Usługa MFA na użytkownika może być włączona bez dodatkowych kosztów. Po włączeniu usługi MFA na użytkownika, użytkownik zostanie poproszony o uwierzytelnianie drugiego czynnika dla wszystkich aplikacji powiązanych z dzierżawy usługi Azure AD. Czynności można znaleźć [w opcji 1.](#peruser)
* Dostęp warunkowy pozwala na lepszą kontrolę nad tym, jak należy promować drugi czynnik. Można zezwolić na przypisanie usługi MFA tylko do sieci VPN i wykluczyć inne aplikacje powiązane z dzierżawą usługi Azure AD. Czynności można znaleźć [w opcji 2.](#conditional)