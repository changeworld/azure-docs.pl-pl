---
title: Uruchamianie prognoz wsadowych przy użyciu programu Azure Machine Learning Designer (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak szkolić model i skonfigurować potok prognozowania usługi Batch przy użyciu narzędzia Projektant. Wdróż potok jako sparametryzowanej usługi sieci Web, która może być wyzwalana z dowolnej biblioteki HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/19/2019
ms.custom: Ignite2019
ms.openlocfilehash: 1e346d2542193ec1880ad0a56bd6afa1b0a46890
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122631"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Uruchamianie prognoz wsadowych za pomocą narzędzia Azure Machine Learning Designer
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym instruktażu dowiesz się, jak używać projektanta do uczenia modelu i konfigurowania potoku prognozowania partii i usługi sieci Web. Funkcja przewidywania wsadowe umożliwia ciągłe i na żądanie ocenianie modeli w dużych zestawach danych, opcjonalnie skonfigurowanych jako usługa sieci Web, która może być wyzwalana z dowolnej biblioteki HTTP. 

Aby skonfigurować usługi wsadowych oceniania przy użyciu zestawu SDK, zobacz towarzyszące [instrukcje](how-to-use-parallel-run-step.md).

W tym instruktażu przedstawiono następujące zadania:

> [!div class="checklist"]
> * Tworzenie eksperymentu Basic ML w potoku
> * Tworzenie potoku wnioskowania o sparametryzowanym zbiorze
> * Ręczne zarządzanie potokami i ich uruchamianie z poziomu punktu końcowego REST

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

1. Utwórz [obszar roboczy](tutorial-1st-experiment-sdk-setup.md).

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).

Ta procedura polega na założeniu podstawowej znajomości tworzenia prostego potoku w projektancie. Aby zapoznać się z przewodnikiem po wprowadzeniu do projektanta, Ukończ [samouczek](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-pipeline"></a>Tworzenie potoku

Aby utworzyć potok wnioskowania o partie, musisz najpierw wykonać eksperyment uczenia maszynowego. Aby go utworzyć, przejdź do karty **Projektant** w obszarze roboczym i Utwórz nowy potok, wybierając opcję **łatwe do użycia prekompilowane moduły** .

![Strona główna projektanta](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-1.png)

Poniżej znajduje się prosty model uczenia maszynowego w celach demonstracyjnych. Dane są zarejestrowanym zestawem danych utworzonym na podstawie otwartych zestawów danych cukrzycą na platformie Azure. Zapoznaj się z [sekcją instrukcje](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) dotyczące rejestrowania zestawów danych z platformy Azure Otwórz zestawy danych. Dane są podzielone na zestawy szkoleń i walidacji, a wzmocnione drzewo decyzyjne jest przeszkolone i oceniane. Potok musi być uruchamiany co najmniej raz, aby można było utworzyć potok inferencing. Kliknij przycisk **Uruchom** , aby uruchomić potok.

![Tworzenie prostego eksperymentu](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>Tworzenie potoku wnioskowania partii

Teraz, gdy potok został uruchomiony, dostępna jest nowa opcja obok opcji **Uruchom** i **Opublikuj** o nazwie **Utwórz potok wnioskowania**. Kliknij listę rozwijaną i wybierz pozycję **potok wnioskowania partii**.

![Tworzenie potoku wnioskowania partii](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-5.png)

Wynik to domyślny potok wnioskowania o partię. Obejmuje to węzeł instalacji oryginalnego eksperymentu potoku, węzeł danych pierwotnych do oceniania, a także węzeł do oceny danych pierwotnych względem oryginalnego potoku.

![Domyślny potok wnioskowania o partie](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-6.png)

Możesz dodać inne węzły, aby zmienić zachowanie procesu wsadowego inferencing. W tym przykładzie dodasz węzeł do próbkowania losowo z danych wejściowych przed rozpoczęciem oceniania. Utwórz **partycję i przykładowy** węzeł i umieść go między węzłami nieprzetworzone dane i ocenianie. Następnie kliknij **partycję i węzeł przykładu** , aby uzyskać dostęp do ustawień i parametrów.

![Nowy węzeł](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-7.png)

*Częstotliwość próbkowania* określa, jaki procent oryginalnego zestawu danych ma pobrać losowy przykład z. Jest to parametr, który będzie przydatny do częstego dopasowywania, dlatego należy go włączyć jako parametr potoku. Parametry potoku można zmienić w czasie wykonywania i można je określić w obiekcie ładunku podczas uruchamiania potoku z punktu końcowego REST. 

Aby włączyć to pole jako parametr potoku, kliknij wielokropek powyżej pola, a następnie kliknij przycisk **Dodaj do parametru potoku**. 

![Ustawienia przykładowe](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-8.png)

Następnie nadaj parametrowi nazwę i wartość domyślną. Nazwa będzie używana do identyfikowania parametru i określana w wywołaniu REST.

![Parametr potoku](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>Wdróż potok inferencing Batch

Teraz możesz przystąpić do wdrożenia potoku. Kliknij przycisk **Wdróż** , który powoduje otwarcie interfejsu w celu skonfigurowania punktu końcowego. Kliknij listę rozwijaną i wybierz pozycję **Nowy PipelineEndpoint**.

![Wdrażanie potoku](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-10.png)

Podaj nazwę i opcjonalny opis punktu końcowego. W dolnej części zobaczysz parametr `sample-rate` skonfigurowany z wartością domyślną 0,8. Gdy wszystko będzie gotowe, kliknij przycisk **Wdróż**.

![Punkt końcowy konfiguracji](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-11.png)

## <a name="manage-endpoints"></a>Zarządzanie punktami końcowymi 

Po zakończeniu wdrażania przejdź do karty **punkty końcowe** i kliknij nazwę właśnie utworzonego punktu końcowego.

![Łącze punktu końcowego](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-12.png)

Na tym ekranie są wyświetlane wszystkie opublikowane potoki w określonym punkcie końcowym. Kliknij potok inferencing.

![Potok wnioskowania](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-13.png)

Na stronie Szczegóły potoku zostanie wyświetlona Szczegółowa historia uruchamiania i informacje o parametrach połączenia dla potoku. Kliknij przycisk **Uruchom** , aby utworzyć ręczny przebieg potoku.

![Szczegóły potoku](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-14.png)

W instalatorze uruchomienia można podać opis przebiegu i zmienić wartość parametrów potoku. Tym razem należy ponownie uruchomić potok inferencing z próbką częstotliwości 0,9. Kliknij przycisk **Uruchom** , aby uruchomić potok.

![Uruchomienie potoku](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-15.png)

Karta **Używanie** zawiera punkt końcowy REST do uruchomienia potoku. Aby można było wywołać metodę REST, potrzebny jest nagłówek uwierzytelniania OAuth 2,0 typu okaziciela. Zapoznaj się z poniższą [sekcją samouczka](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) , aby uzyskać więcej szczegółów na temat konfigurowania uwierzytelniania do obszaru roboczego i wykonywania sparametryzowanych wywołań REST.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [samouczkiem](tutorial-designer-automobile-price-train-score.md) projektanta, aby nauczyć i wdrożyć model regresji.
