---
title: Plany usługi i przydziały dla chmury wiosennej platformy Azure
description: Dowiedz się więcej na temat przydziałów i planów usług dla chmury wiosennej platformy Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 0518b13ea228b4834a095a9bf126b131e70a5f45
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851557"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Limity przydziału i plany usług dla chmury wiosennej platformy Azure

Wszystkie usługi platformy Azure ustawiają domyślne limity i przydziały dla zasobów i funkcji.  W trakcie okresu zapoznawczego chmura Wiosenna Azure oferuje tylko jeden plan usługi.

W tym artykule opisano limity przydziału usługi oferowane w trakcie bieżącego okresu zapoznawczego.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Warstwy i przydziały usług w chmurze ze sprężyną Azure

W trakcie okresu zapoznawczego chmura Wiosenna platformy Azure oferuje tylko jedną warstwę usług.

Zasób | Ilość
------- | -------
vCPU | 4 na wystąpienie usługi
Pamięć | 8 GBytes na wystąpienie usługi
Wystąpienia usługi w chmurze ze sprężyną na platformie Azure na region na subskrypcję | 10
Łączna liczba wystąpień aplikacji na usługę Azure sprężynowego wystąpienia usługi w chmurze | 500
Łączna liczba wystąpień aplikacji na aplikację wiosenną | 20
Woluminy trwałe | 10 x 50 GBytes

Po osiągnięciu limitu przydziału otrzymasz błąd 400, który odczytuje: "przydział przekracza *Limit subskrypcji subskrypcji w regionie* regionu, w *którym jest tworzona usługa w chmurze Azure Wiosenna*.

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Jeśli zasób wymaga zwiększenia, [Utwórz żądanie pomocy technicznej](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
