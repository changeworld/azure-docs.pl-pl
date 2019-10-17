---
title: Wyświetlanie i zarządzanie żądaniami dotyczącymi pakietu dostępu w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak wyświetlać, przetwarzać i anulować żądania dotyczące pakietu dostępu w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
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
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430283"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Wyświetlanie i zarządzanie żądaniami dotyczącymi pakietu dostępu w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W usłudze Azure AD uprawnienia do zarządzania można zobaczyć, kto zażądał pakietów dostępu, ich zasad i stanu. W tym artykule opisano sposób wyświetlania, ponownego przetwarzania i anulowania żądań dla pakietów dostępu.

## <a name="view-requests"></a>Wyświetl żądania

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **żądania**.

1. Kliknij konkretne żądanie, aby wyświetlić dodatkowe szczegóły.

    ![Lista żądań dla pakietu dostępu](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>Wyświetl błędy dostarczania żądania

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **żądania**.

1. Wybierz żądanie, które chcesz wyświetlić.

    Jeśli żądanie zawiera błędy dostarczania, stan żądania zostanie **wystawiony** lub **częściowo dostarczony**.

    Jeśli występują błędy dostarczania, w okienku szczegółów żądania zostanie wyliczona liczba błędów dostarczania.

1. Kliknij liczbę, aby wyświetlić wszystkie błędy dostarczania żądania.

## <a name="reprocess-a-request"></a>Przetwórz ponownie żądanie

Jeśli w żądaniu wystąpi błąd, można ponownie przetworzyć żądanie, aby ponowić próbę. Można ponownie przetwarzać żądanie, które ma stan **dostawy zakończony niepowodzeniem** lub **częściowo dostarczone** i datę ukończenia mniejszą niż tydzień.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **żądania**.

1. Kliknij żądanie, które chcesz ponownie przetworzyć.

1. W okienku Szczegóły żądania kliknij pozycję **Przetwórz ponownie żądanie**.

    ![Przetwórz ponownie żądanie zakończone niepowodzeniem](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Można anulować tylko oczekujące żądanie, które nie zostało jeszcze dostarczone lub którego dostarczenie nie powiodło się.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **żądania**.

1. Kliknij żądanie, które chcesz anulować.

1. W okienku Szczegóły żądania kliknij przycisk **Anuluj żądanie**.

## <a name="next-steps"></a>Następne kroki

- [Ustawienia żądania zmiany i zatwierdzania dla pakietu dostępu](entitlement-management-access-package-request-policy.md)
- [Wyświetlanie, Dodawanie i usuwanie przypisań dla pakietu dostępu](entitlement-management-access-package-assignments.md)