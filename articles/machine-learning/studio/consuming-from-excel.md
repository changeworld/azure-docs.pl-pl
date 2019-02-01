---
title: Używanie usługi sieci web w programie Excel
titleSuffix: Azure Machine Learning Studio
description: Usługa Azure Machine Learning Studio ułatwia wywołują usługi sieci web bezpośrednio z programu Excel, bez konieczności pisania kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 95206d0e3cb7bdb619524237ca1e466414c5acf5
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492888"
---
# <a name="consuming-an-azure-machine-learning-studio-web-service-from-excel"></a>Korzystanie z usługi sieci Web Azure Machine Learning Studio z poziomu programu Excel

 Usługa Azure Machine Learning Studio ułatwia wywołują usługi sieci web bezpośrednio z programu Excel, bez konieczności pisania kodu.

Jeśli używasz programu Excel 2013 (lub nowszego) lub usłudze Excel Online, a następnie zaleca się, że używasz programu Excel [dodatek programu Excel](excel-add-in-for-web-services.md).



## <a name="steps"></a>Kroki
Publikowanie usługi sieci web. [Ta strona](walkthrough-5-publish-web-service.md) wyjaśnia, jak to zrobić. Obecnie funkcja skoroszytu programu Excel jest obsługiwana tylko dla usług żądań/odpowiedzi, które mają jeden z (czyli pojedynczej oceniania etykiety). 

Po utworzeniu usługi sieci web, kliknij pozycję **usług sieci WEB** sekcji po lewej stronie programu studio, a następnie wybierz usługę sieci web do korzystania z programu Excel.

**Klasyczna usługa sieci Web**

1. Na **pulpit NAWIGACYJNY** kartę dla usługi sieci web jest wiersz **ŻĄDAŃ/odpowiedzi** usługi. Jeśli ta usługa pojedynczego wyjścia, powinien zostać wyświetlony **pobrać skoroszyt programu Excel** łącza w tym wierszu.
   
    ![][1]
2. Kliknij pozycję **pobrać skoroszyt programu Excel**.

**Nowa usługa sieci Web**

1. W portalu usługi sieci Web Azure Machine Learning, wybierz **zużywania**.
2. Na stronie zużywania w **opcje użycia usługi sieci Web** kliknij ikonę programu Excel.

**Używaj skoroszytu**

1. Otwórz skoroszyt.
2. Zostanie wyświetlone ostrzeżenie o zabezpieczeniach; Kliknij pozycję **Włącz edytowanie** przycisku.
   
    ![][2]
3. Zostanie wyświetlone ostrzeżenie o zabezpieczeniach. Kliknij pozycję **Włącz zawartość** przycisk, aby uruchomić makra w arkuszu kalkulacyjnym.
   
    ![][3]
4. Po włączeniu makra, generowany jest tabelą. Kolumny w niebieskim są wymagane jako dane wejściowe do usługi sieci web RRS, lub **parametry**. Należy pamiętać, danych wyjściowych usługi RRS **przewidywane wartości** w kolorze zielonym. Gdy wszystkie kolumny dla danego wiersza są wypełnione, skoroszyt automatycznie wywołuje interfejs API oceniania i wyświetla wyniki ocenami.
   
    ![][4]
5. Zdobycie więcej niż jeden wiersz, wypełnienie drugi wiersz z danymi i przewidywane wartości są tworzone. Jednocześnie można wkleić nawet kilka wierszy.

Umożliwia dowolnej funkcji programu Excel (wykresy, power map, formatowanie warunkowe itp.) z przewidywane wartości pomagają wizualizować dane.    

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Makr do pracy klucz interfejsu API musi być częścią arkusza kalkulacyjnego. Oznacza to, że należy udostępnić skoroszyt tylko w przypadku jednostek/osób, którym ufasz.

## <a name="automatic-updates"></a>Automatyczne aktualizacje
Wykonano wywołanie rekordy zasobów w tych dwóch sytuacji:

1. Po raz pierwszy wiersz ma zawartość we wszystkich jej **parametrów**
2. Ilekroć dowolny z **parametry** zmian w wierszu, który miał wszystkie jego **parametry** wprowadzony.

[1]: ./media/consuming-from-excel/excellink.png
[2]: ./media/consuming-from-excel/enableeditting.png
[3]: ./media/consuming-from-excel/enablecontent.png
[4]: ./media/consuming-from-excel/sampletable.png
