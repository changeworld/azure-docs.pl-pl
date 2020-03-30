---
title: Przykłady manifestów aplikacji usługi Azure Service Fabric
description: Dowiedz się, jak skonfigurować ustawienia manifestu aplikacji i usługi dla aplikacji sieci szkieletowej usług.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451646"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Przykłady manifestów aplikacji i manifestów sieci szkieletowej usług
Ta sekcja zawiera przykłady manifestów aplikacji i usług. Te przykłady nie są przeznaczone do pokazywać ważne scenariusze, ale aby pokazać różne ustawienia, które są dostępne i jak z nich korzystać. 

Poniżej przedstawiono indeks wyświetlanych funkcji i przykładowych manifestów, w których są częścią.

|Funkcja|Manifest|
|---|---|
|[Nadzór nad zasobami](service-fabric-resource-governance.md)|[Manifest aplikacji niezawodnych usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Uruchamianie usługi jako lokalnego konta administratora](service-fabric-application-runas-security.md)|[Manifest aplikacji niezawodnych usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Stosowanie zasad domyślnych do wszystkich pakietów kodów usługi](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Manifest aplikacji niezawodnych usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Tworzenie podmiotów głównych użytkowników i grup](service-fabric-application-runas-security.md)|[Manifest aplikacji niezawodnych usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|udostępnianie pakietu danych między wystąpieniami usługi|[Manifest aplikacji niezawodnych usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Zastępowanie punktów końcowych usługi](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Manifest aplikacji niezawodnych usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Uruchamianie skryptu podczas uruchamiania usługi](service-fabric-run-script-at-service-startup.md)|[Manifest usługi VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definiowanie punktu końcowego HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifest usługi VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Deklarowanie pakietu konfiguracyjnego](service-fabric-application-and-service-manifests.md)|[Manifest usługi VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Deklarowanie pakietu danych](service-fabric-application-and-service-manifests.md)|[Manifest usługi VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Zastępowanie zmiennych środowiskowych](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurowanie mapowania port-host kontenera](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurowanie uwierzytelniania rejestru kontenerów](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ustawianie trybu izolacji](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Określanie obrazów kontenerów specyficznych dla kompilacji systemu operacyjnego](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ustawianie zmiennych środowiskowych](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest usługi Usługi FrontEndService kontenera](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [manifest usługi wewnętrznej bazy kontenerowej](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Konfigurowanie punktu końcowego](service-fabric-get-started-containers.md#configure-communication)|[Manifest usługi Kontener FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [Manifest usługi wewnętrznej bazy kontenerów,](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)manifest usługi [VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|przekazywanie poleceń do kontenera|[Manifest usługi Container FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Importowanie certyfikatu do kontenera](service-fabric-securing-containers.md)|[Manifest usługi Container FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Konfigurowanie sterownika woluminu](service-fabric-containers-volume-logging-drivers.md)|[Manifest usługi wewnętrznej bazy danych kontenera](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

