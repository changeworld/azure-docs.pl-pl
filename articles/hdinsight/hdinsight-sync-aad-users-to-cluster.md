---
title: Synchronizowanie użytkowników usługi Azure Active Directory z klastrem usługi HDInsight
description: Synchronizowanie uwierzytelnionych użytkowników z usługi Azure Active Directory z klastrem USŁUGI HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 299d242c38152db6a471159d1f3d2803598c1832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744865"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synchronizowanie użytkowników usługi Azure Active Directory z klastrem usługi HDInsight

[Klastry usługi HDInsight z pakietem zabezpieczeń przedsiębiorstwa (ESP)](hdinsight-domain-joined-introduction.md) mogą używać silnego uwierzytelniania u użytkowników usługi Azure Active Directory (Azure AD), a także używać zasad *kontroli dostępu opartej na rolach* (RBAC). Podczas dodawania użytkowników i grup do usługi Azure AD można synchronizować użytkowników, którzy potrzebują dostępu do klastra.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli jeszcze tego nie zrobiono, [utwórz klaster HDInsight z pakietem zabezpieczeń przedsiębiorstwa](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Dodawanie nowych użytkowników usługi Azure AD

Aby wyświetlić hosty, otwórz interfejs użytkownika sieci Web Ambari. Każdy węzeł zostanie zaktualizowany o nowe ustawienia uaktualnienia nienadzorowanego.

1. Z [witryny Azure Portal](https://portal.azure.com)przejdź do katalogu usługi Azure AD skojarzonego z klastrem ESP.

2. Wybierz **pozycję Wszyscy użytkownicy** z menu po lewej stronie, a następnie wybierz pozycję **Nowy użytkownik**.

    ![Użytkownicy i grupy w witrynie Azure portal](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Wypełnij nowy formularz użytkownika. Wybierz grupy utworzone w celu przypisania uprawnień opartych na klastrze. W tym przykładzie należy utworzyć grupę o nazwie "HiveUsers", do której można przypisać nowych użytkowników. [Przykładowe instrukcje dotyczące](hdinsight-domain-joined-configure.md) tworzenia klastra ESP `HiveUsers` obejmują `AAD DC Administrators`dodawanie dwóch grup i .

    ![Grupy wyboru okienka użytkowników portalu Azure](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Wybierz **pozycję Utwórz**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Użyj interfejsu API Apache Ambari REST do synchronizacji użytkowników

Grupy użytkowników określone w trakcie procesu tworzenia klastra są synchronizowane w tym czasie. Synchronizacja użytkowników odbywa się automatycznie co godzinę. Aby natychmiast zsynchronizować użytkowników lub zsynchronizować grupę inną niż grupy określone podczas tworzenia klastra, użyj interfejsu API AMbari REST.

Poniższa metoda używa POST z interfejsem API AMbari REST. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami HDInsight przy użyciu interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Użyj [polecenia ssh,](hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po uwierzytelnieniu wprowadź następujące polecenie:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    Odpowiedź powinna wyglądać następująco:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Aby wyświetlić stan synchronizacji, `curl` wykonaj nowe polecenie:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    Odpowiedź powinna wyglądać następująco:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Ten wynik pokazuje, że stan jest **COMPLETE,** jeden nowy użytkownik został utworzony, a użytkownik został przypisany członkostwa. W tym przykładzie użytkownik jest przypisany do grupy LDAP zsynchronizowanej przez użytkowników, ponieważ użytkownik został dodany do tej samej grupy w usłudze Azure AD.

    > [!NOTE]  
    > Poprzednia metoda synchronizuje tylko grupy usługi Azure AD określone we właściwości **grupy użytkowników programu Access** ustawień domeny podczas tworzenia klastra. Aby uzyskać więcej informacji, zobacz [tworzenie klastra HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Weryfikowanie nowo dodanego użytkownika usługi Azure AD

Otwórz [interfejs użytkownika sieci Web Apache Ambari,](hdinsight-hadoop-manage-ambari.md) aby sprawdzić, czy nowy użytkownik usługi Azure AD został dodany. Uzyskaj dostęp do interfejsu użytkownika sieci **`https://CLUSTERNAME.azurehdinsight.net`** Web Ambari, przejdź do pliku . Wprowadź nazwę użytkownika i hasło administratora klastra.

1. Na pulpicie nawigacyjnym Ambari wybierz pozycję **Zarządzaj ambari** w menu **administratora.**

    ![Apache Ambari deska rozdzielcza Zarządzaj Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Wybierz **pozycję Użytkownicy** w grupie menu **Zarządzanie użytkownikami + grupa** po lewej stronie strony.

    ![Menu Użytkownicy i grupy HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Nowy użytkownik powinien być wymieniony w tabeli Użytkownicy. Typ jest ustawiony `LDAP` na `Local`zamiast .

    ![Omówienie strony użytkowników aad HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Zaloguj się do Ambari jako nowy użytkownik

Gdy nowy użytkownik (lub inny użytkownik domeny) loguje się do Ambari, używają pełnej nazwy użytkownika usługi Azure AD i poświadczeń domeny.  Ambari wyświetla alias użytkownika, który jest nazwą wyświetlaną użytkownika w usłudze Azure AD.
Nowy przykładowy użytkownik ma `hiveuser3@contoso.com`nazwę użytkownika . W Ambari, ten nowy `hiveuser3` użytkownik pojawia się jako ale `hiveuser3@contoso.com`użytkownik loguje się do Ambari jako .

## <a name="see-also"></a>Zobacz też

* [Konfigurowanie zasad gałęzi Apache w udziale HDInsight za pomocą usługi ESP](hdinsight-domain-joined-run-hive.md)
* [Zarządzanie klastrami HDInsight za pomocą esp](hdinsight-domain-joined-manage.md)
* [Autoryzowanie użytkowników do Apache Ambari](hdinsight-authorize-users-to-ambari.md)
