---
title: Szyfrowanie danych w trybie spoczynku moderatora zawartości
titleSuffix: Azure Cognitive Services
description: Szyfrowanie danych w stanie spoczynku moderatora zawartości.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372162"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Szyfrowanie danych w trybie spoczynku moderatora zawartości

Content Moderator automatycznie szyfruje dane, gdy są one utrwalane w chmurze, pomagając osiągnąć cele w zakresie zabezpieczeń i zgodności z przepisami.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta moderatora zawartości](https://aka.ms/cogsvc-cmk). To zajmie około 3-5 dni roboczych, aby usłyszeć z powrotem na temat statusu wniosku. W zależności od zapotrzebowania, może być umieszczony w kolejce i zatwierdzone jako miejsce staje się dostępne. Po zatwierdzeniu do korzystania z CMK w usłudze Content Moderator należy utworzyć nowy zasób moderatora zawartości i wybrać E0 jako warstwę cenową. Po utworzeniu zasobu moderatora zawartości za pomocą warstwy cenowej E0 można użyć usługi Azure Key Vault do skonfigurowania tożsamości zarządzanej.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Włączanie szyfrowania danych dla zespołu moderatorów zawartości

Aby włączyć szyfrowanie danych dla zespołu recenzentów moderatora zawartości, zobacz [Przewodnik Szybki start: Wypróbuj moderatora zawartości w sieci Web](quick-start.md#create-a-review-team).  

> [!NOTE]
> Musisz podać identyfikator _zasobu_ w warstwie cenowej Content Moderator E0.


## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza zarządzanego przez moderatora zawartości](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o usłudze Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
