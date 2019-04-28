---
title: Wyświetl szczegóły modelu — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Karta modeli w dowolnym projekcie zawiera szczegóły każdego modelu, takich jak nazwa modelu, stan modelu BELEU oceny, szkolenia, dostosowywania, testowanie liczba zdania.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 13f3d88ad69d2acc64b9a6469415eceaf22fa491
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456462"
---
# <a name="view-model-details"></a>Wyświetlanie szczegółów modelu

Karta modeli w ramach projektu pokazuje wszystkie modele, w tym projekcie. Wszystkie modele uczony, dla których projekt znajduje się na tej karcie.

Dla każdego modelu w projekcie te informacje są wyświetlane.

1.  Nazwa modelu: Wyświetlana nazwa modelu danego modelu.

2.  Stan: Wyświetla stan podanego modelu. Szkolenia nowych będzie posiadał status przesłane, dopóki nie zostanie zaakceptowany. Stan zmieni się do przetwarzania danych, gdy usługa oblicza zawartości dokumentów. Po zakończeniu oceny dokumentów stan zmieni się na uruchomiony i będzie można znaleźć liczbę zdań, które są częścią szkolenia, w tym dostosowywania i testowania zestawów, które są tworzone automatycznie. Poniżej znajduje się lista stan modelu, który opisuje stan modeli.

    -  Przesłano: Określa, że wewnętrznej bazy danych przetwarza dokumenty dla tego modelu.

    -  TrainingQueued: Określa, że szkolenie jest dodawane do kolejki MT systemu dla tego modelu.

    -  Uruchomione: Określa, że szkolenie działa w systemie MT dla tego modelu.

    -  Powodzenie: Określa, że szkolenie zakończyło się pomyślnie w systemie MT, jak i model jest dostępna. W ten stan wynik BELEU jest wyświetlany dla tego modelu.

    -  Wdrożone: Określa, że pomyślnie uczony model jest przesyłany do usługi system MT na potrzeby wdrażania.

    -  Aby można: Określa, że można jest wdrożony model.

    -  Cofnięto jego wdrożenie: Określa, że proces wycofania wdrożenia modelu została zakończona pomyślnie.

    -  Szkolenie nie powiodło się: Określa, że szkolenie nie powiodło się. Jeśli wystąpi błąd szkolenia, ponów próbę wykonania zadania szkolenia. Jeśli błąd będzie się powtarzać, skontaktuj się z nami. Nie należy usuwać modelu nie powiodło się.

    - DataProcessingFailed: Określa, czy przetwarzanie danych nie powiodło się dla jednego lub więcej dokumentów należących do modelu.

    - Niepowodzenia wdrożenia: Określa, że wdrożenie modelu zakończyła się niepowodzeniem.

    - MigratedDraft: Określa, czy model jest w stanie projektu, po przeprowadzeniu migracji z centrum niestandardowe w usłudze Translator.

4.  Wynik BELEU: pokazuje wynik BELEU (Understudy dwujęzyczny oceny) modelu określający jakość tłumaczenia systemu. Ten wynik informuje, jak blisko tłumaczenia wykonywane przez system tłumaczeń wynikające z to szkolenie dopasowania zdań odwołanie do zestawu danych testowych. Wynik BELEU jest wyświetlana, gdy szkolenie zostało pomyślnie ukończone. Jeśli szkolenia nie jest pełną / nie powiodło się, nie zobaczysz żadnych wynik BELEU.

5.  Liczba zdania szkolenia: Przedstawia sumę zdania używany jako zestaw szkoleniowy.

6.  Dostosowywanie liczby zdanie: Pokazuje całkowitą liczbę zdań używane jako dostosowywania zestawu.

7.  Liczba zdania szkolenia: Pokazuje sumę zdania używany jako zestaw testów.

8.  Mono liczba zdanie: Pokazuje łączna liczba zdania używany jako zestaw mono.

9.  Wdróż przycisk akcji: Pomyślnie nauczonego modelu należy go przedstawia przycisk "Deploy" w przeciwnym razie wdrożone. Jeśli model jest wdrażany, jest wyświetlany przycisk "Undeploy".

10. Usuwanie: Możesz użyć tego przycisku, jeśli chcesz usunąć model. Usuwanie modelu nie usuwa wszelkie dokumenty, użyty do utworzenia tego modelu.

    ![Wyświetlanie szczegółów modelu](media/how-to/how-to-view-model-details.png)

>[!Note]
>Do porównywania kolejnych szkoleniach dla tych samych systemach, warto zachować Ustaw dostosowywania i testowania ustawianie stałej.

## <a name="view-model-training-details"></a>Wyświetl szczegóły szkolenie modelu

Po zakończeniu szkolenia można przejrzeć szczegółowe informacje o szkolenia na stronie szczegółów. Wybierz projekt, zlokalizuj i wybierz kartę w modele i wybierz model.

Na stronie modelu ma dwie karty: Szczegóły szkolenia i testowania.

1.  **Szczegóły szkolenia:** Ta karta przedstawia listę dokumentów używanych w szkolenia:

    -  Nazwa dokumentów: To pole zawiera nazwę dokumentu

    -  Typ dokumentu: To pole wskazuje, czy ten dokument jest równoległe / mono.

    -  Liczba zdanie w języku źródła: Pole to pokazuje liczbę zdań są dostępne jako część języka źródłowego.

    -  Liczba zdanie w języku docelowym: Pole to pokazuje liczbę zdań są dostępne jako część języka docelowego.

    -  Zdania wyrównany: To pole, które pokazuje liczbę zdań ma zostały dostosowane przez niestandardowe w usłudze Translator podczas dostosowanie procesu.

    -  Używane zdania: To pole, które pokazuje liczbę zdań była używana przez niestandardowe w usłudze Translator w trakcie tego szkolenia.

    ![Szczegóły szkolenie modelu](media/how-to/how-to-model-training-details.png)

2.  **Test:** Ta karta przedstawia szczegóły testu dla pomyślnego szkolenia.

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [wyniki testu](how-to-view-system-test-results.md) i przeanalizować wyniki szkolenia.
