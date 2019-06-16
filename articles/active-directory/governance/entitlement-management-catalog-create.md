---
title: Tworzenie i zarządzanie nimi katalogu w usłudze Azure AD uprawnienie management (wersja zapoznawcza) — usługi Azure Active Directory
description: Dowiedz się, jak utworzyć nowy kontener zasoby i dostęp do pakietów w usłudze Azure Active Directory Zarządzanie uprawnieniami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541618"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Tworzenie i zarządzanie nimi wykazu w zarządzanie uprawnieniami w usłudze Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Tworzenie katalogu

Katalog jest kontenerem, zasoby i dostęp do pakietów. Tworzenie katalogu, gdy chcesz grupować powiązane zasoby i uzyskiwać dostęp do pakietów. Kto tworzy katalog staje się pierwszym właściciela katalogu. Właściciel katalogu można dodać katalogu dodatkowych właścicieli.

**Rola wymagań wstępnych:** Użytkownika administrator lub twórca katalogu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Kliknij przycisk **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **katalogi**.

    ![Uprawnienia zarządzania katalogi w witrynie Azure portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Kliknij przycisk **nowy katalog**.

1. Wprowadź unikatową nazwę dla katalogu i podaj opis.

    Użytkownicy będą widzieć te informacje w szczegółach pakietu dostępu.

1. Pakiety dostępu, w tym katalogu, które mają być dostępne dla użytkowników zażądać zaraz po ich utworzeniu, ustawić **włączone** do **tak**.

1. Jeśli chcesz umożliwić użytkownikom w wybrane katalogi zewnętrzne, aby móc żądać dostępu do pakietów w tym katalogu, należy ustawić **włączone dla użytkowników zewnętrznych** do **tak**.

    ![Nowe okienko katalogu](./media/entitlement-management-catalog-create/new-catalog.png)

1. Kliknij przycisk **Utwórz** tworzenia wykazu.

## <a name="add-resources-to-a-catalog"></a>Dodawanie zasobów do katalogu

Aby dołączyć zasoby pakietu programu access, zasoby muszą istnieć w katalogu. Typy zasobów, które można dodać to grupy, aplikacje i witryny usługi SharePoint Online.

**Rola wymagań wstępnych:** Administrator użytkownika lub właściciela katalogu

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **katalogi** , a następnie otwórz katalog, którego chcesz dodać zasoby do.

1. W menu po lewej stronie kliknij **zasobów**.

1. Kliknij przycisk **Dodaj zasoby**.

1. Kliknij typ zasobu: **Grupy**, **aplikacje**, lub **witryny programu SharePoint**.

    Jeśli jesteś twórcą katalogu, możesz dodać wszelkie grupy usługi Office 365 lub grupy zabezpieczeń usługi Azure AD, których właścicielem do katalogu. Jeśli istnieje grupa, którą chcesz przypisać do użytkowników, ale nie jesteś właścicielem grupy, należy mieć administrator użytkowników, dodać tę grupę do katalogu.

    Jeśli jesteś twórcą katalogu, możesz dodać wszelkie jesteś właścicielem, łącznie z aplikacjami SaaS i własne aplikacje Sfederowane z usługą Azure AD do katalogu aplikacji enterprise dla usługi Azure AD. W przypadku aplikacji, która ma zostać przypisana użytkownikowi, ale nie są jego własnością, musisz mieć administrator użytkowników, dodaj ją do katalogu. Gdy aplikacja jest częścią katalogu, możesz wybrać jedną z ról aplikacji w pakiecie dostępu.

1. Wybierz co najmniej jeden zasób typu, który chcesz dodać do katalogu.

1. Po zakończeniu kliknij przycisk **Dodaj**.

    Te zasoby mogą być teraz dołączane do dostępu do pakietów w wykazie.

## <a name="remove-resources-from-a-catalog"></a>Usuń zasoby z katalogu

Możesz usunąć zasoby z katalogu. Zasób tylko można usunąć z katalogu, jeśli nie jest on używany w żadnym z pakietów dostępu do wykazu.

**Rola wymagań wstępnych:** Administrator użytkownika lub właściciela katalogu

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **katalogi** , a następnie otwórz katalog, aby usunąć zasoby z.

1. W menu po lewej stronie kliknij **zasobów**.

1. Wybierz zasoby, które chcesz usunąć.

1. Kliknij przycisk **Usuń** (lub kliknij przycisk wielokropka ( **...** ) a następnie kliknij przycisk **Usuń zasób**).

## <a name="add-catalog-owners-or-access-package-managers"></a>Dodawanie właścicieli katalogu ani uzyskać dostępu do menedżerów pakietów

Jeśli chcesz delegować zarządzanie w katalogu lub pakietów dostępu w katalogu, dodawanie właścicieli katalogu lub dostęp do menedżerów pakietów. Kto tworzy wykaz staje się pierwszym właściciela katalogu.

**Rola wymagań wstępnych:** Administrator użytkownika lub właściciela katalogu

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **katalogi** , a następnie otwórz katalogu, którego chcesz dodać administratorów.

1. W menu po lewej stronie kliknij **role i Administratorzy**.

1. Kliknij przycisk **Dodawanie właścicieli** lub **dodać menedżerów pakietów dostępu** zaznacz składniki dla tych ról.

1. Kliknij przycisk **wybierz** można dodać te składowe.

## <a name="edit-a-catalog"></a>Edytuj katalog

Można edytować nazwę i opis dla katalogu. Użytkownicy widzą tę informację w szczegóły pakietu dostępu.

**Rola wymagań wstępnych:** Administrator użytkownika lub właściciela katalogu

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **katalogi** , a następnie otwórz katalog, który chcesz edytować.

1. W wykazie **Przegląd** kliknij **Edytuj**.

1. Edytuj nazwę lub opis wykazu.

1. Kliknij pozycję **Zapisz**.

## <a name="delete-a-catalog"></a>Usuwanie katalogu

Można usunąć katalogu, ale tylko wtedy, gdy nie ma żadnych pakietów dostępu.

**Rola wymagań wstępnych:** Administrator użytkownika lub właściciela katalogu

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **katalogi** , a następnie otwórz katalog do usunięcia.

1. W wykazie **Przegląd**, kliknij przycisk **Usuń**.

1. W wyświetlonym oknie komunikatu kliknij **tak**.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie i zarządzanie nimi pakietu dostępu](entitlement-management-access-package-create.md)
