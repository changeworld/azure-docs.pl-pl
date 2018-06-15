---
title: Wdrażanie i zarządzanie nimi topologii Apache Storm w usłudze HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażanie, monitorowanie i zarządzanie topologiami Apache Storm na HDInsight przy użyciu pulpitu nawigacyjnego Storm. Narzędzia usługi Hadoop dla programu Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5e542072-f014-42aa-82d6-2694a76df520
ms.service: hdinsight
ms.devlang: java
ms.topic: conceptual
ms.date: 03/01/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 55c92e6408522b8a96a37dbedd99d929af1e49fb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414987"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Wdrażanie i zarządzanie topologiami Apache Storm na HDInsight opartych na systemie Windows

Pulpit nawigacyjny Storm umożliwia łatwe wdrażanie i uruchamianie topologii Apache Storm do klastra usługi HDInsight przy użyciu przeglądarki sieci web. Można również pulpit nawigacyjny do monitorowania i zarządzania uruchomionymi topologiami. Jeśli używasz programu Visual Studio, narzędzia HDInsight Tools for Visual Studio zapewniają podobne funkcje w programie Visual Studio.

Pulpit nawigacyjny Storm i funkcje systemu Storm w narzędziach HDInsight korzystają z interfejsu API REST Storm, która może służyć do tworzenia własnych monitorowania i rozwiązań do zarządzania.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają Storm w klastrze usługi HDInsight, z systemem Windows jako systemu operacyjnego. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).
>
> Aby uzyskać informacje dotyczące wdrażania i zarządzania topologii Storm z klastrem usługi HDInsight, który używa systemu Linux, zobacz [wdrażanie i zarządzanie topologiami Apache Storm na HDInsight opartych na systemie Linux](apache-storm-deploy-monitor-topology-linux.md)

## <a name="prerequisites"></a>Wymagania wstępne

* **Apache Storm w usłudze HDInsight** — zobacz [wprowadzenie do Apache Storm w usłudze HDInsight](apache-storm-tutorial-get-started-linux.md) instrukcje dotyczące tworzenia klastra.

* Aby uzyskać **pulpitu nawigacyjnego Storm**: przeglądarki nowoczesnych witryn sieci web, która obsługuje protokół HTML5.

* Dla **programu Visual Studio** -zestawu Azure SDK 2.5.1 lub nowszej i narzędzi HDInsight Tools for Visual Studio. Zobacz [rozpocząć korzystanie z narzędzia HDInsight Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) do instalowania i konfigurowania narzędzi HDInsight tools for Visual Studio.

    Jedna z następujących wersji programu Visual Studio:

  * Program Visual Studio 2012 z aktualizacją 4

  * Visual Studio 2013 z aktualizacją 4 lub Visual Studio 2013 Community

  * Visual Studio 2015 (dowolna wersja)

  * Visual Studio 2017 (dowolna wersja)

## <a name="storm-dashboard"></a>Pulpit nawigacyjny STORM

Pulpit nawigacyjny Storm jest dostępne na klaster Storm strony sieci web. Adres URL jest **https://&lt;clustername >.azurehdinsight.net/**, gdzie **clustername** jest nazwą Storm w klastrze usługi HDInsight.

W górnej części pulpitu nawigacyjnego Storm, wybierz **przesyłania topologii**. Postępuj zgodnie z instrukcjami na stronie Uruchom przykładowa topologia lub aby przekazać i uruchom topologii, który został utworzony.

![na stronie topologia przesyłania][storm-dashboard-submit]

### <a name="storm-ui"></a>STORM interfejsu użytkownika

Wybierz z pulpitu nawigacyjnego Storm **interfejsu użytkownika platformy Storm** łącza. Zostaną wyświetlone informacje o klastrze, oprócz żadnych uruchomionych topologii.

![Interfejs użytkownika platformy storm][storm-dashboard-ui]

> [!NOTE]
> W niektórych wersjach programu Internet Explorer może stwierdzić, interfejsu użytkownika platformy Storm nie powoduje odświeżenia po raz pierwszy odwiedzeniu go. Na przykład może nie pokazywać nowe topologie przesłane lub mogą być wyświetlane topologii jako aktywny, gdy wcześniej dezaktywowana. Firma Microsoft zna tego problemu i pracuje nad rozwiązaniem.

#### <a name="main-page"></a>Strona główna

Strona główna interfejsu użytkownika Storm udostępnia następujące informacje:

* **Podsumowanie klastra**: podstawowe informacje dotyczące klastra Storm.

* **Topologia podsumowania**: Lista uruchomionych topologii. Użyj linków w tej sekcji, aby wyświetlić więcej informacji na temat określonych topologii.

* **Podsumowanie przełożonego**: informacje o przełożonego Storm.

* **Konfiguracja nimbus**: Nimbus konfiguracji klastra.

#### <a name="topology-summary"></a>Topologia podsumowania

Kliknąć łącze z **podsumowanie topologii** sekcja zawiera następujące informacje o topologii:

* **Topologia podsumowania**: podstawowe informacje o topologii.

* **Akcje topologii**: Akcje zarządzania, które można wykonywać w topologii.

  * **Aktywuj**: wznowienie przetwarzania dezaktywowanej topologii.

  * **Dezaktywuj**: wstrzymanie uruchomionej topologii.

  * **Rebalance**: dopasowanie równoległości topologii. Po zmianie liczby węzłów w klastrze należy przeprowadzić ponowne równoważenie uruchomionych topologii. Dzięki temu topologii dostosować równoległość kompensacji zwiększenia lub zmniejszenia liczby węzłów w klastrze.

      Aby uzyskać więcej informacji, zobacz [pojęcie równoległości w topologii Storm (http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

  * **Kasowanie**: kończy topologii Storm po określonym czasie.

* **Statystyka topologii**: Statystyka topologii. Użyj linków w **okna** kolumnę, aby ustawić zakres czasu dla pozostałe wpisy na stronie.

* **Spouts**: elementach spout, używany przez topologii. Użyj łącza w tej sekcji, aby wyświetlić więcej informacji o elementach spout określonych.

* **Bolts**: elementów bolt, używany przez topologii. Użyj łącza w tej sekcji, aby wyświetlić więcej informacji na temat określonych elementów bolt.

* **Topologia konfiguracji**: konfiguracji wybranego topologii.

#### <a name="spout-and-bolt-summary"></a>Spout i Bolt — podsumowanie

Wybieranie spout z **Spouts** lub **Bolts** sekcje zawiera następujące informacje dotyczące wybranej pozycji:

* **Składnik podsumowania**: podstawowe informacje o spout lub bolt.

* **Spout/Bolt stats**: Statystyka spout lub bolt. Użyj linków w **okna** kolumnę, aby ustawić zakres czasu dla pozostałe wpisy na stronie.

* **Wprowadź statystykę** (tylko dla elementów bolt): informacje o strumienie wejściowe, używane przez elementy bolt.

* **OUTPUT stats**: informacje o strumieni emitowane przez to spout ani dla elementów bolt.

* **Modułów**: informacje na temat wystąpień spout lub bolt. Wybierz **portu** utworzony wpis dla określonego modułu wykonującego wyświetlić dziennik informacje diagnostyczne dla tego wystąpienia.

* **Błędy**: informacje o błędzie dla tego spout lub elementów bolt.

## <a name="hdinsight-tools-for-visual-studio"></a>HDInsight Tools for Visual Studio

Narzędzia HDInsight Tools może służyć do przesyłania C# i hybrydowych topologii do klastra Storm. Poniższe kroki użyć przykładowej aplikacji. Aby uzyskać informacji o tworzeniu własnych topologii za pomocą narzędzi HDInsight, zobacz [topologii opracowywania C# za pomocą narzędzi HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Wykonaj następujące kroki, aby wdrożyć przykładowe Storm w klastrze usługi HDInsight, a następnie Wyświetl i zarządzaj nimi topologii.

1. Jeśli nie został jeszcze zainstalowany najnowszej wersji narzędzia HDInsight Tools for Visual Studio, zobacz [rozpocząć korzystanie z narzędzia HDInsight Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Otwórz program Visual Studio, wybierz **pliku** > **nowy** > **projektu**.

3. W **nowy projekt** okna dialogowego rozwiń **zainstalowana** > **szablony**, a następnie wybierz **HDInsight**. Wybierz z listy szablonów **próbki Storm**. W dolnej części okna dialogowego wpisz nazwę aplikacji.

    ![Obraz](./media/apache-storm-deploy-monitor-topology/sample.png)

4. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **przesyłania do systemu Storm w usłudze HDInsight**.

   > [!NOTE]
   > Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla subskrypcji platformy Azure. Jeśli masz więcej niż jedną subskrypcję, zaloguj się za jedną, która zawiera Storm w klastrze usługi HDInsight.

5. Wybierz Storm w klastrze usługi HDInsight z **klaster Storm** listy rozwijanej, a następnie wybierz **przesyłania**. Można monitorować, czy przekazywania działa prawidłowo, używając **dane wyjściowe** okna.

6. Gdy topologia zostało pomyślnie przesłane, **topologii Storm** dla klastra powinna zostać wyświetlona. Wybierz topologię z listy, aby wyświetlić informacje o uruchomionej topologii.

    ![monitor programu Visual studio](./media/apache-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > Można również wyświetlić **topologii Storm** z **Eksploratora serwera** rozwijając **Azure** > **HDInsight**, a następnie prawym przyciskiem myszy klaster Storm w usłudze HDInsight i wybierając **topologii Storm widoku**.

    Wybierz kształt na elementach spout lub elementów bolt, aby wyświetlić informacje dotyczące tych składników. Otwiera nowe okno dla każdego wybranego elementu.

   > [!NOTE]
   > Nazwa topologii jest nazwą klasy topologii (w tym przypadku `HelloWord`,) z sygnaturą czasową dołączane.

7. Z **podsumowanie topologii** widok, wybierz opcję **Kill** przestanie topologii.

   > [!NOTE]
   > Topologii STORM nadal działać do chwili zostały zatrzymane lub usunąć klastra.


## <a name="rest-api"></a>Interfejs API REST

Interfejsu użytkownika platformy Storm jest oparty na interfejsie API REST, dlatego można wykonać podobne do zarządzania i monitorowania funkcji za pomocą interfejsu API REST. Można użyć interfejsu API REST do tworzenia niestandardowych narzędzi do zarządzania i monitorowania topologii Storm.

Aby uzyskać więcej informacji, zobacz [interfejsu API REST interfejsu użytkownika Storm](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). Następujące informacje są specyficzne dla z systemu Apache Storm w usłudze HDInsight przy użyciu interfejsu API REST.

### <a name="base-uri"></a>Podstawowy identyfikator URI

Podstawowy identyfikator URI dla interfejsu API REST w klastrach HDInsight jest **https://&lt;clustername >.azurehdinsight.net/stormui/api/v1/**, gdzie **clustername** jest nazwą Storm w usłudze HDInsight klaster.

### <a name="authentication"></a>Authentication

Żądania do interfejsu API REST muszą używać **uwierzytelnianie podstawowe**, aby użyć nazwy administratora klastra usługi HDInsight i hasło.

> [!NOTE]
> Ponieważ uwierzytelnianie podstawowe jest wysyłany przy użyciu zwykłego tekstu, należy **zawsze** zabezpieczenia komunikacji z klastrem przy użyciu protokołu HTTPS.

### <a name="return-values"></a>Wartości zwracane

Można używać z wewnątrz klastra lub maszyn wirtualnych w tej samej sieci wirtualnej Azure, co klaster może być tylko informacje zwrócone z interfejsu API REST. Na przykład w pełni kwalifikowaną nazwę (FQDN) zwróciła dozorcy serwery są nie być dostępny z Internetu.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy znasz już sposobu wdrażania i monitorowania topologii za pomocą pulpitu nawigacyjnego Storm, Dowiedz się, jak:

* [Opracowywanie topologii C# za pomocą narzędzi HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

* [Opracowywanie topologii opartych na języku Java za pomocą programu Maven](apache-storm-develop-java-topology.md)

Aby uzyskać listę więcej przykładowe topologie, zobacz [przykładowe topologie dla systemu Storm w usłudze HDInsight](apache-storm-example-topology.md).

[hdinsight-dashboard]: ./media/apache-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/apache-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/apache-storm-deploy-monitor-topology/storm-ui-summary.png
