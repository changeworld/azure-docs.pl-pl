---
title: Wysoka dostępność — dedykowany moduł HSM platformy Azure | Dokumenty firmy Microsoft
description: Przykład wysokiej dostępności modułu HSM usługi Azure i podstawowe zagadnienia
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 536ef62acad900090924598edfa45450b2a8c951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70882251"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Wysoka dostępność dedykowanego modułu HSM platformy Azure

Usługa Azure Dedicated HSM jest oparta na centrach danych firmy Microsoft o wysokiej dostępności. Jednak wszystkie wysoko dostępne centrum danych jest narażone na zlokalizowane awarie i w ekstremalnych okolicznościach awarie na poziomie regionalnym. Firma Microsoft wdraża urządzenia HSM w różnych centrach danych w regionie, aby upewnić się, że inicjowanie obsługi administracyjnej wielu urządzeń nie prowadzi do udostępniania przez te urządzenia pojedynczego stojaka. Kolejny poziom wysokiej dostępności można osiągnąć, parując te moduły HSM w centrach danych w regionie przy użyciu funkcji grupy gemalto ha. Istnieje również możliwość parowania urządzeń między regionami w celu rozwiązania regionalnej pracy awaryjnej w sytuacji odzyskiwania po awarii. Dzięki tej wielowarstwowej konfiguracji o wysokiej dostępności każda awaria urządzenia zostanie automatycznie zaadresowana, aby aplikacje działały. Wszystkie centra danych mają również zapasowe urządzenia i składniki na miejscu, dzięki czemu każde nieudane urządzenie można wymienić w odpowiednim czasie.

## <a name="high-availability-example"></a>Przykład wysokiej dostępności

Informacje na temat konfigurowania urządzeń HSM pod kątem wysokiej dostępności na poziomie oprogramowania znajdują się w "Przewodniku administracyjnym sieci 500 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 Ten dokument jest dostępny na [stronie GEMALTO HSM .](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)

Na poniższym diagramie przedstawiono architekturę o wysokiej dostępności. Używa wielu urządzeń w regionie i wielu urządzeń sparowanych w oddzielnym regionie. Ta architektura używa co najmniej czterech urządzeń HSM i składników sieci wirtualnej.

![Diagram wysokiej dostępności](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Następne kroki

Zaleca się, aby wszystkie kluczowe pojęcia usługi, takie jak wysoka dostępność i zabezpieczenia, były dobrze rozumiane przed inicjowania obsługi administracyjnej urządzenia i projektowania aplikacji lub wdrażania.
Dalsze tematy na poziomie koncepcji:

* [Architektura wdrażania](deployment-architecture.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Obsługa sieci](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowania](monitoring.md)

Szczegółowe informacje na temat konfigurowania urządzeń HSM w celu zapewnienia wysokiej dostępności można znaleźć w portalu obsługi klienta Gemalto dla przewodników dla administratorów oraz w sekcji 6.
