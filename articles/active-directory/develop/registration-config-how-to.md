---
title: Jak wybrać uprawnienia dla danego interfejsu API | Dokumentacja firmy Microsoft
description: Jak znaleźć punkty końcowe uwierzytelniania dla aplikacji niestandardowych, tworzysz lub rejestrowanie w usłudze Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: f6c2540d8f0bb49491a428b085d20067a36d970a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44724184"
---
# <a name="how-to-select-permissions-for-a-given-api"></a>Jak wybrać uprawnienia dla danego interfejsu API

Punkty końcowe uwierzytelniania można znaleźć aplikacji na platformie [witryny Azure portal](https://portal.azure.com).

-   Przejdź do witryny [Azure Portal](https://portal.azure.com).

-   W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory**.

-   Kliknij przycisk **rejestracje aplikacji** i wybierz polecenie **punktów końcowych**.

-   Otwórz ten **punktów końcowych** stronie listy wszystkie punkty końcowe uwierzytelniania dla dzierżawy.

-   Użyj punktu końcowego, które są specyficzne dla protokołu uwierzytelniania, którego używasz, w połączeniu z Identyfikatorem aplikacji w celu uwierzytelniania jednostki żądania dotyczące danej aplikacji.

## <a name="next-steps"></a>Kolejne kroki
[Przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
