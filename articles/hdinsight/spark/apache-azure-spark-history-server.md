---
title: Użyj rozszerzonej serwer historii platformy Spark na debugowanie i diagnozowanie aplikacji platformy Spark — Azure HDInsight
description: Użyj rozszerzonej serwer historii platformy Spark na debugowanie i diagnozowanie aplikacji platformy Spark — Azure HDInsight.
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 96c2f1c021e27a8f5a0f4d2af2b34970b9d44e80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60484587"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Serwer historii platformy Spark w usłudze rozszerzonej Apache umożliwia debugowanie i diagnozowanie aplikacji platformy Apache Spark

Ten artykuł zawiera wskazówki dotyczące sposobu używania rozszerzony serwer historii platformy Spark Apache do debugowania i diagnozowania ukończone i uruchamianie aplikacji platformy Spark. Rozszerzenie zawiera karty danych i grafu karty i diagnostyki. Na **danych** karcie użytkownicy mogli sprawdzać dane wejściowe i wyjściowe zadań platformy Spark. Na **wykres** karcie użytkownicy mogli sprawdzać dane przepływu i odtwarzania wykresu zadania. Na **diagnostyki** kartę, użytkownik może odwoływać się do **Niesymetryczności danych**, **czasowego** i **analizy użycia funkcji wykonawczej**.

## <a name="get-access-to-apache-spark-history-server"></a>Uzyskaj dostęp do serwera programu Apache Spark historii

Serwer historii platformy Spark Apache jest interfejs użytkownika sieci web dla aplikacji platformy Spark ukończone i uruchomione. 

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Otwórz interfejs użytkownika Apache Spark historii serwera sieci Web z witryny Azure portal

1. Z [witryny Azure portal](https://portal.azure.com/), otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz [listy i wyświetlaniu klastrów](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Z **szybkich łączy**, kliknij przycisk **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **serwer historii platformy Spark**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra Spark. 

    ![Platforma Spark jest serwer historii](./media/apache-azure-spark-history-server/launch-history-server.png "Spark serwer historii")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Otwórz Web serwer historii platformy Spark interfejsu użytkownika według adresu URL
Otwórz Zamień serwer historii platformy Spark, przechodząc pod następujący adres URL `<ClusterName>` nazwą klastra Spark klienta.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Serwer historii platformy Spark w sieci web UI wygląda następująco:

![Serwer historii platformy HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="data-tab-in-spark-history-server"></a>Kartę dane na serwer historii platformy Spark
Wybierz identyfikator zadania, a następnie kliknij przycisk **danych** w menu Narzędzia, aby uzyskać widok danych.

+ Sprawdź **dane wejściowe**, **dane wyjściowe**, i **operacje tabeli** , wybierając oddzielnie na kartach.

    ![Dane kart](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Kopiuj wszystkie wiersze, klikając przycisk **kopiowania**.

    ![Kopiowanie danych](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Zapisz wszystkie dane jako plik CSV, klikając przycisk **csv**.

    ![Dane, Zapisz](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Wyszukaj, wpisując słowa kluczowe w polu **wyszukiwania**, wynikiem wyszukiwania będą wyświetlane natychmiast.

    ![Wyszukiwanie danych](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Kliknij nagłówek kolumny, aby posortować tabelę, kliknij znak plus, aby rozwinąć wiersz, aby wyświetlić więcej szczegółów lub kliknij znak minus, aby zwinąć wiersz.

    ![Tabela danych](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Pobierz jeden plik, klikając przycisk **częściowe pobieranie** umieść po prawej stronie, a następnie wybrany plik zostanie pobrany do lokalnego lub jeśli plik nie istnieje, zostanie otwarty nową kartę, aby wyświetlić komunikaty o błędach.

    ![Wiersz pobierania danych](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopiuj pełną ścieżkę lub ścieżki względnej, wybierając **Kopiuj pełną ścieżkę**, **Kopiuj ścieżkę względną** rozszerza się w menu Pobierz. W przypadku plików magazynu usługi azure data lake **Otwórz w Eksploratorze usługi Azure Storage** spowoduje uruchomienie Eksploratora usługi Azure Storage i Znajdź folder podczas logowania.

    ![Ścieżka do kopiowania danych](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Kliknij numer pod tabelą, aby przejść strony kiedy zbyt wiele wierszy do wyświetlenia na jednej stronie. 

    ![Strona danych](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Umieść kursor na znak zapytania obok dane, aby pokazać etykietkę narzędzia, lub kliknij znak zapytania, aby uzyskać więcej informacji.

    ![Dane uzyskać więcej informacji](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Prześlij opinię z problemami, klikając **przesłanie nam opinii**.

    ![Wykres opinii](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="graph-tab-in-apache-spark-history-server"></a>Wykres karty w serwer historii platformy Spark Apache
Wybierz identyfikator zadania, a następnie kliknij przycisk **wykres** w menu Narzędzia, aby uzyskać widok wykresu zadania.

+ Sprawdź Przegląd zadania, Graf zadania wygenerowany. 

+ Domyślnie, spowoduje wyświetlenie wszystkich zadań i może być filtrowane według **identyfikator zadania**.

    ![Identyfikator zadania programu Graph](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Domyślnie **postępu** jest zaznaczone, użytkownik można sprawdzić przepływu danych, wybierając **odczytu/Written** na liście rozwijanej **wyświetlania**.

    ![Wyświetl wykres](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Wyświetlanie węzła grafu kolor, który pokazuje cieplnej.

    ![Wykres mapy cieplnej](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Odtwarzanie zadania, klikając **odtwarzania** przycisk i zatrzymać w dowolnym momencie, klikając przycisk Zatrzymaj. Wyświetlanie zadań są oznaczone kolorem do wyświetlania stanu różnych podczas odtwarzania:

  + Zielony dla zakończyło się pomyślnie: Zadanie zostało ukończone pomyślnie.
  + Pomarańczowy na ponawiane: Wystąpienia zadania, które nie powiodło się, ale nie wpływają na ostateczny wynik zadania. Te zadania ma zduplikowany lub spróbuj ponownie wystąpienia, które może powieść się później.
  + Niebieski do uruchomienia: Zadanie jest uruchomione.
  + Biały dla oczekiwania lub pominięte: Zadanie oczekuje na uruchomienie lub etapu została pominięta.
  + Kolor czerwony dla nie powiodło się: Zadanie nie powiodło się.

    ![przykład koloru wykresu, uruchomione](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    Uzyskać informacje o wyświetlaniu pominięto etap białe.
    ![przykład koloru wykresu, Pomiń](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![przykład koloru wykresu, nie powiodło się](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]  
    > Odtwarzanie dla każdego zadania jest dozwolone. Odtwarzanie dla zadanie nieukończone, nie jest obsługiwane.


+ Myszy Przewija powiększenia w pionie i poziomie wykresu zadania, lub kliknąć przycisk **Dopasuj do** celu dopasowania go do ekranu.
 
    ![Powiększenie wykresu, aby dopasować](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Umieść kursor w węźle wykres, aby zobaczyć, że etykietki narzędzia w przypadku zadań zakończonych niepowodzeniem, a następnie kliknij etap, aby otworzyć stronę etapu.

    ![etykietki narzędzi wykresu](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Na karcie grafu zadania etapy będą miały etykietki narzędzi i mała ikona wyświetlana, jeśli mają zadania, które spełniają poniższe warunki:
  + Niesymetryczność danych: rozmiar odczytu danych > ilość odczytanych danych średni rozmiar wszystkich zadań w tym etapie * 2 i dane odczytane rozmiar > 10 MB.
  + Niesymetryczność czasu: czasu wykonywania > Średni czas wykonywania wszystkich zadań w tym etapie * 2 i czasu wykonywania > 2 minut.

    ![niesymetryczność ikoną wykresu](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ Węzłem grafu zadania zostaną wyświetlone następujące informacje, każdy z etapów:
  + ID.
  + Nazwy lub opisu.
  + Liczba wszystkich zadań.
  + Dane odczytane: Suma rozmiar danych wejściowych i shuffle rozmiar odczytu.
  + Zapisywanie danych: Suma rozmiar danych wyjściowych i shuffle rozmiar zapisu.
  + Czas wykonywania: czas między przy pierwszej próbie czas rozpoczęcia i czas ukończenia ostatniej próby.
  + Liczba wierszy: Suma rekordów danych wejściowych, wyjściowych rekordów mieszania odczytu rekordów i mieszania rekordów zapisu.
  + Postęp.

    > [!NOTE]  
    > Domyślnie węzłem grafu zadania spowoduje wyświetlenie informacji z ostatniej próby, każdy z etapów (z wyjątkiem czas wykonywania etapu), ale podczas odtwarzania wykresu węźle są wyświetlane informacje z kolejnymi próbami.

    > [!NOTE]  
    > Rozmiar danych odczytu i zapisu, używamy 1 MB = 1000 KB = 1000 * 1000 bajtów.

+ Prześlij opinię z problemami, klikając **przesłanie nam opinii**.

    ![Wykres opinii](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Karta diagnostyki w serwer historii platformy Spark Apache
Wybierz identyfikator zadania, a następnie kliknij przycisk **diagnostyki** w menu Narzędzia, aby zakończyć dane zadanie diagnostyki widoku. Karta Diagnostyka zawiera **Niesymetryczności danych**, **czasowego**, i **analizy użycia funkcji wykonawczej**.
    
+ Sprawdź **Niesymetryczności danych**, **czasowego**, i **analizy użycia funkcji wykonawczej** , wybierając odpowiednio na kartach.

    ![Karty diagnostyki](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Niesymetryczność danych
Kliknij przycisk **Niesymetryczności danych** karcie odpowiednich niesymetryczne zadania są wyświetlane w oparciu o określonych parametrów. 

+ **Określ parametry** — pierwsza sekcja wyświetla parametry, które są używane do wykrywania Niesymetryczności danych. To wbudowane reguły: Odczyt danych zadania jest większy niż 3 razy odczytanych danych średniej zadań i Odczyt danych zadania jest większy niż 10MB. Jeśli chcesz zdefiniować reguły niesymetryczne zadań, możesz wybrać parametry, **nierówne etapu**, i **pochylanie Char** sekcji zostaną odświeżone w związku z tym.

+ **Nierówne etapu** — w drugiej sekcji wyświetlany etapów, które zawierać nierówne zadań spełniających kryteria określone powyżej. Jeśli istnieje więcej niż jednego zadania niesymetryczne w etapie, tabeli niesymetryczny etap są wyświetlane tylko najbardziej niesymetryczne zadania (np. największy danych dla danych pochylić).

    ![Sekcja2 niesymetryczność danych](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Pochylanie wykresu** — po wybraniu wiersz w tabeli przemieszczania niesymetryczność Wyświetla wykres niesymetryczność dystrybucje szczegółowe zadań na podstawie danych odczytu i czasu wykonywania. Niesymetryczne zadania są oznaczone kolorem czerwonym i normalnego zadania są oznaczone kolorem niebieskim. Uwaga dotycząca wydajności wykresu wyświetla tylko maksymalnie 100 przykładowe zadania podrzędne. Szczegóły zadania są wyświetlane w panelu z prawej u dołu.

    ![Section3 niesymetryczność danych](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Niesymetryczność czasu
**Czasowego** karcie wyświetlane są niesymetryczne zadań na podstawie czasu wykonywania zadania. 

+ **Określ parametry** — pierwsza sekcja wyświetla parametry, które są używane do wykrywania czasowego. Jest kryteria domyślne, aby wykryć niesymetryczność czasu: czas wykonania zadania jest większy niż 3 razy z Średni czas wykonywania i czasu wykonywania zadania jest większy niż 30 sekund. Można zmienić parametrów, zgodnie z potrzebami. **Nierówne etapu** i **pochylanie wykresu** podobnie jak wyświetlić odpowiednie etapy i informacji o zadaniach **Niesymetryczności danych** powyżej kartę.

+ Kliknij przycisk **czasowego**, a następnie odfiltrowany wynik jest wyświetlany w **nierówne etapu** sekcji zgodnie z parametrami w sekcji **Określanie parametrów**. Kliknij jeden element w **nierówne etapu** sekcji, a następnie odpowiedni wykres jest sporządzone w section3, oraz szczegóły zadania są wyświetlane w panelu z prawej u dołu.

    ![Sekcja2 niesymetryczność czasu](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analiza użycia funkcji wykonawczej
Wykres użycia funkcji wykonawczej wizualizuje Spark rzeczywiste wykonawca alokacji i uruchomieniu stan zadania.  

+ Kliknij przycisk **analizy użycia funkcji wykonawczej**, a następnie przygotowanego krzywych cztery typy o użycie funkcji wykonawczej, w tym **przydzielone Executors**, **systemem Executors**, **bezczynne Executors**, i **maksymalna liczba wystąpień funkcji wykonawczej**. Dotyczące przydzielonego executors zwiększania lub zmniejszania przydzielonego executors każdego "Wykonawca dodany" lub "Usunięte wykonawca" zdarzenia, "Osi czasu zdarzeń" można sprawdzić na karcie "Zadania" więcej porównanie.

    ![Karta executors](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Kliknij ikonę kolor, aby zaznacz lub odznacz odpowiadająca jej zawartość do wszystkich wersji roboczych.

    ![Wybierz wykres](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)


## <a name="faq"></a>Często zadawane pytania

### <a name="1-revert-to-community-version"></a>1. Przywróć wersję community

Aby powrócić do wersji community, wykonaj następujące czynności:

1. Otwórz klaster w Ambari. Kliknij przycisk **Spark2** w panelu po lewej stronie.
2. Kliknij przycisk **Configs** kartę.
3. Rozwiń grupę **niestandardowe spark2 — domyślne**.
4. Kliknij przycisk **Dodaj właściwość**, Dodaj **spark.ui.enhancement.enabled=false**, Zapisz.
5. Ustawia właściwość **false** teraz.
6. Kliknij przycisk **Zapisz** Aby zapisać konfigurację.

    ![Wyłącza funkcję](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Kliknij przycisk **Spark2** w lewym panelu w obszarze **Podsumowanie** kliknij pozycję **serwer historii Spark2**.

    ![ponowne uruchomienie komputera serwer1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Uruchom ponownie serwer historii, klikając **ponowne uruchomienie** z **serwer historii Spark2**.

    ![Uruchom ponownie serwer2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Odśwież serwer historii platformy Spark w internetowym interfejsie użytkownika, będzie można przywrócić do wersji community.

### <a name="2-upload-history-server-event"></a>2. Przekazywanie zdarzeń serwera historii

Jeśli napotkasz błąd serwera historii postępuj zgodnie z instrukcjami, aby zapewnić zdarzenia:
1. Pobierz zdarzenia, klikając **Pobierz** w historii serwera internetowego interfejsu użytkownika.

    ![Pobieranie zdarzenia](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Kliknij przycisk **przesłanie nam opinii** z karty danych/wykresu.

    ![Wykres opinii](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Podaj tytuł i opis błędu, przeciągnij plik zip do pola edycji, a następnie kliknij przycisk **Prześlij nowy problem**.

    ![problem z pliku](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Uaktualnij plik jar w scenariuszu poprawki

Do uaktualnienia przy użyciu poprawek, należy użyć poniższego skryptu, która spowoduje uaktualnienie enhancement.jar* platformy spark.

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

**Użycie**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**Przykład**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar` 

**Aby użyć pliku bash z witryny Azure portal**

1. Uruchom [witryny Azure Portal](https://ms.portal.azure.com)i wybierz klaster.
2. Kliknij przycisk **akcji skryptu**, następnie **Prześlij nowe**. Wykonaj **Prześlij akcję skryptu** formularza, a następnie kliknij przycisk **Utwórz** przycisku.
    
    + **Typ skryptu**: Wybierz **niestandardowe**.
    + **Nazwa**: Określ nazwę skryptu.
    + **Identyfikator URI skryptu powłoki systemowej**: Przekaż plik programu bash do klastra prywatnego, a następnie skopiuj adres URL, w tym miejscu. Można również użyć podanego identyfikatora URI.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

   + Sprawdzanie **Head** i **procesu roboczego**.
   + **Parametry**: Ustaw następujące parametry użycia powłoki bash.

     ![przekazanie dzienników lub uaktualnienia poprawki](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issues"></a>Znane problemy

1.  Obecnie usługa działa tylko dla klastra Spark 2.3.

2.  Dane wejściowe i wyjściowe przy użyciu RDD nie będzie widoczna na karcie danych.

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie zasobami klastra Apache Spark w HDInsight](apache-spark-resource-manager.md)
* [Konfigurowanie ustawień platformy Apache Spark](apache-spark-settings.md)


## <a name="contact-us"></a>Skontaktuj się z nami

Jeśli macie jakieś uwagi lub występują inne problemy, korzystając z tego narzędzia, Wyślij wiadomość e-mail na ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
