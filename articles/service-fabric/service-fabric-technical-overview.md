---
title: Dowiedz się, terminologii sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Zawiera przegląd terminologia usługi sieci szkieletowej. W tym artykule omówiono kluczową terminologię pojęć i terminów używanych w pozostałej części dokumentacji.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: ryanwi
ms.openlocfilehash: e3da081f9b327031d6d1e0afd2f2fb52383bf933
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212072"
---
# <a name="service-fabric-terminology-overview"></a>Omówienie terminologii sieci szkieletowej usług
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. W tym artykule szczegółowo z terminologią używaną przez sieć szkieletowa usług, aby zrozumieć terminów używanych w dokumentacji.

Pojęcia wymienione w tej sekcji omówiono także następujące filmy wideo Microsoft Virtual Academy: <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">podstawowe pojęcia</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">pojęcia czasu projektowania</a>, i <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">pojęcia dotyczące środowiska uruchomieniowego</a>.

## <a name="infrastructure-concepts"></a>Pojęcia dotyczące infrastruktury
**Klaster**: zestaw połączonych z siecią maszyn wirtualnych lub fizycznych, w których są wdrożone i zarządzane z mikrousług.  Klastry mogą obejmować nawet tysiące maszyn.

**Węzeł**: nosi nazwę komputera lub maszyny Wirtualnej, która jest częścią klastra *węzła*. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają cechy, takie jak właściwości umieszczania. Każdy komputer lub maszyna wirtualna jest automatycznie uruchamiana usługa systemu Windows, `FabricHost.exe`, który zacznie działać po rozruchu, a następnie uruchamia dwa pliki wykonywalne: `Fabric.exe` i `FabricGateway.exe`. Te dwa pliki wykonywalne składają się na węźle. Dla scenariuszy testowania, może obsługiwać wiele węzłów na jednym komputerze lub maszynie Wirtualnej, uruchamiając wiele wystąpień `Fabric.exe` i `FabricGateway.exe`.

## <a name="application-concepts"></a>Pojęcia dotyczące aplikacji
**Typ aplikacji**: Nazwa wersji przypisane do kolekcji typu usługi. Jest on zdefiniowany w `ApplicationManifest.xml` pliku i osadzone w katalogu pakietu aplikacji. Katalog jest następnie skopiowana do magazynu obrazu klastra sieci szkieletowej usług. Następnie można utworzyć nazwanego aplikacji tego typu aplikacji w klastrze.

Odczyt [model aplikacji](service-fabric-application-model.md) artykułu, aby uzyskać więcej informacji.

**Pakiet aplikacji**: katalog dysk zawierający ten typ aplikacji `ApplicationManifest.xml` pliku. Odwołuje się do pakietów usług dla każdego typu usług, tworzącą typu aplikacji. Pliki w katalogu pakietu aplikacji są kopiowane do magazynu obrazu klastra sieci szkieletowej usług. Na przykład pakiet aplikacji dla typu aplikacji poczty e-mail mogą zawierać odwołania do pakietu usług kolejki, pakietu usług frontonu i pakiet usługi dla bazy danych.

**O nazwie aplikacji**: po skopiowaniu pakietu aplikacji do magazynu obrazów, Utwórz wystąpienie aplikacji w klastrze. Określ pakiet aplikacji typu aplikacji, za pomocą jego nazwy lub wersji należy utworzyć wystąpienie. Każde wystąpienie typu aplikacji przypisano nazwę uniform resource identifier (URI), wygląda następująco: `"fabric:/MyNamedApp"`. W ramach klastra można utworzyć wiele aplikacji o nazwie z typem pojedynczej aplikacji. Można również utworzyć nazwanego aplikacje z typów inną aplikację. Każda aplikacja o nazwie jest niezależnie zarządzane i numerów wersji.      

**Typ usługi**: Nazwa wersji przypisane do pakietów kodu usługi, danych i pakietów konfiguracji. Typ usługi jest zdefiniowany w `ServiceManifest.xml` pliku i osadzone w katalogu pakietu usług. Katalog pakietu usługi następnie odwołuje się pakietu aplikacji `ApplicationManifest.xml` pliku. W ramach klastra po utworzeniu aplikacji o nazwie, można utworzyć nazwanego usługi jednego z typów usługi typu aplikacji. Typ usługi `ServiceManifest.xml` plik zawiera opis usługi.

Odczyt [model aplikacji](service-fabric-application-model.md) artykułu, aby uzyskać więcej informacji.

Istnieją dwa typy usług:

* **Bezstanowe**: Użyj usługi bezstanowej, gdy trwały stan usługi jest przechowywana w usłudze magazynu zewnętrznego, takiego jak magazyn Azure, baza danych SQL Azure lub bazy danych Azure rozwiązania Cosmos. Użyj usługi bezstanowej, jeśli usługa nie ma żadnych magazynu trwałego. Na przykład usługi Kalkulator, których wartości są przekazywane do usługi, obliczenia jest wykonywane, który używa tych wartości, a następnie zwrócony wynik.
* **Stateful**: usługi stanowej należy używać sieci szkieletowej usług do zarządzania stanem usługi za pośrednictwem jej wiarygodnych kolekcje lub Reliable Actors modele programowania. Podczas tworzenia usługi o nazwie, określ liczbę partycji chcesz rozprzestrzeniać się swój stan skalowalności. Również określić, ile razy, aby replikować swój stan między węzłami niezawodności. Każda usługa o nazwie ma jednej repliki podstawowej i wiele replik pomocniczych. Stan usługi o nazwie można modyfikować podczas zapisywania do repliki podstawowej. Sieć szkieletowa usług replikuje ten stan do repliki pomocniczej do synchronizowania swój stan. Sieć szkieletowa usług automatycznie wykrywa replikę podstawową kończy się niepowodzeniem i zamienia istniejące repliki pomocniczej do repliki podstawowej. Sieć szkieletowa usług tworzy następnie nową replikę pomocniczą.  

**Repliki lub wystąpień** odwoływać się do kodu (i stan dla stanowych usług), usługi, które zostało wdrożone i uruchomiona. Zobacz [repliki i wystąpień](service-fabric-concepts-replica-lifecycle.md).

**Ponowna konfiguracja** proces zmiany w zestawie replik usługi. Zobacz [ponownej konfiguracji](service-fabric-concepts-reconfiguration.md).

**Pakiet usługi**: katalogu dysku zawierającego typ usługi `ServiceManifest.xml` pliku. Odwołuje się do tego pliku, kod, dane statyczne i pakiety konfiguracji dla typu usługi. Pliki w katalogu pakietu usługi odwołuje się typ aplikacji `ApplicationManifest.xml` pliku. Na przykład pakiet usługi może odnosić się do kodu, dane statyczne i pakiety konfiguracji, które tworzą usługi bazy danych.

**O nazwie usługi**: po utworzeniu aplikacji o nazwie, można utworzyć wystąpienia jednego z jego typów usług w klastrze. Musisz wybrać typ usługi za pomocą jego nazwa/wersja. Każdego wystąpienia typu usługi przypisano nazwę identyfikatora URI zakres w obszarze jej aplikację o nazwie identyfikatora URI. Na przykład, jeśli tworzysz "Mojabazadanych" o nazwie service w "MyNamedApp" o nazwie aplikacji, identyfikator URI wygląda: `"fabric:/MyNamedApp/MyDatabase"`. W ramach aplikacji o nazwie można utworzyć kilka usług o nazwie. Każda usługa o nazwie może mieć własny schemat partycji i wystąpienia lub liczby replik.

**Pakiet kodu**: katalogu dysku zawierającego pliki wykonywalne typ usługi, zazwyczaj są to pliki EXE/DLL. Pliki w katalogu pakietu kodu odwołuje się typ usługi `ServiceManifest.xml` pliku. Podczas tworzenia usługi o nazwie pakietu kodu jest kopiowany do węzła lub węzłów wybranych do uruchamiania usługi o nazwie. Następnie kod zaczyna działać. Istnieją dwa typy plików wykonywalnych pakietu kodu:

* **Pliki wykonywalne gościa**: pliki wykonywalne, który uruchamiany jako — znajduje się na hoście systemu operacyjnego (Windows lub Linux). Te pliki wykonywalne nie połączyć lub odwołać pliki środowiska uruchomieniowego platformy Service Fabric i w związku z tym nie używać żadnych modeli programowania sieci szkieletowej usług. Te pliki wykonywalne będą mogli korzystać z niektórych funkcji sieci szkieletowej usług, takich jak usługi nazewnictwa do odnajdywania punktu końcowego. Pliki wykonywalne gościa nie można utworzyć raportu metryki obciążenia, które są specyficzne dla każdego wystąpienia usługi.
* **Usługi plików wykonywalnych hosta**: pliki wykonywalne, używanego przez usługi sieć szkieletowa programowania modeli łącząc pliki środowiska uruchomieniowego platformy Service Fabric, włączenie funkcji sieci szkieletowej usług. Na przykład wystąpienie usługi o nazwie można zarejestrować punktów końcowych z usługą Naming Service Fabric i zgłaszać także metryki obciążenia.      

**Pakiet danych**: katalog dysku, który zawiera pliki statyczne, tylko do odczytu danych typu usługi, zwykle fotografii, pliki dźwiękowe i wideo. Pliki w katalogu pakietu danych odwołuje się typ usługi `ServiceManifest.xml` pliku. Podczas tworzenia usługi o nazwie pakiet danych jest kopiowany do węzła lub węzłów wybranych do uruchamiania usługi o nazwie. Kod zacznie działać i teraz dostęp do plików danych.

**Pakiet konfiguracji**: katalog dysku, który zawiera pliki statyczne, tylko do odczytu konfiguracji typ usługi, zwykle plików tekstowych. Pliki w katalogu pakietu konfiguracji odwołuje się typ usługi `ServiceManifest.xml` pliku. Podczas tworzenia usługi o nazwie pliki w pakiet konfiguracji są kopiowane, lub wybrać więcej węzłów do uruchamiania usługi o nazwie. Następnie kod uruchamia uruchomione i można teraz dostęp do plików konfiguracyjnych.

**Kontenery**: domyślnie wdraża sieci szkieletowej usług i aktywuje usługi jako procesów. Sieć szkieletowa usług można także wdrożyć usługi kontenera obrazów. Kontenery są technologii wirtualizacji, która Wirtualizuje system operacyjny z aplikacji. Aplikacji i jej środowiska uruchomieniowego, zależności i biblioteki systemu uruchamiane wewnątrz kontenera. Kontener ma pełne, prywatny dostęp do widoku kontenera własnych izolowane konstrukcji systemu operacyjnego. Sieć szkieletowa usług obsługuje kontenery Docker na kontenery systemu Linux i Windows Server. Aby uzyskać więcej informacji, przeczytaj [sieci szkieletowej usług i kontenery](service-fabric-containers-overview.md).

**Schemat partycji**: podczas tworzenia usługi o nazwie Określ schemat partycji. Usługi z znacznej ilości stanu podzielić dane na partycje, których stan rozprzestrzenia się w węzłach klastra. Przez dzielenia danych na partycji, stan usługi o nazwie można skalować. W partycji bezstanowych usługi o nazwie mają wystąpień, stateful o nazwie usług mają repliki. Zazwyczaj bezstanowych usługi o nazwie mieć tylko jedną partycję, ponieważ zawierają nie stan wewnętrzny. Wystąpienia partycji zapewnienia dostępności. Jeśli jedno wystąpienie nie powiedzie się, inne wystąpienia kontynuował normalne działanie, a następnie sieć szkieletowa usług tworzy nowe wystąpienie. Obsługa stateful o nazwie usługi ich stan w obrębie repliki i każda partycja ma własną zestawu, stan są utrzymywane w synchronizacji replik. Replika ulegnie awarii, sieć szkieletowa usług tworzy nową replikę z istniejących replik.

Odczyt [niezawodne usługi sieć szkieletowa usług partycji](service-fabric-concepts-partitioning.md) artykułu, aby uzyskać więcej informacji.

## <a name="system-services"></a>Usług systemowych
Brak usług systemowych, które są tworzone w każdym klastrze zapewniające możliwości platformy Service Fabric.

**Usługa nazewnictwa**: klastra sieci szkieletowej usług każdy ma usługi nazw, który jest rozpoznawany jako nazwy usługi lokalizacji w klastrze. Zarządzanie nazw usługi i właściwości, takich jak internet systemu nazw domen (DNS) dla klastra. Klienci bezpiecznego komunikowania się z dowolnego węzła w klastrze za pomocą usługi nazewnictwa do rozpoznania nazwy usługi i jego lokalizacji. Przenieś aplikacje w klastrze. Na przykład może to być z powodu błędów, równoważenia zasobów lub zmiany rozmiaru klastra. Można programować usług i klientów, które są rozpoznawane bieżącą lokalizację sieciową. Klienci uzyskać adres IP rzeczywistego maszyny i port, na którym jest obecnie uruchomiona.

Odczyt [komunikacja z usługami](service-fabric-connect-and-communicate-with-services.md) uzyskać więcej informacji dotyczących komunikacji klienta oraz usługi interfejsów API, które współpracują z usługą Naming.

**Usługa składnika Image Store**: klastra sieci szkieletowej usług każdy ma usługi składnika Image Store gdzie są przechowywane pakietów aplikacji wdrożonych, numerów wersji. Kopiowanie pakietu aplikacji do magazynu obrazów, a następnie zarejestruj typ aplikacji zawartych w tym pakiecie aplikacji. Po udostępnieniu typ aplikacji, można utworzyć aplikację o nazwie z niego. Po usunięciu wszystkich nazwanego aplikacji można wyrejestrować typu aplikacji w usłudze magazynu obrazów.

Odczyt [zrozumieć ustawienie element ImageStoreConnectionString](service-fabric-image-store-connection-string.md) Aby uzyskać więcej informacji o usłudze magazynu obrazów.

Odczyt [wdrażania aplikacji](service-fabric-deploy-remove-applications.md) artykułu, aby uzyskać więcej informacji na temat wdrażania aplikacji do usługi składnika Image Store.

**Usługa menedżera trybu failover**: każdy sieci szkieletowej usług klastra z usługą Failover Manager service, która jest odpowiedzialna za następujące akcje:
   - Wykonuje funkcje związane z wysoką dostępność i spójność usług.
   - Organizuje uaktualnienia aplikacji i klastra.
   - Współdziała z innymi składnikami systemu.

**Napraw program Manager service**: to jest usługą opcjonalne systemu, która umożliwia wykonywanie w klastrze w sposób bezpieczne, akcji naprawy automatable i przezroczysty. Napraw manager jest używany w:
   - Konserwacja Azure naprawia na [trwałości srebrna i złota](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) klastrów sieci szkieletowej usług Azure.
   - Akcje naprawy dla [poprawki aplikacji aranżacji](service-fabric-patch-orchestration-application.md)

## <a name="built-in-programming-models"></a>Wbudowane modele programowania
Dostępne są programy .NET Framework i Java modele programowania do tworzenia usług sieci szkieletowej usług:

**Niezawodne usługi**: interfejs API do tworzenia usług bezstanowych i stanowych. Usługi stanowej przechowywanie ich stanu w niezawodnej kolekcji, takie jak słownika lub kolejki. Można także podłączyć stosach różnych komunikacji, takie jak interfejsu API sieci Web i usługi Windows Communication Foundation (WCF).

**Reliable Actors**: interfejs API do tworzenia obiektów bezstanowe i stanowe za pomocą modelu programowania aktora wirtualnego. Ten model jest przydatne, jeśli masz wiele niezależnych jednostek obliczeń lub stanu. Ten model używa na podstawie Włącz model wątkowości, więc jest unikanie kodu, który uwidacznia do innych osób lub usług, ponieważ poszczególnych aktora nie może przetworzyć innych żądań przychodzących, zakończenie swoje żądania wychodzącego.

Można również uruchomić istniejących aplikacji w sieci szkieletowej usług:

**Kontenery**: sieć szkieletowa usług obsługuje wdrażanie kontenerów Docker na kontenery systemu Linux i Windows Server w systemie Windows Server 2016, wraz z możliwością w trybie izolacji funkcji Hyper-V. W sieci szkieletowej usług [model aplikacji](service-fabric-application-model.md), kontener reprezentuje hosta aplikacji, w których wiele usługi repliki są umieszczane. Sieć szkieletowa usług można uruchamiać żadnych kontenerów i scenariusz jest podobny do scenariusza pliku wykonywalnego gościa, gdy pakiet istniejącej aplikacji wewnątrz kontenera. Ponadto można [uruchamiania usługi sieć szkieletowa usług wewnątrz kontenerów](service-fabric-services-inside-containers.md) również.

**Pliki wykonywalne gościa**: można uruchamiać dowolnego typu kodu, np. Node.js, Java lub C++ w sieci szkieletowej usług Azure jako usługa. Sieć szkieletowa usług odwołuje się do tych typów usług jako gość plików wykonywalnych, które są traktowane jako usług bezstanowych. Zalety do uruchomienia pliku wykonywalnego w klastrze usługi sieć szkieletowa Gość obejmują wysoką dostępność, monitorowanie kondycji zarządzania cyklem życia aplikacji, o wysokiej gęstości i odnajdywania.

Odczyt [wybierz model programowania dla usługi](service-fabric-choose-framework.md) artykułu, aby uzyskać więcej informacji.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat sieci szkieletowej usług:

* [Omówienie sieci szkieletowej usług](service-fabric-overview.md)
* [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Dlaczego warto tworzyć aplikacje z użyciem mikrousług?)
* [Scenariusze aplikacji](service-fabric-application-scenarios.md)


