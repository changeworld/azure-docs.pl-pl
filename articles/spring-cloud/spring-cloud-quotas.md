---
title: Plany usługi i przydziały dla chmury wiosennej platformy Azure
description: Dowiedz się więcej na temat przydziałów i planów usług dla chmury wiosennej platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278890"
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

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Jeśli zasób wymaga zwiększenia, [Utwórz żądanie pomocy technicznej](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
