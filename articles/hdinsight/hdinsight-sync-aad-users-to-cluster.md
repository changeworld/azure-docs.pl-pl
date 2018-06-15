---
title: Synchronizowanie użytkowników usługi Azure Active Directory do klastra - Azure HDInsight | Dokumentacja firmy Microsoft
description: Synchronizuj uwierzytelnionych użytkowników z usługi Azure Active Directory do klastra.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: f2deaaa31a4d0e8a91d048b538e9251a8eb9e1b7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409285"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synchronizowanie użytkowników usługi Azure Active Directory do klastra usługi HDInsight

[Przyłączony do domeny w usłudze hdinsight](hdinsight-domain-joined-introduction.md) można silnego uwierzytelniania za pomocą użytkowników usługi Azure Active Directory (Azure AD), a także użyć *kontroli dostępu opartej na rolach* zasad (RBAC). W miarę dodawania użytkowników i grup do usługi Azure AD, możesz zsynchronizować użytkowników, którzy potrzebują dostępu do klastra.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli użytkownik nie zostało jeszcze zrobione, [tworzenia klastra usługi HDInsight przyłączonych do domeny](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Dodaj nowy Azure użytkowników usługi AD

Aby wyświetlić hosty, Otwórz interfejs sieci Web Ambari. Każdy węzeł zostanie zaktualizowany przy użyciu nowych ustawień uaktualnienia instalacji nienadzorowanej.

1. W [portalu Azure](https://portal.azure.com), przejdź do katalogu usługi Azure AD skojarzony z klastrem przyłączone do domeny.

2. Wybierz **wszyscy użytkownicy** z menu po lewej stronie, następnie wybierz **nowego użytkownika**.

    ![Wszystkie okienka użytkowników](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Wypełnij formularz nowego użytkownika. Wybierz grupy, które utworzono przypisywania uprawnień opartych na klastrze. W tym przykładzie należy utworzyć grupę o nazwie "HiveUsers", do której można przypisać nowych użytkowników. [Przykładowe instrukcje](hdinsight-domain-joined-configure.md) tworzenia klastra przyłączonych do domeny obejmują dodawanie dwie grupy `HiveUsers` i `AAD DC Administrators`.

    ![Nowe okienko użytkownika](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Wybierz pozycję **Utwórz**.

## <a name="use-the-ambari-rest-api-to-synchronize-users"></a>Synchronizowanie użytkowników za pomocą interfejsu API REST Ambari

Grupy użytkowników określana podczas procesu tworzenia klastra są synchronizowane w tym czasie. Synchronizacja użytkownika odbywa się automatycznie co godzinę. Synchronizowanie użytkowników natychmiast lub zsynchronizować grupę innych niż grup określone podczas tworzenia klastra, należy użyć interfejsu API REST Ambari.

Następująca metoda używa POST przy użyciu interfejsu API REST Ambari. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Połączyć się z klastrem przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md). W okienku Omówienie klastra w portalu Azure, wybierz **Secure Shell (SSH)** przycisku.

    ![Bezpieczna powłoka (SSH)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. Skopiuj wyświetlone `ssh` polecenie i wklej go do klienta SSH. Wprowadź ssh hasło użytkownika, po wyświetleniu monitu.

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

4. Aby wyświetlić stan synchronizacji, wykonaj nowy `curl` polecenia przy użyciu `href` wartość zwracana z poprzedniego polecenia:

    ```bash
    curl -u admin:<YOUR PASSWORD> http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1
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
> Poprzednia metoda synchronizuje tylko grup usługi Azure AD, określona w **grupy użytkowników dostępu** właściwość ustawienia domeny podczas tworzenia klastra. Aby uzyskać więcej informacji, zobacz [tworzenia klastra usługi HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Sprawdź nowo dodanego użytkownika usługi Azure AD

Otwórz [Interfejsu sieci Web Ambari](hdinsight-hadoop-manage-ambari.md) do sprawdzenia, czy nowy dodano użytkownika usługi Azure AD. Dostęp do Interfejsu sieci Web Ambari, przechodząc do **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Wprowadź hasło i nazwa użytkownika administratora klastra.

1. Na pulpicie nawigacyjnym Ambari, wybierz **Zarządzanie Ambari** w obszarze **admin** menu.

    ![Zarządzanie Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Wybierz **użytkowników** w obszarze **użytkownika + grupy zarządzania** grupy menu w lewej części strony.

    ![Element menu użytkowników](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. Nowy użytkownik powinien być wyświetlany w tabeli użytkowników. Typ ma ustawioną wartość `LDAP` zamiast `Local`.

    ![Strona Użytkownicy](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Zaloguj się do narzędzia Ambari jako nowy użytkownik

Kiedy nowy użytkownik (lub innego użytkownika domeny), o których loguje się do narzędzia Ambari, korzystają z ich pełną usługi Azure AD poświadczeniach nazwy użytkownika i domeny.  Ambari Wyświetla alias użytkownika, który jest nazwa wyświetlana użytkownika w usłudze Azure AD. Nowy użytkownik przykładzie ma nazwę użytkownika `hiveuser3@contoso.com`. W Ambari, ten nowy użytkownik jest wyświetlany jako `hiveuser3` , ale użytkownik loguje się do narzędzia Ambari jako `hiveuser3@contoso.com`.

## <a name="see-also"></a>Zobacz także

* [Konfigurowanie zasad Hive w usłudze HDInsight z przyłączonych do domeny](hdinsight-domain-joined-run-hive.md)
* [Zarządzanie przyłączonych do domeny w usłudze hdinsight](hdinsight-domain-joined-manage.md)
* [Autoryzowanie użytkowników do Ambari](hdinsight-authorize-users-to-ambari.md)
