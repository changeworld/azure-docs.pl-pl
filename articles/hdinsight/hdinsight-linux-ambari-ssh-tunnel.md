---
title: Używanie protokołu SSH tunelowania do dostępu do usługi Azure HDInsight
description: Dowiedz się, jak bezpiecznie przeglądać zasoby sieci web hostowanych w węzłach usługi HDInsight opartych na systemie Linux za pomocą tunelu SSH.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
origin.date: 04/30/2018
ms.date: 02/04/2019
ms.author: hrasheed
ms.openlocfilehash: 0361539cefbacb8fc0473a1f863cf2ae4638b444
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766761"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-web-uis"></a>Korzystanie z tunelowania SSH do uzyskania dostępu do systemu Apache Ambari web UI, JobHistory, NameNode, Apache Oozie i innych web UI

Klastry HDInsight zapewniają dostęp do interfejsu użytkownika sieci web Apache Ambari za pośrednictwem Internetu, ale niektóre funkcje wymagają tunelu SSH. Na przykład interfejsu użytkownika sieci web dla usługi Apache Oozie nie są dostępne za pośrednictwem sieci internet bez tunelu SSh.

## <a name="why-use-an-ssh-tunnel"></a>Dlaczego warto korzystać z tunelu SSH

Tylko kilka kolejnych menu w Ambari działają za pośrednictwem tunelu SSH. Tych menu zależą od witryny sieci web i usług działających na inne typy węzłów, takich jak węzły procesu roboczego.

Następujących interfejsów użytkownika sieci Web wymagają tunelu SSH:

* JobHistory
* NameNode
* Stosy wątków
* Oozie web UI
* Interfejs użytkownika głównego interfejsu użytkownika HBase i dzienniki

W przypadku dostosowywania klastra przy użyciu akcji skryptu, żadnych usług ani programów narzędziowych, które zostały zainstalowane, które udostępniają usługi sieci web należy wymagać tunelu SSH. Na przykład po zainstalowaniu aplikacji Hue, za pomocą akcji skryptu, musi być tunelu SSH dostęp do interfejsu użytkownika sieci web aplikacji Hue.

> [!IMPORTANT]
> Jeśli masz bezpośredni dostęp do HDInsight za pośrednictwem sieci wirtualnej, jest konieczne używanie tunelu SSH. Aby uzyskać przykład bezpośredni dostęp do HDInsight za pośrednictwem sieci wirtualnej, zobacz [Connect HDInsight z siecią lokalną](connect-on-premises-network.md) dokumentu.

## <a name="what-is-an-ssh-tunnel"></a>Co to jest tunel SSH

[Secure Shell (SSH) tunelowania](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) łączy na port na komputerze lokalnym z węzłem głównym w HDInsight. Ruch wysyłany do lokalnego portu jest kierowany przez połączenie SSH z węzłem głównym. Żądanie zostanie rozwiązany, tak jakby pochodziły węzła głównego. Odpowiedź następnie odbywa się wstecz przez tunel do stacji roboczej.

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Większość systemów operacyjnych udostępnia klienta SSH za pośrednictwem `ssh` polecenia. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Przeglądarka sieci web, które mogą być skonfigurowane do korzystania z serwera proxy SOCKS5.

    > [!WARNING]
    > Obsługa serwera proxy SOCKS wbudowanych ustawień internetowych Windows nie obsługuje SOCKS5, a nie działa z krokami w tym dokumencie. Następujące przeglądarki zależą od ustawień serwera proxy Windows i obecnie nie współpracujesz z krokami w tym dokumencie:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome opiera się również na Windows ustawienia serwera proxy. Jednakże można zainstalować rozszerzenia, które obsługują SOCKS5. Firma Microsoft zaleca [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Tworzenia tunelu przy użyciu polecenia SSH

Użyj następujące polecenie, aby utworzyć SSH tunelu przy użyciu `ssh` polecenia. Zastąp **sshuser** użytkownikowi SSH dla klastra HDInsight i Zastąp **clustername** nazwą klastra usługi HDInsight:

```bash
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

To polecenie tworzy połączenie, które kieruje ruch do lokalnego portu 9876 do klastra za pośrednictwem protokołu SSH. Dostępne opcje to:

* **D 9876** — port lokalny, który kieruje ruch przez tunel.
* **C** -skompresować wszystkie dane ponieważ ruch internetowy jest przede wszystkim tekstu.
* **2** -force SSH do wypróbowania protokołu tylko w wersji 2.
* **q** — tryb cichy.
* **T** -Wyłącz Alokacja pseudo tty, ponieważ są po prostu przekazywanie portu.
* **n** — zapobieganie odczytu STDIN, ponieważ są po prostu przekazywanie portu.
* **N** -nie wykonuj polecenia zdalnego, ponieważ są po prostu przekazywanie portu.
* **f** -uruchomione w tle.

Po zakończeniu działania polecenia ruch wysyłany do portu 9876 na komputerze lokalnym jest kierowany do węzła głównego klastra.

## <a name="useputty"></a>Tworzenia tunelu przy użyciu programu PuTTY

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) to graficzny klient SSH dla Windows. Jeśli nie znasz programu PuTTY, zobacz [programu PuTTY dokumentacji](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Umożliwia tworzenie tunelu SSH przy użyciu programu PuTTY następujące czynności:

### <a name="create-or-load-a-session"></a>Utworzyć lub załadować sesji

1. Otwórz program PuTTY i upewnij się, **sesji** wybrano w menu po lewej stronie. Jeśli został już zapisany sesji, wybierz nazwę sesji, z **zapisane sesje** listy, a następnie kliknij przycisk **obciążenia**.

1. Jeśli nie masz już zapisaną sesję, wprowadź informacje o połączeniu:
    * **Nazwa hosta (lub adres IP)** -adres SSH dla klastra HDInsight. Na przykład **mójklaster ssh.azurehdinsight.net**
    * **Port** — 22
    * **Typ połączenia** — SSH
1. Kliknij pozycję **Zapisz**

    ![Tworzenie sesji SSH](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

2. W **kategorii** sekcji po lewej stronie okna dialogowego, rozwiń **połączenia**, rozwiń węzeł **SSH**, a następnie wybierz pozycję **tuneli**.

3. Podaj następujące informacje w **przekierowanie portów w opcji kontroli SSH** formularza:
   
   * **Source port** (Port źródłowy) — port na komputerze klienckim, który ma być przekierowany. Na przykład **9876**.

   * **Miejsce docelowe** -adres SSH dla klastra HDInsight. Na przykład **mójklaster-ssh.azurehdinsight.net**.

   * **Dynamic** (Dynamiczny) — umożliwia dynamiczny routing serwera proxy SOCKS.
     
     ![Obraz opcji tunelowania](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Kliknij przycisk **Dodaj** dodać ustawienia, a następnie kliknij przycisk **Otwórz** otworzyć połączenie SSH.

5. Po wyświetleniu monitu zaloguj się do serwera.

## <a name="use-the-tunnel-from-your-browser"></a>Korzystał z tunelu z przeglądarki

> [!IMPORTANT]
> Kroki opisane w tej sekcji Użyj przeglądarki Mozilla FireFox, ponieważ oferuje te same ustawienia serwera proxy na wszystkich platformach. Nowoczesnych przeglądarek, takich jak Google Chrome, mogą wymagać rozszerzeniem, na przykład FoxyProxy do pracy z tunelu.

1. Skonfiguruj przeglądarkę, aby użyć **localhost** i port używany podczas tworzenia tunelu jako **SOCKS v5** serwera proxy. Poniżej przedstawiono wygląd ustawień przeglądarki Firefox. Jeśli użyto innego portu niż 9876 zmiany portu, na który została użyta:
   
    ![Obraz ustawień przeglądarki Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > Wybieranie **DNS zdalnego** rozpoznaje żądań systemu nazw domen (DNS, Domain Name System) przy użyciu klastra HDInsight. To ustawienie jest rozpoznawany jako DNS przy użyciu węzła głównego klastra.

2. Sprawdź, czy tunelu działa, odwiedzając witrynę sieci, takich jak [ https://www.whatismyip.com/ ](https://www.whatismyip.com/). Adres IP zwrócony powinien być używany przez centrum danych Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Weryfikuj za pomocą interfejsu użytkownika sieci web systemu Ambari

Po ustanowieniu klastra, wykonaj następujące kroki, aby sprawdzić, czy są dostępne usługi sieci web UI sieci Ambari Web:

1. W przeglądarce przejdź do http\:/ / headnodehost:8080. `headnodehost` Adres są wysyłane za pośrednictwem tunelu do klastra i rozwiązania z węzłem głównym, z systemem Ambari. Po wyświetleniu monitu wprowadź nazwę użytkownika administratora (Administrator) i hasło dla klastra. Może pojawić się prośba drugi raz przez interfejs webowy Ambari. Jeśli tak, należy ponownie wprowadzić informacje.

   > [!NOTE]  
   > Przy użyciu protokołu http\://headnodehost:8080 adres, aby połączyć się z klastrem, jest nawiązywane za pośrednictwem tunelu. Komunikacja jest zabezpieczana za pomocą tunelu SSH, zamiast HTTPS. Aby połączyć się za pośrednictwem Internetu, przy użyciu protokołu HTTPS, używania protokołu https\:/ / clustername.azurehdinsight.net, gdzie **clustername** jest nazwą klastra.

2. Interfejs użytkownika sieci Web Ambari zaznacz systemu plików HDFS z listy po lewej stronie.

    ![Obraz z systemu plików HDFS wybrane](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Po wyświetleniu informacji o usłudze systemu plików HDFS wybierz **szybkich łączy**. Zostanie wyświetlona lista głównymi węzłami klastra. Wybierz jeden z węzłów głównych, a następnie wybierz **NameNode UI**.

    ![Obraz z rozwinięte menu spisach](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]
   > Po wybraniu __szybkich łączy__, możesz otrzymać wskaźnik oczekiwania. Ten stan może wystąpić w przypadku wolnego połączenia internetowego. Poczekaj minutę lub dwie danych odbierane z serwera, a następnie spróbuj ponownie listę.
   >
   > Niektóre wpisy w **szybkich łączy** menu może zostać przerwane przez po prawej stronie ekranu. Jeśli tak, rozwiń menu przy użyciu myszy, a następnie użyj klawisza Strzałka w prawo przewiń ekran w prawo, aby zobaczyć pozostałą część menu.

4. Zostanie wyświetlona strona podobna do poniższej ilustracji:

    ![Obraz przedstawiający NameNode interfejsu użytkownika](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]  
   > Zwróć uwagę, adres URL dla tej strony; powinny być podobne do **http\://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Ten identyfikator URI przy użyciu w wewnętrznej w pełni kwalifikowana nazwa domeny (FQDN) węzła i jest dostępna tylko podczas używania tunelu SSH.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy zawarto informacje dotyczące tworzenia i używania tunelu SSH, zobacz poniższy dokument na inne sposoby korzystania z systemu Ambari:

* [Zarządzanie klastrami HDInsight przy użyciu Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Aby uzyskać więcej informacji dotyczących korzystania z protokołu SSH z usługą HDInsight, zobacz [użycia protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

