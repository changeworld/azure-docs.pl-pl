---
title: 'Usługa Azure AD Connect: wybierz typ instalacji | Dokumenty firmy Microsoft'
description: W tym temacie opisano, jak wybrać typ instalacji, który ma być używany w usłudze Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90a624a6b3b4696899af0d8606f653df260cc201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60348284"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Wybieranie typu instalacji do użycia dla programu Azure AD Connect
Usługa Azure AD Connect ma dwa typy instalacji dla nowej instalacji: ekspresowe i dostosowane. W tym temacie można zdecydować, której opcji użyć podczas instalacji.

## <a name="express"></a>Express
Express jest najczęstszą opcją i jest używany przez około 90% wszystkich nowych instalacji. Został on zaprojektowany w celu zapewnienia konfiguracji, która działa dla najbardziej typowych scenariuszy klienta.

Zakłada się, że:

- Masz jeden las usługi Active Directory lokalnie.
- Masz konto administratora przedsiębiorstwa, którego możesz użyć do instalacji.
- W lokalnej usłudze Active Directory jest mniej niż 100 000 obiektów.

Otrzymujesz:

- [Synchronizacja skrótu haseł](how-to-connect-password-hash-synchronization.md) z lokalnego do usługi Azure AD dla logowania jednokrotnego.
- Konfiguracja, która synchronizuje [użytkowników, grupy, kontakty i komputery z systemem Windows 10](concept-azure-ad-connect-sync-default-configuration.md).
- Synchronizacja wszystkich kwalifikujących się obiektów we wszystkich domenach i we wszystkich systemach organizacyjnych.
- [Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md) jest włączone, aby upewnić się, że zawsze używasz najnowszej dostępnej wersji.

Opcje, w których nadal można korzystać z expressu:

- Jeśli nie chcesz synchronizować wszystkich układów organizacyjnych, nadal możesz używać expressu, a na ostatniej stronie odznacz **Rozpocznij proces synchronizacji...***. Następnie uruchom ponownie kreatora instalacji i zmień procesory w [opcjach konfiguracji](how-to-connect-installation-wizard.md#customize-synchronization-options) i włącz zaplanowaną synchronizację.
- Chcesz włączyć jedną z funkcji w usłudze Azure AD Premium, takich jak zapisywanie hasła. Najpierw przejdź przez express, aby zakończyć początkową instalację. Następnie uruchom ponownie kreatora instalacji i zmień [opcje konfiguracji](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Niestandardowy
Dostosowana ścieżka umożliwia o wiele więcej opcji niż ekspresowe. Powinien być używany we wszystkich przypadkach, gdy konfiguracja opisana w poprzedniej sekcji dla express nie jest reprezentatywna dla organizacji.

Zastosowania:

- Nie masz dostępu do konta administratora przedsiębiorstwa w usłudze Active Directory.
- Masz więcej niż jeden las lub planujesz zsynchronizować więcej niż jeden las w przyszłości.
- Domeny w lesie są niedostępne z serwera Connect.
- Planujesz używać uwierzytelniania federacji lub przekazywania do logowania się użytkownika.
- Masz więcej niż 100 000 obiektów i musisz użyć pełnego programu SQL Server.
- Planujesz używać filtrowania opartego na grupach, a nie tylko filtrowania opartego na domenie lub u przedu.

## <a name="upgrade-from-dirsync"></a>Uaktualnianie przy użyciu narzędzia DirSync
Jeśli obecnie używasz programu DirSync, wykonaj kroki opisane w [programie Upgrade from DirSync,](how-to-dirsync-upgrade-get-started.md) aby uaktualnić istniejącą konfigurację. Dostępne są dwie różne opcje uaktualnienia:

- Uaktualnienie w miejscu, aby zainstalować connect na tym samym serwerze.
- Wdrożenie równoległe w celu zainstalowania connect na nowym serwerze, gdy istniejący serwer DirSync jest nadal operacyjny.

## <a name="upgrade-from-azure-ad-sync"></a>Uaktualnianie z usługi Azure AD Sync
Jeśli obecnie używasz usługi Azure AD Sync, możesz wykonać [te same kroki,](how-to-upgrade-previous-version.md) co podczas uaktualniania z jednej wersji Connect do nowszej. Dostępne są dwie różne opcje uaktualnienia:

- Uaktualnienie w miejscu, aby zainstalować connect na tym samym serwerze.
- Migracja wahadłowa w celu zainstalowania connect na nowym serwerze, gdy istniejący serwer usługi Azure AD Sync jest nadal operacyjny.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migracja z FIM2010 lub MIM2016
Jeśli obecnie używasz programu Forefront Identity Manager 2010 lub Microsoft Identity Manager 2016 z usługą Azure AD Connector, jedyną opcją jest migracja. Wykonaj kroki opisane w [swing-migration](how-to-upgrade-previous-version.md#swing-migration). W krokach zastąp wszelkie wzmianki o usłudze Azure AD Sync na PROGRAM FIM2010/MIM2016.

## <a name="next-steps"></a>Następne kroki
W zależności od wybranej opcji użyj tabeli zawartości po lewej stronie, aby znaleźć artykuł ze szczegółowymi krokami.
