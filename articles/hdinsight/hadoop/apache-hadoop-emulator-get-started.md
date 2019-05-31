---
title: Dowiedz się, przy użyciu technologii Apache Hadoop piaskownicy — emulator — Azure HDInsight
description: 'Aby rozpocząć, nauka o korzystaniu z ekosystemem Apache Hadoop, skonfigurowaniem piaskownica usługi Hadoop firmy Hortonworks na maszynie wirtualnej platformy Azure. '
keywords: emulator usługi hadoop, piaskownica usługi hadoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 2cb99cfe765e1d3444f362e591812f5088c78c0e
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393147"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Rozpoczynanie pracy z piaskownicą Apache Hadoop, emulator na maszynie wirtualnej

Dowiedz się, jak zainstalować piaskownicy usługi Apache Hadoop firmy Hortonworks na maszynie wirtualnej, aby dowiedzieć się więcej na temat ekosystemu platformy Hadoop. Piaskownica udostępnia lokalne Środowisko deweloperskie Aby dowiedzieć się więcej na temat usługi Hadoop, Hadoop Distributed pliku System (HDFS) i przesłania zadania. Po przejściu na platformie Hadoop, można uruchomić za pomocą usługi Hadoop na platformie Azure przez utworzenie klastra usługi HDInsight. Aby uzyskać więcej informacji na temat rozpocząć pracę, zobacz [Rozpoczynanie pracy z platformą Hadoop w HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Wymagania wstępne
* [Oracle VirtualBox](https://www.virtualbox.org/). Pobierz i zainstaluj go z [tutaj](https://www.virtualbox.org/wiki/Downloads).


## <a name="download-and-install-the-virtual-machine"></a>Pobierz i zainstaluj maszynę wirtualną
1. Przejdź do [Cloudera pliki do pobrania](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

2. Kliknij przycisk **VIRTUALBOX** w obszarze **Wybieranie typu instalacji** do pobierania najnowszych Piaskownicą Hortonworks na maszynie Wirtualnej. Zaloguj się lub wypełnić formularz interesujących produktów.

1. Kliknij przycisk **PIASKOWNICY HDP (najnowsze)** aby rozpocząć pobieranie.

Aby uzyskać instrukcje na temat konfigurowania piaskownicy, zobacz [piaskownicy wdrożenia i Podręcznik instalacji](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Aby pobrać starsze piaskownicy wersji HDP, zobacz łącza w obszarze **starsze wersje**.

## <a name="start-the-virtual-machine"></a>Uruchom maszynę wirtualną

1. Otwórz Oracle VM VirtualBox.
2. Z **pliku** menu, kliknij przycisk **urządzenia importu**, a następnie określ obraz Piaskownicą Hortonworks.
1. Wybierz z Piaskownicą Hortonworks, kliknij przycisk **Start**, a następnie **Start normalny**. Po zakończeniu procesu rozruchu maszyny wirtualnej przedstawia instrukcje logowania.

    ![Normalnego uruchamiania](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Otwórz przeglądarkę internetową i przejdź do adresu URL wyświetlane (zazwyczaj `http://127.0.0.1:8888`).

## <a name="set-sandbox-passwords"></a>Ustawianie haseł piaskownicy

1. Z **wprowadzenie** krok z Piaskownicą Hortonworks wybierz opcję **widoku Zaawansowane opcje**. Skorzystaj z informacji na tej stronie, aby zalogować się do piaskownicy przy użyciu protokołu SSH. Użyj nazwy i hasła.

   > [!NOTE]
   > Jeśli nie masz zainstalowanego klienta SSH, można użyć SSH opartego na sieci web, podane w przez maszynę wirtualną w **http://localhost:4200/** .

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

