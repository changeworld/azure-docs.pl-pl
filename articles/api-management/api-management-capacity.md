---
title: Pojemność wystąpienia usługi Azure API Management | Dokumentacja firmy Microsoft
description: W tym artykule opisano metryki pojemności jest oraz do podejmowania świadomych decyzji, czy można skalować wystąpienia usługi Azure API Management.
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
ms.openlocfilehash: 4983854a14a6efe9214692dc677dedeada73933b
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296106"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Pojemność wystąpienia usługi Azure API Management

**Pojemność** jedna, najważniejszych [Metryka Azure Monitor](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) przy podejmowaniu świadomych decyzji, czy skalowania obsłużyć obciążenie więcej wystąpienia interfejsu API zarządzania. Konstrukcja jest złożony i nakłada określone zachowanie.

W tym artykule opisano, co **pojemności** jest i jak działa. Widoczny jest sposób dostępu **pojemności** metryki w portalu Azure, a także sugeruje, kiedy należy wziąć pod uwagę skalowanie lub uaktualniania wystąpienia interfejsu API zarządzania.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w tym artykule, wymaga:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie APIM. Aby uzyskać więcej informacji, zobacz [utworzenia wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

## <a name="what-is-capacity"></a>Co to jest pojemność

![Metryki pojemności](./media/api-management-capacity/capacity-ingredients.png)

**Pojemność** jest wskaźnikiem obciążenia w wystąpieniu APIM. Odzwierciedla użycia zasobów (procesora CPU, pamięci) i długości kolejki w sieci. Użycie procesora CPU i pamięci ujawnia zużycia zasobów przez:

+ APIM usług, takich jak zarządzanie akcje lub żądania przetwarzania, obejmujące przekazywania żądań lub uruchamianie zasad
+ procesów wybranego systemu operacyjnego, w tym procesów obejmujących koszt uzgodnienia SSL na nowe połączenia.

Całkowita liczba **pojemności** jest średnią z wartości z każdej jednostki wystąpienia interfejsu API zarządzania.

## <a name="capacity-metric-behavior"></a>Zachowanie metryki pojemności

Ze względu na jego konstrukcji w rzeczywistych **pojemności** może mieć wpływ na wiele zmiennych, na przykład:

+ wzorce połączenia (nowe połączenie vs żądania, ponowne wykorzystywanie istniejących połączeń)
+ rozmiar żądania i odpowiedzi
+ zasady skonfigurowane dla każdego interfejsu API lub liczbę klientów, którzy wysyłają żądania.

Są bardziej złożonych operacji w odpowiedzi na żądania, wyższy **pojemności** będą zużycie. Na przykład zasad przekształcania złożonych używać Procesora większą niż przekazywania prostego żądania. Odpowiedzi usługi powolne wewnętrznej bazy danych spowoduje zwiększenie jego zbyt.

> [!IMPORTANT]
> **Pojemność** nie jest bezpośrednio miarą liczba aktualnie przetwarzanych żądań.

![Nagłego metryki pojemności](./media/api-management-capacity/capacity-spikes.png)

**Pojemność** również sporadycznie chwilowo wzrastają lub być większa od zera, nawet jeśli nie ma przetwarzanych żądań. Się dzieje z powodu akcji specyficzne dla systemu lub platformy, a nie powinien brana pod uwagę, podejmując decyzję o skalowanie wystąpienia.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Aby sprawdzić pojemność za pomocą portalu Azure
  
![Metryki pojemności](./media/api-management-capacity/capacity-metric.png)  

1. Przejdź do Twojego wystąpienia APIM w [portalu Azure](https://portal.azure.com/).
2. Wybierz **metryki (wersja zapoznawcza)**.
3. Wybierz z sekcji purpurowa **pojemności** metryki z dostępnymi metrykami i pozostaw domyślnie **Śr.** agregacji.

    > [!TIP]
    > Zawsze należy zwrócić uwagę na **pojemności** metryki podział według lokalizacji, aby uniknąć interpretacje niewłaściwy.

4. W sekcji zielony, wybierz **lokalizacji** do dzielenia Metryka według wymiaru.
5. Wybierz żądaną przedział czasu, z górnym pasku sekcji.

    Można ustawić metryki alert z informacją, gdy wykonywane jest nieoczekiwane. Na przykład otrzymywać powiadomienia, gdy wystąpienie APIM został exceededing pojemności oczekiwanego szczytowego przez ponad 20 minut.

    >[!TIP]
    > Można skonfigurować alerty, aby umożliwić wiedzieć, kiedy usługa zaczyna brakować pojemności lub za pomocą funkcji skalowania automatycznego Azure Monitor można automatycznie dodać jednostkę usługi Azure API Management. Skalowanie operacji może zająć około 30 minut, dlatego należy odpowiednio zaplanować reguł.  
    > Dozwolone jest tylko skalowanie lokalizacji głównej.

## <a name="use-capacity-for-scaling-decisions"></a>Pojemność skalowania decyzji

**Pojemność** jest metryka przy podejmowaniu decyzji, czy można skalować wystąpienia interfejsu API zarządzania obsłużyć więcej obciążenie. Należy wziąć pod uwagę:

+ Spojrzenie na trend długoterminowej i średnia.
+ Zostanie zignorowany nagłym nagłego, które są najbardziej prawdopodobną nie dotyczą wzrost obciążenia (zobacz sekcję "Zachowanie metryki pojemności" Opis).
+ Uaktualnianie i skalowanie wystąpienia, gdy **pojemność**w wartość przekracza 60% lub 70% dłuższy okres czasu (na przykład 30 minut). Różne wartości mogą działać lepiej dla usługi lub scenariusza.

>[!TIP]  
> Jeśli jesteś w stanie wcześniej oszacować ruchu, należy przetestować wystąpienia APIM na obciążenie oczekiwanych. Można to zwiększenie obciążenia żądania w dzierżawie stopniowo i monitorować, jakie wartości metryki pojemności odpowiada obciążenia szczytowego. Wykonaj kroki opisane w poprzedniej sekcji, aby zrozumieć, jaka pojemność jest używane w danej chwili za pomocą portalu Azure.

## <a name="next-steps"></a>Kolejne kroki

[Jak skalować lub Uaktualnij wystąpienie usługi Zarządzanie interfejsami API Azure](upgrade-and-scale.md)