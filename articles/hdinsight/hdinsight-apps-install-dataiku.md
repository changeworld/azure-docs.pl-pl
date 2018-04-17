---
title: Zainstaluj opublikowana aplikacja — DDS Dataiku - Azure HDInsight | Dokumentacja firmy Microsoft
description: Zainstalować i używać aplikacji Hadoop Dataiku DDS innych firm.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: facee9187528fa0c6a74f71ee73636dc23ca35ba
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---dataiku-dds"></a>Instalowanie aplikacji opublikowanych - Dataiku DDS

W tym artykule opisano, jak instalować i uruchamiać [Dataiku DDS](https://www.dataiku.com/) opublikowana aplikacja platformy Hadoop w usłudze Azure HDInsight. Przegląd platformy aplikacji usługi HDInsight oraz listę z dostępnych niezależnie od producenta oprogramowania (ISV) opublikowanych aplikacji, zobacz [instalacji aplikacji innych firm Hadoop](hdinsight-apps-install-applications.md). Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

## <a name="about-dataiku-dss"></a>O Dataiku DSS

Dataiku [Studio nauki danych (DSS)](https://www.dataiku.com/dss/features/connectivity/), to platforma nauki współpracy danych, która umożliwia analityków danych skompilować i rozwiązań analitycznych. Oferta DSS jako HDInsight aplikacji umożliwia używanie nauki danych do tworzenia rozwiązania typu Big Data i uruchom je na klasy enterprise i skali.

DSS służy do implementacji kompletnego rozwiązania analityczne, zaczynając od wprowadzanie danych, przygotowania i przetwarzania. Rozwiązanie DSS mogą również obejmować szkolenia i stosuje modele uczenia maszyny, wizualizację i następnie operacyjnych.

DSS można zainstalować w usłudze HDInsight z użyciem klastrów platformy Hadoop lub Spark. DSS można zainstalować na istniejących klastrów uruchomione lub podczas tworzenia nowych klastrów. DSS obsługuje również przy użyciu magazynu obiektów Blob platformy Azure jako łącznik do odczytywania danych.

DSS służy do tworzenia projektów, które następnie można wygenerować zadania MapReduce lub Spark. Te zadania są wykonywane jako regularne zadania MapReduce lub Spark w usłudze HDInsight, więc można skalować klastra na żądanie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować tę aplikację w nowym klastrze HDInsight lub istniejącego klastra, musi mieć następującą konfigurację:

* Klaster tier(s): Standard, Premium
* Typy klastrów: Hadoop, Spark
* Klaster wersje: 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Zainstaluj Dataiku DSS opublikowane aplikacji

Aby uzyskać instrukcje krok po kroku dotyczące instalowania tego i innych aplikacjach dostępnych niezależnego dostawcy oprogramowania, przeczytaj [instalacji aplikacji innych firm Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Uruchamianie Dataiku DSS

1. Po zakończeniu instalacji, możesz uruchomić DSS z klastrem w portalu Azure, przechodząc do **ustawienia** okienko, klikając **aplikacji** w obszarze **ogólne** kategorii. W okienku zainstalowane aplikacje Wyświetla listę zainstalowanych aplikacji.

    ![DSS Dataiku zainstalowanych aplikacji](./media/hdinsight-apps-install-dataiku/app.png)

2. Po wybraniu DSS w usłudze HDInsight, zostanie wyświetlony link do strony sieci web i ścieżkę punktu końcowego protokołu SSH. Wybierz łącze strony sieci Web.

3. Przy pierwszym uruchomieniu są prezentowane z formularza, aby utworzyć nowe konto Dataiku bezpłatnie lub zaloguj się do istniejącego konta. Masz również opcję, aby uruchomić bezpłatną wersję próbną 2 tygodni z [Enterprise Edition](https://www.dataiku.com/dss/editions/). Z tego punktu istnieje możliwość dalszego wprowadzania klucza licencji dla wersji Enterprise lub przy użyciu Community Edition.

4. Po zakończeniu pracy z opcji wybranych licencji, są prezentowane z formularz logowania. Wprowadź poświadczenia domyślne wyświetlane przed formularz logowania.

W poniższych krokach przedstawiono prosty pokaz.

1. [Pobierz przykładowe zamówień CSV](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. Na pulpicie nawigacyjnym DSS wybierz **+** (nowy projekt) łącza w menu po lewej stronie, aby utworzyć nowy projekt.

    ![Nowe łącze projektu](./media/hdinsight-apps-install-dataiku/new-project.png)

3. W formularzu nowego projektu wpisz **nazwa**. **Klucz Projekt** jest automatycznie wypełniany sugerowane wartości. W takim przypadku należy wprowadzić "Zamówienia". Kliknij przycisk **Utwórz**.

    ![Nowy formularz projektu](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Wybierz **+ SWÓJ pierwszy zestaw danych IMPORTU** w nowej strony projektu.

    ![Przekazywanie pliku](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Wybierz **przekazać pliki** w obszarze **pliki** listy zestawu danych. Wyświetlone okno dialogowe przekazywania. Polecenie Dodaj plik, wybierz `haiku_shirt_sales.csv` pliku możesz pobrać i zweryfikować.

6. Plik jest przekazywany do DSS. Sprawdź, czy DSS wykryty w formacie CSV poprawnie, klikając przycisk.

    ![Przekazywanie pliku](./media/hdinsight-apps-install-dataiku/preview.png)

7. Importowanie jest prawie idealne. Plik CSV jest za pomocą separatora kartę. Można zauważyć, że dane są w formie tabeli z kolumnami o nazwie funkcji oraz wiersze, które reprezentują uwagi. Jeden błąd jest najwyraźniej plik zawiera pusty wiersz między nagłówka i danych. Aby naprawić ten błąd, wprowadź `1` w **Przejdź dalej wierszy** pola.

    ![Zapisz](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Nadaj nazwę nowy zestaw danych. Wprowadź **haiku_shirt_sales** w polu u góry ekranu, następnie wybierz **Utwórz** przycisku.

9. Zobaczysz tabelarycznego widoku danych, której będzie można rozpocząć eksplorowanie go. Dla każdej kolumny, powinny być widoczne czy Dataiku nauki Studio wykrył typu danych w _blue_ — w tym przypadku tekstu, liczby lub daty (nieprzeanalizowanej). Miernika wskazuje stosunek kolumny, dla którego wartości prawdopodobnie nie jest zgodny z typem (zaznaczony kolorem czerwonym) lub brakuje (pusta). W tym przykładzie zestawie danych dział ma zarówno puste wartości, jak i nieprawidłowe dane.

    ![Widok tabelaryczny](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Manipulowanie danymi

Rzeczywiste dane prawie zawsze jest lepiej i rzadko jej starannie spakowaniu. Czyszczenie danych zwykle wymaga łańcucha skryptów i skojarzonej logiki biznesowej. Dataiku DSS zawiera dedykowany **laboratorium** narzędzie do tworzenia tego zadania jest bardziej przyjazny dla użytkownika.

1. Polecenie **laboratorium** w prawym górnym rogu.

    ![Przycisk laboratorium](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. Zostanie otwarte okno laboratorium. Laboratorium jest wielokrotnie powtarzane którym pracujesz nad swojego zestawu danych, aby uzyskać dalsze do niego. W tym samouczku przedstawiono aspekt Visual analizy. Wybierz **nowy** znajdujący się poniżej Visual analizy. Monit o określenie nazwy dla analizy. Pozostaw nazwę domyślną teraz, a następnie kliknij przycisk **Utwórz**.

    ![Tworzenie laboratorium](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Wybierz **Statystyka szybkie kolumn** przycisk w prawym górnym rogu strony.

    ![Statystyka szybkie kolumn](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Zobacz statystyki dla typów danych i wartości wyświetlane na wykresach oparte na osi czasu w obszarze **kolumn szybki przegląd** okienka.

    ![Szybki przegląd kolumn](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Teraz można eksplorować DSS przy użyciu przykładowych danych. Można wyczyścić i pracować z danymi i utworzyć nowe.

Aby uzyskać szczegółowe samouczki, przeczytaj [Dowiedz się DSS Dataiku](https://www.dataiku.com/learn/).

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja Dataiku DSS](https://doc.dataiku.com/dss/latest/).
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć aplikację usługi HDInsight nieopublikowane do usługi HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartej na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): Dowiedz się, jak instalować dodatkowe aplikacje przy użyciu akcji skryptu.
* [Użyj węzłami pusty krawędzi w usłudze HDInsight](hdinsight-apps-use-edge-node.md): Dowiedz się, jak uzyskać dostęp do klastrów usługi HDInsight i do badania i obsługiwania aplikacji usługi HDInsight za pomocą węzła krawędzi puste.
