---
title: Opcje obliczeń platformy Azure - usługi w chmurze Azure | Dokumentacja firmy Microsoft
description: 'Dowiedz się więcej o hostingu opcje i jak działają obliczeń platformy Azure: App Service, usługi w chmurze Azure i maszyny wirtualne'
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 2871a8c02db0ffc6d9033724e7c9f4a454afef8e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29120288"
---
# <a name="should-i-choose-azure-cloud-services-or-something-else"></a>Należy wybrać usługi w chmurze Azure lub coś innego?
Usługi w chmurze Azure jest wybór dla Ciebie? Platforma Azure oferuje różne modele hostingu uruchamiania aplikacji. Każda z nich zapewnia inny zestaw usług. Które z nich można wybrać, zależy od tego, dokładnie co próbujesz wykonać.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-azure-cloud-services"></a>Podaj informacje o usługi w chmurze Azure
Usługi w chmurze platformy Azure jest przykładem [platforma jako usługa](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Podobnie jak [usłudze Azure App Service](../app-service/app-service-web-overview.md), ta technologia umożliwia obsługę aplikacji, które są skalowalny, niezawodny i niedrogie do działania. W ten sam sposób, że usługi aplikacji znajduje się na maszynach wirtualnych (VM), dlatego jest za usługi w chmurze Azure. Jednak masz większą kontrolę nad maszynami wirtualnymi. Można zainstalować lokalnie własne oprogramowanie na maszynach wirtualnych, które używają usług Azure Cloud Services i będą dostępne zdalnie.

![Diagram usługi w chmurze Azure](./media/cloud-services-choose-me/diagram.png)

Większa kontrola oznacza również mniej łatwość użycia. Chyba że potrzebne opcje dodatkową kontrolę zwykle jest szybsze i łatwiejsze do uruchomienia aplikacji sieci web i działa w aplikacji sieci Web funkcji usługi App Service w porównaniu do usługi w chmurze Azure.

Istnieją dwa typy ról usługi w chmurze Azure. Jedyną różnicą między tymi dwoma jest sposób roli użytkownika jest obsługiwana na maszynach wirtualnych:

* **Rola sieci Web**: automatycznie wdraża i hostuje aplikację za pośrednictwem usług IIS.

* **Rola proces roboczy**: nie korzysta z usług IIS i uruchamia autonomiczny Twojej aplikacji.

Prosta aplikacja może na przykład użyć tylko jednego rolę sieci web, obsługująca witryny sieci Web. Bardziej złożonych aplikacji mogą używać roli sieci web do obsługi żądań przychodzących od użytkowników, a następnie przekazać te żądania na rolę procesu roboczego do przetwarzania. (Tej komunikacji może używać [Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) lub [magazynu kolejek Azure](../storage/common/storage-introduction.md).)

Jak sugeruje powyższej ilustracji, wszystkie maszyny wirtualne w jednej aplikacji uruchomić w tej samej usłudze w chmurze. Użytkownikom dostępu aplikacji za pomocą pojedynczego publicznego adresu IP, z żądaniami automatyczne ładowanie równoważone między maszynami wirtualnymi aplikacji. Platforma [skaluje i wdraża](cloud-services-how-to-scale-portal.md) maszyn wirtualnych w aplikacji usług Azure Cloud Services w taki sposób, który pozwala uniknąć pojedynczy punkt awarii sprzętowej.

Mimo że działają na maszynach wirtualnych jest pamiętać, że usługi w chmurze Azure oferuje PaaS, nie infrastruktura jako usługa (IaaS). W tym miejscu jest jednym ze sposobów traktować go. Z IaaS, takich jak maszyny wirtualne platformy Azure należy najpierw utworzyć i skonfigurować uruchomieniu aplikacji w środowisku. Następnie możesz wdrożyć aplikację w tym środowisku. Wszystko jest odpowiedzialny za zarządzanie znacznie tego świecie, wykonując czynności takich jak wdrażanie nowych poprawioną wersji systemu operacyjnego w każdej maszyny Wirtualnej. W PaaS natomiast jest tak, jakby środowisko już istnieje. Wszystko, co należy zrobić, jest wdrażanie aplikacji. Zarządzanie platformy, którą jest uruchamiany na, takich jak wdrażanie nowej wersji systemu operacyjnego jest już obsługiwane.

## <a name="scaling-and-management"></a>Skalowanie i zarządzanie
Z usług Azure Cloud Services nie Tworzenie maszyn wirtualnych. Zamiast tego Podaj pliku konfiguracji, który informuje Azure, ile chcesz, takie jak "trzy wystąpienia roli sieci web" i "dwóch procesu roboczego wystąpień roli." Platforma następnie utworzy je. Wybierz nadal [jaki rozmiar](cloud-services-sizes-specs.md) tych kopii maszyn wirtualnych powinny być, ale nie jawnie tworzyć je samodzielnie. Jeśli aplikacja musi obsługiwać większe obciążenie, może poprosić o więcej maszyn wirtualnych, a system Azure utworzy tych wystąpień. Jeśli zmniejsza obciążenie, można zamknąć te wystąpienia i Zatrzymaj płatności dla nich.

Aplikacja usługi w chmurze Azure jest zwykle udostępniane użytkownikom za pośrednictwem dwuetapowego procesu. Projektant pierwszy [przekazuje aplikacji](cloud-services-how-to-create-deploy-portal.md) do obszaru przemieszczania platformy. Gdy Projektant jest gotowy do Utwórz aplikację na żywo, przejdą do portalu Azure można zamienić przemieszczania z produkcji. To [przełączanie między tymczasowych i produkcyjnych](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) może odbywać się bez przestojów, co pozwala uruchomionej aplikacji można uaktualnić do nowej wersji bez zakłócania jego użytkowników.

## <a name="monitoring"></a>Monitorowanie
Usługi w chmurze Azure zapewnia również monitorowanie. Podobnie jak maszyny wirtualne wykryje nie powiodło się serwerze fizycznym i ponowne uruchomienie maszyn wirtualnych, które były uruchomione na tym serwerze, na nowych komputerach. Jednak usługi w chmurze Azure wykrywa również nie powiodło się maszyn wirtualnych i aplikacji, nie tylko na awarie sprzętowe. W przeciwieństwie do maszyn wirtualnych składa się z agentem wewnątrz każdej roli sieci web i proces roboczy, a więc jest w stanie uruchomić nowych maszyn wirtualnych i wystąpień aplikacji w przypadku wystąpienia awarii.

Rodzaj PaaS usługi w chmurze Azure ma zbyt innych skutków. Jest jednym z najważniejszych, że aplikacje oparte na tej technologii mają być zapisywane działała poprawnie, gdy wystąpienie roli sieci web lub procesu roboczego nie powiodło się. Można to osiągnąć, aplikację usługi w chmurze Azure nie zachowują stan, w systemie plików własnych maszyn wirtualnych. W przeciwieństwie do maszyn wirtualnych utworzonych za pomocą maszyn wirtualnych zapisuje do maszyn wirtualnych usługi w chmurze platformy Azure nie są trwałe. Brak nie przypomina dysku danych maszyny wirtualnej. Zamiast tego aplikacji usługi w chmurze Azure należy jawnie zapisać wszystkich stanów do bazy danych SQL Azure, obiekty BLOB, tabel lub innych magazynu zewnętrznego. Tworzenie aplikacji w ten sposób ułatwia ich skali i zwiększyć odporność na awarie, które są zarówno ważne celów usługi w chmurze Azure.

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie aplikacji usługi w chmurze w .NET](cloud-services-dotnet-get-started.md) 
* [Tworzenie aplikacji usługi w chmurze w środowisku Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Tworzenie aplikacji usługi w chmurze w języku PHP](../cloud-services-php-create-web-role.md) 
* [Tworzenie aplikacji usługi w chmurze w języku Python](cloud-services-python-ptvs.md)



