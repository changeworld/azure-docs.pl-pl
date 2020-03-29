---
title: Personalizator usługi szyfrowania danych w spoczynku
titleSuffix: Azure Cognitive Services
description: Personalizator usługi szyfrowania danych w spoczynku.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071090"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Personalizator usługi szyfrowania danych w spoczynku

Usługa Personalizer automatycznie szyfruje dane po utrwaleniu ich w chmurze. Szyfrowanie usługi Personalizer chroni twoje dane i pomaga w spełnienie zobowiązań w zakresie bezpieczeństwa i zgodności organizacji.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta personalizatora.](https://aka.ms/cogsvc-cmk) To zajmie około 3-5 dni roboczych, aby usłyszeć z powrotem na temat statusu wniosku. W zależności od zapotrzebowania, może być umieszczony w kolejce i zatwierdzone jako miejsce staje się dostępne. Po zatwierdzeniu do korzystania z CMK z usługą Personalizer należy utworzyć nowy zasób Personalizer i wybrać E0 jako warstwę cenową. Po utworzeniu zasobu personalizatora za pomocą warstwy cenowej E0 można użyć usługi Azure Key Vault do skonfigurowania tożsamości zarządzanej.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza zarządzanego przez klienta personalizatora](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o usłudze Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
