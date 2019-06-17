---
title: 'Program Azure AD Connect: Wybierz typ instalacji | Dokumentacja firmy Microsoft'
description: W tym temacie przedstawiono kroki Wybieranie typu instalacji do użycia programu Azure AD Connect
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60348284"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Wybieranie typu instalacji do użycia programu Azure AD Connect
Program Azure AD Connect udostępnia dwa typy instalacji nowej instalacji: ekspresową i niestandardową. Ten temat ułatwia podjęcie decyzji, którą opcję do użycia podczas instalacji.

## <a name="express"></a>Express
Express jest najbardziej typowych opcji i jest używany przez około 90% wszystkich nowych urządzeń. Została zaprojektowana w celu zapewnienia odpowiedniej konfiguracji najbardziej typowych scenariuszy.

Zakłada:

- Masz jednego Active Directory lasu lokalnego.
- Masz konto administratora przedsiębiorstwa, używane do instalacji.
- Masz mniej niż 100 000 obiektów w usłudze Active Directory w środowisku lokalnym.

Oto, co otrzymujesz:

- [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md) ze środowiska lokalnego do usługi Azure AD na potrzeby logowania jednokrotnego.
- Konfiguracja, która synchronizuje [użytkownikom, grupom, kontaktów i komputerach z systemem Windows 10](concept-azure-ad-connect-sync-default-configuration.md).
- Synchronizacja wszystkich kwalifikujących się obiektów w wszystkie domeny i wszystkich jednostek organizacyjnych.
- [Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md) jest włączona, aby upewnić się, należy zawsze używać najnowszej dostępnej wersji.

Opcje, których można nadal używać Express:

- Jeśli nie chcesz synchronizować wszystkich jednostek organizacyjnych, można nadal używać Express i na ostatniej stronie, usuń zaznaczenie ** proces synchronizacji rozpocznie się... ***. A następnie ponownie uruchom Kreatora instalacji, a następnie Zmień jednostki organizacyjne w [opcje konfiguracji](how-to-connect-installation-wizard.md#customize-synchronization-options) i Włącz synchronizację według harmonogramu.
- Chcesz włączyć jedną z funkcji w usłudze Azure AD Premium, takich jak funkcja zapisywania zwrotnego haseł. Najpierw wykonaj express można pobrać ukończeniem instalacji początkowej. A następnie ponownie uruchom Kreatora instalacji, a następnie zmień [opcje konfiguracji](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Niestandardowy
Ścieżka dostosowane umożliwia wiele opcji więcej niż express. Można używać we wszystkich przypadkach, w którym konfiguracji opisanej w poprzedniej sekcji dotyczącej express nie jest językiem dla Twojej organizacji.

Zastosowania:

- Nie masz dostępu do konta administratora przedsiębiorstwa w usłudze Active Directory.
- Masz więcej niż jednym lesie lub planujesz zsynchronizować więcej niż jednym lesie w przyszłości.
- Masz domen w lesie nie jest dostępny z serwera Connect.
- Planowanie na potrzeby federacji lub uwierzytelniania przekazywanego logowania użytkownika.
- Masz ponad 100 000 obiektów, a konieczne użycie pełnej wersji programu SQL Server.
- Zamierzasz używać filtrowania na podstawie grupy i nie tylko domeny lub filtrowanie na podstawie jednostki Organizacyjnej.

## <a name="upgrade-from-dirsync"></a>Uaktualnianie przy użyciu narzędzia DirSync
Jeśli obecnie używasz narzędzia DirSync, należy wykonać czynności opisane w [uaktualnianie z narzędzia DirSync](how-to-dirsync-upgrade-get-started.md) uaktualnienia istniejącej konfiguracji. Istnieją dwa różne opcje uaktualniania:

- Uaktualnienie w miejscu do zainstalowania Connect na tym samym serwerze.
- Wdrożenie równoległe, aby zainstalować Connect na nowym serwerze, gdy nadal działa istniejącego serwera narzędzia DirSync.

## <a name="upgrade-from-azure-ad-sync"></a>Uaktualnianie z programu Azure AD Sync
Jeśli obecnie używasz usługi Azure AD Sync, a następnie możesz wykonać [te same kroki](how-to-upgrade-previous-version.md) jako podczas uaktualniania z jednej wersji Connect do nowszej. Istnieją dwa różne opcje uaktualniania:

- Uaktualnienie w miejscu do zainstalowania Connect na tym samym serwerze.
- Migracja typu Swing, aby zainstalować Connect na nowym serwerze, gdy istniejący serwer usługi Azure AD Sync jest nadal działa.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migracja z FIM2010 lub programie MIM2016
Jeśli obecnie używasz programu Forefront Identity Manager 2010 lub Microsoft Identity Manager 2016 z łącznika usługi Azure AD, obecnie jedyną opcją jest migracji. Wykonaj kroki opisane w [migracja typu swing](how-to-upgrade-previous-version.md#swing-migration). W poniższych krokach Zastąp FIM2010/programie MIM2016 odpowiednią wzmianką w usłudze Azure AD Sync.

## <a name="next-steps"></a>Kolejne kroki
W zależności od opcji wybranej do użycia Użyj spis treści po lewej stronie, aby znaleźć artykuł z szczegółowy opis kroków.
