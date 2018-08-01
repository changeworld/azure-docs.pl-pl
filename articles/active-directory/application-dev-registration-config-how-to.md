---
title: Jak wybrać uprawnienia dla danego interfejsu API | Dokumentacja firmy Microsoft
description: Jak znaleźć punkty końcowe uwierzytelniania dla aplikacji niestandardowych, tworzysz lub rejestrowanie w usłudze Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: dcb3a8f735b72b2408e28d2f0dc61b2c634baf96
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366637"
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
