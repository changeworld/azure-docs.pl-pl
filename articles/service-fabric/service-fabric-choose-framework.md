---
title: Omówienie modelu programowania usługi Service Fabric | Dokumentacja firmy Microsoft
description: 'Usługa Service Fabric oferuje dwie struktury do tworzenia usług: Struktura aktora i w ramach usługi. Oferują one różne wad i zalet prostota i kontrola.'
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: d764cbe2df78cb9029a4109caa2998ddded5d6ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60341968"
---
# <a name="service-fabric-programming-model-overview"></a>Omówienie modelu programowania usługi Service Fabric
Usługa Service Fabric oferuje wiele sposobów, aby zapisać i zarządzania usługami. Usługi można używać interfejsów API usługi Service Fabric w pełni wykorzystać funkcje platformy i struktury aplikacji. Usługi mogą być także skompilowany program wykonywalny napisane w dowolnym języku lub kodu uruchamianego w kontenerze hostowanych w klastrze usługi Service Fabric.

## <a name="guest-executables"></a>Pliki wykonywalne gościa
A [pliku wykonywalnego gościa](service-fabric-guest-executables-introduction.md) jest istniejące, dowolnego pliku wykonywalnego (napisane w dowolnym języku), można uruchomić jako usługę w aplikacji. Pliki wykonywalne gościa należy wywoływać bezpośrednio interfejsów API zestawu SDK usługi Service Fabric. Jednak mogą nadal korzystać z funkcji, które oferuje platforma, takich jak usługi odnajdywania, niestandardowe kondycji i obciążenia raportowania przez wywołanie interfejsów API REST udostępniane przez usługę Service Fabric. Mają one również obsługę cyklu życia pełnej aplikacji.

Wprowadzenie do plików wykonywalnych gościa przez wdrożenie pierwszego [aplikacja wykonywalna gościa](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Containers
Domyślnie Usługa Service Fabric wdraża i aktywuje usługi jako procesów. Usługa Service Fabric można także wdrożyć usługi w [kontenery](service-fabric-containers-overview.md). Usługa Service Fabric obsługuje wdrażanie kontenerów systemu Linux i kontenerów Windows w systemie Windows Server 2016. Obrazy kontenerów można pobierane z dowolnym repozytorium kontenerów i wdrożone na maszynie. Istniejące aplikacje można wdrożyć jako plików wykonywalnych gościa usługi Service Fabric bezstanowych lub stanowych usług Reliable services i Reliable Actors w kontenerach, a można łączyć usługi w procesach i usługi w kontenerach w tej samej aplikacji.

[Dowiedz się więcej o konteneryzowania usługi Windows lub Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
Usług Reliable Services to architektura lekki dotyczące pisania usługi, które integrują się z platformy usługi Service Fabric oraz korzystać z pełnego zestawu funkcji platformy. Usług Reliable Services zapewniają minimalny zestaw interfejsy API umożliwiające zarządzanie cyklem życia Twoich usług środowiska uruchomieniowego usługi Service Fabric i umożliwiające wchodzić w interakcje ze środowiskiem uruchomieniowym usług. Struktura aplikacji jest minimalny, co daje pełną kontrolę nad opcje projektowania i implementacji i mogą być używane do obsługi żadnych innych strukturę aplikacji, takich jak ASP.NET Core.

Usług Reliable Services może być bezstanowe, podobny do większości platform usług, takich jak serwery sieci web, w których każde wystąpienie usługi jest tworzona w taki sam i stanu są utrwalane w rozwiązaniu zewnętrznych, takich jak baza danych Azure lub usługi Azure Table Storage.

Usług Reliable Services można też stanowy wzajemnie się do usługi Service Fabric, których stan jest trwały bezpośrednio w usłudze, używając elementów Reliable Collections. Stan jest wykonywane o wysokiej dostępności dzięki replikacji i dystrybuowane za pośrednictwem partycjonowanie, wszystkie zarządzane automatycznie przez usługę Service Fabric.

[Dowiedz się więcej na temat usług Reliable Services](service-fabric-reliable-services-introduction.md) lub zacznij od [pisania pierwszej usługi Reliable](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core
ASP.NET Core to nowe środowisko typu open source i dla wielu platform do tworzenia nowoczesnych aplikacji działających w chmurze podłączonej do Internetu, takich jak aplikacje sieci web, aplikacji IoT i zapleczy aplikacji mobilnych. Usługi Service Fabric jest zintegrowany z platformy ASP.NET Core, więc stanowe i bezstanowe platformy ASP.NET Core mogą pisać aplikacje, które korzystać z elementów Reliable Collections i możliwości organizowania zaawansowane usługi Service Fabric.

[Dowiedz się więcej na temat platformy ASP.NET Core w usłudze Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) lub zacznij od [pisania pierwszej aplikacji ASP.NET Core usługi Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors
Zbudowana na podstawie usług Reliable Services, framework Reliable Actor to platforma aplikacji, która implementuje wzorzec wirtualnego aktora, oparte na wzorcu projektowym aktora. Framework Reliable Actor używa niezależnych zespołów mocy obliczeniowej i stan z jednowątkowe wykonywania o nazwie aktorów. Framework Reliable Actor zapewnia komunikacji wbudowanej aktorów i wstępnie ustawionych stan konfiguracji trwałości i skalowalnego w poziomie.

Ponieważ elementów Reliable Actors to struktura aplikacji oparta na usług Reliable Services, jest w pełni zintegrowany z platformy usługi Service Fabric i korzysta z pełnego zestawu funkcji oferowanych przez platformę.

[Dowiedz się więcej o usłudze Reliable Actors](service-fabric-reliable-actors-introduction.md) lub zacznij od [pisania pierwszej usługi Reliable Actor](service-fabric-reliable-actors-get-started.md)


[Tworzenie usługi frontonu przy użyciu platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Kolejne kroki
[Omówienie usługi Service Fabric i kontenery](service-fabric-containers-overview.md)

[Omówienie usług Reliable Services](service-fabric-reliable-services-introduction.md)

[Omówienie elementów Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Usługa Service Fabric i programu ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)




