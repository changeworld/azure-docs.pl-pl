---
title: Co to jest Azure Cloud Services | Dokumentacja firmy Microsoft
description: Dowiedz się o nowościach usług Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: ce88dcaedf32f293fc121cda2a088388c99badee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337536"
---
# <a name="overview-of-azure-cloud-services"></a>Omówienie usługi Azure Cloud Services
Usługi Azure Cloud Services znajduje się przykład [platforma jako usługa](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Podobnie jak [usługi Azure App Service](../app-service/overview.md), ta technologia jest przeznaczona do obsługi aplikacji, które są skalowalne, niezawodne i tanie w utrzymaniu. W ten sam sposób, że usługi App Service znajduje się na maszynach wirtualnych (VM), dlatego też jest usług Azure Cloud Services. Jednak masz większą kontrolę nad maszynami wirtualnymi. Własne oprogramowanie można zainstalować na maszynach wirtualnych, które używają usług Azure Cloud Services, a użytkownik może uzyskiwać do nich dostęp zdalnie.

![Azure Cloud Services diagramu](./media/cloud-services-choose-me/diagram.png)

Większa kontrola oznacza również mniej łatwość użycia. Chyba że potrzebne opcje dodatkową kontrolę, zazwyczaj jest szybsze i łatwiejsze do uruchomienia aplikacji sieci web, a w aplikacji sieci Web funkcji App Service w porównaniu do usług Azure Cloud Services.

Istnieją dwa typy ról, usług Azure Cloud Services. Jedyną różnicą między tymi dwoma jest o tym, jak Twoja rola znajduje się na maszynach wirtualnych:

* **Rola internetowa**: Automatycznie wdraża i obsługuje aplikację za pośrednictwem usług IIS.

* **Rola procesu roboczego**: Korzysta z usług IIS i uruchamia autonomiczny Twojej aplikacji.

Na przykład prosta aplikacja może użyć tylko jednej roli sieci web, obsługująca witryny sieci Web. Bardziej złożonych aplikacji mogą używać roli sieci web do obsługi przychodzących żądań od użytkowników, a następnie przekazać te żądania do roli procesu roboczego do przetworzenia. (Ta komunikacja może używać [usługi Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) lub [Azure Queue storage](../storage/common/storage-introduction.md).)

Jak sugeruje poprzednim rysunku, wszystkie maszyny wirtualne w jednej aplikacji uruchomione w tej samej usługi w chmurze. Dostęp użytkowników aplikacji za pomocą jednego publicznego adresu IP, za pomocą żądań automatycznie ładować równoważone w aplikacji w maszynach wirtualnych. Platforma [skaluje się i wdraża](cloud-services-how-to-scale-portal.md) maszyn wirtualnych w aplikacji usług Azure Cloud Services w taki sposób, aby uniknąć pojedynczego punktu awarii sprzętowej.

Mimo, że aplikacje są uruchamiane na maszynach wirtualnych, ważne jest zrozumienie, że usługi Azure Cloud Services zapewnia PaaS, nie infrastruktura jako usługa (IaaS). W tym miejscu jest jednym ze sposobów myślenia o nim. Rozwiązania iaas, takich jak Azure Virtual Machines należy najpierw utworzyć i skonfigurować środowisko, które aplikacja działa w. Następnie możesz wdrożyć aplikację w tym środowisku. Jesteś odpowiedzialny za zarządzanie ilości tym świecie, wykonując czynności, takie jak wdrażanie nowych wersji poprawionego systemu operacyjnego w każdej maszynie Wirtualnej. W modelu PaaS z kolei jest tak, jakby środowisko już istnieje. To wszystko, co należy zrobić, Wdróż aplikację. Zarządzanie platformy, na których ono działa, takich jak wdrażanie nowych wersji systemu operacyjnego jest obsługiwane dla Ciebie.

## <a name="scaling-and-management"></a>Skalowanie i zarządzanie
Za pomocą usług Azure Cloud Services nie są tworzone maszyn wirtualnych. Zamiast tego należy podać plik konfiguracji, które nakazuje platformie Azure, ile chcesz, takie jak "trzy wystąpienia ról sieci web" i "dwa wystąpienia roli procesu roboczego." Platforma utworzy je dla Ciebie. Nadal możesz wybrać [rozmiar](cloud-services-sizes-specs.md) tych kopii maszyn wirtualnych powinna być, ale nie jawnie tworzyć je samodzielnie. Jeśli aplikacja musi obsługiwać większe obciążenie, możesz poprosić, aby uzyskać więcej maszyn wirtualnych, a platforma Azure tworzy tych wystąpień. Jeśli zmniejsza obciążenie, możesz wyłączenie tych wystąpień i Zatrzymaj płacić za.

Aplikacji usług Azure Cloud Services jest zwykle udostępniane użytkownikom za pośrednictwem procesem dwuetapowym. Deweloper pierwszy [przekazuje aplikacji](cloud-services-how-to-create-deploy-portal.md) do obszaru przemieszczania platformy. Gdy deweloper jest gotowy do marka aplikacji na żywo, używają witryny Azure portal można zamienić przemieszczania z produkcją. To [przełączać się między środowiskiem przejściowym i produkcyjnym](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) może odbywać się bez przestojów, co pozwala uruchomionej aplikacji, można uaktualnić do nowej wersji bez zakłócania działania swoich użytkowników.

## <a name="monitoring"></a>Monitorowanie
Usługi Azure Cloud Services oferuje także funkcje monitorowania. Takie jak maszyny wirtualne wykryje serwera fizycznego nie powiodło się i ponowne uruchomienie maszyn wirtualnych, które były uruchomione na tym serwerze, na nowych komputerach. Ale usług Azure Cloud Services wykrywa także nie powiodło się maszyny wirtualne i aplikacje, nie tylko na awarie sprzętowe. W przeciwieństwie do maszyn wirtualnych ma agenta w każdej roli sieci web i proces roboczy, a więc jest możliwe uruchomienie nowych maszyn wirtualnych i wystąpień aplikacji, gdy wystąpią błędy.

Rodzaj PaaS usług Azure Cloud Services ma inne implikacje zbyt. Jest jednym z najważniejszych, że aplikacje korzystające z tej technologii mają być zapisywane działała poprawnie, gdy wszystkie wystąpienia roli sieć web lub procesu roboczego nie powiodło się. Aby to osiągnąć, aplikacji usług Azure Cloud Services nie zachowują stan, w systemie plików swoje własne maszyny wirtualne. W przeciwieństwie do maszyn wirtualnych utworzonych za pomocą maszyn wirtualnych zapisu wykonane w maszynach wirtualnych usługi chmury platformy Azure nie są trwałe. Ma nic, takich jak dysk z danymi maszyn wirtualnych. Zamiast tego aplikacji usług Azure Cloud Services powinien jawnie zapisać wszystkich stanów do usługi Azure SQL Database, obiekty BLOB, tabel lub zewnętrznego magazynu. Tworzenie aplikacji w ten sposób sprawia, że ich łatwiejsze do skali i bardziej odporne na awarie, które są zarówno ważne cele usługi Azure Cloud Services.

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie aplikacji usługi w chmurze na platformie .NET](cloud-services-dotnet-get-started.md) 
* [Tworzenie aplikacji usługi w chmurze w środowisku Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Tworzenie aplikacji usługi w chmurze w języku PHP](../cloud-services-php-create-web-role.md) 
* [Tworzenie aplikacji usługi w chmurze w języku Python](cloud-services-python-ptvs.md)



