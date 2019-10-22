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
ms.openlocfilehash: 28478f38df6ba6ea356626cc36c23ad498fc1f47
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692421"
---
Potok jest uruchamiany w obiekcie docelowym obliczeń, czyli zasobem obliczeniowym dołączonym do obszaru roboczego.  Po utworzeniu obiektu docelowego obliczeń można użyć go ponownie do przyszłych przebiegów.

1. Wybierz pozycję **Uruchom** w górnej części kanwy, aby uruchomić potok.

1. Gdy zostanie wyświetlone okienko **Ustawienia** , wybierz pozycję **Wybierz element docelowy obliczeń**.

    Jeśli masz już dostępny element docelowy obliczeń, możesz wybrać go do uruchomienia tego potoku.

    > [!NOTE]
    > Interfejs wizualny może uruchamiać tylko eksperymenty dla środowisko obliczeniowe usługi Machine Learning obiektów docelowych. Inne elementy docelowe obliczeń nie będą wyświetlane.

1. Podaj nazwę zasobu obliczeniowego.

1. Wybierz pozycję **Zapisz**.

    ![Skonfiguruj cel obliczeń](./media/aml-ui-create-training-compute/set-compute.png)

1. Wybierz pozycję **Uruchom**.

1. W oknie dialogowym **Konfigurowanie uruchomienia potoku** wybierz pozycję **+ nowy eksperyment** do **eksperymentu**

    * Wprowadź **nazwę eksperymentu** opisowego

    * Wybierz pozycję **Uruchom**
    
    Możesz wyświetlić stan przebiegu i szczegóły w prawym górnym rogu kanwy.

    > [!NOTE]
    > Utworzenie zasobu obliczeniowego trwa około 5 minut. Po utworzeniu zasobu można go ponownie wykorzystać i pominąć ten czas oczekiwania na przyszłe uruchomienia.
    >
    > Zasób obliczeniowy zostanie automatycznie przeskalowany do 0 węzłów, gdy jest w stanie bezczynności, aby zaoszczędzić koszt.  Gdy użyjesz go ponownie po opóźnieniu, możesz ponownie wykonać około 5 minut czasu oczekiwania podczas skalowania w górę.
