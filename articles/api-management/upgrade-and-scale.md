---
title: Uaktualnianie i skalowanie wystąpienia usługi Azure API Management | Dokumenty firmy Microsoft
description: W tym temacie opisano sposób uaktualniania i skalowania wystąpienia usługi Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018233"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Uaktualnianie i skalowanie wystąpienia usługi Azure API Management  

Klienci mogą skalować wystąpienie usługi Azure API Management (APIM), dodając i usuwając jednostki. **Jednostka** składa się z dedykowanych zasobów platformy Azure i ma pewną nośność wyrażoną jako liczba wywołań interfejsu API miesięcznie. Ten numer nie reprezentuje limitu wywołań, ale raczej maksymalną wartość przepływności, aby umożliwić planowanie pojemności przybliżone. Rzeczywista przepływność i opóźnienie różnią się zasadniczo w zależności od czynników, takich jak liczba i szybkość równoczesnych połączeń, rodzaj i liczba skonfigurowanych zasad, rozmiary żądań i odpowiedzi oraz opóźnienie wewnętrznej bazy danych.

Pojemność i cena każdej jednostki zależy od **warstwy,** w której jednostka istnieje. Do wyboru są cztery warstwy: **Deweloper,** **Podstawowy,** **Standardowy,** **Premium.** Jeśli musisz zwiększyć pojemność usługi w warstwie, należy dodać jednostkę. Jeśli warstwa, która jest aktualnie zaznaczona w wystąpieniu interfejsu APIM, nie zezwala na dodawanie większej liczby jednostek, należy uaktualnić do warstwy wyższego poziomu.

Cena każdej jednostki i dostępnych funkcji (na przykład wdrożenia w wielu regionach) zależy od warstwy wybranej dla wystąpienia interfejsu APIM. Artykuł [ze szczegółami cen,](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) wyjaśnia cenę za jednostkę i funkcje, które otrzymujesz w każdej warstwie. 

>[!NOTE]
>W [artykule szczegółów cen przedstawiono](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przybliżoną liczbę zdolności produkcyjnych jednostki w każdej warstwie. Aby uzyskać dokładniejsze liczby, należy przyjrzeć się realistycznemu scenariuszowi interfejsów API. Zobacz [pojemność](api-management-capacity.md) wystąpienia usługi Azure API Management.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki z tego artykułu, należy:

+ Mieć aktywną subskrypcję platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Mieć wystąpienie interfejsu APIM. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

+ Poznaj pojęcie [pojemności wystąpienia](api-management-capacity.md)usługi Azure API Management .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Uaktualnianie i skalowanie  

Możesz wybrać jedną z czterech warstw: **Developer**, **Basic**, **Standard** i **Premium**. Warstwa **dewelopera** powinna służyć do oceny usługi; nie powinny być wykorzystywane do produkcji. Warstwa **Deweloper** nie ma umowy SLA i nie można skalować tej warstwy (jednostek dodawania/usuwania). 

**Podstawowe,** **Standardowe** i **Premium** to warstwy produkcyjne, które mają umowy SLA i mogą być skalowane. Warstwa **Podstawowa** jest najtańszą warstwą, która ma umowy SLA i może być skalowana do 2 jednostek, **warstwa standardowa** może być skalowana do maksymalnie czterech jednostek. Możesz dodać dowolną liczbę jednostek do poziomu **premium.**

Warstwa **Premium** umożliwia dystrybucję pojedynczego wystąpienia usługi Azure API Management w dowolnej liczbie żądanych regionów platformy Azure. Podczas początkowego tworzenia usługi Azure API Management, wystąpienie zawiera tylko jedną jednostkę i znajduje się w jednym regionie platformy Azure. Region początkowy jest wyznaczony jako region **podstawowy.** Można łatwo dodać dodatkowe regiony. Podczas dodawania regionu należy określić liczbę jednostek, które mają zostać przydzielenia. Na przykład można mieć jedną jednostkę w regionie **podstawowym** i pięć jednostek w innym regionie. Można dostosować liczbę jednostek do ruchu, który masz w każdym regionie. Aby uzyskać więcej informacji, zobacz [Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach platformy Azure.](api-management-howto-deploy-multi-region.md)

Można uaktualnić i obniżyć do iz dowolnej warstwy. Należy zauważyć, że uaktualnianie lub obniżanie wersji może usunąć niektóre funkcje — na przykład wirtualne sieci wirtualne lub wdrożenie w wielu regionach, gdy przejście na poziom standardowy lub podstawowy z warstwy Premium.

> [!NOTE]
> Proces uaktualniania lub skalowania może potrwać od 15 do 45 minut. Otrzymasz powiadomienie, gdy to zrobisz.

> [!NOTE]
> Usługa API Management w warstwie **Zużycie** jest skalowana automatycznie na podstawie ruchu.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Uaktualnianie i skalowanie za pomocą portalu Azure

![Skalowanie interfejsu APIM w witrynie Azure portal](./media/upgrade-and-scale/portal-scale.png)

1. Przejdź do wystąpienia interfejsu APIM w [witrynie Azure portal](https://portal.azure.com/).
2. Z menu **wybierz opcję Skaluj i cennik.**
3. Wybierz żądaną warstwę.
4. Określ liczbę **jednostek,** które chcesz dodać. Można użyć suwaka lub wpisać liczbę jednostek.  
    Jeśli wybierzesz warstwę **Premium,** musisz najpierw wybrać region.
5. Naciśnij pozycję **Zapisz**.

## <a name="downtime-during-scaling-up-and-down"></a>Przestoje podczas skalowania w górę i w dół
Jeśli skalowanie z lub do warstwy dewelopera, będzie przestojów. W przeciwnym razie nie ma przestojów. 


## <a name="next-steps"></a>Następne kroki

- [Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach świadczenia usługi Azure](api-management-howto-deploy-multi-region.md)
- [Jak automatycznie skalować wystąpienie usługi Usługi Azure API Management](api-management-howto-autoscale.md)
