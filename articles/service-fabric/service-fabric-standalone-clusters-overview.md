---
title: Autonomiczna Usługa Service Fabric clusters — omówienie | Dokumentacja firmy Microsoft
description: Klastry usługi Service Fabric z systemem Windows Server i Linux, co oznacza, że będziesz mieć możliwość wdrażania i hostów dowolnym miejscu aplikacji usługi Service Fabric można uruchomić system Windows Server lub Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 5997526098980220014d9bb2d47efe6c9aedee3d
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752332"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Omówienie usługi Service Fabric autonomicznych klastrów

Klaster usługi Service Fabric to zbiór połączonych z siecią maszyn wirtualnych lub fizycznych, w których mikrousługi są wdrażania i zarządzania nimi. Komputer lub maszynę Wirtualną, która jest częścią klastra, jest nazywana węzłem klastra. Klastry można skalować do tysięcy węzłów. Po dodaniu nowych węzłów do klastra usługi Service Fabric rebalances replik partycji usługi i wystąpień na większą liczbę węzłów. Ogólna zwiększa wydajność aplikacji i zmniejsza rywalizacji o dostęp do pamięci. Jeśli nie są wydajnie używane węzły w klastrze, możesz zmniejszyć liczbę węzłów w klastrze. Usługa Service Fabric ponownie rebalances replik partycji i wystąpień na obniżenie liczby węzłów, aby lepiej wykorzystać możliwości sprzętu na każdym węźle.

Typ węzła definiuje rozmiar, liczbę i właściwości dla zestawu węzłów w klastrze. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Typy węzłów służą do definiowania ról zestawu węzłów klastra, takich jak „fronton” lub „zaplecze”. Klaster może mieć więcej niż jeden typ węzła, ale podstawowy typ węzła musi obejmować co najmniej pięć maszyn wirtualnych w przypadku klastrów produkcyjnych (lub co najmniej trzy maszyny wirtualne w przypadku klastrów testowych). [Usługi systemowe Service Fabric](service-fabric-technical-overview.md#system-services) są umieszczane w węzłach podstawowego typu.

Proces tworzenia klastra usługi Service Fabric w środowisku lokalnym jest podobny do procesu tworzenia klastra w dowolnej chmurze wybranych przez użytkownika za pomocą zestawu maszyn wirtualnych. Początkowe kroki, aby zainicjować obsługę maszyn wirtualnych podlegają dostawcy usług w chmurze lub środowisku lokalnym, którego używasz. Po utworzeniu zestawu maszyn wirtualnych z połączeniem sieciowym włączone między nimi, następnie kroki, aby skonfigurować pakiet usługi Service Fabric, edytować ustawienia klastra i uruchom Tworzenie klastra i skrypty zarządzania są identyczne. Dzięki temu swojej wiedzy i doświadczenia, obsługi i zarządzania klastrami usługi Service Fabric przenoszeniu po wybraniu pod kątem nowego środowiska hostingu.

## <a name="cluster-security"></a>Zabezpieczenia klastra
Klaster usługi Service Fabric jest zasobem, którego jesteś właścicielem.  Jest odpowiedzialny za Zabezpieczanie klastrów zapobiega nieautoryzowanym użytkownikom nawiązywanie połączeń z nich. Zabezpieczonego klastra jest szczególnie ważne w przypadku obciążeń produkcyjnych są uruchomione w klastrze.

### <a name="node-to-node-security"></a>Węzeł węzeł zabezpieczeń
Węzeł węzeł zabezpieczeń zabezpiecza komunikację między maszynami wirtualnymi lub komputerów w klastrze. W tym scenariuszu zabezpieczeń gwarantuje, że tylko te komputery, które są autoryzowane do przyłączenia się do klastra można uczestniczyć w hostingu aplikacji i usług w klastrze. Usługa Service Fabric używa certyfikatów X.509 do zabezpieczenia klastra i udostępnia funkcje zabezpieczeń aplikacji.  Certyfikat klastra jest wymagana do zabezpieczania ruchu klastra i klastra i serwera uwierzytelniania.  Podpisany — certyfikaty samodzielnie mogą być używane w przypadku klastrów testowych, ale certyfikatu z zaufanego urzędu certyfikacji powinien służyć do zabezpieczenia klastrów produkcyjnych.

Windows security można również włączyć dla klastra autonomicznego Windows. Jeśli masz system Windows Server 2012 R2 i Windows Active Directory, zaleca się używać Windows zabezpieczeń za pomocą kont usługi zarządzanych przez grupę. W przeciwnym razie za pomocą Windows zabezpieczenia konta Windows.

Aby uzyskać więcej informacji, przeczytaj [zabezpieczeń między węzłami](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Węzeł klienta zabezpieczeń
Węzeł klienta zabezpieczeń uwierzytelnia klientów i pomaga bezpiecznej komunikacji między klientem a poszczególnych węzłów w klastrze. Ten typ zabezpieczeń pomaga upewnić się, że tylko autoryzowani użytkownicy mogą dostęp do klastra i aplikacje, które są wdrażane w klastrze. Klienci są jednoznacznie identyfikowany przy użyciu jednej z ich poświadczeń zabezpieczeń certyfikatu X.509. Dowolną liczbę certyfikatów klienta opcjonalnie może służyć do uwierzytelniania klientów administratora lub użytkownika z klastrem.

Oprócz certyfikaty klienta można również skonfigurować do uwierzytelniania klientów z klastrem usługi Azure Active Directory.

Aby uzyskać więcej informacji, przeczytaj [węzeł klienta zabezpieczeń](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Usługa Service Fabric obsługuje także kontroli dostępu, aby ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników. Dzięki temu zabezpieczeniu klastra. Dwa typy kontroli dostępu są obsługiwane w przypadku klientów nawiązujących połączenie z klastrem: Rola administratora i roli użytkownika.  

Aby uzyskać więcej informacji, przeczytaj [kontroli dostępu opartej na rolach (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Skalowanie

Zmień zapotrzebowania aplikacji wraz z upływem czasu. Może być konieczne zwiększenie zasobów klastra aplikacji zwiększenia obciążenia lub sieci natężenia ruchu lub zmniejszanie zasobów klastra, gdy zapotrzebowanie maleje. Po utworzeniu klastra usługi Service Fabric można skalować klastra w poziomie (zmienić liczbę węzłów), czy w pionie (zmienić zasoby węzłów). Możesz skalować klastra w dowolnym momencie, nawet gdy działają obciążenia w klastrze. Jak jest skalowana w klastrze, aplikacje będą skalowane automatycznie również.

Aby uzyskać więcej informacji, przeczytaj [skalowanie klastrów autonomicznych](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Uaktualnianie

Autonomicznego klastra jest zasób, całkowicie własne. Odpowiedzialność stosowanie poprawek podstawowego systemu operacyjnego i Inicjowanie uaktualnienia sieci szkieletowej. Możesz ustawić do klastra, aby otrzymywać aktualizacje automatyczne środowiska uruchomieniowego, gdy firma Microsoft publikuje nową wersję lub wybrać wersję obsługiwane środowisko uruchomieniowe, która ma. Oprócz uaktualnienia sieci szkieletowej można również stosowanie poprawek systemu operacyjnego i aktualizować konfiguracji klastra, takie jak certyfikaty lub porty aplikacji. 

Aby uzyskać więcej informacji, przeczytaj [uaktualniania klastrów autonomicznych](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Jesteś w stanie Tworzenie klastrów na maszynach wirtualnych lub komputerach z tymi systemami operacyjnymi (Linux nie jest jeszcze obsługiwany):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj więcej na temat [Zabezpieczanie](service-fabric-cluster-security.md), [skalowanie](service-fabric-cluster-scaling-standalone.md), i [uaktualnianie](service-fabric-cluster-upgrade-standalone.md) autonomicznych klastrów.

Dowiedz się więcej o [opcje pomocy technicznej usługi Service Fabric](service-fabric-support.md).
