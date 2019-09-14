---
title: Synchronizowanie Azure Active Directory użytkowników z klastrem — Azure HDInsight
description: Zsynchronizuj uwierzytelnionych użytkowników z Azure Active Directory do klastra usługi HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9a7008ca7967135209b8fc2c341998570a2679be
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960835"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synchronizowanie użytkowników usługi Azure Active Directory z klastrem usługi HDInsight

[Klastry usługi HDInsight z pakiet Enterprise Security (ESP)](hdinsight-domain-joined-introduction.md) mogą używać silnego uwierzytelniania z użytkownikami Azure Active Directory (Azure AD), a także używać zasad *kontroli dostępu opartej na rolach* (RBAC). Podczas dodawania użytkowników i grup do usługi Azure AD można synchronizować użytkowników, którzy potrzebują dostępu do klastra.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli jeszcze tego nie zrobiono, [Utwórz klaster usługi HDInsight z pakiet Enterprise Security](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Dodawanie nowych użytkowników usługi Azure AD

Aby wyświetlić hosty, Otwórz interfejs użytkownika sieci Web Ambari. Każdy węzeł zostanie zaktualizowany przy użyciu nowych ustawień uaktualnienia nienadzorowanego.

1. W [Azure Portal](https://portal.azure.com)przejdź do katalogu usługi Azure AD skojarzonego z klastrem ESP.

2. Wybierz pozycję **Wszyscy użytkownicy** w menu po lewej stronie, a następnie wybierz pozycję **nowy użytkownik**.

    ![Okienko wszystkie użytkowników](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Wypełnij formularz nowego użytkownika. Wybierz grupy, które zostały utworzone na potrzeby przypisywania uprawnień opartych na klastrze. W tym przykładzie należy utworzyć grupę o nazwie "HiveUsers", do której można przypisać nowych użytkowników. [Przykładowe instrukcje](hdinsight-domain-joined-configure.md) dotyczące tworzenia klastra ESP obejmują dodanie dwóch grup `HiveUsers` i. `AAD DC Administrators`

    ![Okienko nowego użytkownika](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Wybierz pozycję **Utwórz**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Korzystanie z interfejsu API REST usługi Apache Ambari w celu synchronizowania użytkowników

Grupy użytkowników określone podczas procesu tworzenia klastra są zsynchronizowane w tym czasie. Synchronizacja użytkowników odbywa się automatycznie co godzinę. Aby natychmiast zsynchronizować użytkowników lub zsynchronizować grupę inną niż grupa określona podczas tworzenia klastra, użyj interfejsu API REST Ambari.

W poniższej metodzie jest używany wpis POST z interfejsem API REST Ambari. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Połącz się z klastrem przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md). W okienku Przegląd klastra w Azure Portal wybierz przycisk **Secure Shell (SSH)** .

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-secure-shell.png)

2. Skopiuj wyświetlone `ssh` polecenie i wklej je do klienta SSH. Po wyświetleniu monitu wprowadź hasło użytkownika ssh.

3. Po uwierzytelnieniu wprowadź następujące polecenie:

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    Odpowiedź powinna wyglądać następująco:

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. Aby wyświetlić stan synchronizacji, wykonaj nowe `curl` polecenie:

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    Odpowiedź powinna wyglądać następująco:
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
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

5. Ten wynik pokazuje, że stan jest **zakończony**, jeden nowy użytkownik został utworzony, a użytkownik przypisał do niego członkostwo. W tym przykładzie użytkownik jest przypisany do synchronizowanej grupy LDAP "HiveUsers", ponieważ użytkownik został dodany do tej samej grupy w usłudze Azure AD.

> [!NOTE]  
> Poprzednia metoda synchronizuje grupy usługi Azure AD określone we właściwości **grupy użytkowników dostępu** w ustawieniach domeny podczas tworzenia klastra. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra usługi HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Weryfikowanie nowo dodanego użytkownika usługi Azure AD

Otwórz [interfejs użytkownika programu Apache Ambari Web](hdinsight-hadoop-manage-ambari.md) , aby sprawdzić, czy został dodany nowy użytkownik usługi Azure AD. Dostęp do Interfejsu sieci Web Ambari, przechodząc do **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Wprowadź nazwę użytkownika i hasło administratora klastra.

1. Na pulpicie nawigacyjnym Ambari wybierz pozycję **Zarządzaj Ambari** w menu **administrator** .

    ![Zarządzanie Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Wybierz opcję **Użytkownicy** w grupie menu **Zarządzanie użytkownikami i grupami** po lewej stronie.

    ![Element menu użytkownicy](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Nowy użytkownik powinien być wymieniony w tabeli users (Użytkownicy). Typ ma ustawioną wartość `LDAP` , `Local`a nie.

    ![Strona użytkowników](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Zaloguj się do Ambari jako nowy użytkownik

Gdy nowy użytkownik (lub inny użytkownik domeny) zaloguje się do usługi Ambari, użyje pełnej nazwy użytkownika i domeny usługi Azure AD.  Ambari wyświetla alias użytkownika, który jest nazwą wyświetlaną użytkownika w usłudze Azure AD. Nowy przykładowy użytkownik ma nazwę `hiveuser3@contoso.com`użytkownika. W programie Ambari ten nowy użytkownik jest wyświetlany, `hiveuser3` gdy użytkownik zaloguje się do Ambari `hiveuser3@contoso.com`jako.

## <a name="see-also"></a>Zobacz także

* [Konfigurowanie zasad Apache Hive w usłudze HDInsight przy użyciu protokołu ESP](hdinsight-domain-joined-run-hive.md)
* [Zarządzanie klastrami usługi HDInsight przy użyciu protokołu ESP](hdinsight-domain-joined-manage.md)
* [Autoryzuj użytkowników do Apache Ambari](hdinsight-authorize-users-to-ambari.md)
