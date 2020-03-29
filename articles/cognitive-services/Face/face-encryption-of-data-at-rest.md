---
title: Szyfrowanie danych w stanie spoczynku
titleSuffix: Azure Cognitive Services
description: Szyfrowanie danych w spoczynku.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372217"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Szyfrowanie danych w stanie spoczynku

Usługa Face automatycznie szyfruje dane po ich utrwaleniu w chmurze. Szyfrowanie usługi Face chroni dane i pomaga w spełnie przestrzeganie zobowiązań w zakresie bezpieczeństwa i zgodności organizacji.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta usługi Face Service](https://aka.ms/cogsvc-cmk). To zajmie około 3-5 dni roboczych, aby usłyszeć z powrotem na temat statusu wniosku. W zależności od zapotrzebowania, może być umieszczony w kolejce i zatwierdzone jako miejsce staje się dostępne. Po zatwierdzeniu do korzystania z CMK z usługą Twarz, należy utworzyć nowy zasób twarzy i wybrać E0 jako warstwę cenową. Po utworzeniu zasobu twarzy za pomocą warstwy cenowej E0 możesz użyć usługi Azure Key Vault do skonfigurowania tożsamości zarządzanej.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza zarządzanego przez klienta usługi face service](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o usłudze Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


