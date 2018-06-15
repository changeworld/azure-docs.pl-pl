---
title: Zarządzaj subskrypcjami punktu końcowego platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule należy utworzyć klucz naliczane punktu końcowego dla Twojego konta LUIS zapewnić nieograniczony ruch do punktu końcowego po plan płatności.
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 3526871f126ac975f323fe84b14883b361b684ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349796"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Zarządzanie kluczami subskrypcji punktu końcowego platformy Azure

Testowanie i prototypu tylko Użyj bezpłatnej warstwy (F0). W przypadku systemów produkcyjnych, użyj [płatnej](https://aka.ms/luis-price-tier) warstwy. 

> [!NOTE]
> Nie używaj [tworzenia klucza](luis-concept-keys.md#authoring-key) dla punktu końcowego kwerend w środowisku produkcyjnym.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>Utwórz klucz punktu końcowego LUIS

1. Zaloguj się do  **[platformy Microsoft Azure](https://ms.portal.azure.com/)** 
2. Kliknij zielony **+** Zaloguj się w górnym lewym panelu i wyszukaj "LUIS" w witrynie marketplace, a następnie kliknij przycisk **opis języka** i postępuj zgodnie z **utworzyć środowisko**  Aby utworzyć konto subskrypcji LUIS. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

3. Konfigurowanie subskrypcji z ustawieniami, łącznie z nazwą konta ceny warstw itp. 

    ![Wybór interfejsu API platformy Azure](./media/luis-azure-subscription/azure-api-choice.png) 

4. Po utworzeniu usługi LUIS można wyświetlić klawisze dostępu wygenerowanych w **kluczy -> Zarządzanie zasobami**.  

    ![Kluczy Azure](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > * Zaloguj się do regionu [LUIS](luis-reference-regions.md) witryny sieci Web i [Dodaj nowy klucz punktu końcowego LUIS](Manage-Keys.md#assign-endpoint-key). 
    > * Trzeba pamiętać nazwy usługi Azure został utworzony, aby go zaznaczyć na region [LUIS](luis-reference-regions.md) strony publikacji.  

## <a name="change-luis-pricing-tier"></a>Warstwa cenowa LUIS zmiany

1.  W [Azure](https://portal.azure.com), Znajdź LUIS subskrypcji. Kliknij subskrypcję LUIS.
    ![Znajdź LUIS subskrypcji](./media/luis-usage-tiers/find.png)
2.  Kliknij przycisk **warstwa cenowa** aby zobaczyć dostępne warstwy cenowe. 
    ![Widok warstw cenowych](./media/luis-usage-tiers/subscription.png)
3.  Kliknij wybraną warstwę cenową, a następnie kliknij przycisk **wybierz** można zapisać zmiany. 
    ![Zmień warstwę LUIS płatności](./media/luis-usage-tiers/plans.png)
4.  Po ukończeniu zmieniania cenowej okno podręczne sprawdza nową warstwę cenową. 
    ![Sprawdź warstwę LUIS płatności](./media/luis-usage-tiers/updated.png)
5. Pamiętaj, aby [przypisać ten punkt końcowy klucz](manage-keys.md#assign-endpoint-key) na **publikowania** strony i używać jej w wszystkie zapytania punktu końcowego. 

## <a name="exceed-pricing-tier-usage"></a>Przekracza użycia warstwy cenowej
Każda warstwa umożliwia żądania punktu końcowego na koncie LUIS z określoną szybkością. Jeśli to liczba żądań jest wyższa niż dozwolone szybkość naliczane konta na minutę lub miesięcznie, żądania odbierania błędu HTTP "429: zbyt wiele żądań."

Każda warstwa umożliwia kumulacyjne żądań miesięcznie. Jeśli całkowita liczba żądań są wyższe niż dozwolone szybkość, żądania odbierania błędu HTTP "403: zabronione".  

## <a name="viewing-summary-usage"></a>Wyświetlanie podsumowania użycia
Można wyświetlić informacje o użyciu LUIS na platformie Azure. **Omówienie** strony pokazuje ostatnie podsumowanie informacji o tym wywołania i błędów. Jeśli wprowadzisz LUIS żądania punktu końcowego, następnie natychmiast oglądać **strony Przegląd**, Zezwalaj użycia wyświetlani do pięciu minut.

![Wyświetlanie podsumowania użycia](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Dostosowywanie wykresy użycia
Metryki zapewnia bardziej szczegółowy wgląd w dane.

![Domyślne metryki](./media/luis-usage-tiers/metrics-default.png)

Można skonfigurować wykresach metryki dla okresu i metryki typu. 

![Metryki niestandardowe](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Całkowita liczba transakcji próg alertu
Jeśli chcesz wiedzieć, kiedy osiągnięto określonego progu transakcji, na przykład 10 000 transakcji, można utworzyć alertu. 

![Domyślne alertów](./media/luis-usage-tiers/alert-default.png)

Dodawanie metryki alertu dla **całkowita liczba połączeń** metryki w danym okresie czasu. Dodaj adresy e-mail wszystkich osób, które powinien zostać wyświetlony alert. Dodawanie elementów webhook we wszystkich systemach, które powinien zostać wyświetlony alert. Można również uruchomić aplikację logiki, po wyzwoleniu alertu. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać [wersji](luis-how-to-manage-versions.md) Aby zarządzać zmianami LUIS aplikacji.