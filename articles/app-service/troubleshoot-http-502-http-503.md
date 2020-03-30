---
title: Naprawianie błędów HTTP 502 i HTTP 503
description: Rozwiązywanie problemów z błędami 502 złych bram i 503 niedostępnych usług w aplikacji hostowanych w usłudze Azure App Service.
tags: top-support-issue
keywords: 502 zła brama, 503 usługa niedostępna, błąd 503, błąd 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 9345b6fb28aa282e85f1167f6f2531e5f990e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688324"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Rozwiązywanie problemów z błędami HTTP "502 bad gateway" i "503 usługa niedostępna" w usłudze Azure App Service
"502 bad gateway" i "503 usługa niedostępna" to typowe błędy w aplikacji hostowane w [usłudze Azure App Service.](https://go.microsoft.com/fwlink/?LinkId=529714) Ten artykuł ułatwia rozwiązywanie tych błędów.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [platformie MSDN Azure i forach przepełnienia stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i kliknij pozycję Uzyskaj pomoc **techniczną**.

## <a name="symptom"></a>Objaw
Podczas przeglądania aplikacji zwraca błąd HTTP "502 Bad Gateway" lub błąd HTTP "Usługa niedostępna" HTTP.

## <a name="cause"></a>Przyczyna
Ten problem jest często spowodowany przez problemy z poziomem aplikacji, takie jak:

* wnioski, które zajmują dużo czasu
* aplikacja wykorzystująca wysoką pamięć/procesor
* awaria aplikacji z powodu wyjątku.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Rozwiązywanie kroków w celu rozwiązania błędów "502 bad gateway" i "503 usługa niedostępna"
Rozwiązywanie problemów można podzielić na trzy różne zadania, w kolejności:

1. [Obserwowanie i monitorowanie zachowania aplikacji](#observe)
2. [Zbieranie danych](#collect)
3. [Złagodzenie problemu](#mitigate)

[Usługa App Service](overview.md) oferuje różne opcje na każdym kroku.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Obserwować i monitorować zachowanie aplikacji
#### <a name="track-service-health"></a>Śledź kondycję usługi
Platforma Microsoft Azure nagłaśnia za każdym razem, gdy występuje przerwa w świadczeniu usług lub spadek wydajności. Kondycję usługi można śledzić w witrynie [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [Śledzenie kondycji usługi](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Monitorowanie aplikacji
Ta opcja umożliwia dowiedzieć się, czy aplikacja ma jakiekolwiek problemy. W bloku aplikacji kliknij kafelek **Żądania i błędy.** **Metryka** bloku pokaże wszystkie metryki, które można dodać.

Niektóre z danych, które możesz monitorować dla aplikacji, to

* Zestaw pracy średniej pamięci
* Średni czas reakcji
* Czas procesora
* Zestaw roboczy pamięci
* Żądania

![monitor aplikacji w kierunku rozwiązywania błędów HTTP 502 złej bramy i 503 usługa niedostępna](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Aby uzyskać więcej informacji, zobacz:

* [Monitorowanie aplikacji w usłudze Azure App Service](web-sites-monitor.md)
* [Otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Zbieranie danych
#### <a name="use-the-diagnostics-tool"></a>Korzystanie z narzędzia diagnostycznego
Usługa App Service zapewnia inteligentne i interaktywne środowisko, które ułatwia rozwiązywanie problemów z aplikacją bez konieczności konfigurowania. Gdy napotkasz problemy z aplikacją, narzędzie diagnostyczne wskaże, co jest nie tak, aby poprowadzić Cię do właściwych informacji, aby łatwiej i szybciej rozwiązać problem.

Aby uzyskać dostęp do diagnostyki usługi App Service, przejdź do aplikacji usługi App Service lub środowiska usługi app service w [witrynie Azure portal](https://portal.azure.com). W lewej nawigacji kliknij przycisk **Diagnozuj i rozwiązuj problemy**.

#### <a name="use-the-kudu-debug-console"></a>Korzystanie z konsoli debugowania Kudu
Usługa App Service jest wyposażona w konsolę debugowania, której można używać do debugowania, eksplorowania, przekazywania plików, a także punktów końcowych JSON w celu uzyskania informacji o środowisku. Nazywa się to *konsolą Kudu* lub *pulpitem nawigacyjnym SCM* dla twojej aplikacji.

Dostęp do tego pulpitu nawigacyjnego można uzyskać, przechodząc do łącza **https://&lt;nazwa aplikacji>.scm.azurewebsites.net/**.

Niektóre z rzeczy, które zapewnia Kudu to:

* ustawienia środowiska dla aplikacji
* strumień dziennika
* zrzut diagnostyczny
* konsola debugowania, w której można uruchamiać polecenia cmdlet programu Powershell i podstawowe polecenia DOS.

Inną przydatną cechą Kudu jest to, że w przypadku, gdy aplikacja zgłasza wyjątki pierwszej szansy, można użyć Kudu i narzędzia SysInternals Procdump do tworzenia zrzutów pamięci. Te zrzuty pamięci są migawkami procesu i często mogą pomóc w rozwiązywaniu bardziej skomplikowanych problemów z aplikacją.

Aby uzyskać więcej informacji na temat funkcji dostępnych w kudu, zobacz [Narzędzia online witryn sieci Web platformy Azure, o których powinieneś wiedzieć.](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Złagodzenie problemu
#### <a name="scale-the-app"></a>Skalowanie aplikacji
W usłudze Azure App Service, aby zwiększyć wydajność i przepływność, można dostosować skalę, w której są uruchomione aplikacji. Skalowanie aplikacji obejmuje dwie powiązane akcje: zmiana planu usługi App Service na wyższą warstwę cenową i skonfigurowanie niektórych ustawień po przełączeniu do wyższej warstwy cenowej.

Aby uzyskać więcej informacji na temat skalowania, zobacz [Skalowanie aplikacji w usłudze Azure App Service](manage-scale-up.md).

Ponadto można uruchomić aplikację na więcej niż jednym wystąpieniu . Zapewnia to nie tylko większe możliwości przetwarzania, ale także zapewnia pewną ilość odporności na uszkodzenia. Jeśli proces ustępuje w dół na jedno wystąpienie, inne wystąpienie będzie nadal obsługiwać żądania.

Skalowanie można ustawić tak, aby było ręczne lub automatyczne.

#### <a name="use-autoheal"></a>Korzystanie z funkcji AutoHeal
AutoHeal odtwarza proces roboczy aplikacji na podstawie ustawień, które wybierzesz (takich jak zmiany konfiguracji, żądania, limity oparte na pamięci lub czas potrzebny do wykonania żądania). W większości przypadków recykling jest najszybszym sposobem na rozwiązanie problemu. Chociaż zawsze można ponownie uruchomić aplikację bezpośrednio w witrynie Azure Portal, AutoHeal zrobi to automatycznie za Ciebie. Wszystko, co musisz zrobić, to dodać kilka wyzwalaczy w web.config root dla aplikacji. Należy zauważyć, że te ustawienia będą działać w ten sam sposób, nawet jeśli aplikacja nie jest .NET.

Aby uzyskać więcej informacji, zobacz [Automatyczne koryguje witryny sieci Web platformy Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Uruchom ponownie aplikację
Jest to często najprostszy sposób, aby odzyskać od problemów jednorazowych. W [witrynie Azure Portal](https://portal.azure.com/)— w bloku aplikacji dostępne są opcje zatrzymania lub ponownego uruchomienia aplikacji.

 ![uruchom ponownie aplikację, aby rozwiązać błędy HTTP 502 złej bramy i 503 usługa niedostępna](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Możesz również zarządzać aplikacją za pomocą programu Azure Powershell. Aby uzyskać więcej informacji, zobacz temat [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Azure PowerShell z usługą Azure Resource Manager).

