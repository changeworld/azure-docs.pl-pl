---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70393812"
---
## <a name="set-up"></a>Konfiguruj

### <a name="create-a-translator-text-resource"></a>Tworzenie zasobu tekst translatora

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla tekstu translatora przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Pobierz [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services) ważny przez 7 dni za darmo. Po zarejestrowaniu się będzie on dostępny w witrynie sieci Web platformy Azure.
* Wyświetlanie istniejącego zasobu w [witrynie Azure portal](https://portal.azure.com/).

Po dokonaniu klucza z subskrypcji próbnej lub zasobu utwórz dwie [zmienne środowiskowe:](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`- Klucz subskrypcji zasobu Tłumacza tekstowego.
* `TRANSLATOR_TEXT_ENDPOINT`- Globalny punkt końcowy dla tekstu tłumacza. Użyj witryny `https://api.cognitive.microsofttranslator.com/`.
