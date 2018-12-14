---
title: Napraw 502 — Zła brama, 503 Usługa niedostępna błędów — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z 502 — Zła brama i 503 występowanie błędów niedostępności usługi w aplikacji sieci web hostowaną w usłudze Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 — Zła brama, 503 Usługa niedostępna, błąd 503, błąd 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 71842f9dbc8d0454da1847c956dea3b063208836
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53388956"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Rozwiązywanie problemów z błędami HTTP "502 — Zła brama" i "503 Usługa niedostępna" w aplikacjach sieci web platformy Azure
"502 — Zła brama" i "503 Usługa niedostępna" są typowe błędy w aplikacji sieci web hostowanych w [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Ten artykuł pomaga rozwiązać te błędy.

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i kliknij pozycję **Uzyskaj pomoc techniczną**.

## <a name="symptom"></a>Objaw
Podczas przechodzenia do aplikacji sieci web, funkcja zwraca HTTP "502 — Zła brama" błąd lub HTTP Błąd "503 Usługa niedostępna".

## <a name="cause"></a>Przyczyna
Ten problem jest często spowodowane przez problemy z poziomu aplikacji, takich jak:

* żądania zajmuje dużo czasu
* aplikacji przy użyciu wysokie wykorzystanie pamięci/Procesora
* aplikacja uległa awarii z powodu wyjątku.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Kroki rozwiązywania problemów, aby rozwiązać "502 — Zła brama" i "503 Usługa niedostępna" błędy
Rozwiązywanie problemów z można podzielić na trzy różne zadania, w kolejności sekwencyjnej:

1. [Sprawdź i monitorowanie zachowania aplikacji](#observe)
2. [Zbieranie danych](#collect)
3. [Rozwiązać problem](#mitigate)

[App Service Web Apps](app-service-web-overview.md) daje różne opcje w każdym kroku.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Sprawdź i monitorowanie zachowania aplikacji
#### <a name="track-service-health"></a>Śledzenie kondycji usługi
Microsoft Azure publicizes każdorazowo, gdy występuje spadek przerw i wydajności usługi. Kondycja usługi można śledzić na [witryny Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [śledzenie kondycji usługi](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorowanie aplikacji sieci web
Ta opcja umożliwia dowiedzieć się, jeśli masz problemy aplikacji. W bloku aplikacji sieci web kliknij **żądania i błędy** kafelka. **Metryki** bloku wyświetli wszystkie metryki, które można dodać.

Niektóre metryki, które warto monitorować aplikacji sieci web

* Średni zestaw roboczy pamięci
* Średni czas odpowiedzi
* Czas procesora CPU
* Zestaw roboczy pamięci
* Żądania

![monitorowanie aplikacji sieci web do rozwiązania błędami HTTP 502 — Zła brama i 503 Usługa niedostępna](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Aby uzyskać więcej informacji, zobacz:

* [Monitoruj aplikacje sieci Web w usłudze Azure App Service](web-sites-monitor.md)
* [Otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Zbieranie danych
#### <a name="use-the-diagnostics-tool"></a>Używanie narzędzia do diagnostyki
Usługa App Service udostępnia inteligentne i interaktywne środowisko ułatwiające rozwiązywanie problemów z aplikacją sieci web bez konieczności konfiguracji. Jeśli napotkasz problemy z aplikacją sieci web, narzędzia do diagnostyki wskaże na czym polega problem poświęcany na właściwe informacje, aby łatwiej i szybciej rozwiązać problem.

Aby uzyskać dostęp do diagnostyki usługi App Service, przejdź do swojej aplikacji usługi App Service lub App Service Environment w [witryny Azure portal](https://portal.azure.com). Na lewym pasku nawigacyjnym kliknij **diagnozowanie i rozwiązywanie problemów**.

#### <a name="use-the-kudu-debug-console"></a>Użyj konsoli debugowania aparatu Kudu
Aplikacje sieci Web jest dostarczany za pomocą konsoli debugowania, używanego do debugowania, eksplorowanie, przekazywanie plików, a także JSON punkty końcowe, w celu uzyskania informacji dotyczących środowiska. Jest to nazywane *konsoli Kudu* lub *pulpitu nawigacyjnego funkcji SCM* dla aplikacji sieci web.

Dostęp do tego pulpitu nawigacyjnego, przechodząc do łącza **https://&lt;Twoja nazwa aplikacji >.scm.azurewebsites.net/**.

Niektóre rzeczy, które zapewnia aparat Kudu, to:

* ustawienia środowiska dla aplikacji
* strumień dziennika
* diagnostyczne zrzutu
* Debuguj konsoli, w którym można uruchomić polecenia cmdlet programu Powershell i podstawowe polecenia systemu DOS.

Inną przydatną cechą Kudu jest, że w przypadku, gdy aplikacja zgłasza wyjątki pierwszej szansy, można użyć narzędzia Kudu i zrzuty narzędzie SysInternals Procdump, aby utworzyć pamięci. Te zrzuty pamięci są migawki procesu i często może pomóc rozwiązać bardziej skomplikowanych problemów z aplikacją sieci web.

Aby uzyskać więcej informacji na temat funkcji dostępnych w Kudu, zobacz [narzędzia online usługi Azure Websites, należy wiedzieć o](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Rozwiązać problem
#### <a name="scale-the-web-app"></a>Skalowanie aplikacji sieci web
W usłudze Azure App Service Aby zwiększyć wydajność i przepływność, można dostosować skali, w którym aplikacja jest uruchamiana. Skalowanie aplikacji sieci web obejmuje dwie akcje powiązane: zmiana planu usługi App Service na wyższą warstwę cenową i konfigurowanie niektórych ustawień, po przełączeniu do wyższej warstwy cenowej.

Aby uzyskać więcej informacji na temat skalowania, zobacz [skalowanie aplikacji sieci web w usłudze Azure App Service](web-sites-scale.md).

Ponadto można uruchomić aplikację na więcej niż jedno wystąpienie. To nie tylko zapewnia więcej możliwości przetwarzania, ale również zapewnia pewien stopień odporności na uszkodzenia. Jeśli proces ulegnie awarii w jednym wystąpieniu, inne wystąpienie będzie w dalszym ciągu obsługiwać żądania.

Możesz ustawić skalowanie ręczne lub automatyczne.

#### <a name="use-autoheal"></a>Używają funkcji AutoHeal
Funkcji AutoHeal odtwarzania procesu roboczego dla aplikacji na podstawie ustawień wybranych (np. zmiany w konfiguracji, żądań, limity opartego na pamięci lub czas potrzebny do wykonania na żądanie). W większości przypadków, odtwarzania procesu jest najszybszym sposobem na odzyskanie problem. Chociaż można zawsze uruchomić ponownie aplikację internetową z bezpośrednio z poziomu witryny Azure Portal, funkcji AutoHeal będzie zrobiła to automatycznie dla Ciebie. To wszystko, co należy zrobić, Dodaj niektórych wyzwalaczy w głównym pliku web.config dla aplikacji sieci web. Należy pamiętać, że tych ustawień będzie działać w taki sam sposób, nawet jeśli aplikacja nie jest jeden .net.

Aby uzyskać więcej informacji, zobacz [Samonaprawianie witryn sieci Web Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Uruchom ponownie aplikację internetową
Często jest najprostszym sposobem, aby odzyskać sprawność po jednorazowych problemach. Na [witryny Azure Portal](https://portal.azure.com/), w bloku aplikacji sieci web mają opcji, aby zatrzymać lub uruchomić ponownie aplikację.

 ![Uruchom ponownie aplikację, aby rozwiązać błędy HTTP 502 — Zła brama i 503 Usługa niedostępna](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Można również zarządzać aplikacją sieci web przy użyciu programu Azure Powershell. Aby uzyskać więcej informacji, zobacz temat [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Azure PowerShell z usługą Azure Resource Manager).

