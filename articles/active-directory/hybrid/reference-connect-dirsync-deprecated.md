---
title: Uaktualnienie z programów DirSync i usługi Azure AD Sync | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób uaktualniania z programu DirSync i usługi Azure AD Sync do usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 803fcc0161f2a092006e60db5a98f5bf18dce1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381182"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Upgrade Windows Azure Active Directory Sync and Azure Active Directory Sync (Uaktualnianie programów Windows Azure Active Directory Sync i Azure Active Directory Sync)
Użycie programu Azure AD Connect to najlepszy sposób na połączenie katalogu lokalnego z usługami Azure AD i Office 365. Jest to świetny czas, aby uaktualnić do usługi Azure AD Connect z synchronizacji usługi Windows Azure Active Directory (DirSync) lub usługi Azure AD Sync, ponieważ te narzędzia są teraz przestarzałe i nie są już obsługiwane od 13 kwietnia 2017 r.

Dwa narzędzia synchronizacji tożsamości, które są przestarzałe były oferowane dla pojedynczych klientów lasu (DirSync) i dla wielu lasów i innych zaawansowanych klientów (Azure AD Sync). Te starsze narzędzia zostały zastąpione jednym rozwiązaniem, które jest dostępne dla wszystkich scenariuszy: Usługi Azure AD Connect. Oferuje nowe funkcje, ulepszenia funkcji i obsługę nowych scenariuszy. Aby móc nadal synchronizować lokalne dane tożsamości z usługą Azure AD i office 365, zdecydowanie zaleca się uaktualnienie do usługi Azure AD Connect. Firma Microsoft nie gwarantuje, że te starsze wersje będą działać po 31 grudnia 2017 r.

Ostatnia wersja programu DirSync została wydana w lipcu 2014 r., a ostatnia wersja usługi Azure AD Sync została wydana w maju 2015 r.

## <a name="what-is-azure-ad-connect"></a>Co to jest program Azure AD Connect
Usługa Azure AD Connect jest następcą dirsync i usługi Azure AD Sync. Łączy wszystkie scenariusze te dwa obsługiwane. Więcej informacji na ten temat można przeczytać w obszarze [Integrowanie tożsamości lokalnych z usługą Azure Active Directory.](whatis-hybrid-identity.md)

## <a name="deprecation-schedule"></a>Harmonogram deprecation
| Data | Komentarz |
| --- | --- |
| 13 kwietnia 2016 r. |Synchronizacja usługi Windows Azure Active Directory ("DirSync") i synchronizacja usługi Microsoft Azure Active Directory ("Usługa Azure AD Sync") są ogłaszane jako przestarzałe. |
| 13 kwietnia 2017 r. |Wsparcie się kończy. Klienci nie będą już mogli otwierać sprawy pomocy technicznej bez wcześniejszego uaktualnienia do usługi Azure AD Connect. |
|31 grudnia 2017 r.|Usługa Azure AD może nie akceptować już komunikacji z synchronizacji usługi Windows Azure Active Directory ("DirSync") i synchronizacji usługi Microsoft Azure Active Directory ("Usługa Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Jak przejść do usługi Azure AD Connect
Jeśli korzystasz z programu DirSync, istnieją dwa sposoby uaktualnienia: uaktualnienie w miejscu i wdrożenie równoległe. Uaktualnienie w miejscu jest zalecane dla większości klientów i jeśli masz najnowszy system operacyjny i mniej niż 50 000 obiektów. W innych przypadkach zaleca się wykonanie wdrożenia równoległego, w którym konfiguracja programu DirSync jest przenoszona na nowy serwer z uruchomiona usługą Azure AD Connect.

| Rozwiązanie | Scenariusz |
| --- | --- |
| [Uaktualnianie z narzędzia DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Jeśli masz już uruchomiony serwer DirSync.</li> |
| [Uaktualnianie z usługi Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Jeśli przenosisz się z usługi Azure AD Sync.</li> |

Jeśli chcesz zobaczyć, jak wykonać uaktualnienie w miejscu z DirSync do usługi Azure AD Connect, zobacz ten klip wideo kanału 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Najczęściej zadawane pytania
**P: Otrzymałem powiadomienie e-mail od zespołu platformy Azure i/lub wiadomość z centrum komunikatów usługi Office 365, ale używam connect.**  
Powiadomienie zostało również wysłane do klientów korzystających z usługi Azure AD Connect z numerem kompilacji 1.0. \*.0 (przy użyciu wersji wstępnej 1.1). Firma Microsoft zaleca klientom, aby byli na bieżąco z wersjami usługi Azure AD Connect. Funkcja [automatycznego uaktualniania](how-to-connect-install-automatic-upgrade.md) wprowadzona w wersji 1.1 ułatwia zawsze instalowanie najnowszej wersji usługi Azure AD Connect.

**Pyt.: Czy dirsync/Azure AD Sync przestanie działać 13 kwietnia 2017 r.?**  
DirSync/Azure AD Sync będzie nadal działać 13 kwietnia 2017 r.  Jednak usługa Azure AD może przestać akceptować komunikację z DirSync/Azure AD Sync po 31 grudnia 2017 r.

**Pyt.: Z jakich wersji dirsync można uaktualnić?**  
Jest obsługiwany do uaktualnienia z dowolnej wersji DirSync obecnie używane. 

**Pyt.: Co z usługą Azure AD Connector dla FIM/MIM?**  
Usługa Azure AD Connector dla programu FIM/MIM **nie** została ogłoszona jako przestarzała. Jest w **zamrożeniu funkcji;** nie jest dodawana żadna nowa funkcjonalność i nie otrzymuje żadnych poprawek błędów. Firma Microsoft zaleca klientom korzystającym z niego, aby planowali przenieść się z niego do usługi Azure AD Connect. Zdecydowanie zaleca się, aby nie uruchamiać żadnych nowych wdrożeń przy użyciu go. Ten łącznik zostanie ogłoszony przestarzały w przyszłości.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
