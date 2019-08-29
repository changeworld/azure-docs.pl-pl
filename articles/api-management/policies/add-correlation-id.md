---
title: Przykład zasad usługi Azure API Management — dodawanie nagłówka zawierającego identyfikator korelacji | Microsoft Docs
description: Przykład zasad usługi Azure API Management — pokazuje, jak dodać nagłówek zawierający identyfikator korelacji do żądania przychodzącego.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 9f091345a4eaf174c47959cad3cb4525fd926689
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074209"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Dodawanie nagłówka zawierającego identyfikator korelacji

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak dodać nagłówek zawierający identyfikator korelacji do żądania przychodzącego. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

