---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: eb84dc1b5bf3f756e484ef27aaa998ab6b94cc51
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891640"
---
Eksperyment jest uruchamiany w miejscu docelowym obliczeń, zasobem obliczeniowym dołączonym do obszaru roboczego.  Po utworzeniu obiektu docelowego obliczeń można użyć go ponownie do przyszłych przebiegów.

1. Wybierz pozycję **Uruchom** u dołu, aby uruchomić eksperyment.

1. Gdy zostanie wyświetlone okno dialogowe **cele obliczeń instalacji** , jeśli obszar roboczy ma już zasób obliczeniowy, możesz go wybrać teraz.  W przeciwnym razie wybierz pozycję **Utwórz nowy**.

    > [!NOTE]
    > Interfejs wizualny może uruchamiać tylko eksperymenty dla środowisko obliczeniowe usługi Machine Learning obiektów docelowych. Inne elementy docelowe obliczeń nie będą wyświetlane.

1. Podaj nazwę zasobu obliczeniowego.

1. Wybierz pozycję **Uruchom**.

    ![Skonfiguruj cel obliczeń](./media/aml-ui-create-training-compute/set-compute.png)

    Zasób obliczeniowy zostanie teraz utworzony. Wyświetl stan w prawym górnym rogu eksperymentu. 

    > [!NOTE]
    > Utworzenie zasobu obliczeniowego trwa około 5 minut. Po utworzeniu zasobu można go ponownie wykorzystać i pominąć ten czas oczekiwania na przyszłe uruchomienia.
    >
    > Zasób obliczeniowy zostanie automatycznie przeskalowany do 0 węzłów, gdy jest w stanie bezczynności, aby zaoszczędzić koszt.  Gdy użyjesz go ponownie po opóźnieniu, możesz ponownie wykonać około 5 minut czasu oczekiwania podczas skalowania w górę.
