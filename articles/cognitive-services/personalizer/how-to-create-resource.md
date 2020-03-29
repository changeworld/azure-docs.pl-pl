---
title: Tworzenie zasobu personalizatora
description: Konfiguracja usługi obejmuje sposób, w jaki usługa traktuje nagrody, jak często usługa eksploruje, jak często model jest przeszkolony i ile danych jest przechowywanych.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336062"
---
# <a name="create-a-personalizer-resource"></a>Tworzenie zasobu personalizatora

Zasób Personalizer jest tym samym, co pętla uczenia personalizatora. Dla każdej domeny podmiotu lub obszaru zawartości, który posiadasz, jest tworzony pojedynczy zasób lub pętla uczenia się. Nie należy używać wielu obszarów zawartości w tej samej pętli, ponieważ będzie to mylić pętli uczenia się i zapewnić słabe prognozy.

Jeśli chcesz, aby Personalizer wybrał najlepszą zawartość dla więcej niż jednego obszaru zawartości strony internetowej, użyj innej pętli uczenia się dla każdego.


## <a name="create-a-resource-in-the-azure-portal"></a>Tworzenie zasobu w witrynie Azure portal

Utwórz zasób Personalizer dla każdej pętli sprzężenia zwrotnego.

1. Zaloguj się do [witryny Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Poprzednie łącze spowoduje przejście do strony **Utwórz** dla usługi Personalizator.
1. Wprowadź nazwę usługi, wybierz subskrypcję, lokalizację, warstwę cenową i grupę zasobów.

    > [!div class="mx-imgBorder"]
    > ![Użyj witryny Azure portal do tworzenia zasobów Personalizer, nazywanych również pętlą uczenia się.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Wybierz **pozycję Utwórz,** aby utworzyć zasób.

1. Po wdrożeniu zasobu wybierz przycisk **Przejdź do zasobu,** aby przejść do zasobu Personalizer.

1. Wybierz stronę **Szybki start** dla zasobu, a następnie skopiuj wartości punktu końcowego i klucza. Do użycia interfejsów API rangi i nagrody potrzebny jest zarówno punkt końcowy zasobu, jak i klucz.

1. Wybierz stronę **Konfiguracja** nowego zasobu, aby [skonfigurować pętlę uczenia .](how-to-settings.md)

## <a name="create-a-resource-with-the-azure-cli"></a>Tworzenie zasobu za pomocą interfejsu wiersza polecenia platformy Azure

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure za pomocą następującego polecenia:

    ```azurecli-interactive
    az login
    ```

1. Utwórz grupę zasobów, logiczne grupowanie do zarządzania wszystkimi zasobami platformy Azure, które mają być używane z zasobem Personalizer.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Utwórz nowy zasób Personalizer, _pętla uczenia się_, z następującym poleceniem dla istniejącej grupy zasobów.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Zwraca obiekt JSON, który zawiera **punkt końcowy zasobu**.

1. Użyj następującego polecenia interfejsu wiersza polecenia platformy Azure, aby uzyskać **klucz zasobu**.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Do użycia interfejsów API rangi i nagrody potrzebny jest zarówno punkt końcowy zasobu, jak i klucz.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie](how-to-settings.md) Pętla uczenia się personalizatora