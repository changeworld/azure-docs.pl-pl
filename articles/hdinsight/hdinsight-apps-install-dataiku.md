---
title: Instalowanie opublikowanej aplikacji — Dataiku DDS — Azure HDInsight
description: Zainstalować i używać aplikacji platformy Hadoop innych firm Dataiku DDS.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 8d01b94ce4af6e3f5bcdc05ce84ef6deb1d3cae6
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39598775"
---
# <a name="install-published-application---dataiku-dds"></a>Instalowanie opublikowanej aplikacji — Dataiku DDS

W tym artykule opisano sposób instalowania i uruchamiania [Dataiku DDS](https://www.dataiku.com/) opublikowanych aplikacji platformy Hadoop w usłudze Azure HDInsight. Omówienie platformy aplikacji HDInsight i listę z dostępnych niezależnym dostawcą oprogramowania (ISV) opublikowanych aplikacji, zobacz [instalowanie aplikacji platformy Hadoop innych firm](hdinsight-apps-install-applications.md). Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

## <a name="about-dataiku-dss"></a>Temat Dataiku DSS

Dataiku [Data Science Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/), to platforma do nauki o danych współpracy, która umożliwia analitykom danych, tworzenie i dostarczanie rozwiązań analitycznych. Oferty DSS jako HDInsight aplikacji pozwala używać do nauki o danych do tworzenia rozwiązań do obsługi dużych ilości danych i ich uruchamianie w klasy korporacyjnej i skalowania.

Można użyć DSS implementacji kompletnego rozwiązania analityczne, zaczynając od pozyskiwania, przygotowywania i przetwarzania. Rozwiązanie DSS może również obejmować szkolenia i stosowanie modeli uczenia maszynowego, wizualizacji i operacjonalizacji następnie.

DSS można zainstalować na HDInsight przy użyciu klastrów usługi Hadoop i Spark. DSS można zainstalować w istniejących klastrach uruchomionej lub podczas tworzenia nowych klastrów. DSS obsługuje również związany z odczytem danych przy użyciu usługi Azure Blob storage jako łącznik.

DSS służy do tworzenia projektów, które następnie można wygenerować zadań MapReduce lub platformą Spark. Te zadania są wykonywane jako zwykłe zadania MapReduce lub platformą Spark na HDInsight, dzięki czemu można skalować klastra na żądanie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować tę aplikację w nowym klastrze HDInsight lub istniejącego klastra, musisz mieć następującą konfigurację:

* Warstwy klastrów: standardowa, Premium
* Typy klastrów: Hadoop, Spark
* Wersje klastra: 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Instalowanie Dataiku DSS opublikowanej aplikacji

Aby uzyskać instrukcje krok po kroku dotyczące instalowania tego i innych dostępnych aplikacji niezależnych dostawców oprogramowania, przeczytaj [instalowanie aplikacji platformy Hadoop innych firm](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Uruchom Dataiku DSS

1. Po zakończeniu instalacji możesz uruchomić DSS z klastrem w witrynie Azure portal, przechodząc do **ustawienia** okienku kliknięcie **aplikacje** w obszarze **ogólne** kategorii. W okienku zainstalowane aplikacje zawiera listę zainstalowanych aplikacji.

    ![Aplikacja zainstalowanych Dataiku DSS](./media/hdinsight-apps-install-dataiku/app.png)

2. Po wybraniu DSS na HDInsight, zostanie wyświetlony link do strony sieci web i ścieżka punktu końcowego protokołu SSH. Wybierz łącze strony sieci Web.

3. Przy pierwszym uruchomieniu zostanie wyświetlony formularz, aby bezpłatnie utworzyć nowe konto Dataiku lub zalogować się do istniejącego konta. Istnieje również możliwość Rozpocznij bezpłatny okres próbny 2-tygodniowych z [Enterprise Edition](https://www.dataiku.com/dss/editions/). Od tej pory masz możliwość kontynuowanie wprowadzania klucza licencji dla wersji Enterprise Edition lub za pomocą Community Edition.

4. Po zakończeniu wybrana opcja wybrany licencji, zostanie wyświetlony formularz logowania. Wprowadź poświadczenia domyślne wyświetlane przed formularz logowania.

W poniższych krokach przedstawiono prosty pokaz.

1. [Pobierz przykładowe zamówienia CSV](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. Na pulpicie nawigacyjnym DSS wybierz **+** (nowy projekt) link w menu po lewej stronie, aby utworzyć nowy projekt.

    ![Nowe łącze projektu](./media/hdinsight-apps-install-dataiku/new-project.png)

3. W formularzu nowego projektu wpisz **nazwa**. **Klucz projektu** jest automatycznie wypełniany sugerowana wartość. W takich przypadkach wprowadź "Orders". Kliknij przycisk **Utwórz**.

    ![Formularz nowego projektu](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Wybierz **+ SWÓJ pierwszy zestaw danych IMPORTU** na nowej stronie projektu.

    ![Przekazywanie pliku](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Wybierz **przekazywanie plików** w obszarze **pliki** lista zestawów danych. Zostanie wyświetlona przy użyciu okna dialogowego przekazywania. Kliknij pozycję Dodaj plik, wybierz `haiku_shirt_sales.csv` pliku, dla którego pobrano i sprawdzania poprawności.

6. Plik jest przekazywany do DSS. Sprawdź, jeśli DSS wykryte w formacie CSV poprawnie, klikając przycisk podglądu.

    ![Przekazywanie pliku](./media/hdinsight-apps-install-dataiku/preview.png)

7. Importowanie jest prawie doskonałą. Plik CSV jest za pomocą separatora kartę. Widać, że dane są w formacie tabeli z kolumnami o nazwie funkcji i wierszy, które reprezentują uwagi. Jeden błąd jest prawdopodobnie plik zawiera pusty wiersz między nagłówkiem i danych. Aby naprawić ten błąd, należy wprowadzić `1` w **przejść dalej wiersze** pola.

    ![Zapisz](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Nazwij nowy zestaw danych. Wprowadź **haiku_shirt_sales** w polu u góry ekranu, a następnie zaznacz **Utwórz** przycisku.

9. Zobaczysz tabelaryczny widok danych, której będzie można rozpocząć eksplorowanie go. Dla każdej kolumny, powinien zostać wyświetlony, że Dataiku Science Studio wykrył typu danych w _niebieski_ — w tym przypadku, tekst, liczby lub daty (nieanalizowanych). Miernik wskazuje współczynnik kolumn, dla którego wartości prawdopodobnie nie jest zgodny z typem (na czerwono) lub Brak (pusty). W tym przykładzie zestawie danych dział zawiera wartości puste i nieprawidłowe dane.

    ![Widok tabelaryczny](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Manipulowanie danymi

Jest prawie zawsze nieuporządkowane rzeczywistych danych, a rzadko jest on starannie wyrównywać spakowany. Czyszczenie danych zazwyczaj wymaga łańcucha skrypty i skojarzonej logiki biznesowej. Dataiku DSS zapewnia dedykowany **laboratorium** narzędzia, aby zadanie było bardziej czytelne.

1. Kliknij pozycję **laboratorium** w prawym górnym rogu.

    ![Przycisk laboratorium](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. Zostanie otwarte okno laboratorium. Laboratorium jest iteracyjne gdzie pracujesz na twoim zestawie danych, aby uzyskać dalsze tę sytuację. Ten samouczek przedstawia aspekt analizy wizualnej. Wybierz **New** znajdujący się poniżej analizy wizualnej. Monit, aby określić nazwę dla analizy. Pozostaw nazwę domyślną teraz, a następnie kliknij przycisk **Utwórz**.

    ![Tworzenie laboratorium](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Wybierz **statystyki kolumn szybkie** przycisk w prawym górnym rogu strony.

    ![Statystyki kolumn szybki](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Zobacz statystyki dla typów danych i wartości wyświetlane na wykresach oparte na osi czasu w obszarze **szybki wgląd w kolumnach** okienka.

    ![Szybki wgląd w kolumnach](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Teraz możesz eksplorować DSS przy użyciu przykładowych danych. Możesz wyczyścić i pracować z danymi i tworzyć nowe wizualizacje.

Aby uzyskać szczegółowe samouczki, przeczytaj [informacje Dataiku DSS](https://www.dataiku.com/learn/).

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja Dataiku DSS](https://doc.dataiku.com/dss/latest/).
* [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć nieopublikowane aplikację HDInsight w usłudze HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): Dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Używanie pustych węzłów brzegowych w HDInsight](hdinsight-apps-use-edge-node.md): Dowiedz się, jak użyć pustego węzła krawędzi do uzyskiwania dostępu do klastrów HDInsight, a także do testowania i obsługi aplikacji HDInsight.
