---
title: Używanie usługi sieci web w programie Excel
titleSuffix: Azure Machine Learning Studio
description: Usługa Azure Machine Learning Studio ułatwia wywołują usługi sieci web bezpośrednio z programu Excel, bez konieczności pisania kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: ef1d8f1a72c5936ff661636c4c51acf439a0a5ea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60773794"
---
# <a name="consuming-an-azure-machine-learning-studio-web-service-from-excel"></a>Korzystanie z usługi sieci Web Azure Machine Learning Studio z poziomu programu Excel

 Usługa Azure Machine Learning Studio ułatwia wywołują usługi sieci web bezpośrednio z programu Excel, bez konieczności pisania kodu.

Jeśli używasz programu Excel 2013 (lub nowszego) lub usłudze Excel Online, a następnie zaleca się, że używasz programu Excel [dodatek programu Excel](excel-add-in-for-web-services.md).



## <a name="steps"></a>Kroki
Publikowanie usługi sieci web. [Samouczek 3: Wdrażanie modelu ryzyko kredytowe](tutorial-part3-credit-risk-deploy.md) wyjaśnia, jak to zrobić. Obecnie funkcja skoroszytu programu Excel jest obsługiwana tylko dla usług żądań/odpowiedzi, które mają jeden z (czyli pojedynczej oceniania etykiety). 

Po utworzeniu usługi sieci web, kliknij pozycję **usług sieci WEB** sekcji po lewej stronie programu studio, a następnie wybierz usługę sieci web do korzystania z programu Excel.

**Klasyczna usługa sieci Web**

1. Na **pulpit NAWIGACYJNY** kartę dla usługi sieci web jest wiersz **ŻĄDAŃ/odpowiedzi** usługi. Jeśli ta usługa pojedynczego wyjścia, powinien zostać wyświetlony **pobrać skoroszyt programu Excel** łącza w tym wierszu.

    ![Pobieranie skoroszytu programu Excel przy użyciu portalu usług sieci Web Studio](./media/consuming-from-excel/excellink.png)
2. Kliknij pozycję **pobrać skoroszyt programu Excel**.

**Nowa usługa sieci Web**

1. W portalu usługi sieci Web Azure Machine Learning, wybierz **zużywania**.
2. Na stronie zużywania w **opcje użycia usługi sieci Web** kliknij ikonę programu Excel.

**Używaj skoroszytu**

1. Otwórz skoroszyt.
2. Zostanie wyświetlone ostrzeżenie o zabezpieczeniach; Kliknij pozycję **Włącz edytowanie** przycisku.

    ![Włącz edytowanie usunąć ostrzeżenie o zabezpieczeniach widok chroniony](./media/consuming-from-excel/enableeditting.png)
3. Zostanie wyświetlone ostrzeżenie o zabezpieczeniach. Kliknij pozycję **Włącz zawartość** przycisk, aby uruchomić makra w arkuszu kalkulacyjnym.

    ![Włącz zawartość zamknąć ostrzeżenie o zabezpieczeniach wyłączenie makr](./media/consuming-from-excel/enablecontent.png)
4. Po włączeniu makra, generowany jest tabelą. Kolumny w niebieskim są wymagane jako dane wejściowe do usługi sieci web RRS, lub **parametry**. Należy pamiętać, danych wyjściowych usługi RRS **przewidywane wartości** w kolorze zielonym. Gdy wszystkie kolumny dla danego wiersza są wypełnione, skoroszyt automatycznie wywołuje interfejs API oceniania i wyświetla wyniki ocenami.

    ![Tabeli dla parametru w danych wejściowych i wynikowy przewidywane wartości](./media/consuming-from-excel/sampletable.png)
5. Zdobycie więcej niż jeden wiersz, wypełnienie drugi wiersz z danymi i przewidywane wartości są tworzone. Jednocześnie można wkleić nawet kilka wierszy.

Umożliwia dowolnej funkcji programu Excel (wykresy, power map, formatowanie warunkowe itp.) z przewidywane wartości pomagają wizualizować dane.

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Makr do pracy klucz interfejsu API musi być częścią arkusza kalkulacyjnego. Oznacza to, że należy udostępnić skoroszyt tylko w przypadku jednostek/osób, którym ufasz.

## <a name="automatic-updates"></a>Automatyczne aktualizacje
Wykonano wywołanie rekordy zasobów w tych dwóch sytuacji:

1. Po raz pierwszy wiersz ma zawartość we wszystkich jej **parametrów**
2. Ilekroć dowolny z **parametry** zmian w wierszu, który miał wszystkie jego **parametry** wprowadzony.