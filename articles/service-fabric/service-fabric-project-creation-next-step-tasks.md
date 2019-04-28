---
title: Kolejne kroki tworzenia projektu usługi Service Fabric | Dokumentacja firmy Microsoft
description: Więcej informacji na temat projektu aplikacji, który został utworzony w programie Visual Studio.  Dowiedz się, jak tworzyć usługi przy użyciu samouczków i dowiedzieć się więcej na temat tworzenia usługi dla usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: atsenthi
ms.openlocfilehash: e5371cd3ea9de1993f0f824325f6cbf1e25343d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773047"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Aplikację usługi Service Fabric i następne kroki
Utworzono aplikację usługi Azure Service Fabric. W tym artykule opisano kilka samouczków, możesz wypróbować, korzeń projektu, pewne dodatkowe informacje, które mogą być zainteresowane i potencjalne następnych kroków.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Rozpoczynanie pracy z usługą samouczki, przewodniki i przykłady
Chcesz zacząć?  

Działa przez samouczek dotyczący aplikacji platformy .NET. Dowiedz się, jak [tworzenia aplikacji](service-fabric-tutorial-create-dotnet-app.md) za pomocą frontonu platformy ASP.NET Core i stanowej back-end, [wdrożenia aplikacji](service-fabric-tutorial-deploy-app-to-party-cluster.md) do klastra, [Konfigurowanie ciągłej integracji/ciągłego Dostarczania](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), i [Konfigurowanie monitorowanie i Diagnostyka](service-fabric-tutorial-monitoring-aspnet.md).

Lub, wypróbuj jedną z następujących przewodników i Utwórz pierwsze...
- [C#Niezawodne usługi w Windows](service-fabric-reliable-services-quick-start.md) 
- [C#Niezawodne usługi aktorów na Windows](service-fabric-reliable-actors-get-started.md) 
- [Usługi wykonywalnej gościa na Windows](quickstart-guest-app.md) 
- [Aplikacja kontenera dla systemu Windows](service-fabric-get-started-containers.md) 

Użytkownik może również zainteresować się wypróbowania naszej [przykładowe aplikacje](https://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Masz pytania lub opinię?  Potrzebujesz zgłosić problem?
Zapoznaj się z artykułem [często zadawane pytania dotyczące](service-fabric-common-questions.md) i znajduj odpowiedzi na możliwościach usługi Service Fabric i jak powinna być używana.

[Opcje pomocy technicznej](service-fabric-support.md) Wyświetla fora w witrynie StackOverflow i MSDN dotyczące zadawania pytań, a także opcje do zgłaszania problemów dotyczących, uzyskiwanie pomocy technicznej i przesyłanie opinii o produkcie.

## <a name="the-application-project"></a>Projekt aplikacji
Co nowego aplikacja zawiera projekt aplikacji. Może to być jeden lub dwa dodatkowe projekty, w zależności od typu wybranych usług.

Projekt aplikacji składa się z:

* Zestaw odwołania do usług, które składają się na aplikację.
* Opublikuj trzy profile (1 węzła lokalnego, 5 węzłów lokalnego i chmury), które można użyć, aby zachować preferencje dotyczące pracy z różnych środowisk — takie jak preferencje dotyczące punkt końcowy klastra i czy ma być przeprowadzane uaktualnienie wdrożeń domyślnie.
* Parametr aplikace trzy pliki (tak samo jak powyżej), można użyć do obsługi konfiguracji specyficznych dla środowiska aplikacji, takich jak liczba partycji można utworzyć dla niej. Dowiedz się, jak [Konfigurowanie aplikacji dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md).
* Skrypt wdrożenia, który służy do wdrażania aplikacji z poziomu wiersza polecenia lub w ramach ciągłej integracji i ciągłego wdrażania potoku zautomatyzowanego. Dowiedz się więcej o [wdrażanie aplikacji przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md).
* Manifest aplikacji zawiera opis aplikacji. Manifest można znaleźć w folderze ApplicationPackageRoot. Dowiedz się więcej o [manifestów aplikacji i usługi](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Dowiedz się więcej o modelach programowania
Usługa Service Fabric oferuje wiele sposobów, aby zapisać i zarządzania usługami.  Poniżej przedstawiono omówienie i podstawowe pojęcia [bezstanowych i stanowych usług Reliable Services](service-fabric-reliable-services-introduction.md), [elementów Reliable Actors](service-fabric-reliable-actors-introduction.md), [kontenery](service-fabric-containers-overview.md), [plików wykonywalnych gościa ](service-fabric-guest-executables-introduction.md), i [stanowych i bezstanowych usług platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Dowiedz się więcej o komunikacji usług
Aplikacja usługi Service Fabric składa się z różnych usług, w którym każda usługa wykonuje specjalne zadanie. Te usługi mogą komunikować się ze sobą i mogą być aplikacje klienckie poza klastrem nawiązać połączenie i komunikować się z usługami. Dowiedz się, jak [skonfigurowanie komunikacji z oraz między usługami](service-fabric-connect-and-communicate-with-services.md) w usłudze Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Informacje na temat konfigurowania zabezpieczeń aplikacji
Można zabezpieczyć aplikacje, które są uruchomione w klastrze, w ramach różnych kont użytkowników. Usługa Service Fabric pomaga również w zabezpieczenia zasobów używanych przez aplikacje w czasie wdrażania na kontach użytkowników — na przykład, pliki, katalogi i certyfikatów. Dzięki temu uruchomionych aplikacji, nawet w środowisku współdzielonym hostowanej bardziej bezpieczne od siebie nawzajem.  Dowiedz się, jak [podczas konfigurowania zasad zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).

Twoja aplikacja może zawierać poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być traktowane w postaci zwykłego tekstu. Dowiedz się, jak [zarządzania wpisami tajnymi aplikacji](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Dowiedz się więcej o cyklu życia aplikacji
Zgodnie z innymi platformami aplikacji usługi Service Fabric zwykle odbywa się przez następujące fazy: projektowania, rozwoju, testowania, wdrażanie, uaktualnianie, obsługi i usuwania. [W tym artykule](service-fabric-application-lifecycle.md) zawiera omówienie interfejsów API i jak są one używane przez różne role, w fazach cyklu życia aplikacji usługi Service Fabric.

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie klastra Windows na platformie Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Wizualizowanie klastra, w tym wdrożone aplikacje i fizyczny układ z [narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Wersja i uaktualniania usług](service-fabric-application-upgrade-tutorial.md)


