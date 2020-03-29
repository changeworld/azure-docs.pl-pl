---
title: Używanie rozszerzonych funkcji serwera Apache Spark History Server do debugowania aplikacji — Azure HDInsight
description: Użyj rozszerzonych funkcji na serwerze Historii platformy Spark Apache do debugowania i diagnozowania aplikacji platformy Spark — Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548938"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Używanie rozszerzonych funkcji serwera Apache Spark History Server do debugowania i diagnozowania aplikacji Platformy Spark

W tym artykule pokazano, jak używać rozszerzonych funkcji serwera historii platformy Spark Apache do debugowania i diagnozowania ukończonych lub uruchomionych aplikacji platformy Spark. Rozszerzenie zawiera **kartę Dane,** kartę **Wykres** i kartę **Diagnostyka.** Na **karcie Dane** można sprawdzić dane wejściowe i wyjściowe zadania Platformy Spark. Na karcie **Wykres** można sprawdzić przepływ danych i odtworzyć wykres zadania. Na karcie **Diagnoza** można odwołać się do funkcji **Pochylenia danych,** **Pochylenia czasu**i **Analizy użycia wykonawcy.**

## <a name="get-access-to-the-spark-history-server"></a>Uzyskaj dostęp do serwera historii platformy Spark

Serwer historii platformy Spark to internetowy interfejs użytkownika dla ukończonych i uruchomionych aplikacji Platformy Spark. Można go otworzyć w witrynie Azure portal lub z adresu URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Otwieranie interfejsu użytkownika sieci Web serwera historii platformy Spark w portalu Azure

1. Z [witryny Azure portal](https://portal.azure.com/)otwórz klaster platformy Spark. Aby uzyskać więcej informacji, zobacz [Lista i pokaż klastry](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Z **pulpitów nawigacyjnych klastra**wybierz **pozycję Serwer historii platformy Spark**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra platformy Spark.

    ![Uruchom serwer historii platformy Spark z witryny Azure portal.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Serwer historii platformy Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Otwórz interfejs użytkownika sieci Web serwera historii platformy Spark według adresu URL

Otwórz serwer historii platformy Spark, przeglądając program `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` **CLUSTERNAME,** gdzie nazwa klastra platformy Spark jest nazwą klastra platformy Spark.

Interfejs użytkownika sieci Web serwera spark History Server może wyglądać podobnie do tego obrazu:

![Strona Serwer historii platformy Spark.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Korzystanie z karty Dane na serwerze historii iskier

Wybierz identyfikator zadania, a następnie wybierz pozycję **Dane** w menu narzędzia, aby wyświetlić widok danych.

+ Przejrzyj **dane wejściowe,** **dane wyjściowe**i **operacje tabeli,** wybierając poszczególne karty.

    ![Karty danych na stronie Dane dla aplikacji platformy Spark.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Skopiuj wszystkie wiersze, wybierając przycisk **Kopiuj.**

    ![Kopiowanie danych na stronie aplikacji Spark.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Zapisz wszystkie dane jako . pliku CSV, wybierając przycisk **csv.**

    ![Zapisywanie danych jako pliku . CSV ze strony Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Przeszukuj dane, wprowadzając słowa kluczowe w polu **Wyszukiwanie.** Wyniki wyszukiwania zostaną wyświetlone natychmiast.

    ![Wyszukaj dane na stronie Dane dla aplikacji platformy Spark.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Wybierz nagłówek kolumny, aby posortować tabelę. Wybierz znak plus, aby rozwinąć wiersz, aby wyświetlić więcej szczegółów. Wybierz znak minus, aby zwinąć wiersz.

    ![Tabela danych na stronie Dane dla aplikacji platformy Spark.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Pobierz pojedynczy plik, wybierając przycisk **Częściowe pobieranie** po prawej stronie. Wybrany plik zostanie pobrany lokalnie. Jeśli plik już nie istnieje, otworzy się nowa karta, aby wyświetlić komunikaty o błędach.

    ![Wiersz pobierania danych na stronie Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Skopiuj pełną ścieżkę lub ścieżkę względną, wybierając opcję **Kopiuj pełną ścieżkę** lub **Kopiuj ścieżkę względną,** która rozwinie się z menu pobierania. W przypadku plików usługi Azure Data Lake Storage wybierz pozycję **Otwórz w Eksploratorze usługi Azure Storage,** aby uruchomić Eksploratora usługi Azure Storage i zlokalizować folder po zalogowaniu się.

    ![Kopiuj opcje Pełna ścieżka i Kopiuj ścieżkę względną na stronie Dane dla aplikacji platformy Spark.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Jeśli na jednej stronie jest zbyt wiele wierszy do wyświetlenia, wybierz numery stron u dołu tabeli, aby nawigować.

    ![Numery stron na stronie Dane dla aplikacji platformy Spark.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Aby uzyskać więcej informacji, umieść wskaźnik myszy na lub wybierz znak zapytania obok **pozycji Data for Spark Application,** aby wyświetlić etykietkę narzędzia.

    ![Uzyskaj więcej informacji ze strony Dane aplikacji platformy Spark.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Aby wysłać opinię na temat problemów, wybierz opcję **Przekaż nam opinię.**

    ![Przekaż opinię ze strony Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Używanie karty Wykres na serwerze historii iskier

+ Wybierz identyfikator zadania, a następnie wybierz **pozycję Wykres** w menu narzędzia, aby wyświetlić wykres zadania. Domyślnie na wykresie będą wyświetlane wszystkie zadania. Filtruj wyniki za pomocą menu rozwijanego **Identyfikator zadania.**

    ![Menu rozwijane Identyfikator zadania na stronie Spark Application & Job Graph.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **Postęp** jest wybierany domyślnie. Sprawdź przepływ danych, wybierając pozycję **Odczyt** lub **Napis na piśmie** w menu rozwijanym **Wyświetlanie.**

    ![Sprawdź przepływ danych na stronie Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ Kolor tła każdego zadania odpowiada mapie cieplnej.

   ![Mapa cieplna na stronie Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Kolor |Opis |
    |---|---|
    |Zielony|Zadanie zostało zakończone pomyślnie.|
    |Orange|Zadanie nie powiodło się, ale nie ma to wpływu na końcowy wynik zadania. Te zadania mają duplikaty lub ponowić próbę wystąpień, które mogą zakończyć się pomyślnie później.|
    |Blue|Zadanie jest uruchomione.|
    |Biały|Zadanie oczekuje na uruchomienie lub etap został pominięty.|
    |Red|Zadanie nie powiodło się.|

     ![Uruchamianie zadania na stronie Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     Pominięte etapy są wyświetlane w kolorze białym.
    ![Pominięte zadanie na stronie Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Zadanie nie powiodło się na stronie Wykres & zadania aplikacji platformy Spark.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > Odtwarzanie jest dostępne dla ukończonych zadań. Wybierz przycisk **Odtwarzanie,** aby odtworzyć zadanie. Zatrzymaj zadanie w dowolnym momencie, wybierając przycisk stop. Po odtworzonym zadaniu każde zadanie wyświetli jego stan według koloru. Odtwarzanie nie jest obsługiwane dla zadań niekompletnych.

+ Przewiń, aby powiększyć lub pomniejszyć wykres zadania, lub wybierz **opcję Powiększ, aby dopasować** go do ekranu.

    ![Wybierz opcję Powiększ, aby zmieścić się na stronie Wykres & zadania aplikacji Spark.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Gdy zadania nie powiodą się, umieść wskaźnik myszy na węźle wykresu, aby wyświetlić etykietkę narzędzia, a następnie wybierz etap, aby otworzyć go na nowej stronie.

    ![Wyświetl etykietkę narzędzia na stronie Wykres & zadania aplikacji Spark.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Na stronie Spark Application & Job Graph na poszczególnych etapach zostaną wyświetlone etykietki narzędzi i małe ikony, jeśli zadania spełniają następujące warunki:
  + Pochylenie danych: rozmiar odczytu danych > średni rozmiar odczytu danych wszystkich zadań na tym etapie * 2 *i* rozmiar odczytu danych > 10 MB.
  + Pochylenie czasu: czas wykonania > średni czas wykonywania wszystkich zadań wewnątrz tego etapu * 2 *i* czas wykonania > 2 minuty.

    ![Pochylona ikona zadania na stronie Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ Węzeł wykresu zadania wyświetli następujące informacje o każdym etapie:
  + ID
  + Nazwa lub opis
  + Całkowita liczba zadań
  + Odczyt danych: suma rozmiaru wejściowego i rozmiaru odczytu losowego
  + Zapis danych: suma rozmiaru wyjściowego i rozmiaru zapisu losowego
  + Czas wykonania: czas między czasem rozpoczęcia pierwszej próby a czasem zakończenia ostatniej próby
  + Liczba wierszy: suma rekordów wejściowych, rekordów wyjściowych, rekordów odczytu losowego i losowych rekordów zapisu
  + Postęp

    > [!NOTE]  
    > Domyślnie węzeł wykresu zadania będzie wyświetlał informacje z ostatniej próby każdego etapu (z wyjątkiem czasu wykonania etapu). Ale podczas odtwarzania węzeł wykresu zadania wyświetli informacje o każdej próbie.

    > [!NOTE]  
    > W przypadku odczytu danych i rozmiarów zapisu danych używamy 1MB = 1000 KB = 1000 * 1000 bajtów.

+ Wyślij opinię o problemach, wybierając **pozycję Przekaż nam opinię.**

    ![Opcja opinii na stronie Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Korzystanie z karty Diagnoza na serwerze historii iskier

Wybierz identyfikator zadania, a następnie wybierz **pozycję Diagnoza** w menu narzędzia, aby wyświetlić widok diagnozy zadania. Karta **Diagnoza** zawiera **pochylenie danych,** **pochylenie czasu**i **analizę użycia executora.**

+ Przejrzyj **pochylenie danych,** **pochylenie czasu**i **analizę użycia wykonawcy,** wybierając odpowiednio karty.

    ![Karta Pochylenie danych na karcie Diagnoza.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Pochylenie danych

Wybierz kartę **Pochylenie danych.** Odpowiednie zadania skośne są wyświetlane na podstawie określonych parametrów.

#### <a name="specify-parameters"></a>Określ parametry

Sekcja **Określ parametry** wyświetla parametry, które są używane do wykrywania pochylenia danych. Domyślna reguła to: Odczyt danych zadania jest większy niż trzy razy średni odczyt danych zadania, a odczyt danych zadania wynosi więcej niż 10 MB. Jeśli chcesz zdefiniować własną regułę dla zadań pochylonych, możesz wybrać parametry. **Przekładnie na stole rozsyłanym** i **pochylenie wykresu** zostaną odpowiednio zaktualizowane.

#### <a name="skewed-stage"></a>Stopień skośny

Sekcja **Stół skośny** wyświetla etapy, które mają skośne zadania spełniające określone kryteria. Jeśli na etapie znajduje się więcej niż jedno zadanie skośne, w sekcji **Skośny stół montażowy** jest wyświetlane tylko najbardziej pochylone zadanie (czyli największe dane dla pochylenia danych).

![Większy widok karty Pochylenie danych na karcie Diagnoza.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Wykres pochylenia

Po wybraniu wiersza w tabeli **Pochylenie stołu montażowego** **na wykresie pochylenia** jest wyświetlanych więcej szczegółów dystrybucji zadań na podstawie czasu odczytu i wykonania danych. Pochylone zadania są zaznaczone na czerwono, a normalne zadania są oznaczone na niebiesko. Aby wziąć pod uwagę wydajność, na wykresie jest wyświetlanych do 100 przykładowych zadań. Szczegóły zadania są wyświetlane w prawym dolnym panelu.

![Wykres pochylenia dla etapu 10 w interfejsie spark.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Pochylenie czasu

Na karcie **Pochylenie czasu** są wyświetlane pochylone zadania na podstawie czasu wykonania zadania.

#### <a name="specify-parameters"></a>Określ parametry

W sekcji **Określ parametry** wyświetlane są parametry, które służą do wykrywania pochylenia czasu. Domyślna reguła jest: Czas wykonywania zadania jest większy niż trzy razy średni czas wykonywania, a czas wykonywania zadania jest większy niż 30 sekund. Parametry można zmieniać w zależności od potrzeb. Wykres **pochylony** i **pochylony** wyświetla odpowiednie informacje o etapach i zadaniach, podobnie jak na karcie **Pochylenie danych.**

Po wybraniu opcji **Pochylenie czasu**filtrowany wynik jest wyświetlany w sekcji **Stół pochylony** zgodnie z parametrami ustawionymi w sekcji **Określanie parametrów.** Po wybraniu jednego elementu w sekcji **Stół pochylony** odpowiedni wykres jest sporządzany w trzeciej sekcji, a szczegóły zadania są wyświetlane w prawym dolnym panelu.

![Karta Pochylenie czasu na karcie Diagnoza.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Wykresy analizy użycia wykonawcy

**Wykres użycia executora** wyświetla rzeczywistą alokację wykonawcy zadania i stan uruchomienia.  

Po **wybraniu analizy użycia executora,** cztery różne krzywe dotyczące użycia wykonawcy są sporządzane: **Allocated Executors**, **Running Executors**, **idle Executors**i **Max Executor Instances**. Każdy **wykonawca dodany** lub wykonawca **usunięte** zdarzenie zwiększy lub zmniejszyć przydzielonych wykonawców. Możesz sprawdzić **oś czasu zdarzenia** na karcie **Zadania, aby** uzyskać więcej porównań.

![Karta Analiza użycia executora na karcie Diagnoza.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Wybierz ikonę koloru, aby zaznaczyć lub usunąć zaznaczenie odpowiedniej zawartości we wszystkich wersjach pochyleniach.

 ![Wybierz wykres na karcie Analiza użycia wykonawcy.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Najczęściej zadawane pytania

### <a name="how-do-i-revert-to-the-community-version"></a>Jak przywrócić wersję społeczności?

Aby powrócić do wersji społeczności, wykonaj następujące kroki.

1. Otwórz klaster w Ambari.
1. Przejdź do **Spark2** > **Configs**.
1. Wybierz **niestandardowe domyślne iskrzenie2**.
1. Wybierz **pozycję Dodaj właściwość ...**.
1. Dodaj **plik spark.ui.enhancement.enabled=false**, a następnie zapisz go.
1. Właściwość ustawia się **false** teraz.
1. Wybierz **pozycję Zapisz,** aby zapisać konfigurację.

    ![Wyłącz funkcję w Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Wybierz **opcję Spark2** w lewym panelu. Następnie na karcie **Podsumowanie** wybierz pozycję **Spark2 History Server**.

    ![Widok podsumowania w Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Aby ponownie uruchomić serwer historii platformy Spark, wybierz przycisk **Uruchomiono** po prawej stronie **serwera historii Spark2,** a następnie wybierz **polecenie Uruchom ponownie** z menu rozwijanego.

    ![Uruchom ponownie serwer historii platformy Spark w Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Odśwież interfejs użytkownika sieci Web serwera spark History Server. Powróci do wersji społecznościowej.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Jak przesłać zdarzenie programu Spark History Server, aby zgłosić to jako problem?

Jeśli napotkasz błąd w spark history server, wykonaj następujące kroki, aby zgłosić zdarzenie.

1. Pobierz zdarzenie, wybierając **pozycję Pobierz** w interfejsie użytkownika sieci Web serwera Spark History Server.

    ![Pobierz zdarzenie w interfejsie użytkownika serwera historii platformy Spark.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Wybierz **opcję Przekaż nam opinię** ze strony Spark Application & Job **Graph.**

    ![Przekazywanie opinii na temat strony Wykres zadań aplikacji Spark &](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Podaj tytuł i opis błędu. Następnie przeciągnij plik zip do pola edycji i wybierz pozycję **Prześlij nowy problem**.

    ![Prześlij i prześlij nowy problem.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Jak uaktualnić plik jar w scenariuszu poprawki?

Jeśli chcesz uaktualnić za pomocą poprawki, użyj następującego `spark-enhancement.jar*`skryptu, który spowoduje uaktualnienie .

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Sposób użycia

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Przykład

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Użyj pliku bash z witryny Azure portal

1. Uruchom [witrynę Azure portal](https://ms.portal.azure.com), a następnie wybierz klaster.
2. Wykonaj [akcję skryptu](../hdinsight-hadoop-customize-cluster-linux.md) z następującymi parametrami.

    |Właściwość |Wartość |
    |---|---|
    |Typ skryptu|- Niestandardowe|
    |Nazwa|UpgradeJar (Pakiet Ulepszeń)|
    |Identyfikator URI skryptu bash|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Typy węzłów|Głowa, Pracownik|
    |Parametry|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Akcja przesyłania skryptu przez witrynę Azure portal](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Znane problemy

+ Obecnie serwer spark history działa tylko dla platformy Spark 2.3 i 2.4.

+ Dane wejściowe i wyjściowe korzystające z rdd nie będą wyświetlane na karcie **Dane.**

## <a name="next-steps"></a>Następne kroki

+ [Zarządzanie zasobami klastra Platformy Spark apache w programie HDInsight](apache-spark-resource-manager.md)
+ [Konfigurowanie ustawień platformy Apache Spark](apache-spark-settings.md)

## <a name="feedback"></a>Opinia

Jeśli masz jakieś uwagi lub natkniesz się na jakiekolwiek[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)problemy podczas korzystania z tego narzędzia, wyślij wiadomość e-mail na adres ( ).
