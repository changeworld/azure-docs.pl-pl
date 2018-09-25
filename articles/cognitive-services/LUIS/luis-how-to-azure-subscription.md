---
title: Punkt końcowy zarządzania subskrypcjami w usługi LUIS
titleSuffix: Azure Cognitive Services
description: W tym artykule tworzenia klucza punktu końcowego naliczane dla swojego konta usługi LUIS zapewnić nieograniczony ruch do punktu końcowego zgodnie z planem płatności.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: c7c6009d9fec14e8633a37e5384dd203f2f5fa60
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040384"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Zarządzanie kluczami subskrypcji punkt końcowy platformy Azure

Testowanie i prototypu tylko przy użyciu warstwy bezpłatna (F0). W przypadku systemów produkcyjnych, użyj [płatnych](https://aka.ms/luis-price-tier) warstwy. 

> [!NOTE]
> Nie używaj [tworzenia klucza](luis-concept-keys.md#authoring-key) dla punktu końcowego kwerend w środowisku produkcyjnym.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>Tworzenie klucza punktu końcowego usługi LUIS

1. Zaloguj się do  **[platformy Microsoft Azure](https://ms.portal.azure.com/)**. 
2. Kliknij zielony **+** Zaloguj się w prawym górnym panelu po lewej stronie i poszukaj pozycji "LUIS" w witrynie marketplace, a następnie kliknij pozycję **Language Understanding** i postępuj zgodnie z **tworzenia środowiska**  utworzyć konto subskrypcji usługi LUIS. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

3. Konfigurowanie subskrypcji przy użyciu ustawień, łącznie z nazwą konta ceny warstw itp. 

    ![Usługa Azure wybranego interfejsu API](./media/luis-azure-subscription/azure-api-choice.png) 

4. Po utworzeniu usługi LUIS, można wyświetlić klucze dostępu wygenerowanych w **zarządzania zasobami -> klucze**.  

    ![Klucze Azure](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > Zaloguj się w Twoim regionie [LUIS](luis-reference-regions.md) witryny sieci Web i [przypisać nowe usługi LUIS klucza punktu końcowego](luis-how-to-manage-keys.md#assign-endpoint-key). Potrzebna jest nazwa subskrypcji usługi LUIS z kroku 3.

## <a name="change-luis-pricing-tier"></a>Zmiana usługi LUIS — warstwa cenowa

1.  W [Azure](https://portal.azure.com), Znajdź swoją subskrypcję usługi LUIS. Kliknij subskrypcję usługi LUIS.
    ![Znajdź swoją subskrypcję usługi LUIS](./media/luis-usage-tiers/find.png)
2.  Kliknij przycisk **warstwa cenowa** aby można było wyświetlić dostępnych warstw cenowych. 
    ![Wyświetl warstwy cenowe](./media/luis-usage-tiers/subscription.png)
3.  Kliknij wybraną warstwę cenową, a następnie kliknij przycisk **wybierz** można zapisać zmiany. 
    ![Zmień warstwę płatności usługi LUIS](./media/luis-usage-tiers/plans.png)
4.  Po zakończeniu zmianą cen okno podręczne sprawdza nowej warstwy cenowej. 
    ![Sprawdź warstwę płatności usługi LUIS](./media/luis-usage-tiers/updated.png)
5. Pamiętaj, aby [przypisania tego klucza punktu końcowego](luis-how-to-manage-keys.md#assign-endpoint-key) na **Publikuj** strony i używać jej w wszystkie zapytania punktu końcowego. 

## <a name="exceed-pricing-tier-usage"></a>Przekracza użycie warstwy cenowej
Każda warstwa umożliwia żądania punktu końcowego do swojego konta usługi LUIS z określoną szybkością. Jeśli liczba żądań jest większy niż dozwolone stopień konta naliczane za minutę lub miesięcznie, żądania otrzymują błąd HTTP "429: zbyt wiele żądań."

Każda warstwa umożliwia kumulacyjne żądań miesięcznie. Jeśli łączna liczba żądań są wyższe niż dozwolone szybkość, żądania otrzymują błąd HTTP "403: dostęp zabroniony".  

## <a name="viewing-summary-usage"></a>Wyświetlanie podsumowania użycia
Możesz wyświetlić informacje dotyczące użycia usługi LUIS na platformie Azure. **Przegląd** strona zawiera najnowsze informacje podsumowania, w tym połączeń i błędów. Jeśli usługa LUIS żądania punktu końcowego, następnie natychmiast obejrzeć **strony Przegląd**, Zezwalaj na użycie pojawienie się do pięciu minut.

![Wyświetlanie podsumowania użycia](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Dostosowywanie wykresy użycia
Metryki zapewnia bardziej szczegółowy wgląd w dane.

![Domyślne metryki](./media/luis-usage-tiers/metrics-default.png)

Wykresy metryk można skonfigurować w przedziale czasu i typ metryki. 

![Metryki niestandardowe](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Łączna liczba transakcji próg alertu
Jeśli chcesz dowiedzieć się, gdy został osiągnięty limit określonego progu transakcji, na przykład 10 000 transakcji, można utworzyć alertu. 

![Domyślne alerty](./media/luis-usage-tiers/alert-default.png)

Dodaj alert dotyczący metryki dla **łączna liczba wywołań** metryk przez pewien czas. Dodaj adresy e-mail wszystkich osób, które powinien zostać wyświetlony alert. Dodaj elementy webhook dla wszystkich systemów, które powinny otrzymać alert. Można również uruchomić aplikację logiki, gdy zostanie wyzwolony alert. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać [wersji](luis-how-to-manage-versions.md) Zarządzanie zmianami w aplikacją usługi LUIS.