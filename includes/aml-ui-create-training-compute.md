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
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929642"
---
Potok jest uruchamiany w obiekcie docelowym obliczeń, który jest zasobem obliczeniowym dołączonym do obszaru roboczego. Po utworzeniu obiektu docelowego obliczeń można użyć go ponownie do przyszłych przebiegów.

1. Wybierz pozycję **Uruchom** w górnej części kanwy, aby uruchomić potok.

1. Gdy zostanie wyświetlone okienko **Ustawienia** , wybierz pozycję **Wybierz element docelowy obliczeń**.

    Jeśli masz już dostępny element docelowy obliczeń, możesz wybrać go do uruchomienia tego potoku.

    > [!NOTE]
    > Projektant może uruchamiać eksperymenty tylko na Azure Machine Learning docelowych obliczeń. Inne elementy docelowe obliczeń nie będą wyświetlane.

1. Wprowadź nazwę zasobu obliczeniowego.

1. Wybierz pozycję **Zapisz**.

    ![Skonfiguruj cel obliczeń](./media/aml-ui-create-training-compute/set-compute.png)

1. Wybierz pozycję **Uruchom**.

1. W oknie dialogowym **Konfigurowanie uruchomienia potoku** wybierz pozycję **+ nowy eksperyment** do **eksperymentu**.

    > [!NOTE]
    > Grupy eksperymentów działają podobnie. W przypadku uruchomienia potoku wiele razy można wybrać ten sam eksperyment dla kolejnych uruchomień.

    1. Wprowadź opisową nazwę **eksperymentu**.

    1. Wybierz pozycję **Uruchom**.
    
    Możesz wyświetlić stan przebiegu i szczegóły w prawym górnym rogu kanwy.

    > [!NOTE]
    > Utworzenie zasobu obliczeniowego trwa około 5 minut. Po utworzeniu zasobu można go ponownie wykorzystać i pominąć ten czas oczekiwania na przyszłe uruchomienia.
    >
    > Zasób obliczeniowy jest automatycznie skalowany na zero węzłów, gdy jest bezczynny, aby zaoszczędzić koszt. Gdy używasz go ponownie po opóźnieniu, może wystąpić około pięć minut czasu oczekiwania podczas skalowania kopii zapasowej.
