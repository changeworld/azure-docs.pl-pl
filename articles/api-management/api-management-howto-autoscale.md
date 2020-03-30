---
title: Konfigurowanie automatycznej skali wystąpienia usługi Azure API Management | Dokumenty firmy Microsoft
description: W tym temacie opisano sposób konfigurowania zachowania skalowania automatycznego dla wystąpienia usługi Azure API Management.
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
ms.openlocfilehash: 8c1c96fdb1f4f42c7592791881b855f74d411171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018292"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Automatyczne skalowanie wystąpienia usługi Azure API Management  

Wystąpienie usługi Usługi Azure API Management można skalować automatycznie na podstawie zestawu reguł. To zachowanie można włączyć i skonfigurować za pośrednictwem usługi Azure Monitor i jest obsługiwane tylko w warstwach **Standardowa** i **Premium** usługi Azure API Management.

W artykule przechodzi przez proces konfigurowania skalowania automatycznego i sugeruje optymalną konfigurację reguł skalowania automatycznego.

> [!NOTE]
> Usługa api Management w warstwie **Zużycie** skaluje się automatycznie na podstawie ruchu — bez konieczności dodatkowej konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki z tego artykułu, należy:

+ Mieć aktywną subskrypcję platformy Azure.
+ Mieć wystąpienie usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Poznaj pojęcie [pojemności wystąpienia](api-management-capacity.md)usługi Azure API Management .
+ Opis [ręcznego procesu skalowania wystąpienia usługi Azure API Management,](upgrade-and-scale.md)w tym konsekwencji kosztów.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Ograniczenia skalowania automatycznego usługi Azure API Management

Niektóre ograniczenia i konsekwencje decyzji skalowania należy wziąć pod uwagę przed skonfigurowaniem zachowania skalowania automatycznego.

+ Skalowanie automatyczne można włączyć tylko dla **warstwy Standardowa** i **Premium** usługi Azure API Management.
+ Warstwy cenowe określają również maksymalną liczbę jednostek dla wystąpienia usługi.
+ Proces skalowania zajmie co najmniej 20 minut.
+ Jeśli usługa jest zablokowana przez inną operację, żądanie skalowania zakończy się niepowodzeniem i ponowi próbę automatycznie.
+ W przypadku usługi z wdrożeń wieloregionalnych tylko jednostki w **lokalizacji podstawowej** mogą być skalowane. Jednostek w innych lokalizacjach nie można skalować.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Włączanie i konfigurowanie skalowania automatycznego dla usługi Azure API Management

Wykonaj poniższe czynności, aby skonfigurować skalowanie automatyczne dla usługi Azure API Management:

1. Przejdź do **wystąpienia monitoruj** w witrynie Azure portal.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Wybierz **opcję Skalowanie automatyczne** z menu po lewej stronie.

    ![Zasób automatycznej skali usługi Azure Monitor](media/api-management-howto-autoscale/02.png)

3. Znajdź usługę Azure API Management na podstawie filtrów w menu rozwijanych.
4. Wybierz żądane wystąpienie usługi Usługi Azure API Management.
5. W nowo otwartej sekcji kliknij przycisk **Włącz skalowanie automatyczne.**

    ![Automatyczne skalowanie monitora platformy Azure](media/api-management-howto-autoscale/03.png)

6. W sekcji **Reguły** kliknij przycisk **+ Dodaj regułę**.

    ![Reguła dodawania automatycznego skalowania monitora platformy Azure](media/api-management-howto-autoscale/04.png)

7. Zdefiniuj nową regułę skalowania w poziomie.

   Na przykład reguła skalowania w poziomie może wyzwolić dodanie jednostki usługi Azure API Management, gdy średnia metryka pojemności w ciągu ostatnich 30 minut przekracza 80%. Poniższa tabela zawiera konfigurację takiej reguły.

    | Parametr             | Wartość             | Uwagi                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Źródło metryki         | Bieżący zasób  | Zdefiniuj regułę na podstawie bieżących metryk zasobów usługi Azure API Management.                                                                                                                                                                                                     |
    | *Kryteria*            |                   |                                                                                                                                                                                                                                                                                 |
    | Agregacja czasu      | Średnia           |                                                                                                                                                                                                                                                                                 |
    | Nazwa metryki           | Pojemność          | Metryka pojemności to metryka usługi Azure API Management odzwierciedlającya użycie zasobów wystąpienia usługi Azure API Management.                                                                                                                                                            |
    | Statystyka ziarna czasu  | Średnia           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Większe niż      |                                                                                                                                                                                                                                                                                 |
    | Próg             | 80%               | Próg dla metryki zdolności produkcyjnej uśredniony.                                                                                                                                                                                                                                 |
    | Czas trwania (w minutach) | 30                | Okres, aby uśrednić metrykę pojemności nad jest specyficzne dla wzorców użycia. Im dłuższy jest okres, tym gładsza będzie reakcja - przerywane kolce będą miały mniejszy wpływ na decyzję o skali w poziomie. Jednak będzie również opóźnić wyzwalacz skalowania w poziomie. |
    | *Akcja*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operacja             | Zwiększ licznik o |                                                                                                                                                                                                                                                                                 |
    | Liczba wystąpień        | 1                 | Skalowanie w poziomie wystąpienia usługi Azure API Management przez 1 jednostkę.                                                                                                                                                                                                                          |
    | Czas schładzania (minuty)   | 60                | Skalowanie w poziomie usługi Azure API Management zajmuje co najmniej 20 minut. W większości przypadków okres schładzania wynoszący 60 minut uniemożliwia wyzwalanie wielu skalowanych wyjęć.                                                                                                  |

8. Kliknij **przycisk Dodaj,** aby zapisać regułę.

    ![Reguła skalowania w poziomie usługi Azure Monitor](media/api-management-howto-autoscale/05.png)

9. Kliknij ponownie na **+ Dodaj regułę**.

    Tym razem należy zdefiniować skalę reguły. Zapewni to, że zasoby nie są marnowane, gdy zmniejsza się użycie interfejsów API.

10. Zdefiniuj nową skalę w regule.

    Na przykład skala w regule może wyzwolić usunięcie jednostki usługi Azure API Management, gdy średnia metryka pojemności w ciągu ostatnich 30 minut była niższa niż 35%. Poniższa tabela zawiera konfigurację takiej reguły.

    | Parametr             | Wartość             | Uwagi                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Źródło metryki         | Bieżący zasób  | Zdefiniuj regułę na podstawie bieżących metryk zasobów usługi Azure API Management.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Kryteria*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Agregacja czasu      | Średnia           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nazwa metryki           | Pojemność          | Taka sama metryka, jak ta używana dla reguły skalowania w poziomie.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Statystyka ziarna czasu  | Średnia           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Mniejsze niż         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Próg             | 35%               | Podobnie jak reguła skalowania w poziomie, ta wartość w dużej mierze zależy od wzorców użycia usługi Azure API Management. |
    | Czas trwania (w minutach) | 30                | Taką samą wartość jak ta używana dla reguły skalowania w poziomie.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Akcja*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operacja             | Zmniejsz licznik o | W przeciwieństwie do tego, co zostało użyte do reguły skalowania w poziomie.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Liczba wystąpień        | 1                 | Taką samą wartość jak ta używana dla reguły skalowania w poziomie.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Czas schładzania (minuty)   | 90                | Skala powinna być bardziej konserwatywna niż skala w poziomie, więc okres schładzać powinien być dłuższy.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Kliknij **przycisk Dodaj,** aby zapisać regułę.

    ![Skalowanie usługi Azure Monitor w regule](media/api-management-howto-autoscale/06.png)

12. Ustaw **maksymalną** liczbę jednostek usługi Azure API Management.

    > [!NOTE]
    > Usługa Azure API Management ma limit jednostek, do które wystąpienie można skalować w poziomie. Limit zależy od warstwy usług.

    ![Skalowanie usługi Azure Monitor w regule](media/api-management-howto-autoscale/07.png)

13. Kliknij przycisk **Zapisz**. Skalowanie automatyczne zostało skonfigurowane.

## <a name="next-steps"></a>Następne kroki

+ [Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach świadczenia usługi Azure](api-management-howto-deploy-multi-region.md)
