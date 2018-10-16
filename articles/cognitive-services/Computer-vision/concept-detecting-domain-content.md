---
title: Wykrywanie specyficznego dla domeny zawartości — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z opisem obrazów przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: a9c71fa7e5d86cfeb4fe6fab44bbce241546ccb8
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342563"
---
# <a name="detecting-domain-specific-content"></a>Wykrywanie zawartości specyficznej dla domeny

Ponadto do znakowania i najwyższego poziomu o kategoryzacji, przetwarzanie obrazów obsługuje również informacje specjalistyczne (lub specyficznego dla domeny). Informacje można zaimplementować jako metoda autonomiczna lub wysokiego poziomu o kategoryzacji. Działa ona jako sposób, aby dalej zawęzić Taksonomia kategorii 86 przez dodanie modeli specyficznych dla domeny.

Istnieją dwie opcje używania modeli specyficznych dla domeny:

* Analizy o określonym zakresie  
  Analizuj wybrany model przez wywołanie metody POST protokołu HTTP. Jeśli znasz model, który chcesz użyć, należy określić nazwę modelu. Możesz pobrać wyłącznie informacje istotne dla modelu. Na przykład można używasz tej opcji do rozpoznawania osobistości są wyszukiwane tylko. Odpowiedź zawiera listę potencjalnych dopasowania osobistości, wraz z ich oceny zaufania.
* Rozszerzone analizy  
  Analizuj, aby zapewnić dodatkowe szczegóły powiązane z kategorii Taksonomia kategorii 86. Ta opcja jest dostępna do użytku w aplikacjach, w którym użytkownicy chcą uzyskać ogólny obraz analizy oprócz szczegółów z jednego lub więcej modeli specyficznych dla domeny. Gdy ta metoda jest wywoływana, najpierw jest wywoływana klasyfikatora Taksonomia kategorii 86. Jeśli dowolnej kategorii pasuje do znanego lub zgodnych modeli, drugi — dostęp próbny wywołania klasyfikatora jest zgodna. Na przykład jeśli `details` parametru wywołanie metody POST protokołu HTTP albo jest ustawiona na "wszystkie" lub "osobistości" obejmuje, metody wywołania klasyfikatora osobistości, po klasyfikatora 86 kategorii jest wywoływana. Jeśli obraz zostanie sklasyfikowany jako `people_` lub podkategorii w kategorii, a następnie klasyfikatora osobistości jest wywoływana.

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