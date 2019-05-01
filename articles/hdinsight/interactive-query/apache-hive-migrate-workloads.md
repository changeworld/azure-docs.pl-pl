---
title: Migrowanie obciążeń usługi Azure HDInsight 3.6 Hive HDInsight 4.0
description: Dowiedz się, jak przeprowadzić migrację obciążeń Apache Hive w HDInsight 3.6 HDInsight 4.0.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/24/2019
ms.openlocfilehash: b181edc08c51a5afa8682858b330acc84da7d73d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706999"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrowanie obciążeń usługi Azure HDInsight 3.6 Hive HDInsight 4.0

W tym dokumencie dowiesz się, jak przeprowadzić migrację obciążeń Apache Hive i funkcji LLAP w HDInsight 3.6 HDInsight 4.0. HDInsight 4.0 dostarcza nowsze funkcje programu Hive i funkcji LLAP, takie jak zmaterializowanych widoków i buforowanie wyników zapytania. Podczas migracji obciążeń do HDInsight 4.0, można użyć wielu nowsze funkcje Hive 3, które nie są dostępne w HDInsight 3.6.

W tym artykule omówiono następujące zagadnienia:

* Migracja metadanych programu Hive HDInsight 4.0
* Bezpieczne migracji kwasu i ACID inne niż tabele
* Zachowywanie informacji o gałęzi zasad zabezpieczeń w różnych wersjach HDInsight
* Wykonanie zapytania i debugowania z HDInsight 3.6 HDInsight 4.0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migracja metadanych Apache Hive HDInsight 4.0

Jedną z zalet programu Hive jest możliwość eksportowania metadanych do zewnętrznej bazy danych (określone jako magazyn metadanych Hive). **Hive magazynu metadanych** jest odpowiedzialny za przechowywanie statystyk tabeli, m.in. Lokalizacja magazynu tabeli nazwy kolumn i informacji o indeksu tabeli. Schemat bazy danych magazynu metadanych różni się między wersjami programu Hive. Wykonaj następujące czynności, aby uaktualnić metadanych programu Hive HDInsight 3.6 tak, aby była ona zgodna z HDInsight 4.0.

1. Utwórz nową kopię swojej zewnętrzny Magazyn metadanych. HDInsight 3.6 HDInsight 4.0 wymagane schematy innego magazynu metadanych i nie może udostępniać pojedynczy Magazyn metadanych.
1. Dołącz tę nową kopię magazynu metadanych) do istniejącego klastra HDInsight 4.0 lub (b) klastra, który tworzysz po raz pierwszy. Zobacz [używać zewnętrznych magazynów metadanych w usłudze Azure HDInsight](../hdinsight-use-external-metadata-stores.md) Aby dowiedzieć się więcej na temat dołączania zewnętrzny Magazyn metadanych do klastra usługi HDInsight. Po dołączeniu magazynu metadanych automatycznie zostaną przekonwertowane do metadanych zgodnego z 4.0.

> [!Warning]
> Nie można wycofać uaktualnienie, które konwertuje schematu metadanych HDInsight 3.6 schematu HDInsight 4.0.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrowanie tabel programu Hive HDInsight 4.0

Po zakończeniu poprzedniego zestawu kroków, aby przeprowadzić migrację magazynu metadanych Hive HDInsight 4.0, tabele i rejestrowane w Magazyn metadanych bazy danych będą widoczne w ramach klastra HDInsight 4.0, wykonując `show tables` lub `show databases` z w ramach klastra . Zobacz [wykonanie zapytania w różnych wersjach HDInsight](#query-execution-across-hdinsight-versions) uzyskać informacji na temat wykonywania zapytań w klastrach HDInsight 4.0.

Rzeczywiste dane z tabel, jednak nie jest dostępny do momentu klaster uzyskał dostęp do kont magazynu konieczne. Aby upewnić się, że klaster HDInsight 4.0 może uzyskiwać dostęp do tych samych danych co stary klaster HDInsight 3.6, wykonaj następujące czynności:

1. Określ konto usługi Azure storage w tabeli lub bazy danych przy użyciu opisać sformatowany.
2. Jeśli klaster HDInsight 4.0 jest już uruchomiona, należy dołączyć konta magazynu platformy Azure do klastra za pomocą systemu Ambari. Jeśli nie został jeszcze utworzony klaster HDInsight 4.0, upewnij się, że konto usługi Azure storage jest określony jako podstawowy lub pomocniczy klastra konta magazynu. Aby uzyskać więcej informacji na temat dodawania kont magazynu w klastrach HDInsight, zobacz [dodawanie kolejnych kont magazynu do HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tabele są traktowane inaczej w HDInsight 3.6 i HDInsight 4.0. Z tego powodu nie można udostępniać tych samych tabelach w przypadku klastrów w różnych wersjach. Jeśli chcesz użyć HDInsight 3.6 w tym samym czasie jako HDInsight 4.0, konieczne jest posiadanie oddzielnych kopii danych dla każdej wersji.

Obciążenia Hive może zawierać kombinację ACID ACID inne niż tabele. Jedną kluczową różnicą między gałęzi w HDInsight 3.6 (Hive 2) i Hive HDInsight 4.0 (Hive 3) jest zgodności ze standardem ACID dla tabel. W HDInsight 3.6 oraz włączenie zgodności ze standardem Hive ACID wymaga dodatkowych czynności konfiguracyjnych, ale w HDInsight w wersji 4.0 tabele są standardem ACID domyślnie. Jedyną akcją, wymagany, zanim migracji będzie uruchamiać ACID tabeli kompaktowania głównych w klastrze 3.6. Z widoku Hive lub z usługi Beeline uruchom następujące zapytanie:

```bash
alter table myacidtable compact 'major';
```

Kompaktowania jest wymagana, ponieważ tabele HDInsight 3.6 i HDInsight 4.0 ACID zrozumienie różnic ACID inaczej. Kompaktowanie wymusza pustego, która gwarantuje spójność. Sekcja 4 [Hive dokumentacja dotycząca migracji](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) zawiera wskazówki dotyczące kompaktowania zbiorcze HDInsight 3.6 ACID tabel.

Po zakończeniu kroków migracji i kompaktowania magazynu metadanych można przeprowadzić migrację magazynu rzeczywista. Po wykonaniu migracji magazynu Hive, w magazynie HDInsight 4.0 mają następujące właściwości:

* Tabele zewnętrzne w HDInsight 3.6 będzie tabel zewnętrznych w HDInsight w wersji 4.0
* Transakcyjne inne niż tabele zarządzanych w HDInsight 3.6 będzie tabel zewnętrznych w HDInsight w wersji 4.0
* Transakcyjne zarządzanych tabel w HDInsight 3.6 będą zarządzane tabel w HDInsight w wersji 4.0

Może być konieczne dostosowanie właściwości magazynu przed wykonaniem migracji. Na przykład Jeśli spodziewasz się, że część tabeli będzie uzyskiwał dostęp do innych firm (takimi jak klaster usługi HDInsight 3.6), to tej tabeli musi być zewnętrzny, po zakończeniu migracji. W HDInsight w wersji 4.0 wszystkie tabele zarządzane są transakcyjne. W związku z tym zarządzanych tabel w HDInsight w wersji 4.0 powinien zostać oceniony jedynie przez klastry HDInsight 4.0.

Gdy właściwości tabeli są ustawione poprawnie, uruchom narzędzie migracji magazynu Hive z jednego z głównymi węzłami klastra, przy użyciu powłoki SSH:

1. Nawiązać połączenie z głównym węzłem klastra przy użyciu protokołu SSH. Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z HDInsight przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Otwórz powłokę Zaloguj się jako użytkownik gałęzi, uruchamiając `sudo su - hive`
1. Sprawdź wersję stosu Hortonworks Data Platform, wykonując `ls /usr/hdp`. Spowoduje to wyświetlenie ciąg wersji, która powinna być używana w następnego polecenia.
1. Wykonaj następujące polecenie z poziomu powłoki. Zastąp `${{STACK_VERSION}}` z ciągu wersji z poprzedniego kroku:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

Po zakończeniu działania narzędzia migracji magazynu Hive będzie gotowy dla HDInsight w wersji 4.0. 

> [!Important]
> Tabele zarządzanych w wersji 4.0 HDInsight (takie jak tabele po migracji z 3.6) nie powinni mieć dostęp z innych usług lub aplikacji, w tym klastry HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Zabezpieczanie gałęzi w różnych wersjach HDInsight

Od wersji HDInsight 3.6 i HDInsight integruje się z usługą Azure Active Directory przy użyciu usługi HDInsight Enterprise Security pakietu (ESP). ESP używa protokołu Kerberos oraz struktury Apache Ranger do zarządzania uprawnieniami do określonych zasobów w klastrze. Zasady platformy ranger wdrożonych względem programu Hive w HDInsight 3.6 można poddać migracji do HDInsight 4.0 wykonując następujące kroki:

1. Przejdź do panelu Ranger Service Manager w klastrze usługi HDInsight 3.6.
2. Przejdź do zasad o nazwie **HIVE** i wyeksportować zasady do pliku json.
3. Upewnij się, że wszyscy użytkownicy, określone w formacie json wyeksportowanego zasady istnieją w nowym klastrze. Jeśli użytkownik jest określany w formacie json zasad, ale nie istnieje w nowym klastrze, Dodaj użytkownika do nowego klastra albo Usuń odwołanie z zasad.
4. Przejdź do **programu Service Manager Ranger** panelu w klastrze usługi HDInsight 4.0.
5. Przejdź do zasad o nazwie **HIVE** i zaimportuj json zasady platformy ranger z kroku 2.

## <a name="query-execution-across-hdinsight-versions"></a>Wykonywanie zapytania w różnych wersjach HDInsight

Istnieją dwa sposoby wykonania i debugowanie zapytań programu Hive/LLAP w ramach klastra usługi HDInsight 3.6. HiveCLI udostępnia środowisko wiersza polecenia i Wyświetl widok/Hive Tez zapewnia wyposażone w graficzny przepływ pracy.

W HDInsight w wersji 4.0 HiveCLI został zastąpiony z usługi Beeline. HiveCLI jest klienta thrift Hiveserver 1 i z usługi Beeline jest klientem JDBC, który zapewnia dostęp do Hiveserver 2. Z usługi beeline można również połączyć się z żadnych innych JDBC bazy danych punktu końcowego zgodnego z. Z usługi beeline jest dostępne out-of-box 4.0 HDInsight bez żadnej instalacji wymagane.

W HDInsight 3.6 oraz graficznego interfejsu użytkownika klienta do interakcji z serwerem programu Hive jest widoku Hive narzędzia Ambari. HDInsight 4.0 zastępuje widoku Hive za pomocą Hortonworks Data Analytics Studio (DAS). DAS nie są dostarczane z HDInsight clusters out-of-box i nie jest oficjalnie obsługiwany pakiet. Jednakże DAS można zainstalować w klastrze w następujący sposób:

Uruchamianie akcji skryptu względem klastra, za pomocą "Węzły główne" jako typ węzła do wykonania. Wklej następujący identyfikator URI w polu tekstowym, oznaczone jako "URI skryptu powłoki systemowej": https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Data Analytics Studio można uruchomić za pomocą adresu URL: https://<clustername>.azurehdinsight.net/das/



Po zainstalowaniu DAS, jeśli nie widzisz zapytań, uruchamianych w podglądzie zapytania, wykonaj następujące czynności:

1. Ustaw konfiguracje Hive Tez i DAS, zgodnie z opisem w [tego przewodnika Rozwiązywanie problemów z instalacją DAS](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Upewnij się, że następujące konfiguracje katalogu usługi Azure storage są Stronicowymi obiektami blob i są wyświetlane w obszarze `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Uruchom ponownie system plików HDFS, Hive, Tez i DAS na obu węzłów głównych.

## <a name="next-steps"></a>Kolejne kroki

* [Ogłoszenie HDInsight 4.0](../hdinsight-version-release.md)
* [Szczegółowe omówienie HDInsight 4.0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3 ACID tabel programu hive](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
