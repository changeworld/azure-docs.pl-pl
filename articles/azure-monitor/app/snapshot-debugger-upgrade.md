---
title: Uaktualnianie debugera migawek usługi Azure Application Insights
description: Jak uaktualnić debuger migawek dla aplikacji platformy .NET do najnowszej wersji w usłudze Azure App Services lub za pośrednictwem pakietów Nuget
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671396"
---
# <a name="upgrading-the-snapshot-debugger"></a>Uaktualnianie debugera migawki

Aby zapewnić najlepsze możliwe zabezpieczenia danych, firma Microsoft odchodzi od protokołu TLS 1.0 i TLS 1.1, które okazały się podatne na określone osoby atakujące. Jeśli używasz starszej wersji rozszerzenia witryny, będzie to wymagało uaktualnienia, aby kontynuować pracę. W tym dokumencie opisano kroki potrzebne do uaktualnienia debugera migawki do najnowszej wersji. Istnieją dwie podstawowe ścieżki uaktualniania w zależności od tego, czy włączono debuger migawki przy użyciu rozszerzenia lokacji lub jeśli do aplikacji dodano pakiet SDK/Nuget. Obie ścieżki uaktualnienia zostały omówione poniżej. 

## <a name="upgrading-the-site-extension"></a>Uaktualnianie rozszerzenia witryny

> [!IMPORTANT]
> Starsze wersje usługi Application Insights używały rozszerzenia witryny prywatnej o nazwie _Rozszerzenie usługi Application Insights dla usługi Azure App Service._ Bieżące środowisko usługi Application Insights jest włączone przez ustawienie ustawień aplikacji, aby zapalić wstępnie zainstalowane rozszerzenie lokacji.
> Aby uniknąć konfliktów, które mogą spowodować, że witryna przestanie działać, należy najpierw usunąć rozszerzenie witryny prywatnej. Patrz krok 4 poniżej.

Jeśli debuger migawki został włączony przy użyciu rozszerzenia lokacji, można uaktualnić za pomocą następującej procedury:

1. Zaloguj się do Portalu Azure.
2. Przejdź do zasobu z włączoną usługą Application Insights i debugerem migawki. Na przykład w przypadku aplikacji sieci Web przejdź do zasobu usługi App Service:

   ![Zrzut ekranu przedstawiający pojedynczy zasób usługi app service o nazwie DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Po przejściu do zasobu kliknij na rozszerzenia bloku i czekać na listę rozszerzeń do wypełniania:

   ![Zrzut ekranu przedstawiający rozszerzenia usługi App Service z zainstalowanym rozszerzeniem usługi Application Insights dla usługi Azure App Service](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Jeśli zainstalowana jest dowolna wersja _rozszerzenia usługi Application Insights dla usługi Azure App Service,_ wybierz ją i kliknij przycisk Usuń. Potwierdź **tak,** aby usunąć rozszerzenie i poczekaj na zakończenie usuwania przed przejściem do następnego kroku.

   ![Zrzut ekranu przedstawiający rozszerzenia usługi App Service z rozszerzeniem usługi Application Insights dla usługi Azure App Service z wyróżnionym przyciskiem Usuń](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Przejdź do bloku Przegląd zasobu i kliknij usługa Application Insights:

   ![Zrzut ekranu przedstawiający trzy przyciski. Wybrano przycisk Centrum z nazwą Usługi Application Insights](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Jeśli jest to pierwszy raz, gdy przeglądasz plik danych usługi Application Insights dla tej usługi app service, zostanie wyświetlony monit o włączenie usługi Application Insights. Wybierz **pozycję Włącz wgląd w aplikacje**.
 
   ![Zrzut ekranu przedstawiający środowisko po raz pierwszy dla bloku Usługi Application Insights z wyróżnionym przyciskiem Włącz statystykę aplikacji](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. Zostaną wyświetlone bieżące ustawienia usługi Application Insights. Jeśli nie chcesz skorzystać z okazji, aby zmienić ustawienia, możesz je pozostawić w takiej stanie, w jaki sposób. Przycisk **Zastosuj** u dołu bloku nie jest domyślnie włączony i musisz przełączyć jedno z ustawień, aby aktywować przycisk. Nie musisz zmieniać żadnych rzeczywistych ustawień, a raczej możesz zmienić ustawienie, a następnie natychmiast zmienić je z powrotem. Zalecamy przełączanie ustawienia profilera, a następnie wybranie opcji **Zastosuj**.

   ![Zrzut ekranu przedstawiający stronę Konfiguracji usługi app service usługi Application Insights z wyróżnionym na czerwono przyciskem Zastosuj](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Po **kliknięciu przycisku Zastosuj**zostaniesz poproszony o potwierdzenie zmian.

    > [!NOTE]
    > Witryna zostanie ponownie uruchomiona w ramach procesu uaktualniania.

   ![Zrzut ekranu przedstawiający monit o monitorowanie stosowania usługi App Service. W polu tekstowym wyświetlany jest komunikat: "Teraz zastosujemy zmiany w ustawieniach aplikacji i zainstalujemy nasze narzędzia, aby połączyć zasób usługi Application Insights z aplikacją internetową. Spowoduje to ponowne uruchomienie witryny. Czy chcesz kontynuować?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Kliknij **przycisk Tak,** aby zastosować zmiany i poczekać na zakończenie procesu.

Witryna została uaktualniona i jest gotowa do użycia.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Uaktualnianie debugera migawki przy użyciu sdk/nuget

Jeśli aplikacja używa wersji `Microsoft.ApplicationInsights.SnapshotCollector` poniżej wersji 1.3.1, będzie musiała zostać uaktualniona do [nowszej wersji,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) aby kontynuować pracę.
