---
title: Zbieranie wymaganych parametrów
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Parametry dla wszystkich kontenerów Cognitive Services "
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465948"
---
## <a name="gathering-required-parameters"></a>Zbieranie wymaganych parametrów

Istnieją trzy podstawowe parametry dla wszystkich kontenerów Cognitive Services, które są wymagane. Umowa licencyjna użytkownika oprogramowania (EULA) musi być obecna z wartością `accept`. Ponadto wymagany jest adres URL punktu końcowego i klucz interfejsu API.

### <a name="endpoint-uri-endpoint_uri"></a>`{ENDPOINT_URI}` identyfikatora URI punktu końcowego

Wartość identyfikatora URI **punktu końcowego** jest dostępna na stronie *Przegląd* Azure Portal odpowiedniego zasobu usługi poznawczej. Przejdź do strony *Przegląd* , umieść kursor nad punktem końcowym, a zostanie <span class="docon docon-edit-copy x-hidden-focus"></span> wyświetlona ikona `Copy to clipboard`. Kopiowanie i używanie tam, gdzie jest to zbędne.

![Zbierz identyfikator URI punktu końcowego do późniejszego użycia](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Klucze `{API_KEY}`

Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie klucze Azure Portal odpowiedniego zasobu usługi poznawczej. Przejdź do strony *klucze* i kliknij ikonę `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> .

![Pobierz jeden z dwóch kluczy do późniejszego użycia](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Te klucze subskrypcji są używane do uzyskiwania dostępu do interfejsu API usługi poznawczej. Nie udostępniaj kluczy. Przechowuj je bezpiecznie, na przykład przy użyciu Azure Key Vault. Zalecamy także regularne ponowne generowanie tych kluczy. Tylko jeden klucz jest wymagany do wywołania interfejsu API. Po ponownym wygenerowaniu pierwszego klucza można użyć drugiego klawisza w celu uzyskania ciągłego dostępu do usługi.