---
title: Tłumaczenie za zaporami — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Wykonuje translację elementu zapory adresów IP przy użyciu interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: bf1a63d996a1ed2021654c96576eb9dac2cff8e6
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683364"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Jak przekształcać za zaporami IP za pomocą interfejsu API tłumaczenia tekstu

Interfejs API tekstu usługi Translator można tłumaczyć za zaporami przy użyciu nazwy domeny lub filtrowania adresów IP. Filtrowanie nazwa domeny jest to preferowana metoda. Firma Microsoft **nie zaleca się** uruchomiona usługa Microsoft Translator za pośrednictwem pakietu Integracyjnego filtrowane zapory. Instalator jest prawdopodobnie można przerwać w przyszłości, bez powiadomienia. 

## <a name="translator-ip-addresses"></a>Adresy IP w usłudze Translator
Adresy IP dla api.cognitive.microsofttranslator.com — interfejs API tekstu usługi Translator firmy Microsoft od 20 listopada 2018 r.:

* **Azja i Pacyfik:** 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99
* **Ameryka Północna:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Tłumaczenie zapory adresów IP w wywołania interfejsu API usługi Translator](reference/v3-0-translate.md)