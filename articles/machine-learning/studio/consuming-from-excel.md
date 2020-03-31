---
title: Korzystanie z usługi sieci Web w programie Excel
titleSuffix: ML Studio (classic) - Azure
description: Usługa Azure Machine Learning Studio (klasyczna) ułatwia wywoływanie usług sieci web bezpośrednio z programu Excel bez konieczności pisania kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 333ed411ab818cff77a7cba6c7de4f42c36f5b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218211"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Korzystanie z usługi Azure Machine Learning Studio (klasycznej) usługi sieci Web z programu Excel

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Usługa Azure Machine Learning Studio (klasyczna) ułatwia wywoływanie usług sieci web bezpośrednio z programu Excel bez konieczności pisania kodu.

Jeśli używasz programu Excel 2013 (lub nowszego) lub aplikacji Excel Online, zalecamy użycie [dodatku programu](excel-add-in-for-web-services.md)Excel Excel .



## <a name="steps"></a>Kroki
Publikowanie usługi sieci web. [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md) wyjaśnia, jak to zrobić. Obecnie funkcja skoroszytu programu Excel jest obsługiwana tylko w przypadku usług żądania/odpowiedzi, które mają jedno dane wyjściowe (czyli pojedynczą etykietę oceniania). 

Po uruchomieniu usługi sieci web kliknij sekcję **USŁUGI SIECI WEB** po lewej stronie studia, a następnie wybierz usługę sieci Web, którą chcesz z niej korzystać w programie Excel.

**Klasyczna usługa sieci Web**

1. Na karcie **DASHBOARD** dla usługi sieci web znajduje się wiersz dla usługi **REQUEST/RESPONSE.** Jeśli ta usługa miała pojedyncze dane wyjściowe, w tym wierszu powinno zostać wyświetlenie łącza **Pobierz skoroszyt programu Excel.**

    ![Pobieranie skoroszytu programu Excel za pomocą portalu studio (klasycznej) usługi sieci Web](./media/consuming-from-excel/excellink.png)
2. Kliknij **pozycję Pobierz skoroszyt programu Excel**.

**Nowa usługa sieci Web**

1. W portalu usługi azure machine learning web service wybierz pozycję **Konsumuj**.
2. Na stronie Zużycie w sekcji **Opcje użycia usługi sieci Web** kliknij ikonę programu Excel.

**Korzystanie ze skoroszytu**

1. Otwórz skoroszyt.
2. Pojawi się ostrzeżenie o zabezpieczeniach; kliknij przycisk **Włącz edycję.**

    ![Włącz edycję, aby usunąć ostrzeżenie o zabezpieczeniach widoku chronionego](./media/consuming-from-excel/enableeditting.png)
3. Pojawi się ostrzeżenie o zabezpieczeniach. Kliknij przycisk **Włącz zawartość,** aby uruchomić makra w arkuszu kalkulacyjnym.

    ![Włącz zawartość, aby odrzucić ostrzeżenie o zabezpieczeniach wyłączając makra](./media/consuming-from-excel/enablecontent.png)
4. Po włączeniu makr jest generowana tabela. Kolumny na niebiesko są wymagane jako wejście do usługi internetowej RRS lub **PARAMETRY**. Zanotuj dane wyjściowe usługi RRS, **PRZEWIDYWANE WARTOŚCI** na zielono. Po wypełnieniu wszystkich kolumn dla danego wiersza skoroszyt automatycznie wywołuje interfejs API oceniania i wyświetla wynik.

    ![Tabela dla danych wejściowych parametrów i wynikowych wartości przewidywanych](./media/consuming-from-excel/sampletable.png)
5. Aby uzyskać więcej niż jeden wiersz, wypełnij drugi wiersz danymi i prognozowane wartości są produkowane. Możesz nawet wkleić kilka wierszy naraz.

Można użyć dowolnej funkcji programu Excel (wykresy, powermap, formatowanie warunkowe itp.) z przewidywanymi wartościami, aby ułatwić wizualizację danych.

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Aby makra działały, klucz interfejsu API musi być częścią arkusza kalkulacyjnego. Oznacza to, że skoroszyt należy udostępniać tylko zaufanym jednostkom/osobom.

## <a name="automatic-updates"></a>Automatyczne aktualizacje
Wywołanie RRS jest wykonywania w tych dwóch sytuacjach:

1. Po raz pierwszy wiersz ma zawartość we wszystkich jego **PARAMETRÓW**
2. Za każdym razem, gdy dowolny z **parametrów** zmienia się w wierszu, w który wprowadzono wszystkie jego **PARAMETRY.**