---
title: Regiony publikowania & punktach końcowych — USŁUGA LUIS
description: Region określony w witrynie Azure portal jest taki sam, gdzie można opublikować aplikację usługi LUIS i adres URL punktu końcowego jest generowany dla tego samego regionu.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a7709d15a51637772d7a4775cd71c307dc21c52b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292084"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Tworzenie i publikowanie regionów i skojarzonych kluczy

Trzy regiony tworzenia są obsługiwane przez odpowiednie portale usługi LUIS. Aby opublikować aplikację usługi LUIS w więcej niż jednym regionie, potrzebujesz co najmniej jednego klucza na region.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Regiony tworzenia usługi LUIS
Istnieją trzy portale tworzenia usługi LUIS, na podstawie regionu. Tworzyć i publikować należy w tym samym regionie.

|LUIS|Region tworzenia|Nazwa regionu platformy Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|U.S.<br>nie Europa<br>nie Australia| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Australia| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Europa|`westeurope`|

Regiony tworzenia [zostały sparowane w regionie awaryjnym](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Regiony publikowania i zasoby platformy Azure
Aplikacja jest publikowana we wszystkich regionach skojarzonych z zasobami usługi LUIS dodanymi w portalu usługi LUIS. Na przykład dla aplikacji utworzonej na [www.luis.ai][www.luis.ai], jeśli utworzysz zasób usługi LUIS lub Cognitive Service w **usłudze westus** i [dodasz go do aplikacji jako zasób,](luis-how-to-azure-subscription.md)aplikacja zostanie opublikowana w tym regionie.

## <a name="public-apps"></a>Aplikacje publiczne
Aplikacja publiczna jest publikowana we wszystkich regionach, dzięki czemu użytkownik z kluczem zasobów usługi LUIS oparty na regionie może uzyskać dostęp do aplikacji w dowolnym regionie, który jest skojarzony z kluczem zasobów.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Regiony publikowania są powiązane z regionami tworzenia

Aplikacja regionu tworzenia można opublikować tylko w odpowiednim regionie publikowania. Jeśli aplikacja znajduje się obecnie w niewłaściwym regionie tworzenia, wyeksportuj aplikację i zaimportuj ją do odpowiedniego regionu tworzenia dla regionu publikowania.

Aplikacje usługi https://www.luis.ai LUIS utworzone na mogą być publikowane we wszystkich punktach końcowych z wyjątkiem regionów [europejskich](#publishing-to-europe) i [australijskich.](#publishing-to-australia)

## <a name="publishing-to-europe"></a>Publikowanie w Europie

Aby publikować w regionach europejskich, https://eu.luis.ai należy utworzyć aplikacje usługi LUIS tylko. Jeśli spróbujesz opublikować gdziekolwiek indziej przy użyciu klucza w regionie Europa, usługa LUIS wyświetla komunikat ostrzegawczy. Zamiast tego https://eu.luis.aiużyj pliku . Aplikacje usługi [https://eu.luis.ai][eu.luis.ai] LUIS utworzone w nie są automatycznie migrowane do innych regionów. Eksportuj, a następnie zaimportuj aplikację usługi LUIS w celu jej migracji.

## <a name="europe-publishing-regions"></a>Regiony wydawnicze w Europie

 Region globalny | Tworzenie regionu interfejsu API & witrynie tworzenia witryny sieci Web| Publikowanie & regionów kwerend<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Francja Środkowa<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Północna<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Zachodnia<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Południowe Zjednoczone Królestwo<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publikowanie w Australii

Aby opublikować w regionach Australii, https://au.luis.ai należy utworzyć aplikacje usługi LUIS tylko. Jeśli spróbujesz opublikować gdziekolwiek indziej przy użyciu klucza w regionie Australii, usługa LUIS wyświetla komunikat ostrzegawczy. Zamiast tego https://au.luis.aiużyj pliku . Aplikacje usługi [https://au.luis.ai][au.luis.ai] LUIS utworzone w nie są automatycznie migrowane do innych regionów. Eksportuj, a następnie zaimportuj aplikację usługi LUIS w celu jej migracji.

## <a name="australia-publishing-regions"></a>Regiony wydawnicze Australii

 Region globalny | Tworzenie regionu interfejsu API & witrynie tworzenia witryny sieci Web| Publikowanie & regionów kwerend<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| [Australia](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australia Wschodnia<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Publikowanie w innych regionach

Aby opublikować w innych regionach, [https://www.luis.ai](https://www.luis.ai) należy utworzyć aplikacje usługi LUIS tylko.

## <a name="other-publishing-regions"></a>Inne regiony wydawnicze

 Region globalny | Tworzenie regionu interfejsu API & witrynie tworzenia witryny sieci Web| Publikowanie & regionów kwerend<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| Afryka | `westus`<br>[www.luis.ai][www.luis.ai]| Republika Południowej Afryki Północ<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Indie Środkowe<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Azja Wschodnia<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Japonia Wschodnia<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Japonia Zachodnia<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Korea Środkowa<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Azja Południowo-Wschodnia<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada Środkowa<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Środkowe stany USA<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Wschodnie stany USA<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Wschodnie stany USA 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Północno-środkowe stany USA<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Południowo-środkowe stany USA<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Zachodnio-środkowe stany USA<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Zachodnie stany USA<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Zachodnie stany USA 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Ameryka Południowa | `westus`<br>[www.luis.ai][www.luis.ai] | Brazylia Południowa<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Punkty końcowe

Dowiedz się więcej o [punktach końcowych tworzenia i przewidywania](developer-reference-resource.md).

## <a name="failover-regions"></a>Regiony trybu failover

Każdy region ma region pomocniczy do pracy awaryjnej. Europa zawodzi w Europie, a Australia zawodzi w Australii.

Regiony tworzenia [zostały sparowane w regionie awaryjnym](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Prekompilowane elementy odniesienia](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
