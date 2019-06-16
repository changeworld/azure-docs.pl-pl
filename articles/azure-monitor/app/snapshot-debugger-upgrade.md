---
title: Usługa Azure Application Insights Snapshot Debugger uaktualnienie aplikacji programu .NET | Dokumentacja firmy Microsoft
description: Jak uaktualnić rozszerzenia Snapshot Debugger do najnowszej wersji w usłudze Azure App Services lub za pośrednictwem pakietów Nuget
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60784197"
---
# <a name="upgrading-the-snapshot-debugger"></a>Uaktualnianie rozszerzenia Snapshot Debugger

Aby zapewnić najlepsze możliwe zabezpieczeń danych, firma Microsoft jest wykonują protokołu TLS 1.0 i TLS 1.1, które zostały pokazane są narażone na określone osoby atakujące. Jeśli używasz starszej wersji rozszerzenia lokacji wymaga uaktualnienia, aby kontynuować pracę. W tym dokumencie opisano kroki niezbędne do uaktualnienia usługi rozszerzenia Snapshot debugger do najnowszej wersji. Istnieją dwa podstawowe ścieżki uaktualnienia w zależności od Jeśli włączone jest debugera migawki za pomocą rozszerzenia witryny lub jeśli użyto Nuget zestawu SDK/dodane do Twojej aplikacji. Obie ścieżki uaktualniania omówiono poniżej. 

## <a name="upgrading-the-site-extension"></a>Uaktualnianie rozszerzenia witryny

Jeśli włączono rozszerzenia Snapshot debugger przy użyciu rozszerzenia witryny możesz łatwo przeprowadzić uaktualnienie korzystając z następującej procedury:

1. Zaloguj się do Portalu Azure.
2. Przejdź do zasobu usługi Application Insights i rozszerzenia Snapshot debugger włączone. Na przykład dla aplikacji sieci Web, przejdź do zasobu usługi App Service:

   ![Zrzut ekranu przedstawiający pojedynczego zasobu usługi App Service o nazwie DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Po przejściu do zasobu, kliknij w usłudze Application Insights, w bloku przeglądu:

   ![Zrzut ekranu przedstawiający trzy przyciski. Wybrano środkowy przycisk za pomocą nazwy Application Insights](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Z bieżącymi ustawieniami zostanie otwarty nowy blok. Chyba że użytkownik chce skorzystać z możliwości, aby zmienić swoje ustawienia, można pozostawić ich się. **Zastosuj** przycisk w dolnej części bloku nie jest domyślnie włączona i będzie konieczne jedno z ustawień, aby uaktywnić przycisk przełączania. Nie masz zmienić dowolne ustawienia rzeczywiste, zamiast można zmienić ustawienia i następnie natychmiast zmienić je ponownie. Firma Microsoft zaleca, przełączając Profiler ustawienia, a następnie wybierając **Zastosuj**.

   ![Zrzut ekranu z aplikacji usługi Konfiguracja usługi Application Insights strony za pomocą przycisku Zastosuj wyróżniony czerwonym kolorem](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Po kliknięciu **Zastosuj**, pojawi się prośba o potwierdzenie zmian.

    > [!NOTE]
    > Witryna zostanie ponownie uruchomiona w ramach procesu uaktualniania.

   ![Zrzut ekranu z usługi App Service stosowane monitorowania wiersza. Pole tekstowe wyświetli komunikat: "Mamy teraz zastosować zmiany w ustawieniach Twojej aplikacji i zainstalowanie naszych narzędzi, aby połączyć zasobu usługi Application Insights do aplikacji sieci web. To spowoduje ponowne uruchomienie witryny. Czy chcesz kontynuować?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Kliknij przycisk **tak** Aby zastosować zmiany. Podczas procesu zostanie wyświetlone powiadomienie, pokazujący, że zmiany są stosowane:

   ![Zrzut ekranu przedstawiający Zastosuj zmiany — aktualizowanie komunikat rozszerzeń, który pojawia się w prawym górnym rogu](./media/snapshot-debugger-upgrade/updating-extensions.png)

Po ukończeniu **"Zmiany"** zostanie wyświetlone powiadomienie.

   ![Zrzut ekranu przedstawiający komunikat z informacją, zmiany zostaną zastosowane.](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Witryna został uaktualniony i jest gotowa do użycia.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Uaktualnianie rozszerzenia Snapshot Debugger przy użyciu zestawu SDK/Nuget

Jeśli aplikacja używa wersji `Microsoft.ApplicationInsights.SnapshotCollector` wersji starszej niż 1.3.1, będzie musiała zostać uaktualniony do [nowsza wersja](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) na kontynuowanie pracy.
