---
title: Usługi w chmurze lokalnie w emulatorze obliczeń profilowania | Dokumentacja firmy Microsoft
services: cloud-services
description: Zbadaj problemy z wydajnością usług w chmurze przy użyciu profilera Visual Studio
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
ms.openlocfilehash: 8ff7b88a3086488ab669288687c274237ca30b47
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testowanie wydajności usługi w chmurze lokalnie w emulatorze obliczeń platformy Azure przy użyciu profilera Visual Studio
Różnych narzędzi i technik są dostępne do testowania wydajności usługi w chmurze.
Po opublikowaniu usługi w chmurze na platformie Azure, program Visual Studio zbierania danych profilowania, a następnie analizować lokalnie, zgodnie z opisem w [profilowania aplikacji Azure][1].
Umożliwia także diagnostyki do śledzenia różne liczniki wydajności, zgodnie z opisem w [na platformie Azure za pomocą liczników wydajności][2].
Można także profilu aplikacji lokalnie w emulatorze obliczeń przed jego wdrożeniem w chmurze.

W tym artykule opisano profilowanie metodą próbkowania procesora, które można przeprowadzić lokalnie w emulatorze. Próbkowanie Procesora jest metoda profilowania, która nie jest bardzo niepożądanych. Interwałem próbkowania wyznaczonych profilera wykonuje migawkę stosu wywołań. Dane są zbierane przez pewien czas i wyświetlane w raporcie. Ta metoda profilowania zwykle wskazuje, gdzie w praktyce znacznym aplikacji większość pracy Procesora jest wykonywana.  Zapewnia możliwość skupić się na "ścieżce aktywnej" gdzie aplikacji jest poświęcany najwięcej czasu.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Konfigurowanie programu Visual Studio do profilowania
Najpierw istnieje kilka opcji konfiguracji programu Visual Studio, które mogą być przydatne podczas profilowania. Aby sensu raportów profilowania, musisz symboli (.pdb, pliki) dla aplikacji i również symboli dla biblioteki systemu. Należy się upewnić, że odwoływania serwerów dostępna symbolu. Aby to zrobić, na **narzędzia** menu w programie Visual Studio, wybierz **opcje**, a następnie wybierz **debugowanie**, następnie **symbole**. Upewnij się, że serwerów symboli firmy Microsoft znajduje się w obszarze **symboli (.pdb) lokalizacje**.  Można także odwoływać http://referencesource.microsoft.com/symbols, które mogą mieć dodatkowe symboli plików.

![Opcje symbolu][4]

W razie potrzeby można uprościć raportów, które generuje profilera, ustawiając opcję tylko mój kod. Z tylko mój kod włączone stosy wywołań funkcji są uproszczone, dzięki czemu wywołania całkowicie wewnętrznej biblioteki i .NET Framework są ukryte przed raporty. Na **narzędzia** menu, wybierz **opcje**. Następnie rozwiń węzeł **narzędzi wydajności** węzeł i wybierz polecenie **ogólne**. Zaznacz pole wyboru **Włącz opcję tylko mój kod dla raportów profilera**.

![Tylko mój kod opcje][17]

Instrukcje te mogą posłużyć z istniejącego projektu lub z nowym projektem.  Jeśli tworzysz nowy projekt, aby spróbować techniki opisane poniżej, wybierz C# **usługi w chmurze Azure** projekt i wybierz **roli sieci Web** i **roli procesu roboczego**.

![Azure ról projektu usługi w chmurze][5]

Na przykład celów, dodać kod do projektu, który zajmuje dużo czasu i przedstawiono niektóre problem z wydajnością oczywiste. Na przykład dodaj następujący kod do projektu roli proces roboczy:

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

Wywołaj ten kod w metodzie RunAsync w klasie pochodnej RoleEntryPoint roli procesu roboczego. (Zignorować ostrzeżenie dotyczące metody uruchomiona synchronicznie).

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

Tworzenie i uruchamianie usługi w chmurze lokalnie bez debugowania (Ctrl + F5) z konfiguracją rozwiązania ustawioną **wersji**. Dzięki temu, że wszystkie pliki i foldery są tworzone dla aplikacji uruchomionej na komputerze lokalnym i zapewnia, że są uruchomione te emulatory. Uruchom interfejs użytkownika emulatora obliczeń z poziomu paska zadań, aby sprawdzić, czy działa swojej roli procesu roboczego.

## <a name="2-attach-to-a-process"></a>2: dołączanie do procesu
Profilowanie aplikacji przez uruchomienie go z programu Visual Studio 2010 IDE, a nie musi dołączanie profilera do uruchomionego procesu. 

Aby dołączanie profilera do procesu, na **Analizuj** menu, wybierz **profilera** i **Attach/Detach**.

![Dołącz profile — opcja][6]

Dla roli proces roboczy Znajdź proces WaWorkerHost.exe.

![Proces WaWorkerHost][7]

W przypadku folderu projektu na dysku sieciowym, profilera zostanie wyświetlony monit o podanie inną lokalizację, aby zapisać raportów profilowania.

 Możesz także dołączyć do roli sieci web przez dołączenie do WaIISHost.exe.
Jeśli istnieje wiele procesów roli procesu roboczego w aplikacji, należy użyć processID, aby odróżnić je. ProcessID można badać programowo, uzyskując dostęp do obiektu procesu. Na przykład jeśli dodasz ten kod do metody Run klas pochodnych po RoleEntryPoint w roli można przyjrzeć się zaloguj interfejs użytkownika emulatora obliczeń wiedzieć, jakie procesy, aby nawiązać połączenie.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Aby wyświetlić dziennik, uruchom interfejs użytkownika emulatora obliczeń.

![Uruchom Emulator obliczeń interfejsu użytkownika][8]

Otwórz okno konsoli dziennik roli procesu roboczego w interfejs użytkownika emulatora obliczeń, klikając na pasku tytułu okna konsoli. Identyfikator procesu w dzienniku jest widoczny.

![Identyfikator procesu widoku][9]

Jedna podłączona, wykonaj kroki w interfejsie użytkownika aplikacji (w razie potrzeby) do odtworzenia tego scenariusza.

Aby zatrzymać profilowanie, wybrać **Zatrzymaj profilowanie** łącza.

![Zatrzymaj profilowanie opcji][10]

## <a name="3-view-performance-reports"></a>3: Wyświetl raporty wydajności
Zostanie wyświetlony raport wydajności aplikacji.

W tym momencie profilera zatrzymuje wykonywanie, zapisuje dane w pliku Vsp i wyświetla raport zawiera analizę danych.

![Raport programu profilującego][11]

Jeśli widzisz String.wstrcpy w aktywnej ścieżki, kliknij tylko mój kod, aby zmienić widok, aby pokazać tylko kod użytkownika.  Jeśli widzisz String.concat — spróbuj nacisnąć przycisk Pokaż cały kod.

Powinna zostać wyświetlona ZŁĄCZ.teksty metoda i String.concat — zajmuje dużo czasu wykonywania.

![Raport analizy][12]

Jeśli dodano kod łączenia ciągu w tym artykule, w tym powinna zostać wyświetlona ostrzeżenie na liście zadań. Może również zostać wyświetlony ostrzeżenie, że jest nadmierne ilości pamięci, czyli z powodu liczby ciągów, które są tworzone i usunięty.

![Ostrzeżenia wydajności][14]

## <a name="4-make-changes-and-compare-performance"></a>4: wprowadź zmiany i porównanie wydajności
Można także porównać wydajności przed i po zmianie kodu.  Zatrzymaj uruchomionego procesu, a następnie Edytuj kod, aby zastąpić ciąg operacji łączenia z użyciem klasy StringBuilder:

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

Czy inne uruchomienie wydajności, a następnie porównaj wydajność. W Eksploratorze wydajności, jeśli działa znajdują się w tej samej sesji można po prostu wybraniu obu raportów, otwórz menu skrótów i **raporty porównanie wydajności**. Do porównania z uruchomiony w innej sesji wydajności, należy otworzyć **Analizuj** menu i wybierz polecenie **raporty porównanie wydajności**. Określ oba pliki, w wyświetlonym oknie dialogowym.

![Porównanie opcji raporty wydajności][15]

Raporty zaznacz różnice między dwa przebiegi.

![Raport porównawczy][16]

Gratulacje! Rozpoczęciu pracy przy użyciu profilera.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
* Upewnij się, czy profilowania kompilacji wydania i uruchomić bez debugowania.
* Jeśli opcja Attach/Detach nie jest włączona w menu profilera, uruchom Kreatora wydajności.
* Interfejs użytkownika emulatora obliczeń Umożliwia wyświetlenie stanu aplikacji. 
* Jeśli masz problemy z uruchamianiem aplikacji w emulatorze lub dołączenie profilera, zamknij emulator obliczeń w dół i uruchom ją ponownie. Jeśli to nie rozwiąże problemu, spróbuj wykonać ponowny rozruch. Ten problem może wystąpić, jeśli używasz emulatora obliczeniowe do wstrzymania i usuwanie wdrożeń uruchomionych.
* Jeśli używasz dowolnego z profilowania poleceń w wierszu polecenia, szczególnie ustawienia globalne, upewnij się, VSPerfClrEnv /globaloff została wywołana i zamknąć VsPerfMon.exe.
* Jeśli podczas pobierania próbek, zostanie wyświetlony komunikat "PRF0025: żadne dane nie zostały zebrane," Sprawdź, czy dołączyć do procesu ma aktywności Procesora. Aplikacje, które są bezczynny obliczeniowy może nie dawać dane z próbkowania.  Istnieje również możliwość, że proces został zakończony zanim wykonano żadnych próbkowania. Sprawdź, czy metoda Run dla roli, do której są profilowania nie kończy.

## <a name="next-steps"></a>Następne kroki
Instrumentacja Azure pliki binarne w emulatorze nie jest obsługiwany w profilera Visual Studio, ale jeśli chcesz przetestować alokacji pamięci, możesz wybrać tę opcję podczas profilowania. Można również wybrać profilowania współbieżności, który pomaga ustalić, czy wątki są marnować czas rywalizuje blokady lub warstwy profilowanie interakcji, który pomaga wykrywać problemy z wydajnością, gdy interakcji między warstwami aplikacji, większość często między warstwą danych i rolę procesu roboczego.  Można wyświetlić kwerendy bazy danych, które generuje aplikacji i za pomocą danych profilowania zwiększyć korzystania z bazy danych. Informacji o profilowanie interakcji między warstwami znajduje się w temacie we wpisie blogu [wskazówki: w programie Visual Studio Team System 2010 przy użyciu profilera interakcji warstwy][3].

[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
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
