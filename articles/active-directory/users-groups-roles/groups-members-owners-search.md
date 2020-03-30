---
title: Grupy wyszukiwania i filtrowanie członków i właścicieli (wersja zapoznawcza) — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Wyszukiwanie i filtrowanie grup członków i właścicieli w witrynie Azure portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206116"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Grupy wyszukiwania i członkowie (wersja zapoznawcza) w usłudze Azure Active Directory

W tym artykule opisano, jak wyszukiwać członków i właścicieli grupy oraz jak używać filtrów wyszukiwania w ramach podglądu ulepszeń grup w portalu Azure Active Directory (Azure AD). Istnieje wiele ulepszeń w środowiskach grup, które pomogą Ci szybko i łatwo zarządzać grupami, w tym członkami i właścicielami. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zmiany w tej wersji zapoznawczej obejmują:

- Nowe funkcje wyszukiwania grup, takie jak wyszukiwanie podciągów w nazwach grup
- Nowe opcje filtrowania i sortowania na listach członków i właścicieli
- Nowe możliwości wyszukiwania list członków i właścicieli
- Dokładniejsze liczby grup dla dużych grup

## <a name="enabling-and-managing-the-preview"></a>Włączanie podglądu i zarządzanie nim

Ułatwiliśmy dołączenie do wersji zapoznawczej:

  1. Zaloguj się do [portalu usługi Azure AD](https://portal.azure.com)i wybierz pozycję **Grupy**.
  2. Na stronie Grupy — wszystkie grupy wybierz baner u góry strony, aby dołączyć do podglądu.

Najnowsze funkcje i ulepszenia można również sprawdzić, wybierając **pozycję Informacje podglądu** na stronie **Wszystkie grupy.** Po dołączeniu do podglądu możesz zobaczyć znacznik podglądu na wszystkich stronach grup, które mają ulepszenia i są częścią podglądu. Nie każda strona grup została zaktualizowana w ramach tej wersji zapoznawczej.

Jeśli masz jakiekolwiek problemy, możesz przełączyć starsze środowisko, wybierając baner u góry strony **Wszystkie grupy.** Dziękujemy za opinie, dzięki czemu możemy poprawić nasze doświadczenie.

## <a name="group-search-and-sorting"></a>Grupowanie wyszukiwania i sortowania

Wyszukiwanie listy grup zostało rozszerzone, dzięki czemu podczas wprowadzania ciągu wyszukiwania `startswith` wyszukiwanie automatycznie przeprowadza wyszukiwanie i podciąg na liście nazw grup. Wyszukiwanie podciągów jest wykonywane tylko dla całych wyrazów i nie zawiera znaków specjalnych. W wyszukiwaniu podciągów rozróżniana jest wielkość liter.

![nowe wyszukiwania podciągów na stronie Wszystkie grupy](./media/groups-members-owners-search/groups-search-preview.png)

Na przykład wyszukiwanie "zasady" zwróci teraz zarówno "Zasady MDM – Zachód" i "Grupa zasad". Grupa o nazwie "New_policy" nie zostanie zwrócona.

- Można wykonać to samo wyszukiwanie na listach członkostwa w grupach, jak również.
- Teraz można sortować listę grup według nazwy, używając strzałek po prawej stronie nagłówka kolumny nazwy, aby posortować listę w kolejności rosnącej lub malejącej.

## <a name="group-member-search-and-filtering"></a>Wyszukiwanie i filtrowanie członków grupy

### <a name="search-group-member-and-owner-lists"></a>Listy członków i właścicieli grupy wyszukiwania

Teraz możesz wyszukiwać członków określonej grupy według nazwy i wykonywać to samo wyszukiwanie na liście właścicieli grupy. W nowym doświadczeniu, jeśli wprowadzisz ciąg w polu wyszukiwania, wyszukiwanie rozpocznie się automatycznie. Na przykład, wyszukiwanie "Scott" powróci Scott Wilkinson.

![nowe wyszukiwania podciągów na listach członków i właścicieli grupy](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Lista członków i właścicieli filtru

Oprócz wyszukiwania, teraz można filtrować listy członków i właścicieli według typu użytkownika. Są to informacje znalezione w kolumnie Typ użytkownika listy. Możesz więc filtrować listę według członków i gości, aby ustalić, czy w grupie są goście.

### <a name="view-and-manage-membership"></a>Wyświetlanie członkostwa i zarządzanie nim

Oprócz wyświetlania bezpośrednich członków określonej grupy można teraz wyświetlić listę wszystkich członków grupy na stronie Członkowie. Lista członków zawiera wszystkich unikatowych członków grupy, w tym wszystkich przechodnich członków.

Można również wyszukiwać i filtrować listę bezpośrednich członków i listę wszystkich członków indywidualnie. Filtrowanie listy wszystkich członków nie ma wpływu na filtry, które są stosowane na liście bezpośrednich członków.

## <a name="improved-group-member-counts"></a>Ulepszona liczba członków grupy

Ulepszyliśmy stronę **Przegląd** grupy, aby zapewnić liczbę członków grupy dla grup o wszystkich rozmiarach. Możesz zobaczyć liczbę członków nawet dla grup liczących ponad 1000 członków. Teraz możesz zobaczyć całkowitą liczbę bezpośrednich członków dla grupy i całkowitą liczbę członków (wszystkich unikatowych członków grupy, w tym przechodnich członków) na stronie **Przegląd.**

![Większa dokładność liczby członków grupy](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje na temat pracy z grupami w usłudze Azure AD.

- [Wyświetlanie grup i elementów członkowskich](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-create-rule.md)
- [Edytowanie ustawień grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie dostępem do zasobów przy użyciu grup](../fundamentals/active-directory-manage-groups.md)
- [Zarządzanie dostępem do aplikacji SaaS przy użyciu grup](groups-saasapps.md)
- [Zarządzanie grupami przy użyciu poleceń programu PowerShell](groups-settings-v2-cmdlets.md)
- [Dodawanie subskrypcji platformy Azure do usługi Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
