---
title: Usługa Azure Security Center — pytania dotyczące uprawnień
description: To często zadawane pytania dotyczy pytań dotyczących uprawnień w usłudze Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać ich i reagować na nie.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599394"
---
# <a name="permissions"></a>Uprawnienia

## <a name="how-do-permissions-work-in-azure-security-center"></a>Jak działają uprawnienia w usłudze Azure Security Center?

Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md), które można przypisać do użytkowników, grup i usług Azure.

Usługa Security Center ocenia konfigurację zasobów w celu zidentyfikowania problemów z zabezpieczeniami i luk w zabezpieczeniach. W U centrum zabezpieczeń informacje związane z zasobem są wyświetlane tylko wtedy, gdy przypisano rolę właściciela, współautora lub czytnika dla subskrypcji lub grupy zasobów, do której należy zasób.

Aby dowiedzieć się więcej o rolach i dozwolonych akcjach w usłudze Security Center, zobacz [Uprawnienia w usłudze Azure Security Center.](security-center-permissions.md)



## <a name="who-can-modify-a-security-policy"></a>Kto może modyfikować zasady zabezpieczeń?

Aby zmodyfikować zasady zabezpieczeń, użytkownik musi być administratorem zabezpieczeń lub właścicielem lub współautorem tej subskrypcji.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md).