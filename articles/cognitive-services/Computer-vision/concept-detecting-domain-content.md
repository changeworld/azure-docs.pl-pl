---
title: Wykrywanie specyficznego dla domeny zawartości — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak określić domenę kategoryzacji obrazu do zwrócenia bardziej szczegółowe informacje o obrazie.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 76484a2340e527dc016f321dbafa29adb7c358b5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157538"
---
# <a name="detecting-domain-specific-content"></a>Wykrywanie zawartości specyficznej dla domeny

Ponadto do znakowania i najwyższego poziomu o kategoryzacji, przetwarzanie obrazów obsługuje również informacje specjalistyczne (lub specyficznego dla domeny). Informacje specjalistyczne można wdrażać jako metodę autonomiczną lub na wysokim poziomie kategoryzacji. Funkcjonują one jako sposób dalszego uściślenia taksonomii obejmującej 86 kategorii przez dodanie modeli specyficznych dla domeny.

Istnieją dwie opcje używania modeli specyficznych dla domeny:

* Analizy o określonym zakresie  
  Analizuj wybrany model przez wywołanie metody POST protokołu HTTP. Jeśli znasz model, który chcesz użyć, należy określić nazwę modelu. Możesz pobrać wyłącznie informacje istotne dla modelu. Możesz na przykład użyć tej opcji tylko do wyszukiwania rozpoznawania celebrytów. Odpowiedź zawiera listę potencjalnych pasujących celebrytów wraz ze współczynnikami ufności.
* Rozszerzone analizy  
  Analiza dostarczająca dodatkowe szczegóły związane z kategoriami z taksonomii zawierającej 86 kategorii. Ta opcja jest dostępna w aplikacjach, w których użytkownicy chcą uzyskać analizę ogólną obrazu oprócz szczegółów z co najmniej jednego modelu specyficznego dla domeny. Po wywołaniu tej metody klasyfikator taksonomii zawierający 86 kategorii zostanie wywołany jako pierwszy. Jeśli dowolnej kategorii pasuje do znanego lub zgodnych modeli, drugi — dostęp próbny wywołania klasyfikatora jest zgodna. Na przykład jeśli `details` parametru wywołanie metody POST protokołu HTTP albo jest ustawiona na "wszystkie" lub "osobistości" obejmuje, metody wywołania klasyfikatora osobistości, po klasyfikatora 86 kategorii jest wywoływana. Jeśli obraz zostanie sklasyfikowany jako `people_` lub podkategorii w kategorii, a następnie klasyfikatora osobistości jest wywoływana.

## <a name="listing-domain-specific-models"></a>Wyświetlanie listy modeli specyficznych dla domeny

Możesz wyświetlić listę modeli specyficznych dla domeny, obsługiwanych przez przetwarzania obrazów. Obecnie Usługa przetwarzania obrazów obsługuje następujące modele specyficzne dla domeny do wykrywania specyficznego dla domeny zawartości:

| Name (Nazwa) | Opis |
|------|-------------|
| osobistości | Rozpoznawanie osobistości, obsługiwane w przypadku obrazów sklasyfikować w obszarze `people_` kategorii |
| Charakterystycznych elementów krajobrazu | Rozpoznawania charakterystycznych elementów krajobrazu, obsługiwane w przypadku obrazów sklasyfikować w obszarze `outdoor_` lub `building_` kategorii |

### <a name="domain-model-list-example"></a>Przykład listy modelu domeny

Następującą odpowiedź JSON zawiera listę modeli specyficznych dla domeny, obsługiwanych przez przetwarzania obrazów.

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [kategoryzowanie obrazów](concept-categorizing-images.md).
