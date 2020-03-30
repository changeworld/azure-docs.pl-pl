---
title: Zmienianie ustawień cyklu życia pakietu dostępu w zarządzania uprawnieniami usługi Azure AD — usługa Azure Active Directory
description: Dowiedz się, jak zmienić ustawienia cyklu życia pakietu dostępu w zarządzaniu uprawnieniami usługi Azure Active Directory.
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
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261985"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Zmienianie ustawień cyklu życia pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Jako menedżer pakietów dostępu można zmienić ustawienia cyklu życia pakietu dostępu w dowolnym momencie, edytując istniejące zasady. Jeśli zmienisz datę wygaśnięcia zasad, data wygaśnięcia żądań, które są już w oczekującym zatwierdzeniu lub zatwierdzonym stanie, nie ulegnie zmianie.

W tym artykule opisano, jak zmienić ustawienia cyklu życia istniejącego pakietu dostępu.

## <a name="open-lifecycle-settings"></a>Otwórz ustawienia cyklu życia

Aby zmienić ustawienia cyklu życia pakietu dostępu, należy otworzyć odpowiednie zasady. Wykonaj następujące kroki, aby otworzyć ustawienia cyklu życia dla pakietu dostępu.

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. Kliknij **pozycję Zasady,** a następnie kliknij zasadę zawierającą ustawienia cyklu życia, które chcesz edytować.

    W dolnej części strony zostanie otwarte okienko Szczegóły zasad.

    ![Pakiet dostępu — okienko szczegółów zasad](./media/entitlement-management-shared/policy-details.png)

1. Kliknij **przycisk Edytuj,** aby edytować zasady.

    ![Pakiet dostępu — edytowanie zasad](./media/entitlement-management-shared/policy-edit.png)

1. Kliknij kartę **Cykl życia,** aby otworzyć ustawienia cyklu życia.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Następne kroki

- [Zmienianie ustawień żądania i zatwierdzania pakietu dostępu](entitlement-management-access-package-request-policy.md)