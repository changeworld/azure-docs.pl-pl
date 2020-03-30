---
title: Przykładowe zasady zarządzania interfejsami API — generowanie podpisu dostępu współdzielonego
titleSuffix: Azure API Management
description: Przykład zasad zarządzania interfejsami API platformy Azure — pokazuje, jak wygenerować podpis dostępu współdzielonego przy użyciu wyrażeń i przesłać żądanie do magazynu platformy Azure za pomocą zasad ponownego zapisywania uri.Azure Management policy — pokazuje, jak wygenerować podpis dostępu współdzielonego przy użyciu wyrażeń i przesłać żądanie do magazynu platformy Azure z zasadami ponownego zapisu uri.
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
ms.openlocfilehash: 0f003bc268af6b7f8bd6b046ae84734dbefeac28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442458"
---
# <a name="generate-shared-access-signature"></a>Generowanie podpisu dostępu współdzielonego

W tym artykule przedstawiono przykład zasad zarządzania interfejsami API platformy Azure, który pokazuje, jak wygenerować [podpis dostępu współdzielonego](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) przy użyciu wyrażeń i przesłać żądanie do magazynu platformy Azure z zasadami ponownego zapisywania uri. Aby ustawić lub edytować kod zasad, wykonaj czynności opisane w [załączniku Do konfigurowania lub edytowania zasad](../set-edit-policies.md). Aby zobaczyć inne przykłady, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

