---
title: Zbieranie wymaganych parametrów
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Parametry dla wszystkich kontenerów usług Cognitive Services
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465948"
---
## <a name="gathering-required-parameters"></a>Zbieranie wymaganych parametrów

Istnieją trzy podstawowe parametry dla wszystkich kontenerów usług Cognitive Services, które są wymagane. Umowa licencyjna użytkownika końcowego `accept`(EULA) musi mieć wartość . Ponadto potrzebny jest adres URL punktu końcowego i klucz interfejsu API.

### <a name="endpoint-uri-endpoint_uri"></a>Identyfikator URI punktu końcowego`{ENDPOINT_URI}`

Wartość identyfikatora URI **punktu końcowego** jest dostępna na stronie *Przegląd* portalu Azure odpowiedniego zasobu usługi Cognitive Service. Przejdź do strony *Przegląd,* umieść wskaźnik myszy `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> na punkcie końcowym i pojawi się ikona. W razie potrzeby skopiuj i używaj.

![Zbierz punkt końcowy uri do późniejszego użycia](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Klucze`{API_KEY}`

Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie Klucze portalu Azure odpowiedniego zasobu usługi Cognitive Service. Przejdź do strony *Klawisze* i `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> kliknij ikonę.

![Pobierz jeden z dwóch kluczy do późniejszego użycia](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Te klucze subskrypcji są używane do uzyskiwania dostępu do interfejsu API usługi Cognitive Service. Nie udostępniaj kluczy. Przechowuj je bezpiecznie, na przykład przy użyciu usługi Azure Key Vault. Zalecamy również regularne regenerowanie tych kluczy. Tylko jeden klucz jest niezbędny do wywołania interfejsu API. Podczas ponownego generowania pierwszego klucza, można użyć drugiego klucza do dalszego dostępu do usługi.