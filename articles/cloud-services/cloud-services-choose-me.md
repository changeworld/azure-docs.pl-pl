---
title: Co to są usługi w chmurze azure | Dokumenty firmy Microsoft
description: Dowiedz się, czym są usługi w chmurze Azure.
services: cloud-services
author: tgore03
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: c531e02656c9f6342670024b2220386e789a2d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386854"
---
# <a name="overview-of-azure-cloud-services"></a>Omówienie usług w chmurze platformy Azure
Usługi w chmurze platformy Azure jest przykładem [platformy jako usługi](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Podobnie jak [usługa Azure App Service,](../app-service/overview.md)ta technologia jest przeznaczona do obsługi aplikacji, które są skalowalne, niezawodne i niedrogie w obsłudze. W ten sam sposób, że usługa App Service jest hostowana na maszynach wirtualnych(VM), podobnie jak usługi Azure Cloud Services. Jednak masz większą kontrolę nad maszynami wirtualnymi. Możesz zainstalować własne oprogramowanie na maszynach wirtualnych korzystających z usług Azure Cloud Services i uzyskać do nich zdalny dostęp.

![Diagram usług w chmurze platformy Azure](./media/cloud-services-choose-me/diagram.png)

Większa kontrola oznacza również mniejszą łatwość użycia. Jeśli nie potrzebujesz dodatkowych opcji sterowania, zazwyczaj jest szybsze i łatwiejsze do uruchomienia aplikacji sieci web w aplikacji aplikacji sieci Web w usłudze App Service w porównaniu do usług w chmurze Azure.

Istnieją dwa typy ról usług w chmurze platformy Azure. Jedyną różnicą między tymi dwoma jest sposób, w jaki twoja rola jest hostowana na maszynach wirtualnych:

* **Rola sieci Web:** automatycznie wdraża i hostuje aplikację za pośrednictwem usług IIS.

* **Rola procesu roboczego:** nie używa usług IIS i uruchamia aplikację autonomiczną.

Na przykład prosta aplikacja może używać tylko jednej roli sieci web, obsługującej witrynę sieci Web. Bardziej złożona aplikacja może używać roli sieci web do obsługi żądań przychodzących od użytkowników, a następnie przekazać te żądania do roli procesu roboczego do przetwarzania. (W tej komunikacji może być używana [usługa Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) lub usługa Azure Queue [storage).)](../storage/common/storage-introduction.md)

Jak sugeruje poprzedni rysunek, wszystkie maszyny wirtualne w jednej aplikacji są uruchamiane w tej samej usłudze w chmurze. Użytkownicy uzyskują dostęp do aplikacji za pośrednictwem jednego publicznego adresu IP, a żądania są automatycznie równoważone w przypadku maszyn wirtualnych aplikacji. Platforma [skaluje i wdraża](cloud-services-how-to-scale-portal.md) maszyny wirtualne w aplikacji usługi Azure Cloud Services w sposób, który pozwala uniknąć pojedynczego punktu awarii sprzętu.

Mimo że aplikacje są uruchamiane na maszynach wirtualnych, ważne jest, aby zrozumieć, że usługi Azure Cloud Services zapewnia PaaS, a nie infrastruktury jako usługi (IaaS). Oto jeden ze sposobów, aby o tym pomyśleć. Za pomocą usługi IaaS, takiej jak maszyny wirtualne platformy Azure, należy najpierw utworzyć i skonfigurować środowisko, w które działa aplikacja. Następnie wdrożyć aplikację w tym środowisku. Użytkownik jest odpowiedzialny za zarządzanie dużą częścią tego świata, wykonując takie czynności, jak wdrażanie nowych poprawionych wersji systemu operacyjnego w każdej maszynie wirtualnej. W PaaS, natomiast, to tak, jakby środowisko już istnieje. Wszystko, co musisz zrobić, to wdrożyć aplikację. Zarządzanie platformą, na której działa, w tym wdrażanie nowych wersji systemu operacyjnego, jest obsługiwane za Ciebie.

## <a name="scaling-and-management"></a>Skalowanie i zarządzanie
Usługi w chmurze azure nie tworzy maszyn wirtualnych. Zamiast tego należy podać plik konfiguracji, który informuje platformy Azure, ile z każdego chcesz, takich jak "trzy wystąpienia roli sieci web" i "dwa wystąpienia roli procesu roboczego." Platforma następnie tworzy je dla Ciebie. Nadal wybierasz [rozmiar](cloud-services-sizes-specs.md) tych kopii maszyn wirtualnych, ale nie jawnie utworzyć je samodzielnie. Jeśli aplikacja musi obsługiwać większe obciążenie, można poprosić o więcej maszyn wirtualnych i platformy Azure tworzy te wystąpienia. Jeśli obciążenie zmniejsza się, można zamknąć te wystąpienia i przestać płacić za nie.

Aplikacja usługi Azure Cloud Services jest zazwyczaj udostępniana użytkownikom za pośrednictwem dwuetapowego procesu. Deweloper najpierw [przekazuje aplikację](cloud-services-how-to-create-deploy-portal.md) do obszaru tymczasowego platformy. Gdy deweloper jest gotowy do aplikacji na żywo, używają witryny Azure Portal do wymiany przemieszczania z produkcją. Ten [przełącznik między przemieszczania i produkcji](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) można wykonać bez przestojów, co pozwala uruchomionej aplikacji uaktualnić do nowej wersji bez przeszkadzania jej użytkowników.

## <a name="monitoring"></a>Monitorowanie
Usługi w chmurze azure zapewnia również monitorowanie. Podobnie jak maszyny wirtualne, wykrywa serwer fizyczny nie powiodło się i uruchamia ponownie maszyny wirtualne, które były uruchomione na tym serwerze na nowym komputerze. Ale usługa Azure Cloud Services wykrywa również nieudane maszyny wirtualne i aplikacje, a nie tylko awarie sprzętu. W przeciwieństwie do maszyn wirtualnych ma agenta wewnątrz każdej roli sieci web i procesu roboczego, a więc jest w stanie uruchomić nowe maszyny wirtualne i wystąpienia aplikacji, gdy wystąpią błędy.

Charakter PaaS usług w chmurze azure ma inne implikacje, zbyt. Jednym z najważniejszych jest to, że aplikacje oparte na tej technologii powinny być zapisywane do poprawnego działania, gdy wystąpienie roli sieci web lub procesu roboczego nie powiedzie się. Aby to osiągnąć, aplikacja usługi Azure Cloud Services nie powinna zachowywać stanu w systemie plików własnych maszyn wirtualnych. W przeciwieństwie do maszyn wirtualnych utworzonych za pomocą maszyn wirtualnych zapisy wprowadzone na maszynach wirtualnych usługi Azure Cloud Services nie są trwałe. Nie ma nic takiego jak dysk danych maszyn wirtualnych. Zamiast tego aplikacja usługi Azure Cloud Services powinna jawnie zapisywać wszystkie stany w bazie danych SQL Azure, obiektach BLOB, tabelach lub innym magazynie zewnętrznym. Tworzenie aplikacji w ten sposób ułatwia skalowanie i jest bardziej odporne na awarie, które są zarówno ważnymi celami usług w chmurze azure.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie aplikacji usługi w chmurze w sieci .NET](cloud-services-dotnet-get-started.md) 
* [Tworzenie aplikacji usługi w chmurze w pliku Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Tworzenie aplikacji usługi w chmurze w PHP](../cloud-services-php-create-web-role.md) 
* [Tworzenie aplikacji usługi w chmurze w języku Python](cloud-services-python-ptvs.md)






