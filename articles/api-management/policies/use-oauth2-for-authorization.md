---
title: Azure próbce interfejsu API zarządzania zasad - OAuth2 używany do autoryzacji między bramą a wewnętrznej bazie danych | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - przedstawiono sposób użycia protokołu OAuth2 autoryzacji między bramą a wewnętrznej bazie danych. Pokazuje sposób uzyskiwania tokenu dostępu z usługi AAD i przekazywania go do zaplecza.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: d064e918d514b9be1b9fa2dbf30c10edf5167908
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287824"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Użyj protokołu OAuth2 autoryzacji między bramą a wewnętrznej bazie danych

W tym artykule przedstawiono przykład zasad interfejsu API usługi Azure zarządzania, który demonstruje sposób użycia protokołu OAuth2 autoryzacji między bramą a wewnętrznej bazie danych. Pokazuje sposób uzyskiwania tokenu dostępu z usługi AAD i przekazywania go do zaplecza. 

Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

Poniższy skrypt używa właściwości, które są widoczne w {{właściwości}}. Aby dowiedzieć się więcej o właściwościach i sposobu ich używania w ramach zasad usługi API Management, zobacz [to](../api-management-howto-properties.md) tematu.
 
## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

