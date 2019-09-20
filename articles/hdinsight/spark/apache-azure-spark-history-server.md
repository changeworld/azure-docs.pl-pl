---
title: Rozszerzony serwer historii platformy Spark do debugowania aplikacji platformy Spark — Azure HDInsight
description: Użyj rozszerzonego serwera historii platformy Spark do debugowania i diagnozowania aplikacji platformy Spark — Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 9398745cb240e7b7dff45ff5d6d9cdf064239bfd
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130356"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Użyj serwera Apache Spark History, aby debugować i diagnozować aplikacje Apache Spark

Ten artykuł zawiera wskazówki dotyczące korzystania z serwera Apache Spark History w celu debugowania i diagnozowania ukończonych i uruchomionych aplikacji platformy Spark. Rozszerzenie zawiera kartę dane, kartę graf i kartę Diagnostyka. Na karcie **dane** użytkownicy mogą sprawdzać dane wejściowe i wyjściowe zadania Spark. Na karcie **Wykres** użytkownicy mogą sprawdzić przepływ danych i ponownie odtworzyć wykres zadania. Na karcie **Diagnostyka** użytkownik może odwoływać się do **pochylenia danych**, **pochylenia czasowego** i **analizy użycia programu wykonującego**.

## <a name="get-access-to-apache-spark-history-server"></a>Uzyskaj dostęp do serwera historii Apache Spark

Serwer historii Apache Spark jest interfejsem użytkownika sieci Web dla ukończonych i uruchomionych aplikacji platformy Spark.

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Otwórz interfejs użytkownika sieci Web serwera historii Apache Spark z Azure Portal

1. W [Azure Portal](https://portal.azure.com/)Otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz Wyświetlanie [i wyświetlanie klastrów](../hdinsight-administer-use-portal-linux.md#showClusters).
2. W obszarze **szybkie linki**kliknij pozycję **pulpit nawigacyjny klastra**, a następnie kliknij pozycję **serwer historii platformy Spark**. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra Spark.

    ![Uruchom Portal serwera historii Spark](./media/apache-azure-spark-history-server/launch-history-server.png "Serwer historii platformy Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Otwórz interfejs użytkownika sieci Web serwera historii platformy Spark według adresu URL

Otwórz serwer historii platformy Spark, przechodząc do następującego adresu URL, `<ClusterName>` a następnie zastąp ciąg nazwą klastra Spark.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Interfejs użytkownika sieci Web serwera historii platformy Spark wygląda następująco:

![Serwer historii usługi HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Karta dane na serwerze historii platformy Spark

Wybierz pozycję Identyfikator zadania, a następnie kliknij pozycję **dane** w menu Narzędzia, aby wyświetlić widok danych.

+ Sprawdź **dane wejściowe**, **wyjściowe**i **operacje tabeli** , zaznaczając karty osobno.

    ![Dane dla kart aplikacji platformy Spark](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Skopiuj wszystkie wiersze, klikając przycisk **Kopiuj**.

    ![Dane dla kopiowania aplikacji platformy Spark](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Zapisz wszystkie dane jako plik CSV, klikając przycisk **CSV**.

    ![Dane dotyczące zapisywania aplikacji Spark](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Wyszukaj, wprowadzając słowa kluczowe w polu **wyszukiwania**pola, wynik wyszukiwania będzie wyświetlany natychmiast.

    ![Dane dla wyszukiwania aplikacji platformy Spark](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Kliknij nagłówek kolumny, aby posortować tabelę, kliknij znak plus, aby rozwinąć wiersz, aby wyświetlić więcej szczegółów, lub kliknij znak minus, aby zwinąć wiersz.

    ![Dane dla tabeli aplikacji platformy Spark](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Pobierz pojedynczy plik, klikając przycisk **częściowe pobieranie** , który znajduje się po prawej stronie, a następnie wybrany plik zostanie pobrany do lokalizacji lokalnej, jeśli plik jeszcze nie istnieje. spowoduje to otwarcie nowej karty w celu wyświetlenia komunikatów o błędach.

    ![Dane dla wiersza pobierania aplikacji platformy Spark](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Skopiuj pełną ścieżkę lub ścieżkę względną, zaznaczając opcję **Kopiuj pełną ścieżkę**i **skopiuj ścieżkę względną** rozwinięcie z menu Pobierz. W przypadku plików usługi Azure Data Lake Storage **Otwórz w Eksplorator usługi Azure Storage** zostanie uruchomiony Eksplorator usługi Azure Storage i zlokalizuj folder podczas logowania.

    ![Dane dla ścieżki kopii aplikacji Spark](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Kliknij liczbę poniżej tabeli, aby nawigować po stronach, gdy zbyt wiele wierszy ma być wyświetlanych na jednej stronie.

    ![Dane dla strony aplikacji platformy Spark](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Umieść kursor na znaku zapytania obok pozycji dane, aby wyświetlić etykietkę narzędzia, lub kliknij znak zapytania, aby uzyskać więcej informacji.

    ![Dane dotyczące aplikacji platformy Spark więcej informacji](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Wyślij opinię o problemach, klikając pozycję Prześlij **nam swoją opinię**.

    ![Program Spark Graph dostarczy nam swoją opinię](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Karta Graf na serwerze historii Apache Spark

Wybierz opcję Identyfikator zadania, a następnie kliknij pozycję **Graph** w menu Narzędzia, aby wyświetlić widok wykresu zadań.

+ Zapoznaj się z omówieniem zadania, korzystając z wygenerowanego wykresu zadań.

+ Domyślnie zostaną wyświetlone wszystkie zadania i można je filtrować według **identyfikatora zadania**.

    ![Identyfikator zadania programu Spark Application i Graph zadania](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ Domyślnie jest wybierany **postęp** , użytkownik może sprawdzić przepływ danych, wybierając pozycję **Odczyt/zapis** na liście rozwijanej **wyświetlanej**.

    ![Wyświetlanie grafu aplikacji i zadania platformy Spark](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Węzeł grafu jest wyświetlany w kolorze, który pokazuje mapę cieplną.

    ![Mapę cieplną wykresu aplikacji i zadania platformy Spark](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Odtwórz zadanie, klikając przycisk **odtwarzania** i Zatrzymaj w dowolnym momencie, klikając przycisk Zatrzymaj. Zadanie jest wyświetlane w kolorze, aby pokazać inny stan podczas odtwarzania:

  + Zielony dla powiodło się: Zadanie zostało ukończone pomyślnie.
  + Pomarańczowy dla ponownych prób: Wystąpienia zadań, które zakończyły się niepowodzeniem, ale nie wpływają na końcowy wynik zadania. Te zadania mają zduplikowane lub ponawiane wystąpienia, które mogą się powieść później.
  + Niebieski do uruchamiania: Zadanie jest uruchomione.
  + Biały dla oczekiwania lub pominięty: Zadanie oczekuje na uruchomienie lub etap został pominięty.
  + Czerwony dla niepowodzenia: Zadanie nie powiodło się.

    ![Przykładowy kolor grafu aplikacji i zadania platformy Spark, uruchomiony](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    Pominięty etap zostanie wyświetlony w kolorze białym.
    ![Przykład koloru aplikacji i wykresu platformy Spark, Pomiń](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Próbka koloru aplikacji i wykresu platformy Spark nie powiodła się](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > Dla każdego zadania jest dozwolone odtwarzanie. W przypadku niekompletnego zadania odtwarzanie nie jest obsługiwane.

+ Przesuwa mysz, aby powiększyć/pomniejszyć wykres zadania, lub kliknij przycisk **Powiększ, aby dopasować** go do ekranu.

    ![Powiększenie aplikacji i wykresu zadań platformy Spark](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Umieść kursor w węźle grafu, aby wyświetlić etykietkę narzędzia, gdy zadania zakończone niepowodzeniem, a następnie kliknij pozycję etap, aby otworzyć stronę etap.

    ![Etykietka narzędzia grafu aplikacji i zadania platformy Spark](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Na karcie wykres zadania etapy będą mieć etykietkę narzędzia i małą ikonę wyświetlaną, jeśli mają one zadania spełniające poniższe warunki:
  + Pochylenie danych: rozmiar odczytu danych > średni rozmiar odczytanych danych wszystkich zadań w tym etapie * 2 i rozmiar odczytu danych > 10 MB.
  + Pochylenie czasu: czas wykonywania > Średni czas wykonywania wszystkich zadań w tym etapie * 2 i czas wykonywania > 2 min.

    ![Ikona pochylanie aplikacji i wykresu zadań platformy Spark](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ W węźle grafu zadania zostaną wyświetlone następujące informacje dotyczące poszczególnych etapów:
  + #C1.
  + Nazwa lub opis.
  + Łączny numer zadania.
  + Odczytane dane: suma rozmiaru danych wejściowych i rozmiaru losowego odczytu.
  + Zapis danych: suma rozmiaru wyjściowego i rozmiaru losowego zapisu.
  + Czas wykonywania: czas między godziną rozpoczęcia pierwszej próby i czas zakończenia ostatniej próby.
  + Liczba wierszy: suma rekordów wejściowych, rekordów wyjściowych, losowego odczytywania rekordów i losowego zapisu rekordów.
  + Wykonywane.

    > [!NOTE]  
    > Domyślnie węzeł grafu zadania będzie wyświetlał informacje z ostatniej próby każdego etapu (z wyjątkiem czasu wykonywania etapu), ale w węźle grafu odtwarzania zostaną wyświetlone informacje o każdej próbie.

    > [!NOTE]  
    > W przypadku rozmiaru danych odczytu i zapisu używamy 1 MB = 1000 KB = 1000 * 1000 bajtów.

+ Wyślij opinię o problemach, klikając pozycję Prześlij **nam swoją opinię**.

    ![Opinia na temat aplikacji i wykresu zadań platformy Spark](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Karta Diagnostyka na serwerze historii Apache Spark

Wybierz pozycję Identyfikator zadania, a następnie kliknij pozycję **Diagnostyka** w menu Narzędzia, aby wyświetlić widok Diagnostyka zadań. Karta Diagnostyka obejmuje **przechylenie danych**, **pochylenie czasu**i **analizę użycia programu wykonującego**.

+ Sprawdź **pochylenie danych**, **pochylenie czasu**i **analizę użycia wykonawcy** , wybierając odpowiednio karty.

    ![Karta przechylenie danych diagnostyki SparkUI ponownie](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Pochylenie danych

Kliknij kartę **pochylanie danych** , odpowiednie skośne zadania są wyświetlane na podstawie określonych parametrów.

+ **Określ parametry** — pierwsza sekcja zawiera parametry, które są używane do wykrywania pochylenia danych. Wbudowana reguła to: Odczyt danych zadania jest dłuższy niż 3 razy średniego odczytania danych zadania, a odczyt danych zadania przekracza 10 MB. Jeśli chcesz zdefiniować własną regułę dla pochylonych zadań, możesz wybrać parametry, **etap skośny**i odcinek **skośny** zostaną odpowiednio odświeżone.

+ **Etap skośny** — druga sekcja przedstawia etapy, w których zadania skośne spełniają kryteria określone powyżej. Jeśli na etapie występuje więcej niż jedno zadanie skośne, w tabeli etapów skośnych jest wyświetlane tylko zadanie najbardziej skośne (np. największe dane dotyczące pochylenia danych).

    ![Karta skośność danych diagnostyki sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Wykres skośny** — Jeśli wybrano wiersz w tabeli etap skośny, wykres skośny Wyświetla więcej szczegółów dystrybucji zadań w oparciu o czas odczytu i wykonywania danych. Skośne zadania są oznaczone kolorem czerwonym, a normalne zadania są oznaczone kolorem niebieskim. Ze względu na wydajność Wykres wyświetla tylko do 100 przykładowych zadań. Szczegóły zadania są wyświetlane w prawym dolnym panelu.

    ![Wykres skośny sparkui dla etapu 10](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Pochylenie czasu

Karta **przechylenie czasu** przedstawia zadania skośne w oparciu o czas wykonywania zadania.

+ **Określ parametry** — w pierwszej sekcji są wyświetlane parametry, które są używane do wykrywania pochylenia czasu. Domyślne kryteria wykrywania pochylenia czasu to: czas wykonywania zadania jest dłuższy niż 3 razy średniego czasu wykonywania, a czas wykonywania zadania jest większy niż 30 sekund. Można zmienić parametry w zależności od potrzeb. Wykres **skośny** i **skośny** wyświetlają odpowiednie etapy i informacje o zadaniach, podobnie jak powyższa karta **skośność danych** .

+ Kliknij przycisk **przechylenie czasu**, a następnie przefiltrowany wynik jest wyświetlany w sekcji **skośne etap** zgodnie z parametrami ustawionymi w sekcji **Określanie parametrów**. Kliknij jeden element w sekcji **skośnego etapu** , a następnie odpowiedni wykres jest przygotowywany w section3, a szczegóły zadania są wyświetlane w prawym dolnym panelu.

    ![Sekcja pochylanie czasu diagnostyki sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analiza użycia wykonawcy

Wykres użycia programu wykonującego wizualizuje rzeczywistą alokację wykonawców zadania platformy Spark i stan uruchomienia.  

+ Kliknij pozycję **Analiza użycia programu wykonującego**, a następnie wybierz cztery typy krzywe dotyczące użycia programu wykonującego, w tym **przydzieloną wykonawcy**, **uruchomione wykonawcy**,**wykonawcze bezczynne**i **maksymalne wystąpienia wykonawców**. W odniesieniu do przyznanych wykonawców zdarzenie "dodaliśmy" lub "wykonawca usunięty" spowoduje zwiększenie lub zmniejszenie przyznanych wykonawców, można sprawdzić "oś czasu zdarzeń" na karcie "zadania", aby uzyskać więcej informacji.

    ![Karta modułów wykonujących diagnostyki sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Kliknij ikonę koloru, aby wybrać lub usunąć zaznaczenie odpowiedniej zawartości we wszystkich wersjach roboczych.

    ![sparkui — Wybieranie wykresu](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Często zadawane pytania

### <a name="1-revert-to-community-version"></a>1. Przywróć wersję społeczności

Aby powrócić do wersji społeczności, wykonaj następujące czynności:

1. Otwórz klaster w Ambari. Kliknij pozycję **Spark2** w lewym panelu.
2. Kliknij **kartę** konfiguracje.
3. Rozwiń grupę **niestandardowe spark2 — domyślne**.
4. Kliknij pozycję **Dodaj właściwość**, Dodaj pozycję **Spark. UI. ulepszenie. Enabled = false**, Zapisz.
5. Właściwość ma teraz **wartość false** .
6. Kliknij przycisk **Zapisz** , aby zapisać konfigurację.

    ![Funkcja Apache Ambari wyłącza się](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

7. Kliknij pozycję **Spark2** w lewym panelu, w obszarze Karta **Podsumowanie** kliknij pozycję **serwer historii Spark2**.

    ![Widok podsumowania Spark2 Apache Ambari](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

8. Uruchom ponownie serwer historii, klikając pozycję **Uruchom ponownie** **serwer historii Spark2**.

    ![Ponowne uruchamianie historii Apache Ambari Spark2](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
9. Odśwież interfejs użytkownika sieci Web serwera historii platformy Spark, który zostanie przywrócony do wersji społecznościowej.

### <a name="2-upload-history-server-event"></a>2. Prześlij zdarzenie serwera historii

Jeśli wystąpi błąd serwera historii, postępuj zgodnie z instrukcjami, aby podać zdarzenie:

1. Zdarzenie pobierania przez kliknięcie przycisku **Pobierz** w interfejsie użytkownika sieci Web serwera historii.

    ![Pobieranie serwera historii Spark2](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Kliknij pozycję Prześlij **nam opinię** z karty dane/wykres.

    ![Program Spark Graph przedstawia nam swoją opinię](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Podaj tytuł i opis błędu, przeciągnij plik zip do pola edycji, a następnie kliknij pozycję **Prześlij nowy problem**.

    ![przykład problemu z plikiem platformy Apache Spark](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Uaktualnij plik JAR dla scenariusza poprawek

Jeśli chcesz przeprowadzić uaktualnienie przy użyciu poprawki, użyj poniższego skryptu, aby uaktualnić plik Spark-Enhancement. jar *.

**upgrade_spark_enhancement. sh**:

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

**Użycie**:

`upgrade_spark_enhancement.sh https://${jar_path}`

**Przykład**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**Aby użyć pliku bash z Azure Portal**

1. Uruchom [Azure Portal](https://ms.portal.azure.com)i wybierz klaster.
2. Kliknij pozycję **Akcje skryptu**, a następnie **Prześlij nowy**. Wypełnij formularz **Akcja przesyłania skryptu** , a następnie kliknij przycisk **Utwórz** .

    + **Typ skryptu**: wybierz pozycję **niestandardowy**.
    + **Nazwa**: Określ nazwę skryptu.
    + **Identyfikator URI skryptu bash**: Przekaż plik bash do klastra prywatnego, a następnie skopiuj tutaj adres URL. Alternatywnie możesz użyć podanego identyfikatora URI.

   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

   + Sprawdź **kierownika** i **proces roboczy**.
   + **Parametry**: ustaw parametry zgodnie z bash użycie.

     ![Azure Portal akcji przesyłania skryptu](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Znane problemy

1. Obecnie działa tylko w przypadku klastra Spark 2,3 i 2,4.

2. Dane wejściowe/wyjściowe przy użyciu RDD nie będą wyświetlane na karcie dane.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami klastra Apache Spark w usłudze HDInsight](apache-spark-resource-manager.md)
* [Skonfiguruj ustawienia Apache Spark](apache-spark-settings.md)

## <a name="contact-us"></a>Skontaktuj się z nami

Jeśli masz jakieś opinie lub występują inne problemy podczas korzystania z tego narzędzia, Wyślij wiadomość e-mail na adres ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
