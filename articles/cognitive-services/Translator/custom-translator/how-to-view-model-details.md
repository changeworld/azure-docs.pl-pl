---
title: Wyświetlanie szczegółów modelu — tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: Karta Modele w dowolnym projekcie zawiera szczegóły każdego modelu, takie jak nazwa modelu, stan modelu, wynik BLEU, szkolenie, dostrajanie, testowanie liczby zdań.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595567"
---
# <a name="view-model-details"></a>Wyświetlanie szczegółów modelu

Modelowanie kartę w obszarze projekt pokazuje wszystkie modele w tym projekcie. Wszystkie modele przeszkolone dla tego projektu jest wymieniony w tej karcie.

Dla każdego modelu w projekcie te szczegóły są wyświetlane.

1.  Nazwa modelu: Pokazuje nazwę modelu danego modelu.

2.  Stan: Pokazuje stan danego modelu. Twoje nowe szkolenie będzie miało status Przesłane, dopóki nie zostanie zaakceptowane. Stan zmieni się na Przetwarzanie danych, podczas gdy usługa ocenia zawartość dokumentów. Po zakończeniu oceny dokumentów stan zmieni się na Uruchomiony i będzie można zobaczyć liczbę zdań, które są częścią szkolenia, w tym strojenie i testowanie zestawów, które są tworzone dla Ciebie automatycznie. Poniżej znajduje się lista stanu modelu, który opisuje stan modeli.

    -  Przesłane: Określa, że wewnętrznej bazy danych jest przetwarzanie dokumentów dla tego modelu.

    -  TrainingQueued: Określa, że szkolenie jest w kolejce do systemu MT dla tego modelu.

    -  Uruchomione: Określa, że szkolenie jest uruchomione w systemie MT dla tego modelu.

    -  Powiodło się: Określa, że szkolenie zakończyło się pomyślnie w systemie MT i model jest dostępny. W tym stanie wynik BLEU jest wyświetlany dla tego modelu.

    -  Wdrożony: Określa, że pomyślny model szkolony jest przesyłany do systemu MT w celu wdrożenia.

    -  Undeploying: Określa, że wdrożony model jest undeploying.

    -  Nieskładkowane: Określa, że proces cofania dostępu modelu został zakończony pomyślnie.

    -  Szkolenie nie powiodło się: Określa, że szkolenie nie powiodło się. Jeśli wystąpi niepowodzenie szkolenia, ponów próbę zadania szkoleniowego. Jeśli błąd będzie się powtarzał, skontaktuj się z nami. Nie usuwaj modelu, który uległ awarii.

    - DataProcessingFailed: Określa, że przetwarzanie danych nie powiodło się dla jednego lub więcej dokumentów należących do modelu.

    - DeploymentFailed: Określa, że wdrożenie modelu nie powiodło się.

    - MigratedDraft: Określa, że model jest w stanie roboczym po migracji z centrum do usługi Translator niestandardowy.

4.  Wynik BLEU: pokazuje wynik BLEU (Dwujęzyczna Ocena Understudy) modelu, wskazując jakość systemu tłumaczenia. Ten wynik informuje, jak ściśle tłumaczenia wykonane przez system tłumaczenia wynikające z tego szkolenia odpowiadają zdaniach referencyjnych w zestawie danych testowych. Wynik BLEU pojawia się, jeśli szkolenie zostanie pomyślnie zakończone. Jeśli szkolenie nie zostanie zakończone/ nie powiodło się, nie zobaczysz żadnego wyniku BLEU.

5.  Liczba zdań szkolenia: pokazuje całkowitą liczbę zdań używanych jako zestaw szkoleniowy.

6.  Liczba zdań dostrajania: pokazuje całkowitą liczbę zdań używanych jako zestaw strojenia.

7.  Liczba zdań szkoleniowych: pokazuje całkowitą liczbę zdań używanych jako zestaw testowy.

8.  Liczba zdań mono: pokazuje całkowitą liczbę zdań używanych jako zestaw mono.

9.  Przycisk akcji Wdrażanie: W przypadku pomyślnie uczonego modelu jest wyświetlany przycisk "Wdrażanie", jeśli nie zostanie wdrożony. Jeśli model jest wdrożony, wyświetlany jest przycisk "Undeploy".

10. Usuń: Możesz użyć tego przycisku, jeśli chcesz usunąć model. Usunięcie modelu nie spowoduje usunięcia żadnych dokumentów użytych do utworzenia tego modelu.

    ![Wyświetlanie szczegółów modelu](media/how-to/how-to-view-model-details.png)

>[!Note]
>Aby porównać kolejne szkolenia dla tych samych systemów, ważne jest, aby zachować stały zestaw strojenia i zestaw testów.

## <a name="view-model-training-details"></a>Wyświetlanie szczegółów szkolenia modelu

Po zakończeniu szkolenia można przejrzeć szczegóły dotyczące szkolenia na stronie szczegółów. Wybierz projekt, znajdź i wybierz kartę modele oraz wybierz model.

Strona modelu ma dwie karty: szczegóły szkolenia i test.

1.  **Szczegóły szkolenia:** Ta karta zawiera listę dokumentów używanych w szkoleniu:

    -  Nazwa dokumentu: to pole zawiera nazwę dokumentu

    -  Typ dokumentu: To pole pokazuje, czy ten dokument jest równoległy/ mono.

    -  Liczba zdań w języku źródłowym: to pole pokazuje liczbę zdań jako część języka źródłowego.

    -  Liczba zdań w języku docelowym: to pole pokazuje liczbę zdań jako część języka docelowego.

    -  Wyrównane zdania: To pole zawiera liczbę zdań wyrównanych przez tłumacza niestandardowego podczas procesu wyrównywania.

    -  Używane zdania: To pole zawiera liczbę zdań, które zostały użyte przez tłumacza niestandardowego podczas tego szkolenia.

    ![Szczegóły szkolenia modelu](media/how-to/how-to-model-training-details.png)

2.  **Badanie:** Ta karta zawiera szczegóły testu dla pomyślnego szkolenia.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wyniki testów](how-to-view-system-test-results.md) i analizuj wyniki treningu.
