---
title: Azure próbce interfejsu API zarządzania zasad - OAuth2 używany do autoryzacji między bramą a wewnętrznej bazie danych | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - przedstawiono sposób użycia protokołu OAuth2 autoryzacji między bramą a wewnętrznej bazie danych. Widoczny jest sposób uzyskać token dostępu z usługi AAD i przesyła je do wewnętrznej bazy danych.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: fc896656a4725475fc78cadb5bab54a27cfc02a2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Użyj protokołu OAuth2 autoryzacji między bramą a wewnętrznej bazie danych

W tym artykule przedstawiono przykład zasad interfejsu API usługi Azure zarządzania, który demonstruje sposób użycia protokołu OAuth2 autoryzacji między bramą a wewnętrznej bazie danych. Widoczny jest sposób uzyskać token dostępu z usługi AAD i przesyła je do wewnętrznej bazy danych. 

Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

Poniższy skrypt używa właściwości, które są widoczne w {{właściwości}}. Aby dowiedzieć się więcej o właściwościach i sposobu ich używania w ramach zasad usługi API Management, zobacz [to](../api-management-howto-properties.md) tematu.
 
## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

