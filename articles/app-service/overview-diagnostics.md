---
title: Omówienie diagnostyki — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sposoby rozwiązywania problemów z aplikacją sieci web za pomocą diagnostyki usługi App Service.
keywords: Usługa App service, usłudze azure app service, Diagnostyka, pomocy technicznej, aplikacji sieci web, rozwiązywania problemów, samodzielne uzyskiwanie pomocy
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: a8b256f43d8e4103404ab4276431ceb06d9ed36a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839439"
---
# <a name="azure-app-service-diagnostics-overview"></a>Omówienie diagnostyki w usłudze Azure App Service 

Po uruchomieniu aplikacji sieci web, należy się przygotować wszelkich problemów, które mogą powstać w wyniku błędów 500 informujący użytkowników, które witryna nie działa. Diagnostyki usługi App Service to środowisko im zwiększenie inteligencji i interaktywnych, aby ułatwić rozwiązywanie problemów z aplikacją sieci web bez konieczności konfiguracji. Jeśli napotkasz problemy z aplikacją sieci web, diagnostyki usługi App Service będzie wspomnieć, na czym polega problem poświęcany na właściwe informacje, aby łatwiej i szybciej rozwiązać problem. 
 
Mimo że to środowisko jest najbardziej przydatne, gdy występują problemy z aplikacją sieci web w ciągu ostatnich 24 godzin, diagnostycznych wykresy będzie dostępne do analizowania przez cały czas. Dodatkowe narzędzia do rozwiązywania problemów oraz linki do pomocną dokumentacją i fora znajdują się w prawej kolumnie.

Diagnostyki usługi App Service działa w przypadku nie tylko Twojej aplikacji na Windows, ale również aplikacji na [kontenerów systemu Linux/](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [środowiska App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro), i [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Otwórz diagnostyki usługi App Service

Aby uzyskać dostęp do diagnostyki usługi App Service, przejdź do swojej aplikacji usługi App Service lub App Service Environment w [witryny Azure portal](https://portal.azure.com). Na lewym pasku nawigacyjnym kliknij **diagnozowanie i rozwiązywanie problemów**. 

Dla usługi Azure Functions, przejdź do aplikacji funkcji, a w górnym menu nawigacyjnym kliknij pozycję **funkcje platformy** i wybierz **diagnozowanie i rozwiązywanie problemów** z **monitorowanie**sekcji. 

![Strona główna](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Kontrolę kondycji

Jeśli nie wiesz, czym jest problem z aplikacją sieci web lub nie wiadomo, gdzie zacząć Rozwiązywanie problemów związanych z kontrolę kondycji jest dobrym miejscem do rozpoczęcia. Kontrolę kondycji będzie analizować aplikacji sieci web, które pozwalają na krótkiego, interaktywnego omówienia, który wskazuje, co jest w dobrej kondycji i na czym polega problem, informujący, gdzie należy zbadać problem. Jego im zwiększenie inteligencji i interaktywny interfejs zapewnia wskazówki proces rozwiązywania problemów.  

![Kontrolę kondycji](./media/app-service-diagnostics/HealthCheckup2.png)

W przypadku wykrycia problemu z kategorią konkretnego problemu w ciągu ostatnich 24 godzinach, można wyświetlić raport diagnostyczny pełnej i diagnostyki usługi App Service może zostać wyświetlony komunikat do wyświetlenia porady dotyczące bardziej rozwiązywania problemów oraz kolejne kroki dotyczące bardziej przewodnikiem.

![Kroki rozwiązywania problemów i dalej](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Skróty kafelka

Jeśli znasz dokładnie jakiego rodzaju informacje, których szukasz dotyczące rozwiązywania problemów, skróty kafelka spowoduje przejście bezpośrednio do pełnej diagnostycznych raportu kategorię problemu, która interesuje Cię. W porównaniu do kontrolę kondycji, skróty kafelka są więcej bezpośrednio, ale mniej krok po kroku sposób uzyskiwania dostępu do swojej metryki diagnostyki. W ramach kafelka skróty, jest to również miejsce **narzędzia diagnostyczne** są bardziej zaawansowane narzędzia, które pomogą Ci zbadać problemy związane z problemów z kodem aplikacji, powolność, parametry połączenia i nie tylko. 

![Skróty kafelka](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Raport diagnostyczny

Czy chcesz więcej informacji, po uruchomieniu [kontrolę kondycji](#health-checkup) lub kliknięcie jednego z [Kafelek skróty](#tile-shortcuts), raport diagnostyczny pełnej pokaże odpowiedniego wykresie metryk z ostatnich 24 godzin. Jeśli aplikacja żadnych przestojów, jest reprezentowana przez pomarańczowy pasek poniżej osi czasu. Możesz wybrać jedną z pomarańczowym paski w celu wybrania przestojów zobacz Uwagi dotyczące tego przestojów i sugerowane kroki rozwiązywania problemów. 

![Raport diagnostyczny](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Badanie problemów z kodem aplikacji

Ponieważ wiele problemów z aplikacjami są powiązane z nim problemów w kodzie aplikacji, diagnostyki usługi App Service integruje się z [usługi Application Insights](https://azure.microsoft.com/services/application-insights/) do wyróżnienia wyjątków i problemów z zależnością skorelować wybranego przestojów. Usługa Application Insights muszą być włączone w oddzielnie. 

Zaznacz, aby wyświetlić wyjątków usługi Application Insights i zależności **niedziałającej aplikacji internetowej** lub **powolne aplikacji sieci Web** Kafelek skróty. 

![Usługa Application insights](./media/app-service-diagnostics/AppInsights6.png)

