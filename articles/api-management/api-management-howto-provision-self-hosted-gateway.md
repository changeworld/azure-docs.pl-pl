---
title: Aprowizowanie bramy hostowane samodzielnie w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak aprowizować bramę hostowane samodzielnie w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075286"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Aprowizowanie bramy hostowane samodzielnie w usłudze Azure API Management

Inicjowanie obsługi administracyjnej zasobu bramy w wystąpieniu usługi Azure API Management jest warunkiem wstępnym wdrożenia bramy hostowanej samodzielnie. W tym artykule ominia się o krokach, aby aprowizować zasób bramy w usłudze API Management.

> [!NOTE]
> Funkcja bramy hostowanego samodzielnie jest w wersji zapoznawczej. W wersji zapoznawczej brama hostowana samodzielnie jest dostępna tylko w warstwach Deweloper i Premium bez dodatkowych opłat. Warstwa deweloperów jest ograniczona do pojedynczego wdrożenia bramy hostowanej samodzielnie.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Aprowizowanie własnej bramy

1. Wybierz **bramy** z sekcji **Ustawienia**.
2. Kliknij pozycję **+ Dodaj**.
3. Wprowadź **nazwę** i **region** bramy.
> [!TIP]
> **Region** określa zamierzoną lokalizację węzłów bramy, które będą skojarzone z tym zasobem bramy. Jest to semantycznie równoważne podobnej właściwości skojarzonej z dowolnym zasobem platformy Azure, ale można przypisać dowolną wartość ciągu.

4. Opcjonalnie wprowadź **opis** zasobu bramy.
5. Opcjonalnie wybierz **+** w obszarze **interfejsy API, aby skojarzyć** jeden lub więcej interfejsów API z tym zasobem bramy.
> [!TIP]
> Interfejs API można skojarzyć i usunąć z bramy na karcie **Ustawienia** interfejsu API.

> [!IMPORTANT]
> Domyślnie żaden z istniejących interfejsów API nie będzie skojarzony z nowym zasobem bramy. W związku z tym próby wywołania ich `404 Resource Not Found` za pośrednictwem nowej bramy spowoduje odpowiedzi.

6. Kliknij przycisk **Dodaj**.

Teraz zasób bramy został aprowizny w wystąpieniu usługi API Management. Można przystąpić do wdrażania bramy.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o bramie hostowanej samodzielnie, zobacz [Omówienie bramy hostowanego przez](self-hosted-gateway-overview.md) usługę Azure API Management
* Dowiedz się więcej o [wdrażaniu bramy hostowanego samodzielnie w umoście w umięśnych sieciach](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Dowiedz się więcej o [wdrażaniu bramy hostowanego samodzielnie w platformie Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
