---
title: Usługa Azure Service Fabric kontenera aplikacji manifestu przykłady | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować aplikację i obsługiwania manifestu ustawienia aplikacji usługi Service Fabric.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 85a3066095cfc30da19b06d26f41bdc156f85832
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60718227"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Aplikacja usługi Service Fabric i usługa manifestu przykłady
Ta sekcja zawiera przykłady manifestów aplikacji i usługi. Te przykłady nie są przeznaczone do wyświetlenia ważnych scenariuszy, ale aby pokazać różne ustawienia, które są dostępne i jak z nich korzystać. 

Oto funkcji pokazanej indeksu i manifest(s) przykład, są one częścią.

|Cecha|Manifest|
|---|---|
|[Resource governance (Zarządzanie zasobami)](service-fabric-resource-governance.md)|[Niezawodne manifest aplikacji usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Uruchamianie usługi jako konto administratora lokalnego](service-fabric-application-runas-security.md)|[Niezawodne manifest aplikacji usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Zastosuj zasady domyślne do wszystkie pakiety kodu usługi](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Niezawodne manifest aplikacji usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Tworzenie użytkowników i grup jednostek](service-fabric-application-runas-security.md)|[Niezawodne manifest aplikacji usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|Udostępnianie pakietów danych między wystąpieniami usług|[Niezawodne manifest aplikacji usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Zastąp punkty końcowe usługi](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Niezawodne manifest aplikacji usług](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Za pomocą skryptu podczas uruchamiania usługi](service-fabric-run-script-at-service-startup.md)|[Manifest usługi VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definiowanie punktu końcowego HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifest usługi VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Deklaracja pakietu konfiguracji](service-fabric-application-and-service-manifests.md)|[VotingData manifestu usługi](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Deklaracja pakietu danych](service-fabric-application-and-service-manifests.md)|[VotingData manifestu usługi](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Zastąp zmienne środowiskowe](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Skonfiguruj mapowanie kontenera typu port do hosta](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Skonfiguruj uwierzytelnianie rejestru kontenerów](service-fabric-get-started-containers.md#configure-container-registry-authentication)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ustawia tryb izolacji](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Określanie obrazów kontenera specyficzne dla kompilacji systemu operacyjnego](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ustawianie zmiennych środowiskowych](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest usługi FrontEndService kontenera](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [BackEndService kontenera manifestu usługi](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Skonfiguruj punkt końcowy](service-fabric-get-started-containers.md#configure-communication)|[Manifest usługi FrontEndService kontenera](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [manifestu usługi kontenera BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData manifestu usługi](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|Przekaż poleceń do kontenera|[Manifest usługi FrontEndService kontenera](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Zaimportuj certyfikat do kontenera](service-fabric-securing-containers.md)|[Manifest usługi FrontEndService kontenera](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Konfigurowanie sterownika woluminu](service-fabric-containers-volume-logging-drivers.md)|[Manifest usługi BackEndService kontenera](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

