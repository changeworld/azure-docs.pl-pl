---
title: Zbieranie wymaganych parametrów
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Parametry dla wszystkich kontenerów Cognitive Services "
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 06f68d28d06dec7c9e738ad8cb50ca337fa840be
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102005"
---
## <a name="gathering-required-parameters"></a>Zbieranie wymaganych parametrów

Istnieją trzy podstawowe parametry dla wszystkich kontenerów Cognitive Services, które są wymagane. Umowa licencyjna użytkownika oprogramowania (EULA) musi być obecna z wartością `accept`. Ponadto wymagany jest adres URL punktu końcowego i klucz interfejsu API.

> [!NOTE]
> Jedynym wyjątkiem od tych trzech wymaganych parametrów jest to, że kontenery są uznawane za kontenery "offline". Kontenery w trybie offline nie zgłaszają użycia, nie są naliczane i obowiązują różne metody rozliczania.

### <a name="endpoint-uri-endpoint_uri"></a>Identyfikator URI punktu końcowego`{ENDPOINT_URI}`

Wartość identyfikatora URI **punktu końcowego** jest dostępna na stronie *Przegląd* Azure Portal odpowiedniego zasobu usługi poznawczej. Przejdź do strony *Przegląd* , umieść kursor nad punktem końcowym, a `Copy to clipboard` zostanie wyświetlona <span class="docon docon-edit-copy x-hidden-focus"></span> ikona. Kopiowanie i używanie tam, gdzie jest to zbędne.

![Zbierz identyfikator URI punktu końcowego do późniejszego użycia](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Ponownie`{API_KEY}`

Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie klucze Azure Portal odpowiedniego zasobu usługi poznawczej. Przejdź do strony *klucze* i kliknij `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikonę.

![Pobierz jeden z dwóch kluczy do późniejszego użycia](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Te klucze subskrypcji są używane do uzyskiwania dostępu do interfejsu API usługi poznawczej. Nie udostępniaj kluczy. Przechowuj je bezpiecznie, na przykład przy użyciu Azure Key Vault. Zalecamy także regularne ponowne generowanie tych kluczy. Tylko jeden klucz jest wymagany do wywołania interfejsu API. Po ponownym wygenerowaniu pierwszego klucza można użyć drugiego klawisza w celu uzyskania ciągłego dostępu do usługi.