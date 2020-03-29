---
title: Wyświetlanie wyników testów systemu i wdrażania — usługa Custom Translator
titleSuffix: Azure Cognitive Services
description: Gdy szkolenie zakończy się pomyślnie, przejrzyj testy systemowe, aby przeanalizować wyniki treningu. Jeśli wyniki szkolenia są zadowalające, umieść żądanie wdrożenia dla wyszkolonego modelu.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f5e1a21f24fdd2458d4803d6ed1675455fa5037d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595602"
---
# <a name="view-system-test-results"></a>Wyświetlanie wyników testu systemu

Gdy szkolenie zakończy się pomyślnie, przejrzyj testy systemowe, aby przeanalizować wyniki treningu. Jeśli wyniki szkolenia są zadowalające, umieść żądanie wdrożenia dla wyszkolonego modelu.

## <a name="system-test-results-page"></a>Strona wyników testów systemu

Wybierz projekt, a następnie wybierz kartę modele tego projektu, znajdź model, którego chcesz użyć, a na koniec wybierz kartę testu.

Karta testowa pokazuje:

1.  **Wyniki testów systemu:** Wynik procesu testowego w szkoleniach. Proces testowy tworzy wynik BLEU.

    **Liczba zdań:** Ile równoległych zdań zostało użytych w zestawie testów.

     **Wynik BLEU:** Wynik BLEU wygenerowany dla modelu po zakończeniu szkolenia.

    **Status:** Wskazuje, czy proces testu został zakończony lub w toku.

    ![Wyniki testów systemu](media/how-to/how-to-system-test-results.png)

2.  Kliknij na wyniki testu systemu, a to zajmie Ci przetestować szczegóły wyników strony. Na tej stronie przedstawiono tłumaczenie maszynowe zdań, które były częścią zestawu danych testowych.

3.  Tabela na stronie szczegółów wyników testu ma dwie kolumny — po jednej dla każdego języka w parze. Kolumna dla języka źródłowego pokazuje zdanie do przetłumaczenia. Kolumna dla języka docelowego zawiera dwa zdania w każdym wierszu.

    **Ref:** To zdanie jest tłumaczeniem odwołania zdania źródłowego podane w zestawie danych testowych.

    **MT:** To zdanie jest automatyczne tłumaczenie zdania źródłowego wykonane przez model zbudowany po szkolenia została przeprowadzona.

    ![Porównanie wyników testów systemu](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Test pobierania

Kliknij łącze Pobierz tłumaczenia, aby pobrać plik zip. Zip zawiera tłumaczenia maszynowe zdań źródłowych w zestawie danych testowych.

![Test pobierania](media/how-to/how-to-system-test-download.png)

To pobrane archiwum zip zawiera trzy pliki.

1.  **custom.mt.txt:** Ten plik zawiera tłumaczenia maszynowe zdań języka źródłowego w języku docelowym wykonane przez model przeszkolony z danymi użytkownika.

2.  **ref.txt:** Ten plik zawiera podane przez użytkownika tłumaczenia zdań języka źródłowego w języku docelowym.

3.  **źródło.txt:** Ten plik zawiera zdania w języku źródłowym.

    ![Pobrane wyniki testów systemowych](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Wdrażanie modelu

Aby zażądać wdrożenia:

1.  Wybierz projekt, przejdź do karty Modele.

2. Dla pomyślnie uczonego modelu, pokazuje przycisk "Wdrażanie", jeśli nie wdrożony.

    ![Wdrażanie modelu](media/how-to/how-to-deploy-model.png)

3.  Kliknij przycisk Wdrażanie.
4.  Wybierz **pozycję Wdrożone** dla regionów, w których chcesz wdrożyć model, a następnie kliknij przycisk Zapisz. Można wybrać **wdrożone** dla wielu regionów.

    ![Wdrażanie modelu](media/how-to/how-to-deploy-model-regions.png)

5.  Stan modelu można wyświetlić w kolumnie "Stan".

>[!Note]
>Usługa Custom Translator obsługuje 10 wdrożonych modeli w obszarze roboczym w dowolnym momencie.

## <a name="update-deployment-settings"></a>Aktualizowanie ustawień wdrażania

Aby zaktualizować ustawienia wdrażania:

1.  Wybierz projekt i przejdź do karty **Modele.**

2. W przypadku pomyślnie wdrożonego modelu jest wyświetlany przycisk **Aktualizuj.**

    ![Wdrażanie modelu](media/how-to/how-to-update-undeploy-model.png)

3.  Wybierz pozycję **Update** (Aktualizuj).
4.  Wybierz **pozycję Wdrożone** lub **Nierozsądzone** dla regionów, w których chcesz wdrożyć lub cofnąć wdrożenie modelu, a następnie kliknij przycisk **Zapisz**.

    ![Wdrażanie modelu](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Jeśli **wybierzesz Undeployed** dla wszystkich regionów, model jest undeployed ze wszystkich regionów i umieścić w stanie nierozmieszczonych. Jest teraz niedostępny do użycia.

## <a name="next-steps"></a>Następne kroki

- Zacznij korzystać z wdrożonego niestandardowego modelu tłumaczenia za pośrednictwem [interfejsu API tekstu usługi Microsoft Translator w wersji 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Dowiedz [się, jak zarządzać ustawieniami](how-to-manage-settings.md) udostępniania obszaru roboczego, zarządzać kluczem subskrypcji.
- Dowiedz [się, jak przeprowadzić migrację obszaru roboczego i projektu](how-to-migrate.md) z centrum Microsoft Translator [Hub](https://hub.microsofttranslator.com)
