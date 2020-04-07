---
title: Zapytania przestrzenne
description: Jak wykonywać zapytania przestrzenne w scenie
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680533"
---
# <a name="spatial-queries"></a>Zapytania przestrzenne

Zapytania przestrzenne to operacje, za pomocą których można zapytać usługę zdalnego renderowania, które obiekty znajdują się w danym obszarze. Zapytania przestrzenne są często używane do implementowania interakcji, takich jak ustalenie, który obiekt jest skierowany na użytkownika.

Wszystkie zapytania przestrzenne są oceniane na serwerze. W związku z tym są one operacje asynchroniczne i wyniki dojdą z opóźnieniem, które zależy od opóźnienia sieci. Ponieważ każde zapytanie przestrzenne generuje ruch sieciowy, należy uważać, aby nie robić zbyt wiele naraz.

## <a name="collision-meshes"></a>Siatki kolizji

Zapytania przestrzenne są zasilane przez silnik [Havok Physics](https://www.havok.com/products/havok-physics) i wymagają dedykowanej siatki kolizji. Domyślnie [konwersja modelu](../../how-tos/conversion/model-conversion.md) generuje siatki kolizji. Jeśli nie potrzebujesz zapytań przestrzennych w modelu złożonym, rozważ wyłączenie generowania siatki kolizji w [opcjach konwersji,](../../how-tos/conversion/configure-model-conversion.md)ponieważ ma on wpływ na wiele sposobów:

* [Konwersja modelu](../../how-tos/conversion/model-conversion.md) potrwa znacznie dłużej.
* Przekonwertowane rozmiary plików modelu są zauważalnie większe, co wpływa na szybkość pobierania.
* Czas ładowania środowiska uruchomieniowego jest dłuższy.
* Zużycie pamięci procesora CPU środowiska wykonawczego jest wyższe.
* Istnieje niewielkie obciążenie wydajności środowiska uruchomieniowego dla każdego wystąpienia modelu.

## <a name="ray-casts"></a>Odlewy promieniowe

*Rzutowanie promieni* jest kwerendą przestrzenną, w której środowisko uruchomieniowe sprawdza, które obiekty są przecinane przez promień, zaczynając od danej pozycji i wskazując w określonym kierunku. Jako optymalizacja podaje się również maksymalną odległość promienia, aby nie wyszukiwać obiektów, które są zbyt daleko.

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

Istnieją trzy tryby kolekcji trafień:

* **Najbliżej:** W tym trybie zostanie zgłoszony tylko najbliższy trafienie.
* **Dowolne:** Preferuj ten tryb, gdy chcesz tylko wiedzieć, *czy* promień uderzy w cokolwiek, ale nie obchodzi cię, co zostało dokładnie uderzone. Ta kwerenda może być znacznie tańsze do oceny, ale także ma tylko kilka aplikacji.
* **Wszystkie:** W tym trybie wszystkie trafienia wzdłuż promienia są zgłaszane, sortowane według odległości. Nie używaj tego trybu, chyba że naprawdę potrzebujesz więcej niż pierwsze trafienie. Ogranicz liczbę zgłoszonych `MaxHits` trafień za pomocą opcji.

Aby wykluczyć obiekty selektywnie z rozważanych dla rzutowania promieni, można użyć [składnika HierarchicalStateOverrideComponent.](override-hierarchical-state.md)

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Wynik trafienia

Wynikiem kwerendy rzutowej promieni jest tablica trafień. Tablica jest pusta, jeśli żaden obiekt nie został trafiony.

Trafienie ma następujące właściwości:

* **HitEntity:** Który [podmiot](../../concepts/entities.md) został trafiony.
* **Podpartid:** Który *submesh* został trafiony w [MeshComponent](../../concepts/meshes.md). Może służyć do `MeshComponent.UsedMaterials` indeksu i wyszukiwania [materiału](../../concepts/materials.md) w tym momencie.
* **Pozycja trafienia:** Pozycja przestrzeni świata, w której promień przecinał obiekt.
* **HitNormal:** Powierzchnia powierzchni świata normalna siatka w pozycji przecięcia.
* **DistanceToHit:** Odległość od pozycji wyjściowej promienia do trafienia.

## <a name="next-steps"></a>Następne kroki

* [Granice obiektu](../../concepts/object-bounds.md)
* [Nadrzędne stany hierarchiczne](override-hierarchical-state.md)
