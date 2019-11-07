---
title: Plany usługi i przydziały dla chmury wiosennej platformy Azure
description: Dowiedz się więcej na temat przydziałów i planów usług dla chmury wiosennej platformy Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41a2b1a7d9aa5089ba2ee73cd3c5c5c5e31f5225
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607670"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Limity przydziału i plany usług dla chmury wiosennej platformy Azure

Wszystkie usługi platformy Azure ustawiają domyślne limity i przydziały dla zasobów i funkcji.  W trakcie okresu zapoznawczego chmura Wiosenna Azure oferuje tylko jeden plan usługi.

W tym artykule opisano limity przydziału usługi oferowane w trakcie bieżącego okresu zapoznawczego.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Warstwy i przydziały usług w chmurze ze sprężyną Azure

W trakcie okresu zapoznawczego chmura Wiosenna platformy Azure oferuje tylko jedną warstwę usług.

Zasób | Kwota
------- | -------
Procesor wirtualny | 4
Memory (Pamięć) | 8 GBytes
Subskrypcja chmury ze sprężyną Azure | 1
Wystąpienia usługi w chmurze ze sprężyną na platformie Azure na region na subskrypcję | 2
Łączna liczba wystąpień aplikacji na usługę Azure sprężynowego wystąpienia usługi w chmurze | 50
Łączna liczba wystąpień aplikacji na aplikację wiosenną | 20
Woluminy trwałe | 10 x 50 GBytes

Po osiągnięciu limitu przydziału otrzymasz błąd 400, który odczytuje: "przydział przekracza *Limit subskrypcji subskrypcji w regionie* regionu, w *którym jest tworzona usługa w chmurze Azure Wiosenna*.

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Jeśli zasób wymaga zwiększenia, [Utwórz żądanie pomocy technicznej](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
