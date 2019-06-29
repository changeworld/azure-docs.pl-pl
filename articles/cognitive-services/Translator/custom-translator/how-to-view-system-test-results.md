---
title: Wyświetlanie wyników testów systemu i wdrażanie — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Po pomyślnym szkolenia, przejrzyj testy systemu, aby analizować wyniki szkolenia. Jeśli jesteś zadowolony z wyników szkolenia, umieść żądanie wdrożenia trenowanego modelu.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: ec15851ae7ff59a752fbf0d823d87aa6e68f10e9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442478"
---
# <a name="view-system-test-results"></a>Wyświetlanie wyników testu systemu

Po pomyślnym szkolenia, przejrzyj testy systemu, aby analizować wyniki szkolenia. Jeśli jesteś zadowolony z wyników szkolenia, umieść żądanie wdrożenia trenowanego modelu.

## <a name="system-test-results-page"></a>Strona wyników testu systemu

Wybierz projekt, a następnie wybierz kartę modeli tego projektu, zlokalizuj modelu, którego chcesz użyć, a na koniec wybierz karty testu.

Karta testu dowiesz się:

1.  **Wyniki testu systemu:** Wynik procesu testu w szkoleniach. Proces testowego daje wynik BELEU.

    **Liczba zdanie:** Jak wiele równoległych zdania były używane w zestawie testów.

     **Wynik BELEU:** Wynik BELEU wygenerowany dla modelu, po zakończeniu szkolenia.

    **Stan:** Wskazuje, czy zakończeniu testu w toku.

    ![Wyniki testu systemu](media/how-to/how-to-system-test-results.png)

2.  Kliknij na temat wyników testów systemu, a który nastąpi przekierowanie do strony szczegółów wyników testu. Ta strona zawiera tłumaczenia maszynowego zdań, które były częścią zestawu danych testowych.

3.  Tabela na stronie szczegółów wyników testu ma dwie kolumny — jeden dla każdego z języków w pary. W kolumnie dla języka źródłowego widoczne zdanie, które ma zostać poddany translacji. Kolumna, dla języka docelowego zawiera dwa zdania w każdym wierszu.

    **Odnośnik:** To pole następujące zdanie jest tłumaczenia dokumentacja zdania źródłowy zgodnie z zestawu danych testowych.

    **MT:** To pole następujące zdanie jest automatyczne tłumaczenie zdania źródło wykonywane przez model tworzony po przeprowadzono szkolenia.

    ![Porównaj wyniki testu systemu](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Pobierz test

Kliknij link Pobierz tłumaczeń, aby pobrać plik zip. Plik zip zawiera tłumaczenia maszynowe zdań źródłowego zestawu danych testowych.

![Pobierz test](media/how-to/how-to-system-test-download.png)

To archiwum zip pobranego zawiera trzy pliki.

1.  **custom.mt.txt:** Ten plik zawiera tłumaczenia maszynowe zdań język źródłowy w języku docelowym, wykonywane przez modelu wyszkolonego przy danych użytkownika.

2.  **REF.txt:** Ten plik zawiera tłumaczenia zdania język źródłowy w języku docelowym podane przez użytkownika.

3.  **Source.txt:** Ten plik zawiera zdań w języku źródła.

    ![System pobranych wyników testu](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Wdrażanie modelu

Żądanie wdrożenia:

1.  Wybierz projekt, przejdź na kartę w modele.

2. Pomyślnie nauczonego modelu należy go przedstawia przycisk "Deploy", jeśli nie wdrożone.

    ![Wdrażanie modelu](media/how-to/how-to-deploy-model.png)

3.  Kliknij pozycję wdrożenia.
4.  Wybierz **wdrożono** przypadku regionów, w której chcesz modelu można wdrożyć, a następnie kliknij przycisk Zapisz. Możesz wybrać **wdrożono** dla wielu regionów.

    ![Wdrażanie modelu](media/how-to/how-to-deploy-model-regions.png)

5.  W kolumnie "Status", możesz wyświetlić stan modelu.

>[!Note]
>Niestandardowe w usłudze Translator obsługuje 10 modeli wdrożone w obszarze roboczym w dowolnym momencie w czasie.

## <a name="update-deployment-settings"></a>Ustawienia wdrożenia aktualizacji

Aby zaktualizować ustawienia wdrażania:

1.  Wybierz projekt, a następnie przejdź do **modeli** kartę.

2. Pomyślnie wdrożono modelu pokazuje **aktualizacji** przycisku.

    ![Wdrażanie modelu](media/how-to/how-to-update-undeploy-model.png)

3.  Wybierz pozycję **Update** (Aktualizuj).
4.  Wybierz **wdrożono** lub **Undeployed** regiony, w którym ma być wdrożony lub cofnięto jego wdrożenie modelu, następnie kliknij **Zapisz**.

    ![Wdrażanie modelu](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Jeśli wybierzesz **Undeployed** we wszystkich regionach, model jest cofnięto jego wdrożenie ze wszystkich regionów i przejście w stan wycofane. Teraz jest dostępny do użycia.

## <a name="next-steps"></a>Kolejne kroki

- Rozpoczynanie korzystania z modelu tłumaczenia niestandardowych wdrożone za pośrednictwem [V3 interfejs API tekstu usługi Translator firmy Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Dowiedz się, [sposób zarządzania ustawieniami](how-to-manage-settings.md) udostępniania obszaru roboczego, zarządzanie klucz subskrypcji.
- Dowiedz się, [jak przeprowadzić migrację do swojego obszaru roboczego i projektu](how-to-migrate.md) z [Centrum w usłudze Translator firmy Microsoft](https://hub.microsofttranslator.com)
