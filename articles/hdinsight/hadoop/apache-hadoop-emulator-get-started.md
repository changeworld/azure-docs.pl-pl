---
title: Naucz się używać piaskownicy Apache Hadoop, emulatora - Azure HDInsight
description: 'Aby rozpocząć naukę korzystania z ekosystemu Apache Hadoop, można skonfigurować piaskownicę Hadoop z Hortonworks na maszynie wirtualnej platformy Azure. '
keywords: emulator hadoop, piaskownica hadoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 47ee66393e3e1678576b12a70b767f35cb3bc635
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73044768"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Wprowadzenie do piaskownicy Apache Hadoop, emulatora na maszynie wirtualnej

Dowiedz się, jak zainstalować piaskownicę Apache Hadoop z Hortonworks na maszynie wirtualnej, aby dowiedzieć się więcej o ekosystemie Hadoop. Piaskownica zapewnia lokalne środowisko programistyczne, aby dowiedzieć się więcej o Hadoop, Hadoop Distributed File System (HDFS) i przesyłaniu zadań. Po zapoznaniu się z Hadoop, można rozpocząć korzystanie z Usługi Hadoop na platformie Azure, tworząc klaster HDInsight. Aby uzyskać więcej informacji na temat rozpoczynania pracy, zobacz [Wprowadzenie do usługi Hadoop w programie HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Oracle VirtualBox](https://www.virtualbox.org/). Pobierz i zainstaluj go [stąd](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>Pobieranie i instalowanie maszyny wirtualnej

1. Przejdź do [pobierania cloudera](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Kliknij **pozycję VIRTUALBOX** w obszarze **Wybierz typ instalacji,** aby pobrać najnowszą piaskownicę Hortonworks na maszynie wirtualnej. Zaloguj się lub wypełnij formularz zainteresowania produktem.

1. Kliknij przycisk **HDP SANDBOX (NAJNOWSZE),** aby rozpocząć pobieranie.

Aby uzyskać instrukcje dotyczące konfigurowania piaskownicy, zobacz [Przewodnik wdrażania i instalowania piaskownicy](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Aby pobrać starszą piaskownicę w wersji HDP, zobacz łącza w obszarze **Starsze wersje**.

## <a name="start-the-virtual-machine"></a>Uruchamianie maszyny wirtualnej

1. Otwórz oracle VM VirtualBox.
1. W menu **Plik** kliknij polecenie **Importuj urządzenie**, a następnie określ obraz piaskownicy Hortonworks.
1. Wybierz piaskownicę Hortonworks, kliknij przycisk **Start**, a następnie **pozycję Normalny start**. Po zakończeniu procesu rozruchu maszyna wirtualna wyświetla instrukcje logowania.

    ![normalny start menedżera virtualbox](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Otwórz przeglądarkę internetową i przejdź do `http://127.0.0.1:8888`wyświetlanego adresu URL (zwykle).

## <a name="set-sandbox-passwords"></a>Ustawianie haseł piaskownicy

1. W kroku **rozpoczynania** strony Piaskownica Hortonworks wybierz pozycję **Wyświetl opcje zaawansowane**. Użyj informacji na tej stronie, aby zalogować się do piaskownicy za pomocą SSH. Użyj podanej nazwy i hasła.

   > [!NOTE]
   > Jeśli nie masz zainstalowanego klienta SSH, możesz użyć internetowego SSH dostarczonego **http://localhost:4200/** przez maszynę wirtualną pod adresem .

    Przy pierwszym połączeniu przy użyciu funkcji SSH zostanie wyświetlony monit o zmianę hasła do konta głównego. Wprowadź nowe hasło, którego używasz podczas logowania za pomocą SSH.

2. Po zalogowaniu wprowadź następujące polecenie:

        ambari-admin-password-reset

    Po wyświetleniu monitu podaj hasło do konta administratora Ambari. Jest to używane podczas uzyskiwania dostępu do interfejsu użytkownika sieci Web Ambari.

## <a name="use-hive-commands"></a>Używanie poleceń gałąź

1. Z połączenia SSH do piaskownicy użyj następującego polecenia, aby uruchomić powłokę Hive:

        hive
2. Po uruchomieniu powłoki użyj następujących opcji, aby wyświetlić tabele dostarczane z piaskownicą:

        show tables;
3. Aby pobrać 10 wierszy z `sample_07` tabeli, użyj następujących czynności:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak korzystać z programu Visual Studio w piaskownicy Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)

* [Nauka lin piaskownicy Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Tutorial Hadoop - Pierwsze kroki z HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
