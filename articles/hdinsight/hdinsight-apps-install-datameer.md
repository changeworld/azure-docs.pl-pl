---
title: Instalowanie opublikowanej aplikacji — Datameer — Azure HDInsight
description: Zainstalować i używać aplikacja Datameer Apache Hadoop innych firm.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 97d99aa59c490cf2dcdd4a69f32411a051942d36
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037808"
---
# <a name="install-published-application---datameer"></a>Instalowanie opublikowanej aplikacji — Datameer

W tym artykule opisano sposób instalowania i uruchamiania [Datameer](https://www.datameer.com/) opublikowanych aplikacji Apache Hadoop w usłudze Azure HDInsight. Omówienie platformy aplikacji HDInsight i listę z dostępnych niezależnym dostawcą oprogramowania (ISV) opublikowanych aplikacji, zobacz [instalowanie aplikacji platformy Hadoop innych firm](hdinsight-apps-install-applications.md). Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

## <a name="about-datameer"></a>Temat Datameer

Aplikacja Datameer jest natywnych aplikacji dla usługi Hadoop platformy, rozszerzanie istniejących możliwości usługi Azure HDInsight i udostępnia szybkie integrowanie, przygotowywania i analizy danych ze strukturą i bez struktury. Datameer mogą uzyskiwać dostęp do ponad 70 źródeł formaty: ze strukturą, częściową strukturą i bez struktury. Możesz bezpośrednio przekazać dane, lub użyć ich łącza unikatowe dane do pobierania danych na żądanie. Funkcje samoobsługowego firmy Datameer i dobrze znanych arkusza kalkulacyjnego interfejsu zmniejsza złożoność technologii danych Big Data i skrócenie czasu wprowadzania na wgląd. Interfejs arkusza kalkulacyjnego zapewnia prosty mechanizm wprowadzania deklaratywne formuł, które następnie są tłumaczone na zoptymalizowane pod kątem zadań usługi Hadoop. Z Datameer i analizy biznesowej (BI) i program Excel umiejętności używając usługi Hadoop w chmurze szybko. Aby uzyskać więcej informacji, zobacz [dokumentacji Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować tę aplikację w nowym klastrze HDInsight lub istniejącego klastra, musisz mieć następującą konfigurację:

* Warstwy klastrów: standardowa
* Typ klastra: Hadoop
* Wersja klastra: 3.4

## <a name="install-the-datameer-published-application"></a>Instalowanie Datameer opublikowanej aplikacji

Aby uzyskać instrukcje krok po kroku dotyczące instalowania tego i innych dostępnych aplikacji niezależnych dostawców oprogramowania, przeczytaj [instalowanie aplikacji platformy Hadoop innych firm](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Uruchom Datameer

1. Po zakończeniu instalacji możesz uruchomić Datameer z klastrem w witrynie Azure portal, przechodząc do **ustawienia** okienku kliknięcie **aplikacje** w obszarze **ogólne** kategorii . W okienku zainstalowane aplikacje zawiera listę zainstalowanych aplikacji.

    ![Datameer zainstalowanych aplikacji](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Po wybraniu Datameer, zostanie wyświetlony link do strony sieci web i ścieżka punktu końcowego protokołu SSH. Wybierz łącze strony sieci Web.

3. Przy pierwszym uruchomieniu dostępne są dwie opcje licencji: albo bezpłatne 14-dniowa wersja próbna, lub przeprowadź aktywację istniejącej licencji.

    ![Opcje licencji](./media/hdinsight-apps-install-datameer/license.png)

4. Po zakończeniu wybrana opcja wybrany licencji, zostaną wyświetlone za pomocą formularza logowania. Wprowadź poświadczenia domyślne wyświetlane przed formularz logowania. Po zalogowaniu należy zaakceptować umowę oprogramowania, aby kontynuować.

    ![Login](./media/hdinsight-apps-install-datameer/login.png)

Poniższe kroki pokazują pokaz "Hello World".

1. [Pobierz plik CSV](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Kliknij przycisk **+** Zaloguj się w górnej części pulpitu nawigacyjnego Datameer, a następnie kliknij przycisk **przekazywania plików**.

    ![Przekazywanie pliku](./media/hdinsight-apps-install-datameer/upload.png)

3. W oknie dialogowym przekazywanie, wyszukiwanie i wybieranie **Hello World.csv** pobrany plik. Upewnij się, że **typ pliku** jest ustawiony do pliku CSV / TSV. Wybierz opcję **Dalej**. Następnie klikasz **dalej** aż do zakończenia pracy przez kreatora.

    ![Przekazywanie pliku](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Nadaj plikowi nazwę **Witaj, świecie** poniżej nowy Folder. Zmień nazwę nowego folderu "Pokaz". Wybierz pozycję **Zapisz**.

    ![Zapisz](./media/hdinsight-apps-install-datameer/save.png)

5. Kliknij przycisk **+** zalogować się ponownie i wybrać **skoroszytu** Aby utworzyć nowy skoroszyt danych.

    ![Dodaj skoroszytu](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Rozwiń **danych** folderze **FileUploads**, a następnie **pokaz** folderu utworzonego podczas zapisywania pliku "Hello World". Wybierz **Witaj, świecie** formularza listy plików, a następnie kliknij przycisk **Dodaj dane**.

    ![Zapisz](./media/hdinsight-apps-install-datameer/select-file.png)

7. Zobaczysz dane załadowane w interfejsu przypominającego arkusz kalkulacyjny. Aby wybrać podzbiór danych, wybierz **filtru** przycisku na pasku narzędzi.

    ![Przycisk Filtruj](./media/hdinsight-apps-install-datameer/filter-button.png)

8. W oknie dialogowym Zastosuj filtr, wybierz **Miasto** kolumnie **jest równa** operatora i typu **Chicago** w polu tekstowym filtru. Sprawdź **Tworzenie filtru w nowy arkusz** pole wyboru, następnie wybierz pozycję **utworzyć filtr**.

    ![Zastosuj filtr](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Zapisz skoroszyt, klikając **pliku**, następnie **Zapisz**. Podaj nazwę, np. "Hello World skoroszytu".

10. Dostępne są opcje, jak i kiedy uruchamiać skoroszytu. Na razie wszystkich opcji Pozostaw ich wartości domyślne, a następnie sprawdź **Start obliczeń proces niezwłocznie po Zapisz**i wybierz **Zapisz**.

    ![Zapisz skoroszyt](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer udostępnia narzędzia zaawansowane wizualizacje. Aby wyświetlić dane, należy utworzyć grafikę informacyjną dotyczącą. Wybierz **+** Zaloguj się w górnej części pulpitu nawigacyjnego, a następnie wybierz **grafikę informacyjną dotyczącą**.

    ![Dodaj grafikę informacyjną](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Przeciągnij element widget wykresu słupkowego z listy elementów widget po lewej stronie, jak pokazano w kroku 1 na poniższym diagramie. Następnie przejdź do folderu danych w przeglądarce danych po prawej stronie, a następnie rozwiń skoroszytu, w arkuszu dodawana za pomocą filtru (krok 2). Przeciągnij **nazwa** kolumny w górnej części wykresu słupkowego i upuszczania do **etykiety** docelowych można ustawić skoroszytu w kolumnie Nazwa jako pole etykiety wykresu (krok 3).

    ![Grafika informacyjna](./media/hdinsight-apps-install-datameer/infographic.png)

13. Aby ustawić wiek jako osi Y wykresu, przeciągnij **wiek** kolumny na wykresie **danych** pola.

    ![Grafika informacyjna](./media/hdinsight-apps-install-datameer/infographic-age.png)

Gratulacje! Utworzono wizualizację danych bez pisania żadnego kodu. Teraz możesz eksplorować, iż dodatkowe wizualizacje.

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć nieopublikowane aplikację HDInsight w usłudze HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): Dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Używanie pustych węzłów brzegowych w HDInsight](hdinsight-apps-use-edge-node.md): Dowiedz się, jak użyć pustego węzła krawędzi do uzyskiwania dostępu do klastrów HDInsight, a także do testowania i obsługi aplikacji HDInsight.
