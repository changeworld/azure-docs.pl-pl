---
title: Architektura usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Service Fabric to platforma systemów rozproszonych używana do tworzenia skalowalnych, niezawodnych i łatwych w zarządzaniu aplikacji w chmurze. W tym artykule przedstawiono architekturę usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: chackdan
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: rsinha
ms.openlocfilehash: a1e68e2e39ea6f1c8cf8669e2e02d8dacaf0f284
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097848"
---
# <a name="service-fabric-architecture"></a>Architektura usługi Service Fabric
Usługa Service Fabric jest tworzone za pomocą warstwowej podsystemów. Podsystemy te umożliwiają pisanie aplikacji, które są:

* O wysokiej dostępności
* Skalowalność
* Łatwe w zarządzaniu
* Sprawdzalnego działa zgodnie

Na poniższym diagramie przedstawiono główne podsystemów usługi Service Fabric.

![Diagram architektury usługi Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

W rozproszonym systemie możliwość bezpiecznej komunikacji między węzłami w klastrze jest niezwykle istotne. Podstawową stosu jest podsystemu transportu, która zapewnia bezpiecznej komunikacji między węzłami. Powyżej transportu podsystemu zaletą podsystemu Federacji, który klastrów różnych węzłach w pojedynczą jednostkę (o nazwie klastry) tak, aby usługi Service Fabric można wykrywać błędy, wykonywanie lidera i zapewniają routing spójne. Podsystem niezawodność warstwowo podsystemu federacji jest odpowiedzialny za niezawodność usługi Service Fabric za pomocą mechanizmów, takich jak zarządzanie zasobami, replikacji i trybu failover. Podsystem Federacji źródłową również podsystemu hostingu i aktywacji, który umożliwia zarządzanie cyklem życia aplikacji w jednym węźle. Podsystemu zarządzania umożliwia zarządzanie cyklem życia aplikacji i usług. Podsystem testowania pomaga deweloperom aplikacji, przetestować swoich usług przy użyciu symulowanego usterek przed i po wdrożeniu aplikacji i usług na środowisko produkcyjne. Usługa Service Fabric udostępnia możliwość rozpoznać lokalizacji usługi przy użyciu jego podsystem komunikacji. Modele programowania aplikacji widoczne dla deweloperów przedstawiono nałożone na podstawie tych podsystemów, wraz z modelu aplikacji, aby włączyć narzędzia.

## <a name="transport-subsystem"></a>Podsystem transportu
Podsystem transportu implementuje kanał komunikacyjny datagram point-to-point. Ten kanał jest używany do komunikacji w obrębie klastry usługi Service fabric i komunikacji między klaster usługi Service fabric i klientami. Obsługuje ona jednokierunkowe i wzorce komunikacji "żądanie-odpowiedź", które stanowi podstawę dla implementacji emisji i multiemisji w warstwie federacji. Podsystem transportu zabezpiecza komunikację przy użyciu X509 certyfikatów lub zabezpieczeń Windows. Podsystem ten jest używana wewnętrznie przez usługę Service Fabric i nie jest bezpośrednio dostępny dla deweloperów do programowania aplikacji.

## <a name="federation-subsystem"></a>Podsystem Federacji
Aby poprawić zestaw węzłów w rozproszonym systemie, musisz mieć spójny widok systemu. Podsystem federacyjnych używa podstawowych komunikacji, dostarczone przez podsystem transportu i odwzorowywały różnych węzłów w klastrze ujednoliconego pojedynczego go może poprawić. Zapewnia podstawowych systemów rozproszonych, wymagane przez inne podsystemy — wykrywanie awarii lidera i spójne routingu. Podsystem federacyjnej jest oparty na tabel skrótów rozproszonego przy użyciu 128-bitowej przestrzeni tokenu. Podsystem tworzy topologia pierścienia dla węzłów, z każdego węzła w pierścienia przydzielane podzbiór tokenu miejsca dla własności. Do wykrywania awarii warstwy używa był mechanizm dzierżawienia na podstawie serce kucie i postępowań arbitrażowych. Podsystem Federacji gwarantuje również za pośrednictwem skomplikowanych sprzężenia i protokoły wyjścia, które tylko jednego właściciela token istnieje w dowolnym momencie. Zapewnia to lidera i spójne gwarancje routingu.

## <a name="reliability-subsystem"></a>Podsystem niezawodności
Podsystem niezawodność udostępnia mechanizm do wysokiej dostępności stan usługi Service Fabric za pośrednictwem *replikatora*, *Menedżer trybu Failover*, i *zasobów Moduł równoważenia*.

* Replikator gwarantuje, czy zmiany stanu w replice podstawowej usługi będą automatycznie replikowane do repliki pomocnicze utrzymania spójności między replikę podstawową i pomocniczą zestawu replik usługi. Replikator jest odpowiedzialny za zarządzanie kworum między replikami zestawu replik. Współpracuje z usługą jednostki trybu failover, aby uzyskać listę operacji replikacji, a element reconfiguration agent dostarcza mu konfiguracji zestawu replik. Tę konfigurację wskazuje replik, które operacje mają być replikowane. Usługa Service Fabric udostępnia replikatora domyślny, o nazwie replikatora sieci szkieletowej, która może służyć przez interfejs API modelu się stan usługi, wysoko dostępne i niezawodne.
* Menedżer trybu Failover gwarantuje, że jeśli węzły są dodawane do lub usunięte z klastra, obciążenia jest automatycznie rozpowszechniane dostępnych węzłów. W przypadku awarii węzła w klastrze klastra zostanie automatycznie ponownie skonfigurować repliki usługi w celu zapewnienia dostępności.
* Menedżer zasobów umieszcza repliki usługi w domenie awarii klastra i gwarantuje, czy działają wszystkie jednostki trybu failover. Menedżer zasobów również równoważy zasobów usługi między bazowego obszaru udostępnionej puli węzłów klastra, aby osiągnąć optymalną jednolitego rozłożenia.

## <a name="management-subsystem"></a>Podsystemu zarządzania
Podsystemu zarządzania zapewnia end-to-end usługi i zarządzanie cyklem życia aplikacji. Polecenia cmdlet programu PowerShell i interfejsów API administracyjne pozwalają aprowizować, wdrażanie, poprawki, uaktualniania i anulować ich aprowizację bez utraty dostępności. Podsystem management wykonuje to za pośrednictwem następujących usług.

* **Menedżer klastra**: Jest to podstawowa usługa, która współdziała z Menedżera trybu Failover z niezawodności do umieszczenia aplikacji w węzłach, na podstawie ograniczeń umieszczania usługi. Menedżer zasobów w podsystemie pracy awaryjnej zapewnia ograniczenia nigdy nie są uszkodzone. Menedżer klastra zarządza cyklem życia aplikacji z należy anulować aprowizację. Można zintegrować go z Menedżera kondycji, aby upewnić się, że dostępność aplikacji nie zostaną utracone z punktu widzenia semantycznego kondycji podczas uaktualniania.
* **Menedżer kondycji**: Ta usługa umożliwia monitorowanie kondycji aplikacji, usług i obiektów klastra. Klaster jednostki (takie jak węzłów, usługa partycje i repliki) może zgłaszać informacje o kondycji, które następnie są agregowane w magazynie kondycji scentralizowany. Te informacje o kondycji zawiera migawkę kondycji ogólnej punktu w czasie usługi i rozproszone na wielu węzłach w klastrze, dzięki czemu możesz podjąć wszystkie wymagane akcje naprawcze węzłów. Zapytania kondycji interfejsów API umożliwiają zapytań dotyczących zdarzeń kondycji zgłoszone w podsystemie kondycji. Zapytania kondycji, które interfejsy API zwracają dane pierwotne kondycji, przechowywane w magazynie kondycji lub zagregowane, interpretuje dane kondycji dla obiektu określonego klastra.
* **Obraz Store**: Ta usługa zapewnia magazyn i stopień rozproszenia danych binarnych aplikacji. Ta usługa zapewnia proste rozproszony magazyn plików gdzie aplikacje są przekazywane do i pobierane z.

## <a name="hosting-subsystem"></a>Podsystem hostingu
Menedżer klastra informuje o hostingu podsystemu (uruchomione w każdym węźle), usług, które musi zarządzać dla określonego węzła. Podsystem hostingu następnie zarządza cyklem życia aplikacji, w tym węźle. Współpracuje ze składnikami niezawodności i kondycji, aby upewnić się, że repliki są prawidłowo umieszczone i są w dobrej kondycji.

## <a name="communication-subsystem"></a>Podsystem komunikacji
Podsystem ten zapewnia niezawodną obsługę komunikatów w ramach odnajdywania klastra i usługi za pośrednictwem usługi nazewnictwa. Usługa nazewnictwa jest rozpoznawany jako nazwy usług lokalizacji w klastrze i umożliwia użytkownikom zarządzanie nazwy usługi i właściwości. Przy użyciu usługi nazewnictwa, klienci mogą bezpiecznie komunikować się z dowolnego węzła w klastrze, aby rozpoznać nazwę usługi i pobierania metadanych usługi. Użytkownicy usługi Service Fabric przy użyciu prostego interfejsu API klienta nazewnictwa, można tworzyć usługi i klienci z możliwością obsługi bieżącą lokalizację sieciową, pomimo dynamiki węzła lub ponownie zmiany rozmiaru klastra.

## <a name="testability-subsystem"></a>Podsystem testowalności
Testowalność to pakiet narzędzi zaprojektowane specjalnie w celu testowania usług oferowanych w usłudze Service Fabric. Narzędzia pozwalają dla deweloperów, łatwo wywoływać istotnych błędów i uruchamiaj scenariusze testowania do wykonywania, a następnie sprawdź liczne Stany i przejścia, które usługa będzie wystąpić w okresie swojego istnienia, wszystko to w sposób kontrolowany i bezpieczny. Testowalność zawiera także mechanizm do uruchomienia dłużej testy, które można wykonać iterację za pomocą różnych możliwych błędów bez utraty dostępności. Zapewnia to za pomocą środowiska testowego w środowisku produkcyjnym.

