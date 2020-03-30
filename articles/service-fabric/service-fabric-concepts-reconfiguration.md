---
title: Ponowna konfiguracja w sieci szkieletowej usług Azure
description: Dowiedz się więcej o konfiguracjach dla replik usług stanowych i procesie ponownej konfiguracji usługi sieci szkieletowej używa do zachowania spójności i dostępności podczas zmiany.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609999"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Ponowna konfiguracja w sieci szkieletowej usług Azure
*Konfiguracja* jest zdefiniowana jako repliki i ich role dla partycji usługi stanowej.

*Ponowna konfiguracja* to proces przenoszenia jednej konfiguracji do innej konfiguracji. Wprowadza zmianę do zestawu replik dla partycji usługi stanowej. Stara konfiguracja nazywa się *poprzednią konfiguracją (PC),* a nowa konfiguracja nazywana jest *bieżącą konfiguracją (CC).* Protokół ponownej konfiguracji w usłudze Azure Service Fabric zachowuje spójność i zachowuje dostępność podczas wszelkich zmian w zestawie replik.

Menedżer trybu failover inicjuje ponowne konfiguracje w odpowiedzi na różne zdarzenia w systemie. Na przykład jeśli podstawowy nie powiedzie się, a następnie rekonfiguracja jest inicjowana w celu promowania aktywnego pomocniczego do podstawowego. Innym przykładem jest w odpowiedzi na uaktualnienia aplikacji, gdy może być konieczne przeniesienie podstawowego do innego węzła w celu uaktualnienia węzła.

## <a name="reconfiguration-types"></a>Typy ponownej konfiguracji
Ponowne konfiguracje można podzielić na dwa typy:

- Ponowne konfiguracje, w których zmienia się podstawowy:
    - **Przewijanie w błąd:** Trybu failover są ponownekonfiguracje w odpowiedzi na niepowodzenie uruchomionego podstawowego.
    - **SwapPrimary:** Swaps są rekonfiguracje, gdzie sieci szkieletowej usług musi przenieść uruchomiony podstawowy z jednego węzła do drugiego, zwykle w odpowiedzi na równoważenie obciążenia lub uaktualnienia.

- Ponowne konfiguracje, w których podstawowy nie zmienia.

## <a name="reconfiguration-phases"></a>Fazy ponownej konfiguracji
Ponowna konfiguracja przebiega w kilku fazach:

- **Faza0:** Ta faza występuje w rekonfiguracji podstawowych swap, gdzie bieżąca podstawowa przenosi swój stan do nowej podstawowej i przejścia do aktywnego pomocniczego.

- **Faza 1:** Ta faza ma miejsce podczas rekonfiguracji, gdzie zmienia się podstawowy. W tej fazie sieci szkieletowej usług identyfikuje poprawne podstawowe wśród bieżących replik. Ta faza nie jest potrzebna podczas ponownej konfiguracji podstawowych swap, ponieważ nowa podstawowa została już wybrana. 

- **Faza 2:** W tej fazie sieci szkieletowej usług zapewnia, że wszystkie dane są dostępne w większości replik bieżącej konfiguracji.

Istnieje kilka innych faz, które są tylko do użytku wewnętrznego.

## <a name="stuck-reconfigurations"></a>Zablokowane rekonfiguracje
Ponowne konfiguracje mogą *utknąć* z różnych powodów. Oto niektóre z najczęstszych przyczyn:

- **Repliki w dół:** Niektóre fazy ponownej konfiguracji wymagają większości replik w konfiguracji, aby być w górę.
- **Problemy z siecią lub komunikacją:** Ponowne konfiguracje wymagają łączności sieciowej między różnymi węzłami.
- **Awarie interfejsu API:** Protokół ponownej konfiguracji wymaga, aby implementacje usługi kończyć niektóre interfejsy API. Na przykład nie honorowanie tokenu anulowania w niezawodnej usłudze powoduje, że ponowne konfiguracje SwapPrimary utknąć.

Raporty kondycji ze składników systemu, takich jak System.FM, System.RA i System.RAP, można zdiagnozować, gdzie zostanie zablokowana ponowna konfiguracja. [Strona raportu kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) opisuje te raporty dotyczące kondycji.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat koncepcji sieci szkieletowej usług, zobacz następujące artykuły:

- [Cykl życia usług Reliable Services — C#](service-fabric-reliable-services-lifecycle.md)
- [Raporty dotyczące kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md)
