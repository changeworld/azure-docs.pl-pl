---
title: 'Samouczek: Konfigurowanie powiadomień e-mail Apache Ambari w usłudze Azure HDInsight'
description: W tym artykule opisano, jak używać SendGrid z Apache Ambari do powiadomień e-mail.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 21376eb40fb40abe67f7e03d15aabd7d89ea62f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082314"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Samouczek: Konfigurowanie powiadomień e-mail Apache Ambari w usłudze Azure HDInsight

W tym samouczku skonfigurujesz powiadomienia e-mail Apache Ambari za pomocą SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) upraszcza zarządzanie i monitorowanie klastra HDInsight, zapewniając łatwy w użyciu interfejs użytkownika sieci web i interfejs API REST. Ambari jest dołączony do klastrów HDInsight i służy do monitorowania klastra i wprowadzania zmian konfiguracji. [SendGrid](https://sendgrid.com/solutions/) to bezpłatna chmurowa usługa poczty e-mail, która zapewnia niezawodne transakcyjne dostarczanie wiadomości e-mail, skalowalność i analizę w czasie rzeczywistym wraz z elastycznymi interfejsami API, które ułatwiają niestandardową integrację. W każdym miesiącu klienci platformy Azure mogą odblokować 25 000 bezpłatnych wiadomości e-mail.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uzyskaj nazwę użytkownika Sendgrid
> * Konfigurowanie powiadomień e-mail Apache Ambari

## <a name="prerequisites"></a>Wymagania wstępne

* Konto e-mail SendGrid. Zobacz [Jak wysłać wiadomość e-mail przy użyciu sendgrid z platformy Azure,](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email) aby uzyskać instrukcje.

* Klaster HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Uzyskaj nazwę użytkownika SendGrid

1. Z [witryny Azure portal](https://portal.azure.com)przejdź do zasobu SendGrid.

1. Na stronie Przegląd wybierz pozycję **Zarządzaj**, aby przejść do strony sendgrid dla swojego konta.

    ![Omówienie sendgrid w witrynie Azure Portal](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. W menu po lewej stronie przejdź do nazwy konta, a następnie **do szczegółów konta**.

    ![Nawigacja na pulpicie nawigacyjnym SendGrid](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. Na stronie **Szczegóły konta** zapisz **nazwę użytkownika**.

    ![Szczegóły konta SendGrid](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Konfigurowanie powiadomienia e-mail ambari

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. Z listy rozwijanej **Akcje** wybierz pozycję **Zarządzaj powiadomieniami**.

1. W oknie **Zarządzanie powiadomieniami o alertach** wybierz ikonę. **+**

    ![Ambari utworzyć powiadomienie o alertach](./media/apache-ambari-email/azure-portal-create-notification.png)

1. W oknie dialogowym **Tworzenie powiadomień o alertach** podaj następujące informacje:

    |Właściwość |Opis |
    |---|---|
    |Nazwa|Podaj nazwę powiadomienia.|
    |Grupy|Skonfiguruj zgodnie z potrzebami.|
    |Ważność|Skonfiguruj zgodnie z potrzebami.|
    |Opis|Element opcjonalny.|
    |Metoda|Wyjdź na **e-mail**.|
    |Wyślij wiadomość e-mail do|Udostępniaj wiadomości e-mail w celu otrzymywania powiadomień oddzielonych przecinkiem.|
    |Serwer SMTP|`smtp.sendgrid.net`|
    |SMTP Port|25 lub 587 (dla połączeń niezaszyfrowanych/TLS).|
    |Wyślij wiadomość e-mail z|Podaj adres e-mail. Adres nie musi być autentyczny.|
    |Korzystanie z uwierzytelniania|Zaznacz to pole wyboru.|
    |Nazwa użytkownika|Podaj nazwę użytkownika SendGrid.|
    |Hasło|Podaj hasło użyte podczas tworzenia zasobu SendGrid na platformie Azure.|
    |Potwierdzenie hasła|Ponownie wprowadź hasło.|
    |Uruchamianie protokołu TLS|Zaznacz to pole wyboru|

    ![Ambari utworzyć powiadomienie o alertach](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Wybierz **pozycję Zapisz**. Powrócisz do okna **Zarządzanie powiadomieniami o alertach.**

1. W oknie **Zarządzanie powiadomieniami o alertach** wybierz pozycję **Zamknij**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku, dowiedziałeś się, jak skonfigurować Apache Ambari powiadomień e-mail za pomocą SendGrid. Aby dowiedzieć się więcej o Apache Ambari, dowiedz się więcej o Apache Ambari:

* [Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari](./hdinsight-hadoop-manage-ambari.md)

* [Tworzenie powiadomienia o alertach](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
