---
title: Architektura sieci szkieletowej usług Azure
description: W tym artykule opisano architekturę sieci szkieletowej usług, platformy systemów rozproszonych używanej do tworzenia skalowalnych, niezawodnych i łatwo zarządzanych aplikacji dla chmury.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024708"
---
# <a name="service-fabric-architecture"></a>Architektura usługi Service Fabric

Sieci szkieletowej usług jest zbudowany z warstwowych podsystemów. Te podsystemy umożliwiają zapisywanie aplikacji, które są:

* Wysoka dostępność
* Skalowalność
* Zarządzaniu
* Testować

Na poniższym diagramie przedstawiono główne podsystemy sieci szkieletowej usług.

![Diagram architektury sieci szkieletowej usług](media/service-fabric-architecture/service-fabric-architecture.png)

W systemie rozproszonym możliwość bezpiecznej komunikacji między węzłami w klastrze ma kluczowe znaczenie. U podstawy stosu znajduje się podsystem transportu, który zapewnia bezpieczną komunikację między węzłami. Nad podsystemem transportu znajduje się podsystem federacji, który klastruje różne węzły w jedną jednostkę (nazwane klastry), dzięki czemu sieć szkieletowa usług może wykrywać awarie, przeprowadzać wybory lidera i zapewniać spójne routing. Podsystem niezawodności, warstwowy na podstawie podsystemu federacji, jest odpowiedzialny za niezawodność usług sieci szkieletowej usług za pośrednictwem mechanizmów, takich jak replikacja, zarządzanie zasobami i pracy awaryjnej. Podsystem federacyjny stanowi również podstawę podsystemu hostingu i aktywacji, który zarządza cyklem życia aplikacji w jednym węźle. Podsystem zarządzania zarządza cyklem życia aplikacji i usług. Podsystem testowalności pomaga deweloperom aplikacji testować swoje usługi za pomocą symulowanych błędów przed i po wdrożeniu aplikacji i usług w środowiskach produkcyjnych. Sieć szkieletowa usług umożliwia rozpoznawanie lokalizacji usług za pośrednictwem podsystemu komunikacyjnego. Modele programowania aplikacji udostępniane deweloperom są warstwowe na wierzchu tych podsystemów wraz z modelem aplikacji, aby włączyć narzędzia.

## <a name="transport-subsystem"></a>Podsystem transportu

Podsystem transportu implementuje kanał komunikacji datagramu punkt-punkt. Ten kanał jest używany do komunikacji w klastrach sieci szkieletowej usług i komunikacji między klastrem sieci szkieletowej usług a klientami. Obsługuje wzorce komunikacji jednokierunkowej i odpowiedzi na żądanie, która stanowi podstawę do implementowania emisji i multiemisji w warstwie Federacja. Podsystem transportu zabezpiecza komunikację przy użyciu certyfikatów X509 lub zabezpieczeń systemu Windows. Ten podsystem jest używany wewnętrznie przez sieci szkieletowej usług i nie jest bezpośrednio dostępny dla deweloperów do programowania aplikacji.

## <a name="federation-subsystem"></a>Podsystem federacji

Aby rozsądek o zestaw węzłów w systemie rozproszonym, należy mieć spójny widok systemu. Podsystem federacyjny używa ujednoliconych wiązek komunikacji dostarczonych przez podsystem transportu i łączy różne węzły w jeden ujednolicony klaster, którego może uzasadniać. Zapewnia prymitywy systemów rozproszonych potrzebne przez inne podsystemy - wykrywanie awarii, wybór lidera i spójne routingu. Podsystem federacji jest zbudowany na podstawie rozproszonych tabel mieszania z 128-bitowym miejscem tokenu. Podsystem tworzy topologii pierścienia nad węzłami, z każdego węzła w pierścieniu są przydzielane podzbiór miejsca tokenu na własność. W przypadku wykrywania awarii warstwa wykorzystuje mechanizm leasingu oparty na biciu serca i arbitrażu. Podsystem federacji gwarantuje również za pośrednictwem skomplikowanych protokołów sprzężenia i wyjazdu, że w dowolnym momencie istnieje tylko jeden właściciel tokenu. Zapewnia to wybór lidera i spójne gwarancje routingu.

## <a name="reliability-subsystem"></a>Podsystem niezawodności

Podsystem niezawodności zapewnia mechanizm, aby stan usługi sieci szkieletowej usług był wysoce dostępny dzięki użyciu *programu Replikator*, *Menedżera trybu failover*i *bilansowania zasobów.*

* Replikator zapewnia, że zmiany stanu repliki usługi podstawowej będą automatycznie replikowane do replik pomocniczych, zachowując spójność między replikami podstawowymi i pomocniczymi w zestawie replik usługi. Replikator jest odpowiedzialny za zarządzanie kworum między replikami w zestawie replik. Współdziała z jednostką trybu failover, aby uzyskać listę operacji do replikacji, a agent ponownej konfiguracji zapewnia konfigurację zestawu replik. Ta konfiguracja wskazuje, które repliki operacje muszą być replikowane. Sieci szkieletowej usług zawiera domyślny replikator o nazwie Replicator sieci szkieletowej, który może być używany przez interfejs API modelu programowania, aby stan usługi wysokiej dostępności i niezawodności.
* Menedżer trybu failover zapewnia, że gdy węzły są dodawane lub usuwane z klastra, obciążenie jest automatycznie rozprowadzane między dostępnymi węzłami. Jeśli węzeł w klastrze ulegnie awarii, klaster automatycznie ponownie skonfiguruje repliki usługi w celu utrzymania dostępności.
* Menedżer zasobów umieszcza repliki usług w domenach awarii w klastrze i zapewnia, że wszystkie jednostki trybu failover działają. Menedżer zasobów równoważy również zasoby usługi w podstawowej puli współużytkowanej węzłów klastra, aby osiągnąć optymalny równomierny rozkład obciążenia.

## <a name="management-subsystem"></a>Podsystem zarządzania

Podsystem zarządzania zapewnia kompleksowe zarządzanie usługami i cyklem życia aplikacji. Polecenia cmdlet programu PowerShell i administracyjne interfejsy API umożliwiają aprowizowanie, wdrażanie, instalowanie poprawek, uaktualniania i usuwania z obsługi administracyjnej aplikacji bez utraty dostępności. Podsystem zarządzania wykonuje to za pośrednictwem następujących usług.

* **Menedżer klastrów:** Jest to usługa podstawowa, która współdziała z Menedżerem trybu failover od niezawodności do umieszczania aplikacji w węzłach na podstawie ograniczeń umieszczania usługi. Menedżer zasobów w podsystemie trybu failover zapewnia, że ograniczenia nigdy nie zostaną przerwane. Menedżer klastra zarządza cyklem życia aplikacji od udostępniania do wyrównywania. Integruje się z menedżerem kondycji, aby upewnić się, że dostępność aplikacji nie zostanie utracona z punktu widzenia kondycji semantycznej podczas uaktualniania.
* **Menedżer kondycji:** Ta usługa umożliwia monitorowanie kondycji aplikacji, usług i jednostek klastra. Jednostki klastra (takie jak węzły, partycje usług i repliki) mogą raportować informacje o kondycji, które są następnie agregowane w scentralizowanym magazynie kondycji. Te informacje o kondycji zawiera ogólną migawkę kondycji punktu w czasie usług i węzłów rozproszonych w wielu węzłach w klastrze, umożliwiając podjęcie wszelkich niezbędnych działań naprawczych. Interfejsy API zapytań o kondycji umożliwiają wykonywanie zapytań o zdarzenia dotyczące kondycji zgłoszone do podsystemu kondycji. Interfejsy API kwerend kondycji zwracają surowe dane kondycji przechowywane w magazynie kondycji lub zagregowane, interpretowane dane kondycji dla określonej jednostki klastra.
* **Magazyn obrazów:** Ta usługa zapewnia przechowywanie i dystrybucję plików binarnych aplikacji. Ta usługa zapewnia prosty rozproszony magazyn plików, do którego aplikacje są przekazywane i pobierane.

## <a name="hosting-subsystem"></a>Podsystem hostingu

Menedżer klastra informuje podsystem hostingu (uruchomiony w każdym węźle), które usługi musi zarządzać dla określonego węzła. Podsystem hostingu następnie zarządza cyklem życia aplikacji w tym węźle. Współdziała ze składnikami niezawodności i kondycji, aby upewnić się, że repliki są prawidłowo umieszczone i są w dobrej kondycji.

## <a name="communication-subsystem"></a>Podsystem komunikacyjny

Ten podsystem zapewnia niezawodne wiadomości w klastrze i odnajdywania usług za pośrednictwem usługi nazewnictwa. Usługa nazewnictwo rozpoznaje nazwy usług do lokalizacji w klastrze i umożliwia użytkownikom zarządzanie nazwami i właściwościami usług. Za pomocą usługi nazewnictwa klienci mogą bezpiecznie komunikować się z dowolnym węzłem w klastrze, aby rozpoznać nazwę usługi i pobrać metadane usługi. Za pomocą prostego interfejsu API klienta nazewnictwa użytkownicy sieci szkieletowej usług mogą tworzyć usługi i klientów zdolnych do rozpoznawania bieżącej lokalizacji sieciowej pomimo dynamiki węzła lub ponownego rozmiaru klastra.

## <a name="testability-subsystem"></a>Podsystem testowalność

Testability to zestaw narzędzi specjalnie zaprojektowanych do testowania usług zbudowanych na sieci szkieletowej usług. Narzędzia pozwalają deweloperowi łatwo wywoływać znaczące błędy i uruchamiać scenariusze testów, aby wykonywać i weryfikować liczne stany i przejścia, które usługa będzie doświadczać przez cały okres jej istnienia, a wszystko to w sposób kontrolowany i bezpieczny. Testability zapewnia również mechanizm uruchamiania dłuższych testów, które można iterować przez różne możliwe awarie bez utraty dostępności. Zapewnia to środowisko testowe w środowisku produkcyjnym.
