---
title: Uaktualnianie z narzędzia DirSync i Azure AD Sync | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób uaktualniania z narzędzia DirSync i Azure AD Sync do programu Azure AD Connect.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381182"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Uaktualnianie systemu Windows Azure Active Directory Sync i Azure Active Directory Sync
Użycie programu Azure AD Connect to najlepszy sposób na połączenie katalogu lokalnego z usługami Azure AD i Office 365. Jest to doskonały moment na uaktualnienie do programu Azure AD Connect, z Windows Azure Active Directory Sync (DirSync) lub Azure AD Sync, te narzędzia są one przestarzałe i nie są już obsługiwane 13 kwietnia 2017 r.

Narzędzia synchronizacji tożsamości dwóch, które zostały zaniechane były udostępniane dla klientów z pojedynczym lasem (DirSync) i obejmującego wiele lasów i inne zaawansowane klientów (Azure AD Sync). Te starsze narzędzia zostały zamienione na pojedyncze rozwiązanie, które są dostępne we wszystkich scenariuszach: Azure AD Connect. Oferuje nową funkcję, ulepszone funkcje i obsługę nowych scenariuszy. Aby móc nadal się synchronizują dane tożsamości lokalnych z usługą Azure AD i Office 365, zdecydowanie zaleca się uaktualnienie do programu Azure AD Connect. Microsoft nie gwarantuje tych starszych wersji działać po 31 grudnia 2017 r.

Ostatnia wersja narzędzia DirSync została wydana w lipcu 2014 i ostatniego wydania usługi Azure AD Sync wydanej w maju 2015 r.

## <a name="what-is-azure-ad-connect"></a>Co to jest program Azure AD Connect
Program Azure AD Connect jest następcą programu DirSync i Azure AD Sync. Łączy ona wszystkie scenariusze te dwie obsługiwane. Możesz dowiedzieć się więcej o nim w [integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Ustalonym harmonogramem
| Date | Komentarz |
| --- | --- |
| 13 kwietnia 2016 r. |Windows Azure Active Directory Sync ("DirSync") i Microsoft Azure Active Directory Sync ("Azure AD Sync") są ogłoszone jako przestarzałe. |
| 13 kwietnia 2017 r. |Obsługa kończy się. Klienci nie będą już mogli otworzyć zgłoszenie do pomocy technicznej bez najpierw uaktualnienia do programu Azure AD Connect. |
|Do 31 grudnia 2017 r.|Usługa Azure AD może już nie akceptują komunikację od Windows Azure Active Directory Sync ("DirSync") i Microsoft Azure Active Directory Sync ("Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Temat przechodzenia do usługi Azure AD Connect
Jeśli używasz narzędzia DirSync, istnieją dwa sposoby, które można uaktualnić: Uaktualnianie i równoległych wdrożeń w miejscu. Uaktualnienie w miejscu jest zalecane dla większości klientów i czy masz najnowsze operacyjnego i mniej niż 50 000 obiektów. W innych przypadkach zalecane jest przeprowadzenie wdrożenia równoległego, gdzie konfiguracji narzędzia DirSync, zostanie przeniesiony do nowego serwera z programem Azure AD Connect.

| Rozwiązanie | Scenariusz |
| --- | --- |
| [Uaktualnianie z narzędzia DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Jeśli masz istniejące już działający serwer DirSync.</li> |
| [Uaktualnianie z programu Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Jeśli przenosisz z usługi Azure AD Sync.</li> |

Jeśli chcesz zobaczyć, jak przeprowadzić uaktualnienie w miejscu z narzędzia DirSync do programu Azure AD Connect, następnie oglądać to wideo Channel 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Często zadawane pytania
**Pyt.: Otrzymuję powiadomienie e-mail od zespołu platformy Azure i/lub komunikat z Centrum wiadomości usługi Office 365, ale używam Connect.**  
Klienci korzystający z usługi Azure AD Connect przy użyciu numeru kompilacji 1.0 również zostało wysłane powiadomienie. \*.0 (przy użyciu wersji pre-1.1). Firma Microsoft zaleca klientom aktualne informacje o wersji usługi Azure AD Connect. [Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md) funkcja wprowadzona w wersji 1.1 ułatwia posiadanie najnowszej wersji programu Azure AD Connect.

**Pyt.: Narzędzie DirSync/Azure AD Sync przestanie działać 13 kwietnia 2017 r.?**  
Narzędzie DirSync/Azure AD Sync będą nadal działać 13 kwietnia 2017 r.  Jednak usługi Azure AD może już nie akceptują komunikację od narzędzia DirSync/Azure AD Sync po 31 grudnia 2017 r.

**Pyt.: Które wersje narzędzia DirSync można uaktualnić z?**  
Możliwe jest uaktualnienie z dowolnej wersji narzędzia DirSync, są obecnie używane. 

**Pyt.: Informacje o łączniku usługi AD systemu Azure dla programu FIM/MIM?**  
Łącznik usługi Azure AD dla programu FIM/MIM ma **nie** zostały ogłoszone jako przestarzałe. Znajduje się na **blokowanie funkcji**; jest dodane żadne nowe funkcje i odbiera nie poprawki. Firma Microsoft zaleca klientom używanie go do ma zostać przeniesiona z niego do programu Azure AD Connect. Zdecydowanie zaleca się nie uruchomić we wszystkich nowych wdrożeniach korzystania z niego. Ten łącznik zostanie ogłoszona w przyszłości przestarzałe.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
