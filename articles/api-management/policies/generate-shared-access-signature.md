---
title: Przykład zasad usługi Azure API Management — generowanie sygnatury dostępu współdzielonego | Microsoft Docs
description: Przykład zasad usługi Azure API Management — pokazuje, jak wygenerować sygnaturę dostępu współdzielonego przy użyciu wyrażeń i przesłać żądanie do usługi Azure Storage za pomocą zasad ponownego zapisywania identyfikatorów URI.
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
ms.openlocfilehash: 45e29673e264f64c976cce664eaf749636400c7f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067733"
---
# <a name="generate-shared-access-signature"></a>Generowanie sygnatury dostępu współdzielonego

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak wygenerować [sygnaturę dostępu](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) współdzielonego przy użyciu wyrażeń i przesłać żądanie do usługi Azure Storage przy użyciu zasad ponownego zapisywania identyfikatorów URI. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

