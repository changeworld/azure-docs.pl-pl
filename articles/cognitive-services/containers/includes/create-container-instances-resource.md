---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób wystąpienia kontenera platformy Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 1679862b1660d3c8b2505c6e0c54f203f5d4665d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383535"
---
## <a name="create-an-azure-container-instance-resource"></a>Tworzenie zasobu wystąpienia kontenera platformy Azure

1. Przejdź do strony [Tworzenie](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) dla Container Instances.

2. Na karcie **podstawowe** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Subskrypcja|Wybierz subskrypcję.|
    |Grupa zasobów|Wybierz dostępną grupę zasobów lub Utwórz nową, taką jak `cognitive-services`.|
    |Nazwa kontenera|Wprowadź nazwę, taką jak `cognitive-container-instance`. Nazwa musi być krótsza.|
    |Lokalizacja|Wybierz region do wdrożenia.|
    |Typ obrazu|Jeśli obraz kontenera jest przechowywany w rejestrze kontenerów, który nie wymaga poświadczeń, wybierz `Public`. W przypadku uzyskiwania dostępu do obrazu kontenera wymagane są poświadczenia, wybierz `Private`. Zapoznaj się z [repozytoriami i obrazami kontenera,](../../cognitive-services-container-support.md#container-repositories-and-images) Aby uzyskać szczegółowe informacje na temat tego, czy obraz kontenera jest `Public`, czy `Private` ("publiczna wersja zapoznawcza"). |
    |Nazwa obrazu|Wprowadź Cognitive Services lokalizację kontenera. Lokalizacja jest używana jako argument polecenia `docker pull`. Zapoznaj się z [repozytoriami i obrazami kontenerów](../../cognitive-services-container-support.md#container-repositories-and-images) dla dostępnych nazw obrazów i ich odpowiedniego repozytorium.<br><br>Nazwa obrazu musi być w pełni kwalifikowana, określając trzy części. Najpierw należy do rejestru kontenerów, a następnie repozytorium, na końcu nazwę obrazu: `<container-registry>/<repository>/<image-name>`.<br><br>Oto przykład, `mcr.microsoft.com/azure-cognitive-services/keyphrase` reprezentuje obraz wyodrębnianie kluczowych fraz w Container Registry firmy Microsoft w ramach repozytorium Cognitive Services platformy Azure. Innym przykładem jest, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`, która będzie reprezentować mowę do obrazu tekstu w repozytorium firmy Microsoft w rejestrze kontenerów w wersji zapoznawczej kontenera. |
    |Typ systemu operacyjnego|`Linux`|
    |Rozmiar|Zmień rozmiar sugerowanych zaleceń dla określonego kontenera usługi poznawczej:<br>2 rdzenie procesora CPU<br>4 GB

3. Na karcie **Sieć** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Porty|Ustaw port TCP na `5000`. Udostępnia kontener na porcie 5000.|

4. Na karcie **Zaawansowane** wprowadź wymagane **zmienne środowiskowe** dla ustawień rozliczania kontenerów zasobu wystąpienia kontenera platformy Azure:

    | Klucz | Wartość |
    |--|--|
    |`apikey`|Skopiowane ze strony **klucze** zasobu. Jest to 32 ciąg znaków alfanumerycznych bez spacji ani kresek, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Skopiowane ze strony **Przegląd** zasobu.|
    |`eula`|`accept`|

1. Kliknij przycisk **Przeglądaj i Utwórz**
1. Po zakończeniu walidacji kliknij przycisk **Utwórz** , aby zakończyć proces tworzenia
1. Po pomyślnym wdrożeniu zasobu jest gotowy