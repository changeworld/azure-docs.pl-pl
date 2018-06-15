---
title: Wizualizacja klastra przy użyciu usługi Azure Service Fabric Explorer | Dokumentacja firmy Microsoft
description: Service Fabric Explorer to aplikacja do inspekcji i zarządzania aplikacji w chmurze oraz węzłów w klastrze usługi sieć szkieletowa usług Microsoft Azure.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: mikhegn
ms.openlocfilehash: 916742d89447af4097d37b5d78e97ff86c12834c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210185"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer

Service Fabric Explorer (SFX) jest narzędziem open source za kontrolę i zarządzania klastrami sieć szkieletowa usług Azure. Eksploratora usługi sieć szkieletowa jest aplikacją dla systemu Windows i Linux. Obsługa MacOS będzie dostępna wkrótce.

## <a name="service-fabric-explorer-download"></a>Pobieranie Service Fabric Explorer

Aby pobrać Service Fabric Explorer jako aplikacja na komputerze, użyj następujących łączy:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Tej wersji programu Service Fabric Explorer może mieć więcej lub mniej funkcji niż obsługi klastra. Użytkownik może wrócić do wersji Service Fabric Explorer wdrożone do klastra w celu zapewnienia zgodności wszystkich funkcji.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Uruchomiona Service Fabric Explorer z klastra

Eksploratora usługi sieć szkieletowa jest również obsługiwane w punkt końcowy zarządzania HTTP klastra sieci szkieletowej usług. Aby uruchomić SFX w przeglądarce sieci web, przejdź do punktu końcowego zarządzania HTTP klastra z dowolnej przeglądarki — na przykład https://clusterFQDN:19080.

Ustawienia stacji roboczej developer, Service Fabric Explorer można uruchomić w klastrze lokalnym przechodząc do https://localhost:19080/Explorer. Obejrzyj ten artykuł, aby [przygotowywanie środowiska projektowego](service-fabric-get-started.md).

## <a name="connect-to-a-service-fabric-cluster"></a>Łączenie z klastrem sieci szkieletowej usług
Aby ustanowić połączenie z klastrem usługi sieć szkieletowa, należy punkt końcowy zarządzania klastrami (IP/nazwa FQDN) i port punktu końcowego zarządzania HTTP (19080 domyślnie). Na przykład: https://mysfcluster.westus.cloudapp.azure.com:19080. Użyj pola wyboru "Połącz z hostem lokalnym" nawiązywania połączenia z lokalnym klastrem na stacji roboczej.

### <a name="connect-to-a-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem
Można kontrolować dostęp klienta do klastra usługi sieć szkieletowa z certyfikatów lub przy użyciu usługi Azure Active Directory (AAD).

Próba nawiązania bezpiecznego klastrem, następnie w zależności od konfiguracji klastra trzeba będzie przedstawić certyfikat, klienta lub zaloguj się za pomocą usługi AAD.

## <a name="video-tutorial"></a>Samouczek wideo

Aby dowiedzieć się, jak używać Eksploratora usługi sieć szkieletowa, obejrzyj następujące wideo Microsoft Virtual Academy:

> [!NOTE]
> To wideo pokazuje, że Service Fabric Explorer hostowanych w klastrze usługi sieć szkieletowa nie wersji do pulpitu.
>
>

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="understand-the-service-fabric-explorer-layout"></a>Zrozumienie układu Service Fabric Explorer
Za pomocą Eksploratora usługi sieć szkieletowa można nawigować przy użyciu drzewa po lewej stronie. W katalogu głównym drzewa pulpit nawigacyjny klastra zawiera omówienie klastra, w tym podsumowanie aplikacji i kondycji węzła.

![Pulpit nawigacyjny klastra Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Wyświetl układ klastra
Węzły w klastrze usługi sieć szkieletowa usług są umieszczane między dwuwymiarowa siatki domen błędów i uaktualnienia domen. To umieszczania gwarantuje, że aplikacje pozostają dostępne obecności awarie sprzętu i aktualizacje aplikacji. Możesz wyświetlić bieżący klaster układ przy użyciu mapy klastra.

![Mapa klastra Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Wyświetl aplikacje i usługi
Klaster zawiera dwa poddrzewa: jeden dla aplikacji i drugi dla węzłów.

Można skorzystać z widoku aplikacji poruszać się po hierarchii logicznej usługi Service Fabric: aplikacji, usług, partycji i replik.

W przykładzie poniżej aplikacji **moja_aplikacja** składa się z dwóch usług **MyStatefulService** i **WebService**. Ponieważ **MyStatefulService** jest obiektem stanowym, zawiera partycji z jednego podstawowego i dwóch replik pomocniczych. Z kolei WebSvcService jest bezstanowych i zawiera pojedyncze wystąpienie.

![Widok aplikacji w narzędziu Service Fabric Explorer][sfx-application-tree]

Na każdym poziomie drzewa w okienku głównym zostaną wyświetlone odpowiednie informacje o elemencie. Można na przykład Zobacz stan kondycji i wersji określonej usługi.

![Okienko essentials Service Fabric Explorer][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Wyświetlanie węzłów klastra
Widok węzła przedstawia fizyczny układ klastra. Dla danego węzła można sprawdzić, które aplikacje mają kod wdrożony w tym węźle. W szczególności widać replik, które są aktualnie uruchomione istnieje.

## <a name="actions"></a>Akcje
Service Fabric Explorer oferuje możliwość szybkiego wywołanie akcji w węzłach, aplikacji i usług w ramach klastra.

Na przykład, aby usunąć aplikację, wybierz aplikację z drzewa po lewej stronie, a następnie wybierz **akcje** > **Usuń aplikację**.

![Usunięcie aplikacji w narzędziu Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Te same akcje można wykonać przez kliknięcie przycisku wielokropka obok każdego elementu.
>
> Wszystkie akcje, które mogą być wykonywane za pomocą Eksploratora usługi sieć szkieletowa można również przeprowadzić za pomocą programu PowerShell lub interfejsu API REST, aby włączyć automatyzacji.
>
>

Umożliwia także Eksploratora usługi sieć szkieletowa można utworzyć wystąpienia aplikacji dla danej aplikacji typu i wersji. Wybierz typ aplikacji, w widoku drzewa, a następnie kliknij przycisk **Utwórz wystąpienie aplikacji** link obok wersji chcesz w okienku po prawej stronie.

![Tworzenie wystąpienia aplikacji w narzędziu Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer nie obsługuje parametrów podczas tworzenia wystąpień aplikacji. Wystąpień aplikacji użyj domyślne wartości parametrów.
>
>

## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie aplikacji usługi Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Wdrażanie aplikacji sieci szkieletowej usług za pomocą programu PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png