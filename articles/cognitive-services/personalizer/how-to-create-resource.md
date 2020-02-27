---
title: Utwórz zasób personalizacji
description: Konfiguracja usługi zawiera informacje o tym, jak usługa traktuje korzyści, jak często bada usługa, jak często jest on przełączany i jak dużo danych jest przechowywanych.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: cb14415f3a5950ad1534d9eb8da94198a41f4f91
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624232"
---
# <a name="create-a-personalizer-resource"></a>Utwórz zasób personalizacji

Zasób personalizacji jest tym samym elementem co pętla szkoleniowa programu Personalizacja. Pojedynczy zasób lub pętla szkoleniowa jest tworzona dla każdej domeny podmiotu lub obszaru zawartości. Nie używaj wielu obszarów zawartości w tej samej pętli, ponieważ spowoduje to pomylić pętli uczenia i dostarczenie słabych prognoz.

Jeśli chcesz, aby Personalizował wybór najlepszej zawartości dla więcej niż jednego obszaru zawartości strony sieci Web, użyj innej pętli szkoleniowej dla każdej z nich.


## <a name="create-a-resource-in-the-azure-portal"></a>Utwórz zasób w Azure Portal

Utwórz zasób personalizacji dla każdej pętli opinii.

1. Zaloguj się w [portalu Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Poprzedni link prowadzi do strony **Tworzenie** dla usługi personalizacji.
1. Wprowadź nazwę usługi, wybierz subskrypcję, lokalizację, warstwę cenową i grupę zasobów.

    > [!div class="mx-imgBorder"]
    > ![używać Azure Portal do tworzenia zasobów personalizacji, nazywanych również pętlą uczenia.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć zasób.

1. Po wdrożeniu zasobu wybierz przycisk **Przejdź do zasobu** , aby przejść do zasobu personalizowania. Przejdź do strony **Konfiguracja** nowego zasobu, aby [skonfigurować pętlę uczenia](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Tworzenie zasobu za pomocą interfejsu wiersza polecenia platformy Azure

1. Zaloguj się w interfejsie wiersza polecenia platformy Azure przy użyciu następujące polecenie:

    ```bash
    az login
    ```

1. Utwórz grupę zasobów, czyli grupowanie logiczne służące do zarządzania wszystkimi zasobami platformy Azure, które mają być używane z zasobem narzędzia Personalizacja.


    ```bash
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Utwórz nowy zasób personalizacji, _pętlę uczenia_przy użyciu następującego polecenia dla istniejącej grupy zasobów.

    ```bash
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```
## <a name="next-steps"></a>Następne kroki

* [Konfiguruj](how-to-settings.md) Pętla szkoleniowa personalizacji