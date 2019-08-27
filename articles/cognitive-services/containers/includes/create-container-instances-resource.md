---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób wystąpienia kontenera platformy Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 07e2067571c7bc7403ee8a1d1a0600c451e1581f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051178"
---
## <a name="create-an-azure-container-instance-resource"></a>Tworzenie zasobu wystąpienia kontenera platformy Azure

1. Przejdź do strony [Tworzenie](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) dla Container Instances.

2. Na karcie **podstawowe** wprowadź następujące informacje:

    |Ustawienie|Value|
    |--|--|
    |Subscription|Wybierz subskrypcję.|
    |Resource group|Wybierz dostępną grupę zasobów lub Utwórz nową, taką jak `cognitive-services`.|
    |Nazwa kontenera|Wprowadź nazwę, taką jak `cognitive-container-instance`. Nazwa musi być krótsza.|
    |Location|Wybierz region do wdrożenia.|
    |Typ obrazu|Jeśli obraz kontenera jest przechowywany w rejestrze kontenerów, który nie wymaga poświadczeń, wybierz `Public`opcję. W przypadku uzyskiwania dostępu do obrazu kontenera wymagane są `Private`poświadczenia. Zapoznaj się z [repozytoriami kontenerów i obrazami](../../cognitive-services-container-support.md#container-repositories-and-images) , aby uzyskać szczegółowe informacje na `Public` temat tego, czy obraz kontenera jest lub `Private` ("publiczna wersja zapoznawcza"). |
    |Nazwa obrazu|Wprowadź Cognitive Services lokalizację kontenera. Lokalizacja jest używana jako argument `docker pull` polecenia. Zapoznaj się z [repozytoriami i obrazami kontenerów](../../cognitive-services-container-support.md#container-repositories-and-images) dla dostępnych nazw obrazów i ich odpowiedniego repozytorium.<br><br>Nazwa obrazu musi być w pełni kwalifikowana, określając trzy części. Najpierw należy nazwa obrazu rejestru kontenerów, a następnie repozytorium: `<container-registry>/<repository>/<image-name>`.<br><br>Oto przykład, `mcr.microsoft.com/azure-cognitive-services/keyphrase` który reprezentuje wyodrębnianie kluczowych fraz obraz w Container Registry Microsoft w ramach repozytorium Cognitive Services platformy Azure. Innym przykładem jest `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` , który będzie reprezentować mowę do obrazu tekstu w repozytorium firmy Microsoft rejestru kontenerów w wersji zapoznawczej kontenera. |
    |Typ systemu operacyjnego|`Linux`|
    |Size|Zmień rozmiar sugerowanych zaleceń dla określonego kontenera usługi poznawczej:<br>2 rdzenie procesora CPU<br>4 GB

3. Na karcie **Sieć** wprowadź następujące informacje:

    |Ustawienie|Value|
    |--|--|
    |Porty|Ustaw port TCP na `5000`. Udostępnia kontener na porcie 5000.|

4. Na karcie **Zaawansowane** wprowadź wymagane **zmienne środowiskowe** dla ustawień rozliczania kontenerów zasobu wystąpienia kontenera platformy Azure:

    | Klucz | Value |
    |--|--|
    |`apikey`|Skopiowane ze strony **klucze** zasobu. Jest to 32 ciąg znaków alfanumerycznych bez spacji ani kresek `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Skopiowane ze strony **Przegląd** zasobu.|
    |`eula`|`accept`|

1. Kliknij przycisk **Przeglądaj i Utwórz**
1. Po zakończeniu walidacji kliknij przycisk **Utwórz** , aby zakończyć proces tworzenia
1. Po pomyślnym wdrożeniu zasobu jest gotowy