---
title: Pobierz klucze subskrypcji usługi mowy niestandardowe na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać subskrypcji kluczy dla wywołania usługi mowy niestandardowe w usługach kognitywnych.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fcef86a19a77581ff82b64173e2ac68b26ae708a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347053"
---
# <a name="obtain-subscription-keys"></a>Uzyskiwanie kluczy subskrypcji
Aby rozpocząć korzystanie z usługi Azure niestandardowe mowy, należy najpierw połączyć konto użytkownika do subskrypcji platformy Azure. Dostępne są subskrypcje warstwami bezpłatnymi i płatnymi. Informacje dotyczące warstw, zobacz [cennikiem](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Pobierz klucz subskrypcji
1. Klucz subskrypcji można pobrać z portalu Azure w jeden z dwóch sposobów:

    * Przejdź do [portalu Azure](https://ms.portal.azure.com), i Dodaj nowy interfejs API usług kognitywnych wyszukując _kognitywnych usług_ , a następnie wybierając **kognitywnych interfejsów API usługi**.

      ![Kognitywnych usługi wyszukiwania](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Lub przejść bezpośrednio do [kognitywnych interfejsów API usługi](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![Interfejsy API usług Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
2. Wypełnij następujące wymagane pola:

      a. **Nazwa konta**. Użyj nazwy, która odpowiada potrzebom użytkownika. Ta nazwa należy pamiętać, aby subskrypcji kognitywnych usług można znaleźć na liście zasobów.

      b. **Subskrypcja**. Wybierz jedno z Twoich subskrypcji platformy Azure.

      c. **Typ interfejsu API**. Wybierz **mowy niestandardowe usługi (wersja zapoznawcza)**.

      d. **Lokalizacja**. Jest ona obecnie **zachodnie stany USA**.

      e. **Warstwa cenowa**. Wybierz warstwę, która odpowiada potrzebom użytkownika. **F0** jest warstwę bezpłatna. Omówiono przydziałów, które są dozwolone na [cennikiem](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Tworzenie konta usługi kognitywnych](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

3. Widok pulpitu nawigacyjnego albo usługi o nazwie podane konto powinien znajdować się na liście zasobów. Gdy zostanie ona wybrana, można zapoznać się z omówieniem usługi. Na liście z lewej strony w obszarze **zarządzanie zasobami**, wybierz pozycję **klucze**. Kopiuj **klucza 1**.

      Ten klucz subskrypcji jest wymagany w następnych krokach.

      ![KLUCZ 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Nie należy kopiować **identyfikator subskrypcji** na stronie Przegląd. Należy klucza subskrypcji w następnym kroku.
      >

      ![Identyfikator subskrypcji — omówienie](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

4. Aby wprowadzić klucz subskrypcji, na Wstążce w prawym górnym rogu wybierz konto użytkownika. Z menu rozwijanego wybierz **subskrypcje**.

      ![Element menu subskrypcji](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Zostanie wyświetlona tabela subskrypcji, która jest pusta zostanie otwarty po raz pierwszy.

    ![Tabela subskrypcji](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

5. Wybierz **Dodaj nowe**. Wprowadź nazwę dla subskrypcji i klucz subskrypcji. Może być albo **klucz 1** (klucza podstawowego) lub **2 klucza** (klucz pomocniczy) z subskrypcji.

      ![Nazwa klucza subskrypcji](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Aby przekazać dane, nauczenia modelu, lub wdrożenie, musisz połączyć działań niestandardowych mowy usługi z subskrypcją platformy Azure. Może być warstwę bezpłatna lub subskrypcji płatnej warstwy. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-id"></a>Pobierz identyfikator subskrypcji
Aby uzyskać identyfikator subskrypcji, przejdź do portalu Azure. Wyszukaj *kognitywnych usług* i *niestandardowej usługi rozpoznawania mowy*i postępuj zgodnie z instrukcjami.

> [!NOTE]
> W dalszej części tego procesu jest wymagany klucz subskrypcji.
>

## <a name="next-steps"></a>Kolejne kroki
* Do tworzenia Twojej [niestandardowych modelu akustycznego](cognitive-services-custom-speech-create-acoustic-model.md).
* Do tworzenia Twojej [model niestandardowych języka](cognitive-services-custom-speech-create-language-model.md).
