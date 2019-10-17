---
title: Zmień ustawienia cyklu życia pakietu dostępu w usłudze Azure AD uprawnienia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak zmienić ustawienia cyklu życia pakietu dostępu w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46d63a12443edf04db3570fa43fbd8f20619122e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392347"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Zmienianie ustawień cyklu życia pakietu dostępu w usłudze Azure AD uprawnienia zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jako Menedżer pakietów dostępu można zmienić ustawienia cyklu życia pakietu dostępu w dowolnym momencie, edytując istniejące zasady. Jeśli zmienisz datę wygaśnięcia zasad, Data wygaśnięcia żądań, które już oczekują na zatwierdzenie lub zatwierdzonego stanu, nie ulegnie zmianie.

W tym artykule opisano sposób zmiany ustawień cyklu życia istniejącego pakietu dostępu.

## <a name="open-lifecycle-settings"></a>Otwórz ustawienia cyklu życia

Aby zmienić ustawienia cyklu życia pakietu dostępu, należy otworzyć odpowiednie zasady. Wykonaj następujące kroki, aby otworzyć ustawienia cyklu życia pakietu dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **zasady** , a następnie kliknij zasady, które mają ustawienia cyklu życia, które chcesz edytować.

    Zostanie otwarte okienko Szczegóły zasad w dolnej części strony.

    ![Dostęp do programu Access — okienko Szczegóły zasad](./media/entitlement-management-shared/policy-details.png)

1. Kliknij przycisk **Edytuj** , aby edytować zasady.

    ![Dostęp do pakietu — edycja zasad](./media/entitlement-management-shared/policy-edit.png)

1. Kliknij kartę **cykl życia** , aby otworzyć ustawienia cyklu życia.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Następne kroki

- [Ustawienia żądania zmiany i zatwierdzania dla pakietu dostępu](entitlement-management-access-package-request-policy.md)