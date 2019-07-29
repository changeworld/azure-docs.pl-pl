---
title: Przetłumacz za zapory — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Przetłumacz za zapory IP przy użyciu interfejs API tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 567e869ab9ccb2f29cd0e88ba2e44d7d1b4a296c
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595279"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Jak przetłumaczyć zapory IP za pomocą interfejs API tłumaczenia tekstu w usłudze Translator

Interfejs API tłumaczenia tekstu w usłudze Translator można przetłumaczyć za zapory przy użyciu nazwy domeny lub filtrowania adresów IP. Filtrowanie nazw domen jest preferowaną metodą. **Nie zaleca** się uruchamiania usługi Microsoft Translator za pomocą odfiltrowanej zapory protokołu IP. Instalacja może się pojawić w przyszłości bez powiadomienia.

## <a name="translator-ip-addresses"></a>Adresy IP translatora
Adresy IP dla api.cognitive.microsofttranslator.com — Microsoft interfejs API tłumaczenia tekstu w usłudze Translator od 20 listopada 2018:

* **Azja i Pacyfik:** 40.90.139.163, 104.44.89.44
* **Terenie** 40.90.138.4, 40.90.141.99
* **Ameryka Północna:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Tłumaczenie za zapory IP w wywołaniu interfejsu API translatora](reference/v3-0-translate.md)
