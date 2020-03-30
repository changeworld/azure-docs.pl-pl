---
title: Omówienie modelu programowania usługi Service Fabric
description: 'Usługa Fabric oferuje dwie struktury dla tworzenia usług: struktury aktora i struktury usług. Oferują one różne kompromisy w prostocie i kontroli.'
author: vturecek
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 11e32c9d1290227e638a314ed8417b1bed906842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749535"
---
# <a name="service-fabric-programming-model-overview"></a>Omówienie modelu programowania usługi Service Fabric

Usługa Service Fabric oferuje wiele sposobów pisania i zarządzania usługami. Usługi można użyć interfejsów API sieci szkieletowej usług, aby w pełni korzystać z funkcji platformy i struktur aplikacji. Usługi mogą być również dowolne skompilowany program wykonywalny napisany w dowolnym języku lub kod uruchomiony w kontenerze hostowanym w klastrze sieci szkieletowej usług.

## <a name="guest-executables"></a>Pliki wykonywalne gościa

Plik [wykonywalny gościa](service-fabric-guest-executables-introduction.md) to istniejący, dowolny plik wykonywalny (napisany w dowolnym języku), który można uruchomić jako usługa w aplikacji. Pliki wykonywalne gościa nie wywołują bezpośrednio interfejsów API SDK sieci szkieletowej usług. Jednak nadal korzystają z funkcji, które oferuje platforma, takich jak wykrywalność usług, niestandardowe raportowanie kondycji i obciążenia, wywołując interfejsy API REST udostępniane przez sieci szkieletowe usług. Mają również pełną obsługę cyklu życia aplikacji.

Rozpocznij pracę z plikami wykonywalnymi gościa, wdrażając [pierwszą aplikację wykonywalną gościa](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Kontenery

Domyślnie sieci szkieletowej usług wdraża i aktywuje usługi jako procesy. Sieci szkieletowej usług można również wdrożyć usługi w [kontenerach.](service-fabric-containers-overview.md) Usługa Sieci szkieletowej obsługuje wdrażanie kontenerów systemu Linux i kontenerów systemu Windows w systemie Windows Server 2016 i nowszych. Obrazy kontenerów można pobierać z dowolnego repozytorium kontenerów i wdrażać na komputerze. Istniejące aplikacje można wdrożyć jako pliki wykonywalne gościa, bezstanowe lub stanowe usługi niezawodne lub niezawodne podmioty w kontenerach i można mieszać usługi w procesach i usługach w kontenerach w tej samej aplikacji.

[Dowiedz się więcej o konteneryzacji usług w systemie Windows lub Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services

Niezawodne usługi to lekka struktura do pisania usług, które integrują się z platformą sieci szkieletowej usług i korzystają z pełnego zestawu funkcji platformy. Niezawodne usługi zapewniają minimalny zestaw interfejsów API, które umożliwiają środowisku wykonawczemu sieci szkieletowej usług zarządzanie cyklem życia usług i które umożliwiają usługom interakcję ze środowiskiem uruchomieniowym. Struktura aplikacji jest minimalna, co daje pełną kontrolę nad wyborami projektowania i implementacji i może służyć do obsługi innych struktur aplikacji, takich jak ASP.NET Core.

Niezawodne usługi mogą być bezstanowe, podobnie jak większość platform usług, takich jak serwery sieci web, w których każde wystąpienie usługi jest tworzone równe i stan jest zachowywany w rozwiązaniu zewnętrznym, takim jak Usługa Azure DB lub Usługa Azure Table Storage.

Wyłącznie dla sieci szkieletowej usług, niezawodne usługi mogą być również stanowe, gdzie stan jest zachowywany bezpośrednio w samej usłudze przy użyciu niezawodnych kolekcji. Stan jest wysoce dostępny za pośrednictwem replikacji i dystrybuowane za pośrednictwem partycjonowania, wszystkie zarządzane automatycznie przez sieci szkieletowej usług.

[Dowiedz się więcej o niezawodnych usługach](service-fabric-reliable-services-introduction.md) lub rozpocznij [pracę, pisząc swoją pierwszą niezawodną usługę.](service-fabric-reliable-services-quick-start.md)

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core to wieloplatformowa struktura typu open source do tworzenia nowoczesnych aplikacji połączonych z Internetem w chmurze, takich jak aplikacje internetowe, aplikacje IoT i zaplecze mobilne. Usługa Service Fabric integruje się z ASP.NET Core, dzięki czemu można pisać zarówno bezstanowych, jak i stanowych aplikacji ASP.NET Core, które korzystają z niezawodnych kolekcji i zaawansowanych możliwości aranżacji sieci szkieletowej usług.

[Dowiedz się więcej o ASP.NET Core in Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) lub rozpocznij [pracę, pisząc pierwszą ASP.NET podstawową sieci szkieletową.](service-fabric-tutorial-create-dotnet-app.md)

## <a name="reliable-actors"></a>Reliable Actors

Zbudowana na podstawie niezawodnych usług, struktura niezawodnego aktora jest strukturą aplikacji, która implementuje wzorzec [aktora wirtualnego,](https://research.microsoft.com/en-us/projects/orleans/) oparty na [modeluktora](https://en.wikipedia.org/wiki/Actor_model)obliczeniowego. Struktura niezawodnego aktora używa niezależnych jednostek obliczeniowych i stanu z wykonaniem jednowątkowym o nazwie *actors*. Struktura niezawodnego aktora zapewnia wbudowaną komunikację dla aktorów i wstępnie ustawionych konfiguracji trwałości stanu i skalowania w poziomie.

Ponieważ Reliable Actors jest strukturą aplikacji opartą na niezawodnych usługach, jest w pełni zintegrowana z platformą sieci szkieletowej usług i korzysta z pełnego zestawu funkcji oferowanych przez platformę.

[Dowiedz się więcej o niezawodnych aktorach](service-fabric-reliable-actors-introduction.md) lub rozpocznij [pisanie pierwszej usługi Reliable Actor](service-fabric-reliable-actors-get-started.md)

[Tworzenie usługi front-end przy użyciu ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Następne kroki

[Omówienie sieci szkieletowej i kontenerów usług](service-fabric-containers-overview.md)

[Omówienie usług Reliable Services](service-fabric-reliable-services-introduction.md)

[Omówienie elementów Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Tkanina serwisowa i ASP.NET rdzeń](service-fabric-reliable-services-communication-aspnetcore.md)
