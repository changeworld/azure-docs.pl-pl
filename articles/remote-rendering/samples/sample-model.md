---
title: Przykładowe modele
description: Wyświetla listę źródeł dla przykładowych modeli.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679485"
---
# <a name="sample-models"></a>Przykładowe modele

W tym artykule wymieniono niektóre zasoby dotyczące przykładowych danych, które mogą służyć do testowania usługi zdalnego renderowania platformy Azure.

## <a name="built-in-sample-model"></a>Wbudowany model próbki

Zapewniamy wbudowany przykładowy model, który zawsze można załadować przy użyciu adresu URL **builtin://Engine**

![Przykładowy model](./media/sample-model.png "Przykładowy model")

Statystyki modelu:

| Nazwa | Wartość |
|-----------|:-----------|
| [Wymagany rozmiar maszyny Wirtualnej](../how-tos/session-rest-api.md#create-a-session) | Standardowych |
| Liczba trójkątów | 18,7 mln |
| Liczba ruchomych części | 2073 |
| Liczba materiałów | 94 |

## <a name="third-party-data"></a>Dane innych firm

Grupa Khronos przechowuje zestaw przykładowych modeli glTF do testowania. ARR obsługuje format glTF zarówno w tekście (*.gltf*) jak i w formacie binarnym (*.glb*). Zalecamy wykorzystanie modeli PBR w celu uzyskania najlepszych efektów wizualnych:

* [Przykładowe modele gltf](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Następne kroki

* [Szybki start: renderowanie modelu za pomocą unity](../quickstarts/render-model.md)
* [Szybki start: konwertowanie modelu do renderowania](../quickstarts/convert-model.md)
