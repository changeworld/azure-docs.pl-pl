---
title: Trenuj model - Niestandardowy tłumacz
titleSuffix: Azure Cognitive Services
description: Szkolenie modelu jest ważnym krokiem podczas tworzenia modelu tłumaczenia. Szkolenie odbywa się na podstawie dokumentów wybranych dla tych szkoleń.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595686"
---
# <a name="train-a-model"></a>Szkolenie modelu

Szkolenie modelu jest ważnym krokiem do tworzenia modelu tłumaczenia, ponieważ bez szkolenia, model nie może być zbudowany. Szkolenie odbywa się na podstawie dokumentów wybranych do szkoleń.

Aby wyszkolić model:

1.  Wybierz projekt, w którym chcesz utworzyć model.

2.  Karta Dane dla projektu wyświetli wszystkie odpowiednie dokumenty dla pary języków projektu. Ręcznie wybierz dokumenty, których chcesz użyć do przeszkolenia modelu. Na tym ekranie można wybrać dokumenty szkoleniowe, strojenia i testowania. Również po prostu wybrać zestaw szkoleniowy i mieć Custom Translator utworzyć strojenie i zestawy testów dla Ciebie.

    -  Nazwa dokumentu: nazwa dokumentu.

    -  Parowanie: Jeśli ten dokument jest dokumentem równoległym lub jednojęzycznym. Dokumenty jednojęzyczne nie są obecnie obsługiwane w szkoleniach.

    -  Typ dokumentu: może to być szkolenie, dostrajanie, testowanie lub słownik.

    -  Para języków: spowoduje to wyświetlenie języka źródłowego i docelowego dla projektu.

    -  Zdania źródłowe: pokazuje liczbę zdań wyodrębnionych z pliku źródłowego.

    -  Zdania docelowe: pokazuje liczbę zdań wyodrębnionych z pliku docelowego.

    ![Trenowanie modelu](media/how-to/how-to-train-model.png)

3.  Kliknij przycisk Pociąg.

4.  W oknie dialogowym określ nazwę modelu.

5.  Kliknij pozycję Model pociągu.

    ![Okno dialogowe modelu pociągu](media/how-to/how-to-train-model-2.png)

6.  Translator niestandardowy prześle szkolenie i wyświetli stan szkolenia na karcie modele.

    ![Strona modelu pociągu](media/how-to/how-to-train-model-3.png)

>[!Note]
>Usługa Custom Translator obsługuje 10 równoczesnych szkoleń w obszarze roboczym w dowolnym momencie.


## <a name="edit-a-model"></a>Edytowanie modelu

Model można edytować za pomocą łącza Edytuj na stronie Szczegóły modelu.

1.  Kliknij ikonę Ołówek.

    ![Edytuj model](media/how-to/how-to-edit-model.png)

2.  W oknie dialogowym

    1.  Nazwa modelu (wymagane): Nadaj modelowi znaczącą nazwę.

        ![Okno dialogowe Edytowanie kolejnych](media/how-to/how-to-edit-model-dialog.png)

3.  Kliknij pozycję Zapisz.


## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak wyświetlić szczegóły modelu](how-to-view-model-details.md).
