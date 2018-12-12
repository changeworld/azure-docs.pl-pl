---
title: Używanie usługi sieci web w programie Excel — usługi Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Usługa Azure Machine Learning Studio ułatwia wywołują usługi sieci web bezpośrednio z programu Excel, bez konieczności pisania kodu.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 3f3cdd2f-1816-487e-ab78-530e01e9788f
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/1/2018
ms.openlocfilehash: 1425cb0f0b327e0f39733b99a4c10c03a0ec3089
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092353"
---
# <a name="consuming-an-azure-machine-learning-web-service-from-excel"></a>Korzystanie z usługi sieci Web Azure Machine Learning z poziomu programu Excel
 Usługa Azure Machine Learning Studio ułatwia wywołują usługi sieci web bezpośrednio z programu Excel, bez konieczności pisania kodu.

Jeśli używasz programu Excel 2013 (lub nowszego) lub usłudze Excel Online, a następnie zaleca się, że używasz programu Excel [dodatek programu Excel](excel-add-in-for-web-services.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

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
