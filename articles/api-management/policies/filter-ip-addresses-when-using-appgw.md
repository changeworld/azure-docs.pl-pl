---
title: Przykładowe zasady zarządzania interfejsami API — filtrowanie adresu IP podczas korzystania z bramy aplikacji
titleSuffix: Azure API Management
description: Przykład zasad zarządzania interfejsami API platformy Azure — pokazuje, jak filtrować adres IP żądania podczas korzystania z bramy aplikacji.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942479"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtrowanie adresu IP żądania podczas korzystania z bramy aplikacji

W tym artykule przedstawiono przykład zasad zarządzania interfejsami API platformy Azure, który pokazuje, jak filtrować adres IP żądania, gdy wystąpienie usługi API Management jest dostępne za pośrednictwem bramy aplikacji lub innego pośrednika. Aby ustawić lub edytować kod zasad, wykonaj czynności opisane w [załączniku Do konfigurowania lub edytowania zasad](../set-edit-policies.md). Aby zobaczyć inne przykłady, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady ograniczeń dostępu](../api-management-access-restriction-policies.md)
+ [Przykłady zasad](../policy-samples.md)
