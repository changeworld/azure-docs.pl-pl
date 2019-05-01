---
title: Synchronizowanie użytkowników usługi Azure Active Directory do klastra — Azure HDInsight
description: Synchronizuj uwierzytelnionych użytkowników z usługi Azure Active Directory do klastra.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 2be67c604bebbe9b4c4356e241d1480ca0778d4a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688546"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synchronizowanie użytkowników usługi Azure Active Directory z klastrem usługi HDInsight

[HDInsight clusters przy użyciu pakietu zabezpieczeń przedsiębiorstwa (ESP)](hdinsight-domain-joined-introduction.md) można silnego uwierzytelniania za pomocą użytkowników usługi Azure Active Directory (Azure AD), a także używać *kontroli dostępu opartej na rolach* zasady (RBAC). W miarę dodawania użytkowników i grup do usługi Azure AD, można zsynchronizować użytkowników, którzy potrzebują dostępu do klastra.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli użytkownik jeszcze nie zrobiono, [Tworzenie klastra HDInsight z pakietem Enterprise Security](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Dodawanie nowej usługi Azure AD użytkownicy

Aby wyświetlić hostów, Otwórz interfejs użytkownika sieci Web Ambari. Każdy węzeł zostanie zaktualizowany o nowe ustawienia uaktualnienia instalacji nienadzorowanej.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do katalogu usługi Azure AD skojarzonego z klastrem ESP.

2. Wybierz **wszyscy użytkownicy** z menu po lewej stronie, następnie wybierz pozycję **nowego użytkownika**.

    ![Wszystkie okienko użytkowników](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Wypełnij formularz nowego użytkownika. Wybierz grupy, utworzonego do przypisywania uprawnień opartych na klastrze. W tym przykładzie należy utworzyć grupę o nazwie "HiveUsers", do którego można przypisywać nowych użytkowników. [Przykładowe instrukcje](hdinsight-domain-joined-configure.md) do utworzenia klastra ESP, należą: dodanie dwóch grup `HiveUsers` i `AAD DC Administrators`.

    ![Nowe okienko użytkownika](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Wybierz pozycję **Utwórz**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Użyj interfejsu API Apache Ambari REST, aby zsynchronizować użytkowników

Grupy użytkowników, które określono podczas procesu tworzenia klastra są synchronizowane w tym czasie. Synchronizacja użytkownika odbywa się automatycznie co godzinę. Aby natychmiast zsynchronizować użytkowników lub zsynchronizować grupie innej niż grupy określone podczas tworzenia klastra, należy użyć interfejsu API REST Ambari.

Następującą metodę używa WPIS, za pomocą interfejsu API REST Ambari. Aby uzyskać więcej informacji, zobacz [HDInsight Zarządzanie klastrami za pomocą interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Połącz z klastrem przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md). W okienku Przegląd klastra w witrynie Azure portal wybierz **Secure Shell (SSH)** przycisku.

    ![Bezpieczna powłoka (SSH)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. Skopiuj wyświetlonych `ssh` polecenie i wklej go do klienta SSH. Wprowadź ssh hasło użytkownika po wyświetleniu monitu.

3. Po uwierzytelnieniu, wprowadź następujące polecenie:

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

4. Aby wyświetlić stan synchronizacji, należy wykonać nową `curl` polecenia:

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

5. Ten wynik pokazuje, że jest w stanie **COMPLETE**, jeden nowy użytkownik został utworzony, a użytkownik został przypisany członkostwa. W tym przykładzie użytkownik jest przypisany do "HiveUsers" synchronizowane grupy LDAP, ponieważ użytkownik został dodany do tej samej grupy w usłudze Azure AD.

> [!NOTE]  
> Poprzednia metoda synchronizuje tylko grup usługi Azure AD, określone w **dostęp do grupy użytkowników** właściwości ustawienia domeny podczas tworzenia klastra. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra usługi HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Sprawdź nowo dodanych użytkowników usługi Azure AD

Otwórz [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) do sprawdzenia, czy nowy użytkownik usługi Azure AD został dodany. Dostęp do Interfejsu sieci Web Ambari, przechodząc do **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Wprowadź nazwę użytkownika administratora klastra i hasło.

1. Na pulpicie nawigacyjnym Ambari wybierz **Zarządzanie Ambari** w obszarze **administratora** menu.

    ![Zarządzanie systemu Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Wybierz **użytkowników** w obszarze **użytkowników i zarządzanie grupami** grupy menu po lewej stronie strony.

    ![Element menu użytkowników](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. Nowy użytkownik powinien zostać wyświetlony w tabeli użytkowników. Typ jest ustawiona na `LDAP` zamiast `Local`.

    ![Strona użytkowników](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Zaloguj się do systemu Ambari jako nowy użytkownik

Kiedy nowy użytkownik (lub innego użytkownika domeny), o których loguje się do systemu Ambari, używają pełnych poświadczeń usługi Azure AD użytkownika nazwy i domeny.  Ambari Wyświetla alias użytkownika, który jest nazwa wyświetlana użytkownika w usłudze Azure AD. Nowy przykład użytkownik ma nazwę użytkownika `hiveuser3@contoso.com`. W Ambari, nowego użytkownika jest wyświetlany jako `hiveuser3` , ale użytkownik loguje się do systemu Ambari jako `hiveuser3@contoso.com`.

## <a name="see-also"></a>Zobacz także

* [Konfigurowanie zasad usługi Apache Hive HDInsight przy użyciu ESP](hdinsight-domain-joined-run-hive.md)
* [Zarządzanie klastrami HDInsight przy użyciu ESP](hdinsight-domain-joined-manage.md)
* [Autoryzowanie użytkowników do systemu Apache Ambari](hdinsight-authorize-users-to-ambari.md)
