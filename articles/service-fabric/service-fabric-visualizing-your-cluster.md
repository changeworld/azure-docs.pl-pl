---
title: Wizualizowanie klastra przy użyciu usługi Azure Service Fabric Explorer | Dokumentacja firmy Microsoft
description: Narzędzie Service Fabric Explorer to aplikacja do inspekcji i zarządzanie aplikacjami w chmurze i węzłów w klastrze usługi Microsoft Azure Service Fabric.
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
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 358ebfa601ff8e4d2fb6ae91e51516cb0a933af7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716578"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer

Service Fabric Explorer (SFX) to narzędzie open source do inspekcji i zarządzania klastrami usługi Azure Service Fabric. Narzędzie Service Fabric Explorer to aplikacja komputerowa, Windows, macOS i Linux.

## <a name="service-fabric-explorer-download"></a>Pobierz narzędzia Service Fabric Explorer

Aby pobrać narzędzia Service Fabric Explorer, jak aplikacja komputerowa, użyj następujących linków:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Klasycznej wersji programu Service Fabric Explorer może mieć więcej lub mniej funkcji niż obsługa klastrów. Użytkownik może wrócić do wersji narzędzia Service Fabric Explorer wdrożona w klastrze w celu zapewnienia zgodności z funkcji pełnego.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Uruchamianie narzędzia Service Fabric Explorer z klastra

Narzędzie Service Fabric Explorer również znajduje się w punkcie końcowym zarządzania HTTP klaster usługi Service Fabric. Aby uruchomić SFX w przeglądarce sieci web, przejść do punktu końcowego zarządzania HTTP klastra z poziomu dowolnej przeglądarce — na przykład https:\//clusterFQDN:19080.

Dla konfiguracji stacji roboczej dla deweloperów, możesz uruchomić narzędzie Service Fabric Explorer w klastrze lokalnym, przechodząc do https://localhost:19080/Explorer. Ten artykuł, aby przyjrzeć się [przygotowywanie środowiska projektowego](service-fabric-get-started.md).

> [!NOTE]
> Jeśli klaster jest zabezpieczony przez certyfikat z podpisem własnym w przeglądarce sieci web będą otrzymywać komunikat o błędzie "Ta lokacja nie jest bezpieczny". Możesz po prostu przejść przez większość nowoczesnych przeglądarek sieci web przez zastąpienie ostrzeżenia. W środowisku produkcyjnym klastra powinien zostać zabezpieczony za pomocą nazwy pospolitej i wystawiony certyfikat. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Nawiąż połączenie z klastrem usługi Service Fabric
Aby połączyć się z klastrem usługi Service Fabric, należy punkt końcowy zarządzania klastrami (IP/nazwę FQDN) i portu HTTP zarządzania punktu końcowego (19080 domyślnie). Na przykład https\:/ / mysfcluster.westus.cloudapp.azure.com:19080. Użyj pola wyboru "Połącz z hostem lokalnym", aby połączyć się z lokalnym klastrem na stacji roboczej.

### <a name="connect-to-a-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem
Możesz kontrolować dostęp klienta do klastra usługi Service Fabric przy użyciu certyfikatów lub przy użyciu usługi Azure Active Directory (AAD).

Jeśli spróbujesz nawiązać połączenie z zabezpieczonym klastrem, następnie w zależności od konfiguracji klastra możesz będą musieli przedstawić certyfikat klienta lub zaloguj się przy użyciu usługi AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Omówienie układu narzędzia Service Fabric Explorer
Możesz przejść za pomocą narzędzia Service Fabric Explorer za pomocą drzewa po lewej stronie. W katalogu głównym drzewa pulpit nawigacyjny klastra zawiera omówienie klastra, w tym podsumowanie kondycji węzła i aplikacji.

![Pulpit nawigacyjny klastra usługi Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Wyświetl układ klastra
Węzły w klastrze usługi Service Fabric są umieszczane w dwuwymiarowej siatki domen błędów i uaktualnień. To umieszczania gwarantuje, że aplikacje pozostały dostępne w obecności awariami sprzętu i uaktualniania aplikacji. Można wyświetlić, jak bieżący klaster jest poukładany za pomocą mapy klastra.

![Mapa klastra usługi Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Wyświetl aplikacje i usługi
Klaster zawiera dwa poddrzewa: jeden dla aplikacji i inny wpis dla węzłów.

Widok aplikacji umożliwia nawigowanie po hierarchii logiczne usługi Service Fabric: aplikacji, usług, partycji i replik.

W przykładzie poniżej aplikacji **MyApp** składa się z dwóch usług **MyStatefulService** i **WebService**. Ponieważ **MyStatefulService** jest stanowa, obejmuje partycja o jedną podstawową i dwóch replik pomocniczych. Z kolei WebSvcService jest bezstanowy i zawiera jedno wystąpienie.

![Widok aplikacji Service Fabric Explorer][sfx-application-tree]

Na każdym poziomie drzewa w okienku głównym wyświetlana odpowiednie informacje o elemencie. Można na przykład, zobacz stan kondycji i wersji określonej usługi.

![Okienko podstawy narzędzia Service Fabric Explorer][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Wyświetl węzły klastra
Widok węzła przedstawia fizyczny układ klastra. Dla danego węzła można sprawdzić, które aplikacje mają kod wdrożony w tym węźle. Dokładniej mówiąc możesz zobaczyć replik, które są aktualnie uruchomione istnieje.

## <a name="actions"></a>Akcje
Service Fabric Explorer oferuje szybkie wykonywanie akcji na węzłach, aplikacji i usług w klastrze.

Na przykład, aby usunąć wystąpienie aplikacji, wybierz aplikację z drzewa po lewej stronie, a następnie wybierz **akcje** > **Usuń aplikację**.

![Usuwanie aplikacji w narzędziu Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Te same akcje można wykonać, klikając wielokropek obok każdego elementu.
>
> Wszystkie akcje, które mogą być wykonywane przy użyciu narzędzia Service Fabric Explorer można również przeprowadzić za pomocą programu PowerShell lub interfejsu API REST, aby włączyć usługi automation.
>
>

Narzędzie Service Fabric Explorer umożliwia również tworzenie wystąpienia aplikacji dla danej aplikacji typu i wersji. Wybierz typ aplikacji, w widoku drzewa, a następnie kliknij przycisk **Utwórz wystąpienie aplikacji** łącze obok wersji chcesz w okienku po prawej stronie.

![Utworzenie wystąpienia aplikacji w narzędziu Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Narzędzie Service Fabric Explorer nie obsługuje parametrów podczas tworzenia wystąpień aplikacji. Wystąpienia aplikacji Użyj domyślnych wartości parametrów.
>
>

## <a name="event-store"></a>Zdarzenie Store
Bazy danych EventStore to funkcja oferowana przez platformę, która dostarcza zdarzenia platformy usługi Service Fabric jest dostępna w narzędziu Service Fabric Explorer i za pośrednictwem interfejsu API REST. Można wyświetlić widoku migawki, co się dzieje w klastrze dla każdej jednostki np. węzeł, usługi, aplikacji i zapytań w zależności od czasu wystąpienia zdarzenia. Możesz również Dowiedz się więcej o bazie danych EventStore na [omówienie bazy danych EventStore](service-fabric-diagnostics-eventstore.md).   

![Bazy danych EventStore][sfx-eventstore]

>[!NOTE]
>Począwszy od usługi Service Fabric w wersji 6.4. Bazy danych EventStore nie jest domyślnie włączona i musi być włączona w szablonie usługi resource manager

>[!NOTE]
>Począwszy od usługi Service Fabric w wersji 6.4. interfejsy API bazy danych EventStore są dostępne tylko w przypadku klastrów Windows działających na platformie Azure, tylko. Pracujemy nad przenoszenie tej funkcji do systemu Linux, a także naszych autonomicznych klastrów.


## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie aplikacje usługi Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Wdrażanie aplikacji usługi Service Fabric przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
