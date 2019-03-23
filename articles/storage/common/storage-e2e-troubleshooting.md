---
title: Rozwiązywanie problemów z usługi Azure Storage za pomocą diagnostyki & Message Analyzer | Dokumentacja firmy Microsoft
description: Samouczek, demonstrując end-to-end Rozwiązywanie problemów przy użyciu usługi Azure Storage Analytics, narzędzia AzCopy i analizatora komunikatów firmy Microsoft
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f88a560d4fa819a055534530ddc0862e4aa330fe
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351885"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Rozwiązywanie problemów z end-to-end, przy użyciu metryk usługi Azure Storage i rejestrowania, narzędzia AzCopy i analizatora komunikatów
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnozowanie i rozwiązywanie problemów z jest umiejętności do tworzenia i obsługi aplikacji klienta za pomocą usługi Microsoft Azure Storage przy użyciu klucza. Ze względu na Rozproszony charakter aplikacji platformy Azure diagnozowanie i rozwiązywanie błędów i problemów z wydajnością może być bardziej skomplikowane niż w tradycyjnych środowiskach.

W tym samouczku pokażemy, jak identyfikować pewne błędy, które mogą mieć wpływ na wydajność i rozwiązywanie tych problemów z end-to-end za pomocą narzędzi dostarczonych przez firmę Microsoft i usługi Azure Storage w celu optymalizacji aplikacji klienckiej.

Ten samouczek zawiera praktyczne eksploracji rozwiązywaniu end-to-end. Aby uzyskać szczegółowy przewodnik koncepcyjny do rozwiązywania problemów z aplikacjami usługi Azure storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Narzędzia do rozwiązywania problemów aplikacji usługi Azure Storage
Rozwiązywać problemy z aplikacji klienckich za pomocą usługi Microsoft Azure Storage, różnych narzędzi służy do określenia, kiedy wystąpił problem, i co może być przyczyną tego problemu. Do tych narzędzi należą:

* **Usługa Azure Storage Analytics**. [Analizy usługi Azure Storage](/rest/api/storageservices/Storage-Analytics) udostępnia metryki i rejestrowanie dla usługi Azure Storage.

  * **Metryki magazynu** śledzenie metryk transakcji i metryki pojemności konta magazynu. Przy użyciu metryk, należy określić, jak aplikacja działa zgodnie z szeregu różnych miar. Zobacz [schemat tabeli metryk usługi Analytics magazynu](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) Aby uzyskać więcej informacji o typach metryki śledzone przez analityka magazynu.
  * **Rejestrowanie magazynu** rejestruje każdego żądania do usługi Azure Storage do dziennika po stronie serwera. Dziennik śledzenia szczegółowe dane dla każdego żądania, w tym operacji wykonywanych, stan operacji, a informacje o opóźnieniu. Zobacz [Format dziennika analizy magazynu](/rest/api/storageservices/Storage-Analytics-Log-Format) Aby uzyskać więcej informacji na temat danych żądań i odpowiedzi, które są zapisywane w dziennikach przez analityka magazynu.

* **Witryna Azure portal**. Rejestrowanie i metryki można skonfigurować dla konta magazynu w [witryny Azure portal](https://portal.azure.com). Można również wyświetlać wykresy i diagramy, które pokazują, jak działa aplikacja wraz z upływem czasu i Konfigurowanie alertów, aby otrzymywać powiadomienia, jeśli Twoja aplikacja działa inaczej niż oczekiwano dla określonej metryki.

    Zobacz [monitorowania na koncie magazynu w witrynie Azure portal](storage-monitor-storage-account.md) informacji o konfigurowaniu monitorowania w witrynie Azure portal.
* **AzCopy**. Dzienniki serwera usługi Azure Storage są przechowywane jako obiekty BLOB, aby można było używać narzędzia AzCopy do kopiowania obiektów blob dziennika do katalogu lokalnego do analizy przy użyciu programu Microsoft Message Analyzer. Zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md) Aby uzyskać więcej informacji na temat narzędzia AzCopy.
* **Narzędzie Microsoft Message Analyzer**. Message Analyzer jest narzędziem, które korzysta z plików dziennika i wyświetla dane dzienników w formacie wizualny, który ułatwia filter, search i dane dziennika dla grupy w zestawach użyteczne, służących do analizowania błędów i problemów z wydajnością. Zobacz [komunikatów analizatora działających w Przewodniku dotyczącym Microsoft](https://technet.microsoft.com/library/jj649776.aspx) Aby uzyskać więcej informacji na temat analizatora komunikatów.

## <a name="about-the-sample-scenario"></a>Przykładowy scenariusz — informacje
W tym samouczku zostaną omówione scenariusz, gdzie metryk usługi Azure Storage Określa niski procent powodzeń dla aplikacji, która wymaga usługi Azure storage. Metryka współczynnik niski procent sukcesu (wyświetlana jako **PercentSuccess** w [witryny Azure portal](https://portal.azure.com) w tabulky metrik) śledzi operacje, które powiedzie się, ale które zwracają kod stanu HTTP, która jest większa niż 299. W plikach dziennika magazynu po stronie serwera, te operacje są rejestrowane ze stanem transakcji **ClientOtherErrors**. Aby uzyskać więcej szczegółów na temat metryki niski procent sukcesu zobacz [metryki pokazują PercentSuccess niski lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operacji na platformie Azure Storage może zwrócić kody stanu HTTP większa niż 299 jako część ich normalnej funkcjonalności. Jednak te błędy, w niektórych przypadkach wskazują, że można zoptymalizować aplikację kliencką w celu zwiększenia wydajności.

W tym scenariuszu firma Microsoft będzie należy wziąć pod uwagę wskaźnik niski procent sukcesu jako niczego poniżej 100%. Możesz jednak wybrać inny poziom metryki, zgodnie z potrzebami. Firma Microsoft zaleca podczas testowania aplikacji, ustanowienie tolerancji punktu odniesienia dla Twojego kluczowe metryki wydajności. Na przykład można określić, na podstawie badań, że aplikacji powinny mieć spójne procent powodzeń 90% lub 85%. Jeśli dane metryk wskazuje, że aplikacja jest odejście od tej liczby, a następnie badania, co może powodować wzrost.

W naszym scenariuszu próbki gdy został określony, to pomiar współczynnika procent sukcesu poniżej 100%, będziemy sprawdzać dzienniki Aby znaleźć błędy, które odnoszą się do metryk i ich używać, aby ustalić przyczynę wolniejszym tempie procent sukcesu. Omówimy szczegółowo błędy w zakresie 400. Następnie firma Microsoft będzie dokładniej zbadać błędów 404 (nie znaleziono).

### <a name="some-causes-of-400-range-errors"></a>Niektóre przyczyny błędów 400 spoza zakresu
Poniższe przykłady przedstawiono niektóre spośród błędy zakresu 400 żądań względem usługi Azure Blob Storage i ich możliwe przyczyny. Żadnego z tych błędów, jak również błędy w zakresie 300 i 500 zakresu, może przyczynić się do szybkości niski procent sukcesu.

Należy pamiętać, że na poniższej liście wymieniono są dalekie od wartości ukończone. Zobacz [stanu i kodów błędów](https://msdn.microsoft.com/library/azure/dd179382.aspx) w witrynie MSDN, aby uzyskać szczegółowe informacje o ogólnych błędach usługi Azure Storage i o błędach do różnych usług magazynu.

**Przykłady kodu 404 (nie znaleziono) stanu**

Występuje, gdy operacja odczytu względem kontenera lub obiektu blob nie powiedzie się, ponieważ nie znaleziono obiektu blob lub kontenera.

* Występuje, gdy kontener lub obiekt blob został usunięty przez innego klienta przed tym żądaniem.
* Występuje, jeśli używasz wywołanie interfejsu API, który tworzy po sprawdzeniu, czy istnieje kontenera lub obiektu blob. Interfejsy API CreateIfNotExists wywołania HEAD najpierw pod kątem istnienia kontenera lub obiektu blob Jeśli nie istnieje, zwracany jest błąd 404, a następnie drugie PUT następuje wywołanie do zapisu kontenera lub obiektu blob.

**Stan kodu 409 (konflikt) przykłady**

* Występuje, gdy użyjesz interfejsu API tworzenia do utworzenia nowego kontenera lub obiektu blob, bez uprzedniego sprawdzania pod istnienie i kontenera lub obiektu blob o tej nazwie już istnieje.
* Występuje, gdy kontener jest usuwana, a następnie spróbuj utworzyć nowy kontener o takiej samej nazwie, przed wykonaniem operacji usuwania.
* Występuje, gdy należy określić w ramach kontenera lub obiektu blob dzierżawy i jest już obecny dzierżawy.

**Kod stanu 412 (niepowodzenie warunku wstępnego) przykłady**

* Występuje, gdy nie został spełniony warunek określony przez warunkowe nagłówka.
* Występuje, gdy określono identyfikator dzierżawy jest niezgodny z Identyfikatorem dzierżawy, kontenera lub obiektu blob.

## <a name="generate-log-files-for-analysis"></a>Generowanie do analizy pliki dziennika
W tym samouczku użyjemy analizatora komunikatów do pracy z trzech różnych typów plików dziennika, mimo że można wybrać do pracy z którymkolwiek z tych:

* **Dziennika serwera**, którym zostanie utworzony po włączeniu rejestrowania usługi Azure Storage. Dziennik serwera zawiera dane dotyczące każdej operacji wywoływana względem jednej z usług Azure Storage — obiektów blob, kolejka, tabela i plik. Dziennik serwera wskazuje, która operacja została wywołana i jaki kod stanu został zwrócony, a także inne szczegóły dotyczące żądania i odpowiedzi.
* **Dziennika klienta .NET**, którym zostanie utworzony po włączeniu rejestrowania po stronie klienta z poziomu aplikacji .NET. Dziennik klienta zawiera szczegółowe informacje o kliencie przygotowuje żądania oraz odbiera i przetwarza odpowiedź.
* **Dziennika śledzenia sieci HTTP**, który zbiera dane na danych żądań i odpowiedzi HTTP/HTTPS, w tym dla operacji w ramach usługi Azure Storage. W tym samouczku polega na wygenerowaniu śledzenia sieci za pomocą analizatora komunikatów.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurowanie metryk i rejestrowania po stronie serwera
Najpierw musimy skonfigurować rejestrowanie usługi Azure Storage i metryki, więc, że mamy dane ze strony usługi do analizy. Rejestrowanie i metryki w na różne sposoby — można skonfigurować za pomocą [witryny Azure portal](https://portal.azure.com), przy użyciu programu PowerShell, lub programowo. Zobacz [włączyć metryki](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) i [włączyć rejestrowanie](storage-analytics-logging.md#enable-storage-logging) szczegółowe informacje na temat konfigurowania, rejestrowanie i metryki.

### <a name="configure-net-client-side-logging"></a>Konfigurowanie rejestrowania po stronie klienta platformy .NET
Aby skonfigurować rejestrowanie po stronie klienta dla aplikacji platformy .NET, Włącz diagnostykę platformy .NET w pliku konfiguracyjnym aplikacji (web.config lub app.config). Zobacz [klienta rejestrowanie za pomocą biblioteki klienckiej magazynu .NET](https://msdn.microsoft.com/library/azure/dn782839.aspx) i [klienta rejestrowanie za pomocą zestawu Microsoft Azure Storage SDK dla języka Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) w witrynie MSDN, aby uzyskać szczegółowe informacje.

Dziennik klienta zawiera szczegółowe informacje o kliencie przygotowuje żądania oraz odbiera i przetwarza odpowiedź.

Biblioteki klienta usługi Storage przechowuje dane dziennika po stronie klienta w lokalizacji określonej w pliku konfiguracji aplikacji (web.config lub app.config).

### <a name="collect-a-network-trace"></a>Zbierać dane śledzenia sieci
Message Analyzer umożliwiają zbieranie śledzenia sieci HTTP/HTTPS, gdy Twoja aplikacja kliencka jest uruchomiona. Message Analyzer używa [Fiddler](https://www.telerik.com/fiddler) na zapleczu. Przed zbierane śledzenia sieci zaleca się skonfigurowanie programu Fiddler, aby zarejestrować nieszyfrowanego ruchu HTTPS:

1. Zainstaluj [Fiddler](https://www.telerik.com/download/fiddler).
2. Uruchamianie programu Fiddler.
3. Wybierz **narzędzia | Opcje programu fiddler**.
4. W oknie dialogowym Opcje, upewnij się, że **nawiązuje połączenie z protokołu HTTPS przechwytywania** i **odszyfrować ruch HTTPS** są zaznaczone, jak pokazano poniżej.

![Skonfiguruj opcje programu Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Samouczek Zbieraj najpierw zapisać w analizatora komunikatów śledzenia sieci, a następnie utworzyć sesję analiz można analizować śledzenia i dzienników. Aby zbierać dane śledzenia sieci w analizatora komunikatów:

1. Message Analyzer, wybierz **pliku | Szybkie śledzenia | Niezaszyfrowane HTTPS**.
2. Śledzenie rozpocznie się natychmiast. Wybierz **zatrzymać** do Zatrzymaj śledzenie, tak aby można go skonfigurować do śledzenia ruchu sieciowego magazynu tylko.
3. Wybierz **Edytuj** edytowanie sesję śledzenia.
4. Wybierz **Konfiguruj** łącza z prawej strony **WebProxy-Microsoft-Pef** dostawcy funkcji ETW.
5. W **Zaawansowane ustawienia** okno dialogowe, kliknij przycisk **dostawcy** kartę.
6. W **filtru Hostname** określ punkty końcowe usługi storage, rozdzielone spacjami. Na przykład można określić punktów końcowych sieci w następujący sposób; Zmień `storagesample` do nazwy konta magazynu:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Zamknij okno dialogowe, a następnie kliknij przycisk **ponowne uruchomienie** umożliwiającą, zbieranie śledzenia za pomocą filtru nazwy hosta w miejscu, tak aby tylko ruch sieciowy w usłudze Azure Storage znajduje się w śledzeniu.

> [!NOTE]
> Po zakończeniu zbierania ślad sieci zdecydowanie zaleca się przywrócenie ustawień, które być może zmieniono w narzędziu Fiddler do odszyfrowywania ruchu HTTPS. W oknie dialogowym Opcje programu Fiddler, usuń zaznaczenie opcji **nawiązuje połączenie z protokołu HTTPS przechwytywania** i **odszyfrować ruch HTTPS** pola wyboru.
>
>

Zobacz [korzystanie z funkcji śledzenia sieci](https://technet.microsoft.com/library/jj674819.aspx) w witrynie Technet, aby uzyskać więcej informacji.

## <a name="review-metrics-data-in-the-azure-portal"></a>Przejrzyj dane metryk w witrynie Azure portal
Gdy aplikacja została uruchomiona przez czas, przejrzenie wykresów metryk, które pojawiają się w [witryny Azure portal](https://portal.azure.com) obserwować, jaka została wydajność usługi.

Przejdź do swojego konta magazynu w witrynie Azure portal. Domyślnie monitorowanie wykres z **odsetek działań zakończonych powodzeniem** metryka jest wyświetlany w bloku konta. Jeśli zostały wcześniej zmodyfikowane wykres, aby wyświetlić różne metryki, należy dodać **odsetek działań zakończonych powodzeniem** metryki.

Zobaczysz teraz **odsetek działań zakończonych powodzeniem** na wykresie monitorowania wraz z innymi metrykami mogły zostać dodane. W tym scenariuszu, w których firma Microsoft będzie zbadać dalej przez analizowanie dzienników w Message Analyzer Częstotliwość powodzeń procentu jest nieco poniżej 100%.

Aby uzyskać szczegółowe informacje na temat dodawania i dostosowywanie wykresów metryk, zobacz [Dostosowywanie wykresów metryk](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Może potrwać kilka danych metryki są wyświetlane w witrynie Azure portal, po włączeniu metryk usługi storage. Jest to spowodowane godzinowe metryki dla poprzedniej godziny nie są wyświetlane w witrynie Azure portal, dopóki nie upłynie bieżącej godziny. Ponadto metryki minut nie są obecnie wyświetlane w witrynie Azure portal. Dlatego zależnie od tego, po włączeniu metryki, może potrwać do dwóch godzin, aby wyświetlić dane metryk.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Kopiowanie dzienników serwera do katalogu lokalnego za pomocą narzędzia AzCopy
Usługa Azure Storage zapisuje dane dziennika serwera obiektów blob, a metryki są zapisywane w tabelach. Obiekty BLOB dziennika są dostępne w dobrze znanych `$logs` kontenera konta magazynu. Obiekty BLOB dziennika są nazywane hierarchicznie przez rok, miesiąc, dzień i godzinę, dzięki czemu można łatwo zlokalizować przedział czasu, który chcesz zbadać. Na przykład w `storagesample` konto, kontener obiektów blob dziennika dla 01/02/2015, od 8 do 9 am, `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Poszczególne obiekty BLOB w tym kontenerze są nazywane kolejno, począwszy od `000000.log`.

Aby pobrać te pliki dziennika po stronie serwera na wybraną lokalizację na komputerze lokalnym, można użyć narzędzia wiersza polecenia narzędzia AzCopy. Na przykład służy następujące polecenie do pobrania plików dziennika dla operacje obiektów blob, które miały miejsce od 2 stycznia 2015 r. do folderu `C:\Temp\Logs\Server`; Zastąp `<storageaccountname>` nazwą konta magazynu i `<storageaccountkey>` swoim kluczem dostępu konta :

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
Narzędzie AzCopy jest dostępna do pobrania na [pliki do pobrania Azure](https://azure.microsoft.com/downloads/) strony. Aby uzyskać szczegółowe informacje dotyczące korzystania z narzędzia AzCopy, zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md).

Aby uzyskać dodatkowe informacje dotyczące pobierania dzienników po stronie serwera, zobacz [Pobierz rejestrowania magazynu danych dziennika](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Korzystanie z analizatora komunikatów firmy Microsoft do analizy danych dziennika
Microsoft Message Analyzer jest narzędziem do przechwytywania, wyświetlanie i analizowanie protokołu komunikatów ruchu, zdarzeń i inne komunikaty w scenariuszach rozwiązywania problemów i diagnostycznych systemu lub aplikacji. Message Analyzer również pozwala na ładowanie, agregowania i analizowania danych z dziennika i zapisane pliki śledzenia. Aby uzyskać więcej informacji na temat analizatora komunikatów, zobacz [komunikatów analizatora działających w Przewodniku dotyczącym Microsoft](https://technet.microsoft.com/library/jj649776.aspx).

Analizator komunikatów obejmuje zasoby dla usługi Azure Storage, które ułatwiają analizowanie serwera, klienta i dzienniki sieciowe w usłudze. W tej sekcji omówiono sposób używania tych narzędzi, aby rozwiązać problem niskiej procent sukcesu w dziennikach magazynu.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Pobierz i zainstaluj programy Message Analyzer i zasoby magazynu platformy Azure
1. Pobierz [Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) z Microsoft Centrum pobierania i uruchom Instalatora.
2. Uruchom analizator komunikatów.
3. Z **narzędzia** menu, wybierz opcję **Menedżer zasobów**. W **Menedżer zasobów** okno dialogowe, wybierz opcję **pliki do pobrania**, następnie filtrowanie według **usługi Azure Storage**. Widoczne są zasoby magazynu platformy Azure, jak pokazano na poniższej ilustracji.
4. Kliknij przycisk **wszystkie elementy wyświetlane synchronizacji** zainstalował zasoby magazynu platformy Azure. Dostępne zasoby obejmują:
   * **Usługa Azure Storage reguły koloru:** Reguły kolorów w usłudze Azure Storage umożliwiają definiowanie specjalne filtry, które umożliwia wyróżnianie komunikatów, które zawierają szczegółowe informacje w śladzie kolorów, tekst i style czcionek.
   * **Azure Storage Charts:** Usługa Azure wykresy magazynu są wstępnie zdefiniowane wykresów, które wykresu danych dziennika serwera. Należy pamiętać, aby korzystać z usługi Azure Storage wykresy w tej chwili, może być tylko załadowanie dziennika serwera na siatkę analizy.
   * **Azure Storage Parsers:** Parsery usługi Azure Storage przeanalizować klienta usługi Azure Storage, server i dzienniki HTTP, aby można było je wyświetlić w siatce analizy.
   * **Filtry usługi Azure Storage:** Usługa Azure filtry magazynu są wstępnie zdefiniowane kryteria, które umożliwia wyszukiwanie danych w siatce analizy.
   * **Usługa Azure Storage układów widoku:** Układów widoku w usłudze Azure Storage są układów wstępnie zdefiniowanych kolumn i grupowania w siatce analizy.
5. Po zainstalowaniu zasoby, należy ponownie uruchomić analizatora komunikatów.

![Menedżer zasobów analizatora komunikatów](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Zainstaluj wszystkie zasoby usługi Azure Storage wyświetlana na potrzeby tego samouczka.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Importowanie plików dzienników do analizatora komunikatów
Możesz zaimportować wszystkie zapisane pliki dziennika (po stronie serwera, klienta i sieci) w jednej sesji w Microsoft Message Analyzer do analizy.

1. Na **pliku** kliknij przycisk menu w programie Microsoft Message Analyzer **nowej sesji**, a następnie kliknij przycisk **puste sesji**. W **nowej sesji** okno dialogowe, wprowadź nazwę dla sesji analizy. W **szczegóły sesji** panelu, kliknij pozycję **pliki** przycisku.
2. Aby załadować dane śledzenia sieci, które są generowane przez analizatora komunikatów, kliknij **Dodaj pliki**, przejdź do lokalizacji, w którym zapisano plik .matp z sesji śledzenia sieci web, wybierz plik .matp, a następnie kliknij przycisk **Otwórz**.
3. Aby załadować dane dziennika po stronie serwera, kliknij **Dodaj pliki**, przejdź do lokalizacji, w której pobrano dzienniki po stronie serwera, wybierz pliki dziennika, aby zakres czasu, aby analizować, a następnie kliknij przycisk **Otwórz**. Następnie w **szczegóły sesji** panelu, ustaw **konfiguracji dziennika tekstowego** listy rozwijanej dla każdego pliku dziennika po stronie serwera, aby **AzureStorageLog** aby upewnić się, że Microsoft Message Analizator można poprawnie przeanalizować pliku dziennika.
4. Aby załadować dane dziennika po stronie klienta, kliknij **Dodaj pliki**, przejdź do lokalizacji, w którym zapisano dzienników po stronie klienta, wybierz pliki dziennika, które chcesz analizować, a następnie kliknij przycisk **Otwórz**. Następnie w **szczegóły sesji** panelu, ustaw **konfiguracji dziennika tekstowego** listy rozwijanej dla każdego pliku dziennika klienta, aby **AzureStorageClientDotNetV4** do upewnij się, że Microsoft Message Analyzer można poprawnie przeanalizować pliku dziennika.
5. Kliknij przycisk **Start** w **nowej sesji** okno dialogowe, aby załadować i przeanalizować dane dziennika. Dane dziennika są wyświetlane w siatce analizy analizatora komunikatów.

Na ilustracji poniżej pokazano przykładową sesję skonfigurowany z serwera, klienta i pliki dziennika śledzenia sieci.

![Konfigurowanie sesji analizatora komunikatów](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Należy pamiętać, że analizator komunikatów ładuje pliki dzienników do pamięci. Jeśli masz duży zestaw danych dziennika, można je filtrować w celu uzyskania najlepszej wydajności z analizatora komunikatów.

Najpierw należy określić przedział czasu, który Cię interesuje przeglądanie i ramce czasu tak małej, jak to możliwe. W wielu przypadkach można przejrzeć okres minuty lub godziny, co najwyżej. Importuj najmniejszego zbioru dzienniki spełniające Twoje potrzeby.

Jeśli nadal masz dużą ilość danych dziennika, może być do określania sesji pozwalający filtrować dane dziennika, zanim będzie można go załadować. W **filtru sesji** wybierz opcję **biblioteki** przycisk, aby wybrać wstępnie zdefiniowany filtr; na przykład wybierz **globalnego czasu filtru I** z usługi Azure Storage filtry do filtrowania na przedział czasu. Następnie można edytować kryteria filtrowania, aby określić początkowa i końcowa sygnatura czasowa dla interwału, który chcesz wyświetlić. Można również filtrować nad kodem określonym statusem; na przykład można załadować tylko wpisy dziennika, gdy kod stanu jest 404.

Aby uzyskać więcej informacji na temat importowania danych dziennika do programu Microsoft Message Analyzer, zobacz [podczas pobierania danych komunikatu](https://technet.microsoft.com/library/dn772437.aspx) w witrynie TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Identyfikator żądania klienta umożliwia korelowanie danych pliku dziennika
Biblioteka klienta usługi Azure Storage automatycznie generuje identyfikator żądania klienta unikatowy dla każdego żądania. Ta wartość są zapisywane do dziennika klienta, dziennik serwera i śledzenie sieci, aby można było używać do skorelowania danych we wszystkich trzech dzienników, w ramach analizatora komunikatów. Zobacz [identyfikator żądania klienta](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) dodatkowe informacje o kliencie żądania identyfikator.

W poniższych rozdziałach opisano sposób używania widoków układów wstępnie skonfigurowanych i niestandardowe do skorelowania i grupowanie danych w oparciu identyfikator żądania klienta.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Wybierz układ widoku do wyświetlenia w siatce analizy
Zasoby magazynu, dla analizatora komunikatów obejmują układów widoku magazynu usługi Azure, które są wstępnie skonfigurowane widoki, które służy do wyświetlania Twoich danych przy użyciu grupowania przydatne i kolumn w różnych scenariuszach. Można również utworzyć widok niestandardowy układy i zapisywać je do ponownego wykorzystania.

Obraz poniżej przedstawia **układ widoku** menu dostępne przez wybranie **układ widoku** na wstążce narzędzi. Układów widoku dla usługi Azure Storage są zgrupowane w obszarze **usługi Azure Storage** węzła, w menu. Możesz wyszukać `Azure Storage` w polu wyszukiwania, aby filtrować w usłudze Azure Storage wyświetlać tylko układów. Możesz również wybrać gwiazdkę obok widok układu ulubionych go i wyświetlić je w górnej części menu.

![Wyświetlić menu układu](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Najpierw wybierz **pogrupowane według ClientRequestID i moduł**. Ten widok układu grupy możesz rejestrować dane ze wszystkich trzech dzienników najpierw identyfikator żądania klienta, a następnie plik dziennika źródła (lub **modułu** w Message Analyzer). W tym widoku można przejść do Identyfikatora żądania danego klienta i wyświetlić dane z wszystkich trzech plików dziennika dla tego żądania klienta, identyfikatora.

Obraz poniżej przedstawia ten widok układu stosowane do przykładowych danych dziennika z podzbiorem wyświetlane kolumny. Widać, że dla Identyfikatora żądania danego klienta, analizy są wyświetlane w siatce danych z dziennika klienta, serwera dziennika i śledzenia sieci.

![Usługa Azure Storage widok układu](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Pliki dziennika mają różne kolumny, więc gdy dane z wielu plików dziennika jest wyświetlana w siatce analizy, niektóre kolumny nie może zawierać żadnych danych dla danego wiersza. Na przykład na powyższym rysunku wierszy dziennika klienta są wyświetlane wszystkie dane dla **sygnatura czasowa**, **TimeElapsed**, **źródła**, i **docelowy**kolumn, ponieważ te kolumny nie istnieją w dzienniku klienta, ale istnieje w śledzenia sieci. Podobnie **sygnatura czasowa** kolumna zawiera znacznik czasu: dane z dziennika serwera, ale nie zostaną wyświetlone dane dla **TimeElapsed**, **źródła**, i  **Miejsce docelowe** kolumn, które nie są częścią dziennika serwera.
>
>

Oprócz używania usługi Azure Storage układów widoku, można także definiować i Zapisz układów widoku. Można wybrać inne żądane pola do grupowania danych i zapisać grupowanie jako część Twojego niestandardowe układy.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Zastosuj reguły koloru do siatki analizy
Zasoby magazynu obejmują również reguły kolorów, które oferują wizualizacji oznacza, że do identyfikacji różnych typów błędów w siatce analizy. Zasady wstępnie zdefiniowany kolor zastosowanie błędy HTTP, aby były widoczne tylko w przypadku śledzenia dziennika i sieci serwera.

Aby zastosować reguły koloru, wybierz **reguły koloru** na wstążce narzędzi. Zobaczysz reguły koloru usługi Azure Storage w menu. Samouczek, należy wybrać **błędy klientów (StatusCode od 400 do 499)**, jak pokazano na poniższej ilustracji.

![Usługa Azure Storage widok układu](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Oprócz używania usługi Azure Storage reguły koloru, można także definiować i zapisać reguły koloru.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Grupy i filtrowanie danych dziennika, aby znaleźć błędy 400-range.
Następnie utworzymy grupy i filtrować dane dziennika, aby znaleźć wszystkie błędy w zakresie 400.

1. Znajdź **StatusCode** kolumną w siatce analizy, kliknij prawym przyciskiem myszy kolumnę, nagłówek i wybierz **grupy**.
2. Następnie Grupuj według **ClientRequestId** kolumny. Wyświetlenie danych w siatce analizy teraz jest zorganizowana według kodu stanu oraz identyfikator żądania klienta.
3. Wyświetlenie okna narzędzia filtr widoku, jeśli nie jest już wyświetlany. Na wstążce paska wybierz **narzędzie Windows**, następnie **filtr widoku**.
4. Aby filtrować dane dziennika, aby wyświetlić tylko 400-range błędy, należy dodać następujące kryteria filtrowania, aby **filtr widoku** okna, a następnie kliknij przycisk **Zastosuj**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

Obraz poniżej przedstawia wyniki tego grupowania i filtrowania. Rozwijanie **ClientRequestID** pola poniżej grupowania stan kodu 409, na przykład pokazuje operacji, która zwróciła kod stanu.

![Usługa Azure Storage widok układu](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Po zastosowaniu tego filtru, zobaczysz, że wiersze z dziennika klienta są wyłączone, co klient nie ma dziennika **StatusCode** kolumny. Najpierw omówimy server i dzienniki śledzenia sieci, aby zlokalizować błędów 404, a następnie wrócimy do dziennika klienta, aby zbadać operacje klienta, które doprowadziły do nich.

> [!NOTE]
> Można filtrować według **StatusCode** kolumny, a jeszcze wyświetlania danych z wszystkich trzech dzienników, w tym dziennika klienta, jeśli wyrażenie jest dodawane do filtru, który zawiera wpisy dziennika, w którym kod stanu: ma wartość null. Do utworzenia tego wyrażenia filtru, należy użyć:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Ten filtr zwraca wszystkie wiersze z klienta dziennika i tylko wiersze z dziennik server i dziennik HTTP których kod stanu jest większa niż 400. Jeśli zastosujesz układ widoku, pogrupowane według Identyfikatora żądania klienta i moduł możesz wyszukiwać i przewiń za pośrednictwem wpisy dziennika, aby znaleźć te, gdzie znajdują się wszystkie trzy dzienniki.   
>
>

### <a name="filter-log-data-to-find-404-errors"></a>Filtruj dane dziennika, aby znaleźć błędy 404
Zasoby magazynu obejmują wstępnie zdefiniowane filtry, które można wykorzystać do zawężenia dane dziennika, aby znaleźć błędy lub trendy, którego szukasz. Następnie będzie Zastosuj dwa wstępnie zdefiniowane filtry: jeden, który filtruje server i dzienniki śledzenia sieci dla błędów 404 oraz jedną, która filtruje dane dotyczące określonego przedziału czasu.

1. Wyświetlenie okna narzędzia filtr widoku, jeśli nie jest już wyświetlany. Na wstążce paska wybierz **narzędzie Windows**, następnie **filtr widoku**.
2. W oknie filtru w widoku wybierz **biblioteki**i wyszukiwania na `Azure Storage` można znaleźć usługi Azure Storage filtrów. Wybierz filtr **404 (nie znaleziono) wiadomości we wszystkich dziennikach**.
3. Wyświetlanie **biblioteki** menu ponownie, a następnie znajdź i zaznacz **globalny filtr czasu**.
4. Edytuj sygnatury czasowe, pokazane na filtrze zakresu, który chcesz wyświetlić. Dzięki temu można zawęzić zakres danych do przeanalizowania.
5. Filtr powinien wyglądać podobnie do poniższego przykładu. Kliknij przycisk **Zastosuj** Aby zastosować filtr do siatki analizy.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Usługa Azure Storage widok układu](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analizuj dane dzienników
Skoro masz pogrupowanymi i filtrowanymi dane, można sprawdzić szczegóły poszczególnych żądań, którzy wygenerowali błąd 404. W bieżący układ widoku danych są grupowane według Identyfikatora żądania klienta, następnie według źródła dziennika. Ponieważ firma Microsoft filtrowania żądań których pole StatusCode zawiera 404, zobaczymy tylko serwera i danych śledzenia sieci, nie dane dziennika klienta.

Na ilustracji poniżej pokazano konkretnego żądania, w którym operacji pobieranie obiektów Blob uzyskane 404, ponieważ obiekt blob nie istnieje. Należy pamiętać, że niektóre kolumny zostały usunięte z standardowy widok, aby wyświetlić odpowiednie dane.

![Filtrowane Server i dzienniki śledzenia sieci](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Następnie firma Microsoft będzie skorelowania tego Identyfikatora żądania klienta z danymi dziennika klienta, aby zobaczyć, jakie akcje klienta miał w momencie wystąpienia błędu. Można wyświetlić nowego widoku siatki analizy dla tej sesji wyświetlić dane dziennika klienta, które zostaną otwarte w drugiej karcie:

1. Po pierwsze, skopiuj wartość **ClientRequestId** pola do Schowka. Można to zrobić, wybierając albo wiersz, lokalizowanie **ClientRequestId** pola, klikając prawym przyciskiem myszy na wartość danych, a następnie wybierając **kopiowania "ClientRequestId"**.
2. Na wstążce paska wybierz **nowy podgląd**, a następnie wybierz **siatki analizy** na otwarcie nowej karty. Nowa karta pokazuje wszystkie dane w plikach dziennika bez grupowanie, filtrowanie lub reguły koloru.
3. Na wstążce paska wybierz **układ widoku**, a następnie wybierz **wszystkie kolumny klienta .NET** w obszarze **usługi Azure Storage** sekcji. Ten układ widoku zawiera dane odebrane od klienta, log, a także dzienniki śledzenia serwera i sieci. Domyślnie są sortowane według **MessageNumber** kolumny.
4. Następnie wyszukaj dziennika klienta identyfikator żądania klienta. Na wstążce paska wybierz **komunikaty Znajdź**, następnie określić filtr niestandardowy identyfikator żądania klienta w **znaleźć** pola. Dla filtru, określając własny identyfikator żądania klienta, należy użyć następującej składni:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Message Analyzer lokalizuje i wybiera pierwszy wpis dziennika, w których kryteria wyszukiwania odpowiada identyfikator żądania klienta. W dzienniku klienta znajdują się kilka wpisy dla każdego Identyfikatora żądania klienta, dlatego warto grupować je według **ClientRequestId** pola, aby ułatwić widoczne wszystko ze sobą. Na ilustracji poniżej pokazano wszystkie komunikaty w dzienniku klienta dla określonego klienta identyfikator żądania.

![Wyświetlanie dziennika klienta błędów 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Dane wyświetlane w układów widoku w tych dwóch kartach można analizować dane żądania, aby określić, co spowodowało błąd. Można również przeglądać żądania, które poprzedzony to jedno, aby zobaczyć, jeśli poprzednie zdarzenie może doprowadzić do błędu 404. Na przykład możesz ocenić wpisy dziennika klienta, poprzedzających ten identyfikator żądania klienta, aby określić, czy obiekt blob został usunięty, czy błąd został spowodowany aplikacji klienckiej, wywoływania interfejsu API CreateIfNotExists na kontenera lub obiektu blob. W dzienniku klienta można znaleźć adres obiektu blob w **opis** pola; na serwerze i dzienniki śledzenia sieci te informacje są wyświetlane w **Podsumowanie** pola.

Jeśli znasz już adres obiektu blob, które zwróciło błąd 404, możesz zbadać dalej. Wyszukiwanie wpisów dziennika dla innych wiadomości, skojarzone z operacjami w tym samym obiekcie blob, można sprawdzić, czy klient wcześniej usunięta jednostki.

## <a name="analyze-other-types-of-storage-errors"></a>Analizowanie innych typów błędów magazynu
Teraz, po zapoznaniu się z używaniem analizatora komunikatów na potrzeby analizowania danych dziennika, inne rodzaje błędów przy użyciu widoku można analizować układów, reguły koloru i wyszukiwanie filtrowania. W poniższej tabeli wymieniono niektóre problemy, które mogą wystąpić i kryteriów filtrowania służących do ich lokalizację. Aby uzyskać więcej informacji o konstruowaniu filtry oraz analizatora komunikatów, filtrowanie języka, zobacz [filtrowanie danych komunikatu](https://technet.microsoft.com/library/jj819365.aspx).

| Aby zbadać... | Użyj wyrażenia filtru... | Wyrażenie ma zastosowanie do dziennika (klienta, serwera, sieci, wszystkie) |
| --- | --- | --- |
| Nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce |AzureStorageClientDotNetV4.Description zawiera "Ponawianie próby nie można wykonać operacji." |Klient |
| HTTP wzrost wartości PercentThrottlingError |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |Sieć |
| Wzrost wartości PercentTimeoutError |HTTP.Response.StatusCode   == 500 |Sieć |
| Wzrost wartości PercentTimeoutError (wszystko) |*StatusCode   == 500 |Wszyscy |
| Wzrost wartości PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Klient |
| Komunikaty HTTP 403 (zabroniony) |HTTP.Response.StatusCode   == 403 |Sieć |
| HTTP 404 (nie znaleziono) wiadomości |HTTP.Response.StatusCode   == 404 |Sieć |
| 404 (wszystko) |* StatusCode == 404 |Wszyscy |
| Udostępnione problem autoryzacji sygnatury dostępu Współdzielonego |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Sieć |
| HTTP 409 (konflikt) wiadomości |HTTP.Response.StatusCode   == 409 |Sieć |
| 409 (wszystko) |* StatusCode == 409 |Wszyscy |
| Wpisy dziennika PercentSuccess niski lub analizy mają operacji ze stanem transakcji ClientOtherErrors |AzureStorageLog.RequestStatus == "ClientOtherError" |Serwer |
| Ostrzeżenie Nagle'a |((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) i (AzureStorageLog.RequestPacketSize < 1460) i (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200) |Serwer |
| Rejestruje przedział czasu w serwera i sieci |#Timestamp > = 2014-10-20T16:36:38 i #Timestamp < = 2014-10-20T16:36:39 |Server, Network |
| Przedział czasu, dzienniki serwera |AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 i AzureStorageLog.Timestamp < = 2014-10-20T16:36:39 |Serwer |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rozwiązywania problemów scenariuszy end-to-end w usłudze Azure Storage zobacz następujące zasoby:

* [Monitor, diagnose, and troubleshoot Microsoft Azure Storage (Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage)](storage-monitoring-diagnosing-troubleshooting.md)
* [Analityka magazynu](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorowanie konta magazynu w witrynie Azure portal](storage-monitor-storage-account.md)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Microsoft Message Analyzer operacyjnego przewodnik](https://technet.microsoft.com/library/jj649776.aspx)
