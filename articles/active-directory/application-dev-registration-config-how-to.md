---
title: Jak wybrać uprawnienia dla danego interfejsu API | Dokumentacja firmy Microsoft
description: Jak znaleźć punkty końcowe uwierzytelniania dla aplikacji niestandardowej podczas tworzenia lub rejestrowania za pomocą usługi Azure AD.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 6c8b8bb81e62747b7ab5eaca94d2820d2e0661d2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26612932"
---
# <a name="how-to-select-permissions-for-a-given-api"></a>Jak wybrać uprawnienia dla danego interfejsu API

Punkty końcowe uwierzytelniania można znaleźć aplikacji w [portalu Azure](https://portal.azure.com).

-   Przejdź do witryny [Azure Portal](https://portal.azure.com).

-   W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory**.

-   Kliknij przycisk **rejestracji aplikacji** i wybierz polecenie **punkty końcowe**.

-   To otwarcie **punkty końcowe** strony, tak aby wyświetlić listę wszystkich punktów końcowych uwierzytelniania dla dzierżawy.

-   Użyj punktu końcowego, które są specyficzne dla protokołu uwierzytelniania, którego używasz, w połączeniu z Identyfikatorem aplikacji sformułować uwierzytelnianie żądań specyficzne dla aplikacji.

## <a name="next-steps"></a>Następne kroki
[Przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
