---
title: Plany serwisowe i przydziały dla usługi Azure Spring Cloud
description: Dowiedz się więcej o przydziałach usług i planach usług dla usługi Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278890"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Przydziały i plany usług dla usługi Azure Spring Cloud

Wszystkie usługi platformy Azure ustawiają domyślne limity i przydziały zasobów i funkcji.  W okresie w wersji zapoznawczej usługa Azure Spring Cloud oferuje tylko jeden plan usługi.

W tym artykule opisano przydziały usług oferowane w bieżącym okresie wersji zapoznawczej.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Warstwy usług i przydziały usługi w chmurze usługi Azure Spring Cloud

W okresie w wersji zapoznawczej usługa Azure Spring Cloud oferuje tylko jedną warstwę usług.

Zasób | Kwota
------- | -------
Procesor wirtualny | 4 na wystąpienie usługi
Memory (Pamięć) | 8 GBytes na wystąpienie usługi
Wystąpienia usługi Azure Spring Cloud na region na subskrypcję | 10
Całkowita liczba wystąpień aplikacji na wystąpienie usługi Azure Spring Cloud | 500
Całkowita liczba wystąpień aplikacji na aplikację Spring | 20
Trwałe woluminy | 10 x 50 GBytów

Po osiągnięciu przydziału otrzymasz błąd 400 o treści: "Przydział przekracza limit subskrypcji *subskrypcji* w regionie, w *którym jest tworzona usługa Azure Spring Cloud.*

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Jeśli zasób wymaga zwiększenia, [utwórz żądanie pomocy technicznej](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
