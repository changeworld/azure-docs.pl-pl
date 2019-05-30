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
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66397227"
---
Eksperyment działa na cel obliczenia zasobu obliczeniowego, który jest dołączony do swojego obszaru roboczego.  Po utworzeniu obliczeniowego elementu docelowego, możesz użyć go ponownie dla przyszłych przebiegów.

1. Wybierz **Uruchom** u dołu, aby uruchomić eksperyment.

     ![Uruchamianie eksperymentu](./media/aml-ui-create-training-compute/run-experiment.png)

1. Gdy **celów obliczeń Instalatora** zostanie wyświetlone okno dialogowe, jeśli obszar roboczy ma już zasobu obliczeniowego, możesz wybrać go teraz.  W przeciwnym razie wybierz **Utwórz nową**.

    > [!NOTE]
    > Interfejs graficzny można uruchamiać eksperymenty tylko na cele obliczeniowego usługi Machine Learning. Inne obliczeniowych elementów docelowych nie będą wyświetlane.

1. Podaj nazwę zasobu obliczeniowego.

1. Wybierz pozycję **Uruchom**.

    ![Instalator obliczeniowych elementów docelowych](./media/aml-ui-create-training-compute/set-compute.png)

    Zasób obliczeniowy będą teraz tworzone. Stan jest wyświetlany w prawym górnym rogu eksperymentu. 

    > [!NOTE]
    > Trwa około 5 minut na utworzenie zasobów obliczeniowych. Po utworzeniu zasobu możesz użyć go ponownie i pominąć ten czas oczekiwania dla przyszłych przebiegów.
    >
    > Zasobów obliczeniowych zostanie automatyczne skalowanie na 0 węzłów w stanie bezczynności koszty.  Gdy używasz go ponownie z opóźnieniem, ponownie mogą występować około 5 minut czasu oczekiwania podczas skanowane ponownie w.
