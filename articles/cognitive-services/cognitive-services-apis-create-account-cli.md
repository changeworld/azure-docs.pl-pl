---
title: Tworzenie zasobu usług Cognitive Services przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z usługami Azure Cognitive Services, tworząc i subskrybując zasób przy użyciu interfejsu wiersza polecenia platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 72b00d78d19ed0e963b4dad01b82033c659e1efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219611"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Tworzenie zasobu usług Cognitive Services przy użyciu interfejsu wiersza polecenia platformy Azure(CLI)

Użyj tego przewodnika Szybki start, aby rozpocząć korzystanie z usługi Azure Cognitive Services przy użyciu [interfejsu wiersza polecenia platformy Azure(CLI).](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Usługi Cognitive Services są reprezentowane przez [zasoby](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) platformy Azure utworzone w ramach subskrypcji platformy Azure. Po utworzeniu zasobu użyj kluczy i punktu końcowego wygenerowanego do uwierzytelniania aplikacji. 


W tym przewodniku Szybki start dowiesz się, jak zarejestrować się w usłudze Azure Cognitive Services i utworzyć konto, które ma subskrypcję z jedną lub wieloma usługami przy użyciu [interfejsu wiersza polecenia platformy Azure.In](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)this Quickstart, you'll learn how to sign up for Azure Cognitive Services and create an account that has a single-service or multi-service subscription, Using the Azure Command Line Interface(CLI) . Te usługi są reprezentowane przez [zasoby](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)platformy Azure, które umożliwiają łączenie się z co najmniej jednym interfejsem API usług Azure Cognitive Services.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure — [utwórz bezpłatną](https://azure.microsoft.com/free/) subskrypcję.
* [Interfejs wiersza polecenia platformy Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Instalowanie interfejsu wiersza polecenia platformy Azure i logowanie się 

Zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom polecenie [logowania az:](https://docs.microsoft.com/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

Można również użyć zielonego przycisku **Wypróbuj,** aby uruchomić te polecenia w przeglądarce.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Tworzenie nowej grupy zasobów usług Azure Cognitive Services

Przed utworzeniem zasobu usług Cognitive Services, musisz mieć grupę zasobów platformy Azure, aby zawierać zasób. Podczas tworzenia nowego zasobu można utworzyć nową grupę zasobów lub użyć istniejącej. W tym artykule pokazano, jak utworzyć nową grupę zasobów.

### <a name="choose-your-resource-group-location"></a>Wybieranie lokalizacji grupy zasobów

Aby utworzyć zasób, musisz jedną z lokalizacji platformy Azure dostępnych dla subskrypcji. Za pomocą polecenia [AZ account list-locations](/cli/azure/account#az-account-list-locations) można pobrać listę dostępnych lokalizacji. Większość usług Cognitive Services jest dostępna z kilku lokalizacji. Wybierz najbliższą ci lokalizację lub zobacz, które lokalizacje są dostępne dla usługi.

> [!IMPORTANT]
> * Zapamiętaj swoją lokalizację platformy Azure, ponieważ będzie jej potrzebować podczas wywoływania usług Azure Cognitive Services.
> * Dostępność niektórych usług Cognitive Services może się różnić w zależności od regionu. Aby uzyskać więcej informacji, zobacz [Produkty platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Po utworzeniu lokalizacji platformy Azure utwórz nową grupę zasobów w wierszu polecenia platformy Azure za pomocą polecenia [tworzenia grupy az.](/cli/azure/group#az-group-create)

W poniższym przykładzie zastąp lokalizację `westus2` platformy Azure jedną z lokalizacji platformy Azure dostępnych dla twojej subskrypcji.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Wybieranie usługi kognitywnej i warstwy cenowej

Podczas tworzenia nowego zasobu, należy znać "rodzaj" usługi, które mają być używane, wraz z [warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/) (lub sku) chcesz. Użyjesz tych i innych informacji jako parametrów podczas tworzenia zasobu.

### <a name="multi-service"></a>Obsługa wielodochodowa

| Usługa                    | Rodzaj                      |
|----------------------------|---------------------------|
| Wiele usług. Więcej informacji można znaleźć na stronie [z cennikiem.](https://azure.microsoft.com/pricing/details/cognitive-services/)            | `CognitiveServices`     |


> [!NOTE]
> Wiele z usług Cognitive Poniżej mają bezpłatną warstwę, której można użyć do wypróbowania usługi. Aby użyć warstwy `F0` bezpłatnej, użyj jako sku dla zasobu.

### <a name="vision"></a>Obraz

| Usługa                    | Rodzaj                      |
|----------------------------|---------------------------|
| Przetwarzanie obrazów            | `ComputerVision`          |
| Wizja niestandardowa - Przewidywanie | `CustomVision.Prediction` |
| Wizja niestandardowa - Szkolenie   | `CustomVision.Training`   |
| Rozpoznawanie twarzy                       | `Face`                    |
| Rozpoznawanie formularzy            | `FormRecognizer`          |
| Rozpoznawanie pisma odręcznego             | `InkRecognizer`           |

### <a name="search"></a>Wyszukiwanie

| Usługa            | Rodzaj                  |
|--------------------|-----------------------|
| Automatyczne sugerowanie Bing   | `Bing.Autosuggest.v7` |
| Wyszukiwanie niestandardowe Bing | `Bing.CustomSearch`   |
| Wyszukiwanie jednostek Bing | `Bing.EntitySearch`   |
| Wyszukiwanie Bing        | `Bing.Search.v7`      |
| Sprawdzanie pisowni Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Mowa

| Usługa            | Rodzaj                 |
|--------------------|----------------------|
| Usługi mowy    | `SpeechServices`     |
| Rozpoznawanie mowy | `SpeakerRecognition` |

### <a name="language"></a>Język

| Usługa            | Rodzaj                |
|--------------------|---------------------|
| Zrozumienie formularzy | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Analiza tekstu     | `TextAnalytics`     |
| Tłumaczenie tekstu   | `TextTranslation`   |

### <a name="decision"></a>Decyzja

| Usługa           | Rodzaj               |
|-------------------|--------------------|
| Narzędzie do wykrywania anomalii  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizacja      | `Personalizer`     |

Listę dostępnych "rodzajów" usługi Cognitive Service można znaleźć za pomocą polecenia [az cognitiveservices account list-kinds:](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Dodawanie nowego zasobu do grupy zasobów

Aby utworzyć i zasubskrybować nowy zasób usług Cognitive Services, użyj [polecenia tworzenia konta az cognitiveservices.](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) To polecenie dodaje nowy zasób podlegający rozliczaleniu do grupy zasobów utworzonej wcześniej. Podczas tworzenia nowego zasobu, należy znać "rodzaj" usługi, które mają być używane, wraz z jego warstwy cenowej (lub sku) i lokalizacji platformy Azure:

Można utworzyć zasób F0 (wolny) dla `anomaly-detector-resource` detektora anomalii, nazwany za pomocą poniższego polecenia.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Pobierz klucze do zasobu

Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia(CLI), użyj polecenia [logowania az.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Użyj polecenia [listy kluczy konta az cognitiveservices,](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) aby uzyskać klucze dla zasobu usługi Cognitive Service.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Warstwy cenowe i rozliczenia

Warstwy cenowe (i kwota, którą otrzymasz naliczone) są oparte na liczbie transakcji wysyłanych przy użyciu informacji uwierzytelniania. Każda warstwa cenowa określa:
* maksymalna liczba dozwolonych transakcji na sekundę (TPS).
* funkcji usługi włączonych w warstwie cenowej.
* Koszt wstępnie zdefiniowanej kwoty transakcji. Przekroczenie tej kwoty spowoduje dodatkową opłatę określoną w [szczegółach cen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) usługi.

## <a name="get-current-quota-usage-for-your-resource"></a>Uzyskaj bieżące użycie przydziału dla zasobu

Użyj [az cognitiveservices account list-usage](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) polecenia, aby uzyskać użycie zasobu usługi Cognitive Service.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz oczyścić i usunąć zasób usług Cognitive Services, możesz go usunąć lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych zasobów zawartych w grupie.

Aby usunąć grupę zasobów i skojarzone z nią zasoby, użyj polecenia delete grupy az.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Zobacz też

* [Uwierzytelnij żądania w usługach Azure Cognitive Services](authentication.md)
* [Co to są usługi Azure Cognitive Services?](Welcome.md)
* [Obsługa języka naturalnego](language-support.md)
* [Obsługa kontenerów platformy Docker](cognitive-services-container-support.md)
