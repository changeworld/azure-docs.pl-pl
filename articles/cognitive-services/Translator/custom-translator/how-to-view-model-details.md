---
title: Wyświetl szczegóły modelu — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Karta modeli w dowolnym projekcie zawiera szczegóły każdego modelu, takich jak nazwa modelu, stan modelu BELEU oceny, szkolenia, dostosowywania, testowanie liczba zdania.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 80ee99fc4490f1fdc591093f357c8db55a97abb9
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627651"
---
# <a name="view-model-details"></a>Wyświetl szczegóły modelu

Karta modeli w ramach projektu pokazuje wszystkie modele, w tym projekcie. Wszystkie modele uczony, dla których projekt znajduje się na tej karcie.

Dla każdego modelu w projekcie są wyświetlane następujące dane modelu.

1.  Nazwa modelu: Pokazuje nazwę modelu danego modelu.

2.  Stan: Wyświetla stan podanego modelu. Szkolenia nowych będzie posiadał status przesłane, dopóki nie zostanie zaakceptowany. Stan zmieni się do przetwarzania danych, gdy usługa oblicza zawartości dokumentów. Po zakończeniu oceny dokumentów stan zmieni się na uruchomiony i będzie można znaleźć liczbę zdań, które są częścią szkolenia, w tym dostosowywania i testowania zestawów, które są tworzone automatycznie. Poniżej znajduje się lista stan modelu, który opisuje stan modeli.

    -  Przesłano: Określa, że wewnętrznej bazy danych przetwarza dokumenty dla tego modelu.

    -  TrainingQueued: Określa, że szkolenie jest dodawane do kolejki MT systemu dla tego modelu.

    -  Uruchamianie: Określa, czy szkolenia jest uruchomiona w systemie MT dla tego modelu.

    -  Powodzenie: Określa, że szkolenie zakończyło się pomyślnie w systemie MT i model jest dostępna. W ten stan wynik BELEU jest wyświetlany dla tego modelu.

    -  Wdrożone: Określa, że pomyślnie uczony model jest przesyłany do usługi system MT na potrzeby wdrażania.

    -  Aby można: Określa, że wdrożony model jest można.

    -  Wycofane: Określa, że proces wycofania wdrożenia modelu została zakończona pomyślnie.

    -  Szkolenie nie powiodła się: Określa, że szkolenie nie powiodło się. Jeśli wystąpi błąd szkolenia, ponów próbę wykonania zadania szkolenia. Jeśli błąd będzie się powtarzać, skontaktuj się z nami. Nie należy usuwać modelu nie powiodło się.

    - DataProcessingFailed: Określa, czy przetwarzanie danych nie powiodło się dla jednego lub więcej dokumentów należących do modelu.

    - Niepowodzenia wdrożenia: Określa, że wdrożenie modelu zakończyła się niepowodzeniem.

    - MigratedDraft: Określa, czy model jest w stanie projektu, po przeprowadzeniu migracji z centrum niestandardowe w usłudze Translator.

4.  Wynik BELEU: pokazuje wynik BELEU (Understudy dwujęzyczny oceny) modelu określający jakość tłumaczenia systemu. Ten wynik informuje, jak blisko tłumaczenia wykonywane przez system tłumaczeń wynikające z to szkolenie dopasowania zdań odwołanie do zestawu danych testowych. Wynik BELEU jest wyświetlana, gdy szkolenie zostało pomyślnie ukończone. Jeśli szkolenia nie jest pełną / nie powiodło się, nie zobaczysz żadnych wynik BELEU.

5.  Szkolenie liczba zdanie: pokazuje całkowitą liczbę zdań używany jako zestaw szkoleniowy.

6.  Dostosowywanie liczby zdanie: przedstawia sumę zdania używany jako dostosowywania zestawu.

7.  Szkolenie liczba zdanie: przedstawia sumę zdania używany jako zestaw testów.

8.  Mono liczba zdanie: przedstawia sumę zdania używany jako zestaw mono.

9.  Przycisk akcji wdrażania: pomyślnie uczonego modelu pokazuje "Wdróż" przycisk Jeśli nie zostanie wdrożony. Jeśli model jest wdrażany, jest wyświetlany przycisk "Undeploy".

10. Usuwanie: Można użyć ten przycisk, aby usunąć model. Usuwanie modelu nie usuwa wszelkie dokumenty, użyty do utworzenia tego modelu.

    ![Wyświetl szczegóły modelu](media/how-to/how-to-view-model-details.png)

>[!Note]
>Do porównywania kolejnych szkoleniach dla tych samych systemach, warto zachować Ustaw dostosowywania i testowania ustawianie stałej.

## <a name="view-model-training-details"></a>Wyświetl szczegóły szkolenie modelu

Po zakończeniu szkolenia można przejrzeć szczegółowe informacje o szkolenia na stronie szczegółów. Wybierz projekt, zlokalizuj i wybierz kartę w modele i wybierz model.

Na stronie modelu ma dwie karty: szczegóły szkolenia i testowania.

1.  **Szkolenie szczegóły:** ta karta przedstawia listę dokumentów używanych w szkolenia:

    -  Nazwa dokumentów: To pole zawiera nazwę dokumentu

    -  Typ dokumentu: To pole wskazuje, czy ten dokument jest równoległe / mono.

    -  Jak w zdaniu count w źródłowy języka: pole ten pokazuje liczbę zdań są dostępne jako część języka źródłowego.

    -  Jak w zdaniu liczby w języku docelowym: pole ten pokazuje liczbę zdań są dostępne jako część języka docelowego.

    -  Zdania wyrównany: To pole, które pokazuje liczbę zdań ma zostały dostosowane przez niestandardowe w usłudze Translator podczas dostosowanie procesu.

    -  Używane zdania: Ten pokazuje liczbę zdań była używana przez niestandardowe w usłudze Translator w trakcie tego szkolenia pola.

    ![Szczegóły szkolenie modelu](media/how-to/how-to-model-training-details.png)

2.  **Testowanie:** ta karta przedstawia szczegóły testu dla pomyślnego szkolenia.

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [wyniki testu](how-to-view-system-test-results.md) i przeanalizować wyniki szkolenia.
