---
title: Czym jest usługa Custom Vision Service?
titlesuffix: Azure Cognitive Services
description: Usługa Custom Vision Service umożliwia kompilowanie klasyfikatorów obrazów niestandardowych w chmurze platformy Azure.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 1d3d1a82cf59b06625487fb241a63f51352e18e5
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365863"
---
# <a name="what-is-the-custom-vision-service"></a>Czym jest usługa Custom Vision Service?

Usługa Custom Vision Service to usługa Microsoft Cognitive Service umożliwiająca tworzenie klasyfikatorów obrazów niestandardowych. Dzięki usłudze można prościej i szybciej tworzyć, wdrażać i poprawiać klasyfikatory obrazów. Usługa Custom Vision Service zapewnia interfejs API REST oraz interfejs internetowy na potrzeby przekazywania obrazów i uczenia klasyfikatora.

## <a name="what-does-custom-vision-service-do-well"></a>Co usługa Custom Vision Service robi dobrze?

Usługa Custom Vision Service działa najlepiej, gdy element do sklasyfikowania jest wyraźnie widoczny na obrazie. 

Aby utworzyć klasyfikator lub detektor, potrzeba kilku obrazów. 50 obrazów na klasę to wystarczająco dużo, aby uruchomić prototyp. Metody używane przez usługę Custom Vision Service są odporne na różnice, więc możesz rozpocząć tworzenie prototypów z tak małą ilością danych. Oznacza to, że usługa Custom Vision Service nie jest dobrym rozwiązaniem w przypadku scenariuszy, w których chcesz wykrywać subtelne różnice. Na przykład w przypadku wykrywania niewielkich rys lub wgnieceń w scenariuszach związanych z zapewnianiem jakości.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak utworzyć klasyfikator](getting-started-build-a-classifier.md)
