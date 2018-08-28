---
title: Instalowanie opublikowanej aplikacji — Datameer — Azure HDInsight
description: Zainstalować i używać aplikacji platformy Hadoop innych firm Datameer.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 377dbadd7b696e62d8464258d22d0dd5ed926208
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43106107"
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Instalowanie opublikowanej aplikacji — Cask Data Application Platform (CDAP)

W tym artykule opisano sposób instalowania i uruchamiania [CDAP](http://cask.co/products/cdap/) opublikowanych aplikacji platformy Hadoop w usłudze Azure HDInsight. Omówienie platformy aplikacji HDInsight i listę z dostępnych niezależnym dostawcą oprogramowania (ISV) opublikowanych aplikacji, zobacz [instalowanie aplikacji platformy Hadoop innych firm](hdinsight-apps-install-applications.md). Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

## <a name="about-cdap"></a>Temat CDAP

Tworzenie aplikacji na platformie Hadoop, może być trudne.  Istnieje szereg rozszerzeń technologii Hadoop, które może zająć trochę czasu, aby zintegrować duży i rosnący. Monitorowanie przepływu danych i zbieranie metryk mogą wymagać od tworzenia oddzielnym rozwiązaniu.

### <a name="how-does-cdap-help"></a>Jak pomaga CDAP?

Cask Data Application Platform (CDAP) to platforma integracji dla danych Big Data. CDAP pozwala skoncentrować się na tworzeniu aplikacji, a nie na podstawowej infrastruktury.

CDAP używa szczegółowo pojęcia i abstrakcje, które są znane deweloperom. Te elementy abstrakcji ukryć komplikacje związane z wewnętrznych systemów oraz zachęcić możliwość ponownego wykorzystania rozwiązań.

Wywołuje to rozszerzenie CDAP [Cask Hydrator](http://cask.co/products/hydrator/) udostępnia interfejs użytkownika do tworzenia potoków danych oraz zarządzanie. Potok danych składa się z różnymi * wtyczek, które wykonują zadania, takie jak pozyskiwanie danych, przekształcania, analizy i używane po tej operacji wykonywania operacji.

Wtyczki CDAP ma interfejs dobrze zdefiniowanych, więc ocena różnych technologii jest to kwestia zastępowania jedną wtyczkę przez siebie, bez konieczności touch pozostałe części aplikacji.

CDAP *potoki* zapewnienia wysokiego poziomu połączyć strumień danych w aplikacji. Tę wizualizację przedstawia przepływ end-to-end danych z pozyskiwania, za pomocą przekształcenia danych i analiz, a na koniec w zewnętrznych danych należy przechowywać.

Poniższy przykład potoku danych pozwalają pozyskać dane z usługi twitter w czasie rzeczywistym, a następnie odfiltrowanie niektórych tweety na podstawie wstępnie zdefiniowanych kryteriów. Potok danych przedstawiony przekształca dane pierwotne tweetu i przechowywać projektów, że dane w bardziej czytelnym formacie następnie grupuje tweety na podstawie zestawu wartości i zapisuje wyniki do bazy danych HBase.

![CDAP potoku](./media/hdinsight-apps-install-cask/pipeline.png)

Ten potok end-to-end został skompilowany przy użyciu **Cask Hydrator UI**, za pomocą wtyczki interfejsu i przeciągnij i upuść działanie formularza połączeń między każdym etapie. Można wyizolować i modyfikowanie funkcjonalności każdej wtyczki niezależnie. Za pomocą CDAP, podobne potoków można skompilować i zweryfikowane w godzinach. W typowym Hadoop world konstruowanie takich rozwiązań może potrwać kilka dni.

CDAP oferuje również rozszerzenia o nazwie [Cask Tracker](http://cask.co/products/tracker/) wizualnie śledzenia dane ponieważ przechodzą przez aplikację. Dodaje cask Tracker *nadzór nad danymi* do systemu, aby zasoby danych odbywa się formalnie w całej aplikacji. Można śledzić pochodzenie każdego punktu danych, zbieranie metryk istotnych i dziennik danych w całym procesie inspekcji.

Poniżej przedstawiono sposób dane będą przepływać w potoku powyżej ilustrację:

![Śledzenie CDAP](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować tę aplikację w nowym klastrze HDInsight lub istniejącego klastra, musisz mieć następującą konfigurację:

* Warstwy klastrów: standardowa
* Typ klastra: baza danych HBase
* Wersja klastra: 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>Instalowanie CDAP opublikowanej aplikacji

Aby uzyskać instrukcje krok po kroku dotyczące instalowania tego i innych dostępnych aplikacji niezależnych dostawców oprogramowania, przeczytaj [instalowanie aplikacji platformy Hadoop innych firm](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Uruchom CDAP

1. Po zakończeniu instalacji uruchom CDAP z klastrem w witrynie Azure portal, przechodząc do **ustawienia** okienka, a następnie wybierając pozycję **aplikacje** w obszarze **ogólne** kategorii. W okienku zainstalowane aplikacje zawiera listę wszystkich zainstalowanych aplikacji.

    ![CDAP zainstalowanych aplikacji](./media/hdinsight-apps-install-cask/cdap-app.png)

2. Po wybraniu CDAP, zostanie wyświetlony link do strony sieci web i punkt końcowy HTTP, a także ścieżkę punktu końcowego protokołu SSH. Wybierz łącze strony sieci Web.

3. Po wyświetleniu monitu wprowadź swoje poświadczenia administratora klastra.

    ![Authentication](./media/hdinsight-apps-install-cask/auth.png)

4. Po zalogowaniu zostanie wyświetlona strona główna Cask CDAP graficznego interfejsu użytkownika.

    ![Strona główna cask graficznego interfejsu użytkownika](./media/hdinsight-apps-install-cask/gui.png)

5. Aby poznać interfejs CDAP, kliknij przycisk **rynku Cask** łącze menu u góry strony.

    ![Cask rynku łącza](./media/hdinsight-apps-install-cask/cask-market.png)

6. Wybierz **przykładowy dziennik dostępu** z listy.

    ![Przykładowy dziennik dostępu](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Kliknij przycisk **obciążenia** o potwierdzenie.

    ![Kliknij opcję Załaduj](./media/hdinsight-apps-install-cask/market-load.png)

8. Wyświetlany jest widok uwzględnione przykładowych danych. Wybierz **dalej**.

    ![Przykładowy dziennik dostępu — wyświetlanie danych](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Wybierz **Stream** jako typ docelowy, wprowadź nazwę docelowego, a następnie wybierz **Zakończ**.

    ![Przykładowy dziennik dostępu — wybierz miejsce docelowe](./media/hdinsight-apps-install-cask/market-destination.png)

10. Po pomyślnym załadowaniu datapack wybierz **Wyświetl szczegóły Stream**.

    ![Pomyślnie przekazano Datapack](./media/hdinsight-apps-install-cask/market-view-details.png)

11. Aby włączyć metadanych dla przestrzeni nazw, zaznacz **Włącz** na karcie użycie na stronie szczegółów dziennik dostępu.

    ![Przykładowy dziennik dostępu — załadowano — Włączanie metadanych](./media/hdinsight-apps-install-cask/log-loaded.png)

12. Po włączeniu metadanych zobaczysz wykres przedstawiający informacje o inspekcji wiadomości.

    ![Przykładowy dziennik dostępu — metadanych włączone](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Aby eksplorować dane dziennika, wybierz **Eksploruj** ikonę na górze strony.

    ![Dostęp do dziennika przykładowy — zapoznaj się z](./media/hdinsight-apps-install-cask/log-explore.png)

14. Zostanie wyświetlony przykładowego zapytania SQL. Swobodnie modyfikować go w formie żądanego, a następnie wybierz opcję **Execute**.

    ![Przykładowy dziennik dostępu — zapoznaj się z zestawu danych za pomocą zapytania](./media/hdinsight-apps-install-cask/log-query.png)

15. Po zakończeniu zapytania wybierz **widoku** ikonę w kolumnie akcje.

    ![Przykładowy dziennik dostępu — Wyświetl ukończone zapytanie](./media/hdinsight-apps-install-cask/log-query-view.png)

16. Zostaną wyświetlone wyniki zapytania.

    ![Przykładowy dziennik dostępu — wyniki zapytania](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja cask](http://cask.co/resources/documentation/).
* [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć nieopublikowane aplikację HDInsight w usłudze HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): Dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Używanie pustych węzłów brzegowych w HDInsight](hdinsight-apps-use-edge-node.md): Dowiedz się, jak użyć pustego węzła krawędzi do uzyskiwania dostępu do klastrów HDInsight, a także do testowania i obsługi aplikacji HDInsight.
