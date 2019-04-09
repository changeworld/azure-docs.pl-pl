---
title: Omówienie usługi Service Fabric na platformie Azure | Microsoft Docs
description: Omówienie usługi Service Fabric obsługującej aplikacje złożone z wielu mikrousług, co umożliwia zapewnienie skalowalności i odporności. Service Fabric to platforma systemów rozproszonych używana do tworzenia skalowalnych i niezawodnych aplikacji dla chmury, które są łatwe w zarządzaniu.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: cd2895378f713749b3a1d0ac01986b728ceafd2b
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009007"
---
# <a name="overview-of-azure-service-fabric"></a>Omówienie usługi Azure Service Fabric
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług i kontenerów oraz zarządzanie nimi. Ponadto usługa Service Fabric pozwala sprostać istotnym wyzwaniom związanym z opracowywaniem natywnych aplikacji chmurowych i zarządzaniem nimi. Deweloperzy i administratorzy mogą uniknąć złożonych problemów związanych z infrastrukturą i skoncentrować się na implementowaniu wymagających obciążeń o znaczeniu strategicznym, które są skalowalne, niezawodne i łatwe w zarządzaniu. Usługa Service Fabric to platforma następnej generacji do tworzenia działających w kontenerach aplikacji w skali chmury warstwy pierwszej dla przedsiębiorstw i zarządzania nimi.

Ten krótki klip wideo zawiera wprowadzenie do usługi Service Fabric i mikrousług:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

## <a name="compliance"></a>Zgodność
Dostawcy zasobów sieci szkieletowej w usłudze Azure Service jest dostępna we wszystkich regionach platformy Azure i jest zgodny ze wszystkich certyfikatów zgodności, które platforma Azure ma; obejmuje to następujące czynności: Audyt SOC i ISO, PCI DSS, HIPAA oraz GDRP. Przejrzyj następujące czynności, aby uzyskać pełną listę certyfikatów zgodności: [Ofert zgodności](https://www.microsoft.com/trustcenter/compliance/complianceofferings)

## <a name="applications-composed-of-microservices"></a>Aplikacje złożone z mikrousług 
Usługa Service Fabric umożliwia tworzenie skalowalnych i niezawodnych aplikacji złożonych z mikrousług — działających z dużą gęstością w udostępnionej puli maszyn, nazywanej klastrem — i zarządzanie nimi. Zapewnia zaawansowane i lekkie środowisko uruchomieniowe, umożliwiające tworzenie skalowalnych, rozproszonych mikrousług stanowych i bezstanowych działających w kontenerach. Udostępnia również kompleksowe funkcje zarządzania aplikacjami, umożliwiające inicjowanie obsługi, wdrażanie, monitorowanie, uaktualnianie/poprawianie i usuwanie wdrożonych aplikacji, w tym usług konteneryzowanych.

Usługa Service Fabric jest obecnie podstawą działania wielu usług firmy Microsoft, takich jak Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype dla firm i wiele kluczowych usług platformy Azure.

Usługa Service Fabric została zaprojektowana z myślą o tworzeniu natywnych aplikacji chmurowych, które można skalować stosownie do potrzeb, od małych początkowych rozmiarów do ogromnej skali z setkami, a nawet tysiącami maszyn.

Współczesne usługi internetowe złożone są z mikrousług. Przykładami mikrousług są bramy protokołów, profile użytkowników, koszyki zakupów, usługi przetwarzania spisów, kolejki czy pamięci podręczne. Usługa Service Fabric to platforma mikrousług nadająca każdej mikrousłudze (lub każdemu kontenerowi) unikatową nazwę, która może być bezstanowa lub stanowa.

Usługa Service Fabric zapewnia kompleksowe środowisko uruchomieniowe i funkcje zarządzania cyklem życia dla aplikacji złożonych z takich mikrousług. Mikrousługi są hostowane w kontenerach wdrażanych i aktywowanych w klastrze usługi Service Fabric. Przejście od maszyn wirtualnych do kontenerów umożliwia zwiększenie gęstości o rząd wielkości. Gęstość można zwiększyć o kolejny rząd wielkości, przechodząc od kontenerów do mikrousług w kontenerach. Na przykład jeden klaster usługi Azure SQL Database obejmuje setki maszyn, na których działają dziesiątki tysięcy kontenerów, w których hostowane są łącznie setki tysięcy baz danych. Każda baza danych jest stanową mikrousługą usługi Service Fabric. 

Aby dowiedzieć się więcej na temat podejścia opartego na mikrousługach, zapoznaj się z artykułem [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Dlaczego warto tworzyć aplikacje z użyciem mikrousług?).

## <a name="container-deployment-and-orchestration"></a>Wdrażanie i orkiestracja kontenerów
Usługa Service Fabric to [orkiestrator kontenerów](service-fabric-cluster-resource-manager-introduction.md) firmy Microsoft, umożliwiający wdrażanie mikrousług w klastrze maszyn. Mikrousługi można tworzyć na wiele sposobów, na przykład korzystając z [modeli programowania usługi Service Fabric](service-fabric-choose-framework.md) czy struktury [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) lub wdrażając [dowolnie wybrany kod](service-fabric-guest-executables-introduction.md). Istotne jest to, że można łączyć w ramach jednej aplikacji usługi w procesach i usługi w kontenerach. Jeśli chcesz tylko [wdrażać kontenery i zarządzać nimi](service-fabric-containers-overview.md), usługa Service Fabric doskonale sprawdzi się jako orkiestrator kontenerów.

## <a name="any-os-any-cloud"></a>Dowolny system operacyjny, dowolna chmura
Usługa Service Fabric może działać wszędzie. Możesz tworzyć klastry usługi Service Fabric w wielu różnych środowiskach, na przykład na platformie Azure lub lokalnie, w systemie Windows Server lub Linux. Można nawet tworzyć klastry w innych chmurach publicznych. Ponadto środowisko programowania w zestawie SDK jest **identyczne** ze środowiskiem produkcyjnym i nie wymaga używania żadnych emulatorów. Innymi słowy — wszystko, co działa w lokalnym klastrze programowania, zostanie wdrożone w klastrach w innych środowiskach.

![Platforma Service Fabric][Image1]

Na potrzeby programowania dla systemu Windows zestaw SDK .NET usługi Service Fabric jest zintegrowany z programami Visual Studio i PowerShell. Zobacz [Przygotowywanie środowiska deweloperskiego w systemie Windows](service-fabric-get-started.md). Na potrzeby programowania dla systemu Linux zestaw SKD Java usługi Service Fabric jest zintegrowany ze środowiskiem Eclipse, a narzędzie Yeoman umożliwia generowanie szablonów dla platform Java i .NET Core oraz aplikacji kontenerowych. Zobacz [Przygotowywanie środowiska projektowego w systemie Linux](service-fabric-get-started-linux.md).

Aby uzyskać więcej informacji na temat tworzenia klastrów, zapoznaj się z artykułami dotyczącymi [tworzenia klastra w systemie Windows Server lub Linux](service-fabric-deploy-anywhere.md) lub w przypadku platformy Azure — tworzenia klastra [w witrynie Azure Portal](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Mikrousługi stanowe i bezstanowe dla usługi Service Fabric
Usługa Service Fabric umożliwia tworzenie aplikacji składających się z mikrousług lub kontenerów. Mikrousługi bezstanowe (na przykład bramy protokołów i internetowe serwery proxy) nie utrzymują modyfikowalnego stanu poza żądaniem i odpowiedzią serwera. Przykładem usługi bezstanowej są procesy robocze usług Azure Cloud Services. Mikrousługi stanowe (na przykład konta użytkowników, bazy danych, urządzenia, koszyki zakupów i kolejki) utrzymują modyfikowalny, autorytatywny stan poza żądaniem i odpowiedzią. Współczesne aplikacje internetowe łączą w sobie mikrousługi stanowe i bezstanowe. 

Ważną cechą wyróżniającą usługę Service Fabric jest koncentracja na tworzeniu usług stanowych przy użyciu [wbudowanych modeli programowania](service-fabric-choose-framework.md) lub konteneryzowanych usług stanowych. [Scenariusze zastosowania](service-fabric-application-scenarios.md) opisują sytuacje, w których używane są usługi stanowe.


## <a name="application-lifecycle-management"></a>Zarządzanie cyklem życia aplikacji
Usługa Service Fabric zapewnia obsługę całego cyklu życia aplikacji i ciągłej integracji/ciągłego wdrażania (CI/CD) aplikacji chmurowych, w tym kontenerów. Ten cykl obejmuje etapy od programowania przez wdrożenie, bieżące zarządzanie i utrzymanie, aż do ostatecznego wycofania.

Funkcje zarządzania cyklem życia aplikacji w usłudze Service Fabric umożliwiają administratorom aplikacji i operatorom IT inicjowanie obsługi, wdrażanie, poprawianie i monitorowanie aplikacji z użyciem prostych przepływów pracy wymagających niewielkich nakładów pracy. Dzięki tym wbudowanym przepływom pracy zapewnienie ciągłej dostępności aplikacji wymaga od operatorów IT znacznie mniej wysiłku.

Większość aplikacji łączy w sobie różne mikrousługi stanowe i bezstanowe, kontenery i inne elementy wykonywalne, wdrażane razem. Dzięki silnej typizacji aplikacji usługa Service Fabric umożliwia wdrażanie wielu wystąpień aplikacji. Każde wystąpienie jest zarządzane i uaktualniane niezależnie. Co istotne, usługa Service Fabric umożliwia wdrażanie kontenerów lub dowolnych elementów wykonywalnych i zapewnienie ich niezawodności. Na przykład za pomocą usługi Service Fabric można wdrażać elementy .NET, ASP.NET Core, node.js, kontenery systemów Windows i Linux, maszyny wirtualne Java, skrypty, elementy usługi Angular oraz dowolne inne elementy aplikacji.

Usługa Service Fabric jest zintegrowana z narzędziami do obsługi cyklu ciągłej integracja/ciągłego wdrażania, takimi jak [Azure Pipelines](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) czy [Octopus Deploy](https://octopus.com/), i współdziała też z innymi popularnymi narzędziami CI/CD.

Aby uzyskać więcej informacji o zarządzaniu cyklem życia aplikacji, zobacz [Cykl życia aplikacji](service-fabric-application-lifecycle.md). Aby uzyskać więcej informacji o wdrażaniu dowolnego kodu, zobacz [Wdrażanie aplikacji wykonywalnej gościa](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>Najważniejsze możliwości
Usługa Service Fabric umożliwia:

* Wdrażanie aplikacji na platformie Azure lub w lokalnych centrach danych z systemem Windows lub Linux bez konieczności wprowadzania zmian w kodzie. Jednokrotne utworzenie aplikacji i wdrożenie jej w dowolnej lokalizacji, w dowolnym klastrze usługi Service Fabric.
* Tworzenie skalowalnych aplikacji złożonych z mikrousług przy użyciu modeli programowania usługi Service Fabric, kontenerów lub dowolnego kodu.
* Tworzenie niezawodnych mikrousług stanowych i bezstanowych. Prostsze tworzenie aplikacji dzięki użyciu mikrousług stanowych. 
* Korzystanie z nowego modelu programowania Reliable Actors w celu tworzenia obiektów chmurowych z autonomicznym kodem i stanem.
* Wdrażanie i koordynowanie kontenerów, między innymi kontenerów systemu Windows i Linux. Usługa Service Fabric to stanowy orkiestrator kontenerów uwzględniający dane.
* Wdrażanie aplikacji w kilka sekund, z gęstością kilkuset lub kilku tysięcy aplikacji lub kontenerów na maszynę.
* Równoległe wdrażanie różnych wersji jednej aplikacji i uaktualnianie poszczególnych aplikacji niezależnie od pozostałych.
* Zarządzanie cyklem życia aplikacji bez przestoju, włącznie z uaktualnieniami przerywającymi i nieprzerywającymi działania aplikacji.
* Skalowanie liczby węzłów w klastrze w górę i w dół. Aplikacje będą skalowane automatycznie wraz ze skalowaniem węzłów.
* Monitorowanie i diagnozowanie kondycji aplikacji oraz określanie zasad wykonywania automatycznej naprawy.
* Obserwowanie koordynacji redystrybucji aplikacji w klastrze przez moduł równoważenia zasobów. Usługa Service Fabric zapewnia odzyskiwanie po awariach oraz optymalizację dystrybucji obciążenia odpowiednio do dostępnych zasobów.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki
* Więcej informacji:
  * [Dlaczego mikrousługi podejście do tworzenia aplikacji?](service-fabric-overview-microservices.md)
  * [Przegląd terminologii](service-fabric-technical-overview.md)
* Konfigurowanie [środowiska deweloperskiego w systemie Windows](service-fabric-get-started.md)  
* Konfigurowanie [środowiska projektowego w systemie Linux](service-fabric-get-started-linux.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
