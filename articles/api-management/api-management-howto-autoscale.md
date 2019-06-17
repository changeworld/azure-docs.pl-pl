---
title: Konfigurowanie automatycznego skalowania wystąpienia usługi Azure API Management | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób konfigurowania zachowanie automatycznego skalowania dla wystąpienia usługi Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: a01e50debf11daf2f1163a56726f5574f7e3e379
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123471"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Automatyczne skalowanie wystąpienia usługi Azure API Management  

Usługa Azure API Management, który wystąpienie usługi można skalować automatycznie na podstawie zestawu reguł. To zachowanie może być włączony i skonfigurowany za pomocą usługi Azure Monitor i jest obsługiwany tylko w **standardowa** i **Premium** warstwy usługi Azure API Management.

Tym artykule przedstawiono proces konfigurowania automatycznego skalowania i sugeruje optymalną konfigurację reguły skalowania automatycznego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki z tego artykułu, musisz mieć:

+ Mieć aktywną subskrypcję platformy Azure.
+ Utworzono wystąpienie usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).
+ Zrozumienie pojęcia [pojemności wystąpienia usługi Azure API Management](api-management-capacity.md).
+ Zrozumienie [ręczne skalowanie procesu wystąpienia usługi Azure API Management](upgrade-and-scale.md), tym konsekwencje kosztów.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Ograniczenia skalowania automatycznego platformy Azure API Management

Pewnych ograniczeń i konsekwencji decyzji dotyczących skalowania należy wziąć pod uwagę przed skonfigurowaniem zachowanie automatycznego skalowania.

+ Automatyczne skalowanie można włączyć tylko w przypadku **standardowa** i **Premium** warstwy usługi Azure API Management.
+ Warstwy cenowe również określić maksymalną liczbę jednostek danego wystąpienia usługi.
+ Proces skalowania potrwa co najmniej 20 minut.
+ Jeśli usługa jest zablokowany przez inną operację, żądanie skalowania będą się nie powieść i spróbuj ponownie automatycznie.
+ W przypadku usługi za pomocą wielu regionalnych wdrożeń, tylko jednostki w **lokalizacji głównej** może być skalowana. Nie można przeskalować jednostki w innych lokalizacjach.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Włączanie i Konfigurowanie automatycznego skalowania usługi Azure API Management

Wykonaj poniższe kroki, aby skonfigurować automatyczne skalowanie dla usługi Azure API Management:

1. Przejdź do **Monitor** wystąpienia w witrynie Azure portal.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Wybierz **skalowania automatycznego** z menu po lewej stronie.

    ![Zasobów skalowania automatycznego w usłudze Azure Monitor](media/api-management-howto-autoscale/02.png)

3. Znajdź usługi Usługa Azure API Management, w oparciu o filtry w menu rozwijanych.
4. Wybierz żądane wystąpienia usługi Azure API Management.
5. W nowo otwartym sekcji kliknij **włączyć Skalowanie automatyczne** przycisku.

    ![Usługi Azure Monitor automatycznego skalowania pozwalają](media/api-management-howto-autoscale/03.png)

6. W **reguły** kliknij **+ Dodaj regułę**.

    ![Skalowanie automatyczne monitorowanie platformy Azure Dodaj regułę](media/api-management-howto-autoscale/04.png)

7. Zdefiniuj nowy reguła skalowania w poziomie.

   Na przykład reguła skalowania w poziomie może wywołać Dodawanie jednostki usługi Azure API Management, gdy Metryka wydajności średni w ciągu ostatnich 30 minut przekracza 80%. Poniższa tabela zawiera konfigurację dla tych reguł.

    | Parametr             | Wartość             | Uwagi                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Źródło metryki         | Bieżący zasób  | Zdefiniuj reguły na podstawie bieżącego metryk zasobów usługi Azure API Management.                                                                                                                                                                                                     |
    | *Kryteria*            |                   |                                                                                                                                                                                                                                                                                 |
    | Agregacja czasu      | Średnia           |                                                                                                                                                                                                                                                                                 |
    | Nazwa metryki           | Pojemność          | Metryka wydajności to metryki usługi Azure API Management odzwierciedlający użycia zasobów wystąpienia usługi Azure API Management.                                                                                                                                                            |
    | Statystyka ziarna czasu  | Średnia           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Większe niż      |                                                                                                                                                                                                                                                                                 |
    | Próg             | 80%               | Próg dla metryki uśrednionej pojemności.                                                                                                                                                                                                                                 |
    | Czas trwania (w minutach) | 30                | Timespan średnia wartość metryki pojemności za pośrednictwem zależy od wzorców użycia. Im dłuższy okres czasu jest, wygładzania będzie reakcji — sporadyczne wzrostów będzie ograniczyć jej efekty na decyzję skalowalnego w poziomie. Jednakże będzie również opóźnić wyzwalacza skalowalnego w poziomie. |
    | *Akcja*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operacja             | Zwiększ liczbę o |                                                                                                                                                                                                                                                                                 |
    | Liczba wystąpień        | 1                 | Skalowanie wystąpienia usługi Azure API Management za 1 jednostkę.                                                                                                                                                                                                                          |
    | Czas ochładzania (minuty)   | 60                | Trwa co najmniej 20 minut do skalowania w poziomie za pomocą usługi Azure API Management. W większości przypadków chłodzenia 60 minut zapobiega wyzwalania wiele szczegółowymi skali.                                                                                                  |

8. Kliknij przycisk **Dodaj** można zapisać reguły.

    ![Usługa Azure Monitor reguła skalowania w poziomie](media/api-management-howto-autoscale/05.png)

9. Kliknij ponownie **+ Dodaj regułę**.

    Tym razem musi można zdefiniować reguły skalowania. Zapewni, że zasoby są nie są zmarnowany, gdy zmniejsza użycie interfejsów API.

10. Definiowanie nowej skali w regule.

    Na przykład reguły skalowania może wywołać usuwania jednostki usługi Azure API Management, po niższy niż 35% metryki wydajności średni w ciągu ostatnich 30 minut. Poniższa tabela zawiera konfigurację dla tych reguł.

    | Parametr             | Wartość             | Uwagi                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Źródło metryki         | Bieżący zasób  | Zdefiniuj reguły na podstawie bieżącego metryk zasobów usługi Azure API Management.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Kryteria*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Agregacja czasu      | Średnia           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nazwa metryki           | Pojemność          | Tym samym metryki jak konto używane dla reguły skalowania.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Statystyka ziarna czasu  | Średnia           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Mniejsze niż         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Próg             | 35%               | Podobnie do reguły skalowania, ta wartość silnie zależy od wzorców użycia usługi Azure API Management. |
    | Czas trwania (w minutach) | 30                | Tę samą wartość jak konto używane dla reguły skalowania.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Akcja*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operacja             | Zmniejsz liczbę o | W przeciwnym użytymi dla reguły skalowania.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Liczba wystąpień        | 1                 | Tę samą wartość jak konto używane dla reguły skalowania.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Czas ochładzania (minuty)   | 90                | Skalowanie w powinien być bardziej konserwatywnego niż skalowanie w poziomie, więc chłodzenia okres powinien być dłuższy.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Kliknij przycisk **Dodaj** można zapisać reguły.

    ![Skalowanie usługi Azure Monitor w regule](media/api-management-howto-autoscale/06.png)

12. Ustaw **maksymalna** liczba jednostek w usłudze Azure API Management.

    > [!NOTE]
    > Usługa Azure API Management ma limit jednostek, których wystąpienia można skalować do. Limit zależy od warstwy usług.

    ![Skalowanie usługi Azure Monitor w regule](media/api-management-howto-autoscale/07.png)

13. Kliknij pozycję **Zapisz**. Skonfigurowano usługi skalowania automatycznego.

## <a name="next-steps"></a>Kolejne kroki

+ [Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach świadczenia usługi Azure](api-management-howto-deploy-multi-region.md)
