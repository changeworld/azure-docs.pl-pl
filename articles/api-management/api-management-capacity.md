---
title: Pojemność wystąpienia usługi Azure API Management | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jakie metryki pojemności i jak do podejmowania świadomych decyzji, czy wykonać skalowanie wystąpienia usługi Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: fe77361c4c9bed9310f8443ed4ff37faf7ea53a9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60658334"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Pojemność wystąpienia usługi Azure API Management

**Pojemność** jest pojedynczą najważniejszą [metryki usługi Azure Monitor](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) dokonywania świadomych decyzji, czy wykonać skalowanie wystąpienia usługi API Management, aby pomieścić większe obciążenie. Konstrukcja jest złożona i nakłada pewne zachowanie.

W tym artykule opisano, jakie **pojemności** jest i jak działa. Pokazuje, jak uzyskać dostęp do **pojemności** metryki w witrynie Azure portal, a także sugeruje, kiedy należy rozważyć skalowanie lub uaktualnienie wystąpienia usługi API Management.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki z tego artykułu, musisz mieć:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie usługi APIM. Aby uzyskać więcej informacji, zobacz [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Co to jest pojemność

![Metryka pojemności](./media/api-management-capacity/capacity-ingredients.png)

**Pojemność** stanowi wskaźnik obciążenie wystąpienia usługi APIM. Odzwierciedla użycie zasobów (procesor CPU, pamięci) i długości kolejki sieci. Użycie procesora CPU i pamięci, co spowoduje wyświetlenie zużycie zasobów:

+ Usługi APIM, takie jak zarządzanie akcji lub żądania przetwarzania, wraz z ewentualnym przekazuje żądania, czy używasz zasad
+ procesów wybranego systemu operacyjnego, w tym procesy, które obejmują koszt uzgodnienia protokołu SSL na nowe połączenia.

Łączna liczba **pojemności** jest średnią z wartości z każdej jednostki wystąpienia usługi API Management.

## <a name="capacity-metric-behavior"></a>Zachowanie metryki pojemności

Ze względu na jej konstrukcji w realnym **pojemności** może mieć wpływ na wiele zmiennych, na przykład:

+ wzorce połączenia (nowe połączenie przy użyciu żądania ponownego wykorzystania istniejącego połączenia)
+ rozmiar żądania i odpowiedzi
+ zasady skonfigurowane dla każdego interfejsu API lub liczbę klientów, którzy wysyłają żądania.

Są bardziej złożonych operacji w odpowiedzi na żądania, tym wyżej **pojemności** będzie zużycia. Na przykład zasady skomplikowaną transformację wymagały znacznie większej mocy Procesora niż przekazywanie prostego żądania. Odpowiedzi usługi zaplecza powolne zwiększy się on zbyt.

> [!IMPORTANT]
> **Pojemność** nie jest bezpośrednie miarą liczba aktualnie przetwarzanych żądań.

![Wartości graniczne na metryki wydajności](./media/api-management-capacity/capacity-spikes.png)

**Pojemność** również skacze sporadycznie lub być większa od zera, nawet jeśli żadne żądanie przetwarzane. On dziać się z powodu akcje specyficzne dla systemu lub platformy i powinny nie należy brać pod uwagę przy podejmowaniu decyzji, które można skalować wystąpienia.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Użyj witryny Azure Portal, aby sprawdzić pojemność
  
![Metryka pojemności](./media/api-management-capacity/capacity-metric.png)  

1. Przejdź do swojego wystąpienia usługi APIM w [witryny Azure portal](https://portal.azure.com/).
2. Wybierz **metryki (wersja zapoznawcza)** .
3. W części fioletowym, wybierz **pojemności** metryk dostępnych metryk i pozostaw domyślny **Avg** agregacji.

    > [!TIP]
    > Zawsze należy rozważyć **pojemności** metryki podział według lokalizacji w celu uniknięcia interpretacji problem.

4. W sekcji zielony, wybierz **lokalizacji** do dzielenia metryki według wymiaru.
5. Wybierz żądany przedział czasu, z górnym pasku sekcji.

    Można ustawić alert dotyczący metryki informacją o tym, kiedy się coś nieoczekiwanego dzieje. Na przykład Otrzymuj powiadomienia, gdy wystąpienie usługi APIM zostały przekraczające pojemności oczekiwanego szczytowego przez ponad 20 minut.

    >[!TIP]
    > Można skonfigurować alerty, aby umożliwić wiadomo, kiedy usługa zaczyna brakować pojemności lub automatycznie dodawać jednostki usługi Azure API Management za pomocą funkcji skalowania automatycznego usługi Azure Monitor. Skalowanie w operacji może zająć około 30 minut, dlatego należy odpowiednio zaplanować reguł.  
    > Dozwolona jest tylko skalowanie lokalizacji głównej.

## <a name="use-capacity-for-scaling-decisions"></a>Używana pojemność decyzji dotyczących skalowania

**Pojemność** jest metryka przy podejmowaniu decyzji, czy wykonać skalowanie wystąpienia usługi API Management, aby pomieścić większe obciążenie. Należy wziąć pod uwagę:

+ Patrząc długoterminowych trendów i średnia.
+ Ignorowanie nagłe wartości graniczne, które z największym prawdopodobieństwem mogą nie dotyczą wzrost obciążenia (zobacz sekcję "Zachowanie metryki pojemności" wyjaśnienie).
+ Uaktualnianie lub skalowania wystąpienia, gdy **pojemności**firmy wartość przekracza 60% lub 70% przez dłuższy czas, w czasie (na przykład 30 minut). Różne wartości może działać lepiej dla usługi lub scenariusza.

>[!TIP]  
> Jeśli jesteś w stanie wcześniej oszacować ruchu, należy przetestować swojego wystąpienia usługi APIM dla obciążeń, których oczekujesz. Można stopniowo zwiększać obciążenie żądaniami w dzierżawie usługi, a następnie monitorować, jaka wartość metryki pojemności odnosi się do obciążenia szczytowego. Postępuj zgodnie z instrukcjami z poprzedniej sekcji, aby zrozumieć, jaka pojemność jest używany w dowolnym momencie za pomocą witryny Azure portal.

## <a name="next-steps"></a>Kolejne kroki

[Jak skalować lub Uaktualnij wystąpienie usługi Azure API Management](upgrade-and-scale.md)