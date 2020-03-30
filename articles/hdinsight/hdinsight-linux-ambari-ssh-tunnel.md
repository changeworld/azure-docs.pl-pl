---
title: Dostęp do usługi Azure HDInsight za pomocą tunelowania SSH
description: Dowiedz się, jak bezpiecznie przeglądać zasoby internetowe hostowane w węzłach HDInsight opartych na systemie Linux za pomocą tunelu SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 6f4efd9a316b92f17f89cea66a7c81e84ac3cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72991350"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Użyj tunelowania SSH, aby uzyskać dostęp do interfejsu użytkownika sieci Web Apache Ambari, JobHistory, NameNode, Apache Oozie i innych interfejsów użytkownika

Klastry HDInsight zapewniają dostęp do interfejsu użytkownika sieci Web Apache Ambari przez Internet, ale niektóre funkcje wymagają tunelu SSH. Na przykład interfejs użytkownika sieci Web dla usługi Apache Oozie nie jest dostępny przez Internet bez tunelu SSh.

## <a name="why-use-an-ssh-tunnel"></a>Dlaczego warto skorzystać z tunelu SSH

Niektóre menu w Ambari działają tylko przez tunel SSH. Te menu są zależne od witryn sieci web i usług uruchomionych w innych typach węzłów, takich jak węzły procesu roboczego.

Następujące interfejsy użytkownika sieci Web wymagają tunelu SSH:

* JobHistory (Historia zadania)
* NazwaNod
* Stosy wątków
* Interfejs użytkownika sieci Oozie
* Interfejs użytkownika i interfejs użytkownika wzorca bazy danych i dzienników

Jeśli używasz akcji skryptu, aby dostosować klaster, wszystkie usługi lub narzędzia, które można zainstalować, które udostępniają usługę sieci web wymagają tunelu SSH. Na przykład w przypadku instalacji hue przy użyciu akcji skryptu, należy użyć tunelu SSH, aby uzyskać dostęp do interfejsu użytkownika sieciowego Hue.

> [!IMPORTANT]  
> Jeśli masz bezpośredni dostęp do usługi HDInsight za pośrednictwem sieci wirtualnej, nie trzeba używać tuneli SSH. Na przykład bezpośredniego dostępu do usługi HDInsight za pośrednictwem sieci wirtualnej zobacz [Connect HDInsight do lokalnego dokumentu sieciowego.](connect-on-premises-network.md)

## <a name="what-is-an-ssh-tunnel"></a>Co to jest tunel SSH

[Tunelowanie secure shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) łączy port na komputerze lokalnym z węzłem głównym w programie HDInsight. Ruch wysyłany do portu lokalnego jest kierowany przez połączenie SSH do węzła głównego. Żądanie jest rozpoznawane tak, jakby pochodziło z węzła głównego. Odpowiedź jest następnie kierowana z powrotem przez tunel do stacji roboczej.

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Przeglądarka internetowa, która może być skonfigurowana do korzystania z serwera proxy SOCKS5.

    > [!WARNING]  
    > Obsługa serwera proxy SOCKS wbudowana w ustawienia internetowe systemu Windows nie obsługuje socks5 i nie działa z krokami w tym dokumencie. Następujące przeglądarki opierają się na ustawieniach serwera proxy systemu Windows i obecnie nie działają z krokami w tym dokumencie:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome opiera się również na ustawieniach serwera proxy systemu Windows. Można jednak zainstalować rozszerzenia obsługujące SOCKS5. Polecamy [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Tworzenie tunelu za pomocą polecenia SSH

Użyj następującego polecenia, aby utworzyć `ssh` tunel SSH za pomocą polecenia. Zamień `sshuser` na użytkownika SSH dla klastra `CLUSTERNAME` HDInsight i zastąp nazwą klastra HDInsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

To polecenie tworzy połączenie, które kieruje ruch do portu lokalnego 9876 do klastra za pomocą SSH. Dostępne opcje to:

* **D 9876** - Lokalny port, który kieruje ruch przez tunel.
* **C** - Kompresuj wszystkie dane, ponieważ ruch internetowy jest głównie tekstem.
* **2** - Wymuś SSH tylko w wersji 2.
* **q** - Tryb cichy.
* **T** - Wyłącz alokację pseudo-tty, ponieważ po prostu przekazujesz port.
* **n** - Zapobiegaj odczytowi STDIN, ponieważ po prostu przekazujesz port.
* **N** - Nie należy wykonywać polecenia zdalnego, ponieważ po prostu przekazujesz port.
* **f** - Uruchom w tle.

Po zakończeniu polecenia ruch wysyłany do portu 9876 na komputerze lokalnym jest kierowany do węzła głównego klastra.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Tworzenie tunelu przy użyciu putty

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) jest graficznym klientem SSH dla systemu Windows. Jeśli nie znasz putty, zobacz [dokumentację PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Aby utworzyć tunel SSH przy użyciu putty, należy wykonać następujące czynności:

### <a name="create-or-load-a-session"></a>Tworzenie lub ładowanie sesji

1. Otwórz PuTTY i upewnij **się, że sesja** jest zaznaczona w lewym menu. Jeśli sesja została już zapisana, wybierz ją z listy **Zapisane sesje** i wybierz pozycję **Wczytaj**.

1. Jeśli nie masz jeszcze zapisanej sesji, wprowadź informacje o połączeniu:

    |Właściwość |Wartość |
    |---|---|
    |Nazwa hosta (lub adres IP)|Adres SSH dla klastra HDInsight. Na przykład **mójklaster-ssh.azurehdinsight.net**.|
    |Port|22|
    |Typ połączenia|Protokół SSH|

1. Wybierz pozycję **Zapisz**.

    ![HDInsight tworzenie sesji kitu](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. W sekcji **Kategoria** po lewej stronie okna dialogowego rozwiń węzeł **Połączenie,** rozwiń węzeł **SSH**, a następnie wybierz pozycję **Tunele**.

1. Podaj następujące informacje w formularzu **Opcje sterujące przekazywaniem portów SSH:**

    |Właściwość |Wartość |
    |---|---|
    |Port źródłowy|Port na kliencie, który chcesz przekazać dalej. Na przykład **9876**.|
    |Element docelowy|Adres SSH dla klastra HDInsight. Na przykład **mójklaster-ssh.azurehdinsight.net**.|
    |Dynamiczny|Umożliwia dynamiczne routing proxy SOCKS.|

    ![Opcje tunelowania konfiguracji PuTTY](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Wybierz **pozycję Dodaj,** aby dodać ustawienia, a następnie wybierz pozycję **Otwórz,** aby otworzyć połączenie SSH.

1. Po wyświetleniu monitu zaloguj się do serwera.

## <a name="use-the-tunnel-from-your-browser"></a>Korzystanie z tunelu w przeglądarce

> [!IMPORTANT]  
> Kroki opisane w tej sekcji korzystają z przeglądarki Mozilla FireFox, ponieważ zapewnia ona te same ustawienia serwera proxy na wszystkich platformach. Inne nowoczesne przeglądarki, takie jak Google Chrome, mogą wymagać rozszerzenia, takiego jak FoxyProxy, aby pracować z tunelem.

1. Skonfiguruj przeglądarkę tak, aby **używała localhost** i portu użytego podczas tworzenia tunelu jako serwera proxy **SOCKS v5.** Oto jak wyglądają ustawienia Firefoksa. Jeśli użyto innego portu niż 9876, zmień port na używany:

    ![ustawienia serwera proxy przeglądarki Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > Wybranie **opcji Zdalny dns** powoduje rozpoznawanie żądań systemu nazw domen (DNS) przy użyciu klastra HDInsight. To ustawienie powoduje, że usługa DNS jest rozpoznawana przy użyciu węzła głównego klastra.

2. Sprawdź, czy tunel działa, odwiedzając [https://www.whatismyip.com/](https://www.whatismyip.com/)witrynę, taką jak . Zwracany adres IP powinien być używany przez centrum danych platformy Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Sprawdź za pomocą interfejsu użytkownika sieci Web Ambari

Po ustanowieniu klastra należy wykonać następujące czynności, aby sprawdzić, czy można uzyskać dostęp do interfejsów api sieci web usługi z sieci Web Ambari:

1. W przeglądarce przejdź do adresu `http://headnodehost:8080`. Adres `headnodehost` jest wysyłany przez tunel do klastra i rozpoznać do węzła głównego, na który działa Ambari. Po wyświetleniu monitu wprowadź nazwę użytkownika (administratora) administratora i hasło do klastra. Interfejs użytkownika sieci Web Ambari może zostać wyświetlony po raz drugi. Jeśli tak, ponownie wejdź do informacji.

   > [!NOTE]  
   > Podczas korzystania `http://headnodehost:8080` z adresu do łączenia się z klastrem, łączysz się przez tunel. Komunikacja jest zabezpieczona za pomocą tunelu SSH zamiast HTTPS. Aby połączyć się przez Internet `https://clustername.azurehdinsight.net`za `clustername` pomocą protokołu HTTPS, użyj , gdzie jest nazwa klastra.

2. W interfejsie użytkownika sieci Web Ambari wybierz hdfs z listy po lewej stronie.

    ![Apache Ambari hdfs usługa wybrana](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Po wyświetleniu informacji o usłudze HDFS wybierz pozycję **Szybkie łącza**. Pojawi się lista węzłów głównego klastra. Wybierz jeden z węzłów głównego, a następnie wybierz **pozycję NameNode UI**.

    ![Obraz z rozszerzonym menu QuickLinks](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Po wybraniu __opcji Szybkie łącza__może pojawić się wskaźnik oczekiwania. Ten warunek może wystąpić, jeśli masz wolne połączenie z Internetem. Zaczekaj minutę lub dwie, aby dane zostały odebrane z serwera, a następnie spróbuj ponownie wyświetlić listę.
    >
    > Niektóre wpisy w menu **Szybkie łącza** mogą być obcięte po prawej stronie ekranu. Jeśli tak, rozwiń menu za pomocą myszy i użyj klawisza strzałki w prawo, aby przewinąć ekran w prawo, aby zobaczyć resztę menu.

4. Zostanie wyświetlona strona podobna do następującej obrazu:

    ![Obraz interfejsu użytkownika Hadoop NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Zwróć uwagę na adres URL tej strony; powinna być podobna do `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Ten identyfikator URI używa wewnętrznej w pełni kwalifikowanej nazwy domeny (FQDN) węzła i jest dostępny tylko w przypadku korzystania z tunelu SSH.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak tworzyć i używać tunelu SSH, zobacz następujący dokument, aby uzyskać inne sposoby używania Ambari:

* [Zarządzanie klastrami hdinsight za pomocą Apache Ambari](hdinsight-hadoop-manage-ambari.md)
