---
title: Korzystanie z okienka ustawień usługi Azure Application Insights Profiler | Dokumenty firmy Microsoft
description: Zobacz stan profilera i rozpocznij sesje profilowania
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671634"
---
# <a name="configure-application-insights-profiler"></a>Konfigurowanie programu Profiler usługi Application Insights

## <a name="updated-profiler-agent"></a>Zaktualizowany agent profilera
Funkcje wyzwalacza działają tylko z wersją 2.6 lub nowszą agenta profilera. Jeśli korzystasz z usługi Azure App Service, twój agent zostanie automatycznie zaktualizowany. Możesz zobaczyć, jaką wersję agenta używasz, jeśli przejdziesz do adresu URL Kudu dla swojej witryny i https://yourwebsite.scm.azurewebsites.net/diagnosticservicesdodasz \DiagnosticServices na jej końcu, w stylu: . Usługa Webjob profilera aplikacji powinna mieć wersję 2.6 lub nowszą. Możesz wymusić uaktualnienie, uruchamiając ponownie aplikację internetową. 

Jeśli używasz profilera na maszynie Wirtualnej lub usłudze w chmurze, musisz mieć zainstalowane rozszerzenie diagnostyki systemu Windows Azure (WAD) w wersji 16.0.4 lub nowsze. Wersję wad można sprawdzić, logując się do maszyny Wirtualnej i wyczekując ten katalog: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. Nazwa katalogu jest zainstalowaną wersją wad. Agent maszyny wirtualnej platformy Azure zaktualizuje wad automatycznie, gdy nowe wersje są dostępne.

## <a name="profiler-settings-page"></a>Strona ustawień profilera

Aby otworzyć okienko ustawień usługi Azure Application Insights Profiler, przejdź do okienka Wydajność usługi Application Insights, a następnie wybierz przycisk **Konfiguruj profiler.**

![Łącze, aby otworzyć stronę ustawień programu Profiler][configure-profiler-entry]

Spowoduje to otwarcie strony wyglądaw ten sposób:

![Strona ustawień profilera][configure-profiler-page]

Strona **Konfigurowanie profilera aplikacji aplikacji** ma następujące funkcje:

| | |
|-|-|
Profil teraz | Rozpoczyna profilowanie sesji dla wszystkich aplikacji, które są połączone z tym wystąpieniem usługi Application Insights.
Wyzwalacze | Umożliwia skonfigurowanie wyzwalaczy, które powodują uruchomienie profilera. 
Ostatnie sesje profilowania | Wyświetla informacje o poprzednich sesjach profilowania.

## <a name="profile-now"></a>Profil teraz
Ta opcja umożliwia rozpoczęcie sesji profilowania na żądanie. Po kliknięciu tego łącza, wszystkie agentów profilera, które wysyłają dane do tego wystąpienia usługi Application Insights rozpocznie przechwytywanie profilu. Po 5 do 10 minutach sesja profilu pojawi się na poniższej liście.

Dla użytkownika ręcznie wyzwolić sesji profilera, wymagają one co najmniej "zapis" dostępu do ich roli dla składnika usługi Application Insights. W większości przypadków otrzymasz ten dostęp automatycznie i nie jest wymagana żadna dodatkowa praca. Jeśli masz problemy, rola zakresu subskrypcji do dodania będzie rola "Współautor składnika usługi Application Insights". [Zobacz więcej informacji o kontroli dostępu do ról za pomocą usługi Azure Monitoring](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control).

## <a name="trigger-settings"></a>Ustawienia wyzwalacza
![Okno wysuw ustawień wyzwalania][trigger-settings-flyout]

Kliknięcie przycisku Wyzwalacze na pasku menu powoduje otwarcie pola ustawień wyzwalacza. Wyzwalacz można skonfigurować tak, aby rozpoczynał profilowanie, gdy procent użycia procesora CPU lub pamięci osiągnie ustawiony poziom.

| | |
|-|-|
Przycisk włączania / wyłączania | On: profiler może być uruchomiony przez ten wyzwalacz; Wyłączone: profiler nie zostanie uruchomiony przez ten wyzwalacz.
Próg pamięci | Gdy ten procent pamięci jest w użyciu, profiler zostanie uruchomiony.
Czas trwania | Ustawia czas, przez który profiler będzie uruchamiany po wyzwoleniu.
Czas odnowienia | Ustawia czas, przez jaki profiler będzie czekać przed ponownym sprawdzeniem użycia pamięci lub procesora CPU po jego wyzwoleniu.

## <a name="recent-profiling-sessions"></a>Ostatnie sesje profilowania
W tej sekcji strony znajdują się informacje o ostatnich sesjach profilowania. Sesja profilowania reprezentuje okres, w którym agent profilera robił profil na jednej z maszyn hostujących aplikację. Profile z sesji można otworzyć, klikając jeden z wierszy. Dla każdej sesji pokazujemy:

| | |
|-|-|
Wywołane przez | Jak sesja została uruchomiona przez wyzwalacz, Profil teraz lub próbkowanie domyślne. 
Nazwa aplikacji | Nazwa aplikacji, która została sprofilowana.
Wystąpienie maszyny | Nazwa maszyny, na które działał agent profilera.
Znacznik czasu | Czas, w którym profil został przechwycony.
Tracee ( Tracee ) | Liczba śladów dołączonych do poszczególnych żądań.
Procent użycia procesora CPU | Procent procesora CPU, który był używany podczas pracy profilera.
% pamięci | Procent pamięci, który był używany podczas pracy profilera.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a>Używanie testów wydajności sieci Web do generowania ruchu do aplikacji

Profiler można wyzwolić ręcznie za pomocą jednego kliknięcia. Załóżmy, że uruchamiasz test wydajności sieci Web. Będziesz potrzebować śladów, które pomogą Ci zrozumieć, jak aplikacja internetowa działa pod obciążeniem. Posiadanie kontroli nad tym, kiedy ślady są przechwytywane ma kluczowe znaczenie, ponieważ wiesz, kiedy test obciążenia będzie uruchomiony. Ale losowy interwał próbkowania może go przegapić.

W następnych sekcjach przedstawiono, jak działa ten scenariusz:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Krok 1: Generowanie ruchu do aplikacji sieci web przez rozpoczęcie testu wydajności sieci Web

Jeśli aplikacja internetowa ma już ruch przychodzący lub chcesz ręcznie wygenerować ruch, pomiń tę sekcję i przejdź do kroku 2.

1. W portalu usługi Application Insights wybierz pozycję **Konfiguruj testowanie** > **wydajności**. 

1. Aby rozpocząć nowy test wydajności, wybierz przycisk **Nowy.**

   ![tworzenie nowego testu wydajności][create-performance-test]

1. W okienku **testu nowa wydajność** skonfiguruj docelowy adres URL testu. Zaakceptuj wszystkie ustawienia domyślne, a następnie wybierz pozycję **Uruchom test,** aby rozpocząć uruchamianie testu obciążenia.

    ![Konfigurowanie testu obciążenia][configure-performance-test]

    Nowy test jest umieszczany w kolejce jako pierwszy, a następnie stan *w toku*.

    ![Test obciążenia jest przesyłany i umieszczany w kolejce][load-test-queued]

    ![Trwa test obciążenia][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Krok 2: Rozpoczęcie sesji profilera na żądanie

1. Gdy test obciążenia jest uruchomiony, uruchom profiler, aby przechwycić ślady w aplikacji sieci web podczas odbierania obciążenia.

1. Przejdź do okienka **Konfigurowanie profilera.**


### <a name="step-3-view-traces"></a>Krok 3: Wyświetlanie śladów

Po zakończeniu pracy profilera postępuj zgodnie z instrukcjami w powiadomieniu, aby przejść do okienka wydajności i wyświetlić ślady.

## <a name="next-steps"></a>Następne kroki
[Włączanie profilera i wyświetlanie śladów](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
