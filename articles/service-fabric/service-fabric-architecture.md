---
title: Architektura usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Sieć szkieletowa usług to platforma systemów rozproszonych używany do tworzenia aplikacji skalowalny, niezawodny i łatwo zarządzać dla chmury. W tym artykule przedstawiono architektury sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: timlt
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: rsinha
ms.openlocfilehash: 5e69d4b09261c90fd3c33e60645fe484b816e369
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-architecture"></a>Architektura usługi Service Fabric
Sieć szkieletowa usług jest tworzone za podsystemów warstwowego. Podsystemy te umożliwiają pisać aplikacje, które są:

* Wysokiej dostępności
* Skalowalność
* Zarządzane
* Konstruowanie

Na poniższym diagramie przedstawiono główne podsystemami sieci szkieletowej usług.

![Diagram architektury sieci szkieletowej usług](media/service-fabric-architecture/service-fabric-architecture.png)

W rozproszonym systemie możliwości bezpiecznej komunikacji między węzłami w klastrze jest niezwykle istotne. Na podstawie stosu jest podsystemu transportu, który zapewnia bezpiecznej komunikacji między węzłami. Powyżej transportu podsystemu leży podsystemu federacyjnego, który klastrów różnych węzłów do pojedynczej jednostki (o nazwie klastrów), dzięki czemu sieci szkieletowej usług można wykrywanie błędów, wykonaj Elekcja wiodące i zapewnić spójne routingu. Podsystem niezawodności warstwie ponad podsystemem federacyjnego jest odpowiedzialny za niezawodność usługi sieć szkieletowa usług za pomocą mechanizmów, takich jak replikacji, zarządzanie zasobami i pracy awaryjnej. Podsystem federacyjnej jest również podsystemu hosting i aktywacji, który umożliwia zarządzanie cyklem życia aplikacji w jednym węźle. Podsystem zarządzania umożliwia zarządzanie cyklem życia aplikacji i usług. Podsystem zmianę ułatwia deweloperom aplikacji test swoich usług za pomocą symulowane błędy przed i po wdrożeniu aplikacji i usług w środowisku produkcyjnym. Sieć szkieletowa usług zapewnia możliwość rozwiązania lokalizacji usług za pośrednictwem jego podsystemu komunikacji. Modele programowania aplikacji ujawniony dla deweloperów są warstwie ponad te podsystemy wraz z modelu aplikacji, aby włączyć narzędzia.

## <a name="transport-subsystem"></a>Podsystem transportu
Podsystem transportu implementuje kanał komunikacji datagram point-to-point. Ten kanał jest używana do komunikacji w ramach klastrów sieci szkieletowej usług i komunikację między klastra sieci szkieletowej usług i klientów. Obsługuje ona jednokierunkowe i wzorce komunikacji żądanie odpowiedź, które stanowi podstawę dla implementacji emisji i multiemisji w warstwie federacyjnego. Podsystem transportu zabezpiecza komunikację przy użyciu X509 certyfikatów lub zabezpieczenia systemu Windows. Podsystem ten jest używana wewnętrznie przez usługi sieć szkieletowa usług i nie jest bezpośrednio dostępny dla deweloperów do programowania aplikacji.

## <a name="federation-subsystem"></a>Podsystem federacyjnego
Aby przyczyny o zestawie węzłów w rozproszonym systemie, musisz mieć zgodne widok systemu. Podsystem federacyjnych używa podstawowych komunikacji udostępniane przez podsystem transportu i odwzorowywały różnych węzłów w klastrze ujednoliconego pojedynczego może on przyczyny o. Zapewnia on podstawowych systemów rozproszonych wymagane przez innych podsystemów — wykrywanie błędów, wybór wiodące i spójne routingu. Podsystem federacyjnej jest oparty na tabelach skrótów rozproszonej ze spacją tokenu 128-bitowego. Podsystem tworzy topologia pierścienia w węzłach, z każdego węzła w kręgu przydzielane podzbiór tokenu miejsca na własność. Do wykrywania awarii warstwie używa mechanizm dzierżawienia na podstawie serca bicie i rozstrzygnięcia. Podsystem federacyjnego gwarantuje również za pośrednictwem skomplikowanych sprzężenia i wyjścia protokołów, które istnieje tylko jeden właściciel tokenu w dowolnym momencie. Zapewnia to wybór wiodące i spójne gwarancje routingu.

## <a name="reliability-subsystem"></a>Podsystem niezawodności
Podsystem niezawodności zapewnia mechanizm wysokiej dostępności stanu usługi sieć szkieletowa usług za pośrednictwem *replikatora*, *menedżera trybu Failover*, i *równoważenia zasobów*.

* Replikator zapewnia, że zmiany stanu w replice podstawowej usługi automatycznie zostaną zreplikowane do repliki pomocniczej zachowaniu spójności między podstawowych i pomocniczych replik w zestawie replik usługi. Replikator jest odpowiedzialny za zarządzanie kworum między replikami w zestawie replik. Interakcji z jednostki trybu failover, aby uzyskać listę operacji replikacji, a agent rekonfiguracji zapewnia konfiguracji zestawu replik. Tej konfiguracji wskazuje replik, które działania muszą zostać zreplikowane. Usługa Service Fabric realizuje replikatora domyślne, nazywany Replikator sieci szkieletowej, która może służyć przez interfejs API modelu programowania aby stan usługi wysokiej dostępności i niezawodne.
* Menedżera trybu Failover zapewnia, że węzły są dodawane do lub usunięcia z klastra, obciążenia jest automatycznej redystrybucji dostępnych węzłów. Jeśli węzeł w klastrze ulegnie awarii, klaster zostanie automatycznie ponownie skonfigurować repliki usługi do zapewnienia dostępności.
* Menedżer zasobów umieszcza repliki usługi w domenie awarii w klastrze i zapewnia, że wszystkie jednostki trybu failover są operacyjną. Menedżer zasobów również równoważy zasobów usługi na podstawowym współużytkowanej puli węzły klastra w celu osiągnięcia optymalnego obciążenia uniform dystrybucji.

## <a name="management-subsystem"></a>Podsystemu zarządzania
Podsystem zarządzania udostępnia usługi end-to-end i zarządzanie cyklem życia aplikacji. Polecenia cmdlet programu PowerShell i interfejsów API administracyjne umożliwiają zapewnianie, wdrażanie, poprawki, uaktualnienia i usuwanie aplikacji bez utraty dostępności. Podsystem zarządzania wykonuje to za pośrednictwem następujących usług.

* **Menedżer klastra**: jest to podstawowa usługa, która współdziała z Menedżera trybu Failover z niezawodności można umieścić w węzłach, na podstawie ograniczeń umieszczania usługi aplikacji. Menedżera zasobów w podsystemie pracy awaryjnej zapewnia, że ograniczenia nigdy nie są uszkodzone. Menedżer klastra zarządza cyklem życia wnioski z obsługiwać je anulować aprowizację. Umożliwia integrację z Menedżera kondycji, aby upewnić się, że dostępności aplikacji nie jest z punktu widzenia semantycznego kondycji podczas uaktualniania.
* **Menedżer kondycji**: Usługa ta umożliwia monitorowanie kondycji aplikacji, usług i jednostek klastra. Informacje o kondycji, które agregowane w magazynie kondycji scentralizowane zgłosić jednostki klastra (na przykład węzłów, usługi partycji i replik). Te informacje o kondycji zawiera kondycji ogólnej w momencie migawkę usługi i rozproszone na wielu węzłach w klastrze, dzięki któremu można podjąć niezbędne działania korygujące węzłów. Zapytań kondycji interfejsów API umożliwiają zapytań dotyczących kondycji zdarzeń zgłoszonych w podsystemie kondycji. Interfejsy API zwrócić dane pierwotne kondycji przechowywane w magazynie kondycji lub zagregowane, zapytania kondycji interpretowane danych kondycji dla obiektu określonego klastra.
* **Obraz magazynu**: ta usługa udostępnia przechowywania i dystrybucji plików binarnych aplikacji. Ta usługa udostępnia magazynu proste plików rozproszonych, których aplikacje są przekazywane do i pobierane z.

## <a name="hosting-subsystem"></a>Podsystem hostingu
Menedżer klastra informuje hostingu podsystemu (uruchomione w każdym węźle), usług, które wymaga do zarządzania dla określonego węzła. Podsystem hostingu następnie umożliwia zarządzanie cyklem życia aplikacji w tym węźle. Interakcji z składniki niezawodność i kondycji, upewnij się, że repliki są poprawnie umieszczony i są w dobrej kondycji.

## <a name="communication-subsystem"></a>Podsystem komunikacji
Podsystem ten zapewnia niezawodna obsługa komunikatów w ramach odnajdywania klastra i usługi za pośrednictwem usługi nazw. Usługa nazewnictwa rozpoznawania nazw usługi do lokalizacji w klastrze i umożliwia użytkownikom zarządzanie nazwy usługi i właściwości. Przy użyciu usługi nazewnictwa, klienci mogą bezpiecznego komunikowania się z dowolnego węzła w klastrze, aby rozwiązać nazwę usługi i pobieranie metadanych usługi. Użytkownicy sieci szkieletowej usług za pomocą prostego nazewnictwa klienta interfejsu API, można tworzyć usługi i klienci z możliwością obsługi bieżącą lokalizację sieciową pomimo dynamiki węzła lub ponownie zmiany rozmiaru klastra.

## <a name="testability-subsystem"></a>Podsystem testowania
Pola to pakiet narzędzi zaprojektowane specjalnie dla usługi sieci szkieletowej usług w oparciu testowania. Narzędzia umożliwiają dewelopera łatwo wywołać znaczenie błędów i uruchom scenariuszy testowania wykonywania i sprawdzania poprawności wiele stanów i przejść, które usługa może wystąpić w jego okres istnienia, w sposób kontrolowanych i bezpiecznych. Pola są także mechanizm Uruchom dłużej testy, które można wykonać iterację za pośrednictwem różnych potencjalnych awarii bez utraty dostępności. Udostępnia środowiska testowego w środowisku produkcyjnym.

