---
title: Delegowanie dostępu ładu do twórców wykazu w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza) — Azure Active Directory
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170765"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Delegowanie dostępu ładu do twórców wykazu w usłudze Azure AD uprawnień zarządzanie (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aby delegować użytkowników, którzy nie są administratorami, aby mogli tworzyć własne wykazy, można dodać tych użytkowników do roli twórcy zdefiniowana przez Zarządzanie prawami usługi Azure AD. Można dodać poszczególnych użytkowników lub dodać grupę, której członkowie mogą tworzyć wykazy.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Jako administrator IT delegowanie do twórcy katalogu

Wykonaj następujące kroki, aby przypisać użytkownika do roli twórcy katalogu.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie w sekcji **Zarządzanie prawami** kliknij pozycję **Ustawienia**.

1. Kliknij pozycję **Edytuj**.

    ![Ustawienia do dodawania twórców katalogu](./media/entitlement-management-delegate/settings-delegate.png)

1. W sekcji **Zarządzanie uprawnieniami delegowania** kliknij pozycję **Dodaj twórców wykazu** , aby wybrać użytkowników lub grupy, do których chcesz delegować tę rolę zarządzania uprawnieniami.

1. Kliknij pozycję **Wybierz**.

1. Kliknij przycisk **Save** (Zapisz).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie wykazu zasobów i zarządzanie nim](entitlement-management-catalog-create.md)
- [Delegowanie dostępu ładu do menedżerów pakietów](entitlement-management-delegate-managers.md)

