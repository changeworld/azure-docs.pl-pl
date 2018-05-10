---
title: Eksport ciągły dane telemetryczne z usługi Application Insights | Dokumentacja firmy Microsoft
description: Eksportuj dane diagnostyczne i użycia do magazynu w systemie Microsoft Azure i pobrać ją stamtąd.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: mbullwin
ms.openlocfilehash: 942cc355c186b73e0b8802bfbf79ef8b4b39191a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="export-telemetry-from-application-insights"></a>Eksportuj dane telemetryczne z usługi Application Insights
Czy chcesz zachować telemetrii przez czas dłuższy niż okres przechowywania standardowe? Lub przetwarzać dane w jakiś sposób specjalne? Eksport ciągły jest idealna to. Zdarzenia, które są widoczne w portalu usługi Application Insights można wyeksportować do magazynu na platformie Microsoft Azure w formacie JSON. Z tego miejsca można pobrać danych i zapisu, niezależnie od kod należy go przetworzyć.  

Przy użyciu eksportu ciągłego może pociągnąć za sobą dodatkowe opłaty. Sprawdź Twojej [model cenowy](https://azure.microsoft.com/pricing/details/application-insights/).

Przed skonfigurowaniem Eksport ciągły istnieje kilka rozwiązań alternatywnych, które należy wziąć pod uwagę:

* Przycisk Eksportuj w górnej części bloku metryki lub wyszukiwanie umożliwia transfer tabele i wykresy do arkusz kalkulacyjny programu Excel.

* [Analiza](app-insights-analytics.md) zapewnia język kwerendy zaawansowanych telemetrii. Można także wyeksportować wyniki.
* Jeśli szukasz do [eksplorować dane w usłudze Power BI](app-insights-export-power-bi.md), możesz to zrobić bez użycia eksportu ciągłego.
* [Dostępu do danych interfejsu API REST](https://dev.applicationinsights.io/) pozwala uzyskiwać dostęp do telemetrii programowo.
* Można także przejść do instalacji [eksportu ciągłego za pomocą programu Powershell](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/new-azurermapplicationinsightscontinuousexport?view=azurermps-5.7.0).

Po eksportu ciągłego kopiuje dane do magazynu (gdzie pozostawał dla tak długo, jak chcesz), jest on nadal dostępny w usłudze Application Insights dla zwykle [okres przechowywania](app-insights-data-retention-privacy.md).

## <a name="setup"></a> Utwórz eksport ciągły
1. Zasób usługi Application Insights dla aplikacji, otwórz eksportu ciągłego i wybierz polecenie **Dodaj**:

    ![Przewiń w dół i kliknij przycisk eksportu ciągłego](./media/app-insights-export-telemetry/01-export.png)

2. Wybierz typy danych, który chcesz wyeksportować telemetrii.

3. Utwórz lub wybierz [konto magazynu Azure](../storage/common/storage-introduction.md) której chcesz przechowywać dane.

    > [!Warning]
    > Domyślnie zostanie ustawiona lokalizacja magazynu do tego samego regionu geograficznego co zasób usługi Application Insights. Jeśli jest przechowywany w innym regionie, może spowodować naliczenie opłat transferu.

    ![Kliknij przycisk Dodaj, wyeksportować miejsce docelowe konto magazynu, a następnie utwórz nowy magazyn lub wybierz istniejący magazyn](./media/app-insights-export-telemetry/02-add.png)

4. Utwórz lub Wybierz kontener magazynu:

    ![Kliknij przycisk Wybierz typy zdarzeń](./media/app-insights-export-telemetry/create-container.png)

Po utworzeniu eksportu uruchamia przejście. Tylko Pobierz dane przychodzący po utworzeniu eksportu.

Może to być opóźnienie około godziny przed wyświetleniem danych w magazynie.

### <a name="to-edit-continuous-export"></a>Aby edytować Eksport ciągły

Jeśli chcesz zmienić typy zdarzeń, które później, po prostu edytować Eksport:

![Kliknij przycisk Wybierz typy zdarzeń](./media/app-insights-export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>Aby zatrzymać eksportu ciągłego

Aby zatrzymać eksportu, kliknij pozycję Wyłącz. Po kliknięciu włączyć ponownie, eksport zostanie uruchomiony ponownie z nowymi danymi. Nie otrzymasz dane, które dotarły w portalu podczas eksportowania została wyłączona.

Aby zatrzymać trwale eksportu, usuń go. Dzięki temu nie powoduje usunięcia danych z magazynu.

### <a name="cant-add-or-change-an-export"></a>Nie można dodać lub zmienić eksportu?
* Aby dodać lub zmienić eksportu, niezbędne są uprawnienia dostępu do właściciela, współautora lub współautora wgląd w aplikacji. [Dowiedz się więcej o rolach][roles].

## <a name="analyze"></a> Jakie zdarzenia uzyskać?
Wyeksportowane dane jest nieprzetworzone dane telemetryczne, otrzymywanych z aplikacji, ale dodamy danych lokalizacji, które firma Microsoft obliczenia z adresu IP klienta.

Dane, które zostały odrzucone przez [próbkowania](app-insights-sampling.md) nie wchodzi w wyeksportowanych danych.

Inne metryki obliczanej nie są uwzględniane. Na przykład firma Microsoft nie Eksportuj średnie wykorzystanie procesora CPU, ale eksportowania nieprzetworzone dane telemetryczne, z którego jest obliczana średnia.

Dane obejmują także wyniki dowolnego [testów sieci web dostępności](app-insights-monitor-web-app-availability.md) , które zostały skonfigurowane.

> [!NOTE]
> **Próbkowania.** Jeśli aplikacja wyśle dużą ilość danych, funkcję pobierania próbek może działać i wysłać tylko część wygenerowanego telemetrii. [Dowiedz się więcej na temat próbkowania.](app-insights-sampling.md)
>
>

## <a name="get"></a> Sprawdź dane
Możesz sprawdzić magazynu bezpośrednio w portalu. Kliknij przycisk **Przeglądaj**, wybierz konto magazynu, a następnie otwórz **kontenery**.

Aby sprawdzić magazynu Azure w programie Visual Studio, otwórz **widoku**, **Eksplorator chmury**. (Jeśli nie masz tego polecenia menu, należy zainstalować zestaw Azure SDK: Otwórz **nowy projekt** okna dialogowego, rozwiń węzeł Visual C# / w chmurze i wybierz polecenie **pobrać zestaw Microsoft Azure SDK dla platformy .NET**.)

Po otwarciu magazynie obiektów blob, zobaczysz kontener z zestawu plików obiektów blob. Identyfikator URI poszczególnych plików pochodząca z nazwę zasobu usługi Application Insights, jego klucza instrumentacji, telemetrii — typ, datę i godzinę. (Nazwa zasobu jest tylko małe litery i łączniki pomija klucza instrumentacji.)

![Sprawdź magazynu obiektów blob za pomocą odpowiedniego narzędzia](./media/app-insights-export-telemetry/04-data.png)

Data i godzina są UTC i gdy telemetrii został złożony w magazynie — nie czas, który został wygenerowany. Dlatego podczas pisania kodu, aby pobrać dane, jego można przesuwać liniowo danych.

Poniżej przedstawiono formularz ścieżki:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Gdzie

* `blobCreationTimeUtc` Godzina utworzenia obiektu blob w wewnętrznej jest przemieszczania magazynu
* `blobDeliveryTimeUtc` to czas, kiedy obiekt blob jest kopiowany do magazynu docelowego eksportu

## <a name="format"></a> Format danych
* Każdy obiekt blob jest to plik tekstowy, który zawiera wiele "\n'-separated wierszy. Zawiera on telemetrii przetwarzane w czasie około pół minuty.
* Każdy wiersz reprezentuje punktu danych telemetrii, takich jak żądania lub strony widoku.
* Każdy wiersz jest niesformatowany dokumentu JSON. Jeśli sit i stare jego, otwórz go w programie Visual Studio i wybierz pozycję Edytuj, zaawansowane, Format pliku:

![Wyświetl dane telemetryczne z narzędziem do odpowiedniego](./media/app-insights-export-telemetry/06-json.png)

Okresach czasu są w taktach, gdzie znaczniki 10 000 = 1 ms. Na przykład, te wartości Pokaż czas 1 ms, aby wysłać żądania z przeglądarki, 3 ms odbierać i 1.8 s do przetwarzania tej strony w przeglądarce:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Szczegółowe dane modelu odwołania dla typów właściwości i wartości.](app-insights-export-data-model.md)

## <a name="processing-the-data"></a>Przetwarzanie danych
Na małą skalę można napisać kod służący do pobierania danych, przeczytaj je do arkusza kalkulacyjnego i tak dalej. Na przykład:

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

Dla większych przykładowy kod, zobacz [przy użyciu roli procesu roboczego][exportasa].

## <a name="delete"></a>Usuń stare dane
Należy pamiętać, że jest odpowiedzialny za zarządzanie pojemności pamięci masowej i usuwania starych danych, jeśli to konieczne.

## <a name="if-you-regenerate-your-storage-key"></a>Jeśli musisz ponownie wygenerować klucz magazynu...
Jeśli zmienisz klucz magazynu, Eksport ciągły przestaną działać. Zostanie wyświetlone powiadomienie na koncie Azure.

Otwarcie bloku eksportu ciągłego i edytowanie eksportu. Edytowanie docelowego eksportu, ale pozostaw tego samego magazynu wybrane. Kliknij przycisk OK, aby potwierdzić.

![Edytuj Eksport ciągły otwierających i zamykających ją miejsce docelowe eksportu.](./media/app-insights-export-telemetry/07-resetstore.png)

Eksport ciągły zostanie uruchomiony ponownie.

## <a name="export-samples"></a>Przykłady eksportu

* [Eksportowanie do bazy danych SQL przy użyciu usługi analiza strumienia][exportasa]
* [Stream Analytics próbki 2](app-insights-export-stream-analytics.md)

Na większą skalę, należy wziąć pod uwagę [HDInsight](https://azure.microsoft.com/services/hdinsight/) -klastrów Hadoop w chmurze. Usługa HDInsight zapewnia różne technologie zarządzania i analizy danych big data, i można go używać do przetwarzania danych, który został wyeksportowany z usługi Application Insights.

## <a name="q--a"></a>Pytania i odpowiedzi
* *Ale wszystkie wybrane jednorazowe pobierania wykresu.*  

    Tak, możesz to zrobić. W górnej części bloku, kliknij **eksportowanie danych**.
* *Skonfigurować eksportu, ale nie ma żadnych danych w magazynie my.*

    Usługi Application Insights dotarła wszystkie dane telemetryczne z aplikacji, ponieważ ustawić eksport? Otrzymasz nowych danych.
* *Nastąpiła próba ustawienia eksportu, ale nastąpiła odmowa dostępu*

    Jeśli konto jest własnością organizacji, musisz być członkiem grupy Właściciele i współautorzy.
* *Można wyeksportować wprost do sklep lokalnej?*

    Nie, Niestety. Nasze mechanizm eksportu jest obecnie obsługiwane tylko z usługą Azure storage w tej chwili.  
* *Czy istnieje limitów ilości danych, które należy umieścić w sklepu?*

    Nie. Firma Microsoft będzie przechowywać przekazywanie danych do momentu usunięcia eksportu. Jeśli mamy trafień zewnętrzne limity dla magazynu obiektów blob, ale jest to bardzo dużych zostanie zatrzymana. Jest na kontrolowanie ile miejsca do magazynowania można użyć.  
* *Jak wiele obiektów blob zobacz w magazynie*

  * Dla każdego typu danych wybranych do wyeksportowania nowego obiektu blob jest tworzony co minutę (jeśli są dostępne dane).
  * Ponadto w przypadku aplikacji o dużym ruchu, jednostki dodatkowe partycji są przydzielone. W takim przypadku każdej jednostki tworzy obiektu blob, co minutę.
* *I ponownie wygenerować klucz do mojego magazynu lub zmienić nazwę kontenera, a teraz eksportu nie działa.*

    Edytuj Eksport i otwarcie bloku docelowego eksportu. Pozostaw tego samego magazynu co poprzednio zaznaczone, a następnie kliknij przycisk OK, aby potwierdzić. Eksport zostanie uruchomiony ponownie. Zmiany były w ciągu ostatnich kilku dni, nie spowodować utratę danych.
* *Czy można wstrzymać eksportu?*

    Tak. Kliknij przycisk Wyłącz.

## <a name="code-samples"></a>Przykłady kodu

* [Przykładowe analiza strumienia](app-insights-export-stream-analytics.md)
* [Eksportowanie do bazy danych SQL przy użyciu usługi analiza strumienia][exportasa]
* [Szczegółowe dane modelu odwołania dla typów właściwości i wartości.](app-insights-export-data-model.md)

<!--Link references-->

[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md
