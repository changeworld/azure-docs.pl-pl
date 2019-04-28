---
title: Uaktualnianie i skalowanie wystąpienia usługi Azure API Management | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób uaktualniania i Skaluj wystąpienia usługi Azure API Management.
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
ms.openlocfilehash: ac8babf3a00c73b942ae64ac4cca00c7be7cfcfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859552"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Uaktualnianie i skalowanie wystąpienia usługi Azure API Management  

Klienci mogą skalować wystąpienia usługi Azure API Management (APIM), dodając i usuwając jednostki. A **jednostki** składa się z dedykowanych zasobów platformy Azure, a także ma pewne nośnych pojemności, wyrażony jako liczby wywołań interfejsu API na miesiąc. Ta liczba nie reprezentuje ograniczenie wywołań, ale raczej wartość maksymalna przepływność do obsługi planowania pojemności nierównej. Rzeczywista przepływność i opóźnienie zależy szeroko czynników, takich jak liczba i szybkość jednoczesnych połączeń, rodzaj i liczba skonfigurowanych zasad, żądań i rozmiarów odpowiedzi i opóźnienie zaplecza.

Pojemność i Cena każdej jednostki jest zależna od **warstwy** , w którym istnieje jednostki. Możesz wybrać między czterech warstwach: **Deweloper**, **podstawowe**, **standardowa**, **Premium**. Jeśli potrzebujesz zwiększyć pojemność dla usługi w obrębie warstwy, należy dodać jednostkę. Warstwy, która jest aktualnie wybrany w swojego wystąpienia usługi APIM nie zezwala na dodawanie większej liczby jednostek, musisz wykonać uaktualnienie do warstwy wyższego poziomu.

Cena każdej jednostki i dostępnych funkcji (na przykład wdrożenie w wielu regionach), zależy od warstwy, który został wybrany dla swojego wystąpienia usługi APIM. [Cennik](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artykuł, wyjaśnia cena za jednostkę i funkcje w każdej warstwie. 

>[!NOTE]
>[Cennik](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artykule przedstawiono przybliżone liczby jednostek pojemności w przypadku każdej warstwy. Aby uzyskać dokładniejsze numerów, musisz przyjrzyj realistyczny scenariusz dotyczący interfejsów API. Zobacz [pojemności wystąpienia usługi Azure API Management](api-management-capacity.md) artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki z tego artykułu, musisz mieć:

+ Mieć aktywną subskrypcję platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Utworzono wystąpienie usługi APIM. Aby uzyskać więcej informacji, zobacz [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).

+ Zrozumienie pojęcia [pojemności wystąpienia usługi Azure API Management](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Uaktualnianie i skalowanie  

Możesz wybrać między czterech warstwach: **Deweloper**, **podstawowe**, **standardowa** i **Premium**. **Developer** warstwy powinien być używany do oceny usługi; nie należy jej używać w środowisku produkcyjnym. **Developer** warstwy nie ma umowy SLA i nie można skalować tę warstwę (Dodawanie/usuwanie jednostek). 

**Podstawowe**, **standardowa** i **Premium** są warstwy produkcji, umowy SLA, które mogą być skalowane. **Podstawowe** warstwa jest najtańszej warstwy, która jest objęta umową SLA i mogą być skalowane do 2 jednostek **standardowa** warstwę można skalować do maksymalnie czterech jednostek. Można dodać dowolną liczbę jednostek do **Premium** warstwy.

**Premium** warstwy umożliwia dystrybucję pojedynczego wystąpienia usługi Azure API Management w dowolnej liczbie żądaną regiony platformy Azure. Podczas początkowego tworzenia usługi Azure API Management, wystąpienie zawiera tylko jedną jednostkę i znajduje się w jednym regionie platformy Azure. Początkowy regionów jest wyznaczony jako **głównej** regionu. Łatwo można dodawać dodatkowe regiony. Podczas dodawania regionu, możesz określić liczbę jednostek, które mają zostać przydzielone. Na przykład można mieć jedną jednostkę **głównej** region i pięć jednostek w niektórych innych regionów. Można dostosować liczbę jednostek do ruchu sieciowego, w każdym regionie. Aby uzyskać więcej informacji, zobacz [jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach platformy Azure](api-management-howto-deploy-multi-region.md).

Można uaktualnić i obniżenia poziomu do i z dowolnej warstwy. Należy pamiętać, że uaktualnienia lub zmiany na starszą wersję można usunąć niektórych funkcji — na przykład sieci wirtualnych lub wdrożenie w wielu regionach, podczas zmiany na starszą wersję do wersji Standard lub Basic z warstwy Premium.

>[!NOTE]
>Procesu uaktualniania lub skalowania zastosowanie może potrwać od 15 do 45 minut. Otrzymuj powiadomienia, gdy wszystko będzie gotowe.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Uaktualnianie i skalowanie za pomocą witryny Azure portal

![Skalowanie usługi APIM w witrynie Azure portal](./media/upgrade-and-scale/portal-scale.png)

1. Przejdź do swojego wystąpienia usługi APIM w [witryny Azure portal](https://portal.azure.com/).
2. Wybierz **skalowanie i cennik** z menu.
3. Wybierz odpowiednią warstwę.
4. Określ liczbę **jednostek** chcesz dodać. Można użyć suwaka lub wpisz liczbę jednostek.  
    Jeśli wybierzesz **Premium** warstwy, najpierw musisz wybrać region.
5. Naciśnij pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

- [Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach świadczenia usługi Azure](api-management-howto-deploy-multi-region.md)
- [Jak automatycznie skalować wystąpienie usługi Azure API Management](api-management-howto-autoscale.md)
