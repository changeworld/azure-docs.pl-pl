---
title: Delegowanie nadzoru dostępu do menedżerów pakietów dostępu w zarządzania uprawnieniami usługi Azure AD — usługa Azure Active Directory
description: Dowiedz się, jak delegować zarządzanie dostępem od administratorów IT do uzyskiwania dostępu do menedżerów pakietów i menedżerów projektów, aby mogli samodzielnie zarządzać dostępem.
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
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73174356"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegowanie nadzoru dostępu do menedżerów pakietów dostępu w zarządzaniu uprawnieniami usługi Azure AD

Aby delegować tworzenie i zarządzanie pakietami dostępu w katalogu, należy dodać użytkowników do roli menedżera pakietów dostępu. Menedżerowie pakietów dostępu muszą być zaznajomieni z koniecznością żądania dostępu użytkowników do zasobów w katalogu. Na przykład jeśli katalog jest używany dla projektu, a następnie potencjalny klient projektu może być menedżerem pakietów dostępu dla tego katalogu.  Menedżerowie pakietów dostępu nie mogą dodawać zasobów do katalogu, ale mogą zarządzać pakietami dostępu i zasadami w katalogu.  Podczas delegowannia do menedżera pakietów dostępu ta osoba może być odpowiedzialna za:

- Jakie role użytkownik będzie miał do zasobów w katalogu
- Kto będzie potrzebował dostępu
- Kto musi zatwierdzić żądania dostępu
- Jak długo będzie trwał projekt

W tym klipie wideo przedstawiono informacje na temat delegowania nadzoru dostępu od właściciela katalogu do menedżera pakietów dostępu.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Jako właściciel katalogu delegoj się do menedżera pakietów dostępu

Wykonaj następujące kroki, aby przypisać użytkownika do roli menedżera pakietów dostępu:

**Rola wstępna:** Administrator globalny, administrator użytkownika lub właściciel katalogu

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Katalogi,** a następnie otwórz katalog, do którego chcesz dodać administratorów.

1. W menu po lewej stronie kliknij pozycję **Role i administratorzy**.

    ![Katalogi ról i administratorów](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kliknij **pozycję Dodaj menedżerów pakietów dostępu,** aby wybrać członków dla tych ról.

1. Kliknij **przycisk Wybierz,** aby dodać tych członków.

## <a name="remove-an-access-package-manager"></a>Usuwanie menedżera pakietów dostępu

Wykonaj następujące kroki, aby usunąć użytkownika z roli menedżera pakietów dostępu:

**Rola wstępna:** Administrator globalny, administrator użytkownika lub właściciel katalogu

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Katalogi,** a następnie otwórz katalog, do którego chcesz dodać administratorów.

1. W menu po lewej stronie kliknij pozycję **Role i administratorzy**.

1. Dodaj znacznik wyboru obok menedżera pakietów dostępu, który chcesz usunąć.

1. Kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie nowego pakietu dostępu](entitlement-management-access-package-create.md)
