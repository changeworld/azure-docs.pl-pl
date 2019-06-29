---
title: Tłumaczenie za zaporami — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Wykonuje translację elementu zapory adresów IP przy użyciu interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: d0ebce2dd41b170a18057ca76dd3ae4cf3ce0be2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435979"
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
