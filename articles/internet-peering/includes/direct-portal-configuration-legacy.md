---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775071"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** Wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![konfiguracja komunikacji równorzędnej — bezpośrednie](../media/setup-direct-conf-tab.png)

    * W obszarze **Typ komunikacji równorzędnej**wybierz pozycję *bezpośrednie*.
    * W przypadku **sieci firmy Microsoft**wybierz pozycję *AS8075*. Nie wybieraj numeru ASN 8069. Jest ona zarezerwowana dla specjalnych aplikacji i jest używana tylko przez [komunikację równorzędną firmy Microsoft](mailto:peering@microsoft.com).
    * Wybierz **jednostkę SKU** jako *bezpłatnie jako podstawową*. Nie wybieraj opcji *bezpłatna wersja Premium* , ponieważ jest zarezerwowana dla aplikacji specjalnych.
    * Wybierz lokalizację **, w której** chcesz skonwertować komunikację równorzędną z zasobem platformy Azure. Jeśli masz połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **Metro** , które nie zostały przekonwertowane na zasób platformy Azure, takie połączenia będą wyświetlane w sekcji **połączenia komunikacji równorzędnej** , jak pokazano poniżej. Teraz możesz przekonwertować te połączenia komunikacji równorzędnej na zasób platformy Azure.

        > [!div class="mx-imgBorder"]
        > ![konfiguracja komunikacji równorzędnej — połączenia bezpośrednie/starsze](../media/setup-directlegacy-conf-tab.png)

1. Jeśli potrzebujesz zaktualizować przepustowość, kliknij przycisk Edytuj dla wiersza, jak pokazano poniżej, aby zmodyfikować ustawienia połączenia.

    > [!div class="mx-imgBorder"]
    > ![konfiguracja komunikacji równorzędnej — bezpośrednie edytowanie](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Jeśli chcesz dodać dodatkowe połączenia komunikacji równorzędnej do firmy Microsoft w wybranej lokalizacji **Metro** , możesz to zrobić, klikając przycisk **Utwórz nowy** . Aby uzyskać więcej informacji [, zobacz Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](../howto-direct-portal.md) .
    >

1. Kliknij pozycję **Przegląd + Utwórz**. Zwróć uwagę na to, że w portalu zostanie uruchomione podstawowe sprawdzanie poprawności wprowadzonych informacji. Ten element jest wyświetlany na Wstążce u góry, jak *trwa sprawdzanie poprawności końcowej...*

    > [!div class="mx-imgBorder"]
    > ![kartę walidacji komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po *przejściu na weryfikację*Sprawdź informacje i prześlij żądanie, klikając pozycję **Utwórz**. Jeśli musisz zmodyfikować żądanie, kliknij przycisk **Wstecz** i powtórz powyższe kroki.

    > [!div class="mx-imgBorder"]
    > ![przesyłania komunikacji równorzędnej](../media/setup-direct-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com). Pomyślne wdrożenie zostanie wyświetlone poniżej.

    > [!div class="mx-imgBorder"]
    > ![sukcesu komunikacji równorzędnej](../media/setup-direct-success.png)
