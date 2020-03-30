---
title: Opis konfiguracji okresowej kopii zapasowej
description: Użyj funkcji okresowej kopii zapasowej i przywracania sieci szkieletowej usług, aby włączyć okresową kopię zapasową danych aplikacji.
author: hrushib
ms.topic: article
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: 34c6495e094a1160f6ac75b9f098934d5cbce967
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610152"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Opis okresowej konfiguracji kopii zapasowej w sieci szkieletowej usług Azure

Konfigurowanie okresowej kopii zapasowej niezawodnych usług stanowych lub niezawodnych aktorów składa się z następujących kroków:

1. **Tworzenie zasad tworzenia kopii zapasowych:** W tym kroku tworzona jest co najmniej jedna zasada tworzenia kopii zapasowych w zależności od wymagań.

2. **Włączanie tworzenia kopii zapasowych:** W tym kroku należy skojarzyć zasady tworzenia kopii zapasowych utworzone w **kroku 1** z wymaganymi encjami, _aplikacją,_ _usługą_lub _partycją_.

## <a name="create-backup-policy"></a>Tworzenie zasad tworzenia kopii zapasowych

Zasady tworzenia kopii zapasowych składają się z następujących konfiguracji:

* **Automatyczne przywracanie przy utracie danych**: Określa, czy wyzwalanie przywracania ma być automatycznie używane przy użyciu najnowszej dostępnej kopii zapasowej w przypadku wystąpienia zdarzenia utraty danych przez partycję.

* **Maksymalna przyrostowa kopia zapasowa**: Określa maksymalną liczbę przyrostowych kopii zapasowych, które mają być wykonane między dwoma pełnymi kopiami zapasowymi. Maksymalne przyrostowe kopie zapasowe określają górny limit. Pełna kopia zapasowa może zostać pobrana przed zakończeniem określonej liczby przyrostowych kopii zapasowych w jednym z następujących warunków

    1. Replika nigdy nie podjęła pełnej kopii zapasowej, ponieważ stała się podstawowa.

    2. Niektóre rekordy dziennika od ostatniej kopii zapasowej zostały obcięty.

    3. Replika przekazała limit MaxAccumulatedBackupLogSizeInMB.

* **Harmonogram tworzenia kopii zapasowych:** czas lub częstotliwość wykonywania okresowych kopii zapasowych. Można zaplanować powtarzanie kopii zapasowych w określonym przedziale czasu lub o ustalonym czasie dziennym / tygodniowym.

    1. **Harmonogram tworzenia kopii zapasowych oparty na częstotliwościach:** Ten typ harmonogramu powinien być używany, jeśli konieczne jest wykonywanie kopii zapasowych danych w ustalonych odstępach czasu. Żądany przedział czasu między dwoma kolejnymi kopiami zapasowymi jest definiowany przy użyciu formatu ISO8601. Harmonogram tworzenia kopii zapasowych oparty na częstotliwości obsługuje rozdzielczość interwału do minuty.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Harmonogram tworzenia kopii zapasowych oparty na czasie:** Ten typ harmonogramu powinien być używany, jeśli konieczne jest wykonywanie kopii zapasowej danych o określonych porach dnia lub tygodnia. Typ częstotliwości harmonogramu może być dzienny lub tygodniowy.
        1. ** _Dzienny_ harmonogram tworzenia kopii zapasowych oparty na czasie:** Ten typ harmonogramu powinien być używany, jeśli identyfikator musi wykonać kopię zapasową danych o określonych porach dnia. Aby to określić, ustaw na `ScheduleFrequencyType` _Dzienne_; i `RunTimes` ustawić listę żądanego czasu w ciągu dnia w formacie ISO8601, data określona wraz z czasem zostanie zignorowana. Na przykład `0001-01-01T18:00:00` reprezentuje _6:00 PM_ codziennie, ignorując część _daty 0001-01-01_. Poniższy przykład ilustruje konfigurację wyzwalania codziennej kopii zapasowej codziennie _o 9:00_ i _18:00_ codziennie.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. ** _Tygodniowy_ harmonogram tworzenia kopii zapasowych oparty na czasie:** Ten typ harmonogramu powinien być używany, jeśli identyfikator musi wykonać kopię zapasową danych o określonych porach dnia. Aby to określić, ustaw na `ScheduleFrequencyType` Co _tydzień_; ustawiono `RunDays` listę dni w tygodniu, gdy kopia `RunTimes` zapasowa musi zostać wyzwolona i ustawiona na listę żądanego czasu w ciągu dnia w formacie ISO8601, data określona wraz z czasem zostanie zignorowana. Lista dni w tygodniu, kiedy wyzwolić okresową kopię zapasową. Poniższy przykład ilustruje konfigurację wyzwalania codziennej kopii zapasowej o _9:00_ i _18:00_ w nocy z poniedziałku na piątek.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Magazyn kopii zapasowych:** Określa lokalizację przekazywania kopii zapasowych. Magazyn może być magazynem obiektów blob platformy Azure lub udziałem plików.
    1. **Magazyn obiektów blob platformy Azure:** Ten typ magazynu należy wybrać, gdy potrzeba jest do przechowywania generowanych kopii zapasowych na platformie Azure. Klastry _autonomiczne_ i _oparte na platformie Azure_ mogą używać tego typu magazynu. Opis dla tego typu magazynu wymaga parametry połączenia i nazwy kontenera, w którym należy przekazać kopie zapasowe. Jeśli kontener o określonej nazwie nie jest dostępny, zostanie utworzony podczas przekazywania kopii zapasowej.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Udział plików:** Ten typ magazynu należy wybrać dla _klastrów autonomicznych,_ gdy konieczne jest przechowywanie kopii zapasowej danych lokalnie. Opis dla tego typu magazynu wymaga ścieżki udziału plików, w której należy przekazać kopie zapasowe. Dostęp do udziału plików można skonfigurować przy użyciu jednej z następujących opcji
        1. _Zintegrowane uwierzytelnianie systemu Windows_, w którym dostęp do udziału plików jest udostępniany wszystkim komputerom należącym do klastra sieci szkieletowej usług. W takim przypadku ustaw następujące pola, aby skonfigurować magazyn kopii zapasowych oparty na _udziale plików._

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Ochrona udziału plików przy użyciu nazwy użytkownika i hasła,_ gdzie dostęp do udziału plików jest dostarczany określonym użytkownikom. Specyfikacja magazynu udziału plików zapewnia również możliwość określenia pomocniczej nazwy użytkownika i hasła pomocniczego w celu zapewnienia poświadczeń rezerwowych w przypadku, gdy uwierzytelnianie nie powiedzie się przy użyciu podstawowej nazwy użytkownika i hasła podstawowego. W takim przypadku ustaw następujące pola, aby skonfigurować magazyn kopii zapasowych oparty na _udziale plików._

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Upewnij się, że niezawodność pamięci masowej spełnia lub przekracza wymagania dotyczące niezawodności danych kopii zapasowych.
>

* **Zasady przechowywania:** Określa zasady przechowywania kopii zapasowych w skonfigurowanym magazynie. Obsługiwane są tylko podstawowe zasady przechowywania.
    1. **Podstawowe zasady przechowywania:** Ta zasada przechowywania pozwala zapewnić optymalne wykorzystanie magazynu przez usunięcie plików kopii zapasowych, które nie są już wymagane. `RetentionDuration`można określić, aby ustawić przedział czasu, dla którego kopie zapasowe muszą być przechowywane w magazynie. `MinimumNumberOfBackups`jest opcjonalnym parametrem, który można określić, aby upewnić się, `RetentionDuration`że określona liczba kopii zapasowych jest zawsze zachowywana niezależnie od pliku . Poniższy przykład ilustruje konfigurację przechowywania kopii zapasowych przez _10_ dni i nie pozwala, aby liczba kopii zapasowych była niższa niż _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Włączanie okresowego tworzenia kopii zapasowych
Po zdefiniowaniu zasad tworzenia kopii zapasowych w celu spełnienia wymagań dotyczących tworzenia kopii zapasowych zasady tworzenia kopii zapasowych powinny być odpowiednio skojarzone z _aplikacją_lub _usługą_lub _partycją._

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarchiczne propagowanie zasad tworzenia kopii zapasowych
W sieci szkieletowej usług relacja między aplikacją, usługą i partycjami jest hierarchiczna, jak wyjaśniono w [modelu aplikacji](./service-fabric-application-model.md). Zasady tworzenia kopii zapasowej mogą być skojarzone z _aplikacją,_ _usługą_lub _partycją_ w hierarchii. Zasady tworzenia kopii zapasowych propaguje hierarchicznie do następnego poziomu. Zakładając, że istnieje tylko jedna zasada tworzenia kopii zapasowych utworzone i skojarzone z _aplikacją,_ wszystkie partycje stanowe należące do wszystkich _niezawodnych usług stanowych_ i _wiarygodnych aktorów_ _aplikacji_ będą archiwizowane przy użyciu zasad tworzenia kopii zapasowych. Lub jeśli zasady tworzenia kopii zapasowej jest skojarzony z _niezawodną usługą stanową,_ wszystkie jego partycje będą archiwizowane przy użyciu zasad tworzenia kopii zapasowych.

### <a name="overriding-backup-policy"></a>Zastępowanie zasad tworzenia kopii zapasowych
Może istnieć scenariusz, w którym kopia zapasowa danych z tym samym harmonogramem tworzenia kopii zapasowych jest wymagana dla wszystkich usług aplikacji, z wyjątkiem określonych usług, w których konieczne jest tworzenie kopii zapasowych danych przy użyciu wyższego harmonogramu częstotliwości lub wykonywanie kopii zapasowej na inne konto magazynu lub Fileshare. Aby rozwiązać takie scenariusze, usługa przywracania kopii zapasowych zapewnia możliwość zastępowania propagowanych zasad w zakresie usługi i partycji. Gdy zasady tworzenia kopii zapasowej są skojarzone w _usłudze_ lub _partycji,_ zastępuje zasady propagowanej kopii zapasowej, jeśli takie istnieją.

### <a name="example"></a>Przykład

W tym przykładzie użyto konfiguracji z dwiema aplikacjami, _MyApp_A_ i _MyApp_B_. MyApp_A _MyApp_A_ aplikacji zawiera dwie niezawodne usługi stanowe, _SvcA1_ & _SvcA3_i jedną usługę Niezawodnego aktora, _ActorA2_. _SvcA1_ zawiera trzy partycje, podczas gdy _ActorA2_ i _SvcA3_ zawierają dwie partycje.  MyApp_B _MyApp_B_ aplikacji zawiera trzy niezawodne usługi stanowe, _SvcB1_, _SvcB2_i _SvcB3_. _SvcB1_ i _SvcB2_ zawiera dwie partycje, podczas gdy _SvcB3_ zawiera trzy partycje.

Załóżmy, że wymagania dotyczące tworzenia kopii zapasowych danych tych aplikacji są następujące

1. MyApp_A
    1. Tworzenie codziennej kopii zapasowej danych dla wszystkich partycji wszystkich _niezawodnych usług stanowych_ i _wiarygodnych podmiotów_ należących do aplikacji. Przekaż dane kopii zapasowej do lokalizacji _BackupStore1_.

    2. Jedna z usług, _SvcA3,_ wymaga kopii zapasowej danych co godzinę.

    3. Rozmiar danych w _SvcA1_P2_ partycji jest większy niż oczekiwano, a jego dane kopii zapasowej powinny być przechowywane w innej lokalizacji przechowywania _BackupStore2_.

2. MyApp_B
    1. Tworzenie kopii zapasowej danych w każdą niedzielę o 8:00 am dla wszystkich partycji usługi _SvcB1._ Przekaż dane kopii zapasowej do lokalizacji _BackupStore1_.

    2. Tworzenie kopii zapasowej danych codziennie o 8:00 am dla _partycji SvcB2_P1_. Przekaż dane kopii zapasowej do lokalizacji _BackupStore1_.

Aby spełnić te wymagania dotyczące tworzenia kopii zapasowych danych, zasady tworzenia kopii zapasowych BP_1 BP_5 są tworzone, a tworzenie kopii zapasowych jest włączone w następujący sposób.
1. MyApp_A
    1. Tworzenie zasad tworzenia kopii zapasowych, _BP_1,_ z harmonogramem tworzenia kopii zapasowych opartym na częstotliwości, gdzie częstotliwość jest ustawiona na 24 godziny. i magazyn kopii zapasowych skonfigurowany do używania lokalizacji przechowywania _BackupStore1_. Włącz tę zasadę dla _MyApp_A_ aplikacji przy użyciu [interfejsu API włącz tworzenie kopii zapasowych aplikacji.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) Ta akcja umożliwia tworzenie kopii zapasowych danych przy użyciu zasad tworzenia kopii zapasowych _BP_1_ dla wszystkich partycji _niezawodnych usług stanowych_ i _wiarygodnych podmiotów_ należących do _MyApp_A_aplikacji .

    2. Tworzenie zasad tworzenia kopii zapasowych, _BP_2,_ z harmonogramem tworzenia kopii zapasowych opartym na częstotliwości, w którym częstotliwość jest ustawiona na 1 godzinę. i magazyn kopii zapasowych skonfigurowany do używania lokalizacji przechowywania _BackupStore1_. Włącz tę zasadę dla usługi _SvcA3_ przy użyciu [interfejsu API włącz tworzenie kopii zapasowej usługi.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) Ta akcja zastępuje propagowane _zasady BP_1_ przez jawnie włączone zasady tworzenia kopii zapasowych _BP_2_ dla wszystkich partycji usługi _SvcA3_ prowadzące do tworzenia kopii zapasowych danych przy użyciu zasad tworzenia kopii zapasowych _BP_2_ dla tych partycji.

    3. Tworzenie zasad tworzenia kopii zapasowych, _BP_3_, z harmonogramem tworzenia kopii zapasowych opartych na częstotliwości, gdzie częstotliwość jest ustawiona na 24 godziny. i magazyn kopii zapasowych skonfigurowany do używania lokalizacji przechowywania _BackupStore2_. Włącz tę zasadę dla _SvcA1_P2_ partycji przy użyciu [interfejsu API włącz tworzenie kopii zapasowych partycji.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Ta akcja zastępuje propagowane _zasady BP_1_ przez jawnie włączone zasady tworzenia kopii zapasowych _BP_3_ dla _SvcA1_P2_partycji .

2. MyApp_B
    1. Utwórz zasady tworzenia kopii zapasowych, _BP_4_, z harmonogramem tworzenia kopii zapasowych opartym na czasie, gdzie typ częstotliwości harmonogramu jest ustawiony na tygodniowy, dni uruchamiania są ustawione na niedzielę, a godziny uruchamiania są ustawione na 8:00. Magazyn kopii zapasowych skonfigurowany do używania lokalizacji przechowywania _BackupStore1_. Włącz tę zasadę dla usługi _SvcB1_ przy użyciu [interfejsu API włącz tworzenie kopii zapasowej usługi.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) Ta akcja umożliwia tworzenie kopii zapasowych danych przy użyciu zasad tworzenia kopii zapasowych _BP_4_ dla wszystkich partycji usługi _SvcB1_.

    2. Utwórz zasady tworzenia kopii zapasowych, _BP_5_, z harmonogramem tworzenia kopii zapasowych opartym na czasie, w którym typ częstotliwości harmonogramu jest ustawiony na dzienny, a czas wykonywania jest ustawiony na 8:00. Magazyn kopii zapasowych skonfigurowany do używania lokalizacji przechowywania _BackupStore1_. Włącz tę zasadę dla _SvcB2_P1_ partycji przy użyciu [interfejsu API włącz tworzenie kopii zapasowych partycji.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Ta akcja umożliwia tworzenie kopii zapasowych danych przy użyciu zasad tworzenia kopii zapasowych _BP_5_ dla _SvcB2_P1_partycji .

Poniższy diagram przedstawia jawnie włączone zasady tworzenia kopii zapasowych i zasady propagacji kopii zapasowych.

![Hierarchia aplikacji sieci szkieletowej usług][0]

## <a name="disable-backup"></a>Wyłączanie tworzenia kopii zapasowych
Zasady tworzenia kopii zapasowych można wyłączyć, gdy nie ma potrzeby tworzenia kopii zapasowych danych. Zasady tworzenia kopii zapasowej włączone w _aplikacji_ można wyłączyć tylko w tej samej _aplikacji_ przy użyciu [interfejsu API wyłączanie kopii zapasowej aplikacji,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) zasady kopii zapasowej włączone w _usłudze_ można wyłączyć w tej samej _usłudze_ przy użyciu [interfejsu API wyłączanie kopii zapasowej,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) a zasady kopii zapasowej włączone na _partycji_ można wyłączyć na tej _samej partycji_ przy użyciu [interfejsu API Wyłącz tworzenie kopii zapasowej.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup)

* Wyłączenie zasad tworzenia kopii zapasowych dla _aplikacji_ zatrzymuje wszystkie okresowe kopie zapasowe danych w wyniku propagacji zasad tworzenia kopii zapasowych do partycji usługi niezawodne stanowe lub partycje niezawodnego aktora.

* Wyłączenie zasad tworzenia kopii zapasowych dla _usługi_ zatrzymuje wszystkie okresowe kopie zapasowe danych w wyniku propagacji tej zasady tworzenia kopii zapasowych na partycje _usługi_.

* Wyłączenie zasad tworzenia kopii zapasowych dla _partycji_ zatrzymuje wszystkie okresowe tworzenie kopii zapasowych danych z powodu zasad tworzenia kopii zapasowych na partycji.

* Podczas wyłączania kopii zapasowej dla jednostki(aplikacja/usługa/partycja), `CleanBackup` można ustawić na _true,_ aby usunąć wszystkie kopie zapasowe w skonfigurowanym magazynie.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Wstrzymanie & wznowienia tworzenia kopii zapasowej
W niektórych sytuacjach może grozić tymczasowe zawieszenie okresowego tworzenia kopii zapasowych danych. W takiej sytuacji, w zależności od wymagań, zawiesić interfejs API kopii zapasowej mogą być używane w _aplikacji_, _usługi_lub _partycji_. Okresowe zawieszenie kopii zapasowej jest przechodnie przez poddrzewie hierarchii aplikacji od punktu, w który jest stosowana. 

* Po zastosowaniu zawieszenia w _aplikacji_ przy użyciu [interfejsu API wstrzymania kopii zapasowej aplikacji,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) wszystkie usługi i partycje w ramach tej aplikacji są zawieszone na okresowe tworzenie kopii zapasowych danych.

* Po zastosowaniu zawieszenia w _usłudze_ przy użyciu [interfejsu API wstrzymania kopii zapasowej usługi,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) wszystkie partycje w ramach tej usługi są zawieszone na okresowe tworzenie kopii zapasowych danych.

* Gdy zawieszenie jest stosowane na _partycji_ przy użyciu interfejsu API [wstrzymania kopii zapasowej partycji,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) a następnie zawiesza partycje w ramach tej usługi są zawieszone dla okresowej kopii zapasowej danych.

Po zakończeniu konieczności zawieszenia, okresowe tworzenie kopii zapasowych danych można przywrócić przy użyciu odpowiedniego interfejsu API kopii zapasowej wznowić. Okresowa kopia zapasowa musi zostać wznowiona w tej samej _aplikacji,_ _usłudze_lub _partycji,_ w której została zawieszona.

* Jeśli zawieszenie zostało zastosowane w _aplikacji,_ należy je wznowić za pomocą [interfejsu API resume application backup.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) 

* Jeśli zawieszenie zostało zastosowane w _usłudze,_ należy je wznowić za pomocą interfejsu [API resume service backup.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup)

* Jeśli zawieszenie zostało zastosowane na _partycji,_ należy je wznowić za pomocą interfejsu [API kopii zapasowej partycji Wznawiania.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup)

### <a name="difference-between-suspend-and-disable-backups"></a>Różnica między wstrzymania i wyłączania kopii zapasowych
Wyłącz tworzenie kopii zapasowych powinny być używane, gdy kopie zapasowe nie są już wymagane dla określonej aplikacji, usługi lub partycji. Można wywołać wyłączyć żądanie kopii zapasowej wraz z parametrem czystej kopii zapasowej, aby była prawdziwa, co oznaczałoby, że wszystkie istniejące kopie zapasowe są również usuwane. Jednak suspend ma być używany w scenariuszach, w których chcesz tymczasowo wyłączyć kopie zapasowe, na przykład gdy dysk lokalny staje się pełny lub przekazywanie kopii zapasowej nie powiódł się z powodu znanego problemu z siecią itp. 

Chociaż disable może być wywoływana tylko na poziomie, który został wcześniej włączony do tworzenia kopii zapasowych jawnie jednak zawieszenie może być stosowane na dowolnym poziomie, który jest obecnie włączony do tworzenia kopii zapasowych bezpośrednio lub za pośrednictwem dziedziczenia/ hierarchii. Na przykład jeśli kopia zapasowa jest włączona na poziomie aplikacji, można wywołać wyłączyć tylko na poziomie aplikacji jednak zawiesić można wywołać w aplikacji, dowolnej usługi lub partycji w ramach tej aplikacji. 

## <a name="auto-restore-on-data-loss"></a>Automatyczne przywracanie utraty danych
Partycja usługi może utracić dane z powodu nieoczekiwanych awarii. Na przykład dysk dla dwóch z trzech replik dla partycji (w tym repliki podstawowej) zostanie uszkodzony lub wyczyszczony.

Gdy sieci szkieletowej usług wykryje, że `OnDataLossAsync` partycja jest w utracie danych, wywołuje metodę interfejsu na partycji i oczekuje partycji do podjęcia wymaganej akcji, aby wyjść z utraty danych. W tej sytuacji jeśli skuteczne zasady tworzenia `AutoRestoreOnDataLoss` kopii `true` zapasowych na partycji ma ustawioną flagę, a następnie przywracanie zostanie wyzwalane automatycznie przy użyciu najnowszej dostępnej kopii zapasowej dla tej partycji.

## <a name="get-backup-configuration"></a>Pobierz konfigurację kopii zapasowej
Oddzielne interfejsy API są udostępniane w celu uzyskania informacji o konfiguracji kopii zapasowej w _aplikacji,_ _usłudze_i zakresie _partycji._ [Pobierz informacje o konfiguracji kopii zapasowej aplikacji,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo) [Pobierz informacje o konfiguracji kopii zapasowej usługi](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)i Pobierz informacje o konfiguracji kopii zapasowej [partycji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) są te interfejsy API odpowiednio. Głównie te interfejsy API zwracają odpowiednie zasady tworzenia kopii zapasowych, zakres, w którym zasady tworzenia kopii zapasowych są stosowane i szczegóły zawieszenia kopii zapasowej. Poniżej znajduje się krótki opis zwróconych wyników tych interfejsów API.

- Informacje o konfiguracji kopii zapasowej aplikacji: zawiera szczegółowe informacje o zasadach tworzenia kopii zapasowych stosowanych w aplikacji i wszystkich przesiąkniętych zasadach w usługach i partycjach należących do aplikacji. Zawiera również informacje o zawieszeniu dla aplikacji i usług it oraz partycji.

- Informacje o konfiguracji kopii zapasowej usługi: zawiera szczegółowe informacje na temat efektywnych zasad tworzenia kopii zapasowych w serwisie i zakres, w którym ta zasada została zastosowana i wszystkie nadrzędne zasady na swoich partycjach. Zawiera również informacje o zawieszeniu usługi i jej partycji.

- Informacje o konfiguracji kopii zapasowej partycji: zawiera szczegółowe informacje na temat zasad efektywnej kopii zapasowej na partycji i zakresu, w którym ta zasada została zastosowana. Zawiera również informacje o zawieszeniu dla partycji.

## <a name="list-available-backups"></a>Lista dostępnych kopii zapasowych

Dostępne kopie zapasowe można wyświetlić za pomocą interfejsu API Pobierz listę kopii zapasowych. Wynik wywołania interfejsu API zawiera elementy informacji o kopii zapasowej związane ze wszystkimi kopiami zapasowymi dostępnymi w magazynie kopii zapasowych, który jest skonfigurowany w odpowiednich zasadach tworzenia kopii zapasowych. Różne warianty tego interfejsu API są dostarczane do listy dostępnych kopii zapasowych należących do aplikacji, usługi lub partycji. Te interfejsy API obsługują uzyskiwanie _najnowszej dostępnej_ kopii zapasowej wszystkich odpowiednich partycji lub filtrowanie kopii zapasowych na podstawie _daty rozpoczęcia_ i _daty zakończenia._

Te interfejsy API obsługują również podział na strony wyników, gdy parametr _MaxResults_ jest ustawiony na niezerową dodatnią liczbę całkowitą, a następnie interfejs API zwraca maksymalne elementy informacji o kopii zapasowej _MaxResults._ W przypadku, gdy dostępnych jest więcej elementów informacji o kopii zapasowej niż wartość _MaxResults,_ zwracany jest token kontynuacji. Prawidłowy parametr tokenu kontynuacji może służyć do uzyskania następnego zestawu wyników. Gdy prawidłowa wartość tokenu kontynuacji jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Nie token kontynuacji jest uwzględniony w odpowiedzi, gdy wszystkie dostępne wyniki są zwracane.

Poniżej znajduje się krótka informacja o obsługiwanych wariantach.

- [Pobierz listę kopii zapasowych aplikacji:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist)Zwraca listę kopii zapasowych dostępnych dla każdej partycji należącej do danej aplikacji sieci szkieletowej usług.

- [Pobierz listę kopii zapasowych usługi:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist)Zwraca listę kopii zapasowych dostępnych dla każdej partycji należącej do danej usługi sieci szkieletowej usług.
 
- [Pobierz listę kopii zapasowych partycji:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist)Zwraca listę kopii zapasowych dostępnych dla określonej partycji.

## <a name="next-steps"></a>Następne kroki
- [Odwołanie do interfejsu API REST przywracania kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
