---
title: Uaktualnienie i skalować wystąpienia usługi Azure API Management | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób uaktualniania i skalowanie wystąpienia usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6ae977344101c02222fd9930e26a083bf5e3f800
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2017
ms.locfileid: "26658640"
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Uaktualnienie i skalować wystąpienia interfejsu API zarządzania 

Klientów można skalować wystąpienia interfejsu API zarządzania (APIM), przez dodawanie i usuwanie jednostek. A **jednostki** składa się z dedykowanym zasobów platformy Azure i ma niektórych nośnych pojemności wyrażone jako liczba interfejsu API wywołania miesięcznie. Ta liczba nie reprezentuje ograniczenie wywołań, ale raczej wartość maksymalną przepustowość do obsługi planowania pojemności nierównej. Rzeczywiste przepustowości i opóźnień szeroko zależy od czynników, takich jak numer i liczba równoczesnych połączeń, rodzaj i liczbę skonfigurowanych zasad, rozmiary żądań i odpowiedzi i opóźnienia wewnętrznej bazy danych.

Pojemność i Cena każdej jednostki zależy od **warstwy** , w którym istnieje jednostki. Można wybrać jedną z czterech poziomów: **Developer**, **podstawowe**, **standardowe**, **Premium**. Jeśli potrzebujesz zwiększyć pojemność dla usługi w ramach warstwy, należy dodać jednostkę. Warstwę, która jest aktualnie wybrane w wystąpieniu APIM nie zezwala na dodawanie większej liczby jednostek, należy uaktualnić do warstwy wyższego poziomu. 

Cena każdej jednostki i dostępnych funkcji (na przykład w przypadku wdrażania) zależy od warstwy, która została wybrana opcja APIM wystąpienia. [Szczegóły cennika](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artykułu, wyjaśnia cena za jednostkę i funkcje, które otrzymujesz w ramach poszczególnych warstw. 

>[!NOTE]
>[Szczegóły cennika](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artykule przedstawiono przybliżonej liczby pojemność jednostki w każdej warstwie. Aby uzyskać dokładniejsze liczb, należy przyjrzeć się realistyczne scenariusz dla interfejsów API. Zobacz sekcję "Jak planowania pojemności" poniżej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musi mieć:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie APIM. Aby uzyskać więcej informacji, zobacz [utworzenia wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Jak zaplanować pojemność?

Aby dowiedzieć się, jeśli masz za mało jednostki do obsługi ruchu, testu na obciążeń, których się oczekuje. 

Jak wspomniano powyżej, liczba żądań na sekundę, który może przetwarzać jednostki APIM zależy od wielu zmiennych. Na przykład połączenie wzorzec, rozmiar żądania i odpowiedzi, zasady, które są konfigurowane na poszczególnych interfejsu API, liczba klientów, którzy wysyłają żądania.

Użyj **metryki** (korzysta z funkcji Azure monitora) aby zrozumieć, jaka pojemność jest używany w danym momencie.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Użyj portalu Azure, aby sprawdzić metryki 

1. Przejdź do Twojego wystąpienia APIM w [portalu Azure](https://portal.azure.com/).
2. Wybierz **metryki**.
3. Wybierz **pojemności** metryki z **dostępne metryki**. 

    Metryki pojemności udostępnia informacje o niektórych tym ile wydajności obliczeniowej dostępne jest używany w dzierżawie. Wartość jest pochodną zasoby obliczeniowe, używany przez dzierżawy, np. pamięci, procesora CPU i długości kolejki w sieci. Nie jest bezpośrednio miarą liczba aktualnie przetwarzanych żądań. Możesz przetestować, przez co zwiększa obciążenie żądanie dzierżawy i monitorowania, jakie wartości metryki pojemności odpowiada obciążenia szczytowego. Można ustawić metryki alert z informacją, gdy wykonywane jest nieoczekiwane. Na przykład wystąpienie APIM przekroczyła pojemności oczekiwanego szczytowego ponad 10 minut.

    >[!TIP]
    > Można skonfigurować alerty pozwala wiedzieć, kiedy usługa zaczyna brakować pojemności, lub zadzwoń do aplikacji logiki, które automatycznie skalować, dodając jednostki.

## <a name="upgrade-and-scale"></a>Uaktualnienie i skali 

Jak wspomniano wcześniej, można wybrać jedną z czterech poziomów: **Developer**, **podstawowe**, **standardowe** i **Premium**. **Developer** warstwy mają być używane do oceny usługi; nie powinna być używana w środowisku produkcyjnym. **Developer** warstwy nie ma umowy dotyczącej poziomu usług i nie można skalować tę warstwę (dodawania i usuwania jednostek). 

**Podstawowe**, **standardowe** i **Premium** są warstw produkcji, umowy dotyczącej poziomu usług, które mogą być skalowane. **Podstawowe** warstwa ma warstwy najtańszej, którego umowa SLA i może być skalowana maksymalnie 2 jednostki, **standardowe** warstwy mogą być skalowane do maksymalnie cztery jednostki. Można dodać dowolną liczbę jednostek **Premium** warstwy.

**Premium** warstwy umożliwia rozpowszechniają pojedynczego wystąpienia interfejsu API zarządzania dowolną liczbę żądaną regiony platformy Azure. Po utworzeniu początkowo usługi Zarządzanie interfejsami API, wystąpienie zawiera tylko jedną jednostkę i znajduje się w pojedynczym regionie Azure. Początkowa region jest określany jako **głównej** regionu. Łatwo można dodawać dodatkowe regionów. Podczas dodawania regionu, możesz określić liczbę jednostek, które mają zostać przydzielone. Na przykład można mieć jedną jednostkę **głównej** regionu i pięć jednostki w niektórych inny region. Liczba jednostek do ruchu sieciowego w każdym regionie można dostosować. Aby uzyskać więcej informacji, zobacz [sposób wdrażania wystąpienia usługi Azure API Management do wielu regionów platformy Azure](api-management-howto-deploy-multi-region.md).

Można uaktualnić i obniżyć do i z wszelkich warstwy. Należy pamiętać, że uaktualnienia lub zmiany na starszą wersję można usunąć niektórych funkcji — na przykład sieci wirtualnych lub w przypadku wdrożenia, jeśli zmiany na starszą wersję Standard lub podstawowa z warstwy Premium.

>[!NOTE]
>Proces uaktualniania lub skalę zastosowanie może potrwać od 15 do 45 minut. Otrzymasz powiadomienie po zakończeniu.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Użyj portalu Azure do uaktualnienia i skalowanie

1. Przejdź do Twojego wystąpienia APIM w [portalu Azure](https://portal.azure.com/).
2. Wybierz **skali i cenach**.
3. Wybierz żądaną warstwę.
4. Określ liczbę **jednostki** chcesz dodać. Można za pomocą suwaka lub wpisz liczbę jednostek.<br/>
    Jeśli wybierzesz **Premium** warstwy, należy najpierw wybrać region.
5. Naciśnij klawisz **Zapisz**

## <a name="next-steps"></a>Następne kroki

[Jak wdrożyć wystąpienia usługi Azure API Management na wiele regionów platformy Azure](api-management-howto-deploy-multi-region.md)

