---
title: Przykład zasad usługi Azure API Management — wysyłanie informacji kontekstu żądania do usługi wewnętrznej bazy danych | Microsoft Docs
description: Przykład zasad usługi Azure API Management — pokazuje, jak wysyłać informacje kontekstu żądania do usługi zaplecza.
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
ms.openlocfilehash: cc770ff700155f8ab32bbbd6737c9dad7bc6e664
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067524"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Wyślij informacje kontekstu żądania do usługi wewnętrznej bazy danych

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak wysyłać informacje kontekstu żądania do usługi zaplecza. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

