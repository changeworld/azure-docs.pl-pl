---
title: Przykład zasad zarządzania interfejsami API platformy Azure — dodawanie nagłówka przesyłanego dalej | Dokumenty firmy Microsoft
description: Przykład zasad zarządzania interfejsami API platformy Azure — pokazuje, jak dodać nagłówek przesyłany dalej w żądaniu przychodzącym, aby umożliwić interfejsowi API wewnętrznej bazy danych konstruowanie prawidłowych adresów URL.
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
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067500"
---
# <a name="add-a-forwarded-header"></a>Dodawanie nagłówka przesyłanego dalej

W tym artykule przedstawiono przykład zasad zarządzania interfejsami API platformy Azure, który pokazuje, jak dodać nagłówek przesyłania dalej w żądaniu przychodzącym, aby umożliwić interfejsowi API wewnętrznej bazy danych do konstruowania prawidłowych adresów URL. Aby ustawić lub edytować kod zasad, wykonaj czynności opisane w [załączniku Do konfigurowania lub edytowania zasad](../set-edit-policies.md). Aby zobaczyć inne przykłady, zobacz [przykłady zasad](../policy-samples.md).

## <a name="code"></a>Code

Wklej kod do bloku **przychodzącego.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)
