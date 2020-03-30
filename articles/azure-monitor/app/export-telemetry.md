---
title: Ciągły eksport danych telemetrycznych z usługi Application Insights | Dokumenty firmy Microsoft
description: Eksportuj dane diagnostyczne i dane użycia do magazynu na platformie Microsoft Azure i pobieraj je z tego miejsca.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: f6afe42e483ab7ad5810169fc301946c75308c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298283"
---
# <a name="export-telemetry-from-application-insights"></a>Eksportowanie telemetrii z usługi Application Insights
Chcesz zachować dane telemetryczne dłużej niż standardowy okres przechowywania? Lub przetworzyć go w jakiś wyspecjalizowany sposób? Eksport ciągły jest do tego idealny. Zdarzenia widoczne w portalu usługi Application Insights można wyeksportować do magazynu na platformie Microsoft Azure w formacie JSON. Stamtąd możesz pobrać swoje dane i napisać dowolny kod potrzebny do ich przetworzenia.  

Przed skonfigurowaniem eksportu ciągłego istnieją pewne alternatywy, które warto rozważyć:

* Przycisk Eksportuj u góry karty metryki lub wyszukiwanie umożliwia przenoszenie tabel i wykresów do arkusza kalkulacyjnego programu Excel.

* [Analytics](../../azure-monitor/app/analytics.md) zapewnia zaawansowany język zapytań dla telemetrii. Może również eksportować wyniki.
* Jeśli chcesz [eksplorować dane w usłudze Power BI,](../../azure-monitor/app/export-power-bi.md )możesz to zrobić bez użycia ciągłego eksportu.
* [Interfejs API REST dostępu do danych](https://dev.applicationinsights.io/) umożliwia programowy dostęp do telemetrii.
* Można również uzyskać dostęp do ciągłego eksportu konfiguracji [za pośrednictwem programu Powershell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Po ciągłym eksporcie kopiuje dane do magazynu (gdzie może pozostać tak długo, jak chcesz), jest nadal dostępna w usłudze Application Insights dla zwykłego [okresu przechowywania](../../azure-monitor/app/data-retention-privacy.md).

## <a name="continuous-export-advanced-storage-configuration"></a>Konfiguracja zaawansowanego magazynu ciągłego eksportu

Eksport ciągły **nie obsługuje** następujących funkcji/konfiguracji magazynu platformy Azure:

* Korzystanie z [zapór sieci wirtualnej/usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security) w połączeniu z magazynem obiektów blob platformy Azure.

* [Niezmienne magazynu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) dla magazynu obiektów blob platformy Azure.

* [Usługa Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="create-a-continuous-export"></a><a name="setup"></a>Tworzenie eksportu ciągłego

1. W zasobie Usługi Application Insights dla aplikacji w obszarze Konfigurowanie po lewej stronie otwórz opcję Eksportowanie ciągłe i wybierz pozycję **Dodaj:**

2. Wybierz typy danych telemetrycznych, które chcesz wyeksportować.

3. Utwórz lub wybierz [konto magazynu platformy Azure,](../../storage/common/storage-introduction.md) na którym chcesz przechowywać dane. Aby uzyskać więcej informacji na temat opcji cennika pamięci masowej, odwiedź [oficjalną stronę cennika](https://azure.microsoft.com/pricing/details/storage/).

     Kliknij pozycję Dodaj, Eksportuj miejsce docelowe, Konto magazynu, a następnie utwórz nowy magazyn lub wybierz istniejący magazyn.

    > [!Warning]
    > Domyślnie lokalizacja magazynu zostanie ustawiona na ten sam region geograficzny co zasób usługi Application Insights. Jeśli przechowujesz w innym regionie, możesz ponieść opłaty za przelewy.

4. Utwórz lub wybierz kontener w magazynie.

Po utworzeniu eksportu zaczyna on być w toku. Otrzymasz tylko dane, które nadejdzie po utworzeniu eksportu.

Może wystąpić opóźnienie około godziny przed wyświetleniem danych w magazynie.

Po zakończeniu pierwszego eksportu znajdziesz strukturę podobną do następującej w kontenerze magazynu obiektów Blob platformy Azure: (Będzie się to różnić w zależności od danych, które zbierasz).

|Nazwa | Opis |
|:----|:------|
| [Dostępność](export-data-model.md#availability) | Raporty [dostępności testów sieci web](../../azure-monitor/app/monitor-web-app-availability.md).  |
| [Zdarzenie](export-data-model.md#events) | Zdarzenia niestandardowe generowane przez [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent). 
| [Wyjątki](export-data-model.md#exceptions) |Zgłasza [wyjątki](../../azure-monitor/app/asp-net-exceptions.md) na serwerze i w przeglądarce.
| [Wiadomości](export-data-model.md#trace-messages) | Wysyłane przez [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)i [przez karty rejestrowania](../../azure-monitor/app/asp-net-trace-logs.md).
| [Metryki](export-data-model.md#metrics) | Generowane przez wywołania interfejsu API metryki.
| [Liczniki wydajności](export-data-model.md) | Liczniki wydajności zbierane przez usługa Application Insights.
| [Żądania](export-data-model.md#requests)| Wysyłane przez [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Moduły standardowe używają tego do raportów czas odpowiedzi serwera, mierzony na serwerze.| 

### <a name="to-edit-continuous-export"></a>Aby edytować eksport ciągły

Kliknij ciągłe eksportowanie i wybierz konto magazynu do edycji.

### <a name="to-stop-continuous-export"></a>Aby zatrzymać ciągły eksport

Aby zatrzymać eksport, kliknij przycisk Wyłącz. Po ponownym kliknięciu przycisku Włącz eksport zostanie ponownie uruchomiony z nowymi danymi. Nie otrzymasz danych, które dotarły do portalu, gdy eksport został wyłączony.

Aby zatrzymać eksport na stałe, usuń go. W ten sposób nie powoduje usunięcia danych z magazynu.

### <a name="cant-add-or-change-an-export"></a>Nie możesz dodać lub zmienić eksportu?
* Aby dodać lub zmienić eksport, potrzebujesz praw dostępu Właściciela, Współautora lub Współautora usługi Application Insights. [Dowiedz się więcej o rolach][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a>Jakie wydarzenia otrzymujesz?
Eksportowane dane to nieprzetworzona telemetria, którą otrzymujemy od aplikacji, z tą różnicą, że dodajemy dane o lokalizacji, które obliczamy na podstawie adresu IP klienta.

Dane, które zostały odrzucone przez [próbkowanie,](../../azure-monitor/app/sampling.md) nie są uwzględniane w wyeksportowanych danych.

Inne obliczone dane nie są uwzględniane. Na przykład nie eksportujemy średniego wykorzystania procesora CPU, ale eksportujemy nieprzetworzone dane telemetryczne, z których oblicza się średnią.

Dane obejmują również wyniki wszelkich [testów sieci web dostępności,](../../azure-monitor/app/monitor-web-app-availability.md) które zostały skonfigurowane.

> [!NOTE]
> **Pobierania próbek.** Jeśli aplikacja wysyła dużo danych, funkcja próbkowania może działać i wysyłać tylko ułamek wygenerowanej danych telemetrycznych. [Dowiedz się więcej na temat próbkowania.](../../azure-monitor/app/sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a>Sprawdź dane
Magazyn można sprawdzić bezpośrednio w portalu. Kliknij pozycję narzędzia główne w menu po lewej stronie, u góry, gdzie znajduje się napis "Usługi platformy Azure" wybierz **konta magazynu**, wybierz nazwę konta magazynu, na stronie przeglądu wybierz **obiekty Blob** w obszarze usługi, a na koniec wybierz nazwę kontenera.

Aby sprawdzić magazyn platformy Azure w programie Visual Studio, otwórz **program View**, **Cloud Explorer**. (Jeśli nie masz tego polecenia menu, musisz zainstalować zestaw SDK platformy Azure: Otwórz okno dialogowe **Nowy projekt,** rozwiń węzeł Visual C#/Cloud i wybierz pozycję **Pobierz zestaw Microsoft Azure SDK dla platformy .NET).**

Po otwarciu magazynu obiektów blob zostanie wyświetlony kontener z zestawem plików obiektów blob. Identyfikator URI każdego pliku pochodzący z nazwy zasobu usługi Application Insights, jego klucz instrumentacji, typ telemetrii/data/godzina. (Nazwa zasobu jest mała litera, a klucz instrumentacji pomija myślniki).)

![Sprawdź magazyn obiektów blob za pomocą odpowiedniego narzędzia](./media/export-telemetry/04-data.png)

Data i godzina są UTC i są, gdy dane telemetryczne został złożony w magazynie — nie czas został wygenerowany. Więc jeśli piszesz kod, aby pobrać dane, może poruszać się liniowo przez dane.

Oto forma ścieżki:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Lokalizacja

* `blobCreationTimeUtc`to czas, kiedy obiekt blob został utworzony w wewnętrznej magazynie przemieszczania
* `blobDeliveryTimeUtc`to czas, kiedy obiekt blob jest kopiowany do magazynu docelowego eksportu

## <a name="data-format"></a><a name="format"></a>Format danych
* Każdy obiekt blob jest plikiem tekstowym, który zawiera wiele wierszy oddzielonych od '\n'. Zawiera dane telemetryczne przetwarzane w okresie około pół minuty.
* Każdy wiersz reprezentuje punkt danych telemetrii, taki jak żądanie lub widok strony.
* Każdy wiersz jest niesformatowanym dokumentem JSON. Jeśli chcesz usiąść i wpatrywać się w niego, otwórz go w programie Visual Studio i wybierz polecenie Edytuj, Zaawansowane, Formatuj plik:

![Wyświetlanie danych telemetrycznych za pomocą odpowiedniego narzędzia](./media/export-telemetry/06-json.png)

Czas trwania jest w kleszczach, gdzie 10 000 kleszczy = 1 ms. Na przykład te wartości pokazują czas 1 ms, aby wysłać żądanie z przeglądarki, 3 ms, aby je otrzymać, i 1,8 s do przetworzenia strony w przeglądarce:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Szczegółowe odwołanie do modelu danych dla typów właściwości i wartości.](export-data-model.md)

## <a name="processing-the-data"></a>Przetwarzanie danych
Na małą skalę możesz napisać kod, aby rozdzielić dane, odczytać je w arkuszu kalkulacyjnym itd. Przykład:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Aby uzyskać większy przykład kodu, zobacz [przy użyciu roli procesu roboczego][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Usuwanie starych danych
Użytkownik jest odpowiedzialny za zarządzanie pojemnością magazynu i usuwanie starych danych, jeśli to konieczne.

## <a name="if-you-regenerate-your-storage-key"></a>Jeśli ponownie wygenerujesz klucz pamięci masowej...
Jeśli zmienisz klucz w magazynie, ciągły eksport przestanie działać. Powiadomienie zostanie wyświetlone na koncie platformy Azure.

Otwórz kartę Eksport ciągły i edytuj eksport. Edytuj miejsce docelowe eksportu, ale po prostu pozostaw tę samą ilość miejsca. Kliknij przycisk OK, aby potwierdzić.

Ciągły eksport zostanie uruchomiony ponownie.

## <a name="export-samples"></a>Eksportowanie próbek

* [Eksportowanie do bazy danych SQL przy użyciu usługi Stream Analytics][exportasa]
* [Przykładowa analiza strumienia 2](export-stream-analytics.md)

Na większych skalach należy wziąć pod uwagę [HDInsight](https://azure.microsoft.com/services/hdinsight/) — Klastry Hadoop w chmurze. Usługa HDInsight udostępnia wiele technologii do zarządzania dużymi zbiorami danych i ich analizowania, a także do przetwarzania danych wyeksportowanych z usługi Application Insights.

## <a name="q--a"></a>Pytania i odpowiedzi
* *Ale wszystko, czego chcę, to jednorazowe pobranie wykresu.*  

    Tak, możesz to zrobić. U góry karty kliknij pozycję **Eksportuj dane**.
* *Skonfigurowałem eksport, ale w moim sklepie nie ma żadnych danych.*

    Czy usługa Application Insights otrzymała dane telemetryczne z aplikacji od czasu skonfigurowania eksportu? Otrzymasz tylko nowe dane.
* *Próbowałem skonfigurować eksport, ale odmówiono mi dostępu*

    Jeśli konto jest własnością organizacji, musisz być członkiem grupy właścicieli lub współautorów.
* *Czy mogę eksportować bezpośrednio do własnego sklepu lokalnego?*

    Nie, przepraszam. Nasz aparat eksportu działa obecnie tylko z usługą Azure Storage w tej chwili.  
* *Czy istnieje jakiś limit ilości danych, które można umieścić w moim sklepie?*

    Nie. Będziemy wciskać dane do momentu usunięcia eksportu. Zatrzymamy się, jeśli osiągniemy zewnętrzne limity przechowywania obiektów blob, ale to dość ogromne. To do Ciebie, aby kontrolować, ile miejsca używasz.  
* *Ile obiektów blob powinienem zobaczyć w magazynie?*

  * Dla każdego typu danych wybranego do wyeksportowania nowy obiekt blob jest tworzony co minutę (jeśli dane są dostępne).
  * Ponadto w przypadku aplikacji o dużym natężeniu ruchu przydzielane są dodatkowe jednostki partycji. W takim przypadku każda jednostka tworzy obiekt blob co minutę.
* *Ponownie wygenerowano klucz do magazynu lub zmieniłem nazwę kontenera, a teraz eksport nie działa.*

    Edytuj eksport i otwórz kartę miejsca docelowego eksportu. Pozostaw ten sam magazyn zaznaczony jak poprzednio, a następnie kliknij przycisk OK, aby potwierdzić. Eksport zostanie ponownie uruchomiony. Jeśli zmiana nastąpiła w ciągu ostatnich kilku dni, nie utracisz danych.
* *Czy mogę wstrzymać eksport?*

    Tak. Kliknij przycisk Wyłącz.

## <a name="code-samples"></a>Przykłady kodu

* [Przykład analizy strumienia](export-stream-analytics.md)
* [Eksportowanie do bazy danych SQL przy użyciu usługi Stream Analytics][exportasa]
* [Szczegółowe odwołanie do modelu danych dla typów właściwości i wartości.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
