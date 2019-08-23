---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 156486f4f4f0df3d4bb4ab76492709bbecfb8eb5
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906521"
---
## <a name="set-up"></a>Konfigurowanie

### <a name="create-a-translator-text-resource"></a>Tworzenie zasobu tłumaczenie tekstu w usłudze Translator

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla tłumaczenie tekstu w usłudze Translator przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services) ważny przez 7 dni bezpłatnie. Po zarejestrowaniu program będzie dostępny w witrynie sieci Web systemu Azure.
* Wyświetl istniejący zasób w [Azure Portal](https://portal.azure.com/).

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu Utwórz dwie [zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`— Klucz subskrypcji dla zasobu tłumaczenie tekstu w usłudze Translator.
* `TRANSLATOR_TEXT_ENDPOINT`— Regionalny punkt końcowy lub niestandardowa nazwa domeny podrzędnej dla zasobu.
