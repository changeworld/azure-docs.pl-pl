---
title: Synchronizacja LDAP w Ranger i Apache Ambari w usłudze Azure HDInsight
description: Zaaminuj synchronizację LDAP w Ranger i Ambari i podaj ogólne wytyczne.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463221"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Synchronizacja LDAP w Ranger i Apache Ambari w usłudze Azure HDInsight

Klastry pakietu zabezpieczeń HDInsight Enterprise Security Package (ESP) używają rangera do autoryzacji. Apache Ambari i Ranger synchronizują użytkowników i grupy niezależnie i działają trochę inaczej. Ten artykuł ma dotyczyć synchronizacji LDAP w Ranger i Ambari.

## <a name="general-guidelines"></a>Ogólne wskazówki

* Zawsze wdrażaj klastry z grupami.
* Zamiast zmieniać filtry grup w Ambari i Ranger, spróbuj zarządzać nimi w usłudze Azure AD i użyj grup zagnieżdżonych, aby wprowadzić wymaganych użytkowników.
* Po zsynchronizowanie użytkownika nie jest usuwane, nawet jeśli użytkownik nie należy do grup.
* Jeśli musisz zmienić filtry LDAP bezpośrednio, należy najpierw użyć interfejsu użytkownika, ponieważ zawiera pewne weryfikacje.

## <a name="users-are-synced-separately"></a>Użytkownicy są synchronizowani oddzielnie

Ambari i Ranger nie współużytkują bazy danych użytkowników, ponieważ służą one dwóm różnym celom. Jeśli użytkownik musi korzystać z interfejsu użytkownika Ambari, użytkownik musi zostać zsynchronizowany z ambari. Jeśli użytkownik nie jest zsynchronizowany z Ambari, Ambari UI / API odrzuci go, ale inne części systemu będzie działać (te są strzeżone przez Ranger lub Resource Manager, a nie Ambari). Jeśli chcesz dołączyć użytkownika do zasad Ranger, a następnie zsynchronizować użytkownika z Ranger.

Po wdrożeniu bezpiecznego klastra członkowie grupy są synchronizowani przechodnie (wszystkie podgrupy i ich członkowie) z ambari i ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Synchronizacja i konfiguracja użytkownika Ambari

Z węzłów głównego, zadanie `/opt/startup_scripts/start_ambari_ldap_sync.py`cron, , jest uruchamiany co godzinę, aby zaplanować synchronizację użytkownika. Zadanie cron wywołuje interfejsy API odpoczynku Ambari do wykonania synchronizacji. Skrypt przesyła listę użytkowników i grup do synchronizacji (ponieważ użytkownicy nie mogą należeć do określonych grup, oba są określone indywidualnie). Ambari synchronizuje sAMAccountName jako nazwę użytkownika i wszystkich członków grupy, przechodnie.

Dzienniki powinny znajdować `/var/log/ambari-server/ambari-server.log`się w pliku . Aby uzyskać więcej informacji, zobacz [Konfigurowanie poziomu rejestrowania Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

W klastrach usługi Data Lake hak tworzenia użytkownika postu służy do tworzenia folderów domowych dla zsynchronizowanych użytkowników i są one ustawione jako właściciele folderów domowych. Jeśli użytkownik nie jest poprawnie zsynchronizowane z Ambari, użytkownik może napotkać błędy w uzyskiwaniu dostępu do folderów przejściowych i innych folderów tymczasowych.

### <a name="update-groups-to-be-synced-to-ambari"></a>Aktualizuj grupy, które mają być synchronizowane z ambari

Jeśli nie możesz zarządzać członkostwem w grupach w usłudze Azure AD, masz dwie możliwości:

* Wykonaj jednorazową synchronizację zgodnie z opisem w [obszarze Synchronizuj użytkowników i grupy LDAP](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Za każdym razem, gdy członkostwo w grupie ulegnie zmianie, musisz ponownie wykonać tę synchronizację.

* Napisz zadanie cron, czasami wywoż [interfejs API Ambari](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) z nowymi grupami.

## <a name="ranger-user-sync-and-configuration"></a>Synchronizacja i konfiguracja użytkownika Ranger

Ranger ma wbudowany aparat synchronizacji, który działa co godzinę, aby zsynchronizować użytkowników. Nie udostępnia bazy danych użytkownika z Ambari. Usługa HDInsight konfiguruje filtr wyszukiwania w celu zsynchronizowania użytkownika administratora, użytkownika programu watchdog i członków grupy określonej podczas tworzenia klastra. Członkowie grupy zostaną zsynchronizowani przechodnie:

* Wyłącz synchronizację przyrostową.
* Włącz mapę synchronizacji grupy użytkowników.
* Określ filtr wyszukiwania, aby uwzględnić przechodnich członków grupy.
* Synchronizacja sAMAccountName dla użytkowników i atrybut name dla grup.

### <a name="group-or-incremental-sync"></a>Synchronizacja grupowa lub przyrostowa

Ranger obsługuje opcję synchronizacji grupowej, ale działa jako skrzyżowanie z filtrem użytkownika. Nie jest to związek między członkostwem w grupie a filtrem użytkowników. Typowym przypadkiem użycia filtru synchronizacji grup w Ranger jest - filtr grupy: (dn=clusteradmingroup), filtr użytkownika: (city=seattle).

Synchronizacja przyrostowa działa tylko dla użytkowników, którzy są już zsynchronizowani (za pierwszym razem). Przyrostowe nie synchronizuje żadnych nowych użytkowników dodanych do grup po synchronizacji początkowej.

### <a name="update-ranger-sync-filter"></a>Aktualizuj filtr synchronizacji Ranger

Filtr LDAP można znaleźć w interfejsie użytkownika Ambari, w sekcji konfiguracji synchronizacji użytkownika Ranger. Istniejący filtr będzie w `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`formularzu . Upewnij się, że dodajesz predykat na `net ads` końcu i testujesz filtr za pomocą polecenia wyszukiwania lub ldp.exe lub czegoś podobnego.

## <a name="ranger-user-sync-logs"></a>Dzienniki synchronizacji użytkowników Ranger

Synchronizacja użytkownika Ranger może się zdarzyć z jednego z headnodes. Dzienniki znajdują `/var/log/ranger/usersync/usersync.log`się w pliku . Aby zwiększyć szczegółowość dzienników, wykonaj następujące czynności:

1. Zaloguj się do Ambari.
1. Przejdź do sekcji konfiguracji Ranger.
1. Przejdź do sekcji Zaawansowane **usersync-log4j.**
1. Zmień `log4j.rootLogger` poziom `DEBUG` na poziomie (Po `log4j.rootLogger = DEBUG,logFile,FilterLog`zmianie powinien wyglądać ).
1. Zapisz konfigurację i uruchom ponownie ranger.

## <a name="next-steps"></a>Następne kroki

* [Problemy z uwierzytelnianiem w usłudze Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synchronizowanie użytkowników usługi Azure AD z klastrem usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
