---
title: Przykład zasad usługi Azure API Management — Autoryzuj żądanie przy użyciu zewnętrznego upoważnienia | Microsoft Docs
description: Przykład zasad usługi Azure API Management — pokazuje, jak autoryzować żądania przy użyciu zewnętrznego autoryzowanego upoważnienia do hermetyzacji niestandardowej lub starszej logiki uwierzytelniania/autoryzacji.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 92836e0bfe43a41ad6547c68bc0b9a326528862c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074168"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autoryzuj żądania przy użyciu zewnętrznego upoważnienia

W tym artykule przedstawiono przykład zasad zarządzania interfejsem API platformy Azure, który demonstruje sposób zabezpieczania dostępu do interfejsu API przy użyciu zewnętrznego, autoryzowanego do hermetyzacji logiki uwierzytelniania niestandardowego/autoryzacji. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady ograniczeń dostępu](../api-management-access-restriction-policies.md)
+ [Przykłady zasad](../policy-samples.md)
