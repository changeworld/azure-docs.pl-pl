---
title: Delegowanie dostępu ładu do twórców wykazu w usłudze Azure AD uprawnień zarządzanie — Azure Active Directory
description: Dowiedz się, jak delegować nadzór dostępu od administratorów IT do twórców wykazu i menedżerów projektów, aby mogli zarządzać samymi prawami dostępu.
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
ms.openlocfilehash: f71007b886d3cc25a7cf9dc23d784144ed4e1fbd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174382"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegowanie dostępu ładu do twórców wykazu w usłudze Azure AD uprawnienia zarządzania

Aby delegować użytkowników, którzy nie są administratorami, aby mogli tworzyć własne wykazy, można dodać tych użytkowników do roli twórcy zdefiniowana przez Zarządzanie prawami usługi Azure AD. Można dodać poszczególnych użytkowników lub dodać grupę, której członkowie mogą tworzyć wykazy.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Jako administrator IT delegowanie do twórcy katalogu

Wykonaj następujące kroki, aby przypisać użytkownika do roli twórcy katalogu.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie w sekcji **Zarządzanie prawami** kliknij pozycję **Ustawienia**.

1. Kliknij pozycję **Edytuj**.

    ![Ustawienia do dodawania twórców katalogu](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. W sekcji **Zarządzanie uprawnieniami delegowania** kliknij pozycję **Dodaj twórców wykazu** , aby wybrać użytkowników lub grupy, do których chcesz delegować tę rolę zarządzania uprawnieniami.

1. Kliknij pozycję **Wybierz**.

1. Kliknij przycisk **Save** (Zapisz).

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Zezwalaj na role delegowane w celu uzyskania dostępu do Azure Portal

Aby zezwolić na role delegowane, takie jak twórcy katalogu i menedżerów pakietów dostępu, aby uzyskać dostęp do Azure Portal do zarządzania pakietami dostępu, należy sprawdzić ustawienie portalu administracyjnego.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Użytkownicy**.

1. W menu po lewej stronie kliknij pozycję **Ustawienia użytkownika**.

1. Upewnij się, że ustawienie **Ogranicz dostęp do portalu administracyjnego usługi Azure AD** jest ustawione na wartość **nie**.

    ![Ustawienia użytkownika usługi Azure AD — Portal administracyjny](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie wykazu zasobów i zarządzanie nim](entitlement-management-catalog-create.md)
- [Delegowanie dostępu ładu do menedżerów pakietów](entitlement-management-delegate-managers.md)

