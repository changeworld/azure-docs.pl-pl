---
title: Profilowanie usługi w chmurze lokalnie w emulatorze obliczeń | Dokumentacja firmy Microsoft
services: cloud-services
description: Badanie problemów z wydajnością w usługach w chmurze za pomocą profilera Visual Studio
documentationcenter: ''
author: mikejo
manager: douge
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 40ba5814bce08037b9e4d0787defbab4d02e58df
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128570"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testowanie wydajności usługi w chmurze lokalnie w emulatorze obliczeń platformy Azure przy użyciu programu Visual Studio Profiler
Różne narzędzia i techniki są dostępne do testowania wydajności usługi w chmurze.
Podczas publikowania usługi w chmurze na platformie Azure, masz programu Visual Studio, zbierania danych profilowania, a następnie analizować lokalnie, zgodnie z opisem w [profilowanie aplikacji na platformie Azure][1].
Można również użyć diagnostyki do śledzenia różnych liczników wydajności, zgodnie z opisem w [przy użyciu liczników wydajności na platformie Azure][2].
Możesz również chcieć Profiluj aplikację lokalnie w emulatorze obliczeń przed jego wdrożeniem w chmurze.

W tym artykule opisano profilowanie metodą próbkowania procesora, które można przeprowadzić lokalnie w emulatorze. Próbkowanie Procesora jest metodą profilowania, która nie jest bardzo bez wprowadzania niepożądanych. Interwałem próbkowania wyznaczonym program profilujący tworzy migawkę stosu wywołań. Dane są zbierane w przedziale czasu i wyświetlane w raporcie. Ta metoda profilowania zwykle wskazuje, gdzie w aplikacji wymaga dużej mocy obliczeniowej większość pracy procesora CPU jest wykonywana.  To umożliwia skoncentrowanie się na "ścieżki aktywnej" gdzie aplikacja jest wydatków najwięcej czasu.

## <a name="1-configure-visual-studio-for-profiling"></a>1. Konfigurowanie programu Visual Studio do profilowania
Po pierwsze istnieje kilka opcji konfiguracji programu Visual Studio, które mogą być pomocne w przypadku profilowania. Aby poznać raporty profilowania, konieczne będzie symboli (pliki .pdb) dla swojej aplikacji, a także symbole dla bibliotek systemowych. Należy się upewnić, że odwoływać się z serwerów symboli dostępne. Aby to zrobić, na **narzędzia** menu w programie Visual Studio, wybierz opcję **opcje**, następnie wybierz **debugowanie**, następnie **symbole**. Upewnij się, że serwery symboli firmy Microsoft znajduje się w obszarze **symboli (.pdb) lokalizacji**.  Możesz też przywołać https://referencesource.microsoft.com/symbols, które mogą mieć dodatkowe symboli plików.

![Opcje symboli][4]

Jeśli to konieczne, można uprościć raporty, które generuje program profilujący, ustawiając opcję tylko mój kod. Dzięki włączeniu tylko mój kod stosy wywołań funkcji są uproszczone, dzięki czemu wywołania całkowicie wewnętrzne biblioteki i .NET Framework są ukryte przed raportów. Na **narzędzia** menu, wybierz **opcje**. Następnie rozwiń **narzędzia do oceny wydajności** węzeł i wybierz polecenie **ogólne**. Zaznacz pole wyboru **Włącz tylko mój kod dla raportów profilera**.

![Tylko mój kod opcje][17]

Z istniejącego projektu lub z nowym projektem, można użyć tych instrukcji.  Jeśli tworzysz nowy projekt do wypróbowania techniki opisane poniżej, wybierz opcję języka C# **usługa w chmurze** projektu, a następnie wybierz **roli sieci Web** i **roli procesu roboczego**.

![Role usługi w chmurze platformy Azure w projekcie][5]

Na przykład celów, dodać kod do projektu, który zajmuje dużo czasu i pokazuje niektóre problem z wydajnością oczywiste. Na przykład dodaj następujący kod do projektu roli proces roboczy:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Wywołaj ten kod w metodzie RunAsync w klasie pochodnej RoleEntryPoint roli procesu roboczego. (Ignoruj ostrzeżenia o metodzie uruchomiona synchronicznie).

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Skompilować i uruchomić usługi w chmurze lokalnie bez debugowania (klawisze Ctrl + F5), za pomocą konfiguracji rozwiązania, ustaw **wersji**. Zapewnia to, że wszystkie pliki i foldery są tworzone dla aplikacji uruchomionej na komputerze lokalnym i gwarantuje, że są uruchomione wszystkie emulatorów. Uruchom interfejs użytkownika emulatora obliczeń z paska zadań, aby sprawdzić, czy działa swojej roli procesu roboczego.

## <a name="2-attach-to-a-process"></a>2. Dołącz do procesu
Zamiast sprofilować aplikację, uruchamiając go za pomocą programu Visual Studio 2010 IDE, należy dołączyć profiler do uruchomionego procesu. 

Aby dołączyć profiler do procesu, na **analizy** menu, wybierz **Profiler** i **Attach/Detach**.

![Dołącz profile — opcja][6]

Dla roli procesu roboczego Znajdź proces WaWorkerHost.exe.

![Proces WaWorkerHost][7]

W przypadku folderu projektu na dysku sieciowym, program profilujący zapyta zapewnienie innej lokalizacji, aby zapisywać raporty profilowania.

 Można także dołączyć do roli sieci web, dołączając do WaIISHost.exe.
W przypadku wielu procesów roli proces roboczy w aplikacji, należy użyć processID, aby odróżnić je. Można programowo zapytania processID, uzyskując dostęp do obiektu procesu. Na przykład jeśli dodasz ten kod do metody wykonywania klasy pochodnej RoleEntryPoint w roli zapoznanie się z logowania interfejs użytkownika emulatora obliczeń wiedzieć, jakie procesy, aby nawiązać połączenie.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Aby wyświetlić dziennik, należy uruchomić interfejs użytkownika emulatora obliczeń.

![Uruchom interfejs użytkownika emulatora obliczeń][8]

Otwórz okno konsoli dziennika roli procesu roboczego w interfejs użytkownika emulatora obliczeń, klikając przycisk na pasku tytułu okna konsoli. Możesz zobaczyć identyfikator procesu w dzienniku.

![Wyświetl identyfikator procesu][9]

Jedna jest podłączona, wykonaj kroki w interfejsie użytkownika aplikacji (jeśli jest to konieczne) do odtworzenia tego scenariusza.

Jeśli chcesz zatrzymać profilowanie, wybierz **Zatrzymaj profilowanie** łącza.

![Zatrzymaj profilowanie opcji][10]

## <a name="3-view-performance-reports"></a>3. Wyświetl raporty dotyczące wydajności
Raport o wydajności dla aplikacji jest wyświetlany.

W tym momencie program profilujący zatrzymuje wykonywanie, zapisuje dane w pliku Vsp i wyświetla raport zawiera analizę tych danych.

![Profiler raportu][11]

Jeśli widzisz String.wstrcpy gorącej ścieżki, kliknij opcję tylko mój kod, aby zmienić widok, aby wyświetlić tylko kod użytkownika.  Jeśli widzisz String.concat —, spróbuj nacisnąć przycisk Pokaż cały kod.

Powinien zostać wyświetlony ZŁĄCZ.teksty metody i String.concat — zajmuje dużo czasu wykonywania.

![Analizę raportu][12]

Jeśli dodano kod konkatenacji ciągów w tym artykule, powinny pojawić się ostrzeżenie, na liście zadań, w tym. Może też pojawić się ostrzeżenie, brak nadmiernej liczby wyrzucania elementów bezużytecznych, czyli ze względu na liczbę ciągów, które są tworzone i usunięty.

![Ostrzeżeń dotyczących wydajności][14]

## <a name="4-make-changes-and-compare-performance"></a>4. Wprowadź zmiany i porównanie wydajności
Można również porównać wydajność przed i po zmianie kodu.  Zatrzymaj uruchomionego procesu, a następnie edytować kod i Zastąp ciąg operacji łączenia z użyciem klasy StringBuilder:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Wykonaj inne uruchomienie wydajności, a następnie porównaj wydajność. W Eksploratorze wydajności w przypadku uruchomienia w tej samej sesji możesz można po prostu zaznacz oba raporty, otwórz menu skrótów i wybierz **Porównaj wydajność raportów**. Jeśli chcesz porównać z przebiegiem w innej sesji wydajności, otwórz **analizy** menu i wybrać **Porównaj wydajność raportów**. Określ oba pliki w zostanie wyświetlone okno dialogowe.

![Porównanie opcji raportów wydajności][15]

Raporty zaznacz różnice między dwoma przebiegami.

![Raport porównawczy][16]

Gratulacje! Rozpoczęciu pracy przy użyciu profilera.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
* Upewnij się, profilowany kompilację wydania i uruchom bez debugowania.
* Jeśli nie włączono opcji Attach/Detach menu Profiler, uruchom Kreatora wydajności.
* Interfejs użytkownika emulatora obliczeń Umożliwia wyświetlenie stanu aplikacji. 
* Jeśli masz problemy z uruchamianiem aplikacji w emulatorze lub dołączanie programu profiler zamykanie emulatora obliczeń w dół, a następnie uruchom go ponownie. Jeśli to nie rozwiąże problemu, spróbuj wykonać ponowny rozruch. Ten problem może wystąpić, jeśli używasz emulatora obliczeń do wstrzymania i Usuń uruchomionych wdrożeniach.
* Jeśli używasz dowolnego polecenia profilowania z wiersza polecenia, a szczególnie globalne ustawienia, upewnij się, VSPerfClrEnv /globaloff została wywołana i że VsPerfMon.exe został zamknięty.
* Jeśli podczas pobierania próbek, zostanie wyświetlony komunikat "PRF0025: Zebrano żadnych danych,"Sprawdź, czy Proces, który został dołączony do ma działanie procesora CPU. Aplikacje, które nie wykonują wszelkie prace obliczeniową może nie dawać dane z próbkowania.  Istnieje również możliwość, że proces zakończony przed wykonano wszystkie pobierania próbek. Sprawdź, czy metoda Run dla roli, który jest profilowany nie kończy.

## <a name="next-steps"></a>Następne kroki
Instrumentacja Azure plików binarnych w emulatorze nie jest obsługiwane w programie Visual Studio profiler, ale jeśli chcesz przetestować alokacji pamięci, możesz wybrać tę opcję podczas profilowania. Można również wybrać profilowania współbieżności, który pomaga ustalić, czy wątki są marnowania czasu rywalizując o blokady lub warstwy profilowanie interakcji między, co pomoże Ci w wyszukaniu problemy z wydajnością podczas interakcji między warstwami aplikacji, najbardziej często między warstwą danych i rolę procesu roboczego.  Można wyświetlić zapytania do bazy danych, które aplikacja generuje i używać danych profilowania w celu używania bazy danych. Aby uzyskać informacje o profilowaniu interakcji między warstwami, zobacz wpis w blogu [instruktażu: Przy użyciu warstwy Profiler interakcji w programie Visual Studio Team System 2010][3].

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: https://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: https://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
