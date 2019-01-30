---
title: Uzyskiwanie kluczy subskrypcji usługę Custom Speech Service na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można pobrać kluczy subskrypcji dla wywołań Custom Speech Service w usługach Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 74e4b9bb9a8baa6a28f1504c1e5a0e01e9ca56cd
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210102"
---
# <a name="obtain-subscription-keys"></a>Uzyskiwanie kluczy subskrypcji

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Aby rozpocząć pracę, przy użyciu usługi Azure Custom Speech Service, należy najpierw połączyć konto użytkownika z subskrypcją platformy Azure. Dostępnych subskrypcji bezpłatnych i płatnych warstw. Aby uzyskać informacje na temat warstw, zobacz [stronę z cennikiem](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Pobierz klucz subskrypcji
1. Klucz subskrypcji można uzyskać w witrynie Azure portal w jeden z dwóch sposobów:

    * Przejdź do witryny [Azure Portal](https://ms.portal.azure.com) i dodaj nowy interfejs API usług Cognitive Services, wyszukując _Cognitive Services_ , a następnie wybierając pozycję **Interfejsy API usług Cognitive Services**.

      ![Wyszukiwanie poznawcze](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Lub przejdź bezpośrednio do [interfejsy API usług Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![Interfejsy API usług Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. Wypełnij następujące pola wymagane:

      a. **Nazwa konta**. Użyj nazwy, która Ci odpowiada. Należy pamiętać tę nazwę, aby subskrypcji usług Cognitive Services można znaleźć na liście zasobów.

      b. **Subskrypcja**. Wybierz jedną z Twoich subskrypcji platformy Azure.

      c. **Typ interfejsu API**. Wybierz **usługa Custom Speech (wersja zapoznawcza)**.

      d. **Lokalizacja**. Jest ona obecnie **zachodnie stany USA**.

      e. **Warstwa cenowa**. Wybierz warstwę która Ci odpowiada. **F0** jest bezpłatna. Przydziały, które są dozwolone są wyjaśnione w [stronę z cennikiem](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Tworzenie konta usługi cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. Widok na pulpicie nawigacyjnym albo usługa o nazwie podane konto powinien znajdować się na liście zasobów. Gdy zostanie ona wybrana, zostanie wyświetlony przegląd usługi. Na liście z lewej strony w obszarze **zarządzania zasobami**, wybierz opcję **klucze**. Kopiuj **klucz 1**.

      Ten klucz subskrypcji jest wymagany w następnych krokach.

      ![KLUCZ 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Nie Kopiuj **identyfikator subskrypcji** na stronie Przegląd. Wymagany jest klucz subskrypcji w następnym kroku.
      >

      ![Identyfikator subskrypcji — omówienie](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. Aby wprowadzić klucz subskrypcji na Wstążce w prawym górnym rogu, wybierz konto użytkownika. Z menu rozwijanego wybierz **subskrypcje**.

      ![Element menu subskrypcji](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Zostanie wyświetlona tabela subskrypcji, który jest pusty po raz pierwszy zostanie on otwarty.

    ![Tabela subskrypcji](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. Wybierz **Dodaj nowe**. Wprowadź nazwę subskrypcji i klucza subskrypcji. Może być albo **klucz 1** (klucza podstawowego) lub **klucz 2** (klucz pomocniczy) ze swojej subskrypcji.

      ![Nazwa klucza subskrypcji](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Do przekazywania danych, uczenia modelu lub wdrożenie, musisz połączyć swoje działania Custom Speech Service z subskrypcją platformy Azure. Można go w warstwie bezpłatna lub warstwy płatnej subskrypcji. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-id"></a>Pobierz identyfikator subskrypcji
Aby uzyskać identyfikator subskrypcji, przejdź do witryny Azure portal. Wyszukaj *usług Cognitive Services* i *Custom Speech Service*i postępuj zgodnie z instrukcjami.

> [!NOTE]
> Klucz subskrypcji jest wymagana w dalszej części tego procesu.
>

## <a name="next-steps"></a>Kolejne kroki
* Rozpocznij tworzenie swojej [niestandardowy model akustyczny](cognitive-services-custom-speech-create-acoustic-model.md).
* Rozpocznij tworzenie swojej [model języka niestandardowego](cognitive-services-custom-speech-create-language-model.md).
