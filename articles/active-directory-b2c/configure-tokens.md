---
title: Konfigurowanie tokenów — usługa Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia okresu istnienia i zgodności tokenu w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189621"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurowanie tokenów w usłudze Azure Active Directory B2C

W tym artykule dowiesz się, jak skonfigurować [okres istnienia i zgodność tokenu](tokens-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika,](tutorial-create-user-flows.md) aby umożliwić użytkownikom rejestrację i zalogowanie się do aplikacji.

## <a name="configure-token-lifetime"></a>Konfigurowanie okresu istnienia tokenu

Okres istnienia tokenu można skonfigurować w dowolnym przepływie użytkownika.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **subskrypcja katalogu +** w górnym menu i wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **przepływy użytkownika (zasady)**.
5. Otwórz poprzednio utworzony przepływ użytkownika.
6. Wybierz **pozycję Właściwości**.
7. W obszarze **Okres istnienia tokenu**dostosuj następujące właściwości, aby dostosować je do potrzeb aplikacji:

    ![Ustawienia właściwości okresu istnienia tokenu w witrynie Azure portal](./media/configure-tokens/token-lifetime.png)

8. Kliknij przycisk **Zapisz**.

## <a name="configure-token-compatibility"></a>Konfigurowanie zgodności tokenów

1. Wybierz **przepływy użytkownika (zasady)**.
2. Otwórz poprzednio utworzony przepływ użytkownika.
3. Wybierz **pozycję Właściwości**.
4. W obszarze **Ustawienia zgodności tokenu**dostosuj następujące właściwości do potrzeb aplikacji:

    ![Ustawienia właściwości zgodności tokenu w witrynie Azure portal](./media/configure-tokens/token-compatibility.png)

5. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [używaniu tokenów dostępu](access-tokens.md).



