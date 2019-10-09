---
title: Plany usługi i przydziały dla chmury wiosennej platformy Azure
description: Dowiedz się więcej na temat przydziałów i planów usług dla chmury wiosennej platformy Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 89934ee0ab0c901a904a1a5ac2fb620185571a23
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038784"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Limity przydziału i plany usług dla chmury wiosennej platformy Azure

Wszystkie usługi platformy Azure ustawiają domyślne limity i przydziały dla zasobów i funkcji.  W trakcie okresu zapoznawczego chmura Wiosenna Azure oferuje tylko jeden plan usługi.

W tym artykule opisano limity przydziału usługi oferowane w trakcie bieżącego okresu zapoznawczego.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Warstwy i przydziały usług w chmurze ze sprężyną Azure

W trakcie okresu zapoznawczego chmura Wiosenna platformy Azure oferuje tylko jedną warstwę usług.

Zasób | Ilość
------- | -------
vCPU | 4
Pamięć | 8 GBytes
Subskrypcja chmury ze sprężyną Azure | 1
Wystąpienia usługi w chmurze ze sprężyną na platformie Azure na region na subskrypcję | 2
Łączna liczba wystąpień aplikacji na usługę Azure sprężynowego wystąpienia usługi w chmurze | 50
Łączna liczba wystąpień aplikacji na aplikację wiosenną | 20
Woluminy trwałe | 10 x 50 GBytes

Po osiągnięciu limitu przydziału otrzymasz błąd 400, który odczytuje: "przydział przekracza *Limit subskrypcji subskrypcji w regionie* regionu, w *którym jest tworzona usługa w chmurze Azure Wiosenna*.

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Jeśli zasób wymaga zwiększenia, Wyślij do nas żądanie: azure-spring-cloud@service.microsoft.com.
