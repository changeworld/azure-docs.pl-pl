---
title: Dowiedz się, przy użyciu technologii Apache Hadoop piaskownicy — emulator — Azure HDInsight
description: 'Aby rozpocząć, nauka o korzystaniu z ekosystemem Apache Hadoop, skonfigurowaniem piaskownica usługi Hadoop firmy Hortonworks na maszynie wirtualnej platformy Azure. '
keywords: emulator usługi hadoop, piaskownica usługi hadoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 15152196e45265985c8abb409523982bd4c5d427
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129455"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Rozpoczynanie pracy z piaskownicą Apache Hadoop, emulator na maszynie wirtualnej

Dowiedz się, jak zainstalować piaskownicy usługi Apache Hadoop firmy Hortonworks na maszynie wirtualnej, aby dowiedzieć się więcej na temat ekosystemu platformy Hadoop. Piaskownica udostępnia lokalne Środowisko deweloperskie Aby dowiedzieć się więcej na temat usługi Hadoop, Hadoop Distributed pliku System (HDFS) i przesłania zadania. Po przejściu na platformie Hadoop, można uruchomić za pomocą usługi Hadoop na platformie Azure przez utworzenie klastra usługi HDInsight. Aby uzyskać więcej informacji na temat rozpocząć pracę, zobacz [Rozpoczynanie pracy z platformą Hadoop w HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Wymagania wstępne
* [Oracle VirtualBox](https://www.virtualbox.org/). Pobierz i zainstaluj go z [tutaj](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Pobierz i zainstaluj maszynę wirtualną
1. Przejdź do [Hortonworks pliki do pobrania](https://hortonworks.com/downloads/#sandbox).

2. Kliknij przycisk **Pobierz VIRTUALBOX** do pobierania najnowszych Piaskownicą Hortonworks na maszynie Wirtualnej. Monit o zarejestrowanie Hortonworks, przed rozpoczęciem pobierania. Trwa jednej do dwóch godzin do pobrania w zależności od szybkości sieci.

    ![Obraz linku do pobrania Piaskownicą Hortonworks dla VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. W tej samej stronie sieci web kliknij **Import na Virtual Box** link, aby pobrać plik PDF zawierający instrukcje instalacji dla maszyny wirtualnej.

Aby pobrać starsze piaskownicy wersji HDP, rozwiń archiwum:

![Archiwum Piaskownicą Hortonworks](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Uruchom maszynę wirtualną

1. Otwórz Oracle VM VirtualBox.
2. Z **pliku** menu, kliknij przycisk **urządzenia importu**, a następnie określ obraz Piaskownicą Hortonworks.
1. Wybierz z Piaskownicą Hortonworks, kliknij przycisk **Start**, a następnie **Start normalny**. Po zakończeniu procesu rozruchu maszyny wirtualnej przedstawia instrukcje logowania.

    ![Normalnego uruchamiania](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Otwórz przeglądarkę internetową i przejdź do adresu URL wyświetlane (zazwyczaj `http://127.0.0.1:8888`).

## <a name="set-sandbox-passwords"></a>Ustawianie haseł piaskownicy

1. Z **wprowadzenie** krok z Piaskownicą Hortonworks wybierz opcję **widoku Zaawansowane opcje**. Skorzystaj z informacji na tej stronie, aby zalogować się do piaskownicy przy użyciu protokołu SSH. Użyj nazwy i hasła.

   > [!NOTE]
   > Jeśli nie masz zainstalowanego klienta SSH, można użyć SSH opartego na sieci web, podane w przez maszynę wirtualną w **http://localhost:4200/**.

    Po raz pierwszy łączysz się przy użyciu protokołu SSH, monit o zmianę hasła dla konta głównego. Wprowadź nowe hasło, którego używasz, po zalogowaniu przy użyciu protokołu SSH.

2. Po zalogowaniu, wprowadź następujące polecenie:

        ambari-admin-password-reset

    Po wyświetleniu monitu podaj hasło dla konta administratora Ambari. To jest używany, gdy uzyskujesz dostęp do Interfejsu sieci Web Ambari.

## <a name="use-hive-commands"></a>Za pomocą poleceń programu Hive

1. Z poziomu połączenia SSH piaskownicy Użyj następującego polecenia, aby uruchomić powłoki usługi Hive:

        hive
2. Po rozpoczęciu powłoki wyświetlać tabele, które są dostarczane z piaskownicy należy wykonać następujące kroki:

        show tables;
3. Poniższa tabela pobrać 10 wierszy z `sample_07` tabeli:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się, jak używać programu Visual Studio z Piaskownicą Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)
* [Nauka podstaw Piaskownicą Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Samouczek Hadoop — wprowadzenie do HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

