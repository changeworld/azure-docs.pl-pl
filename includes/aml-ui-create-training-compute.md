---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: f7cdfb8b9edbce5ef2b094cf6a603904d39a7404
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493641"
---
Potok jest uruchamiany w miejscu docelowym obliczeń, który jest zasobem obliczeniowym dołączonym do obszaru roboczego. Po utworzeniu obiektu docelowego obliczeń można użyć go ponownie do przyszłych przebiegów.

1. Wybierz pozycję **Uruchom** w górnej części kanwy, aby uruchomić potok.

1. Gdy zostanie wyświetlone okienko **Ustawienia** , wybierz pozycję **Wybierz element docelowy obliczeń**.

    Jeśli masz już dostępny element docelowy obliczeń, możesz wybrać go do uruchomienia tego potoku.

    > [!NOTE]
    > Projektant może uruchamiać tylko eksperymenty dla środowisko obliczeniowe usługi Machine Learning obiektów docelowych. Inne elementy docelowe obliczeń nie będą wyświetlane.

1. Podaj nazwę zasobu obliczeniowego.

1. Wybierz pozycję **Zapisz**.

    ![Skonfiguruj cel obliczeń](./media/aml-ui-create-training-compute/set-compute.png)

1. Wybierz pozycję **Uruchom**.

1. W oknie dialogowym **Konfigurowanie uruchomienia potoku** wybierz pozycję **+ nowy eksperyment** do **eksperymentu**

    > [!NOTE]
    > Grupy eksperymentów działają podobnie. W przypadku uruchomienia potoku wiele razy można wybrać ten sam eksperyment dla kolejnych uruchomień.

    * Wprowadź **nazwę eksperymentu** opisowego

    * Wybierz pozycję **Uruchom**
    
    Możesz wyświetlić stan przebiegu i szczegóły w prawym górnym rogu kanwy.

    > [!NOTE]
    > Utworzenie zasobu obliczeniowego trwa około 5 minut. Po utworzeniu zasobu można go ponownie wykorzystać i pominąć ten czas oczekiwania na przyszłe uruchomienia.
    >
    > Zasób obliczeniowy zostanie automatycznie przeskalowany do 0 węzłów, gdy jest w stanie bezczynności, aby zaoszczędzić koszt.  Gdy użyjesz go ponownie po opóźnieniu, możesz ponownie wykonać około 5 minut czasu oczekiwania podczas skalowania w górę.
