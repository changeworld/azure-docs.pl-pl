---
title: Profilowanie usługi w chmurze lokalnie w emulatorze obliczeniowym | Dokumenty firmy Microsoft
services: cloud-services
description: Badanie problemów z wydajnością w usługach w chmurze za pomocą profilera programu Visual Studio
documentationcenter: ''
author: mikejo
manager: jillfra
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 21270d3c7143ce063ffe30d939368b9813e9072e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70094104"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testowanie wydajności usługi w chmurze lokalnie w emulatorze obliczania platformy Azure przy użyciu programu Visual Studio Profiler
Dostępnych jest wiele narzędzi i technik do testowania wydajności usług w chmurze.
Podczas publikowania usługi w chmurze na platformie Azure, można mieć Visual Studio zbierać dane profilowania, a następnie analizować je lokalnie, zgodnie z opisem w [profilowanie aplikacji platformy Azure][1].
Diagnostykę można również używać do śledzenia różnych liczników wydajności, zgodnie z opisem w [obszarze Używanie liczników wydajności na platformie Azure.][2]
Można również profilować aplikację lokalnie w emulatorze obliczeniowym przed wdrożeniem jej w chmurze.

W tym artykule opisano metodę próbkowania procesora CPU profilowania, które można wykonać lokalnie w emulatorze. Próbkowanie procesora CPU jest metodą profilowania, która nie jest bardzo inwazyjna. W wyznaczonym interwale próbkowania profiler wykonuje migawkę stosu wywołań. Dane są zbierane przez pewien czas i wyświetlane w raporcie. Ta metoda profilowania ma tendencję do wskazywania, gdzie w aplikacji intensywnej obliczeniowej większość pracy procesora CPU jest wykonywana.  Daje to możliwość skupienia się na "gorącej ścieżce", w której aplikacja spędza najwięcej czasu.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Konfigurowanie programu Visual Studio do profilowania
Po pierwsze istnieje kilka opcji konfiguracji programu Visual Studio, które mogą być przydatne podczas profilowania. Aby zrozumieć raporty profilowania, potrzebne są symbole (pliki pdb) dla aplikacji, a także symbole dla bibliotek systemowych. Należy upewnić się, że odwołujesz się do dostępnych serwerów symboli. Aby to zrobić, w menu **Narzędzia** w programie Visual Studio wybierz polecenie **Opcje**, a następnie wybierz polecenie **Debugowanie**, a następnie **symbole**. Upewnij się, że serwery symboli firmy Microsoft są wymienione w **obszarze Lokalizacje plików symboli (pdb).**  Można również https://referencesource.microsoft.com/symbolsodwołać się do programu , który może mieć dodatkowe pliki symboli.

![Opcje symbolu][4]

W razie potrzeby można uprościć raporty generowane przez profiler, ustawiając just my code. Po włączeniu tylko mój kod stosy wywołań funkcji są uproszczone, dzięki czemu wywołania całkowicie wewnętrzne do bibliotek i programu .NET Framework są ukryte przed raportami. W menu **Narzędzia** wybierz polecenie **Opcje**. Następnie rozwiń węzeł **Narzędzia wydajności** i wybierz pozycję **Ogólne**. Zaznacz pole wyboru **Włącz tylko mój kod dla raportów profilera**.

![Opcje tylko mojego kodu][17]

Tych instrukcji można użyć z istniejącym projektem lub z nowym projektem.  Jeśli tworzysz nowy projekt, aby wypróbować techniki opisane poniżej, wybierz projekt **usługi Azure Cloud Service** w języku C# i wybierz rolę sieci **Web** i rolę **procesu roboczego.**

![Role projektu usługi Azure Cloud Service][5]

Na przykład cele, dodaj kod do projektu, który zajmuje dużo czasu i pokazuje jakiś oczywisty problem z wydajnością. Na przykład dodaj następujący kod do projektu roli procesu roboczego:

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

Wywołanie tego kodu z RunAsync metody w roli procesu roboczego RoleEntryPoint klasy. (Zignoruj ostrzeżenie dotyczące metody działającej synchronicznie).

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

Skompiluj i uruchom usługę w chmurze lokalnie bez debugowania (Ctrl+F5), z konfiguracją rozwiązania **ustawioną**na Release . Gwarantuje to, że wszystkie pliki i foldery są tworzone do uruchamiania aplikacji lokalnie i zapewnia, że wszystkie emulatory są uruchamiane. Uruchom interfejs użytkownika emulatora obliczeń z paska zadań, aby sprawdzić, czy rola procesu roboczego jest uruchomiona.

## <a name="2-attach-to-a-process"></a>2: Dołącz do procesu
Zamiast profilowania aplikacji przez uruchomienie go z visual studio 2010 IDE, należy dołączyć profiler do uruchomionego procesu. 

Aby dołączyć profiler do procesu, w menu **Analizuj** wybierz polecenie **Profiler** i **Dołącz/Odłącz**.

![Dołącz opcję profilu][6]

Dla roli procesu procesu WaWorkerHost.exe.

![Proces WaWorkerHost][7]

Jeśli folder projektu znajduje się na dysku sieciowym, profiler poprosi o podanie innej lokalizacji w celu zapisania raportów profilowania.

 Można również dołączyć do roli sieci web, dołączając do WaIISHost.exe.
Jeśli istnieje wiele procesów roli procesu roboczego w aplikacji, należy użyć processID je odróżnić. Programowo można wysyłać zapytania do processID, uzyskując dostęp do obiektu Process. Na przykład jeśli dodać ten kod do Run metody RoleEntryPoint klasy pochodnej w roli, można spojrzeć na dziennik w interfejsie użytkownika emulatora obliczeń, aby wiedzieć, jaki proces połączyć się z.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Aby wyświetlić dziennik, uruchom interfejs użytkownika emulatora obliczeń.

![Uruchamianie interfejsu użytkownika emulatora obliczeń][8]

Otwórz okno konsoli dziennika roli procesu roboczego w interfejsie użytkownika emulatora obliczeniowego, klikając pasek tytułu okna konsoli. Identyfikator procesu można zobaczyć w dzienniku.

![Wyświetl identyfikator procesu][9]

Jeden z nich został dołączony, wykonaj kroki w interfejsie użytkownika aplikacji (w razie potrzeby), aby odtworzyć scenariusz.

Aby zatrzymać profilowanie, wybierz łącze **Zatrzymaj profilowanie.**

![Opcja Zatrzymaj profilowanie][10]

## <a name="3-view-performance-reports"></a>3: Wyświetlanie raportów o skuteczności
Zostanie wyświetlony raport wydajności aplikacji.

W tym momencie profiler przestaje wykonywać, zapisuje dane w pliku vsp i wyświetla raport, który pokazuje analizę tych danych.

![Raport profilera][11]

Jeśli widzisz String.wstrcpy w Hot Path, kliknij tylko mój kod, aby zmienić widok, aby wyświetlić tylko kod użytkownika.  Jeśli widzisz String.Concat, spróbuj nacisnąć przycisk Pokaż cały kod.

Powinien zostać wyświetlony Concatenate metody i String.Concat zajmuje dużą część czasu wykonywania.

![Analiza sprawozdania][12]

Jeśli dodano kod łączenia ciągów w tym artykule, powinien zostać wyświetlony ostrzeżenie na liście zadań dla tego. Może również pojawić się ostrzeżenie, że istnieje nadmierna ilość wyrzucania elementów bezużytecznych, co jest spowodowane liczbą ciągów, które są tworzone i usuwane.

![Ostrzeżenia dotyczące wydajności][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Wprowadzanie zmian i porównywanie wydajności
Można również porównać wydajność przed i po zmianie kodu.  Zatrzymaj uruchomiony proces i edytuj kod, aby zastąpić operację łączenia ciągów za pomocą StringBuilder:

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

Wykonaj inny przebieg wydajności, a następnie porównaj wydajność. W Eksploratorze wydajności, jeśli przebiegi są w tej samej sesji, można po prostu wybrać oba raporty, otworzyć menu skrótów i wybrać **pozycję Porównaj raporty wydajności**. Jeśli chcesz porównać je z uruchomieniem w innej sesji wydajności, otwórz menu **Analizuj** i wybierz polecenie **Porównaj raporty wydajności**. Określ oba pliki w wyświetlonym oknie dialogowym.

![Opcja Porównywanie raportów o skuteczności][15]

Raporty podkreślają różnice między dwoma przebiegami.

![Raport porównawczy][16]

Gratulacje! Zacząłeś z profilera.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
* Upewnij się, że profilowanie kompilacji wydania i uruchomić bez debugowania.
* Jeśli opcja Dołącz/Odłącz nie jest włączona w menu Profiler, uruchom Kreatora wydajności.
* Użyj interfejsu użytkownika emulatora obliczeń, aby wyświetlić stan aplikacji. 
* Jeśli masz problemy z uruchamianiem aplikacji w emulatorze lub dołączaniem profilera, zamknij emulator obliczeniowy i uruchom go ponownie. Jeśli to nie rozwiąże problemu, spróbuj ponownie uruchomić komputer. Ten problem może wystąpić, jeśli używasz emulatora obliczeń do zawieszenia i usunięcia uruchomionych wdrożeń.
* Jeśli użyto dowolnego polecenia profilowania z wiersza polecenia, zwłaszcza ustawień globalnych, upewnij się, że został wywołany vsperfClrEnv /globaloff i że vsPerfMon.exe został zamknięty.
* Jeśli podczas próbkowania zostanie wyświetlony komunikat "PRF0025: Nie zostały zebrane żadne dane", sprawdź, czy proces, do którego dołączono, ma działanie procesora CPU. Aplikacje, które nie wykonują żadnej pracy obliczeniowej, mogą nie tworzyć żadnych danych próbkowania.  Jest również możliwe, że proces zakończył się przed każdym próbkowanie zostało zrobione. Sprawdź, czy run metody dla roli, która są profilowania nie kończy.

## <a name="next-steps"></a>Następne kroki
Instrumentowanie plików binarnych platformy Azure w emulatorze nie jest obsługiwane w programie Visual Studio profiler, ale jeśli chcesz przetestować alokacji pamięci, można wybrać tę opcję podczas profilowania. Można również wybrać profilowanie współbieżności, które pomaga określić, czy wątki marnują czas na konkurowanie o blokady, czy profilowanie interakcji warstwy, co pomaga śledzić problemy z wydajnością podczas interakcji między warstwami aplikacji, większość często między warstwą danych a rolą procesu roboczego.  Można wyświetlić kwerendy bazy danych, które aplikacja generuje i używać danych profilowania, aby poprawić korzystanie z bazy danych. Aby uzyskać informacje na temat profilowania interakcji warstwy, zobacz przewodnik w blogu [Przewodnik: Korzystanie z profilera interakcji warstwy w programie Visual Studio Team System 2010][3].

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
