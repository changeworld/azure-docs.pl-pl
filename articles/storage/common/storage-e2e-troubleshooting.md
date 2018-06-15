---
title: Rozwiązywanie problemów dotyczących usługi Azure Storage z diagnostyki & Message Analyzer | Dokumentacja firmy Microsoft
description: Samouczek prezentacja end-to-end Rozwiązywanie problemów z funkcją analizy magazynu Azure, AzCopy i programu Microsoft Message Analyzer
services: storage
documentationcenter: dotnet
author: tamram
manager: timlt
ms.assetid: 643372a3-1c07-4e88-b4ef-042512a43086
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.openlocfilehash: 324370ae18627a1985e6a40aec11ee2fa871e93b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30323308"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>End-to-end Rozwiązywanie problemów przy użyciu metryk usługi Azure Storage i rejestrowania, AzCopy i analizatora komunikatów
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnozowanie i rozwiązywanie problemów z jest klucza umiejętności umożliwiające tworzenie i obsługa aplikacji klienta za pomocą usługi Magazyn Microsoft Azure. Z powodu Rozproszony charakter aplikacji Azure diagnozowanie i rozwiązywanie problemów z błędów i problemów z wydajnością może być bardziej skomplikowane niż w tradycyjnych środowisk.

W tym samouczku przedstawiony sposób identyfikowania niektórych błędów, które mogą wpłynąć na wydajność i Rozwiąż te błędy z end-to-end w celu zoptymalizowania aplikacji klienta za pomocą narzędzi dostarczanych przez firmę Microsoft i usługi Azure Storage.

Ten samouczek zawiera praktyczne eksploracji end-to-end scenariusza rozwiązywania problemów. Szczegółowy przewodnik koncepcyjny do Rozwiązywanie problemów z aplikacjami usługi Azure storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Magazyn Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Narzędzia do rozwiązywania problemów z aplikacjami usługi Azure Storage
Rozwiązywać aplikacji klienckich za pomocą usługi Magazyn Microsoft Azure kombinację narzędzia służy do określenia wystąpienia problemu i co może być przyczyną problemu. Do tych narzędzi należą:

* **Usługa Azure Storage Analytics**. [Analizy usługi Azure Storage](/rest/api/storageservices/Storage-Analytics) zawiera metryki i rejestrowania usługi Azure Storage.
  
  * **Metryki magazynu** śledzi transakcji metryki i metryki pojemności konta magazynu. Korzystając z metryki, można określić, jak aplikacja działa zgodnie z wielu różnych środków. Zobacz [schemat tabeli metryki analityka magazynu](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) Aby uzyskać więcej informacji o typach metryki śledzone przez analityka magazynu.
  * **Rejestrowanie magazynu** rejestruje każde żądanie do usług magazynu Azure do dzienników po stronie serwera. Dziennik śledzenia szczegółowe dane dla każdego żądania, w tym operacje wykonywane, stan działania i informacje opóźnienia. Zobacz [Format dziennika analityka magazynu](/rest/api/storageservices/Storage-Analytics-Log-Format) Aby uzyskać więcej informacji o danych żądania i odpowiedzi, które są zapisywane w dziennikach przez analityka magazynu.

* **Azure portal**. Można skonfigurować rejestrowanie i metryki dla konta magazynu w [portalu Azure](https://portal.azure.com). Można także wyświetlić schematy i wykresy, które pokazują, jak aplikacja działa w czasie i skonfigurować alerty powiadamiające o, jeśli aplikacja przeprowadza się inaczej, niż oczekiwano dla określonej metryki.
  
    Zobacz [monitorować konto magazynu w portalu Azure](storage-monitor-storage-account.md) informacji o konfigurowaniu monitorowania w portalu Azure.
* **AzCopy**. Dzienniki serwera usługi Azure Storage są przechowywane jako obiekty BLOB, dzięki czemu można użyć narzędzia AzCopy do kopiowania obiektów blob dziennika do katalogu lokalnego do analizy przy użyciu programu Microsoft Message Analyzer. Zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md) Aby uzyskać więcej informacji na temat narzędzia AzCopy.
* **Microsoft Message Analyzer**. Message Analyzer jest narzędziem, które korzysta z plików dziennika i dane dziennika są wyświetlane w formacie visual, który ułatwia grupy danych dzienników, wyszukiwania i filtrów w przydatne zestawy, które umożliwiają analizowanie błędów i problemów z wydajnością. Zobacz [Microsoft komunikatów analizatora operacyjnego przewodnik](http://technet.microsoft.com/library/jj649776.aspx) Aby uzyskać więcej informacji na temat analizatora komunikatów.

## <a name="about-the-sample-scenario"></a>Przykładowy scenariusz — informacje
W tym samouczku zostaną omówione scenariusz, w którym metryk usługi Azure Storage wskazuje tempo niski procent powodzenia dla aplikacji, która wywołuje magazynu Azure. Metryka szybkość niski procent powodzenia (wyświetlane jako **PercentSuccess** w [portalu Azure](https://portal.azure.com) i w tabelach metryk) śledzi operacji, który powiedzie się, ale który zwrócił kod stanu HTTP, która jest większa niż 299. W plikach dziennika magazynu po stronie serwera, operacje te są rejestrowane ze stanem transakcji **ClientOtherErrors**. Aby uzyskać więcej informacji na temat Metryka niski procent powodzenia, zobacz [metryki pokazują PercentSuccess niskim lub wpisy dziennika analytics ma operacji ze stanem transakcji ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure operacje magazynu mogą zwracać kody stanu HTTP większa niż 299 jako część ich normalną funkcjonalność. Jednak te błędy w niektórych przypadkach wskazują, że można zoptymalizować aplikacji klienta, aby uzyskać lepszą wydajność.

W tym scenariuszu teraz przyjrzymy się niskim procent powodzenia wysokości można niczego poniżej 100%. Są dostępne różne poziomy metryki, jednak zgodnie z potrzebami. Firma Microsoft zaleca, że podczas testowania aplikacji, zostanie nawiązana tolerancji linii bazowej dla Twojego kluczowe metryki wydajności. Na przykład można określić na podstawie testowanie, czy aplikacja ma Częstotliwość powodzeń procent spójne 90% lub 85%. Jeśli dane metryk wskazuje, że aplikacja jest różniących się od tego numeru, a następnie można zbadać, co może powodować wzrost.

W naszym scenariuszu próbki po został określony, czy metryka szybkość procent powodzenia jest poniżej 100%, omówione zostanie dzienniki, aby znaleźć błędy, które odnoszą się do metryki i ich używać, aby dowiedzieć się, co jest przyczyną niższe Częstotliwość powodzeń procent. Wyjaśniono, w szczególności błędów w zakresie od 400. Następnie firma Microsoft będzie dokładniejsze badanie błędów 404 (nie znaleziono).

### <a name="some-causes-of-400-range-errors"></a>Niektóre przyczyny błędów 400 zakresu
Przykłady poniżej przedstawiono przykładowy zakres 400 błędy żądań z magazynu obiektów Blob Azure i ich możliwe przyczyny. Żadnego z tych błędów, jak również błędy w zakresie 300 i 500 zakresu, może przyczynić się do szybkości niski procent powodzenia.

Należy pamiętać, że z poniższej listy są daleko od ukończenia. Zobacz [stanu i kodów błędów](http://msdn.microsoft.com/library/azure/dd179382.aspx) w witrynie MSDN, aby uzyskać szczegółowe informacje o ogólne błędy usługi Azure Storage oraz informacji o błędach do różnych usług magazynu.

**Przykłady kodu 404 (nie znaleziono) stanu**

Występuje, gdy operacja odczytu względem kontener lub obiekt blob nie powiedzie się, ponieważ nie znaleziono obiektu blob lub kontenera.

* Występuje, gdy kontener lub obiekt blob został usunięty przez innego klienta przed tego żądania.
* Występuje, gdy używasz wywołanie interfejsu API, która tworzy kontener lub obiekt blob po sprawdzeniu, czy istnieje. Interfejsy API CreateIfNotExists wywoływania HEAD najpierw Sprawdzaj istnienie kontener lub obiekt blob; Jeśli nie istnieje, zwracany jest błąd 404, a drugie wywołanie PUT jest przeprowadzane zapisu kontener lub obiekt blob.

**Kod stanu 409 przykłady (konflikt)**

* Występuje, jeśli używasz interfejsu API tworzenia Aby utworzyć nowy kontener lub obiekt blob, bez uprzedniego sprawdzania pod kątem istnienia i kontener lub obiekt blob o tej nazwie już istnieje.
* Występuje, gdy kontener jest usuwany, a użytkownik próbuje utworzyć nowy kontener o tej samej nazwie, przed wykonaniem operacji usuwania.
* Występuje, gdy Określ dzierżawę na kontener lub obiekt blob, a jest już obecny dzierżawy.

**Kod stanu 412 (warunek wstępny nie powiodła się) przykłady**

* Występuje, gdy nie został spełniony warunek określony przez nagłówek warunkowy.
* Występuje, gdy określono Identyfikatora dzierżawy nie jest zgodny z Identyfikatorem dzierżawy na kontener lub obiekt blob.

## <a name="generate-log-files-for-analysis"></a>Generuj pliki dziennika do analizy
W tym samouczku użyjemy analizatora komunikatów do pracy z trzech różnych typów plików dziennika, mimo że można wybrać do pracy z jednym z nich:

* **Dziennik serwera**, który jest tworzony po włączeniu rejestrowania usługi Azure Storage. Dziennik serwera zawiera dane dotyczące każdej operacji o nazwie względem jednej z usług magazynu Azure — obiekt blob, kolejki, tabel i plików. Dziennik serwera wskazuje, które operacja została wywołana i jakie kod stanu został zwrócony, a także inne szczegóły dotyczące żądania i odpowiedzi.
* **Dziennika klienta .NET**, który jest tworzony po włączeniu rejestrowania po stronie klienta z wewnątrz aplikacji .NET. Dziennik klienta zawiera szczegółowe informacje dotyczące sposobu klienta przygotowuje żądania i odbiera i przetwarza odpowiedzi.
* **Dziennika śledzenia sieci HTTP**, która zbiera dane na dane żądanie i odpowiedź HTTP/HTTPS, tym dla operacji w usłudze Azure Storage. W tym samouczku będziemy zostanie wygenerowany śledzenia sieci za pomocą analizatora komunikatów.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurowanie rejestrowania po stronie serwera i metryki
Najpierw potrzebujemy skonfigurować rejestrowanie usługi Azure Storage i metryki, dzięki czemu będziemy mieć dane z aplikacji klienta do analizowania. Rejestrowanie i metryki na różne sposoby — można skonfigurować za pomocą [portalu Azure](https://portal.azure.com), za pomocą programu PowerShell, lub programowo. Zobacz [włączenie metryki magazynu i wyświetlanie danych metryk](http://msdn.microsoft.com/library/azure/dn782843.aspx) i [Włączanie rejestrowania magazynu i uzyskiwanie dostępu do danych dziennika](http://msdn.microsoft.com/library/azure/dn782840.aspx) w witrynie MSDN, aby uzyskać więcej informacji o konfigurowaniu rejestrowania i metryki.

**Za pomocą portalu Azure**

Aby skonfigurować rejestrowanie i metryki do obsługi magazynu konta przy użyciu [portalu Azure](https://portal.azure.com), postępuj zgodnie z instrukcjami w [monitorować konto magazynu w portalu Azure](storage-monitor-storage-account.md).

> [!NOTE]
> Nie jest możliwe do ustawienia metryki minuty przy użyciu portalu Azure. Jednak zaleca się, że te elementy zostały skonfigurowane na potrzeby tego samouczka i do badania problemów dotyczących wydajności z aplikacją. Można ustawić minuty metryki przy użyciu programu PowerShell, jak pokazano poniżej, albo programowo z użyciem biblioteki klienta usługi storage.
> 
> Należy pamiętać, że portalu Azure nie może wyświetlić metryki minuty, tylko metryki co godzinę.
> 
> 

**Via PowerShell**

Aby zacząć korzystać z programu PowerShell dla usługi Azure, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

1. Użyj [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0) polecenia cmdlet, aby dodać konta użytkownika platformy Azure do okna programu PowerShell:
   
    ```powershell
    Add-AzureAccount
    ```

2. W **Zaloguj się do systemu Microsoft Azure** okna, wpisz adres e-mail i hasło skojarzone z Twoim kontem. Nastąpi uwierzytelnienie i zapisanie informacji o poświadczeniach na platformie Azure, a następnie zamknięcie okna.
3. Ustaw domyślnego konta magazynu na konto magazynu, którego używasz dla tego samouczka, wykonując następujące polecenia w oknie programu PowerShell:
   
    ```powershell
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Włącz rejestrowanie magazynu dla usługi Blob:
   
    ```powershell
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```

5. Włączyć metryki magazynu dla usługi obiektów Blob, upewniając się ustawić **- MetricsType** do `Minute`:
   
    ```powershell
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Konfigurowanie rejestrowania klienta .NET
Aby skonfigurować rejestrowanie po stronie klienta dla aplikacji .NET, Włącz diagnostykę .NET w pliku konfiguracji aplikacji (web.config lub app.config). Zobacz [klienta rejestrowaniem za pomocą biblioteki klienta usługi Storage .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx) i [klienta rejestrowaniem za pomocą programu Microsoft Azure Storage SDK for Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) w witrynie MSDN, aby uzyskać szczegółowe informacje.

Dziennika klienta zawiera szczegółowe informacje dotyczące sposobu klienta przygotowuje żądania i odbiera i przetwarza odpowiedzi.

Biblioteka klienta magazynu przechowuje dane dziennika klienta w lokalizacji określonej w pliku konfiguracji aplikacji (web.config lub app.config).

### <a name="collect-a-network-trace"></a>Zbieraj śledzenia ścieżek połączeń sieciowych
Message Analyzer służy do zbierania śledzenia sieci HTTP i HTTPS, aplikacja kliencka jest uruchomiona. Używa analizatora komunikatów [Fiddler](http://www.telerik.com/fiddler) wewnętrznych. Aby zbierać dane śledzenia sieci, zaleca się skonfigurowanie narzędzia Fiddler do rejestrowania nieszyfrowanego ruchu HTTPS:

1. Zainstaluj [Fiddler](http://www.telerik.com/download/fiddler).
2. Uruchom program Fiddler.
3. Wybierz **narzędzia | Opcje narzędzia fiddler**.
4. W oknie dialogowym Opcje, upewnij się, że **przechwytywania połączenie HTTPS** i **odszyfrowania ruchu HTTPS** są zaznaczone, jak pokazano poniżej.

![Skonfiguruj opcje Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Samouczek zbieranie najpierw zapisać w analizatora komunikatów śledzenia ścieżek połączeń sieciowych, a następnie tworzenia sesji do analizowania śledzenia i dzienniki analizy. Aby zebrać śledzenia sieci w analizatora komunikatów:

1. Message Analyzer, wybierz **pliku | Szybkie śledzenia | Niezaszyfrowana HTTPS**.
2. Śledzenie rozpocznie się natychmiast. Wybierz **zatrzymać** można zatrzymać śledzenia tak, aby można go skonfigurować do śledzenia tylko ruch magazynu.
3. Wybierz **Edytuj** do edycji sesji śledzenia.
4. Wybierz **Konfiguruj** łącza po prawej stronie **Microsoft-Pef-WebProxy** dostawcy ETW.
5. W **Zaawansowane ustawienia** okna dialogowego, kliknij przycisk **dostawcy** kartę.
6. W **filtru Hostname** Określ magazynu punktów końcowych, rozdzielając je spacjami. Na przykład można określić punktów końcowych Zmień `storagesample` do nazwy konta magazynu:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Zamknąć okno dialogowe, a następnie kliknij przycisk **ponowne uruchomienie** do rozpoczęcia zbierania śledzenia z filtrem nazwy hosta w miejscu, tak aby tylko ruchu sieciowego magazynu Azure znajduje się w śledzeniu.

> [!NOTE]
> Po zakończeniu zbierania śledzenia sieci, zdecydowanie zaleca się aby przywrócić ustawienia, które mogły zostać zmienione w narzędziu Fiddler do odszyfrowywania ruchu HTTPS. W oknie dialogowym Opcje Fiddler, usuń zaznaczenie **przechwytywania połączenie HTTPS** i **odszyfrowania ruchu HTTPS** pola wyboru.
> 
> 

Zobacz [korzystanie z funkcji śledzenia sieci](http://technet.microsoft.com/library/jj674819.aspx) w witrynie Technet, aby uzyskać więcej informacji.

## <a name="review-metrics-data-in-the-azure-portal"></a>Przejrzyj dane metryk w portalu Azure
Gdy aplikacja została uruchomiona w danym okresie czasu, można przejrzeć wykresy metryk, które są widoczne w [portalu Azure](https://portal.azure.com) obserwować, jak usługa została wykonywania.

Po pierwsze przejdź do swojego konta magazynu w portalu Azure. Domyślnie monitorowanie wykres z **procent powodzenia** metryka jest wyświetlany w bloku konta. Jeśli zostały wcześniej zmodyfikowane na wykresie, aby wyświetlić różne metryki, Dodaj **procent powodzenia** metryki.

Będą teraz widoczne **procent powodzenia** na wykresie monitorowania oraz innych metryk zostały dodane. W tym scenariuszu, który będzie dalej sprawdzeniu analizując dzienniki w Message Analyzer Częstotliwość powodzeń procent jest nieco poniżej 100%.

Więcej szczegółów na dodawanie i dostosowywanie metryki wykresy, zobacz [dostosowanie metryk wykresy](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Może upłynąć trochę czasu, zanim pojawią się w portalu Azure po włączeniu metryki magazynu danych metryki. Jest to spowodowane co godzinę metryki dla poprzedniej godziny nie są wyświetlane w portalu Azure, dopóki nie upłynie bieżącej godziny. Ponadto metryki minutowe nie są aktualnie wyświetlane w portalu Azure. Dlatego zależnie od tego, po włączeniu metryki, może upłynąć do dwóch godzin, aby wyświetlić dane metryk.
> 
> 

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Użyj narzędzia AzCopy, aby skopiować dzienniki serwera do katalogu lokalnego
Usługa Azure Storage zapisuje dane dzienników serwera do obiektów blob, podczas metryki są zapisywane do tabel. Dziennik obiekty BLOB są dostępne w dobrze znanego `$logs` kontener dla konta magazynu. Obiekty BLOB dziennika są nazywane hierarchicznie przez rok, miesiąc, dzień i godzinę, dzięki czemu można łatwo zlokalizować przedział czasu, który chcesz zbadać. Na przykład w `storagesample` konto, kontenera obiektów blob dziennika dla 01/02/2015 od 8 do 9 am, jest `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Poszczególne obiekty BLOB w tym kontenerze są nazywane sekwencyjnie, począwszy od `000000.log`.

Można użyć narzędzia wiersza polecenia AzCopy, aby pobrać te pliki dziennika po stronie serwera do wybranej lokalizacji na komputerze lokalnym. Na przykład służy następujące polecenie do pobierania plików dziennika dla operacji obiektu blob, które miały miejsce na 2 stycznia 2015 do folderu `C:\Temp\Logs\Server`; zastąpić `<storageaccountname>` z nazwą konta magazynu i `<storageaccountkey>` kluczem dostępu do konta :

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
Narzędzie AzCopy jest dostępny do pobrania na [Azure pobiera](https://azure.microsoft.com/downloads/) strony. Aby uzyskać więcej informacji o używaniu narzędzia AzCopy, zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md).

Aby uzyskać dodatkowe informacje na temat pobierania dzienników po stronie serwera, zobacz [Pobierz rejestrowania magazynu danych dziennika](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Użyj programu Microsoft Message Analyzer do analizowania danych dziennika
Programu Microsoft Message Analyzer jest narzędziem do przechwytywania, wyświetlania i analizowania ruchu, zdarzeń i inne komunikaty w scenariuszach rozwiązywania problemów i diagnozowania systemu lub aplikacji do obsługi komunikatów protokołu. Analizator komunikatów również umożliwia ładowanie, agregacji i analizowania danych z dziennika i zapisać pliki śledzenia. Aby uzyskać więcej informacji na temat Message Analyzer, zobacz [Microsoft komunikatów analizatora operacyjnego przewodnik](http://technet.microsoft.com/library/jj649776.aspx).

Analizator komunikatów obejmuje zasoby usługi Azure Storage, które ułatwiają analizowanie serwera i klienta oraz dzienniki sieci. W tej sekcji omówiono sposób używania tych narzędzi w celu rozwiązania problemu niski procent powodzenia w dziennikach magazynu.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Pobierz i zainstaluj Message Analyzer i zasoby magazynu Azure
1. Pobierz [Message Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) z Microsoft Centrum pobierania i uruchom Instalatora.
2. Uruchom analizator komunikatów.
3. Z **narzędzia** menu, wybierz opcję **Menedżera zasobów**. W **Menedżera zasobów** okno dialogowe, wybierz opcję **pobiera**, następnie odfiltrować **usługi Azure Storage**. Zostanie wyświetlone zasoby magazynu Azure, jak pokazano na rysunku poniżej.
4. Kliknij przycisk **wszystkie elementy wyświetlane synchronizacji** do zainstalowania zasoby magazynu Azure. Dostępne zasoby obejmują:
   * **Reguły koloru magazynu Azure:** reguły koloru magazynu platformy Azure umożliwiają zdefiniowanie specjalne filtry, które Użyj style kolorów, tekst i czcionek, aby wyróżnić komunikaty, które zawierają określone informacje w śledzenia.
   * **Wykresy magazynu Azure:** wykresy magazynu Azure są wstępnie zdefiniowane wykresy, które dane dziennika dla serwera programu graph. Należy pamiętać, aby używać usługi Azure Storage wykresy w tej chwili, może tylko załadowanie dziennik serwera w siatce analizy.
   * **Azure magazynu analizatory składni:** analizatory składni usługi Azure Storage przeanalizować klienta usługi Azure Storage server i dzienniki HTTP, aby je wyświetlić w siatce analizy.
   * **Filtry magazynu Azure:** filtry magazynu Azure są wstępnie zdefiniowane kryteria, które można wykonać zapytania w siatce analizy danych.
   * **Układy widoku magazynu Azure:** układów widoku magazynu Azure są układów wstępnie zdefiniowanych kolumn i grupowania w siatce analizy.
5. Po zainstalowaniu zasoby, należy ponownie uruchomić analizator komunikatów.

![Menedżera zasobów analizator komunikatów](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Zainstaluj wszystkie zasoby usługi Azure Storage, pokazane na potrzeby tego samouczka.
> 
> 

### <a name="import-your-log-files-into-message-analyzer"></a>Importowanie plików dzienników do analizatora komunikatów
Można importować wszystkie zapisane pliki dziennika (po stronie serwera, po stronie klienta i sieci) w jednej sesji w programu Microsoft Message Analyzer do analizy.

1. Na **pliku** kliknij menu programu Microsoft Message Analyzer **nowej sesji**, a następnie kliknij przycisk **puste sesji**. W **nowej sesji** okna dialogowego, wprowadź nazwę dla sesji analizy. W **szczegółowych informacji o sesji** panelu, kliknij pozycję **pliki** przycisku.
2. Aby załadować dane śledzenia sieci generowane przez analizator komunikatów, kliknij na **Dodaj pliki**, przejdź do lokalizacji, w której zapisano plik .matp z śledzenia sesji sieci web, wybierz plik .matp, a następnie kliknij przycisk **Otwórz**.
3. Aby załadować dane dziennika po stronie serwera, kliknij na **Dodaj pliki**, przejdź do lokalizacji, do którego pobrano dzienników po stronie serwera, wybierz plik dziennika dla zakresu czasu, aby przeanalizować, a następnie kliknij przycisk **Otwórz**. Następnie w **szczegółowych informacji o sesji** panelu, ustaw **konfiguracji dziennika tekstowego** listy rozwijanej dla każdego pliku dziennika po stronie serwera, aby **AzureStorageLog** aby upewnić się, że Microsoft Message Analizator można poprawnie przeanalizować pliku dziennika.
4. Aby załadować dane dziennika klienta, kliknij na **Dodaj pliki**, przejdź do lokalizacji, w której zapisano dzienników po stronie klienta, wybierz plik dziennika ma analizować, a następnie kliknij przycisk **Otwórz**. Następnie w **szczegółowych informacji o sesji** panelu, ustaw **konfiguracji dziennika tekstowego** listy rozwijanej dla każdego pliku dziennika klienta, aby **AzureStorageClientDotNetV4** do upewnij się, że Programu Microsoft Message Analyzer można poprawnie przeanalizować pliku dziennika.
5. Kliknij przycisk **Start** w **nowej sesji** okna dialogowego, aby załadować i przeanalizować dane dziennika. Dane dziennika są wyświetlane w siatce analizy analizator komunikatów.

Na rysunku poniżej przedstawiono sesji przykład skonfigurowane z serwera, klienta i pliki dziennika śledzenia sieci.

![Konfigurowanie sesji analizator komunikatów](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Należy pamiętać, że analizator komunikatów ładuje pliki dziennika do pamięci. Jeśli masz dużych zestawów danych dziennika można odfiltrować Aby uzyskać najlepszą wydajność z analizatora komunikatów.

Najpierw należy określić przedział czasu, którego jesteś zrecenzować i ramce czasu możliwie jak najmniejszy. W wielu przypadkach można przejrzeć okres minut lub godzin, co najwyżej. Importuj najmniejszy zestaw dzienników, które mogą zaspokoić potrzeb.

Jeśli masz dużą ilość danych dziennika, może być określone sesję do filtru dane dziennika, zanim będzie można go załadować. W **filtru sesji** wybierz opcję **biblioteki** przycisku Wybierz wstępnie zdefiniowany filtr; na przykład wybrać **globalne czasu filtru I** z usługi Azure Storage filtrów do filtrowania w przedziale czasu. Następnie można edytować kryteria filtrowania, aby określić początkowej i końcowej dla interwału, który chcesz wyświetlić sygnatury czasowej. Można również filtrować według określony kod stanu; na przykład można załadować tylko wpisy dziennika przypadku kod stanu 404.

Aby uzyskać więcej informacji na temat importowania danych dziennika do programu Microsoft Message Analyzer, zobacz [pobierania danych komunikat](http://technet.microsoft.com/library/dn772437.aspx) w witrynie TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Użyj Identyfikatora żądania klienta do skorelowania danych pliku dziennika
Biblioteka klienta magazynu Azure automatycznie generuje identyfikator żądania klienta unikatowy dla każdego żądania. Ta wartość jest zapisywane do dziennika klienta, serwera dziennika i śledzenia sieci, dzięki czemu służy do skorelowania danych we wszystkich trzech dzienników w analizatora komunikatów. Zobacz [Identyfikatora żądania klienta](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) dodatkowe informacje o kliencie żądania identyfikatora.

W poniższych rozdziałach opisano sposób użycia układu wstępnie skonfigurowane i niestandardowe widoki służące do skorelowania i dane grupy oparte na identyfikator żądania klienta.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Wybierz układ widoku do wyświetlenia w siatce analizy
Zasoby magazynu dla analizatora komunikatów obejmują układów widoku magazynu Azure, które są wstępnie skonfigurowane widoki, które służy do wyświetlania danych za pomocą przydatne grup i kolumn dla różnych scenariuszy. Można również utworzyć widok niestandardowy układy i zapisywać je do ponownego użycia.

Obraz poniżej przedstawia **układ widoku** menu dostępne po wybraniu **układ widoku** na wstążce narzędzi. Układów widoku dla usługi Azure Storage są zgrupowane w **usługi Azure Storage** węzła w menu. Możesz wyszukać `Azure Storage` w polu wyszukiwania, aby odfiltrować usługi Azure Storage wyświetlić tylko układów. Można również wybrać gwiazdką układ widoku była Ulubione i wyświetlić je w górnej części menu.

![Menu Widok układu](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Najpierw wybierz **pogrupowane według ClientRequestID i modułu**. Ten widok Układ grup dziennika najpierw danych ze wszystkich trzech dzienników, identyfikator żądania klienta, a następnie plik dziennika źródła (lub **modułu** w analizator komunikatów). Z tym widokiem można przejść do Identyfikatora żądania klienta określonego i wyświetlać dane ze wszystkich trzech plików dziennika dla tego żądania klienta identyfikatora.

Obraz poniżej przedstawia ten widok układu stosowane do przykładowych danych dziennika z podzbiorem wyświetlane kolumny. Widać, że dla określonego Identyfikatora żądania klienta, analizy są wyświetlane w siatce danych z dziennika klienta, serwera dziennika i śledzenia ścieżek połączeń sieciowych.

![Układ widoku magazynu Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Różnych plikach dziennika mają różne kolumny, gdy dane z wielu plików dziennika są wyświetlane w siatce analizy, niektóre kolumny nie może zawierać żadnych danych dla danego wiersza. Na przykład na powyższym rysunku wierszy dziennika klienta są wyświetlane wszystkie dane dotyczące **sygnatury czasowej**, **TimeElapsed**, **źródła**, i **docelowego**kolumn, ponieważ te kolumny nie istnieją w dzienniku klienta, ale istnieje w śledzenia ścieżek połączeń sieciowych. Podobnie **sygnatury czasowej** kolumna zawiera dane sygnatury czasowej w dzienniku serwera, ale żadne dane nie są wyświetlone dla **TimeElapsed**, **źródła**, i  **Docelowy** kolumn, które nie są częścią dziennik serwera.
> 
> 

Oprócz przy użyciu usługi Azure Storage układów widoku, można również zdefiniować i zapisać układów widoku. Można wybrać inne wymagane pola do grupowania danych i zapisać grupowanie jako część programu niestandardowego układu.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Zastosuj reguły koloru do siatki analizy
Zasoby magazynu także reguły koloru, które oferują element wizualny oznacza do identyfikacji różnych typów błędów w siatce analizy. Kolor wstępnie zdefiniowane reguły dotyczą błędy HTTP, aby były wyświetlane tylko w przypadku śledzenia serwera dziennika i sieci.

Aby zastosować reguły koloru, wybierz **reguły koloru** na wstążce narzędzi. Zobaczysz reguły koloru usługi Azure Storage w menu. Samouczek, wybierz **klientów z błędami (StatusCode w przedziale od 400 do 499)**, jak pokazano na rysunku poniżej.

![Układ widoku magazynu Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Oprócz przy użyciu reguły koloru usługi Azure Storage, można również zdefiniować i zapisać reguły koloru.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Grupy i filtrowanie danych dziennika, aby znaleźć błędy 400 zakresu
Następnie firma Microsoft będzie grupy i filtrowanie danych dziennika, aby znaleźć wszystkie błędy w zakresie od 400.

1. Zlokalizuj **StatusCode** kolumny w siatce analizy, kliknij prawym przyciskiem myszy kolumny, nagłówek i wybierz **grupy**.
2. Następnie grupowania **ClientRequestId** kolumny. Zobaczysz danych w siatce analizy teraz jest zorganizowana według kodu stanu oraz identyfikator żądania klienta.
3. Wyświetl okno narzędzia filtr widoku, jeśli nie jest już wyświetlany. Na wstążce narzędzi, wybierz **okna narzędzi**, następnie **filtr widoku**.
4. Aby filtrować dane dziennika, aby wyświetlić tylko błędy 400 zakres, należy dodać następujące kryteria filtru w celu **filtr widoku** okna, a następnie kliknij przycisk **Zastosuj**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

Na rysunku poniżej przedstawiono wyniki tego grupowanie i filtrowanie. Rozszerzanie **ClientRequestID** pole poniżej grupowania dla kodu stanu 409, na przykład przedstawia operację, która spowodowała ten kod stanu.

![Układ widoku magazynu Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Po zastosowaniu tego filtru, zobaczysz, że wiersze z dziennika klienta są wyłączone, co klient dziennika nie zawiera **StatusCode** kolumny. Rozpoczynać się od znaku firma Microsoft może przejrzeć serwera i dzienniki śledzenia sieci, aby zlokalizować błędów 404, a następnie zostanie zwrócona w dzienniku klienta w celu zbadania operacji klienta, które doprowadziły do nich.

> [!NOTE]
> Można filtrować według **StatusCode** kolumny i Wyświetl dane z wszystkich trzech dzienników, w tym dziennika klienta, jeśli dodajesz wyrażenie filtru, która zawiera wpisy dziennika, gdy kod stanu jest null. Do utworzenia tego wyrażenia filtru, należy użyć:
> 
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
> 
> Ten filtr zwraca wszystkie wiersze z klienta dziennika i tylko wiersze z serwera dziennika i dzienników HTTP gdy kod stanu jest większa niż 400. W przypadku zastosowania do układu widoku pogrupowane według Identyfikatora żądania klienta i moduł, możesz wyszukiwać lub przewiń za pomocą wpisów dziennika, aby znaleźć te, których wszystkie trzy dzienniki są reprezentowane.   
> 
> 

### <a name="filter-log-data-to-find-404-errors"></a>Filtrowanie danych dziennika można znaleźć błędów 404
Zasoby magazynu obejmują wstępnie zdefiniowanych filtrów, których można użyć, aby zawęzić zakres danych dziennika, aby znaleźć błędy lub trendy, którego szukasz. Następnie będzie zastosować dwa filtry wstępnie zdefiniowane: jeden filtrujące serwera i błędów 404 dzienniki śledzenia sieci i który filtruje dane dotyczące określonego przedziału czasu.

1. Wyświetl okno narzędzia filtr widoku, jeśli nie jest już wyświetlany. Na wstążce narzędzi, wybierz **okna narzędzi**, następnie **filtr widoku**.
2. W oknie filtru widoku wybierz **biblioteki**i wyszukiwania na `Azure Storage` można znaleźć magazynu Azure filtry. Wybierz filtr **404 (nie znaleziono) wiadomości we wszystkich dziennikach**.
3. Wyświetl **biblioteki** menu ponownie i Znajdź i zaznacz **globalny filtr czasu**.
4. Edytuj sygnatury czasowe, pokazane na filtrze zakresu, który chcesz wyświetlić. Pomoże to zawęzić zakres danych do analizy.
5. Filtr powinien być podobny do poniższego przykładu. Kliknij przycisk **Zastosuj** Aby zastosować filtr do siatki analizy.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Układ widoku magazynu Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analizowanie danych dziennika
Teraz, grupowane i filtrować dane należy zbadać szczegóły poszczególnych żądań, które wygenerowały błędy 404. W układzie bieżącego widoku dane są grupowane według Identyfikatora żądania klienta, następnie według źródła dziennika. Firma Microsoft filtrowania żądań w polu StatusCode, w którym znajduje się 404, firma Microsoft będzie wyświetlanych tylko serwera i danych śledzenia sieci, nie dane dziennika klienta.

Na ilustracji poniżej pokazano określone żądanie, w którym operacji pobrania obiektu Blob zwróciło 404, ponieważ obiektu blob nie istnieje. Należy pamiętać, że niektóre kolumny zostały usunięte z widoku standardowego w celu wyświetlenia odpowiednich danych.

![Filtrowane Server i dzienniki śledzenia sieci](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Następnie firma Microsoft będzie mieć związek tego Identyfikatora żądania klienta z danych dziennika klienta, aby zobaczyć, jakie akcje klienta miał w momencie wystąpienia błędu. Można wyświetlić nowy widok siatki analizy dla tej sesji wyświetlić dane dziennika klienta, który otworzy się w drugiej karty:

1. Po pierwsze, skopiować wartość **ClientRequestId** pole do Schowka. Aby to zrobić, wybierając albo wiersza, lokalizowania **ClientRequestId** pola, klikając wartość danych, a następnie wybierając **kopii "ClientRequestId"**.
2. Na wstążce narzędzi, wybierz **nowy podgląd**, a następnie wybierz pozycję **siatki analizy** na otwarcie nowej karty. Nowa karta przedstawia wszystkie dane w plikach dziennika bez grupowania, filtrowania lub reguły koloru.
3. Na wstążce narzędzi, wybierz **układ widoku**, a następnie wybierz pozycję **wszystkie kolumny klienta .NET** w obszarze **usługi Azure Storage** sekcji. Układ ten widok przedstawia dane z klienta dziennika, a także dzienniki śledzenia serwera i sieci. Domyślnie jest sortowana na **MessageNumber** kolumny.
4. Następnie wyszukaj w dzienniku klienta dla identyfikatora żądania klienta. Na wstążce narzędzi, wybierz **znaleźć wiadomości**, następnie określenia niestandardowego filtru na identyfikator żądania klienta w **znaleźć** pola. Filtru, określając własnego Identyfikatora żądania klienta, należy użyć następującej składni:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Analizator komunikatów lokalizuje i wybiera pierwszy wpis dziennika, jeśli kryteria wyszukiwania odpowiada identyfikator żądania klienta. W dzienniku klienta znajdują się kilka wpisy dla każdego Identyfikatora żądania klienta, dlatego warto grupować je na **ClientRequestId** pola, aby ułatwić widoczne wszystkie razem. Na ilustracji poniżej przedstawia wszystkie komunikaty dziennika klienta dla określonego klienta identyfikator żądania.

![Wyświetlanie dziennika klienta błędów 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Dane wyświetlane w widoku układów w tych dwóch kart można analizować dane żądania, aby określić, co powoduje błąd. Można też przyjrzeć się żądań, które poprzedzone ta, aby zobaczyć, jeśli poprzednie zdarzenie może spowodowały błąd 404. Na przykład możesz przejrzeć wpisy dziennika klienta poprzedzających tego Identyfikatora żądania klienta, aby określić, czy obiektu blob mogła zostać usunięta, czy błąd został spowodowany aplikacji klienckiej, wywoływania CreateIfNotExists API na kontener lub obiekt blob. W dzienniku klienta można znaleźć adres obiektu blob w **opis** pola; na serwerze i dzienniki śledzenia sieci, informacje te pojawiają się w **Podsumowanie** pola.

Znając adres obiektu blob, które zwróciło błąd 404, możesz można zbadać. W przypadku wyszukiwania wpisów dziennika dla innych komunikatów skojarzone z operacjami w tym samym obiekcie blob, można sprawdzić, czy klient wcześniej usunięty jednostki.

## <a name="analyze-other-types-of-storage-errors"></a>Analizowanie inne typy błędów magazynu
Teraz, po zapoznaniu się z za pomocą analizatora komunikatów do analizowania danych dziennika, można przeanalizować inne typy błędów przy użyciu widoku układów, reguły koloru i wyszukiwanie filtrowania. W poniższej tabeli wymieniono niektóre problemy, które można napotkać i kryteria filtrowania, używanych do ich lokalizację. Aby uzyskać więcej informacji dotyczących tworzenia filtrów i Message Analyzer filtrowania języka, zobacz [filtrowania danych komunikat](http://technet.microsoft.com/library/jj819365.aspx).

| Aby zbadać... | Użycie wyrażenia filtru... | Wyrażenie ma zastosowanie do dziennika (klienta, serwera, sieci, wszystkie) |
| --- | --- | --- |
| Nieoczekiwane opóźnienia dotyczące dostarczania wiadomości w kolejce |Zawiera AzureStorageClientDotNetV4.Description "Ponawianie nie można wykonać operacji." |Klient |
| HTTP wzrost PercentThrottlingError |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |Sieć |
| Wzrost PercentTimeoutError |HTTP.Response.StatusCode   == 500 |Sieć |
| Wzrost PercentTimeoutError (wszystkie wersje) |*StatusCode   == 500 |Wszyscy |
| Wzrost PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Klient |
| Komunikaty HTTP 403 (zabroniony) |PROTOKÓŁ HTTP. Response.StatusCode == 403 |Sieć |
| HTTP 404 (nie znaleziono) wiadomości |PROTOKÓŁ HTTP. Response.StatusCode == 404 |Sieć |
| 404 (wszystkie wersje) |* StatusCode == 404 |Wszyscy |
| Udostępnione problem autoryzacji podpis dostępu (SAS) |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Sieć |
| HTTP 409 (konflikt) wiadomości |PROTOKÓŁ HTTP. Response.StatusCode == 409 |Sieć |
| 409 (wszystkie wersje) |* StatusCode == 409 |Wszyscy |
| Działania o stanie transakcji ClientOtherErrors ma PercentSuccess małej lub analityka wpisy dziennika |AzureStorageLog.RequestStatus == "ClientOtherError" |Serwer |
| Ostrzeżenie Nagle'a |((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) i (AzureStorageLog.RequestPacketSize < 1460) i (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200) |Serwer |
| Zakres czasu w dziennikach serwera i sieci |#Timestamp > = 2014-10-20T16:36:38 i #Timestamp < = 2014-10-20T16:36:39 |Serwer, sieci |
| Zakres czasu w dziennikach serwera |AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 i AzureStorageLog.Timestamp < = 2014-10-20T16:36:39 |Serwer |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rozwiązywania problemów scenariuszy end-to-end w usłudze Azure Storage zobacz następujące zasoby:

* [Monitor, diagnose, and troubleshoot Microsoft Azure Storage (Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage)](storage-monitoring-diagnosing-troubleshooting.md)
* [Analityka magazynu](http://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitor konta magazynu w portalu Azure](storage-monitor-storage-account.md)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Przewodnik operacyjny analizatora wiadomości firmy Microsoft](http://technet.microsoft.com/library/jj649776.aspx)
