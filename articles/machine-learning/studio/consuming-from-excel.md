---
title: Korzystanie z usługi sieci Web w programie Excel
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasyczny) ułatwia wywoływanie usług sieci Web bezpośrednio z programu Excel bez konieczności pisania kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 2e95c4bfbe7342e251e6d845fd4acfed6ff6109a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150106"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Zużywanie Azure Machine Learning Studio (klasycznej) usługi sieci Web z programu Excel

 Azure Machine Learning Studio (klasyczny) ułatwia wywoływanie usług sieci Web bezpośrednio z programu Excel bez konieczności pisania kodu.

Jeśli używasz programu Excel 2013 (lub nowszego) lub usługi Excel Online, zalecamy użycie dodatku programu Excel [Excel](excel-add-in-for-web-services.md).



## <a name="steps"></a>Kroki
Publikowanie usługi sieci web. [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md) wyjaśnia, jak to zrobić. Obecnie funkcja skoroszytu programu Excel jest obsługiwana tylko dla usług żądań/odpowiedzi, które mają jeden z (czyli pojedynczej oceniania etykiety). 

Gdy masz usługę sieci Web, kliknij sekcję **usługi sieci Web** po lewej stronie programu Studio, a następnie wybierz usługę sieci Web, która ma być wykorzystana z programu Excel.

**Klasyczna usługa sieci Web**

1. Na karcie **pulpit nawigacyjny** usługi sieci Web jest wierszem usługi **żądania/odpowiedzi** . Jeśli ta usługa ma pojedyncze dane wyjściowe, w tym wierszu powinien zostać wyświetlony link **Pobierz skoroszyt programu Excel** .

    ![Pobieranie skoroszytu programu Excel przy użyciu portalu usługi sieci Web programu Studio (klasycznego)](./media/consuming-from-excel/excellink.png)
2. Kliknij pozycję **Pobierz skoroszyt programu Excel**.

**Nowa usługa sieci Web**

1. W portalu usługi sieci Web **Azure Machine Learning wybierz pozycję**Użyj.
2. Na stronie korzystanie w sekcji **Opcje zużycia usług sieci Web** kliknij ikonę programu Excel.

**Korzystanie z skoroszytu**

1. Otwórz skoroszyt.
2. Zostanie wyświetlone ostrzeżenie o zabezpieczeniach. Kliknij przycisk **Włącz edycję** .

    ![Włącz edytowanie, aby usunąć ostrzeżenie o zabezpieczeniach widoku chronionego](./media/consuming-from-excel/enableeditting.png)
3. Zostanie wyświetlone ostrzeżenie o zabezpieczeniach. Kliknij przycisk **Włącz zawartość** , aby uruchomić makra w arkuszu kalkulacyjnym.

    ![Włącz zawartość, aby odrzucić makra ostrzeżenia o wyłączaniu zabezpieczeń](./media/consuming-from-excel/enablecontent.png)
4. Po włączeniu makra, generowany jest tabelą. Kolumny w kolorze niebieskim są wymagane jako dane wejściowe do usługi sieci Web RR lub **parametrów**. Zwróć uwagę na dane wyjściowe usługi RR, **wartości przewidywane** w kolorze zielonym. Gdy wszystkie kolumny dla danego wiersza są wypełnione, skoroszyt automatycznie wywołuje interfejs API oceniania i wyświetla wyniki ocenami.

    ![Tabela dla danych wejściowych parametrów i wyniki przewidywane](./media/consuming-from-excel/sampletable.png)
5. Zdobycie więcej niż jeden wiersz, wypełnienie drugi wiersz z danymi i przewidywane wartości są tworzone. Jednocześnie można wkleić nawet kilka wierszy.

Umożliwia dowolnej funkcji programu Excel (wykresy, power map, formatowanie warunkowe itp.) z przewidywane wartości pomagają wizualizować dane.

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Makr do pracy klucz interfejsu API musi być częścią arkusza kalkulacyjnego. Oznacza to, że należy udostępnić skoroszyt tylko w przypadku jednostek/osób, którym ufasz.

## <a name="automatic-updates"></a>Automatyczne aktualizacje
Wykonano wywołanie rekordy zasobów w tych dwóch sytuacji:

1. Pierwszy raz wiersz zawiera zawartość we wszystkich jego **parametrach**
2. Wszystkie parametry każdego z **parametrów** zmieniają się w wierszu, który miał wszystkie wprowadzone **Parametry** .