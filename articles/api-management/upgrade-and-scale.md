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
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: ca32c72b1582b2a09f9f1754ad778cf1b682a1c2
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293316"
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Uaktualnienie i skalować wystąpienia interfejsu API zarządzania  

Klientów można skalować wystąpienia interfejsu API zarządzania (APIM), przez dodawanie i usuwanie jednostek. A **jednostki** składa się z dedykowanym zasobów platformy Azure i ma niektórych nośnych pojemności wyrażone jako liczba interfejsu API wywołania miesięcznie. Ta liczba nie reprezentuje ograniczenie wywołań, ale raczej wartość maksymalną przepustowość do obsługi planowania pojemności nierównej. Rzeczywiste przepustowości i opóźnień szeroko zależy od czynników, takich jak numer i liczba równoczesnych połączeń, rodzaj i liczbę skonfigurowanych zasad, rozmiary żądań i odpowiedzi i opóźnienia wewnętrznej bazy danych.

Pojemność i Cena każdej jednostki zależy od **warstwy** , w którym istnieje jednostki. Można wybrać jedną z czterech poziomów: **Developer**, **podstawowe**, **standardowe**, **Premium**. Jeśli potrzebujesz zwiększyć pojemność dla usługi w ramach warstwy, należy dodać jednostkę. Warstwę, która jest aktualnie wybrane w wystąpieniu APIM nie zezwala na dodawanie większej liczby jednostek, należy uaktualnić do warstwy wyższego poziomu.

Cena każdej jednostki i dostępnych funkcji (na przykład w przypadku wdrażania) zależy od warstwy, która została wybrana opcja APIM wystąpienia. [Szczegóły cennika](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artykułu, wyjaśnia cena za jednostkę i funkcje, które otrzymujesz w ramach poszczególnych warstw. 

>[!NOTE]
>[Szczegóły cennika](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artykule przedstawiono przybliżonej liczby pojemność jednostki w każdej warstwie. Aby uzyskać dokładniejsze liczb, należy przyjrzeć się realistyczne scenariusz dla interfejsów API. Zobacz [pojemności wystąpienia usługi Azure API Management](api-management-capacity.md) artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonaj kroki opisane w tym artykule, należy:

+ Mieć aktywną subskrypcją platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Utworzono wystąpienie APIM. Aby uzyskać więcej informacji, zobacz [utworzenia wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

+ Zrozumienie pojęcia [pojemności wystąpienia usługi Azure API Management] (ruch capacity.md interfejsu api zarządzania).

## <a name="upgrade-and-scale"></a>Uaktualnianie i skalowanie  

Można wybrać jedną z czterech poziomów: **Developer**, **podstawowe**, **standardowe** i **Premium**. **Developer** warstwy mają być używane do oceny usługi; nie powinna być używana w środowisku produkcyjnym. **Developer** warstwy nie ma umowy dotyczącej poziomu usług i nie można skalować tę warstwę (dodawania i usuwania jednostek). 

**Podstawowe**, **standardowe** i **Premium** są warstw produkcji, umowy dotyczącej poziomu usług, które mogą być skalowane. **Podstawowe** warstwa ma warstwy najtańszej, którego umowa SLA i może być skalowana maksymalnie 2 jednostki, **standardowe** warstwy mogą być skalowane do maksymalnie cztery jednostki. Można dodać dowolną liczbę jednostek **Premium** warstwy.

**Premium** warstwy umożliwia rozpowszechniają pojedynczego wystąpienia interfejsu API zarządzania dowolną liczbę żądaną regiony platformy Azure. Po utworzeniu początkowo usługi Zarządzanie interfejsami API, wystąpienie zawiera tylko jedną jednostkę i znajduje się w pojedynczym regionie Azure. Początkowa region jest określany jako **głównej** regionu. Łatwo można dodawać dodatkowe regionów. Podczas dodawania regionu, możesz określić liczbę jednostek, które mają zostać przydzielone. Na przykład można mieć jedną jednostkę **głównej** regionu i pięć jednostki w niektórych inny region. Liczba jednostek do ruchu sieciowego w każdym regionie można dostosować. Aby uzyskać więcej informacji, zobacz [sposób wdrażania wystąpienia usługi Azure API Management do wielu regionów platformy Azure](api-management-howto-deploy-multi-region.md).

Można uaktualnić i obniżyć do i z wszelkich warstwy. Należy pamiętać, że uaktualnienia lub zmiany na starszą wersję można usunąć niektórych funkcji — na przykład sieci wirtualnych lub w przypadku wdrożenia, jeśli zmiany na starszą wersję Standard lub podstawowa z warstwy Premium.

>[!NOTE]
>Proces uaktualniania lub skalę zastosowanie może potrwać od 15 do 45 minut. Otrzymasz powiadomienie po zakończeniu.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Użyj portalu Azure do uaktualnienia i skalowanie

![Skala APIM w portalu Azure](./media/upgrade-and-scale/portal-scale.png)

1. Przejdź do Twojego wystąpienia APIM w [portalu Azure](https://portal.azure.com/).
2. Wybierz **skali i cenach** z menu.
3. Wybierz żądaną warstwę.
4. Określ liczbę **jednostki** chcesz dodać. Można za pomocą suwaka lub wpisz liczbę jednostek.  
    Jeśli wybierzesz **Premium** warstwy, należy najpierw wybrać region.
5. Naciśnij klawisz **Zapisz**

## <a name="next-steps"></a>Kolejne kroki

[Jak wdrożyć wystąpienia usługi Azure API Management na wiele regionów platformy Azure](api-management-howto-deploy-multi-region.md)