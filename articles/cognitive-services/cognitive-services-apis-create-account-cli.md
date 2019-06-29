---
title: Tworzenie konta usług Cognitive Services za pomocą wiersza polecenia platformy Azure
titlesuffix: Azure Cognitive Services
description: Jak utworzyć konta interfejsów API usługi Azure Cognitive Services przy użyciu wiersza polecenia platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 26f7f3ab60347d9ec5f2a144410ad3de436f5b5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454896"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>Tworzenie konta usług Cognitive Services za pomocą Interface(CLI) wiersza polecenia platformy Azure

W tym przewodniku Szybki Start dowiesz się, jak utworzyć konto usług Azure Cognitive Services i Utwórz konto, które ma subskrypcję usługi jednego lub wielu usług, używanie [Interface(CLI) wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Te usługi są reprezentowane przez platformę Azure [zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), które umożliwiają łączenie do jednego lub więcej interfejsów API usługi Azure Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

* Ważnej subskrypcji platformy Azure. [Tworzenie konta usługi](https://azure.microsoft.com/free/) za darmo.
* [Interface(CLI) wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Instalowanie interfejsu wiersza polecenia platformy Azure i logowanie 

Zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) polecenia:

```console
az login
```

Możesz również użyć zielony **wypróbuj** przycisk, aby uruchomić te polecenia w przeglądarce.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Utwórz nową grupę zasobów dla usług Azure Cognitive Services

Subskrypcji do usług Cognitive Services są reprezentowane przez zasoby platformy Azure. Każde konto usług Cognitive Services (i jego skojarzonego zasobu platformy Azure) musi należeć do grupy zasobów platformy Azure.

### <a name="choose-your-resource-group-location"></a>Wybierz lokalizację grupy zasobów

Aby utworzyć zasób, należy jednej z dostępnych lokalizacji platformy Azure dla Twojej subskrypcji. Możesz pobrać listę dostępnych lokalizacji za pomocą [konta az list-locations](/cli/azure/account#az_account_list) polecenia. Większość usług Cognitive Services są dostępne w kilku lokalizacjach. Wybierz ten, który znajduje się najbliżej użytkownika lub lokalizacji, w których są dostępne dla usługi.

> [!IMPORTANT]
> * Będzie on potrzebny podczas wywoływania usług Azure Cognitive Services należy pamiętać o Twojej lokalizacji platformy Azure.
> * Dostępność niektórych usług Cognitive Services może różnić między regionami. Aby uzyskać więcej informacji, zobacz [produkty Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Po utworzeniu usługi lokalizacji platformy azure, Utwórz nową grupę zasobów przy użyciu wiersza polecenia platformy Azure [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenia.

W poniższym przykładzie Zastąp lokalizacji platformy azure `westus2` przy użyciu jednego z lokalizacji platformy Azure, która jest dostępna dla Twojej subskrypcji.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Wybierz warstwę cenową i usługi cognitive Services

Podczas tworzenia nowego zasobu, musisz znać "kind" usługi, którego chcesz użyć wraz z [warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/) (lub pojedynczej jednostki sku) ma. Użyjesz tego i innych informacji jako parametry podczas tworzenia zasobu.

> [!NOTE]
> Wiele usług Cognitive services oferować bezpłatną warstwę, używanej do Wypróbuj tę usługę. Aby korzystać z bezpłatnej warstwy, użyj `F0` jako jednostka sku dla zasobu.

### <a name="vision"></a>Obraz

| Usługa                    | rodzaj                      |
|----------------------------|---------------------------|
| Przetwarzanie obrazów            | `ComputerVision`          |
| Custom Vision - prognoz | `CustomVision.Prediction` |
| Custom Vision — szkolenia   | `CustomVision.Training`   |
| Interfejs API rozpoznawania twarzy                   | `Face`                    |
| Rozpoznawanie formularzy            | `FormRecognizer`          |
| Rozpoznawanie pisma odręcznego             | `InkRecognizer`           |

### <a name="search"></a>Wyszukiwanie

| Usługa            | rodzaj                  |
|--------------------|-----------------------|
| Automatyczne sugerowanie Bing   | `Bing.Autosuggest.v7` |
| Wyszukiwanie niestandardowe Bing | `Bing.CustomSearch`   |
| Wyszukiwanie jednostek Bing | `Bing.EntitySearch`   |
| Wyszukiwanie Bing        | `Bing.Search.v7`      |
| Sprawdzanie pisowni Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Mowa

| Usługa            | rodzaj                 |
|--------------------|----------------------|
| Usługi mowy    | `SpeechServices`     |
| Rozpoznawanie mowy | `SpeakerRecognition` |

### <a name="language"></a>Język

| Usługa            | rodzaj                |
|--------------------|---------------------|
| Opis formularza | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Analiza tekstu     | `TextAnalytics`     |
| Tłumaczenie tekstu   | `TextTranslation`   |

### <a name="decision"></a>Decyzja

| Usługa           | rodzaj               |
|-------------------|--------------------|
| Narzędzie do wykrywania anomalii  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizacja      | `Personalizer`     |

Można znaleźć listę dostępnych usługi cognitive Services "rodzaju" z [az cognitiveservices account listę rodzajów](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) polecenia:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Dodaj nowy zasób do grupy zasobów

Aby tworzyć i subskrybować nowy zasób usług Cognitive Services, użyj [az cognitiveservices konta Utwórz](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) polecenia. To polecenie dodaje nowy zasób płatnych w grupie zasobów utworzonej wcześniej. Podczas tworzenia nowego zasobu, musisz znać "kind" usługi, którego chcesz użyć wraz z jego cen warstwy (lub jednostki sku) i lokalizację platformy Azure:

Można utworzyć zasób (wersja bezpłatna) F0 wykrywanie anomalii, o nazwie `anomaly-detector-resource` za pomocą poniższego polecenia.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>Uzyskiwanie kluczy subskrypcji

Aby zalogować się do lokalnej instalacji Interface(CLI) wiersza polecenia, należy użyć [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) polecenia.

```console
az login
```

Użyj [az cognitiveservices account listy kluczy](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) polecenie, aby pobrać klucze służące do zasobu usługi cognitive Services.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupa zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych zasobów skojarzonych z grupą zasobów.

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia Usuń az group.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Zobacz także

* [Uwierzytelnianie żądań usług Azure Cognitive Services](authentication.md)
* [Co to jest Azure Cognitive Services?](Welcome.md)
* [Obsługuje język naturalny](language-support.md)
* [Obsługa kontenerów platformy docker](cognitive-services-container-support.md)
