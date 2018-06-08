---
title: Korzystanie z usługi sieci Web Machine Learning z programu Excel | Dokumentacja firmy Microsoft
description: Korzystanie z usługi sieci Web Azure Machine Learning z programu Excel
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
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
ms.openlocfilehash: 14621e50a397bc1f1922a4c8fae638d6b42ab8ba
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837069"
---
# <a name="consuming-an-azure-machine-learning-web-service-from-excel"></a>Korzystanie z usługi sieci Web Azure Machine Learning z poziomu programu Excel
 Azure Machine Learning Studio ułatwia wywołują usługi sieci web bezpośrednio z programu Excel, bez konieczności pisania kodu.

Jeśli używasz programu Excel 2013 (lub nowszy) lub aplikacji Excel Online, firma Microsoft zaleca użycie programu Excel [dodatek programu Excel](excel-add-in-for-web-services.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="steps"></a>Kroki
Publikowanie usługi sieci web. [Ta strona](walkthrough-5-publish-web-service.md) wyjaśniono, jak to zrobić. Funkcja skoroszytu programu Excel jest obecnie obsługiwana tylko dla usług żądań i odpowiedzi, które mają jeden z (to znaczy pojedynczej oceniania etykiety). 

Po utworzeniu usługi sieci web, kliknij **usług sieci WEB** studio po lewej stronie, a następnie wybierz usługę sieci web, aby korzystać z programu Excel.

**Usługa sieci Web klasycznego**

1. Na **pulpitu NAWIGACYJNEGO** karcie dla usługi sieci web jest wiersz **żądanie/odpowiedź** usługi. Jeśli ta usługa miała pojedynczego wyjścia, powinny pojawić się **Pobierz skoroszyt programu Excel** łącza w tym wierszu.
   
    ![][1]
2. Polecenie **Pobierz skoroszyt programu Excel**.

**Nowa usługa sieci Web**

1. W portalu Azure Machine Learning sieci Web usługi wybierz **Consume**.
2. Na stronie Consume w **opcje przez usługę sieci Web** sekcji, kliknij ikonę programu Excel.

**Używaj skoroszytu**

1. Otwórz skoroszyt.
2. Zostanie wyświetlone ostrzeżenie o zabezpieczeniach; polecenie **Włącz edytowanie** przycisku.
   
    ![][2]
3. Zostanie wyświetlone ostrzeżenie o zabezpieczeniach. Polecenie **Włącz zawartość** przycisk, aby uruchomić makra w arkuszu kalkulacyjnym.
   
    ![][3]
4. Po włączeniu makra, jest generowany tabeli. Kolumny w niebieski są wymagane jako dane wejściowe do rekordów zasobów usługi sieci web, lub **parametry**. Należy pamiętać, dane wyjściowe usługi RRS **PROGNOZOWANE wartości** na zielono. Po wprowadzeniu są wszystkie kolumny dla danego wiersza, skoroszyt automatycznie wywołuje interfejs API oceniania i wyświetla wyniki scored.
   
    ![][4]
5. Aby otrzymać więcej niż jeden wiersz, wypełnienie drugiego wiersza z danymi i przewidywane wartości są tworzone. Jednocześnie można wkleić nawet kilka wierszy.

Możesz ułatwiają dowolnej funkcji programu Excel (wykresy, mapy zasilania, warunkowego formatowania, itp.) z wartościami przewidywane wizualizacji danych.    

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Makra do pracy klucz interfejsu API musi być częścią arkusza kalkulacyjnego. Oznacza to, że tylko z jednostek/osób, którym ufa powinny współużytkować skoroszytu.

## <a name="automatic-updates"></a>Automatyczne aktualizacje
Rekordy zasobów wywołanie w tych dwóch sytuacji:

1. Po raz pierwszy wiersz ma zawartość we wszystkich jego **parametrów**
2. Wtedy żadnego z **parametry** zmiany w wierszu, który ma wszystkie jego **parametry** wprowadzona.

[1]: ./media/consuming-from-excel/excellink.png
[2]: ./media/consuming-from-excel/enableeditting.png
[3]: ./media/consuming-from-excel/enablecontent.png
[4]: ./media/consuming-from-excel/sampletable.png
