---
title: Przykładowa zasada zarządzania interfejsem API platformy Azure — używanie OAuth2 do autoryzacji między bramą a zapleczem
titleSuffix: Azure API Management
description: Przykład zasad zarządzania interfejsami API platformy Azure — pokazuje, jak używać OAuth2 do autoryzacji między bramą a zapleczem. Pokazuje sposób uzyskiwania tokenu dostępu z usługi AAD i przekazywania go do zaplecza.
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
ms.openlocfilehash: 09d51759c07e7dacc25d5b5ffce9698831c37a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442366"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Użyj OAuth2 do autoryzacji między bramą a zapleczem

W tym artykule przedstawiono przykład zasad zarządzania interfejsem API platformy Azure, który pokazuje, jak używać OAuth2 do autoryzacji między bramą a zapleczem. Pokazuje sposób uzyskiwania tokenu dostępu z usługi AAD i przekazywania go do zaplecza. 

Aby ustawić lub edytować kod zasad, wykonaj czynności opisane w [załączniku Do konfigurowania lub edytowania zasad](../set-edit-policies.md). Aby zobaczyć inne przykłady, zobacz [przykłady zasad](../policy-samples.md).

Poniższy skrypt używa właściwości, które pojawiają się w {{property}}. Aby dowiedzieć się więcej o właściwościach i sposobie ich używania w zasadach zarządzania interfejsami API, zobacz [ten](../api-management-howto-properties.md) temat.
 
## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

