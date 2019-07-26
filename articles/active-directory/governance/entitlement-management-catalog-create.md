---
title: Tworzenie wykazu i zarządzanie nim w usłudze Azure AD uprawnienia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak utworzyć nowy kontener zasobów i pakiety dostępu w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e563d86abe3817e4c77cc0d5c8df928e41563f8
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489085"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Tworzenie wykazu i zarządzanie nim w usłudze Azure AD uprawnienia do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Tworzenie katalogu

Katalog jest kontenerem zasobów i pakietów dostępu. Katalog można utworzyć, gdy chcesz grupować powiązane zasoby i pakiety dostępu. Użytkownik, który tworzy wykaz, zostaje pierwszym właścicielem katalogu. Właściciel wykazu może dodawać dodatkowych właścicieli katalogu.

**Rola wymagana wstępnie:** Administrator użytkownika lub twórca katalogu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję wykazy.

    ![Wykazy zarządzania uprawnieniami w Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Kliknij pozycję **nowy wykaz**.

1. Wprowadź unikatową nazwę wykazu i podaj opis.

    Użytkownicy będą widzieć te informacje w szczegółowych pakietach dostępu.

1. Jeśli chcesz, aby pakiety dostępu w tym katalogu były dostępne dla użytkowników, którzy otrzymają żądanie zaraz po ich utworzeniu **, ustaw wartość** **tak**.

1. Jeśli chcesz zezwolić użytkownikom w wybranych katalogach zewnętrznych na żądanie pakietów dostępu w tym wykazie, ustaw opcję **włączone dla użytkowników zewnętrznych** na **wartość tak**.

    ![Nowe okienko katalogu](./media/entitlement-management-catalog-create/new-catalog.png)

1. Kliknij przycisk **Utwórz** , aby utworzyć wykaz.

## <a name="add-resources-to-a-catalog"></a>Dodawanie zasobów do wykazu

Aby uwzględnić zasoby w pakiecie dostępu, zasoby muszą znajdować się w wykazie. Typy zasobów, które można dodać, to grupy, aplikacje i witryny usługi SharePoint Online. Grupy mogą być utworzonymi w chmurze grupami programu Office 365 lub grupami zabezpieczeń usługi Azure AD utworzonych w chmurze. Mogą to być aplikacje dla przedsiębiorstw usługi Azure AD, w tym zarówno aplikacje SaaS, jak i własne aplikacje federacyjne w usłudze Azure AD. Lokacje mogą być witrynami usługi SharePoint Online lub kolekcjami witryn usługi SharePoint Online.

**Rola wymagana wstępnie:** [Aby dodać zasoby do wykazu, zobacz wymagane role](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) .

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij  pozycję wykazy, a następnie otwórz wykaz, do którego chcesz dodać zasoby.

1. W menu po lewej stronie kliknij pozycję **zasoby**.

1. Kliknij pozycję **Dodaj zasoby**.

1. Kliknij typ zasobu: **Grupy**, **aplikacje**lub **witryny programu SharePoint**.

    Jeśli nie widzisz zasobu, który chcesz dodać lub nie możesz dodać zasobu, upewnij się, że masz wymaganą rolę w katalogu usługi Azure AD i rolę zarządzania uprawnieniami. Może być konieczne, aby ktoś z wymaganymi rolami dodał zasób do katalogu. Aby uzyskać więcej informacji, zobacz [wymagane role do dodawania zasobów do wykazu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Wybierz co najmniej jeden zasób typu, który chcesz dodać do wykazu.

1. Po zakończeniu kliknij przycisk **Dodaj**.

    Te zasoby można teraz dołączać do pakietów dostępu w katalogu.

## <a name="remove-resources-from-a-catalog"></a>Usuwanie zasobów z wykazu

Zasoby można usunąć z wykazu. Zasób może zostać usunięty z wykazu tylko wtedy, gdy nie jest używany w żadnym z pakietów dostępu do katalogu.

**Rola wymagana wstępnie:** [Aby dodać zasoby do wykazu, zobacz wymagane role](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) .

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij  pozycję wykazy, a następnie otwórz wykaz, z którego chcesz usunąć zasoby.

1. W menu po lewej stronie kliknij pozycję **zasoby**.

1. Wybierz zasoby, które chcesz usunąć.

1. Kliknij przycisk **Usuń** (lub kliknij przycisk wielokropka ( **...** ), a następnie kliknij pozycję **Usuń zasób**).

## <a name="edit-a-catalog"></a>Edytowanie katalogu

Można edytować nazwę i opis wykazu. Użytkownicy zobaczą te informacje w szczegółowych pakietach dostępu.

**Rola wymagana wstępnie:** Administrator użytkownika lub właściciel katalogu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij  pozycję wykazy, a następnie otwórz wykaz, który chcesz edytować.

1. Na stronie **Przegląd** wykazu kliknij pozycję **Edytuj**.

1. Edytuj nazwę lub opis wykazu.

1. Kliknij polecenie **Zapisz**.

## <a name="delete-a-catalog"></a>Usuwanie wykazu

Katalog można usunąć, ale tylko wtedy, gdy nie ma żadnych pakietów dostępu.

**Rola wymagana wstępnie:** Administrator użytkownika lub właściciel katalogu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij  pozycję wykazy, a następnie otwórz wykaz, który chcesz usunąć.

1. Na stronie **Przegląd**wykazu kliknij pozycję **Usuń**.

1. W wyświetlonym oknie komunikatu kliknij przycisk **tak**.

## <a name="next-steps"></a>Kolejne kroki

- [Dodaj twórcę katalogu](entitlement-management-delegate.md#add-a-catalog-creator)
- [Tworzenie pakietu dostępu i zarządzanie nim](entitlement-management-access-package-create.md)
