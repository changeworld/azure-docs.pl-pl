---
title: Rozwiązywanie problemów z operacjami danych za pomocą diagnostyki i analizatora komunikatów
titleSuffix: Azure Storage
description: Samouczek przedstawiający kompleksowe rozwiązywanie problemów za pomocą usługi Azure Storage Analytics, AzCopy i Microsoft Message Analyzer
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 69983502fb7d099f474fb1c4c084f5d381a173e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314763"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Kompleksowe rozwiązywanie problemów przy użyciu metryk i rejestrowania usługi Azure Storage, narzędzia AzCopy i narzędzia Message Analyzer

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnozowanie i rozwiązywanie problemów to kluczowa umiejętność tworzenia i obsługi aplikacji klienckich za pomocą usługi Microsoft Azure Storage. Ze względu na rozproszony charakter aplikacji platformy Azure diagnozowanie i rozwiązywanie problemów z błędami i wydajnością może być bardziej złożone niż w tradycyjnych środowiskach.

W tym samouczku zademonstrujemy, jak zidentyfikować pewne błędy, które mogą mieć wpływ na wydajność, i rozwiązywać problemy z błędami end-to-end przy użyciu narzędzi dostarczonych przez firmy Microsoft i usługę Azure Storage w celu optymalizacji aplikacji klienckiej.

Ten samouczek zawiera praktyczne badanie scenariusza rozwiązywania problemów typu end-to-end. Aby uzyskać szczegółowy przewodnik koncepcyjny dotyczący rozwiązywania problemów z aplikacjami magazynu platformy Azure, zobacz [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Narzędzia do rozwiązywania problemów z aplikacjami usługi Azure Storage

Aby rozwiązać problemy z aplikacjami klienckimi przy użyciu usługi Microsoft Azure Storage, można użyć kombinacji narzędzi, aby określić, kiedy wystąpił problem i co może być przyczyną problemu. Do tych narzędzi należą:

* **Usługa Azure Storage Analytics**. [Usługa Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) udostępnia metryki i rejestrowanie dla usługi Azure Storage.

  * **Metryki magazynu** śledzą metryki transakcji i metryki pojemności dla twojego konta magazynu. Za pomocą metryk można określić, jak aplikacja działa zgodnie z różnymi miarami. Aby uzyskać więcej informacji na temat typów metryk śledzonych przez analizę magazynu, [zobacz Schemat tabeli metryk analizy magazynu.](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)
  * **Rejestrowanie magazynu** rejestruje każde żądanie do usług Azure Storage do dziennika po stronie serwera. Dziennik śledzi szczegółowe dane dla każdego żądania, w tym wykonano operację, stan operacji i informacje o opóźnieniach. Zobacz [Format dziennika analizy magazynu, aby](/rest/api/storageservices/Storage-Analytics-Log-Format) uzyskać więcej informacji na temat danych żądania i odpowiedzi, które są zapisywane w dziennikach przez usługi Storage Analytics.

* **Portal Azure**. Metryki i rejestrowanie konta magazynu można skonfigurować w [witrynie Azure Portal](https://portal.azure.com). Można również wyświetlić wykresy i wykresy, które pokazują, jak aplikacja działa w czasie i skonfigurować alerty, aby powiadamiać, jeśli aplikacja działa inaczej niż oczekiwano dla określonej metryki.

    Zobacz [Monitorowanie konta magazynu w witrynie Azure portal,](storage-monitor-storage-account.md) aby uzyskać informacje na temat konfigurowania monitorowania w witrynie Azure portal.
* **AzCopy**. Dzienniki serwera dla usługi Azure Storage są przechowywane jako obiekty blob, dzięki czemu można użyć AzCopy skopiować obiekty blob dziennika do katalogu lokalnego do analizy przy użyciu microsoft message analyzer. Aby uzyskać więcej informacji na temat AzCopy, zobacz Przesyłanie danych za [pomocą narzędzia wiersza polecenia AzCopy.](storage-use-azcopy.md)
* **Analizator komunikatów firmy Microsoft**. Analizator wiadomości to narzędzie, które zużywa pliki dziennika i wyświetla dane dziennika w formacie wizualnym, które ułatwia filtrowanie, wyszukiwanie i grupowanie danych dziennika do przydatnych zestawów, których można używać do analizowania błędów i problemów z wydajnością. Więcej informacji na temat [analizatora komunikatów można znaleźć w podręczniku operacyjnym analizatora komunikatów](https://technet.microsoft.com/library/jj649776.aspx) firmy Microsoft.

## <a name="about-the-sample-scenario"></a>Przykładowy scenariusz — informacje

W tym samouczku zbadamy scenariusz, w którym metryki usługi Azure Storage wskazują niski procent sukcesu aplikacji, która wywołuje usługę Azure Storage. Metryka niskiego procentu sukcesu (wyświetlana jako **PercentSuccess** w [witrynie Azure portal](https://portal.azure.com) i w tabelach metryk) śledzi operacje, które zakończyły się pomyślnie, ale zwraca kod stanu HTTP, który jest większy niż 299. W plikach dziennika magazynu po stronie serwera operacje te są rejestrowane ze stanem transakcji **ClientOtherErrors**. Aby uzyskać więcej informacji na temat niskich procent metryki sukcesu, zobacz [Metryki pokaż niskie PercentSuccess lub zapisy dziennika analizy mają operacje ze stanem transakcji ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operacje usługi Azure Storage mogą zwracać kody stanu HTTP większe niż 299 w ramach ich normalnej funkcjonalności. Jednak te błędy w niektórych przypadkach wskazują, że można zoptymalizować aplikację kliencką pod kątem zwiększonej wydajności.

W tym scenariuszu rozważymy niski wskaźnik sukcesu procentowego jako coś poniżej 100%. Możesz jednak wybrać inny poziom metryki, zgodnie z potrzebami. Zaleca się, aby podczas testowania aplikacji, należy ustanowić tolerancję linii bazowej dla kluczowych metryk wydajności. Na przykład można określić, na podstawie testów, że aplikacja powinna mieć spójny procent wskaźnik sukcesu 90% lub 85%. Jeśli dane metryki pokazują, że aplikacja odbiega od tej liczby, następnie można zbadać, co może być przyczyną wzrostu.

W naszym przykładowym scenariuszu po ustaleniu, że wskaźnik wskaźnika powodzenia procentu jest poniżej 100%, zbadamy dzienniki, aby znaleźć błędy, które są skorelowane z metrykami, i użyjemy ich, aby dowiedzieć się, co jest przyczyną niższego procentowego wskaźnika powodzenia. Przyjrzymy się konkretnie błędom w zakresie 400. Następnie będziemy dokładniej zbadać 404 (nie znaleziono) błędy.

### <a name="some-causes-of-400-range-errors"></a>Niektóre przyczyny błędów 400 zakres

Poniższe przykłady przedstawia próbkowanie niektórych błędów zakresu 400 dla żądań względem usługi Azure Blob Storage i ich możliwych przyczyn. Każdy z tych błędów, a także błędy w zakresie 300 i 500, może przyczynić się do niskiego procenta sukcesu.

Należy pamiętać, że poniższe listy są dalekie od zakończenia. Zobacz [kody stanu i błędów](https://msdn.microsoft.com/library/azure/dd179382.aspx) w msdn, aby uzyskać szczegółowe informacje na temat ogólnych błędów usługi Azure Storage i błędów specyficznych dla każdej z usług magazynu.

#### <a name="status-code-404-not-found-examples"></a>Kod stanu 404 (nie znaleziono) Przykłady

Występuje, gdy operacja odczytu względem kontenera lub obiektu blob nie powiedzie się, ponieważ nie znaleziono obiektu blob lub kontenera.

* Występuje, jeśli kontener lub obiekt blob został usunięty przez innego klienta przed tym żądaniem.
* Występuje, jeśli używasz wywołania interfejsu API, który tworzy kontener lub obiekt blob po sprawdzeniu, czy istnieje. Interfejsy API CreateIfNotExists najpierw wywołać HEAD, aby sprawdzić istnienie kontenera lub obiektu blob; jeśli nie istnieje, zwracany jest błąd 404, a następnie drugie wywołanie PUT jest dokonywane w celu zapisania kontenera lub obiektu blob.

#### <a name="status-code-409-conflict-examples"></a>Kod stanu 409 (Konflikt) Przykłady

* Występuje, jeśli używasz Create API do utworzenia nowego kontenera lub obiektu blob, bez sprawdzania istnienia po raz pierwszy i kontenera lub obiektu blob o tej nazwie już istnieje.
* Występuje, jeśli kontener jest usuwany i próbujesz utworzyć nowy kontener o tej samej nazwie przed zakończeniem operacji usuwania.
* Występuje, jeśli określisz dzierżawę kontenera lub obiektu blob, a istnieje już obecny dzierżawy.

#### <a name="status-code-412-precondition-failed-examples"></a>Kod stanu 412 (warunek wstępny nie powiódł się) Przykłady

* Występuje, gdy warunek określony przez nagłówek warunkowy nie został spełniony.
* Występuje, gdy określony identyfikator dzierżawy nie jest zgodny z identyfikatorem dzierżawy w kontenerze lub obiekcie blob.

## <a name="generate-log-files-for-analysis"></a>Generowanie plików dziennika do analizy

W tym samouczku użyjemy analizatora wiadomości do pracy z trzema różnymi typami plików dziennika, chociaż można wybrać pracę z dowolnym z następujących plików:

* **Dziennik serwera**, który jest tworzony po włączeniu rejestrowania usługi Azure Storage. Dziennik serwera zawiera dane dotyczące każdej operacji wywoływanej przez jedną z usług Azure Storage — obiekt blob, kolejkę, tabelę i plik. Dziennik serwera wskazuje, która operacja została wywołana i jaki kod stanu został zwrócony, a także inne szczegóły dotyczące żądania i odpowiedzi.
* **Dziennik klienta .NET**, który jest tworzony po włączeniu rejestrowania po stronie klienta z poziomu aplikacji .NET. Dziennik klienta zawiera szczegółowe informacje o tym, jak klient przygotowuje żądanie i odbiera i przetwarza odpowiedź.
* **Dziennik śledzenia sieci HTTP**, który zbiera dane dotyczące danych żądania i odpowiedzi HTTP/HTTPS, w tym dla operacji w usłudze Azure Storage. W tym samouczku wygenerujemy śledzenia sieci za pośrednictwem analizatora wiadomości.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurowanie rejestrowania i metryk po stronie serwera

Najpierw musimy skonfigurować rejestrowanie usługi Azure Storage i metryki, dzięki czemu mamy dane od strony usługi do analizy. Rejestrowanie i metryki można skonfigurować na różne sposoby — za pośrednictwem [witryny Azure portal](https://portal.azure.com)— za pomocą programu PowerShell lub programowo. Zobacz [Włączanie metryk](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) i [Włączanie rejestrowania, aby](storage-analytics-logging.md#enable-storage-logging) uzyskać szczegółowe informacje na temat konfigurowania rejestrowania i metryk.

### <a name="configure-net-client-side-logging"></a>Konfigurowanie rejestrowania po stronie klienta platformy .NET

Aby skonfigurować rejestrowanie po stronie klienta dla aplikacji .NET, włącz diagnostykę .NET w pliku konfiguracyjnym aplikacji (web.config lub app.config). Zobacz [Rejestrowanie po stronie klienta za pomocą biblioteki klienta magazynu .NET](https://msdn.microsoft.com/library/azure/dn782839.aspx) i [rejestrowania po stronie klienta za pomocą microsoft azure storage SDK dla języka Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) na MSDN, aby uzyskać szczegółowe informacje.

Dziennik po stronie klienta zawiera szczegółowe informacje o tym, jak klient przygotowuje żądanie i odbiera i przetwarza odpowiedź.

Biblioteka klienta magazynu przechowuje dane dziennika po stronie klienta w lokalizacji określonej w pliku konfiguracyjnym aplikacji (web.config lub app.config).

### <a name="collect-a-network-trace"></a>Zbieranie śledzenia sieci

Analizator komunikatów służy do zbierania śledzenia sieci HTTP/HTTPS, gdy aplikacja kliencka jest uruchomiona. Analizator wiadomości używa [Fiddler](https://www.telerik.com/fiddler) na zapleczu. Przed pobraniem śledzenia sieci zaleca się skonfigurowanie fiddler do rejestrowania niezaszyfrowanego ruchu HTTPS:

1. Zainstaluj [Fiddler](https://www.telerik.com/download/fiddler).
2. Uruchom Skrzypek.
3. Wybierz **narzędzia | Opcje skrzypka**.
4. W oknie dialogowym Opcje upewnij się, że wybrano opcję **Capture HTTPS CONNECTs** i **Decrypt HTTPS Traffic,** jak pokazano poniżej.

![Konfigurowanie opcji fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

W samouczku należy najpierw zebrać i zapisać śledzenie sieci w analizatorze wiadomości, a następnie utworzyć sesję analizy w celu przeanalizowania śledzenia i dzienników. Aby zebrać śledzenie sieci w analizatorze wiadomości:

1. W analizatorze komunikatów wybierz pozycję **Plik | Szybkie śledzenie | Niezaszyfrowany protokół HTTPS**.
2. Śledzenie rozpocznie się natychmiast. Wybierz **zatrzymaj,** aby zatrzymać śledzenie, dzięki czemu możemy skonfigurować go do śledzenia tylko ruchu magazynu.
3. Wybierz **edytuj,** aby edytować sesję śledzenia.
4. Wybierz **łącze Konfiguruj** po prawej stronie dostawcy **ETW Firmy Microsoft-Pef-WebProxy.**
5. W oknie dialogowym **Ustawienia zaawansowane** kliknij kartę **Dostawca.**
6. W polu **Filtr nazwy hosta** określ punkty końcowe magazynu oddzielone spacjami. Na przykład można określić punkty końcowe w następujący sposób; zmień `storagesample` nazwę konta magazynu:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Zamknij okno dialogowe i kliknij przycisk **Uruchom ponownie,** aby rozpocząć zbieranie śledzenia z filtrem nazwy hosta w miejscu, tak aby tylko ruch sieciowy usługi Azure Storage został uwzględniony w śledzeniu.

> [!NOTE]
> Po zakończeniu zbierania śledzenia sieci zdecydowanie zaleca się przywrócenie ustawień, które mogły zostać zmienione w języku Fiddler w celu odszyfrowania ruchu HTTPS. W oknie dialogowym Opcje skrzypka usuń zaznaczenie pól wyboru **Capture HTTPS CONNECTs** i **Odszyfruj ruch HTTPS.**

Aby uzyskać więcej [informacji,](https://technet.microsoft.com/library/jj674819.aspx) zobacz Korzystanie z funkcji śledzenia sieci w sieci Technet.

## <a name="review-metrics-data-in-the-azure-portal"></a>Przeglądanie danych metryk w witrynie Azure portal

Po uruchomieniu aplikacji przez pewien czas można przejrzeć wykresy metryki, które pojawiają się w [witrynie Azure portal,](https://portal.azure.com) aby obserwować, jak usługa została wykonania.

Najpierw przejdź do konta magazynu w witrynie Azure portal. Domyślnie wykres monitorowania z metryką **procentową sukcesu** jest wyświetlany w bloku konta. Jeśli wcześniej zmodyfikowałeś wykres, aby wyświetlić różne dane, dodaj metrykę **procentową sukcesu.**

Zobaczysz teraz **procent sukcesu** na wykresie monitorowania wraz z innymi metrykami, które zostały dodane. W scenariuszu będziemy badać dalej, analizując dzienniki w Analizatorze wiadomości, wskaźnik sukcesu procent jest nieco poniżej 100%.

Aby uzyskać więcej informacji na temat dodawania i dostosowywania wykresów metryk, zobacz [Dostosowywanie wykresów metryk](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Może upłynąć trochę czasu, aby dane metryki pojawiają się w witrynie Azure portal po włączeniu metryki magazynu. Dzieje się tak, ponieważ metryki godzinowe dla poprzedniej godziny nie są wyświetlane w witrynie Azure portal, dopóki nie upłynie bieżąca godzina. Ponadto metryki minut nie są obecnie wyświetlane w witrynie Azure portal. W zależności od tego, kiedy włączysz metryki, może upłynąć do dwóch godzin, aby wyświetlić dane metryki.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Kopiowanie dzienników serwera do katalogu lokalnego za pomocą programu AzCopy

Usługa Azure Storage zapisuje dane dziennika serwera w obiektach blob, podczas gdy metryki są zapisywane w tabelach. Obiekty BLOB dziennika są dostępne `$logs` w dobrze znanym kontenerze dla konta magazynu. Obiekty BLOB dziennika są nazywane hierarchicznie według roku, miesiąca, dnia i godziny, dzięki czemu można łatwo zlokalizować zakres czasu, który chcesz zbadać. Na przykład na `storagesample` koncie kontener dla obiektów blob dziennika dla 01/02/2015, od `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`8-9 rano, jest . Poszczególne obiekty blob w tym kontenerze są nazywane sekwencyjnie, począwszy `000000.log`od .

Za pomocą narzędzia wiersza polecenia AzCopy można pobrać te pliki dziennika po stronie serwera do wybranej lokalizacji na komputerze lokalnym. Na przykład można użyć następującego polecenia, aby pobrać pliki dziennika dla operacji obiektów blob, które `C:\Temp\Logs\Server`miały miejsce 2 stycznia 2015 r. do folderu; zastąp `<storageaccountname>` nazwą konta magazynu:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

AzCopy jest dostępny do pobrania na stronie [Pobieranie platformy Azure.](https://azure.microsoft.com/downloads/) Aby uzyskać szczegółowe informacje na temat korzystania z programu AzCopy, zobacz [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md).

Aby uzyskać dodatkowe informacje dotyczące pobierania dzienników po stronie serwera, zobacz [Pobieranie danych dziennika rejestrowania magazynu](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Analizowanie danych dziennika za pomocą analizatora wiadomości firmy Microsoft

Microsoft Message Analyzer to narzędzie do przechwytywania, wyświetlania i analizowania ruchu obsługi wiadomości protokołu, zdarzeń i innych komunikatów systemowych lub aplikacji w scenariuszach rozwiązywania problemów i diagnostyki. Analizator wiadomości umożliwia również ładowanie, agregowanie i analizowanie danych z plików dziennika i zapisanych śledzenia. Aby uzyskać więcej informacji na temat analizatora komunikatów, zobacz [Przewodnik obsługi analizatora komunikatów](https://technet.microsoft.com/library/jj649776.aspx)firmy Microsoft .

Analizator komunikatów zawiera zasoby usługi Azure Storage, które ułatwią analizowanie dzienników serwera, klienta i sieci. W tej sekcji omówimy sposób korzystania z tych narzędzi w celu rozwiązania problemu niskiego procentu sukcesu w dziennikach magazynu.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Pobieranie i instalowanie analizatora komunikatów i zasobów usługi Azure Storage

1. Pobierz [analizator wiadomości](https://www.microsoft.com/download/details.aspx?id=44226) z Centrum pobierania firmy Microsoft i uruchom instalator.
2. Uruchom analizator komunikatów.
3. Z menu **Narzędzia** wybierz polecenie **Menedżer zasobów**. W oknie dialogowym **Menedżer zasobów** wybierz pozycję **Pobrane**, a następnie filtruj w **usłudze Azure Storage**. Zobaczysz zasoby usługi Azure Storage, jak pokazano na rysunku poniżej.
4. Kliknij **pozycję Synchronizuj wszystkie wyświetlane elementy,** aby zainstalować zasoby usługi Azure Storage. Dostępne aktywa obejmują:
   * **Reguły kolorów usługi Azure Storage:** Reguły kolorów usługi Azure Storage umożliwiają definiowanie specjalnych filtrów, które używają stylów kolorów, tekstu i czcionek do wyróżniania wiadomości zawierających określone informacje w śledzeniu.
   * **Wykresy usługi Azure Storage:** Wykresy usługi Azure Storage są wstępnie zdefiniowanymi wykresami, które rejestrują dane serwera. Należy zauważyć, że aby korzystać z wykresów usługi Azure Storage w tej chwili, można załadować tylko dziennik serwera do analysis grid.
   * **Analizatory usługi Azure Storage:** Analizatory usługi Azure Storage analizują dzienniki klienta, serwera i protokołu HTTP usługi Azure Storage w celu wyświetlenia ich w siatce analizy.
   * **Filtry usługi Azure Storage:** Filtry usługi Azure Storage są wstępnie zdefiniowanymi kryteriami, których można użyć do wykonywania zapytań o dane w siatce analizy.
   * **Układy widoku usługi Azure Storage:** Układy widoku usługi Azure Storage są wstępnie zdefiniowanymi układami kolumn i grupowania w siatce analizy.
5. Uruchom ponownie analizator komunikatów po zainstalowaniu zasobów.

![Menedżer zasobów analizatora komunikatów](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Zainstaluj wszystkie zasoby usługi Azure Storage wyświetlane na potrzeby tego samouczka.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Importowanie plików dziennika do analizatora wiadomości

W analizatorze wiadomości firmy Microsoft można zaimportować wszystkie zapisane pliki dziennika (po stronie serwera, po stronie klienta i sieci) do jednej sesji w analizatorze wiadomości firmy Microsoft.

1. W menu **Plik** w analizatorze wiadomości firmy Microsoft kliknij polecenie **Nowa sesja**, a następnie kliknij polecenie **Pusta sesja**. W oknie dialogowym **Nowa sesja** wprowadź nazwę sesji analizy. W panelu **Szczegóły sesji** kliknij przycisk **Pliki.**
2. Aby załadować dane śledzenia sieci generowane przez analizator wiadomości, kliknij **pozycję Dodaj pliki**, przejdź do lokalizacji, w której plik matp został zapisany z sesji śledzenia sieci Web, wybierz plik .matp i kliknij przycisk **Otwórz**.
3. Aby załadować dane dziennika po stronie serwera, kliknij **przycisk Dodaj pliki**, przejdź do lokalizacji, w której pobrano dzienniki po stronie serwera, wybierz pliki dziennika dla zakresu czasu, który chcesz przeanalizować, a następnie kliknij przycisk **Otwórz**. Następnie w panelu **Szczegóły sesji** ustaw z listy **rozwijanej Konfiguracja dziennika tekstu** dla każdego pliku dziennika po stronie serwera na **AzureStorageLog,** aby upewnić się, że analizator wiadomości firmy Microsoft może poprawnie przeanalizować plik dziennika.
4. Aby załadować dane dziennika po stronie klienta, kliknij **przycisk Dodaj pliki**, przejdź do lokalizacji, w której zapisano dzienniki po stronie klienta, wybierz pliki dziennika, które chcesz przeanalizować, a następnie kliknij przycisk **Otwórz**. Następnie w panelu **Szczegóły sesji** ustaw listę **rozwijaną Konfiguracja dziennika tekstu** dla każdego pliku dziennika po stronie klienta na **AzureStorageClientDotNetV4,** aby upewnić się, że analizator wiadomości firmy Microsoft może poprawnie analizować plik dziennika.
5. Kliknij **przycisk Start** w oknie dialogowym Nowa **sesja,** aby załadować i przeanalizować dane dziennika. Dane dziennika są wyświetlane w siatce analizy analizatora komunikatów.

Na poniższej ilustracji przedstawiono przykładową sesję skonfigurowaną z plikami dziennika śledzenia serwera, klienta i sieci.

![Konfigurowanie sesji analizatora komunikatów](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Należy zauważyć, że analizator komunikatów ładuje pliki dziennika do pamięci. Jeśli masz duży zestaw danych dziennika, należy filtrować go w celu uzyskania najlepszej wydajności z analizatora wiadomości.

Najpierw określ ramy czasowe, które są zainteresowane przeglądem, i zachowaj ten przedział czas y tak mały, jak to możliwe. W wielu przypadkach będziesz chciał przejrzeć co najwyżej okres minut lub godzin. Zaimportuj najmniejszy zestaw dzienników, które mogą spełnić Twoje potrzeby.

Jeśli nadal masz dużą ilość danych dziennika, możesz określić filtr sesji, aby filtrować dane dziennika przed załadowaniem. W polu **Filtr sesji** wybierz przycisk **Biblioteka,** aby wybrać wstępnie zdefiniowany filtr; na przykład wybierz globalny **filtr czasu I** z filtrów usługi Azure Storage, aby filtrować w przedziale czasu. Następnie można edytować kryteria filtrowania, aby określić początkową i końcową sygnaturę czasową dla interwału, który ma być widoczny. Można również filtrować na określony kod stanu; na przykład można załadować tylko wpisy dziennika, gdzie kod stanu jest 404.

Aby uzyskać więcej informacji na temat importowania danych dziennika do analizatora wiadomości firmy Microsoft, zobacz [Pobieranie danych wiadomości](https://technet.microsoft.com/library/dn772437.aspx) w witrynie TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Użyj identyfikatora żądania klienta, aby skorelować dane pliku dziennika

Biblioteka klienta usługi Azure Storage automatycznie generuje unikatowy identyfikator żądania klienta dla każdego żądania. Ta wartość jest zapisywana w dzienniku klienta, dzienniku serwera i śledzenia sieci, dzięki czemu można go używać do skorelowania danych we wszystkich trzech dziennikach w analizatorze komunikatów. Zobacz [Identyfikator żądania klienta, aby](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) uzyskać dodatkowe informacje na temat identyfikatora żądania klienta.

W poniższych sekcjach opisano sposób używania wstępnie skonfigurowanych i niestandardowych widoków układu do skorelowania i grupowania danych na podstawie identyfikatora żądania klienta.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Wybieranie układu widoku do wyświetlenia w siatce analizy

Zasoby magazynu dla analizatora komunikatów obejmują układy widoku usługi Azure Storage, które są wstępnie skonfigurowane widoki, których można użyć do wyświetlania danych z przydatnych grupowania i kolumn dla różnych scenariuszy. Można również utworzyć niestandardowe układy widoku i zapisać je do ponownego użycia.

Na poniższej ilustracji przedstawiono menu **Widok układu** dostępnego po wybraniu opcji **Widok układu** na wstążce paska narzędzi. Układy widoku usługi Azure Storage są zgrupowane w obszarze węzła **usługi Azure Storage** w menu. Możesz wyszukiwać `Azure Storage` w polu wyszukiwania, aby filtrować tylko w układach widoku usługi Azure Storage. Możesz również wybrać gwiazdkę obok układu widoku, aby uczynić ją ulubioną i wyświetlić ją w górnej części menu.

![Menu Wyświetl układ](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Na początek wybierz opcję **Pogrupowane według ClientRequestID i Module**. Ten system widoku grupuje dane dziennika ze wszystkich trzech dzienników najpierw według identyfikatora żądania klienta, a następnie przez plik dziennika źródłowego (lub **moduł** w analizatorze komunikatów). W tym widoku można przejść do szczegółów w określonym identyfikatorze żądania klienta i wyświetlić dane ze wszystkich trzech plików dziennika dla tego identyfikatora żądania klienta.

Na poniższej ilustracji przedstawiono ten widok układu zastosowany do przykładowych danych dziennika z wyświetlonym podzbiorem kolumn. Widać, że dla określonego identyfikatora żądania klienta siatka analysis grid wyświetla dane z dziennika klienta, dziennika serwera i śledzenia sieci.

![Układ widoku usługi Azure Storage](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Różne pliki dziennika mają różne kolumny, więc gdy dane z wielu plików dziennika są wyświetlane w siatce analizy, niektóre kolumny mogą nie zawierać żadnych danych dla danego wiersza. Na przykład na powyższym obrazie wiersze dziennika klienta nie są wyświetlane żadne dane dla kolumn **Sygnatura czasowa,** **TimeElapsed**, **Source**i **Destination,** ponieważ te kolumny nie istnieją w dzienniku klienta, ale istnieją w śledzenia sieci. Podobnie kolumna **Sygnatura czasowa** wyświetla dane sygnatury czasowej z dziennika serwera, ale żadne dane nie są wyświetlane dla kolumn **TimeElapsed**, **Source**i **Destination,** które nie są częścią dziennika serwera.
>
>

Oprócz korzystania z układów widoku usługi Azure Storage można również zdefiniować i zapisać własne układy widoku. Można wybrać inne żądane pola do grupowania danych i zapisać grupowanie jako część układu niestandardowego.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Stosowanie reguł kolorów do siatki analizy

Zasoby magazynu obejmują również reguły kolorów, które oferują wizualne środki do identyfikowania różnych typów błędów w siatce analizy. Wstępnie zdefiniowane reguły kolorów mają zastosowanie do błędów HTTP, więc są wyświetlane tylko dla dziennika serwera i śledzenia sieci.

Aby zastosować reguły kolorów, wybierz pozycję **Reguły kolorów** na wstążce paska narzędzi. Zobaczysz reguły kolorów usługi Azure Storage w menu. W samouczku wybierz opcję **Błędy klienta (Kod stanu między 400 a 499)**, jak pokazano na rysunku poniżej.

![Układ widoku usługi Azure Storage](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Oprócz korzystania z reguł kolorów usługi Azure Storage można również zdefiniować i zapisać własne reguły kolorów.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Grupowanie i filtrowanie danych dziennika w celu znalezienia błędów 400 zakresów

Następnie zgrupujemy i przefiltrujemy dane dziennika, aby znaleźć wszystkie błędy w zakresie 400.

1. Znajdź kolumnę **Kod stanu** w siatce analizy, kliknij prawym przyciskiem myszy nagłówek kolumny i wybierz polecenie **Grupuj**.
2. Następnie zgrupuj w kolumnie **ClientRequestId.** Zobaczysz, że dane w siatce analizy są teraz uporządkowane według kodu stanu i identyfikatora żądania klienta.
3. Wyświetl okno narzędzia Filtr widoku, jeśli nie jest jeszcze wyświetlane. Na wstążce paska narzędzi wybierz pozycję **Tool Windows**, a następnie **Wyświetl filtr**.
4. Aby filtrować dane dziennika w celu wyświetlenia tylko 400 błędów zakresu, dodaj następujące kryteria filtru do okna **Filtr widoku** i kliknij przycisk **Zastosuj:**

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

Na poniższym rysunku przedstawiono wyniki tej grupy i filtru. Rozwijanie **ClientRequestID** pole pod grupowanie dla kodu stanu 409, na przykład pokazuje operację, która spowodowała ten kod stanu.

![Układ widoku usługi Azure Storage](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Po zastosowaniu tego filtru zobaczysz, że wiersze z dziennika klienta są wykluczone, ponieważ dziennik klienta nie zawiera kolumny **StatusCode.** Na początek przejrzymy dzienniki śledzenia serwera i sieci, aby zlokalizować 404 błędy, a następnie powrócimy do dziennika klienta, aby sprawdzić operacje klienta, które do nich doprowadziły.

> [!NOTE]
> Można filtrować w **kolumnie StatusCode** i nadal wyświetlać dane ze wszystkich trzech dzienników, w tym dziennika klienta, jeśli dodasz wyrażenie do filtru, który zawiera wpisy dziennika, gdzie kod stanu jest null. Aby skonstruować to wyrażenie filtru, należy użyć:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Ten filtr zwraca wszystkie wiersze z dziennika klienta i tylko wiersze z dziennika serwera i dziennika HTTP, gdzie kod stanu jest większy niż 400. Jeśli zastosujesz go do układu widoku pogrupowanego według identyfikatora żądania klienta i modułu, można wyszukiwać lub przewijać wpisy dziennika, aby znaleźć te, w których są reprezentowane wszystkie trzy dzienniki.

### <a name="filter-log-data-to-find-404-errors"></a>Filtrowanie danych dziennika w celu znalezienia 404 błędów

Zasoby magazynu obejmują wstępnie zdefiniowane filtry, których można użyć do zawężenia danych dziennika w celu znalezienia błędów lub trendów, których szukasz. Następnie zastosujemy dwa wstępnie zdefiniowane filtry: jeden, który filtruje dzienniki śledzenia serwera i sieci pod kątem 404 błędów, a drugi filtruje dane w określonym zakresie czasu.

1. Wyświetl okno narzędzia Filtr widoku, jeśli nie jest jeszcze wyświetlane. Na wstążce paska narzędzi wybierz pozycję **Tool Windows**, a następnie **Wyświetl filtr**.
2. W oknie Filtr widoku wybierz pozycję `Azure Storage` **Biblioteka**i wyszukaj, aby znaleźć filtry usługi Azure Storage. Wybierz filtr **dla 404 (nie znaleziono) wiadomości we wszystkich dziennikach**.
3. Ponownie wyświetl menu **Biblioteka,** a następnie znajdź i wybierz **globalny filtr czasu**.
4. Edytuj sygnatury czasowe wyświetlane w filtrze do zakresu, który chcesz wyświetlić. Pomoże to zawęzić zakres danych do analizy.
5. Filtr powinien wyglądać podobnie do poniższego przykładu. Kliknij **przycisk Zastosuj,** aby zastosować filtr do siatki analizy.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Układ widoku usługi Azure Storage](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analizowanie danych dziennika

Teraz, po zgrupowaniu i przefiltrowaniu danych, można sprawdzić szczegóły poszczególnych żądań, które wygenerowały 404 błędy. W bieżącym układzie widoku dane są pogrupowane według identyfikatora żądania klienta, a następnie według źródła dziennika. Ponieważ filtrujemy żądania, w których pole Kod stanu zawiera 404, zobaczymy tylko dane śledzenia serwera i sieci, a nie dane dziennika klienta.

Na rysunku poniżej przedstawiono określone żądanie, gdzie Operacja Pobierz obiekt blob dał 404, ponieważ obiekt blob nie istnieje. Należy pamiętać, że niektóre kolumny zostały usunięte z widoku standardowego w celu wyświetlenia odpowiednich danych.

![Filtrowane dzienniki śledzenia serwera i sieci](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Następnie skorelujemy ten identyfikator żądania klienta z danymi dziennika klienta, aby zobaczyć, jakie akcje klient podejmował, gdy wystąpił błąd. Można wyświetlić nowy widok siatki analizy dla tej sesji, aby wyświetlić dane dziennika klienta, który otwiera się na drugiej karcie:

1. Najpierw skopiuj wartość pola **ClientRequestId** do schowka. Można to zrobić, wybierając jeden wiersz, lokalizując pole **ClientRequestId,** klikając prawym przyciskiem myszy na wartość danych i wybierając **polecenie Kopiuj "ClientRequestId"**.
2. Na wstążce paska narzędzi wybierz pozycję **Nowa przeglądarka**, a następnie wybierz pozycję **Siatka analizy,** aby otworzyć nową kartę. Nowa karta zawiera wszystkie dane w plikach dziennika bez grupowania, filtrowania lub reguł kolorów.
3. Na wstążce paska narzędzi wybierz pozycję **Wyświetl układ**, a następnie wybierz pozycję Wszystkie kolumny klienta **platformy .NET** w sekcji **Usługi Azure Storage.** Ten układ widoku zawiera dane z dziennika klienta, a także dzienniki śledzenia serwera i sieci. Domyślnie jest sortowany w kolumnie **MessageNumber.**
4. Następnie wyszukaj w dzienniku klienta identyfikator żądania klienta. Na wstążce paska narzędzi wybierz pozycję **Znajdź wiadomości**, a następnie określ filtr niestandardowy w identyfikatorze żądania klienta w polu **Znajdź.** Użyj tej składni dla filtru, określając własny identyfikator żądania klienta:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

Analizator wiadomości lokalizuje i wybiera pierwszy wpis dziennika, w którym kryteria wyszukiwania są zgodne z identyfikatorem żądania klienta. W dzienniku klienta istnieje kilka wpisów dla każdego identyfikatora żądania klienta, więc można pogrupować je w polu **ClientRequestId,** aby ułatwić ich wszystkie razem. Poniższy rysunek przedstawia wszystkie komunikaty w dzienniku klienta dla określonego identyfikatora żądania klienta.

![Dziennik klienta z 404 błędami](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Korzystając z danych wyświetlanych w układach widoku na tych dwóch kartach, można analizować dane żądania, aby ustalić, co mogło być przyczyną błędu. Można również spojrzeć na żądania, które poprzedziły ten jeden, aby sprawdzić, czy poprzednie zdarzenie mogło doprowadzić do błędu 404. Na przykład można przejrzeć wpisy dziennika klienta poprzedzające ten identyfikator żądania klienta, aby ustalić, czy obiekt blob mógł zostać usunięty lub czy błąd był spowodowany wywołaniem przez aplikację kliencką interfejsu API CreateIfNotExists w kontenerze lub obiekcie blob. W dzienniku klienta można znaleźć adres obiektu blob w polu **Opis;** w dziennikach śledzenia serwera i sieci te informacje są wyświetlane w polu **Podsumowanie.**

Po poznaniu adresu obiektu blob, który dał błąd 404, można zbadać dalej. Jeśli przeszukujesz wpisy dziennika w poszukiwaniu innych komunikatów skojarzonych z operacjami na tym samym obiekcie blob, można sprawdzić, czy klient wcześniej usunął jednostkę.

## <a name="analyze-other-types-of-storage-errors"></a>Analizowanie innych typów błędów magazynu

Teraz, gdy jesteś zaznajomiony z za pomocą Analizatora wiadomości do analizowania danych dziennika, można analizować inne typy błędów przy użyciu układów widoku, reguły kolorów i wyszukiwanie/filtrowanie. W poniższych tabelach wymieniono niektóre problemy, które mogą wystąpić, oraz kryteria filtrowania, których można użyć do ich zlokalizowania. Aby uzyskać więcej informacji na temat konstruowania filtrów i języka filtrowania analizatora wiadomości, zobacz [Filtrowanie danych wiadomości](https://technet.microsoft.com/library/jj819365.aspx).

| Aby zbadać... | Użyj wyrażenia filtru... | Wyrażenie ma zastosowanie do dziennika (klient, serwer, sieć, wszystkie) |
| --- | --- | --- |
| Nieoczekiwane opóźnienia w dostarczaniu wiadomości w kolejce |AzureStorageClientDotNetV4.Description zawiera "Ponów próbę wykonania nieudanej operacji." |Klient |
| Wzrost HTTP w PercentThrottlingError |Http. Response.StatusCode == 500 &#124;&#124; HTTP. Odpowiedź.StatusCode == 503 |Network (Sieć) |
| Wzrost percenttimeoutError |Http. Odpowiedź.StatusCode == 500 |Network (Sieć) |
| Wzrost PercentTimeoutError (wszystkie) |*Kod stanu == 500 |Wszystkie |
| Wzrost percentnetworkerror |AzureStorageClientDotNetV4.EventLogEntry.Level < 2 |Klient |
| Komunikaty HTTP 403 (Zabronione) |Http. Odpowiedź.StatusCode == 403 |Network (Sieć) |
| Komunikaty HTTP 404 (Nie znaleziono) |Http. Odpowiedź.StatusCode == 404 |Network (Sieć) |
| 404 (wszystkie) |*Kod stanu == 404 |Wszystkie |
| Problem z autoryzacją sygnatury dostępu współdzielonego |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Network (Sieć) |
| Komunikaty HTTP 409 (Konflikt) |Http. Odpowiedź.StatusCode == 409 |Network (Sieć) |
| 409 (wszystkie) |*Kod stanu == 409 |Wszystkie |
| Niskie procentySukces lub raporty analityczne wpisy dziennika mają operacje ze stanem transakcji ClientOtherErrors |AzureStorageLog.RequestStatus == "ClientOtherError" |Serwer |
| Ostrzeżenie o nagle |((AzureStorageLog.EndToEndLatencyMS — AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) i (AzureStor pakietagelog.RequestPacketSize <1460) i (AzureStorageLog.EndToEndLatencyMS — AzureStorageLog.ServerLatencyMS >= 200) |Serwer |
| Zakres czasu w dziennikach serwera i sieci |#Timestamp >= 2014-10-20T16:36:38 i #Timestamp <= 2014-10-20T16:36:39 |Serwer, Sieć |
| Zakres czasu w dziennikach serwera |AzureStorageLog.Timestamp >= 2014-10-20T16:36:38 i AzureStorageLog.Timestamp <= 2014-10-20T16:36:39 |Serwer |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów ze scenariuszami end-to-end w usłudze Azure Storage, zobacz następujące zasoby:

* [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Analityka magazynu](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorowanie konta magazynu w witrynie Azure Portal](storage-monitor-storage-account.md)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Instrukcja obsługi narzędzia Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
