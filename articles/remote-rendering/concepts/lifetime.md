---
title: Okres istnienia obiektu i zasobu
description: W tym artykule wyjaśniono zarządzanie okresem istnienia dla różnych typów
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681872"
---
# <a name="object-and-resource-lifetime"></a>Okres istnienia obiektu i zasobu

Renderowanie zdalne platformy Azure rozróżnia dwa typy: **obiekty** i **zasoby**.

## <a name="object-lifetime"></a>Okres istnienia obiektu

*Obiekty* są uważane za rzeczy, które użytkownik może tworzyć, modyfikować i niszczyć według własnego uznania. Obiekty mogą być powielane swobodnie i każde wystąpienie może mutować w czasie. W związku z tym [elementy](entities.md) i [składniki](components.md) są obiektami.

Okres istnienia obiektów jest w pełni pod kontrolą użytkownika. Nie jest to jednak związane z okresem istnienia reprezentacji po stronie klienta. Klasy `Entity` takie `Component` jak `Destroy` i mają funkcję, która musi być wywoływana, aby zdelocyfikować obiekt na zdalnym hoście renderowania. Ponadto `Entity.Destroy()` spowoduje zniszczenie jednostki, jej elementów podrzędnych i wszystkich składników w tej hierarchii.

## <a name="resource-lifetime"></a>Okres istnienia zasobu

*Zasoby* to rzeczy, których okres istnienia jest całkowicie zarządzany przez zdalny host renderowania. Zasoby są odwołania liczone wewnętrznie. Dostają one deallocated, gdy nikt nie odwołuje się do nich już.

Większość zasobów można utworzyć tylko pośrednio, zazwyczaj ładując je z pliku. Gdy ten sam plik jest ładowany wiele razy, azure remote rendering zwróci to samo odwołanie i nie ładuje danych ponownie.

Wiele zasobów jest niezmiennych, na przykład [siatki](meshes.md) i [tekstury](textures.md). Niektóre zasoby są modyfikowalne, choć, na przykład [materiały](materials.md). Ponieważ zasoby są często współużytkowane, modyfikowanie zasobu może mieć wpływ na wiele obiektów. Na przykład zmiana koloru materiału spowoduje zmianę koloru wszystkich obiektów korzystających z siatek, które z kolei odwołują się do tego materiału.

### <a name="built-in-resources"></a>Zasoby wbudowane

Renderowanie zdalne platformy Azure zawiera pewne wbudowane zasoby, które można `builtin://` załadować, `AzureSession.Actions.LoadXYZAsync()`dołączając ich odpowiedni identyfikator podczas wywołania . Dostępne wbudowane zasoby są wymienione w dokumentacji dla każdej odpowiedniej funkcji. Na przykład [rozdział nieba](../overview/features/sky.md) zawiera listę wbudowanych tekstur nieba.

## <a name="general-lifetime"></a>Ogólny okres istnienia

Okres istnienia wszystkich obiektów i zasobów jest powiązany z połączeniem. Po odłączeniu wszystko jest odrzucane. Podczas ponownego łączenia się z tą samą sesją wykres sceny będzie pusty, a wszystkie zasoby zostaną wyczyszczone.

W praktyce ładowanie tego samego zasobu do sesji, po rozłączeniu, jest zwykle szybsze niż za pierwszym razem. Dzieje się tak, ponieważ większość zasobów musi zostać pobrana z usługi Azure Storage po raz pierwszy, co nie jest konieczne po raz drugi, oszczędzając znaczną ilość czasu.

## <a name="next-steps"></a>Następne kroki

* [Jednostki](entities.md)
* [Składniki](components.md)
* [Modele](models.md)
