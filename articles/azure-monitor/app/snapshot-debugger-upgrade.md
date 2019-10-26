---
title: Usługa Azure Application Insights Snapshot Debugger uaktualnienie dla aplikacji .NET | Microsoft Docs
description: Jak uaktualnić Snapshot Debugger do najnowszej wersji na platformie Azure App Services lub za pośrednictwem pakietów NuGet
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899848"
---
# <a name="upgrading-the-snapshot-debugger"></a>Uaktualnianie Snapshot Debugger

Aby zapewnić najwyższy możliwy bezpieczeństwo danych, firma Microsoft nie korzysta z protokołów TLS 1,0 i TLS 1,1, które zostały wystawione jako podatne na wyznaczone osoby atakujące. Jeśli używasz starszej wersji rozszerzenia witryny, będzie ono wymagało uaktualnienia, aby kontynuować pracę. W tym dokumencie przedstawiono kroki wymagane do uaktualnienia debugera migawek do najnowszej wersji. Istnieją dwie podstawowe ścieżki uaktualniania w zależności od tego, czy włączono Snapshot Debugger przy użyciu rozszerzenia witryny, czy użyto zestawu SDK/NuGet dodanego do aplikacji. Obie ścieżki uaktualniania zostały omówione poniżej. 

## <a name="upgrading-the-site-extension"></a>Uaktualnianie rozszerzenia witryny

Jeśli debuger migawek został włączony przy użyciu rozszerzenia witryny, można je łatwo uaktualnić przy użyciu następującej procedury:

1. Zaloguj się do Portalu Azure.
2. Przejdź do zasobu z włączonym debugerem Application Insights i migawek. Na przykład w przypadku aplikacji sieci Web przejdź do App Service zasobu:

   ![Zrzut ekranu poszczególnych zasobów App Service o nazwie DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Po przejściu do zasobu kliknij pozycję Application Insights w bloku przegląd:

   ![Zrzut ekranu przedstawiający trzy przyciski. Wybrano środkowy przycisk o nazwie Application Insights](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Zostanie otwarty nowy blok z bieżącymi ustawieniami. Jeśli chcesz zmienić ustawienia, możesz pozostawić je w stanie takim, w jakim się znajdują. Przycisk **Zastosuj** w dolnej części bloku nie jest domyślnie włączony i konieczne będzie przełączenie jednego z ustawień, aby aktywować przycisk. Nie musisz zmieniać żadnych rzeczywistych ustawień, zamiast tego możesz zmienić ustawienie, a następnie natychmiast zmienić je ponownie. Zalecamy przełączenie ustawienia profilera, a następnie wybranie pozycji **Zastosuj**.

   ![Zrzut ekranu strony konfiguracji App Service Application Insights z wyróżnionym przyciskiem Zastosuj w kolorze czerwonym](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Po kliknięciu przycisku **Zastosuj**zostanie wyświetlony monit o potwierdzenie zmian.

    > [!NOTE]
    > Lokacja zostanie ponownie uruchomiona w ramach procesu uaktualniania.

   ![Zrzut ekranu przedstawiający wiersz App Service zastosowania monitorowania. Pole tekstowe wyświetla komunikat: "teraz stosujemy zmiany w ustawieniach aplikacji i zainstalujemy narzędzia do łączenia zasobów Application Insights z aplikacją sieci Web. Spowoduje to ponowne uruchomienie witryny. Czy chcesz kontynuować? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Kliknij przycisk **tak** , aby zastosować zmiany. Podczas procesu pojawi się powiadomienie z informacją, że zmiany są stosowane:

   ![Zrzut ekranu przedstawiający komunikat dotyczący zastosowania zmian — aktualizowanie rozszerzeń, który jest wyświetlany w prawym górnym rogu](./media/snapshot-debugger-upgrade/updating-extensions.png)

Po zakończeniu zostanie wyświetlone powiadomienie **"zmiany zostaną zastosowane"** .

   ![Zrzut ekranu przedstawiający komunikaty zawierające zmiany są stosowane](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Witryna została teraz uaktualniona i jest gotowa do użycia.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Uaktualnianie Snapshot Debugger przy użyciu zestawu SDK/NuGet

Jeśli aplikacja używa wersji `Microsoft.ApplicationInsights.SnapshotCollector` starszej niż wersja 1.3.1, należy przeprowadzić uaktualnienie do [nowszej wersji](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , aby kontynuować pracę.
