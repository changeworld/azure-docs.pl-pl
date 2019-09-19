---
title: Migrowanie obciążeń platformy Azure HDInsight 3,6 do usługi HDInsight 4,0
description: Dowiedz się, jak migrować obciążenia Apache Hive w usłudze HDInsight 3,6 do usługi HDInsight 4,0.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 0363f2d8da1ca1371fd55107c6487c3d96f6d00e
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091469"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrowanie obciążeń platformy Azure HDInsight 3,6 do usługi HDInsight 4,0

W tym dokumencie przedstawiono sposób migrowania obciążeń Apache Hive i LLAP w usłudze HDInsight 3,6 do usługi HDInsight 4,0. Usługa HDInsight 4,0 udostępnia nowsze funkcje Hive i LLAP, takie jak widoki z materiałami i buforowanie wyników zapytania. Podczas migrowania obciążeń do usługi HDInsight 4,0 można korzystać z wielu nowszych funkcji programu Hive 3, które nie są dostępne w usłudze HDInsight 3,6.

W tym artykule opisano następujące zagadnienia:

* Migracja metadanych programu Hive do usługi HDInsight 4,0
* Bezpieczna migracja tabel KWASowych i niekwaśnych
* Zachowywanie zasad zabezpieczeń programu Hive w wersjach usługi HDInsight
* Wykonywanie zapytań i debugowanie z usługi HDInsight 3,6 do usługi HDInsight 4,0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrowanie metadanych Apache Hive do usługi HDInsight 4,0

Jedną z zalet programu Hive jest możliwość eksportowania metadanych do zewnętrznej bazy danych (określanej jako magazyn metadanych Hive). **Magazyn metadanych Hive** jest odpowiedzialny za przechowywanie statystyk tabeli, w tym lokalizacji magazynu tabel, nazw kolumn i informacji o indeksie tabeli. Schemat bazy danych magazynu metadanych różni się między wersjami programu Hive. Wykonaj poniższe czynności, aby uaktualnić magazyn metadanych Hive usługi HDInsight 3,6 w taki sposób, aby był zgodny z usługą HDInsight 4,0.

1. Utwórz nową kopię zewnętrznego magazynu metadanych. Usługi HDInsight 3,6 i HDInsight 4,0 wymagają różnych schematów magazynu metadanych i nie mogą współdzielić pojedynczego magazynu. Zobacz [Używanie zewnętrznych magazynów metadanych w usłudze Azure HDInsight](../hdinsight-use-external-metadata-stores.md) , aby dowiedzieć się więcej na temat dołączania zewnętrznego magazynu Metadata do klastra usługi HDInsight. 
2. Uruchom akcję skryptu względem klastra HDI 3,6 z "węzłami głównymi" jako typem węzła do wykonania. Wklej następujący identyfikator URI do pola tekstowego oznaczonego jako "bash skryptu URI https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh":. W polu tekstowym oznaczonym jako "argumenty" wprowadź wartość Nazwa serwera, bazy danych, nazwę użytkownika i hasło dla skopiowanego magazyn metadanych Hive, rozdzielone spacjami. Podczas określania serwera ServerName nie należy umieszczać ". database.windows.net".

> [!Warning]
> Uaktualnienie, które konwertuje schemat metadanych usługi HDInsight 3,6 do schematu usługi HDInsight 4,0, nie może zostać cofnięte.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrowanie tabel programu Hive do usługi HDInsight 4,0

Po ukończeniu poprzedniego zestawu kroków w celu migrowania magazynu metadanych Hive do usługi HDInsight 4,0 tabele i bazy danych zarejestrowane w magazynie metadanych będą widoczne w klastrze usługi HDInsight 4,0 przez wykonanie `show tables` lub `show databases` z poziomu klastra. . Aby uzyskać informacje na temat wykonywania zapytań w klastrach usługi HDInsight 4,0, zobacz [wykonywanie zapytań w różnych wersjach usługi HDInsight](#query-execution-across-hdinsight-versions) .

Jednak rzeczywiste dane z tabel nie są dostępne, dopóki klaster nie będzie miał dostępu do niezbędnych kont magazynu. Aby upewnić się, że klaster usługi HDInsight 4,0 ma dostęp do tych samych danych co stary klaster usługi HDInsight 3,6, wykonaj następujące czynności:

1. Określ konto usługi Azure Storage tabeli lub bazy danych przy użyciu opisu sformatowanego.
2. Jeśli klaster HDInsight 4,0 jest już uruchomiony, Dołącz konto usługi Azure Storage do klastra za pośrednictwem Ambari. Jeśli klaster HDInsight 4,0 nie został jeszcze utworzony, upewnij się, że konto usługi Azure Storage jest określone jako konto podstawowego lub pomocniczego magazynu klastra. Aby uzyskać więcej informacji na temat dodawania kont magazynu do klastrów usługi HDInsight, zobacz [Dodawanie dodatkowych kont magazynu do usługi HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tabele są traktowane inaczej w usłudze HDInsight 3,6 i HDInsight 4,0. Z tego powodu nie można współużytkować tych samych tabel dla klastrów różnych wersji. Jeśli chcesz używać usługi HDInsight 3,6 w tym samym czasie co Usługa HDInsight 4,0, musisz mieć osobne kopie danych dla każdej wersji.

Obciążenie programu Hive może zawierać mieszaninę tabel KWASowych i niekwasowych. Jedną z kluczowych różnic między usługą Hive w usłudze HDInsight 3,6 (Hive 2) i Hive w usłudze HDInsight 4,0 (Hive 3) jest zgodność z KWASem dla tabel. W usłudze HDInsight 3,6 włączenie zgodności z KWASem Hive wymaga dodatkowej konfiguracji, ale w tabelach usługi HDInsight 4,0 są domyślnie zgodne ze standardami. Jedyną akcją wymaganą przed migracją jest uruchomienie poważniejszej kompaktowania w tabeli KWASowej w klastrze 3,6. W widoku programu Hive lub z Z usługi Beeline Uruchom następujące zapytanie:

```bash
alter table myacidtable compact 'major';
```

Ta kompaktowanie jest wymagane, ponieważ tabele KWASów HDInsight 3,6 i HDInsight 4,0 są w inny sposób rozpoznawane pod kątem różnic w środowisku. Kompaktowanie wymusza czystą podstawę, która gwarantuje spójność. Sekcja 4 [dokumentacji migracji programu Hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) zawiera wskazówki dotyczące masowej kompaktowania tabel z kwasem HDInsight 3,6.

Po ukończeniu kroków migracji i kompaktowania magazynu metadanych można migrować rzeczywisty magazyn. Po zakończeniu migracji magazynu Hive magazyn usługi HDInsight 4,0 będzie miał następujące właściwości:

* Tabele zewnętrzne w usłudze HDInsight 3,6 będą tabelami zewnętrznymi w usłudze HDInsight 4,0
* Nietransakcyjne tabele zarządzane w usłudze HDInsight 3,6 będą tabelami zewnętrznymi w usłudze HDInsight 4,0
* Tabele zarządzane przez transakcyjną w usłudze HDInsight 3,6 będą tabelami zarządzanymi w usłudze HDInsight 4,0

Przed wykonaniem migracji może być konieczne dostosowanie właściwości hurtowni. Na przykład, jeśli oczekujesz, że niektóre tabele będą dostępne dla innej firmy (takiej jak klaster usługi HDInsight 3,6), ta tabela musi być zewnętrzna po zakończeniu migracji. W usłudze HDInsight 4,0 wszystkie zarządzane tabele są transakcyjne. W związku z tym tabele zarządzane w usłudze HDInsight 4,0 powinny być dostępne tylko w klastrach usługi HDInsight 4,0.

Po poprawnym ustawieniu właściwości tabeli należy wykonać narzędzie migracji magazynu Hive z jednego z węzłów głównych klastra przy użyciu powłoki SSH:

1. Nawiąż połączenie z klastrem węzła głównego przy użyciu protokołu SSH. Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z usługą HDInsight przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Otwórz powłokę logowania jako użytkownika programu Hive, uruchamiając`sudo su - hive`
1. Określ wersję stosu Hortonworks Data Platform, wykonując `ls /usr/hdp`. Spowoduje to wyświetlenie ciągu wersji, którego należy użyć w następnym poleceniu.
1. Wykonaj następujące polecenie w powłoce. Zamień `${{STACK_VERSION}}` na ciąg wersji z poprzedniego kroku:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Po zakończeniu działania narzędzia migracji magazyn Hive będzie gotowy do usługi HDInsight 4,0. 

> [!Important]
> Tabele zarządzane w usłudze HDInsight 4,0 (w tym tabele migrowane z 3,6) nie powinny mieć dostępu do innych usług ani aplikacji, w tym klastrów usługi HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Zabezpieczanie usługi Hive w wersjach usługi HDInsight

Ponieważ Usługa HDInsight 3,6, Usługa HDInsight integruje się z Azure Active Directory przy użyciu pakiet Enterprise Security usługi HDInsight (ESP). ESP używa protokołów Kerberos i Apache Ranger do zarządzania uprawnieniami określonych zasobów w klastrze. Zasady Ranger wdrożone w usłudze Hive w usłudze HDInsight 3,6 można migrować do usługi HDInsight 4,0, wykonując następujące czynności:

1. Przejdź do panelu Ranger Service Manager w klastrze usługi HDInsight 3,6.
2. Przejdź do zasad o nazwie **Hive** i wyeksportuj zasady do pliku JSON.
3. Upewnij się, że wszyscy użytkownicy określeni w wyeksportowanym pliku JSON zasad istnieją w nowym klastrze. Jeśli użytkownik jest określony w pliku JSON zasad, ale nie istnieje w nowym klastrze, należy dodać użytkownika do nowego klastra lub usunąć odwołanie z zasad.
4. Przejdź do panelu **Ranger Service Manager** w klastrze usługi HDInsight 4,0.
5. Przejdź do zasad o nazwie **Hive** i zaimportuj kod JSON zasad Ranger z kroku 2.

## <a name="query-execution-across-hdinsight-versions"></a>Wykonywanie zapytania w wersjach usługi HDInsight

Istnieją dwa sposoby wykonywania i debugowania zapytań Hive/LLAP w klastrze usługi HDInsight 3,6. HiveCLI udostępnia środowisko wiersza polecenia, a widok tez/Hive zawiera przepływ pracy oparty na graficznym interfejsie użytkownika.

W usłudze HDInsight 4,0 HiveCLI został zastąpiony Z usługi Beeline. HiveCLI to Thrift Client for Hiveserver 1, a Z usługi Beeline to klient JDBC, który zapewnia dostęp do Hiveserver 2. Z usługi Beeline może również służyć do nawiązywania połączenia z dowolnym innym punktem końcowym bazy danych zgodnym z JDBC. Usługa z usługi Beeline jest dostępna w przypadku usługi HDInsight 4,0 bez konieczności instalacji.

W usłudze HDInsight 3,6 klient z graficznym interfejsem użytkownika służący do współdziałania z serwerem Hive jest widokiem Ambari Hive. Usługa HDInsight 4,0 zastępuje widok programu Hive za pomocą Hortonworks Data Analytics Studio (DAS). DAS nie jest dostarczana z klastrami usługi HDInsight i nie jest oficjalnie obsługiwanym pakietem. Program DAS można jednak zainstalować w klastrze w następujący sposób:

Uruchom akcję skryptu względem klastra z "węzłami głównymi" jako typ węzła do wykonania. Wklej następujący identyfikator URI do pola tekstowego oznaczonego jako "bash skryptu URI": https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Zaczekaj od 5 do 10 minut, a następnie uruchom program Data Analytics Studio przy\<użyciu tego adresu URL: https://ClusterName >. azurehdinsight. NET/Das/

Jeśli nie widzisz zapytań, które zostały uruchomione w podglądzie zapytań, należy wykonać następujące czynności:

1. Ustaw konfiguracje dla programu Hive, tez i DAS, jak opisano w [tym przewodniku dotyczące rozwiązywania problemów z instalacją Das](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Upewnij się, że następujące konfiguracje katalogów usługi Azure Storage są stronicowymi obiektami BLOB i są wyświetlane w obszarze `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Uruchom ponownie System HDFS, Hive, tez i DAS na obu węzłów głównychach.

## <a name="next-steps"></a>Następne kroki

* [Anons usługi HDInsight 4,0](../hdinsight-version-release.md)
* [HDInsight 4,0 głębokie szczegółowe](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabele KWASów Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
