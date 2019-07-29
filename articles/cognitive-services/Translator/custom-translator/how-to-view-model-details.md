---
title: Wyświetlanie szczegółów modelu — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Karta modele w obszarze dowolnego projektu pokazuje szczegóły każdego modelu, takie jak nazwa modelu, stan modelu, BLEU oceny, uczenie, dostrajanie, liczba zdań testowania.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595567"
---
# <a name="view-model-details"></a>Wyświetlanie szczegółów modelu

Karta modele w obszarze projekt zawiera wszystkie modele w tym projekcie. Na tej karcie są wyświetlane wszystkie modele przeszkolone dla tego projektu.

Dla każdego modelu w projekcie są wyświetlane te szczegóły.

1.  Nazwa modelu: Pokazuje nazwę modelu danego modelu.

2.  Stan: Pokazuje stan danego modelu. Twoje nowe szkolenie będzie miało status przesłany do momentu jego zaakceptowania. Stan zmieni się na przetwarzanie danych, podczas gdy usługa szacuje zawartość dokumentów. Po zakończeniu oceny dokumentów stan zmieni się na uruchomiony i będzie można zobaczyć liczbę zdań, które są częścią szkolenia, w tym zestawy dostrajania i testowania, które są tworzone automatycznie. Poniżej znajduje się lista stan modelu opisująca stan modeli.

    -  Złożona Określa, czy zaplecze przetwarza dokumenty dla tego modelu.

    -  TrainingQueued: Określa, że szkolenie jest umieszczane w kolejce do MT systemu dla tego modelu.

    -  Uruchomione: Określa, że szkolenie jest uruchomione w systemie MT dla tego modelu.

    -  Powiodło się Określa, że szkolenie powiodło się w systemie MT i jest dostępny model. W tym stanie zostanie wyświetlony wynik BLEU dla tego modelu.

    -  Szczebl Określa, że pomyślnie szkolony model jest przesyłany do komputera MT w celu wdrożenia.

    -  Cofnięto wdrożenie: Określa, że wdrożony model zostaje cofnięty.

    -  Niewdrażanego Określa, że proces rozmieszczenia został ukończony pomyślnie.

    -  Szkolenie nie powiodło się: Określa, że szkolenie nie powiodło się. Jeśli wystąpi awaria szkoleniowa, spróbuj ponownie wykonać zadanie szkoleniowe. Jeśli błąd będzie się powtarzać, skontaktuj się z nami. Nie usuwaj modelu, który uległ awarii.

    - DataProcessingFailed: Określa, że przetwarzanie danych nie powiodło się dla co najmniej jednego dokumentu należącego do modelu.

    - DeploymentFailed: Określa, że wdrożenie modelu nie powiodło się.

    - MigratedDraft: Określa, że model jest w stanie wersji roboczej po migracji z centrum do translatora niestandardowego.

4.  BLEU Score: pokazuje wynik BLEU (analiza dwujęzyczna) dla modelu, wskazujący jakość systemu tłumaczenia. Ten wynik zawiera informacje o tym, jak ściśle tłumaczenia wykonywane przez system tłumaczenia wynikający z tego szkolenia pasują do zdań odniesienia w zestawie danych testowych. Wynik BLEU pojawia się, jeśli szkolenie zostało pomyślnie zakończone. Jeśli szkolenie nie zostało ukończone/nie zakończyło się niepowodzeniem, nie zobaczysz żadnego wyniku BLEU.

5.  Liczba zdań szkoleniowych: Pokazuje łączną liczbę zdań użytych jako zestaw szkoleniowy.

6.  Liczba zdań strojenia: Pokazuje łączną liczbę zdań używanych jako zestaw dostrajania.

7.  Liczba zdań szkoleniowych: Pokazuje łączną liczbę zdań użytych jako zestaw testowy.

8.  Liczba zdań mono: Pokazuje łączną liczbę zdań użytych jako zestaw mono.

9.  Przycisk wdrożenia akcji: W przypadku modelu, który został pomyślnie przeszkolony, wyświetla przycisk "wdróż", jeśli nie został wdrożony. Jeśli model został wdrożony, wyświetlany jest przycisk "Rozmieść".

10. Usunięty Jeśli chcesz usunąć model, możesz użyć tego przycisku. Usunięcie modelu nie spowoduje usunięcia żadnego z dokumentów użytych do utworzenia tego modelu.

    ![Wyświetlanie szczegółów modelu](media/how-to/how-to-view-model-details.png)

>[!Note]
>Aby porównać kolejne szkolenia dla tych samych systemów, ważne jest, aby zachować zestaw strojenia i stały zestaw testów.

## <a name="view-model-training-details"></a>Wyświetl szczegóły szkolenia modelu

Po zakończeniu szkolenia możesz zapoznać się ze szczegółowymi informacjami na temat szkolenia ze strony szczegółów. Wybierz projekt, Znajdź i wybierz kartę modele i wybierz model.

Strona modelu ma dwie karty: Szczegóły i testowanie szkolenia.

1.  **Szczegóły szkolenia:** Ta karta zawiera listę dokumentów użytych w szkoleniu:

    -  Nazwa dokumentów: To pole zawiera nazwę dokumentu

    -  Typ dokumentu: To pole wskazuje, czy ten dokument jest równoległy/mono.

    -  Liczba zdań w języku źródłowym: To pole pokazuje liczbę zdań w postaci części języka źródłowego.

    -  Liczba zdań w języku docelowym: W tym polu jest wyświetlana liczba zdań w języku docelowym.

    -  Wyrównane zdania: To pole pokazuje liczbę zdań wyrównanych przez translatora niestandardowego podczas procesu wyrównania.

    -  Używane zdania: W tym polu jest wyświetlana liczba zdań używanych przez translatora niestandardowego podczas tego szkolenia.

    ![Szczegóły szkolenia modelu](media/how-to/how-to-model-training-details.png)

2.  **Badan** Na tej karcie są wyświetlane szczegóły testu dotyczące pomyślnego szkolenia.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wyniki testów](how-to-view-system-test-results.md) i Przeanalizuj wyniki szkolenia.
