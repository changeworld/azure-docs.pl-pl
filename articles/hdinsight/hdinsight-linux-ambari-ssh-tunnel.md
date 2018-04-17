---
title: Używanie protokołu SSH tunneling można uzyskać dostępu do usługi Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak bezpiecznie przeglądać zasobów sieci web znajdujących się na węzły HDInsight opartych na systemie Linux za pomocą tunelu SSH.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: larryfr
ms.openlocfilehash: 05e06d6ed8c2a3bec0d12f81aae6f7022a56b942
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Użyj tunelowania SSH, aby uzyskać dostęp do interfejsu użytkownika sieci web Ambari, JobHistory, NameNode, Oozie i innych sieci web UI

Klastry usługi HDInsight opartej na systemie Linux zapewniają dostęp do interfejsu użytkownika sieci web Ambari w Internecie, ale nie są niektóre funkcje interfejsu użytkownika. Na przykład interfejsu użytkownika sieci web dla innych usług, które są udostępniane za pośrednictwem narzędzia Ambari. Aby uzyskać pełną funkcjonalność interfejsu użytkownika sieci web Ambari należy użyć tunelu SSH nagłówek klastra.

## <a name="why-use-an-ssh-tunnel"></a>Dlaczego warto korzystać z tunelowania SSH

Tylko kilka Ambari menu pracy za pośrednictwem tunelu SSH. Tych menu korzystają z witryny sieci web i usług działających na inne typy węzłów, takich jak węzłów procesu roboczego. Często tych witryn sieci web nie są zabezpieczone, więc nie można bezpiecznie udostępnić je bezpośrednio w Internecie.

Następujące UI sieci Web wymagają tunelu SSH:

* JobHistory
* NameNode
* Stosy wątków
* Oozie interfejsu użytkownika sieci web
* Główna baza danych HBase i dzienniki interfejsu użytkownika

Jeśli akcji skryptu można użyć do dostosowania z klastrem, usługi lub narzędzi, które można zainstalować z użyciem interfejsu użytkownika sieci web wymagają tunelu SSH. Na przykład po zainstalowaniu aplikacji Hue za pomocą akcji skryptu, można użyć tunelu SSH dostępu do sieci web aplikacji Hue interfejsu użytkownika.

> [!IMPORTANT]
> Jeśli masz bezpośredni dostęp do usługi HDInsight za pośrednictwem sieci wirtualnej jest konieczne używanie tunelu SSH. Przykład bezpośredni dostęp do usługi HDInsight za pośrednictwem sieci wirtualnej, zobacz [HDInsight połączyć się z siecią lokalną](connect-on-premises-network.md) dokumentu.

## <a name="what-is-an-ssh-tunnel"></a>Co to jest tunelu SSH

[Secure Shell (SSH) tunelowania](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) kieruje ruch wysyłany do znajdującego się na lokalnej stacji roboczej. Ruch jest kierowany przez połączenie SSH do węzła głównego klastra usługi HDInsight. Żądanie zostanie rozwiązany, tak jakby jego występowania w węźle głównym. Odpowiedź jest następnie kierowane wstecz przez tunel do stacji roboczej.

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Większość systemów operacyjnych Podaj klienta SSH za pośrednictwem `ssh` polecenia. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Przeglądarka sieci web, które mogą być skonfigurowane do korzystania z serwera proxy SOCKS5.

    > [!WARNING]
    > Obsługa serwera proxy SOCKS wbudowanych w system Windows nie obsługuje SOCKS5 i nie działa z kroki opisane w tym dokumencie. Następujące przeglądarki zależą od ustawień serwera proxy systemu Windows i obecnie nie współpracujesz z kroki opisane w tym dokumencie:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome również korzysta z ustawień serwera proxy systemu Windows. Można jednak zainstalować rozszerzenia, które obsługują SOCKS5. Firma Microsoft zaleca [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Tworzenie tunelu przy użyciu polecenia SSH

Użyj tunelowania następujące polecenie, aby utworzyć SSH za pomocą `ssh` polecenia. Zastąp **USERNAME** użytkownika SSH dla klastra usługi HDInsight i Zastąp **CLUSTERNAME** o nazwie z klastrem usługi HDInsight:

```bash
ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
```

To polecenie tworzy połączenie kieruje ruchem do portu lokalnego 9876 do klastra za pomocą protokołu SSH. Dostępne opcje to:

* **D 9876** — port lokalny, który przekierowuje ruch za pośrednictwem tunelu.
* **C** -Kompresuj wszystkich danych, ponieważ ruchu w sieci web jest przeważnie tekstu.
* **2** -force SSH próby tylko w wersji 2 protokołu.
* **q** — tryb cichy.
* **T** — Wyłącz pseudo-tty alokacji, ponieważ właśnie przesyłasz portu.
* **n** -zapobiec odczytu STDIN, ponieważ właśnie przesyłasz portu.
* **N** -nie wykonuj polecenia zdalnego, ponieważ właśnie przesyłasz portu.
* **f** -uruchomione w tle.

Po zakończeniu działania polecenia ruch wysyłany do portu 9876 na komputerze lokalnym jest przekierowywane do węzła głównego klastra.

## <a name="useputty"></a>Tworzenie tunelu przy użyciu programu PuTTY

[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty) graficznego klienta SSH dla systemu Windows. Poniższe kroki umożliwiają utworzenie tunelu SSH przy użyciu programu PuTTY:

1. Otwórz program PuTTY i wprowadzić informacje o połączeniu. Jeśli nie masz doświadczenia w obsłudze programu PuTTY, zobacz [programu PuTTY dokumentacji (http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html).

2. W **kategorii** z lewej strony okna dialogowego, rozwiń **połączenia**, rozwiń węzeł **SSH**, a następnie wybierz **tuneli**.

3. Podaj następujące informacje w **przekierowanie portów opcje kontrolujące SSH** formularza:
   
   * **Source port** (Port źródłowy) — port na komputerze klienckim, który ma być przekierowany. Na przykład **9876**.

   * **Docelowy** — adres SSH dla klastra usługi HDInsight opartej na systemie Linux. Na przykład **mójklaster-ssh.azurehdinsight.net**.

   * **Dynamic** (Dynamiczny) — umożliwia dynamiczny routing serwera proxy SOCKS.
     
     ![Obraz tunelowania opcje](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Kliknij przycisk **Dodaj** dodać ustawienia, a następnie kliknij przycisk **Otwórz** można otworzyć połączenia SSH.

5. Po wyświetleniu monitu zaloguj się do serwera.

## <a name="use-the-tunnel-from-your-browser"></a>Użyj tunelu z przeglądarki

> [!IMPORTANT]
> Kroki opisane w tej sekcji Korzystanie z przeglądarki Mozilla FireFox, ponieważ zapewnia te same ustawienia serwera proxy na wszystkich platformach. Inne nowoczesne przeglądarki, takich jak Google Chrome, mogą wymagać rozszerzenia, takie jak FoxyProxy do pracy z tunelu.

1. Skonfiguruj przeglądarkę, aby użyć **localhost** i port używany podczas tworzenia tunelu jako **SOCKS v5** serwera proxy. Poniżej przedstawiono wygląd ustawienia przeglądarki Firefox. Jeśli użyto innego portu niż 9876 zmienić port ten, który został użyty:
   
    ![Obraz ustawień przeglądarki Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > Wybieranie **DNS zdalnego** rozpoznaje żądania systemu nazw domen (DNS, Domain Name System) przy użyciu klastra usługi HDInsight. To ustawienie jest rozpoznawany jako DNS za pomocą węzła głównego klastra.

2. Sprawdź, czy tunelu działa odwiedzając witrynę, takich jak [ http://www.whatismyip.com/ ](http://www.whatismyip.com/). Adres IP zwrócony powinien być używany przez centrum danych Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Weryfikacja Ambari web UI

Po ustanowieniu klastra, wykonaj następujące kroki, aby sprawdzić, czy są dostępne usługi sieci web UI sieci Ambari Web:

1. W przeglądarce przejdź do http://headnodehost:8080. `headnodehost` Adres są wysyłane za pośrednictwem tunelu do klastra i Rozwiąż, aby headnode, uruchomionym Ambari. Po wyświetleniu monitu wprowadź nazwę użytkownika admin (Administrator) i hasło dla klastra. Może pojawić się prośba po raz drugi przez interfejs użytkownika sieci web Ambari. Jeśli tak, należy ponownie wprowadzić informacje.

   > [!NOTE]
   > Korzystając z http://headnodehost:8080 adresów, aby połączyć się z klastrem, jest nawiązywane za pośrednictwem tunelu. Komunikacja jest zabezpieczone przy użyciu tunelu SSH, a nie protokołu HTTPS. Aby połączyć się przez internet przy użyciu protokołu HTTPS, należy użyć https://CLUSTERNAME.azurehdinsight.net, gdzie **CLUSTERNAME** jest nazwą klastra.

2. Z poziomu interfejsu użytkownika sieci Web Ambari wybierz system plików HDFS z listy po lewej stronie.

    ![Obraz o wybrany system plików HDFS](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Podczas wyświetlania informacji usług systemu plików HDFS wybierz **szybkie linki**. Zostanie wyświetlona lista głównymi węzłami klastra. Wybierz jeden z węzłów głównych, a następnie wybierz **interfejsu użytkownika NameNode**.

    ![Obraz menu QuickLinks rozwinięty](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]
   > Po wybraniu __szybkie linki__, mogą wystąpić wskaźnik oczekiwania. Ten stan może wystąpić, jeśli masz wolne połączenie internetowe. Zaczekaj minutę lub dwie danych z serwera, a następnie spróbuj ponownie listy.
   >
   > Niektóre wpisy w **szybkie linki** menu mogą obcięty z prawej strony ekranu. Jeśli tak, rozwiń menu za pomocą myszy, a następnie użyj klawisza Strzałka w prawo, aby przewiń ekran w prawo, aby zobaczyć pozostałą część menu.

4. Wyświetlane są stronę podobną do poniższej ilustracji:

    ![Obraz NameNode interfejsu użytkownika](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]
   > Zwróć uwagę, adres URL dla tej strony; powinna być podobna do **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Ten identyfikator URI węzła przy użyciu wewnętrznego pełną nazwę domeny (FQDN) i jest dostępny tylko przy użyciu tunelu SSH.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu tworzenia i używania tunelu SSH, można znaleźć w dokumencie następujące inne sposoby używania narzędzia Ambari:

* [Zarządzanie klastrami usługi HDInsight przy użyciu Ambari](hdinsight-hadoop-manage-ambari.md)

Aby uzyskać więcej informacji o korzystaniu z protokołu SSH z usługą HDInsight, zobacz [używanie SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

