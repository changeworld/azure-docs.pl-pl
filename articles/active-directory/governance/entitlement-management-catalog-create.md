---
title: Tworzenie & zarządzanie zasobami w zarządzaniu uprawnieniami — Usługa Azure AD
description: Dowiedz się, jak utworzyć nowy kontener zasobów i pakietów dostępu w zarządzaniu uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbafb0c506fd34e975efbe08c75c15c8c0b3888e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128951"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Tworzenie katalogu zasobów w zarządzaniu uprawnieniami usługi Azure AD i zarządzanie nim

## <a name="create-a-catalog"></a>Tworzenie katalogu

Katalog jest kontenerem zasobów i pakietów dostępu. Katalog jest tworzę, gdy chcesz grupować powiązane zasoby i pakiety dostępu. Kto tworzy katalog staje się pierwszym właścicielem katalogu. Właściciel katalogu może dodać dodatkowych właścicieli katalogu.

**Rola wstępna:** Administrator globalny, administrator użytkownika lub twórca katalogu

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Katalogi**.

    ![Katalogi zarządzania uprawnieniami w witrynie Azure portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Kliknij **pozycję Nowy katalog**.

1. Wprowadź unikatową nazwę katalogu i podaj opis.

    Użytkownicy zobaczą te informacje w szczegółach pakietu dostępu.

1. Jeśli chcesz, aby pakiety dostępu w tym katalogu były dostępne dla użytkowników, aby natychmiast po ich utworzeniu, ustaw **enabled** to **Tak**.

1. Jeśli chcesz zezwolić użytkownikom w wybranych katalogach zewnętrznych na żądanie pakietów dostępu w tym katalogu, ustaw **opcję Włączone dla użytkowników zewnętrznych** na **Tak**.

    ![Nowe okienko katalogu](./media/entitlement-management-shared/new-catalog.png)

1. Kliknij **przycisk Utwórz,** aby utworzyć katalog.

### <a name="creating-a-catalog-programmatically"></a>Programowe tworzenie katalogu

Można również utworzyć katalog przy użyciu programu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, `EntitlementManagement.ReadWrite.All` która ma delegowane uprawnienia można wywołać interfejsu API, aby [utworzyć accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Dodawanie zasobów do katalogu

Aby uwzględnić zasoby w pakiecie dostępu, zasoby muszą istnieć w katalogu. Typy zasobów, które można dodać, to grupy, aplikacje i witryny usługi SharePoint Online. Grupy mogą być grupami usługi Office 365 utworzonymi w chmurze lub utworzonymi w chmurze grupami zabezpieczeń usługi Azure AD. Aplikacje mogą być aplikacjami dla przedsiębiorstw usługi Azure AD, w tym zarówno aplikacjami SaaS, jak i własnymi aplikacjami sfederowanymi z usługą Azure AD. Witrynami mogą być witryny usługi SharePoint Online lub zbiory witryn usługi SharePoint Online.

**Rola wstępna:** Zobacz [Wymagane role, aby dodać zasoby do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Katalogi,** a następnie otwórz katalog, do którego chcesz dodać zasoby.

1. W menu po lewej stronie kliknij pozycję **Zasoby**.

1. Kliknij **pozycję Dodaj zasoby**.

1. Kliknij typ zasobu: **Grupy i zespoły,** **Aplikacje**lub **Witryny programu SharePoint**.

    Jeśli nie widzisz zasobu, który chcesz dodać lub nie możesz dodać zasobu, upewnij się, że masz wymaganą rolę zarządzania katalogiem usługi Azure AD i zarządzanie uprawnieniami. Może być konieczne dodanie zasobu do katalogu przez osobę z wymaganymi rolami. Aby uzyskać więcej informacji, zobacz [Wymagane role do dodawania zasobów do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Wybierz jeden lub więcej zasobów typu, które chcesz dodać do katalogu.

    ![Dodawanie zasobów do katalogu](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Po zakończeniu kliknij przycisk **Dodaj**.

    Te zasoby mogą być teraz uwzględnione w pakietach dostępu w katalogu.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Programowe dodawanie zasobu do katalogu

Zasób można również dodać do katalogu za pomocą programu Microsoft Graph.  Użytkownik w odpowiedniej roli lub właściciel katalogu i zasobu z aplikacją, która ma delegowane `EntitlementManagement.ReadWrite.All` uprawnienia można wywołać interfejsu API, aby utworzyć [accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Usuwanie zasobów z katalogu

Zasoby można usunąć z katalogu. Zasób można usunąć z katalogu tylko wtedy, gdy nie jest używany w żadnym z pakietów dostępu katalogu.

**Rola wstępna:** Zobacz [Wymagane role, aby dodać zasoby do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Katalogi,** a następnie otwórz katalog, z którego chcesz usunąć zasoby.

1. W menu po lewej stronie kliknij pozycję **Zasoby**.

1. Wybierz zasoby, które chcesz usunąć.

1. Kliknij **pozycję Usuń** (lub kliknij wielokropek (**...**), a następnie kliknij pozycję **Usuń zasób**).

## <a name="add-additional-catalog-owners"></a>Dodawanie dodatkowych właścicieli katalogu

Użytkownik, który utworzył katalog staje się pierwszym właścicielem katalogu. Aby delegować zarządzanie katalogiem, należy dodać użytkowników do roli właściciela katalogu. Pomaga to współużytkować obowiązki związane z zarządzaniem katalogiem. 

Wykonaj następujące kroki, aby przypisać użytkownika do roli właściciela katalogu:

**Rola wstępna:** Administrator globalny, administrator użytkownika lub właściciel katalogu

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Katalogi,** a następnie otwórz katalog, do którego chcesz dodać administratorów.

1. W menu po lewej stronie kliknij pozycję **Role i administratorzy**.

    ![Katalogi ról i administratorów](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kliknij **pozycję Dodaj właścicieli,** aby wybrać członków dla tych ról.

1. Kliknij **przycisk Wybierz,** aby dodać tych członków.

## <a name="edit-a-catalog"></a>Edytowanie katalogu

Można edytować nazwę i opis katalogu. Użytkownicy widzą te informacje w szczegółach pakietu dostępu.

**Rola wstępna:** Administrator globalny, administrator użytkownika lub właściciel katalogu

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Katalogi,** a następnie otwórz katalog, który chcesz edytować.

1. Na stronie **Przegląd** katalogu kliknij pozycję **Edytuj**.

1. Edytuj nazwę, opis lub włączone ustawienia katalogu.

    ![Edytowanie ustawień katalogu](./media/entitlement-management-shared/catalog-edit.png)

1. Kliknij przycisk **Zapisz**.

## <a name="delete-a-catalog"></a>Usuwanie katalogu

Katalog można usunąć, ale tylko wtedy, gdy nie ma żadnych pakietów dostępu.

**Rola wstępna:** Administrator globalny, administrator użytkownika lub właściciel katalogu

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Katalogi,** a następnie otwórz katalog, który chcesz usunąć.

1. W **przeglądzie**katalogu kliknij pozycję **Usuń**.

1. W wyświetlonym oknie komunikatu kliknij przycisk **Tak**.

### <a name="deleting-a-catalog-programmatically"></a>Programowe usuwanie katalogu

Można również usunąć katalog za pomocą programu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, `EntitlementManagement.ReadWrite.All` która ma delegowane uprawnienia można wywołać interfejsu API, aby [usunąć accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Następne kroki

- [Delegowanie nadzoru dostępu do menedżerów pakietów dostępu](entitlement-management-delegate-managers.md)
