---
title: Pojemność wystąpienia usługi Azure API Management | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, co jest metryka pojemności i jak podejmować świadome decyzje, czy skalować wystąpienie usługi Azure API Management.
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
ms.custom: fasttrack-edit
ms.openlocfilehash: b6d949b50be348e72cedfc3710383308b04de106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336006"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Pojemność wystąpienia usługi Azure API Management

**Pojemność** jest najważniejszą [metryką usługi Azure Monitor](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) do podejmowania świadomych decyzji, czy skalować wystąpienie zarządzania interfejsami API, aby pomieścić większe obciążenie. Jego konstrukcja jest złożona i narzuca pewne zachowanie.

W tym artykule wyjaśniono, co to jest **pojemność** i jak się zachowuje. Pokazano, jak uzyskać dostęp do metryk **pojemności** w witrynie Azure portal i sugeruje, kiedy należy rozważyć skalowanie lub uaktualnianie wystąpienia usługi API Management.

> [!IMPORTANT]
> W tym artykule omówiono sposób monitorowania i skalowania wystąpienia usługi Azure API Management na podstawie jego metryki pojemności. Jednak równie ważne jest, aby zrozumieć, co się dzieje, gdy poszczególne wystąpienie usługi API Management rzeczywiście *osiągnął* swoją pojemność. Usługa Azure API Management nie będzie stosować żadnych ograniczania poziomu usługi, aby zapobiec fizycznemu przeciążeniu wystąpień. Gdy wystąpienie osiągnie swoją pojemność fizyczną, będzie zachowywać się podobnie do dowolnego przeciążonego serwera sieci web, który nie jest w stanie przetworzyć przychodzących żądań: opóźnienie wzrośnie, połączenia zostaną usunięte, wystąpią błędy limitu czasu itp. Oznacza to, że klienci API powinni być przygotowani na radzenie sobie z tą możliwością podobną jak w przypadku każdej innej usługi zewnętrznej (np. poprzez zastosowanie zasad ponawiania).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki z tego artykułu, musisz mieć:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie interfejsu APIM. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Co to jest pojemność

![Metryka pojemności](./media/api-management-capacity/capacity-ingredients.png)

**Pojemność** jest wskaźnikiem obciążenia wystąpienia zarządzania interfejsami API. Odzwierciedla użycie zasobów (procesora CPU, pamięci) i długości kolejek sieciowych. Użycie procesora i pamięci ujawnia zużycie zasobów przez:

+ Usługi płaszczyzny danych zarządzania interfejsem API, takie jak przetwarzanie żądań, które mogą obejmować przekazywanie żądań lub uruchamianie zasad.
+ Usługi płaszczyzny zarządzania zarządzaniem interfejsami API, takie jak akcje zarządzania stosowane za pośrednictwem witryny Azure Portal lub ARM, lub obciążenie pochodzące z [portalu dla deweloperów.](api-management-howto-developer-portal.md)
+ Wybrane procesy systemu operacyjnego, w tym procesy, które wiążą się z kosztem uzgadniania TLS w nowych połączeniach.

Całkowita **pojemność** jest średnią własnych wartości z każdej jednostki wystąpienia usługi API Management.

Chociaż **metryka pojemności** jest przeznaczona do powierzchni problemów z wystąpieniem usługi API Management, istnieją przypadki, gdy problemy nie zostaną odzwierciedlone w zmianach w **metryki pojemności**.

## <a name="capacity-metric-behavior"></a>Zachowanie metryki pojemności

Ze względu na jego budowę, w rzeczywistych **możliwościach** mogą mieć wpływ wiele zmiennych, na przykład:

+ wzorce połączeń (nowe połączenie na żądanie a ponowne podłączenie istniejącego połączenia)
+ wielkość wniosku i odpowiedzi
+ zasady skonfigurowane w każdym interfejsie API lub liczba klientów wysyłających żądania.

Im bardziej złożone operacje na żądaniach, tym większe będzie zużycie **pojemności.** Na przykład zasady transformacji złożonej zużywają znacznie więcej procesora CPU niż proste przekazywanie żądań. Powolne odpowiedzi usługi wewnętrznej bazy danych również ją zwiększą.

> [!IMPORTANT]
> **Pojemność** nie jest bezpośrednią miarą liczby przetwarzanych żądań.

![Skoki metryki pojemności](./media/api-management-capacity/capacity-spikes.png)

**Pojemność** może również skok sporadycznie lub być większa niż zero, nawet jeśli nie są przetwarzane żadne żądania. Dzieje się tak z powodu działań specyficznych dla systemu lub platformy i nie powinny być brane pod uwagę przy podejmowaniu decyzji, czy skalować wystąpienie.

**Metryka o niskiej pojemności** nie musi oznaczać, że wystąpienie usługi API Management nie ma żadnych problemów.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Za pomocą portalu Azure portal do badania pojemności
  
![Metryka pojemności](./media/api-management-capacity/capacity-metric.png)  

1. Przejdź do wystąpienia interfejsu APIM w [witrynie Azure portal](https://portal.azure.com/).
2. Wybierz pozycję **Metryki**.
3. W sekcji purpurowej wybierz **metrykę pojemności** z dostępnych metryk i pozostaw domyślną agregację **Avg.**

    > [!TIP]
    > Należy zawsze patrzeć na podział metryki **pojemności** na lokalizację, aby uniknąć błędnych interpretacji.

4. W sekcji zielonej wybierz **pozycję Lokalizacja, aby** podzielić metrykę według wymiaru.
5. Wybierz żądany przedział czasu z górnego paska sekcji.

    Można ustawić alert metryki, aby poinformować, gdy coś nieoczekiwanego się dzieje. Na przykład otrzymuj powiadomienia, gdy wystąpienie interfejsu APIM przekracza oczekiwaną maksymalną pojemność przez ponad 20 minut.

    >[!TIP]
    > Można skonfigurować alerty, aby poinformować, gdy usługa jest na wyczerpaniu pojemności lub użyć funkcji skalowania automatycznego usługi Azure Monitor, aby automatycznie dodać jednostkę usługi Azure API Management. Skalowanie operacji może potrwać około 30 minut, więc należy odpowiednio zaplanować reguły.  
    > Dozwolone jest tylko skalowanie lokalizacji głównej.

## <a name="use-capacity-for-scaling-decisions"></a>Wykorzystanie zdolności do skalowania decyzji

**Pojemność** jest metryką do podejmowania decyzji, czy skalować wystąpienie usługi API Management, aby pomieścić większe obciążenie. Rozważ następujące kwestie:

+ Patrząc na długoterminowy trend i średnią.
+ Ignorowanie nagłych skoków, które najprawdopodobniej nie są związane ze wzrostem obciążenia (zobacz sekcję "Zachowanie metryki pojemności", aby uzyskać wyjaśnienie).
+ Uaktualnianie lub skalowanie wystąpienia, gdy wartość **pojemności**przekracza 60% lub 70% przez dłuższy okres czasu (na przykład 30 minut). Różne wartości mogą działać lepiej dla usługi lub scenariusza.

>[!TIP]  
> Jeśli jesteś w stanie oszacować ruch wcześniej, przetestować wystąpienie interfejsu APIM na obciążeń, których oczekujesz. Można stopniowo zwiększać obciążenie żądania na dzierżawie i monitorować, jaka wartość metryki pojemności odpowiada poziomowi obciążenia szczytowego. Wykonaj kroki z poprzedniej sekcji, aby użyć witryny Azure portal, aby dowiedzieć się, ile pojemności jest używane w danym momencie.

## <a name="next-steps"></a>Następne kroki

[Jak skalować lub uaktualniać wystąpienie usługi Usługi Azure API Management](upgrade-and-scale.md)