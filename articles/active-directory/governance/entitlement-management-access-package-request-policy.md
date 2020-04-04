---
title: Zmienianie ustawień żądania i zatwierdzania pakietu dostępu w zarządzania uprawnieniami usługi Azure AD — usługa Azure Active Directory
description: Dowiedz się, jak zmienić ustawienia żądania i zatwierdzenia pakietu dostępu w zarządzaniu uprawnieniami usługi Azure Active Directory.
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
ms.date: 03/30/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0eae4f53283c33f3d7372a606f8c0a3ac27d079
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655966"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Zmienianie ustawień żądania i zatwierdzania pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Jako menedżer pakietów dostępu możesz zmienić użytkowników, którzy mogą zażądać pakietu dostępu w dowolnym momencie, edytując zasady lub dodając nowe zasady. Można również zmienić ustawienia zatwierdzania.

W tym artykule opisano sposób zmiany ustawień żądania i zatwierdzania istniejącego pakietu dostępu.

## <a name="choose-between-one-or-multiple-polices"></a>Wybierz jedną lub wiele schołek

Sposób określania, kto może żądać pakietu dostępu, jest z zasadami. Podczas tworzenia pakietu dostępu należy określić ustawienie żądania i zatwierdzania, które tworzy zasady. Większość pakietów dostępu będzie miała jedną zasadę, ale pojedynczy pakiet dostępu może mieć wiele zasad. Należy utworzyć wiele zasad dla pakietu dostępu, jeśli chcesz zezwolić na różne zestawy użytkowników, aby otrzymać przydziały z różnymi ustawieniami żądania i zatwierdzenia. Na przykład jednej zasady nie można przypisać użytkowników wewnętrznych i zewnętrznych do tego samego pakietu dostępu. Można jednak utworzyć dwie zasady w tym samym pakiecie dostępu — jedną dla użytkowników wewnętrznych i jedną dla użytkowników zewnętrznych. Jeśli istnieje wiele zasad, które mają zastosowanie do użytkownika, zostaną one monitowane w momencie ich żądania, aby wybrać zasady, które mają być przypisane do. Na poniższym diagramie przedstawiono pakiet dostępu z dwoma zasadami.

![Wiele zasad w pakiecie dostępu](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scenariusz | Liczba zasad |
| --- | --- |
| Chcę, aby wszyscy użytkownicy w moim katalogu mieli takie same ustawienia żądania i zatwierdzania dla pakietu dostępu | Jeden |
| Chcę, aby wszyscy użytkownicy w niektórych połączonych organizacjach mogli zażądać pakietu dostępu | Jeden |
| Chcę zezwolić użytkownikom w moim katalogu, a także użytkownikom spoza mojego katalogu na żądanie pakietu dostępu | Wiele |
| Chcę określić różne ustawienia zatwierdzania dla niektórych użytkowników | Wiele |
| Chcę, aby niektórzy użytkownicy uzyskiwali dostęp do przydziałów pakietów, podczas gdy inni użytkownicy mogą rozszerzyć swój dostęp | Wiele |

Aby uzyskać informacje na temat logiki priorytetu używanej podczas stosowania wielu zasad, zobacz [Wiele zasad](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Otwieranie istniejących zasad ustawień żądania i zatwierdzania

Aby zmienić ustawienia żądania i zatwierdzania pakietu dostępu, należy otworzyć odpowiednie zasady. Wykonaj następujące kroki, aby otworzyć ustawienia żądania i zatwierdzenia pakietu dostępu.

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. Kliknij **pozycję Zasady,** a następnie kliknij zasadę, którą chcesz edytować.

    W dolnej części strony zostanie otwarte okienko Szczegóły zasad.

    ![Pakiet dostępu — okienko szczegółów zasad](./media/entitlement-management-shared/policy-details.png)

1. Kliknij **przycisk Edytuj,** aby edytować zasady.

    ![Pakiet dostępu — edytowanie zasad](./media/entitlement-management-shared/policy-edit.png)

1. Kliknij kartę **Żądania,** aby otworzyć ustawienia żądania i zatwierdzenia.

1. Wykonaj kroki opisane w jednej z poniższych sekcji żądań.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Dodawanie nowych zasad ustawień żądania i zatwierdzania

Jeśli masz zestaw użytkowników, którzy powinni mieć różne ustawienia żądania i zatwierdzania, prawdopodobnie musisz utworzyć nową zasadę. Wykonaj następujące kroki, aby rozpocząć dodawanie nowych zasad do istniejącego pakietu dostępu.

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **Zasady,** a następnie **dodaj zasadę**.

1. Wpisz nazwę i opis zasad.

    ![Tworzenie zasad z nazwą i opisem](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Kliknij **przycisk Dalej,** aby otworzyć kartę **Żądania.**

1. Wykonaj kroki opisane w jednej z poniższych sekcji żądań.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Jeśli edytujesz zasad kliknij przycisk **Aktualizuj**. Jeśli dodajesz nową zasadę, kliknij przycisk **Utwórz**.

## <a name="next-steps"></a>Następne kroki

- [Zmienianie ustawień cyklu życia pakietu dostępu](entitlement-management-access-package-lifecycle-policy.md)
- [Wyświetlanie żądań pakietu dostępu](entitlement-management-access-package-requests.md)