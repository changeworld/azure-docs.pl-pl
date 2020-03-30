---
title: Informacje o klastrach autonomicznej sieci szkieletowej usług
description: Klastry sieci szkieletowej usług działają w systemach Windows Server i Linux, co oznacza, że będziesz mógł wdrażać i hostować aplikacje sieci szkieletowej usług w dowolnym miejscu, w którym można uruchomić system Windows Server lub Linux.
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: e8912ef5bc0fd6009443b736031fc9af57ab6c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465644"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Omówienie autonomicznych klastrów sieci szkieletowej usług

Klaster sieci szkieletowej usług to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane. Maszyna lub maszyna wirtualna, która jest częścią klastra jest nazywany węzłem klastra. Klastry można skalować do tysięcy węzłów. Jeśli dodasz nowe węzły do klastra, sieci szkieletowej usług równoważe repliki partycji usługi i wystąpienia w zwiększonej liczbie węzłów. Ogólna wydajność aplikacji poprawia i rywalizacji o dostęp do pamięci zmniejsza. Jeśli węzły w klastrze nie są używane efektywnie, można zmniejszyć liczbę węzłów w klastrze. Sieci szkieletowej usług ponownie równoważy repliki partycji i wystąpień w całej zmniejszonej liczby węzłów, aby lepiej wykorzystać sprzęt w każdym węźle.

Typ węzła definiuje rozmiar, liczbę i właściwości zestawu węzłów w klastrze. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Typy węzłów służą do definiowania ról zestawu węzłów klastra, takich jak „fronton” lub „zaplecze”. Klaster może mieć więcej niż jeden typ węzła, ale podstawowy typ węzła musi obejmować co najmniej pięć maszyn wirtualnych w przypadku klastrów produkcyjnych (lub co najmniej trzy maszyny wirtualne w przypadku klastrów testowych). [Usługi systemowe Service Fabric](service-fabric-technical-overview.md#system-services) są umieszczane w węzłach podstawowego typu.

Proces tworzenia klastra sieci szkieletowej usług lokalnie jest podobny do procesu tworzenia klastra w dowolnej wybranej chmurze za pomocą zestawu maszyn wirtualnych. Początkowe kroki do inicjowania obsługi administracyjnej maszyn wirtualnych są regulowane przez dostawcę chmury lub środowiska lokalnego, którego używasz. Po włączeniu zestawu maszyn wirtualnych z włączoną łącznością sieciową kroki konfigurowania pakietu sieci szkieletowej usług, edytowania ustawień klastra i uruchamiania skryptów tworzenia i zarządzania klastrem są identyczne. Dzięki temu twoja wiedza i doświadczenie w obsłudze klastrów sieci szkieletowej usług i zarządzaniu nimi są zbywalne, gdy zdecydujesz się kierować reklamy na nowe środowiska hostingowe.

## <a name="cluster-security"></a>Zabezpieczenia klastra
Klaster sieci szkieletowej usług jest zasobem, który jesteś właścicielem.  Użytkownik jest odpowiedzialny za zabezpieczenie klastrów, aby zapobiec łączeniu się z nimi nieautoryzowanym użytkownikom. Bezpieczny klaster jest szczególnie ważne podczas uruchamiania obciążeń produkcyjnych w klastrze.

### <a name="node-to-node-security"></a>Zabezpieczenia między węzłami
Zabezpieczenia między węzłami zabezpieczają komunikację między maszynami wirtualnymi lub komputerami w klastrze. Ten scenariusz zabezpieczeń gwarantuje, że tylko komputery, które są upoważnione do przyłączenia się do klastra mogą uczestniczyć w hostowania aplikacji i usług w klastrze. Usługa Service Fabric używa certyfikatów X.509 do zabezpieczenia klastra i zapewnienia funkcji zabezpieczeń aplikacji.  Certyfikat klastra jest wymagany do zabezpieczenia ruchu klastra i zapewnienia uwierzytelniania klastra i serwera.  Certyfikaty z podpisem własnym mogą być używane dla klastrów testowych, ale certyfikat z zaufanego urzędu certyfikacji powinien być używany do zabezpieczania klastrów produkcyjnych.

Zabezpieczenia systemu Windows można również włączyć dla autonomicznego klastra systemu Windows. Jeśli masz systemy Windows Server 2012 R2 i Windows Active Directory, zaleca się używanie zabezpieczeń systemu Windows z grupowymi kontami usług zarządzanych. W przeciwnym razie należy użyć zabezpieczeń systemu Windows z kontami systemu Windows.

Aby uzyskać więcej informacji, przeczytaj informacje [o zabezpieczeniach między węzłami](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Zabezpieczenia między klientami
Zabezpieczenia klient-węzeł uwierzytelniają klientów i pomagają w zabezpieczeniu komunikacji między klientem a poszczególnymi węzłami w klastrze. Ten typ zabezpieczeń pomaga zapewnić, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i aplikacji wdrożonych w klastrze. Klienci są jednoznacznie identyfikowani za pomocą poświadczeń zabezpieczeń certyfikatu X.509. Dowolnej liczby opcjonalnych certyfikatów klienta można używać do uwierzytelniania klientów administratora lub użytkowników za pomocą klastra.

Oprócz certyfikatów klientów usługę Azure Active Directory można również skonfigurować do uwierzytelniania klientów za pomocą klastra.

Aby uzyskać więcej informacji, przeczytaj informacje [o zabezpieczeniach między klientami](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Usługa Sieci szkieletowej obsługuje również kontrolę dostępu, aby ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników. Pomaga to uczynić klaster bardziej bezpieczne. Dwa typy kontroli dostępu są obsługiwane dla klientów, którzy łączą się z klastrem: rola administratora i rola użytkownika.  

Aby uzyskać więcej informacji, przeczytaj [artykuł Kontrola dostępu oparta na rolach (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Skalowanie

Wymagania aplikacji zmieniają się wraz z czasem. Może być konieczne zwiększenie zasobów klastra w celu zaspokojenia zwiększonego obciążenia aplikacji lub ruchu sieciowego lub zmniejszenia zasobów klastra w przypadku spadku popytu. Po utworzeniu klastra sieci szkieletowej usług można skalować klaster w poziomie (zmieniać liczbę węzłów) lub w pionie (zmienić zasoby węzłów). Klaster można skalować w dowolnym momencie, nawet gdy obciążenia są uruchomione w klastrze. W miarę skalowania klastra aplikacje są również skalowane automatycznie.

Aby uzyskać więcej informacji, zobacz [Skalowanie klastrów autonomicznych](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Uaktualnianie

Klaster autonomiczny jest zasobem, który jest całkowicie własnością. Użytkownik jest odpowiedzialny za łatanie bazowego systemu operacyjnego i inicjowanie uaktualnień sieci szkieletowej. Klaster można ustawić tak, aby odbierał automatyczne uaktualnienia środowiska uruchomieniowego, gdy firma Microsoft wyda nową wersję, lub wybrać obsługiwaną wersję środowiska uruchomieniowego. Oprócz uaktualnień sieci szkieletowej można również załatać system operacyjny i zaktualizować konfigurację klastra, taką jak certyfikaty lub porty aplikacji. 

Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastrów autonomicznych](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Można tworzyć klastry na maszynach wirtualnych lub komputerach z tymi systemami operacyjnymi (Linux nie jest jeszcze obsługiwany):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [zabezpieczaniu](service-fabric-cluster-security.md), [skalowaniu](service-fabric-cluster-scaling-standalone.md)i [uaktualnianiu](service-fabric-cluster-upgrade-standalone.md) klastrów autonomicznych.

Dowiedz się więcej o [opcjach pomocy technicznej sieci szkieletowej](service-fabric-support.md)usług .
