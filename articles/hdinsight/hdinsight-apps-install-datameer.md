---
title: Zainstaluj opublikowana aplikacja — Datameer - Azure HDInsight | Dokumentacja firmy Microsoft
description: Zainstalować i używać aplikacji Hadoop Datameer innych firm.
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
ms.openlocfilehash: 5008056ae2274d058706649f286b91b71feadc27
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---datameer"></a>Instalowanie aplikacji opublikowanych - Datameer

W tym artykule opisano, jak instalować i uruchamiać [Datameer](https://www.datameer.com/) opublikowana aplikacja platformy Hadoop w usłudze Azure HDInsight. Przegląd platformy aplikacji usługi HDInsight oraz listę z dostępnych niezależnie od producenta oprogramowania (ISV) opublikowanych aplikacji, zobacz [instalacji aplikacji innych firm Hadoop](hdinsight-apps-install-applications.md). Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

## <a name="about-datameer"></a>O Datameer

Datameer jest aplikacji natywnej Hadoop platformy, rozszerzanie istniejących możliwości usługi Azure HDInsight i szybkie integracji, przygotowywania i analizy danych strukturalnych i bez struktury. Datameer mogą uzyskiwać dostęp do więcej niż 70 źródeł i formaty: strukturalnych częściową strukturą i bez struktury. Można bezpośrednio przekazywanie danych lub użyj ich łączy unikatowe dane do pobierania danych na żądanie. Funkcji samoobsługi w Datameer i znanych arkusza kalkulacyjnego interfejsu zmniejsza się złożoność danych Big Data technologii i przyspiesza czas do wglądu. Interfejs arkusza kalkulacyjnego zapewnia prosty mechanizm wprowadzania deklaratywne formuł, które następnie są tłumaczone na zoptymalizowanych pod kątem zadania usługi Hadoop. Z Datameer i analizy biznesowej (BI) i umiejętności programu Excel używając platformy Hadoop w chmurze szybko. Aby uzyskać więcej informacji, zobacz [dokumentacji Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować tę aplikację w nowym klastrze HDInsight lub istniejącego klastra, musi mieć następującą konfigurację:

* Warstwy klastrów: standardowy
* Typ klastra: Hadoop
* Wersja klastra: 3.4

## <a name="install-the-datameer-published-application"></a>Zainstaluj Datameer opublikowane aplikacji

Aby uzyskać instrukcje krok po kroku dotyczące instalowania tego i innych aplikacjach dostępnych niezależnego dostawcy oprogramowania, przeczytaj [instalacji aplikacji innych firm Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Uruchamianie Datameer

1. Po zakończeniu instalacji, możesz uruchomić Datameer z klastrem w portalu Azure, przechodząc do **ustawienia** okienko, klikając **aplikacji** w obszarze **ogólne** kategorii . W okienku zainstalowane aplikacje Wyświetla listę zainstalowanych aplikacji.

    ![Datameer zainstalowanych aplikacji](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Po wybraniu Datameer, zostanie wyświetlony link do strony sieci web i ścieżkę punktu końcowego protokołu SSH. Wybierz łącze strony sieci Web.

3. Przy pierwszym uruchomieniu, dostępne są dwie opcje licencji: albo bezpłatnej 14-dniowej wersji próbnej, lub aktywacji licencji istniejących.

    ![Opcje licencji](./media/hdinsight-apps-install-datameer/license.png)

4. Po zakończeniu pracy z licencji wybranych opcji, zostanie wyświetlony formularz logowania. Wprowadź poświadczenia domyślne wyświetlane przed formularz logowania. Po zalogowaniu, należy zaakceptować umowę dotyczącą oprogramowania, aby kontynuować.

    ![Login](./media/hdinsight-apps-install-datameer/login.png)

W poniższej procedurze pokazano pokaz "Hello World".

1. [Pobierz przykładowe CSV](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Kliknij przycisk **+** Zaloguj się na pulpicie nawigacyjnym Datameer, a następnie kliknij przycisk **przekazywanie pliku**.

    ![Przekazywanie pliku](./media/hdinsight-apps-install-datameer/upload.png)

3. W oknie dialogowym przekazywania, wyszukaj i wybierz **Hello World.csv** pobrany plik. Upewnij się, że **typ pliku** ustawiono CSV / TSV. Wybierz opcję **Dalej**. Klikasz **dalej** aż do zakończenia pracy przez kreatora.

    ![Przekazywanie pliku](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Nadaj nazwę plikowi **Hello World** poniżej nowy Folder. Zmień nazwę nowego folderu "Demo". Wybierz pozycję **Zapisz**.

    ![Zapisz](./media/hdinsight-apps-install-datameer/save.png)

5. Kliknij przycisk **+** Zaloguj się ponownie, a następnie wybierz **skoroszytu** Aby utworzyć nowy skoroszyt danych.

    ![Dodaj skoroszytu](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Rozwiń węzeł **danych** folderu, **FileUploads**, a następnie **pokaz** folder utworzony podczas zapisywania pliku "Hello World". Wybierz **Hello World** tworzą listę plików, a następnie kliknij przycisk **Dodaj danych**.

    ![Zapisz](./media/hdinsight-apps-install-datameer/select-file.png)

7. Czy widzisz dane załadowane w interfejsie arkusza kalkulacyjnego. Aby wybrać podzbiór danych, wybierz **filtru** przycisku w pasku narzędzi.

    ![Przycisk Filtruj](./media/hdinsight-apps-install-datameer/filter-button.png)

8. W oknie dialogowym Filtr wybierz **miasta** kolumny, **jest równe** operatora i typu **Chicago** w polu tekstowym filtru. Sprawdź **Utwórz filtr w nowym arkuszu** pole wyboru, zaznacz **utworzyć filtr**.

    ![Zastosuj filtr](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Zapisz skoroszyt, klikając **pliku**, następnie **zapisać**. Podaj nazwę, na przykład "Hello World skoroszytu".

10. Dostępne są opcje, jak i kiedy uruchamiać skoroszytu. Obecnie wszystkie opcje pozostaw wartości domyślne, a następnie sprawdź **Zapisz bezpośrednio po rozpoczęcia obliczania procesu**i wybierz **zapisać**.

    ![Zapisz skoroszyt](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer udostępnia zaawansowane wizualizacje narzędzia. Aby wyświetlić dane, należy utworzyć Infographic. Wybierz **+** Zaloguj się na pulpicie nawigacyjnym, a następnie wybierz **Infographic**.

    ![Dodaj Infographic](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Przeciągnij element widget wykresu słupkowego z listy widżetów po lewej stronie, jak pokazano w kroku 1 na poniższym diagramie. Następnie przejdź do folderu dane w przeglądarce danych po prawej stronie, a następnie rozwiń skoroszyt, arkusz dodawana z filtrem (krok 2). Przeciągnij **nazwa** kolumny w górnej części wykresu słupkowego i upuszczanie do **etykiety** docelowej, aby ustawić skoroszytu nazwy kolumny jako pole etykiety wykresu (krok 3).

    ![Grafika informacyjna](./media/hdinsight-apps-install-datameer/infographic.png)

13. Aby ustawić wieku jako oś Y wykresu, przeciągnij **wieku** kolumny na wykresie **danych** pola.

    ![Grafika informacyjna](./media/hdinsight-apps-install-datameer/infographic-age.png)

Gratulacje! Po utworzeniu wizualizacji danych bez pisania żadnego kodu. Teraz można eksplorować zmian i dodatkowe wizualizacji.

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć aplikację usługi HDInsight nieopublikowane do usługi HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartej na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): Dowiedz się, jak instalować dodatkowe aplikacje przy użyciu akcji skryptu.
* [Użyj węzłami pusty krawędzi w usłudze HDInsight](hdinsight-apps-use-edge-node.md): Dowiedz się, jak uzyskać dostęp do klastrów usługi HDInsight i do badania i obsługiwania aplikacji usługi HDInsight za pomocą węzła krawędzi puste.
