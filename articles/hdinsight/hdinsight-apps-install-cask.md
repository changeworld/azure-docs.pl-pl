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
ms.openlocfilehash: 9eef1760b7cee3bbdf33122514669b38b0b4d9db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400847"
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Instalowanie aplikacji opublikowanych - pojemnika transportowego danych aplikacji platformy (CDAP)

W tym artykule opisano, jak instalować i uruchamiać [CDAP](http://cask.co/products/cdap/) opublikowana aplikacja platformy Hadoop w usłudze Azure HDInsight. Przegląd platformy aplikacji usługi HDInsight oraz listę z dostępnych niezależnie od producenta oprogramowania (ISV) opublikowanych aplikacji, zobacz [instalacji aplikacji innych firm Hadoop](hdinsight-apps-install-applications.md). Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

## <a name="about-cdap"></a>O CDAP

Tworzenie aplikacji na platformie Hadoop może być trudne.  Brak dużych i wzrostu liczby rozszerzenia technologii Hadoop, które może zająć trochę czasu, aby zintegrować. Przepływ danych monitorowania i zbierania metryk może wymagać tworzenie oddzielnych rozwiązania.

### <a name="how-does-cdap-help"></a>Jak pomaga CDAP?

Pojemnika transportowego danych aplikacji platformy (CDAP) to platforma integracji dla danych Big Data. CDAP umożliwia fokus na tworzeniu aplikacji, a nie na podstawowej infrastruktury.

CDAP używa szczegółowo pojęcia i abstrakcje, które są znane deweloperom. Te obiekty abstrakcyjne Ukryj złożoności wewnętrznych systemach i zachęca możliwość ponownego wykorzystania rozwiązań.

Wywołane rozszerzenie CDAP [Hydrator pojemnika transportowego](http://cask.co/products/hydrator/) udostępnia interfejs użytkownika do opracowywania i zarządzania nimi potoki danych. Potoku danych składa się z różnymi * wtyczek służących do wykonywania zadań, takich jak uzyskiwanie danych, przekształcania, analizy i późniejszej operacji.

Każdy dodatek CDAP ma dobrze zdefiniowanego interfejs tak, aby oceny różnych technologii wystarczy zastępowanie jednej wtyczki na inny bez konieczności touch pozostałej części aplikacji.

CDAP *potoki* zapewnienia wysokiego poziomu obrazkami strumień danych w aplikacji. Tej wizualizacji przedstawia przepływ end-to-end danych z wprowadzanie za pośrednictwem przekształcenia danych i analiz i na koniec do danych zewnętrznych przechowywania.

Poniższy przykład przedstawia potoku danych wysyła strumień danych twitter w czasie rzeczywistym, a następnie odfiltrowuje niektórych tweetów na podstawie wstępnie zdefiniowanych kryteriów. Potok danych przekształcenia danych pierwotnych tweet i przechowywać projekty, czy dane w bardziej czytelnym formacie, następnie grupuje tweetów zgodnie z zestawem wartości i zapisuje wyniki do bazy danych HBase.

![CDAP potoku](./media/hdinsight-apps-install-cask/pipeline.png)

Tego potoku end-to-end jest utworzony przy użyciu **interfejsu użytkownika Hydrator pojemnika transportowego**, za pomocą wtyczki interfejsu i przeciągnij i upuść działanie formularza połączeń między poszczególnymi etapami. Można odizolować i modyfikowanie funkcjonalności każdej wtyczki niezależnie. Przy użyciu CDAP, podobne potoki można wbudowane i zweryfikowane w godzinach. W typowej world Hadoop konstruowanie takich rozwiązań może potrwać kilka dni.

CDAP oferuje również rozszerzenie o nazwie [Tracker pojemnika transportowego](http://cask.co/products/tracker/) wizualnie śledzenia danych, ponieważ przechodzi przez aplikację. Dodaje Tracker pojemnika transportowego *zarządzania danymi* do systemu, aby oficjalnie zarządzanych zasobów danych w całej aplikacji. Można śledzić elementy powiązane każdego punktu danych, zbieranie odpowiednich metryk i dziennik danych w całym procesie inspekcji.

Oto ilustrację jak danych jest przechodzenia w potoku powyżej:

![Śledzenie CDAP](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować tę aplikację w nowym klastrze HDInsight lub istniejącego klastra, musi mieć następującą konfigurację:

* Warstwy klastrów: standardowy
* Typ klastra: HBase
* Wersja klastra: 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>Zainstaluj CDAP opublikowane aplikacji

Aby uzyskać instrukcje krok po kroku dotyczące instalowania tego i innych aplikacjach dostępnych niezależnego dostawcy oprogramowania, przeczytaj [instalacji aplikacji innych firm Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Uruchamianie CDAP

1. Po zakończeniu instalacji, uruchom CDAP z klastrem w portalu Azure, przechodząc do **ustawienia** okienku, wybierając **aplikacji** w obszarze **ogólne** kategorii. W okienku zainstalowane aplikacje Wyświetla listę wszystkich zainstalowanych aplikacji.

    ![CDAP zainstalowanych aplikacji](./media/hdinsight-apps-install-cask/cdap-app.png)

2. Po wybraniu CDAP, zostanie wyświetlony link do strony sieci web i punkt końcowy HTTP, a także ścieżkę punktu końcowego protokołu SSH. Wybierz łącze strony sieci Web.

3. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra.

    ![Authentication](./media/hdinsight-apps-install-cask/auth.png)

4. Po zalogowaniu zostanie wyświetlona strona główna pojemnika transportowego CDAP graficznego interfejsu użytkownika.

    ![Strona główna pojemnika transportowego graficznego interfejsu użytkownika](./media/hdinsight-apps-install-cask/gui.png)

5. Aby zapoznać się z interfejsu CDAP, kliknij przycisk **rynku pojemnika transportowego** łącze menu u góry strony.

    ![Łącze rynku pojemnika transportowego](./media/hdinsight-apps-install-cask/cask-market.png)

6. Wybierz **przykładowy dziennik dostępu** z listy.

    ![Przykładowy dziennik dostępu](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Kliknij przycisk **obciążenia** o potwierdzenie.

    ![Kliknij przycisk Załaduj](./media/hdinsight-apps-install-cask/market-load.png)

8. Zostanie wyświetlony widok dołączone przykładowych danych. Wybierz **dalej**.

    ![Przykładowy dziennik dostępu - danych widoku](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Wybierz **strumienia** jako typ docelowy, wprowadź nazwę docelowego, a następnie wybierz **Zakończ**.

    ![Przykładowy dziennik dostępu — wybierz miejsce docelowe](./media/hdinsight-apps-install-cask/market-destination.png)

10. Po pomyślnym załadowaniu datapack wybierz **Wyświetl szczegóły strumienia**.

    ![Pomyślnie przekazano Datapack](./media/hdinsight-apps-install-cask/market-view-details.png)

11. Aby włączyć metadane dla przestrzeni nazw, zaznacz **włączyć** na karcie użycia na stronie szczegółów dziennik dostępu.

    ![Przykładowy dziennik dostępu — załadowano — Włączanie metadanych](./media/hdinsight-apps-install-cask/log-loaded.png)

12. Po włączeniu metadanych, zobaczysz wykres przedstawiający informacje komunikatów inspekcji.

    ![Przykładowy dziennik dostępu - metadanych włączone](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Aby eksplorować dane dziennika, wybierz **Eksploruj** ikona u góry strony.

    ![Dostęp do dziennika przykładowe — Poznaj](./media/hdinsight-apps-install-cask/log-explore.png)

14. Możesz sprawdzić przykładowe zapytanie SQL. Możesz zmodyfikować go jako odpowiednie, a następnie wybierz opcję **Execute**.

    ![Przykładowy dziennik dostępu — Poznaj zestawu danych z zapytania](./media/hdinsight-apps-install-cask/log-query.png)

15. Po zakończeniu zapytania wybierz **widoku** ikonę w kolumnie akcje.

    ![Przykładowy dziennik dostępu — zapytania dotyczącego widoku ukończone](./media/hdinsight-apps-install-cask/log-query-view.png)

16. Możesz zobaczyć wyniki zapytania.

    ![Przykładowy dziennik dostępu — wyniki zapytania](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja pojemnika transportowego](http://cask.co/resources/documentation/).
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć aplikację usługi HDInsight nieopublikowane do usługi HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartej na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): Dowiedz się, jak instalować dodatkowe aplikacje przy użyciu akcji skryptu.
* [Użyj węzłami pusty krawędzi w usłudze HDInsight](hdinsight-apps-use-edge-node.md): Dowiedz się, jak uzyskać dostęp do klastrów usługi HDInsight i do badania i obsługiwania aplikacji usługi HDInsight za pomocą węzła krawędzi puste.
