---
title: Ciągły Eksport danych telemetrycznych z usługi Application Insights | Dokumentacja firmy Microsoft
description: Dane diagnostyczne i użycia są eksportowane do magazynu na platformie Microsoft Azure i pobrać ją stamtąd.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 71e70962a8c55d397b6261571cfef4a126d3e8b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899421"
---
# <a name="export-telemetry-from-application-insights"></a>Eksportowanie telemetrii z usługi Application Insights
Czy chcesz zachować dane telemetryczne przez czas dłuższy niż okres przechowywania standardowa? Lub przetwarzać dane w jakiś sposób wyspecjalizowane? Eksport ciągły jest idealny dla tego. Zdarzenia, które są widoczne w portalu usługi Application Insights można wyeksportować do magazynu na platformie Microsoft Azure w formacie JSON. W tym miejscu możesz pobrać dane i napisać kod, które możesz: musisz go przetworzyć.  

Przed skonfigurowaniem Eksport ciągły istnieje kilka rozwiązań alternatywnych, z których warto wziąć pod uwagę:

* Przycisk Eksportuj w górnej części bloku metryk lub wyszukiwanie umożliwia przeniesienie tabele i wykresy do arkusza kalkulacyjnego programu Excel.

* [Analiza](../../azure-monitor/app/analytics.md) udostępnia zaawansowany język zapytań dla danych telemetrycznych. Można także eksportować wyniki.
* Jeśli zajmujesz się na [eksplorować dane w usłudze Power BI](../../azure-monitor/app/export-power-bi.md ), możesz to zrobić, bez korzystania z eksportu ciągłego.
* [Interfejsu API REST dostępu do danych](https://dev.applicationinsights.io/) pozwala uzyskiwać dostęp do danych telemetrycznych programowo.
* Można także przejść do ustawień [eksportu ciągłego przy użyciu programu Powershell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Eksportu ciągłego dane są kopiowane do magazynu (gdzie pozostawał dla tak długo, jak chcesz), jest nadal dostępny w usłudze Application Insights dla zwykłego [okres przechowywania](../../azure-monitor/app/data-retention-privacy.md).

## <a name="continuous-export-advanced-storage-configuration"></a>Konfiguracja usługi storage zaawansowane ciągłego eksportu

Eksport ciągły **nie obsługuje** następujące funkcje usługi Azure storage/konfiguracje:

* Korzystanie z [zapory sieci Wirtualnej/usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security) w połączeniu z usługą Azure Blob storage.

* [Niezmienny magazyn](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) dla usługi Azure Blob storage.

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="setup"></a> Utwórz eksport ciągły
1. W zasobie usługi Application Insights dla aplikacji, otwórz Eksport ciągły i wybierz polecenie **Dodaj**:

2. Wybierz dane telemetryczne typy danych, które chcesz wyeksportować.

3. Utwórz lub wybierz [konta usługi Azure storage](../../storage/common/storage-introduction.md) miejscu, w którym chcesz przechowywać dane. Aby uzyskać więcej informacji na temat cen opcje magazynu, odwiedź stronę [official będzie przydatna stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/).

    > [!Warning]
    > Domyślna lokalizacja magazynu zostanie ustawiona do tego samego regionu geograficznego, co zasób usługi Application Insights. Jeśli będą przechowywane w innym regionie, może spowodować naliczenie opłaty za transfer.

    ![Kliknij przycisk Dodaj, eksportowanie miejsce docelowe konto magazynu, a następnie utwórz nowy magazyn lub wybierz istniejący magazyn](./media/export-telemetry/02-add.png)

4. Utwórz lub Wybierz kontener w magazynie:

    ![Kliknij Wybierz typy zdarzeń](./media/export-telemetry/create-container.png)

Po utworzeniu eksportu zacznie pracę. Uzyskujesz tylko danych, przychodzący po utworzeniu eksportu.

Może to być opóźnienie około godziny, zanim dane zostaną wyświetlone w magazynie.

### <a name="to-edit-continuous-export"></a>Aby edytować Eksport ciągły

Jeśli chcesz zmienić typy zdarzeń później, po prostu Edytuj Eksport:

![Kliknij Wybierz typy zdarzeń](./media/export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>Aby zatrzymać Eksport ciągły

Aby zatrzymać eksportu, kliknij opcję Wyłącz. Po kliknięciu ponownie włączyć, eksportowania zostanie uruchomiony ponownie za pomocą nowych danych. Nie otrzymasz dane dostarczone w portalu podczas eksportowania została wyłączona.

Aby zatrzymać trwale eksportu, usuń go. Ten sposób nie powoduje usunięcia danych z magazynu.

### <a name="cant-add-or-change-an-export"></a>Nie można dodać lub zmienić eksportu?
* Aby dodać lub zmienić eksportu, musisz mieć prawa dostępu współautora, Application Insights współautora lub właściciela. [Dowiedz się więcej o rolach][roles].

## <a name="analyze"></a> Jakie zdarzenia uzyskać?
Wyeksportowane dane są nieprzetworzone dane telemetryczne, które są uzyskiwane z aplikacji, z tą różnicą, że możemy dodać danych lokalizacji, w którym możemy obliczyć z adresu IP klienta.

Dane, które zostały odrzucone przez [próbkowania](../../azure-monitor/app/sampling.md) nie są objęte wyeksportowane dane.

Inne metryki obliczeniowe nie są uwzględniane. Na przykład firma Microsoft nie należy eksportować średnie wykorzystanie procesora CPU, ale wyeksportować nieprzetworzone dane telemetryczne, z którego jest obliczana średnia.

Dane obejmują także wyniki dowolnego [testy sieci web dostępności](../../azure-monitor/app/monitor-web-app-availability.md) , które zostały skonfigurowane.

> [!NOTE]
> **Pobieranie próbek.** Jeśli Twoja aplikacja przesyła dużą ilość danych, funkcja próbkowanie może działać i wysyłanie tylko ułamka telemetrii wygenerowanej. [Dowiedz się więcej na temat próbkowania.](../../azure-monitor/app/sampling.md)
>
>

## <a name="get"></a> Sprawdzanie danych
Można sprawdzić magazynu bezpośrednio w portalu. Kliknij przycisk **Przeglądaj**, wybierz konto magazynu, a następnie otwórz **kontenery**.

Aby przeprowadzić inspekcję magazynu platformy Azure w programie Visual Studio, otwórz **widoku**, **programu Cloud Explorer**. (Jeśli nie masz tego polecenia menu, należy zainstalować zestaw Azure SDK: Otwórz **nowy projekt** okna dialogowego, rozwiń węzeł Visual C#/w chmurze i wybierz polecenie **Pobierz zestaw Microsoft Azure SDK dla platformy .NET**.)

Po otwarciu usługi magazynu obiektów blob, zobaczysz kontener za pomocą zestawu plików obiektów blob. Identyfikator URI każdy plik pochodzi z nazwa zasobu usługi Application Insights, jego klucz Instrumentacji i telemetrii — wpisz datę i godzinę. (Nazwa zasobu jest tylko małe litery i łączniki są pomijane przez klucz instrumentacji.)

![Inspekcja magazynu obiektów blob za pomocą odpowiedniego narzędzia](./media/export-telemetry/04-data.png)

Data i godzina są czasem UTC i gdy telemetrii został złożony w magazynie — nie czas, który został wygenerowany. Dlatego jeśli piszesz kod, aby pobrać dane, ona przechodzić przez liniowo danych.

Poniżej przedstawiono formularz ścieżki:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Lokalizacja

* `blobCreationTimeUtc` jest godzina utworzenia obiektu blob wewnętrznego przemieszczania magazynu
* `blobDeliveryTimeUtc` czas, gdy obiekt blob jest kopiowany do magazynu docelowego eksportu

## <a name="format"></a> Format danych
* Każdy obiekt blob jest plik tekstowy, który zawiera wiele "\n'-separated wierszy. Zawiera ona dane telemetryczne przetworzone w okresie około pół minuty.
* Każdy wiersz reprezentuje punkt danych telemetrii, takie jak żądanie lub wyświetlenie strony.
* Każdy wiersz jest niesformatowany dokumentu JSON. Jeśli znajdują się i stare go, otwórz go w programie Visual Studio i wybierz pozycję Edytuj, zaawansowane, plik w formacie:

![Widok danych telemetrycznych za pomocą odpowiedniego narzędzia](./media/export-telemetry/06-json.png)

Czasów trwania są w dziesięciomilionowych częściach sekundy, gdzie znaczniki 10 000 = 1 ms. Na przykład te wartości wyświetlane przez czas 1 ms, aby wysłać żądania z przeglądarki, 3 ms do odbierania i 1.8 s do przetwarzania tej strony w przeglądarce:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Szczegółowe dane modelu odwołań dla typów właściwości i wartości.](export-data-model.md)

## <a name="processing-the-data"></a>Przetwarzanie danych
Na małą skalę można napisać działał kod służący do pobierania danych, przeczytaj go do arkusza kalkulacyjnego i tak dalej. Na przykład:

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

Dla większego przykładu kodu, zobacz [za pomocą roli procesu roboczego][exportasa].

## <a name="delete"></a>Usuń stare dane
Odpowiedzialność za zarządzanie pojemnością magazynu i usuwania starych danych, jeśli to konieczne.

## <a name="if-you-regenerate-your-storage-key"></a>Jeśli należy ponownie wygenerować klucz magazynu...
Jeśli zmienisz klucz do magazynu, Eksport ciągły przestaną działać. Zostanie wyświetlone powiadomienie na swoim koncie platformy Azure.

Otwórz blok Eksport ciągły i edytowanie eksportu. Edytowanie docelowego eksportu, ale pozostaw tego samego magazynu, które są wybrane. Kliknij przycisk OK, aby potwierdzić.

![Edytuj Eksport ciągły otwierających i zamykających ją miejsce docelowe eksportu.](./media/export-telemetry/07-resetstore.png)

Eksport ciągły zostanie uruchomiony ponownie.

## <a name="export-samples"></a>Eksportowanie próbki

* [Eksportowanie do bazy danych SQL przy użyciu usługi Stream Analytics][exportasa]
* [Stream Analytics przykład 2](export-stream-analytics.md)

W przypadku wyższych skal oferowanych, należy wziąć pod uwagę [HDInsight](https://azure.microsoft.com/services/hdinsight/) -klastrów Hadoop w chmurze. HDInsight oferuje różne technologie do zarządzania i analizowanie danych big data i można jej używać do przetwarzania danych, które zostały wyeksportowane z usługi Application Insights.

## <a name="q--a"></a>Pytania i odpowiedzi
* *Ale wszystko, co chcę jest przeznaczony do jednorazowego pobrania wykresu.*  

    Tak, możesz to zrobić. W górnej części bloku kliknij **Eksport danych**.
* *Czy mogę skonfigurować Eksport, ale nie ma żadnych danych w magazynie użytkownika.*

    Usługi Application Insights odebrał żadnych danych telemetrycznych z Twojej aplikacji ponieważ przeprowadza się konfigurację eksportu? Zostanie wyświetlony tylko nowe dane.
* *Podjęto próbę ustawienia eksportu, ale nastąpiła odmowa dostępu*

    Jeśli konto jest własnością Twojej organizacji, trzeba być członkiem grupy Właściciele lub współautorzy.
* *Czy mogę wyeksportować bezpośrednio do własnego magazynu w środowisku lokalnym?*

    Nie, Niestety. Nasz mechanizm eksportu obecnie działa tylko z usługą Azure storage w tej chwili.  
* *Czy istnieje ograniczenie ilości danych, które można umieścić w magazynie użytkownika?*

    Nie. Firma Microsoft będzie przechowywać wypychanie danych dopóki nie usuniesz eksportu. Zatrzymamy, gdy osiągnęliśmy zewnętrzne limity dla usługi blob storage, ale jest to dość duży. To pozwala kontrolować, ile pamięci masowej, możesz użyć.  
* *Jak wiele obiektów blob powinien zostać wyświetlony w magazynie?*

  * Dla każdego typu danych wybrane do wyeksportowania nowy obiekt blob jest tworzony na minutę (Jeśli dane są dostępne).
  * Ponadto w przypadku aplikacji o dużym natężeniu ruchu partycji dodatkowe jednostki są przydzielone. W takim przypadku każda jednostka tworzy obiekt blob, co minutę.
* *Czy mogę ponownie wygenerowano klucz do magazynu, lub zmienić nazwę kontenera, a teraz eksportu nie działa.*

    Edytuj Eksport i otwórz blok docelowy eksportu. Pozostaw tego samego magazynu, tak jak poprzednio zaznaczone, a następnie kliknij przycisk OK, aby potwierdzić. Eksportowanie zostanie uruchomiony ponownie. Jeśli zmiany w ciągu ostatnich kilku dni, nie dojdzie do utraty danych.
* *Czy można wstrzymać eksportu?*

    Tak. Kliknij opcję Wyłącz.

## <a name="code-samples"></a>Przykłady kodu

* [Stream Analytics próbki](export-stream-analytics.md)
* [Eksportowanie do bazy danych SQL przy użyciu usługi Stream Analytics][exportasa]
* [Szczegółowe dane modelu odwołań dla typów właściwości i wartości.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
