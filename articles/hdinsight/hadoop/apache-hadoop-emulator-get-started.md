---
title: Dowiedz się przy użyciu piaskownicy — emulator — Azure HDInsight Hadoop | Dokumentacja firmy Microsoft
description: 'Aby rozpocząć, informacje o użyciu ekosystemu Hadoop, skonfigurowaniem piaskownicy Hadoop z Hortonworks na maszynie wirtualnej platformy Azure. '
keywords: emulator usługi hadoop, piaskownicy hadoop
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: ''
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 0b4daa8c832d40457b19ffe4aee1a365796b2435
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Rozpoczynanie pracy z piaskownicą Hadoop, emulator na maszynie wirtualnej

Dowiedz się, jak zainstalować piaskownicy Hadoop z Hortonworks na maszynie wirtualnej, aby dowiedzieć się więcej o ekosystemu Hadoop. Piaskownica udostępnia środowisko deweloperskie lokalne informacje na temat usługi Hadoop, System plików rozproszonych Hadoop (HDFS) i przesyłanie zadań. Po zapoznaniu się z usługi Hadoop, można uruchomić przy użyciu platformy Hadoop na platformie Azure przez utworzenie klastra usługi HDInsight. Aby uzyskać więcej informacji o tym, jak rozpocząć pracę, zobacz [Rozpoczynanie pracy z platformą Hadoop w HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Wymagania wstępne
* [Oracle VirtualBox](https://www.virtualbox.org/). Pobierz i zainstaluj go z [tutaj](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Pobierz i zainstaluj maszynę wirtualną
1. Przejdź do [pobiera Hortonworks](http://hortonworks.com/downloads/#sandbox).

2. Kliknij przycisk **Pobierz VIRTUALBOX** pobrać najnowsze piaskownicy Hortonworks na maszynie Wirtualnej. Zostanie wyświetlony monit Zarejestruj Hortonworks przed rozpoczęciem pobierania. Trwa jednej do dwóch godzin, aby pobrać w zależności od szybkości sieci.
   
    ![Łącze obrazu do pobrania izolowanego Hortonworks VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. W tej samej stronie sieci web, kliknij polecenie **importu na pole wirtualnego** łącze, aby pobrać plik PDF zawierający instrukcje instalacji dla maszyny wirtualnej.

Aby pobrać starsze piaskownicy wersji HDP, rozwiń archiwum:

![Archiwum Hortonworks piaskownicy](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Uruchom maszynę wirtualną

1. Otwórz program Oracle VirtualBox maszyny Wirtualnej.
2. Z **pliku** menu, kliknij przycisk **urządzenia importu**, a następnie określ obraz piaskownicy Hortonworks.
1. Wybierz piaskownicy Hortonworks, kliknij przycisk **Start**, a następnie **Start normalny**. Po zakończeniu procesu rozruchu maszyny wirtualnej zawiera instrukcje dotyczące logowania.
   
    ![Normalnego uruchomienia](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Otwórz przeglądarkę sieci web i przejdź do adresu URL wyświetlane (zazwyczaj http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Ustawianie hasła piaskownicy

1. Z **wprowadzenie** krok piaskownicy Hortonworks stronie wybierz **zaawansowane opcje wyświetlania**. Skorzystaj z informacji na tej stronie, aby zalogować się do piaskownicy przy użyciu protokołu SSH. Użyj nazwy i hasła.
   
   > [!NOTE]
   > Jeśli nie ma zainstalowanego klienta SSH, możesz użyć SSH opartych na sieci web, dostępnych na przez maszynę wirtualną na **http://localhost:4200/**.
   > 
   
    Podczas pierwszego, gdy nawiązujesz połączenie przy użyciu protokołu SSH, monit o zmianę hasła dla konta głównego. Wprowadź nowe hasło, które zostaną użyte podczas logowania przy użyciu protokołu SSH.

2. Po zalogowaniu, wprowadź następujące polecenie:
   
        ambari-admin-password-reset
   
    Po wyświetleniu monitu podaj hasło dla konta administratora narzędzia Ambari. To jest używany, gdy uzyskujesz dostęp do Interfejsu sieci Web Ambari.

## <a name="use-hive-commands"></a>Używanie Hive poleceń

1. Połączenie SSH piaskownicy Użyj następującego polecenia, aby uruchomić powłokę programu Hive:
   
        hive
2. Po rozpoczęciu powłoki, należy użyć następującego, aby wyświetlić tabele, które są udostępniane w piaskownicy:
   
        show tables;
3. Użyj następujących funkcji do pobrania 10 wierszy z `sample_07` tabeli:
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się, jak używać programu Visual Studio z piaskownicy Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)
* [Learning liny piaskownicy Hortonworks](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Samouczek Hadoop — wprowadzenie HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

