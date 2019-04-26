---
title: Konfigurowanie tokenów — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia tokenu okres istnienia i zgodności w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2455b8c12ed042af3a06a158a4e5c60a0aee748b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60384038"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurowanie tokenów w usłudze Azure Active Directory B2C

W tym artykule dowiesz się, jak skonfigurować [okres istnienia i zgodności tokenu](active-directory-b2c-reference-tokens.md) w usłudze Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika](tutorial-create-user-flows.md) aby użytkownicy mogli zarejestrować się i zaloguj się do aplikacji.

## <a name="configure-token-lifetime"></a>Skonfiguruj okres istnienia tokenu

Na dowolny przepływ użytkownika można skonfigurować okres istnienia tokenu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera dzierżawy usługi Azure AD B2C.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **przepływy użytkownika (zasady)**.
5. Otwórz przepływ użytkownika, która została wcześniej utworzona. 
6. Wybierz **właściwości**.
7. W obszarze **okres istnienia tokenu**, dostosować do potrzeb swojej aplikacji następujące właściwości:

    ![Skonfiguruj okres istnienia tokenu](./media/configure-tokens/token-lifetime.png)

8. Kliknij pozycję **Zapisz**.

## <a name="configure-token-compatibility"></a>Konfigurowanie zgodności tokenów

1. Wybierz **przepływy użytkownika (zasady)**.
2. Otwórz przepływ użytkownika, która została wcześniej utworzona. 
3. Wybierz **właściwości**.
4. W obszarze **ustawień zgodności tokenów**, dostosować do potrzeb swojej aplikacji następujące właściwości:

    ![Konfigurowanie zgodności tokenów](./media/configure-tokens/token-compatibility.png)

5. Kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat [używanie tokenów dostępu](active-directory-b2c-access-tokens.md).



