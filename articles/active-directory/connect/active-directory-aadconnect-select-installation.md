---
title: 'Azure AD Connect: Wybierz typ instalacji | Dokumentacja firmy Microsoft'
description: W tym temacie przedstawiono sposób Wybieranie typu instalacji do użycia programu Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 76f1ce12ab149f57ec6e995d132de83105c5e0ca
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Wybierz typ instalacji, które można użyć programu Azure AD Connect
Azure AD Connect ma dwa typy instalacji nowej instalacji: Express i niestandardowych. Ten temat ułatwia podjęcie decyzji, którą opcję do użycia podczas instalacji.

## <a name="express"></a>Express
Express jest najbardziej typowych opcji i jest używany przez wszystkie nowe instalacje około 90%. Została zaprojektowana w celu zapewnienia odpowiedniej konfiguracji najbardziej typowych scenariuszy.

Zakłada się:

- Masz pojedynczego Active Directory lasu lokalnego.
- Masz konto administratora przedsiębiorstwa, używanej do instalacji.
- Masz mniej niż 100 000 obiektów w usłudze Active Directory lokalnymi.

Pobierz:

- [Synchronizacja skrótów haseł](active-directory-aadconnectsync-implement-password-hash-synchronization.md) z lokalnego do usługi Azure AD dla logowania jednokrotnego.
- Konfiguracja, która synchronizuje [użytkowników, grup, kontaktów i komputerach z systemem Windows 10](active-directory-aadconnectsync-understanding-default-configuration.md).
- Synchronizacja wszystkich kwalifikujących się obiekty w wszystkie domeny i wszystkich jednostkach organizacyjnych.
- [Automatyczne uaktualnianie](active-directory-aadconnect-feature-automatic-upgrade.md) jest włączona, upewnij się, że należy zawsze używać najnowszej dostępnej wersji.

Opcje, których można nadal używać Express:

- Jeśli nie chcesz synchronizować wszystkich jednostkach organizacyjnych, można nadal używać Express i na ostatniej stronie, usuń zaznaczenie ** Uruchom proces synchronizacji... ***. A następnie ponownie uruchom Kreatora instalacji i zmienić jednostki organizacyjne w [opcje konfiguracji](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options) i Włącz synchronizację według harmonogramu.
- Chcesz włączyć funkcji w usłudze Azure AD Premium, takie jak zapisywanie zwrotne haseł. Najpierw należy przeprowadzić express można pobrać ukończeniem instalacji początkowej. A następnie ponownie uruchom Kreatora instalacji i zmienić [opcje konfiguracji](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Niestandardowy
Ścieżka dostosowane umożliwia wiele więcej opcji niż express. Stosuje we wszystkich przypadkach, gdy konfiguracji opisanych w poprzedniej sekcji Express nie jest reprezentatywna dla Twojej organizacji.

Zastosowania:

- Nie masz dostępu do konta administratora przedsiębiorstwa w usłudze Active Directory.
- Masz więcej niż jednym lesie lub planowane jest synchronizowanie więcej niż jednym lesie w przyszłości.
- Masz domen w lesie nie jest dostępny z serwera, Połącz.
- Zamierzasz używać federacyjnego lub przekazywanego uwierzytelniania dla logowania użytkownika.
- Możesz mieć więcej niż 100 000 obiektów i muszą korzystać pełnego serwera SQL.
- Zamierzasz używać filtrowanie na podstawie grupy, a nie tylko domeny lub filtrowanie na podstawie jednostki Organizacyjnej.

## <a name="upgrade-from-dirsync"></a>Uaktualnianie przy użyciu narzędzia DirSync
Jeśli obecnie używasz narzędzia DirSync, należy wykonać czynności opisane w [uaktualnienia narzędzia DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) do uaktualnienia istniejącej konfiguracji. Istnieją dwa różne opcje uaktualniania:

- Uaktualnienie w miejscu do zainstalowania na tym samym serwerze Connect.
- Wdrożenie równoległe do instalacji Connect na nowym serwerze, gdy nadal działa istniejącego serwera narzędzia DirSync.

## <a name="upgrade-from-azure-ad-sync"></a>Uaktualnianie programu Azure AD Sync
Jeśli aktualnie używasz usługi Azure AD Sync, a następnie można wykonać [te same czynności](active-directory-aadconnect-upgrade-previous-version.md) jako podczas uaktualniania z jednej wersji programu Connect do nowszej. Istnieją dwa różne opcje uaktualniania:

- Uaktualnienie w miejscu do zainstalowania na tym samym serwerze Connect.
- Zasięg migration, aby zainstalować Connect na nowym serwerze, gdy istniejący serwer synchronizacji usługi Azure AD jest nadal działa.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrowanie z usługi FIM2010 lub MIM2016
Jeśli używasz obecnie programu Forefront Identity Manager 2010 lub Microsoft Identity Manager 2016 z łącznika usługi Azure AD, jedyną opcją jest migracji. Wykonaj kroki opisane w [migracji zasięg](active-directory-aadconnect-upgrade-previous-version.md#swing-migration). W krokach Zastąp FIM2010/MIM2016 wszelkie uwagi Azure AD Sync.

## <a name="next-steps"></a>Kolejne kroki
W zależności od opcji wybranych do użycia umożliwia odnalezienie artykułu z szczegółowy opis kroków spis treści z lewej strony.
