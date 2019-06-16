---
title: Opis okresowe konfiguracji kopii zapasowej w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Używać usługi Service Fabric okresowych kopii zapasowych i przywracania funkcji umożliwiających okresowe tworzenia kopii zapasowych danych aplikacji.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAA45B4A-0258-4CB3-A825-7E8F70F28401
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: b1b36ed5197aeb056c70200a49e09cc777d66d0b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237357"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Opis okresowe konfiguracji kopii zapasowej w usłudze Azure Service Fabric

Konfigurowanie okresowych kopii zapasowych niezawodne usługi stanowe lub elementów Reliable Actors składa się z następujących czynności:

1. **Tworzenie zasad tworzenia kopii zapasowych**: W tym kroku co najmniej jedne zasady tworzenia kopii zapasowej są tworzone w zależności od wymagań.

2. **Opcja włączania kopii zapasowych**: W tym kroku należy skojarzyć zasady tworzenia kopii zapasowych utworzonych w **kroku 1** do wymaganych jednostek _aplikacji_, _usługi_, lub _partycji_.

## <a name="create-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych składa się z następujących konfiguracji:

* **Auto przywracania na utratę danych**: Określa, czy wyzwalanie przywracania automatycznie przy użyciu najnowszych dostępnych kopii zapasowych, w przypadku, gdy partycja napotyka utraty danych.

* **Maksymalna liczba przyrostowe kopie zapasowe**: Określa maksymalną liczbę przyrostowe kopie zapasowe do wykonania pomiędzy dwoma pełnych kopii zapasowych. Maksymalna liczba przyrostowych kopii zapasowych Określ górny limit. Pełną kopię zapasową można podjąć przed określoną liczbą przyrostowych kopii zapasowych odbywa się w jednym z następujących warunków

    1. Repliki nigdy nie miało pełnej kopii zapasowej, ponieważ jest on podstawowym.

    2. Niektóre rekordy dziennika, ponieważ ostatnia kopia zapasowa została obcięta.

    3. Repliki są przekazywane MaxAccumulatedBackupLogSizeInMB limit.

* **Harmonogram tworzenia kopii zapasowych**: Czas lub częstotliwości, w którym należy wykonać okresowe kopie zapasowe. Jeden można zaplanować tworzenie kopii zapasowych są cykliczne w określonych przedziałach czasowych lub w stały czas dziennie / co tydzień.

    1. **Na podstawie częstotliwości harmonogramu tworzenia kopii zapasowych**: W przypadku konieczności do wykonania kopii zapasowej danych w ustalonych odstępach czasu, należy używać tego typu harmonogramu. Żądany czas interwału dwóch kolejnych kopii zapasowych jest zdefiniowany w formacie ISO8601. Na podstawie częstotliwości harmonogramu tworzenia kopii zapasowych obsługuje rozpoznawanie interwału do minuty.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Harmonogram tworzenia kopii zapasowych oparte na czasie**: W przypadku konieczności do wykonania kopii zapasowej danych o określonych godzinach dnia lub tygodnia, należy używać tego typu harmonogramu. Typ częstotliwości harmonogramu może być codziennie lub co tydzień.
        1. **_Codzienne_ harmonogram tworzenia kopii zapasowych oparte na czasie**: Można używać tego typu harmonogramu, jeśli identyfikator potrzebę do wykonania kopii zapasowej danych o określonych godzinach dnia. Aby określić, to, ustaw `ScheduleFrequencyType` do _codzienne_; i ustaw `RunTimes` do listy żądany czas w danym dniu, w formacie ISO8601, zostaną zignorowane datę określony czas. Na przykład `0001-01-01T18:00:00` reprezentuje _6:00 PM_ codziennie, bez uwzględnienia data _0001-01-01_. Poniższym przykładzie przedstawiono konfigurację, aby wyzwalacz codzienne wykonywanie kopii zapasowych na _9:00 AM_ i _6:00 PM_ codziennie.

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

        2. **_Co tydzień_ harmonogram tworzenia kopii zapasowych oparte na czasie**: Można używać tego typu harmonogramu, jeśli identyfikator potrzebę do wykonania kopii zapasowej danych o określonych godzinach dnia. Aby określić, to, ustaw `ScheduleFrequencyType` do _tygodniowy_; set `RunDays` listę dni w tygodniu, gdy kopia zapasowa musi być wyzwalane oraz ustawić `RunTimes` do listy żądany czas w danym dniu, w formacie ISO8601, daty oraz godziny zostaną zignorowane. Lista dni tygodnia, kiedy wyzwolić okresowych kopii zapasowych. Poniższym przykładzie przedstawiono konfigurację, aby wyzwalacz codzienne wykonywanie kopii zapasowych na _9:00 AM_ i _6:00 PM_ podczas od poniedziałku do piątku.

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

* **Magazyn kopii zapasowych**: Określa lokalizację do przekazania kopii zapasowych. Magazyn może być albo magazynu obiektów blob platformy Azure lub udziału plików.
    1. **Magazyn obiektów blob platformy Azure**: Ten typ magazynu należy wybrać, gdy jest konieczność wygenerowane tworzenia kopii zapasowych na platformie Azure. Zarówno _autonomiczny_ i _opartych na platformie Azure_ klastry mogą używać tego typu magazynu. Opis tego typu magazynu wymaga parametrów połączenia i nazwę kontenera, w których konieczne jest przekazanie kopii zapasowych. Jeśli kontener o podanej nazwie nie jest dostępna, następnie powstaje podczas przekazywania kopii zapasowej.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Udział plików**: Ten typ magazynu, należy wybrać _autonomiczny_ klastry, gdy jest do przechowywania danych kopii zapasowych w środowisku lokalnym. Opis tego typu magazynu wymaga, których kopie zapasowe, konieczne jest przekazanie ścieżki do udziału plików. Dostęp do udziału plików można skonfigurować przy użyciu jednej z następujących opcji
        1. _Zintegrowane uwierzytelnianie Windows_, gdzie dostęp do udziału plików znajduje się na wszystkich komputerach należących do klastra usługi Service Fabric. W tym przypadku ustaw następujące pola do skonfigurowania _udziału plików_ na podstawie magazynu kopii zapasowych.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Ochronę udziału plików przy użyciu nazwy użytkownika i hasła_, gdzie dostęp do udziału plików znajduje się do określonych użytkowników. Specyfikacja magazyn udziału plików zapewnia również możliwość określenia dodatkowej nazwy użytkownika i hasło pomocnicze, aby podać poświadczenia awaryjne, w przypadku, gdy uwierzytelnianie nie powiedzie się z podstawowej nazwy użytkownika i hasła podstawowego. W tym przypadku ustaw następujące pola do skonfigurowania _udziału plików_ na podstawie magazynu kopii zapasowych.

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
> Upewnij się, że niezawodność magazynu spełnia lub przekracza wymagania niezawodność danych kopii zapasowej.
>

* **Zasady przechowywania**: Określa zasady przechowywania kopii zapasowych w skonfigurowanym magazynie. Tylko podstawowe zasady przechowywania są obsługiwane.
    1. **Zasady przechowywania podstawowe**: Te zasady przechowywania umożliwia zapewnienie magazynu optymalne wykorzystanie, usuwając pliki kopii zapasowej, które są już wymagane. `RetentionDuration` można określić tak, aby ustawić okres, do którego mają wymagane będą przechowywane w magazynie kopii zapasowych. `MinimumNumberOfBackups` jest parametrem opcjonalnym, który może być określony, aby upewnić się, że określoną liczbę kopii zapasowych są zawsze zachowywane niezależnie od `RetentionDuration`. Poniższym przykładzie przedstawiono konfigurację do przechowywania kopii zapasowych dla _10_ dni i nie zezwala na liczbę kopii zapasowych do zejść poniżej _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Włącz okresowe wykonywanie kopii zapasowej
Po zdefiniowaniu zasad tworzenia kopii zapasowej do spełnienia wymagań w zakresie tworzenia kopii zapasowej danych, należy odpowiednio skojarzony albo zasad tworzenia kopii zapasowej _aplikacji_, lub _usługi_, lub _partycji_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarchiczna propagacji zasad kopii zapasowych
W usłudze Service Fabric, są hierarchiczne, jak wyjaśniono w relacji między aplikacji, usług i partycji [model aplikacji](./service-fabric-application-model.md). Zasady tworzenia kopii zapasowej może być skojarzony albo _aplikacji_, _usługi_, lub _partycji_ w hierarchii. Zasady tworzenia kopii zapasowej hierarchicznie propaguje następny poziom. Zakładając, że istnieje tylko jeden zasad tworzenia kopii zapasowej utworzona i skojarzona z _aplikacji_, wszystkie partycje stanowych należących do wszystkich _niezawodne usługi stanowe_ i _elementówReliableActors_ z _aplikacji_ będzie można kopii zapasowej za pomocą zasad tworzenia kopii zapasowej. Czy zasady kopii zapasowych jest skojarzony z _niezawodnej usługi stanowej_, jego wszystkich partycji będzie można kopii zapasowej za pomocą zasad tworzenia kopii zapasowej.

### <a name="overriding-backup-policy"></a>Zastępowanie zasad tworzenia kopii zapasowej
Może to być scenariusz, gdzie tworzenia kopii zapasowych danych z tego samego harmonogramu tworzenia kopii zapasowej jest wymagane dla wszystkich usług aplikacji, z wyjątkiem określonych usług, gdzie jest konieczność aby tworzenie kopii zapasowych danych przy użyciu nowszej częstotliwości harmonogramu lub wykonywania kopii zapasowej do innego konta magazynu lub udział plików. Dla takich scenariuszy Usługa przywracania kopii zapasowej zapewnia możliwość zastąpienia propagowane zasad w zakresie usług i partycji. Gdy zasady kopii zapasowych jest skojarzony w _usługi_ lub _partycji_, zastępuje ona propagowany zasad tworzenia kopii zapasowej, jeśli istnieje.

### <a name="example"></a>Przykład

W tym przykładzie użyto konfiguracji z dwiema aplikacjami _MyApp_A_ i _MyApp_B_. Aplikacja _MyApp_A_ zawiera dwie usługi Reliable Stateful _SvcA1_ & _SvcA3_i jedną usługę Reliable Actor, _ActorA2_. _SvcA1_ zawiera trzy partycje podczas _ActorA2_ i _SvcA3_ zawiera dwie partycje.  Aplikacja _MyApp_B_ zawiera trzy usługi Reliable Stateful, _SvcB1_, _SvcB2_, i _SvcB3_. _SvcB1_ i _SvcB2_ zawiera dwie partycje podczas _SvcB3_ zawiera trzy partycje.

Przyjęto założenie, że aplikacje te dane kopii zapasowej ma następujące wymagania

1. MyApp_A
    1. Utwórz tworzenia codziennej kopii zapasowej danych dla wszystkich partycji wszystkich _Reliable Stateful services_ i _elementów Reliable Actors_ należące do aplikacji. Przekaż dane kopii zapasowej do lokalizacji _BackupStore1_.

    2. Jedna z usług, _SvcA3_, wymaga danych kopii zapasowej co godzinę.

    3. Rozmiar danych w partycji _SvcA1_P2_ jest większa niż oczekiwano i jego dane kopii zapasowej powinny być przechowywane w innej lokalizacji magazynu _BackupStore2_.

2. MyApp_B
    1. Tworzenie kopii zapasowych danych każdą niedzielę o godzinie 8:00:00 do wszystkich partycji _SvcB1_ usługi. Przekaż dane kopii zapasowej do lokalizacji _BackupStore1_.

    2. Tworzenie kopii zapasowych danych codziennie o godzinie 8:00:00 dla partycji _SvcB2_P1_. Przekaż dane kopii zapasowej do lokalizacji _BackupStore1_.

Aby spełnić wymagania dotyczące tworzenia kopii zapasowych tych danych, są tworzone zasady tworzenia kopii zapasowych BP_1 BP_5 i kopia zapasowa jest włączona w następujący sposób.
1. MyApp_A
    1. Tworzenie zasad kopii zapasowych _BP_1_, na podstawie częstotliwości harmonogramu tworzenia kopii zapasowych, gdzie parametr frequency ma wartość 24 godzin. i Magazyn skonfigurowane do korzystania z lokalizacji magazynu kopii zapasowych _BackupStore1_. Ta zasada jest włączona dla aplikacji _MyApp_A_ przy użyciu [Włącz kopię zapasową aplikacji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) interfejsu API. Ta akcja powoduje włączenie tworzenia kopii zapasowych danych przy użyciu zasad tworzenia kopii zapasowej _BP_1_ dla wszystkich partycji _Reliable Stateful services_ i _elementów Reliable Actors_ należących do aplikacji  _MyApp_A_.

    2. Tworzenie zasad kopii zapasowych _BP_2_, na podstawie częstotliwości harmonogramu tworzenia kopii zapasowych, gdzie parametr frequency ma wartość 1 godz. i Magazyn skonfigurowane do korzystania z lokalizacji magazynu kopii zapasowych _BackupStore1_. Włącz te zasady usługi _SvcA3_ przy użyciu [Włącz wykonywanie kopii zapasowej usługi](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) interfejsu API. Ta akcja zastępuje zasadę propagowany _BP_1_ przez jawnie włączone zasady tworzenia kopii zapasowej _BP_2_ wszystkich partycji usługi _SvcA3_ prowadzących do tworzenia kopii zapasowych danych przy użyciu kopii zapasowej zasady _BP_2_ dla tych partycji.

    3. Tworzenie zasad kopii zapasowych _BP_3_, na podstawie częstotliwości harmonogramu tworzenia kopii zapasowych, gdzie parametr frequency ma wartość 24 godzin. i Magazyn skonfigurowane do korzystania z lokalizacji magazynu kopii zapasowych _BackupStore2_. Włącz te zasady dla partycji _SvcA1_P2_ przy użyciu [Włącz wykonywanie kopii zapasowej w partycji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) interfejsu API. Ta akcja zastępuje zasadę propagowany _BP_1_ przez jawnie włączone zasady tworzenia kopii zapasowej _BP_3_ dla partycji _SvcA1_P2_.

2. MyApp_B
    1. Tworzenie zasad kopii zapasowych _BP_4_za pomocą harmonogramu tworzenia kopii zapasowych oparte na czasie których typ częstotliwości harmonogramu ma wartość co tydzień, dni wykonywania jest ustawiony do niedzieli, a czas wykonywania jest równa 8:00:00. Magazyn skonfigurowany do korzystania z lokalizacji magazynu kopii zapasowych _BackupStore1_. Włącz te zasady usługi _SvcB1_ przy użyciu [Włącz wykonywanie kopii zapasowej usługi](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) interfejsu API. Ta akcja powoduje włączenie tworzenia kopii zapasowych danych przy użyciu zasad tworzenia kopii zapasowej _BP_4_ wszystkich partycji usługi _SvcB1_.

    2. Tworzenie zasad kopii zapasowych _BP_5_, za pomocą opartego na czasie harmonogram tworzenia kopii zapasowych, gdzie typ częstotliwości harmonogramu jest ustawiona na codzienne i czas wykonywania jest równa 8:00:00. Magazyn skonfigurowany do korzystania z lokalizacji magazynu kopii zapasowych _BackupStore1_. Włącz te zasady dla partycji _SvcB2_P1_ przy użyciu [Włącz wykonywanie kopii zapasowej w partycji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) interfejsu API. Ta akcja powoduje włączenie tworzenia kopii zapasowych danych przy użyciu zasad tworzenia kopii zapasowej _BP_5_ dla partycji _SvcB2_P1_.

Poniższy diagram przedstawia jawnie włączone zasady tworzenia kopii zapasowych i propagowane zasady tworzenia kopii zapasowych.

![Usługa Service Fabric aplikacji hierarchii][0]

## <a name="disable-backup"></a>Wyłącz kopię zapasową
Zasady tworzenia kopii zapasowych można wyłączyć, gdy nie ma potrzeby tworzenia kopii zapasowej danych. Zasady włączone na kopii zapasowych _aplikacji_ można wyłączyć tylko w tym samym _aplikacji_ przy użyciu [Wyłącz kopię zapasową aplikacji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) interfejsu API, zasady tworzenia kopii zapasowych, włączone na _usługi_ może zostać wyłączony w tym samym _usługi_ przy użyciu [Wyłącz kopii zapasowej usługi](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) interfejsu API i włączone na zasady tworzenia kopii zapasowej _partycji_ może zostać wyłączony w tym samym _partycji_ przy użyciu [Wyłącz kopię zapasową partycji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) interfejsu API.

* Wyłączanie zasad tworzenia kopii zapasowej dla _aplikacji_ zatrzymuje wszystkie kopie zapasowe danych okresowe dzieje się w wyniku propagacji zasad tworzenia kopii zapasowej na partycje usługi Reliable Stateful lub partycje Reliable Actor.

* Wyłączanie zasad tworzenia kopii zapasowej dla _usługi_ zatrzymuje wszystkie dane kopie zapasowe wykonywane w wyniku propagacji tych zasad kopii zapasowych do partycji _usługi_.

* Wyłączanie zasad tworzenia kopii zapasowej dla _partycji_ zatrzymuje wszystkie dane kopii zapasowej dzieje się z powodu zasad kopii zapasowych w partycji.

* Podczas wyłączania kopii zapasowej entity(application/service/partition) `CleanBackup` można ustawić _true_ można usunąć wszystkie kopie zapasowe w skonfigurowanym magazynie.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Wstrzymania i wznowienia tworzenia kopii zapasowych
Niektóre sytuacji mogą wymagać tymczasowe zawieszenie okresowe tworzenie kopii zapasowych danych. W takiej sytuacji, w zależności od zapotrzebowania, wstrzymywanie kopii zapasowej można używać interfejsu API _aplikacji_, _usługi_, lub _partycji_. Okresowe zawieszenia kopii zapasowej jest przechodnia poddrzewo hierarchii aplikacji od punktu, który jest stosowany. 

* Kiedy zawieszenie jest stosowana na _aplikacji_ przy użyciu [wstrzymania aplikacji kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) interfejsu API, a następnie wszystkie usługi i partycji w ramach tej aplikacji, które są wstrzymane do okresowego wykonywania kopii zapasowych danych.

* Kiedy zawieszenie jest stosowana na _usługi_ przy użyciu [wstrzymania usługi tworzenia kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) interfejsu API, a następnie wszystkich partycji w ramach tej usługi są wstrzymywane do okresowego wykonywania kopii zapasowych danych.

* Kiedy zawieszenie jest stosowana na _partycji_ przy użyciu [zawiesić kopii zapasowej z partycji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) interfejsu API, a następnie ją wstrzymuje partycji w ramach tej usługi są wstrzymywane do okresowego wykonywania kopii zapasowych danych.

Po potrzebę zawieszenia dane kopii zapasowej można przywrócić przy użyciu odpowiednich wznowienia tworzenia kopii zapasowej interfejsu API. Okresowe backup musi zostać wznowiony w samym _aplikacji_, _usługi_, lub _partycji_ gdzie zostało wstrzymane.

* Jeśli zawieszenia została zastosowana na _aplikacji_, a następnie wznowić, przy użyciu [aplikacji Wznów tworzenie kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) interfejsu API. 

* Jeśli zawieszenia została zastosowana w _usługi_, a następnie wznowić, przy użyciu [usługi Wznów tworzenie kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup) interfejsu API.

* Jeśli zawieszenia została zastosowana na _partycji_, a następnie wznowić, przy użyciu [partycji Wznów tworzenie kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup) interfejsu API.

### <a name="difference-between-suspend-and-disable-backups"></a>Różnica między wstrzymaniem i Wyłącz kopii zapasowych
Wyłącz kopię zapasową powinny być używane, gdy kopie zapasowe nie są wymagane dla określonej aplikacji, usługi lub partycji. Jeden wywołać żądania utworzenia kopii zapasowej Wyłącz wraz z parametru z czystej kopii zapasowych jako wartość true oznacza, że wszystkie istniejące kopie zapasowe są także usuwane. Jednakże zawiesić ma być używany w scenariuszach, gdzie jeden chce wyłączyć tworzenie kopii zapasowych tymczasowo takich jak po zapełnieniu dysku lokalnego lub przekazywanie kopii zapasowej kończy się niepowodzeniem z powodu problemu z znanej sieci itp. 

Podczas Wyłącz może być wywołana tylko na poziomie której była wcześniejsza aktywne do utworzenia kopii zapasowej jawnie jednak zawieszenia mogą być stosowane na dowolnym poziomie, który jest obecnie włączona dla kopii zapasowej albo bezpośrednio lub przez dziedziczenie / hierarchii. Na przykład, jeśli kopia zapasowa jest włączona na poziomie aplikacji, jednego wywołania wyłączyć tylko na poziomie aplikacji zawiesić jednak może być wywoływana na aplikacji, usług lub partycji w ramach tej aplikacji. 

## <a name="auto-restore-on-data-loss"></a>Automatycznego przywracania na utratę danych
Service partition może spowodować utratę danych z powodu nieoczekiwanych awarii. Na przykład dysk dla dwóch spośród trzech replik partycji (łącznie z repliką podstawową) pobiera uszkodzony lub wyczyszczone.

Gdy Usługa Service Fabric wykrywa, czy partycja ma utratę danych, wywołuje ono `OnDataLossAsync` interfejs metody w partycji i oczekuje, że partycji, aby wykonać niezbędne czynności, aby wyjść z utratą danych. W takiej sytuacji efektywnych zasad tworzenia kopii zapasowej na partycji ma `AutoRestoreOnDataLoss` flaga jest ustawiona na `true` , a następnie przywracania pobiera wyzwolona automatycznie za pomocą najnowszych dostępnych kopii zapasowych dla tej partycji.

## <a name="get-backup-configuration"></a>Pobierz konfigurację kopii zapasowej
Oddzielnych interfejsów API udostępnionych można pobrać informacji o konfiguracji kopii zapasowej w _aplikacji_, _usługi_, i _partycji_ zakresu. [Uzyskaj informacje o konfiguracji kopii zapasowej aplikacji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [Get Info konfiguracji kopii zapasowej usługi](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo), i [Get Info konfiguracji kopii zapasowej partycji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) są te interfejsy API odpowiednio. Przede wszystkim te interfejsy API zwracają odpowiednich zasad kopii zapasowych, zakres, jaką zasad tworzenia kopii zapasowej są szczegóły zawieszenia zastosowane i wykonywania kopii zapasowych. Poniżej przedstawiono krótki opis zwracane wyniki tych interfejsów API.

- Informacje o konfiguracji kopii zapasowej aplikacji: szczegółowe zasady tworzenia kopii zapasowych stosowane w aplikacji i wszystkich zasad nadmiernie informacji usług i partycji należące do aplikacji. Zawiera on również informacje zawieszenie aplikacji i usług i dzieli na partycje.

- Usługi informacje o konfiguracji kopii zapasowej: zawiera szczegółowe informacje o efektywnych zasad tworzenia kopii zapasowej na usługę i zakresu, na której zastosowano te zasady i wszystkie zasady nadmiernie informacji na jego partycji. Zawiera także informacje zawieszenie usługi i jego partycji.

- Partycjonowanie informacje o konfiguracji kopii zapasowej: zawiera informacje o efektywnych zasad tworzenia kopii zapasowej na partycji oraz zakres, w którym ta zasada została zastosowana. Zawiera także informacje zawieszenia dla partycji.

## <a name="list-available-backups"></a>Lista dostępnych kopii zapasowych

Dostępnych kopii zapasowych można wystawić za pomocą uzyskać API listy kopii zapasowej. Wynik wywołania interfejsu API zawiera elementy kopii zapasowej informacje związane z wszystkich kopii zapasowych dostępnych w magazynie kopii zapasowych jest skonfigurowana w odpowiednich zasad tworzenia kopii zapasowej. Podano różne odmiany tego interfejsu API dostępnych kopii zapasowych listy należące do aplikacji, usług lub partycji. Te interfejsy API obsługują pobierania _najnowsze_ dostępna kopia zapasowa wszystkich odpowiednich partycji lub filtrowanie kopii zapasowych na podstawie _Data rozpoczęcia_ i _Data zakończenia_.

Te interfejsy API obsługują również stronicowanie wyników, gdy _MaxResults_ ustawiona jest różna od zera dodatnia liczba całkowita, a następnie interfejs API zwraca maksymalny _MaxResults_ kopii zapasowych elementów informacji. W przypadku większej liczby elementów kopii zapasowej informacje są dostępne niż _MaxResults_ wartość zwracana jest token kontynuacji. Nieprawidłowa kontynuacji, można użyć parametru tokenu można pobrać następnej zestaw wyników. Gdy wartość tokenu kontynuacji prawidłowe jest przekazywany do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Brak tokenu kontynuacji jest uwzględniane w odpowiedzi zwracane są wszystkie dostępne wyniki.

Poniżej przedstawiono krótki opis informacji o obsługiwanych wariantów.

- [Pobieranie listy kopii zapasowej aplikacji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): Zwraca listę dostępnych dla każdej partycji należących do danej aplikacji usługi Service Fabric kopii zapasowych.

- [Pobieranie listy kopii zapasowych usługi](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist): Zwraca listę dostępnych dla każdej partycji należących do danej usługi Service Fabric kopii zapasowych.
 
- [Pobieranie listy kopii zapasowej partycji](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): Zwraca listę dostępnych dla określonej partycji kopii zapasowych.

## <a name="next-steps"></a>Kolejne kroki
- [Dokumentacja interfejsu API REST przywracania kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/BackupPolicyAssociationExample.png
