---
title: Azure Security Center często zadawane pytania — pytania dotyczące uprawnień
description: Ten często zadawane pytania zawierają odpowiedzi na pytania dotyczące uprawnień w Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie.
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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599394"
---
# <a name="permissions"></a>Uprawnienia

## <a name="how-do-permissions-work-in-azure-security-center"></a>Jak uprawnienia działają w Azure Security Center?

Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md), które można przypisać do użytkowników, grup i usług Azure.

Usługa Security Center ocenia konfigurację zasobów, aby zidentyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. W usłudze Security Center widoczne są tylko informacje związane z zasobem, gdy masz przypisaną rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

Zobacz [uprawnienia w Azure Security Center](security-center-permissions.md) , aby dowiedzieć się więcej o rolach i dozwolonych akcjach w programie Security Center.



## <a name="who-can-modify-a-security-policy"></a>Kto może modyfikować zasady zabezpieczeń?

Aby zmodyfikować zasady zabezpieczeń, musi być kontem administratora zabezpieczeń lub właścicielem lub współautorem subskrypcji.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md).