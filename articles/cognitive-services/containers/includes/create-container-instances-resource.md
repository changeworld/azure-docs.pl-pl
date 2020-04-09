---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób wystąpienia kontenera platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876451"
---
## <a name="create-an-azure-container-instance-resource"></a>Tworzenie zasobu wystąpienia kontenera platformy Azure

1. Przejdź do strony [Tworzenie](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) dla wystąpień kontenera.

2. Na karcie **Podstawy** wprowadź następujące szczegóły:

    |Ustawienie|Wartość|
    |--|--|
    |Subskrypcja|Wybierz subskrypcję.|
    |Grupa zasobów|Wybierz dostępną grupę zasobów lub `cognitive-services`utwórz nową, taką jak .|
    |Nazwa kontenera|Wprowadź nazwę, `cognitive-container-instance`taką jak . Nazwa musi być w niższych czapkach.|
    |Lokalizacja|Wybierz region do wdrożenia.|
    |Typ obrazu|Jeśli obraz kontenera jest przechowywany w rejestrze kontenerów, `Public`który nie wymaga poświadczeń, wybierz opcję . Jeśli dostęp do obrazu kontenera `Private`wymaga poświadczeń, wybierz opcję . Szczegółowe informacje na temat tego, czy obraz kontenera jest `Public` `Private` lub nie zawiera ("Podgląd publiczny" można znaleźć w [repozytoriach kontenerów i obrazach.](../../cognitive-services-container-support.md#container-repositories-and-images) |
    |Nazwa obrazu|Wprowadź lokalizację kontenera usług Cognitive Services. Lokalizacja jest to, co jest używane `docker pull` jako argument do polecenia. Informacje na temat dostępnych nazw obrazów i odpowiednich repozytoriów kontenerów można znaleźć w [repozytoriach kontenerów i ich](../../cognitive-services-container-support.md#container-repositories-and-images) odpowiednich repozytoriach.<br><br>Nazwa obrazu musi być w pełni kwalifikowana, określając trzy części. Najpierw rejestr kontenerów, następnie repozytorium, na koniec `<container-registry>/<repository>/<image-name>`nazwa obrazu: .<br><br>Oto przykład, `mcr.microsoft.com/azure-cognitive-services/keyphrase` może reprezentować obraz wyodrębniania fraz kluczowych w rejestrze kontenerów firmy Microsoft w repozytorium usług Azure Cognitive Services. Innym przykładem `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` jest, który reprezentuje obraz mowy do tekstu w repozytorium firmy Microsoft rejestru kontenera kontenera w wersji kontenera w wersji owej. |
    |Typ systemu operacyjnego|`Linux`|
    |Rozmiar|Zmień rozmiar na sugerowane zalecenia dla określonego kontenera usługi Cognitive Service:<br>2 rdzenie procesora<br>4 GB

3. Na karcie **Sieć** wprowadź następujące szczegóły:

    |Ustawienie|Wartość|
    |--|--|
    |Porty|Ustaw port TCP `5000`na . Udostępnia kontener na porcie 5000.|

4. Na karcie **Zaawansowane** wprowadź wymagane **zmienne środowiskowe** dla ustawień rozliczeń kontenerów zasobu wystąpienia kontenera platformy Azure:

    | Klucz | Wartość |
    |--|--|
    |`apikey`|Skopiowano ze strony **Klucze** zasobu. Jest to ciąg 32 znaków alfanumerycznej bez spacji lub kresek, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Skopiowano ze strony **Przegląd** zasobu.|
    |`eula`|`accept`|

5. Kliknij **pozycję Przejrzyj i utwórz**
6. Po przejściu weryfikacji kliknij przycisk **Utwórz,** aby zakończyć proces tworzenia
7. Po pomyślnym wdrożeniu zasobu jest on gotowy
