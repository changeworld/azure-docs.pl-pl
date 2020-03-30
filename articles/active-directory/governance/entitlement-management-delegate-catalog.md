---
title: Delegowanie nadzoru dostępu do twórców wykazu w zarządzaniu uprawnieniami usługi Azure AD — usługa Azure Active Directory
description: Dowiedz się, jak delegować zarządzanie dostępem od administratorów IT do twórców katalogów i menedżerów projektów, aby mogli samodzielnie zarządzać dostępem.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120193"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegowanie nadzoru dostępu do twórców katalogu w zarządzaniu uprawnieniami usługi Azure AD

Katalog jest kontenerem zasobów i pakietów dostępu. Katalog jest tworzę, gdy chcesz grupować powiązane zasoby i pakiety dostępu. Domyślnie administrator globalny lub administrator użytkownika może [utworzyć katalog](entitlement-management-catalog-create.md)i dodać dodatkowych użytkowników jako właścicieli katalogu.

Aby delegować użytkowników, którzy nie są administratorami, aby mogli tworzyć własne katalogi, można dodać tych użytkowników do roli twórcy katalogu zdefiniowanej przez zarządzanie uprawnieniami usługi Azure AD. Można dodać poszczególnych użytkowników lub dodać grupę, której członkowie mogą następnie tworzyć katalogi.  Po utworzeniu katalogu mogą następnie dodawać zasoby, które są ich własnością, do katalogu.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Jako administrator IT delegoj się do twórcy katalogu

Wykonaj następujące kroki, aby przypisać użytkownika do roli twórcy katalogu.

**Rola wstępna:** Administrator globalny lub administrator użytkownika

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie w sekcji **Zarządzanie uprawnieniami** kliknij pozycję **Ustawienia**.

1. Kliknij pozycję **Edytuj**.

    ![Ustawienia dodawania twórców katalogu](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. W sekcji **Delegowanie uprawnień do zarządzania** kliknij pozycję **Dodaj twórców wykazu,** aby wybrać użytkowników lub grupy, do których chcesz delegować tę rolę zarządzania uprawnieniami.

1. Kliknij **pozycję Wybierz**.

1. Kliknij przycisk **Zapisz**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Zezwalaj delegowanym rolom na dostęp do witryny Azure Portal

Aby zezwolić delegowanym role, takie jak twórcy katalogu i menedżerów pakietów dostępu, aby uzyskać dostęp do witryny Azure Portal do zarządzania pakietami dostępu, należy sprawdzić ustawienie portalu administracyjnego.

**Rola wstępna:** Administrator globalny lub administrator użytkownika

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Użytkownicy**.

1. W menu po lewej stronie kliknij pozycję **Ustawienia użytkownika**.

1. Upewnij **się, że opcja Ograniczanie dostępu do portalu administracyjnego usługi Azure AD** jest **ustawiona**na Nie .

    ![Ustawienia użytkownika usługi Azure AD — portal administracyjny](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie katalogu zasobów i zarządzanie nim](entitlement-management-catalog-create.md)
- [Delegowanie nadzoru dostępu do menedżerów pakietów dostępu](entitlement-management-delegate-managers.md)

