---
title: Konfigurowanie tokenów — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak skonfigurować okresy istnienia tokenu i ustawienia zgodności w programie Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83f8051fa31b6431d4a8515e2c0912cc1872a402
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064392"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurowanie tokenów w Azure Active Directory B2C

W tym artykule dowiesz się, jak skonfigurować [okres istnienia i zgodność tokenu](active-directory-b2c-reference-tokens.md) w Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika](tutorial-create-user-flows.md) , aby umożliwić użytkownikom rejestrowanie się w aplikacji i logowanie się do niej.

## <a name="configure-token-lifetime"></a>Skonfiguruj okres istnienia tokenu

Okres istnienia tokenu można skonfigurować w dowolnym przepływie użytkownika.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika (zasady)** .
5. Otwórz wcześniej utworzony przepływ użytkownika.
6. Wybierz **właściwości**.
7. W obszarze **okres istnienia tokenu**Dostosuj następujące właściwości, aby dopasować je do potrzeb aplikacji:

    ![Ustawienia właściwości okresu istnienia tokenu w Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Kliknij polecenie **Zapisz**.

## <a name="configure-token-compatibility"></a>Konfigurowanie zgodności tokenu

1. Wybierz pozycję **przepływy użytkownika (zasady)** .
2. Otwórz wcześniej utworzony przepływ użytkownika.
3. Wybierz **właściwości**.
4. W obszarze **Ustawienia zgodności tokenu**Dostosuj następujące właściwości, aby dopasować je do potrzeb aplikacji:

    ![Ustawienia właściwości zgodności tokenu w Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Kliknij polecenie **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [używania tokenów dostępu](active-directory-b2c-access-tokens.md).



