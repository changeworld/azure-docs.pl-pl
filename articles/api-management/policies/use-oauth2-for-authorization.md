---
title: Interfejs API zarządzania zasad przykładowy Azure — użycie protokołu OAuth2, do autoryzacji między bramą a zapleczem | Dokumentacja firmy Microsoft
description: Przykład zasad zarządzania Azure interfejsu API — pokazuje sposób użycia OAuth2 dla autoryzacji między bramą a zapleczem. Pokazuje sposób uzyskiwania tokenu dostępu z usługi AAD i przekazywania go do zaplecza.
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
ms.openlocfilehash: 519233cb9e77bf48f67d869a54af771c17c7827e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859094"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Użyj protokołu OAuth2 dla autoryzacji między bramą a zapleczem

W tym artykule przedstawiono przykładowy zasady zarządzania interfejsem API usługi Azure, który demonstruje sposób używania OAuth2 dla autoryzacji między bramą a zapleczem. Pokazuje sposób uzyskiwania tokenu dostępu z usługi AAD i przekazywania go do zaplecza. 

Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

Poniższy skrypt używa właściwości, które pojawiają się na {{właściwości}}. Aby dowiedzieć się więcej na temat właściwości i sposobu ich używania w ramach zasad usługi API Management, zobacz [to](../api-management-howto-properties.md) tematu.
 
## <a name="policy"></a>Zasady

Wklej kod do **dla ruchu przychodzącego** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

