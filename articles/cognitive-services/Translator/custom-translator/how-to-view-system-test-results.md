---
title: Wyświetlanie wyników testów systemu i wdrażanie — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Po pomyślnym szkolenia, przejrzyj testy systemu, aby analizować wyniki szkolenia. Jeśli jesteś zadowolony z wyników szkolenia, umieść żądanie wdrożenia trenowanego modelu.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 61d3869559d88e14c0b9a3c3e23cd8a1f9c2b6c4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627648"
---
# <a name="view-system-test-results"></a>Wyświetlanie wyników testu systemu

Po pomyślnym szkolenia, przejrzyj testy systemu, aby analizować wyniki szkolenia. Jeśli jesteś zadowolony z wyników szkolenia, umieść żądanie wdrożenia trenowanego modelu. 

## <a name="system-test-results-page"></a>Strona wyników testu systemu

Wybierz projekt, a następnie wybierz kartę modeli tego projektu, zlokalizuj modelu, którego chcesz użyć, a na koniec wybierz karty testu.

Karta testu dowiesz się:

1.  Wyniki testu systemu: Wynik procesu testu w szkoleniach. Proces testowego daje wynik BELEU.

    **Liczba zdanie:** liczbę równoległych zdania były używane w zestawie testów.

     **Wynik BELEU:** wynik BELEU wygenerowany dla modelu, po zakończeniu szkolenia.

    **Stan:** wskazuje, czy zakończeniu testu w toku.

    ![Wyniki testu systemu](media/how-to/how-to-system-test-results.png)

2.  Kliknij na temat wyników testów systemu, a który nastąpi przekierowanie do strony szczegółów wyników testu. Ta strona zawiera tłumaczenia maszynowego zdań, które były częścią zestawu danych testowych.

3.  Tabela na stronie szczegółów wyników testu ma dwie kolumny — jeden dla każdego z języków w pary. W kolumnie dla języka źródłowego widoczne zdanie, które ma zostać poddany translacji. Kolumna, dla języka docelowego zawiera dwa zdania w każdym wierszu.

    **Odnośnik:** to pole następujące zdanie jest tłumaczenia dokumentacja zdania źródłowy zgodnie z zestawu danych testowych.

    **MT:** to pole następujące zdanie jest automatyczne tłumaczenie zdania źródło wykonywane przez model tworzony po przeprowadzono szkolenia.

    ![Porównaj wyniki testu systemu](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Pobierz test

Kliknij link Pobierz tłumaczeń, aby pobrać plik zip. Plik zip zawiera tłumaczenia maszynowe zdań źródłowego zestawu danych testowych.

![Pobierz test](media/how-to/how-to-system-test-download.png)

To archiwum zip pobranego zawiera trzy pliki.

1.  Custom.MT.txt: ten plik zawiera tłumaczenia maszynowe zdań język źródłowy w języku docelowym, wykonywane przez modelu wyszkolonego przy danych użytkownika.

2.  REF.txt: ten plik zawiera tłumaczenia podane przez użytkownika języka źródłowego zdań w języku docelowym.

3.  Source.txt: ten plik zawiera zdań w języku źródła.

    ![System pobranych wyników testu](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Wdrażanie modelu

Żądanie wdrożenia:

1.  Wybierz projekt, przejdź na kartę w modele.

2. Pomyślnie nauczonego modelu należy go przedstawia przycisk "Deploy", jeśli nie wdrożone.

    ![Wdrażanie modelu](media/how-to/how-to-deploy-model.png)

3.  Kliknij pozycję wdrożenia.
4.  Wybierz regiony, w której chcesz modelu do wdrożenia, a następnie kliknij przycisk Zapisz. Możesz wybrać wiele regionów.

    ![Wdrażanie modelu](media/how-to/how-to-deploy-model-regions.png)

5.  W kolumnie "Status", możesz wyświetlić stan modelu.

>[!Note]
>Jeśli model jest już wdrożone, zobaczysz przycisk "Undeploy" dla tego modelu. Aby wdrożyć modelu, kliknij przycisk "Undeploy".

## <a name="next-steps"></a>Kolejne kroki

- Rozpoczynanie korzystania z modelu tłumaczenia niestandardowych wdrożone za pośrednictwem [V3 interfejs API tekstu usługi Translator firmy Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Dowiedz się, [sposób zarządzania ustawieniami](how-to-manage-settings.md) udostępniania obszaru roboczego, zarządzanie klucz subskrypcji.
- Dowiedz się, [jak przeprowadzić migrację do swojego obszaru roboczego i projektu](how-to-migrate.md) z [Centrum w usłudze Translator firmy Microsoft](https://hub.microsofttranslator.com)