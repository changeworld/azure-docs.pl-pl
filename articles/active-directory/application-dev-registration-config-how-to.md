---
title: Jak wybrać uprawnienia dla danego interfejsu API | Dokumentacja firmy Microsoft
description: Jak znaleźć punkty końcowe uwierzytelniania dla aplikacji niestandardowej podczas tworzenia lub rejestrowania za pomocą usługi Azure AD.
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 203ecc1dd83bebffadf0e85a08cb3ee383b0d412
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332011"
---
# <a name="how-to-select-permissions-for-a-given-api"></a>Jak wybrać uprawnienia dla danego interfejsu API

Punkty końcowe uwierzytelniania można znaleźć aplikacji w [portalu Azure](https://portal.azure.com).

-   Przejdź do witryny [Azure Portal](https://portal.azure.com).

-   W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory**.

-   Kliknij przycisk **rejestracji aplikacji** i wybierz polecenie **punkty końcowe**.

-   To otwarcie **punkty końcowe** strony, tak aby wyświetlić listę wszystkich punktów końcowych uwierzytelniania dla dzierżawy.

-   Użyj punktu końcowego, które są specyficzne dla protokołu uwierzytelniania, którego używasz, w połączeniu z Identyfikatorem aplikacji sformułować uwierzytelnianie żądań specyficzne dla aplikacji.

## <a name="next-steps"></a>Kolejne kroki
[Przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
