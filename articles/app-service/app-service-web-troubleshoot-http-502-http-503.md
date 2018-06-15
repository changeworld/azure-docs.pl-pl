---
title: Rozwiązać Zła brama 502, 503 obsługi błędów niedostępności | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z 502 Niewłaściwa brama i 503 błędów niedostępności usługi w aplikacji sieci web hostowanych w usłudze Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: Zła brama 502, 503 usługi niedostępne, błąd 503, błąd 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 397a6aaf7dc27adfa0fc0e722b8a2be5cc1d75f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836518"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Rozwiązywanie błędów HTTP "502 Niewłaściwa brama" i "503 Usługa niedostępna" w aplikacjach sieci web platformy Azure
"brama zły 502" i "503 Usługa niedostępna" są typowe błędy w aplikacji sieci web hostowanych w [usłudze Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Ten artykuł pomoże w rozwiązaniu tych błędów.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [MSDN Azure i fora przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz polecenie **Get Support**.

## <a name="symptom"></a>Objaw
Po przejściu do aplikacji sieci web zwraca HTTP HTTP lub błąd "502 Niewłaściwa brama" błąd "503 Usługa niedostępna".

## <a name="cause"></a>Przyczyna
Ten problem jest często spowodowane przez problemy z poziomu aplikacji, takich jak:

* żądania zbyt długo
* aplikacji przy użyciu pamięci wysokiej/procesora CPU
* Aplikacja awarii z powodu wyjątku.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Kroki rozwiązywania problemów do rozwiązania "502 Niewłaściwa brama" i "503 Usługa niedostępna" błędów
Rozwiązywanie problemów, można podzielić na trzy różne zadania, w kolejności sekwencyjnej:

1. [Sprawdź i monitorowanie zachowania aplikacji](#observe)
2. [Zbieranie danych](#collect)
3. [Ograniczenia problem](#mitigate)

[Aplikacje sieci Web usługi aplikacji](/services/app-service/web/) zapewnia różne opcje w każdym kroku.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Sprawdź i monitorowanie zachowania aplikacji
#### <a name="track-service-health"></a>Śledź usługę kondycji
Microsoft Azure publicizes każdym razem, gdy istnieje degradacji przerw i wydajności usługi. Kondycja usługi można śledzić na [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [śledzić kondycja usługi](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorowanie aplikacji sieci web
Ta opcja pozwala dowiedzieć się, jeśli masz problemy aplikacji. W bloku aplikacja sieci web, kliknij przycisk **żądań i błędów** kafelka. **Metryka** bloku wyświetli wszystkie metryki można dodać.

Niektóre metryki, które mogą zostać do monitorowania aplikacji sieci web

* Pamięć średni zestaw roboczy
* Średni czas odpowiedzi
* Czas procesora CPU
* Zestaw roboczy pamięci
* Żądania

![monitorowanie aplikacji sieci web do rozwiązania błędów HTTP 502 Niewłaściwa brama i 503 Usługa jest niedostępna](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Aby uzyskać więcej informacji, zobacz:

* [Monitorowanie aplikacji sieci Web w usłudze aplikacji Azure](web-sites-monitor.md)
* [Otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Zbieranie danych
#### <a name="use-the-azure-app-service-support-portal"></a>Użyj portalu pomocy technicznej usługi aplikacji Azure
Aplikacje sieci Web umożliwia rozwiązanie problemów dotyczących aplikacji sieci web, analizując HTTP dzienniki, dzienniki zdarzeń, zrzuty procesu i inne. Można uzyskać dostępu do tych informacji za pomocą portalu pomocy technicznej w **http://&lt;Twojego Nazwa aplikacji >.scm.azurewebsites.net/Support**

Portal Azure App Service obsługuje zawiera trzy oddzielne karty do obsługi trzy kroki typowego scenariusza rozwiązywania problemów:

1. Sprawdź bieżące zachowanie
2. Analizowanie zbierania informacji diagnostycznych i uruchamiając analizatorów wbudowane
3. Ograniczenia

Problem jest wykonywane od razu, kliknij przycisk **Analizuj** > **diagnostyki** > **diagnozowanie teraz** utworzenie sesji diagnostycznej, do którego będzie zbierać dzienniki HTTP, dzienniki Podglądu zdarzeń, pamięci zrzuty, dzienniki błędów PHP i PHP przetwarzania raportu.

Gdy dane są zbierane, zostanie również przeprowadzanie analizy danych i dostarczyć raport HTML.

W przypadku, gdy chcesz pobrać dane, domyślnie, powinny być przechowywane w folderze D:\home\data\DaaS.

Aby uzyskać więcej informacji w portalu pomocy technicznej usługi aplikacji Azure, zobacz [nowe aktualizacje do rozszerzenia witrynę pomocy technicznej dla witryn sieci Web Azure](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Użyj konsoli debugowania aparatu Kudu
Aplikacje sieci Web jest dostarczany z konsoli debugowania, która służy do debugowania, eksploracji, przekazywania plików, a także pobieranie informacji na temat środowiska punktów końcowych JSON. Ta metoda jest wywoływana *konsoli Kudu* lub *pulpitu nawigacyjnego SCM* dla aplikacji sieci web.

Dostęp do tego pulpitu nawigacyjnego, przechodząc do łącza **https://&lt;Twojego Nazwa aplikacji >.scm.azurewebsites.net/**.

Niektóre czynności, które program Kudu udostępnia są:

* ustawienia środowiska aplikacji
* strumień dziennika
* diagnostycznych zrzutu
* Konsola, w którym można uruchomić poleceń cmdlet programu Powershell i podstawowe polecenia systemu DOS debugowania.

Inny przydatną cechą Kudu jest, że w przypadku, gdy aplikacja jest zgłaszanie wyjątków pierwszej szansy, można użyć Kudu i zrzuty narzędzie SysInternals Procdump utworzyć pamięci. Te zrzuty pamięci są migawki procesu i często ułatwiają rozwiązywanie problemów z bardziej skomplikowanych problemów z aplikacją sieci web.

Aby uzyskać więcej informacji o funkcjach dostępnych w Kudu, zobacz [narzędzia online witryny sieci Web Azure należy wiedzieć o](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Ograniczenia problem
#### <a name="scale-the-web-app"></a>Skalowanie aplikacji sieci web
W usłudze Azure App Service Aby zwiększyć wydajność i przepływność, można dostosować skali, w którym są uruchomione aplikacji. Skalowanie w górę aplikacji sieci web obejmuje dwie akcje powiązane: zmiana planu usługi aplikacji na wyższej warstwy cenowej i konfigurowanie niektórych ustawień po przełączeniu do wyższej warstwy cenowej.

Aby uzyskać więcej informacji na temat skalowania, zobacz [skalowanie aplikacji sieci web w usłudze Azure App Service](web-sites-scale.md).

Ponadto można wybrać do uruchamiania aplikacji na więcej niż jedno wystąpienie. To nie tylko zapewnia więcej możliwości przetwarzania, ale umożliwia także niektóre ilość odporność na uszkodzenia. Jeśli proces przestanie działać w jednym wystąpieniu, jak inne wystąpienie będzie nadal obsługiwać żądania.

Można skonfigurować jako ręczne lub automatyczne skalowanie.

#### <a name="use-autoheal"></a>Funkcja AutoHeal użycia
Funkcja AutoHeal odtwarzania procesu roboczego dla aplikacji na podstawie ustawień wybranego (na przykład zmiany konfiguracji żądania, limity pamięci lub czas potrzebny na wykonanie żądania). W większości przypadków, odtworzenia procesu jest najszybszym sposobem odzyskanie problem. Chociaż zawsze można ponownie uruchomić aplikacji sieci web z bezpośrednio z poziomu portalu Azure, funkcja AutoHeal będzie robi to automatycznie. To wszystko, co należy zrobić, Dodaj niektóre wyzwalacze w głównym pliku web.config dla aplikacji sieci web. Należy pamiętać, że te ustawienia będzie działać w taki sam sposób, nawet jeśli aplikacja nie jest jedną .net.

Aby uzyskać więcej informacji, zobacz [automatyczne naprawianie Azure Web Sites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Uruchom ponownie aplikację sieci web
Często jest najprostszym sposobem, aby rozwiązać jednorazowo występujące problemy. Na [Azure Portal](https://portal.azure.com/), w bloku aplikacja sieci web ma opcji, aby zatrzymać lub uruchomić ponownie aplikację.

 ![Uruchom ponownie aplikację, aby naprawić błędy HTTP 502 Niewłaściwa brama i 503 Usługa jest niedostępna](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Można również zarządzać aplikacji sieci web przy użyciu programu Azure Powershell. Aby uzyskać więcej informacji, zobacz temat [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Azure PowerShell z usługą Azure Resource Manager).

