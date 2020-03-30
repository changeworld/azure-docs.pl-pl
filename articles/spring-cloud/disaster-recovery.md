---
title: Odzyskiwanie geograficzne usługi Azure Spring Cloud | Dokumenty firmy Microsoft
description: Dowiedz się, jak chronić aplikację Spring Cloud przed regionalnymi awariami
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279150"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Odzyskiwanie po awarii w chmurze usługi Azure Spring Cloud

W tym artykule opisano niektóre strategie, których można użyć do ochrony aplikacji usługi Azure Spring Cloud przed wystąpieniem przestojów.  W dowolnym regionie lub centrum danych mogą wystąpić przestoje spowodowane awariami regionalnymi, ale staranne planowanie może zmniejszyć wpływ na klientów.

## <a name="plan-your-application-deployment"></a>Planowanie wdrażania aplikacji

Aplikacje usługi Azure Spring Cloud są uruchamiane w określonym regionie.  Platforma Azure działa w wielu lokalizacjach geograficznych na całym świecie. Geografia platformy Azure jest zdefiniowanym obszarem świata, który zawiera co najmniej jeden region platformy Azure. Region platformy Azure to obszar w obrębie lokalizacji geograficznej, zawierający co najmniej jedno centrum danych.  Każdy region platformy Azure jest sparowany z innym regionem w tej samej lokalizacji geograficznej, tworząc razem parę regionalną. Platforma Azure serializuje aktualizacje platformy (planowaną konserwację) między parami regionalnymi, zapewniając, że tylko jeden region w każdej parze jest aktualizowany w czasie. W przypadku awarii w wielu regionach, co najmniej jeden region w każdej parze będzie priorytetem dla odzyskiwania.

Zapewnienie wysokiej dostępności i ochrony przed awariami wymaga wdrożenia aplikacji Spring Cloud w wielu regionach.  Platforma Azure udostępnia listę [sparowanych regionów,](../best-practices-availability-paired-regions.md) dzięki czemu można zaplanować wdrożenia spring cloud na pary regionalne.  Zaleca się, aby wziąć pod uwagę trzy kluczowe czynniki podczas projektowania architektury mikrousług: dostępność regionu, regiony sparowane platformy Azure i dostępność usług.

*  Dostępność regionu: wybierz obszar geograficzny w pobliżu użytkowników, aby zminimalizować opóźnienia sieci i czas transmisji.
*  Sparowane regiony platformy Azure: wybierz sparowane regiony w wybranym obszarze geograficznym, aby zapewnić skoordynowane aktualizacje platformy i priorytetowe działania związane z odzyskiwaniem w razie potrzeby.
*  Dostępność usług: Zdecyduj, czy sparowane regiony powinny być gorące/gorące, gorące/ciepłe, czy gorące/zimne.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Kierowanie ruchu za pomocą usługi Azure Traffic Manager

[Usługa Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) zapewnia równoważenie obciążenia ruchu opartego na systemie DNS i może dystrybuować ruch sieciowy w wielu regionach.  Użyj usługi Azure Traffic Manager, aby skierować klientów do najbliższego wystąpienia usługi Azure Spring Cloud do nich.  Aby uzyskać najlepszą wydajność i nadmiarowość, przed wysłaniem go do usługi Azure Spring Cloud należy kierować całym ruchem aplikacji za pośrednictwem usługi Azure Traffic Manager.

Jeśli masz aplikacje usługi Azure Spring Cloud w wielu regionach, użyj usługi Azure Traffic Manager, aby kontrolować przepływ ruchu do aplikacji w każdym regionie.  Zdefiniuj punkt końcowy usługi Azure Traffic Manager dla każdej usługi przy użyciu adresu IP usługi. Klienci powinni połączyć się z nazwą DNS usługi Azure Traffic Manager wskazującą usługę Azure Spring Cloud.  Obciążenie usługi Azure Traffic Manager równoważy ruch między zdefiniowanymi punktami końcowymi.  Jeśli awaria uderzy w centrum danych, usługa Azure Traffic Manager skieruje ruch z tego regionu do jego pary, zapewniając ciągłość usługi.