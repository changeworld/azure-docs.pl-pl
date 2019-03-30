---
title: Usługa Service Fabric w kopii zapasowej i przywracania | Dokumentacja firmy Microsoft
description: Dokumentacji koncepcyjnego dla usługi Service Fabric w kopii zapasowej i przywracania
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: cd40f59cfa7846911c68206c3bc1e85a770b0fcc
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670132"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Kopia zapasowa i przywracanie usług Reliable Services i Reliable Actors
Usługa Azure Service Fabric to platforma wysokiej dostępności, która replikuje stanu w wielu węzłach, aby zachować ten wysokiej dostępności.  W związku z tym nawet w przypadku awarii jednego węzła w klastrze, usługi nadal dostępne. Tę nadmiarowość wbudowane dostarczonego przez platformę, może być wystarczające dla niektórych, w niektórych przypadkach pożądane jest usługi do tworzenia kopii zapasowych (do magazynu zewnętrznego).

> [!NOTE]
> Podstawowe znaczenie ma kopii zapasowej i przywracanie danych (i przetestować, że działa zgodnie z oczekiwaniami), dzięki czemu można odzyskać z utratą danych.
> 

> [!NOTE]
> Firma Microsoft zaleca, aby użyć [okresowych kopii zapasowych i przywracania](service-fabric-backuprestoreservice-quickstart-azurecluster.md) konfigurowania kopii zapasowej danych Reliable Stateful services i Reliable Actors. 
> 


Na przykład usługa warto tworzyć kopie zapasowe danych w celu zapewnienia ochrony przed w następujących scenariuszach:

- W przypadku trwałą utratę cały klaster usługi Service Fabric.
- Trwałą utratę większości replik partycji usługi
- Błędy administracyjne, według której stan przypadkowo pobiera usunięty lub uszkodzony. Na przykład to może się zdarzyć, jeśli administrator z uprawnieniami wystarczające błędnie spowoduje usunięcie usługi.
- Błędy w usłudze, które powodują uszkodzenie danych. Na przykład to może się zdarzyć, gdy uaktualnianie kodu usługi rozpoczyna się zapisywanie uszkodzonych danych w niezawodnej kolekcji. W takim przypadku zarówno kod, jak i danych może być konieczne można przywrócić do poprzedniego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodną mieć przetwarzanie danych dla analizę biznesową, która miejsce osobno od usługi, która generuje dane w trybie offline.

Funkcja Backup/Restore umożliwia usług oparty na niezawodne API usług w celu tworzenia i przywracania kopii zapasowych. Kopii zapasowej interfejsów API dostarczonego przez platformę Zezwalaj na kopie zapasowe stanu partycji usługi, bez blokowania odczytu lub zapisu. Przywracanie interfejsy API umożliwiają można przywrócić z kopii zapasowej wybrany stan partycji usługi.

## <a name="types-of-backup"></a>Typy kopii zapasowych
Dostępne są dwie opcje tworzenia kopii zapasowej: Pełne i przyrostowe.
Pełna kopia zapasowa jest kopii zapasowej, która zawiera wszystkie dane, które są wymagane, aby odtworzyć stan repliki: punkty kontrolne i wszystkie rekordy dziennika.
Ponieważ ma ona punkty kontrolne i dziennik, pełnej kopii zapasowej można przywrócić samodzielnie.

Pojawi się problem z pełnymi kopiami zapasowymi, gdy punkty kontrolne są duże.
Na przykład repliki, która ma 16 GB pamięci stanu będzie miał punktów kontrolnych, które Dodaj maksymalnie około 16 GB.
Jeśli będziemy mieć cel punktu odzyskiwania w pięć minut, replika musi utworzyć kopię zapasową co pięć minut.
Zawsze tworzy kopię zapasową należy go skopiować 16 GB pamięci punkty kontrolne, oprócz 50 MB (można skonfigurować przy użyciu `CheckpointThresholdInMB`) pomagają w zrealizowaniu dzienników.

![Przykład pełnej kopii zapasowej.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Rozwiązanie tego problemu jest przyrostowych kopii zapasowych, których kopię zapasową zawiera tylko rekordy dziennika zmienione od czasu ostatniej kopii zapasowej.

![Przykład: przyrostowych kopii zapasowej.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Ponieważ przyrostowe kopie zapasowe są tylko zmiany od ostatniej kopii zapasowej (nie obejmuje punktów kontrolnych), mają zwykle szybsze, ale nie można przywrócić własnych.
Aby przywrócić przyrostowej kopii zapasowej, wymagana jest cały łańcuch kopii zapasowych.
Łańcuch kopii zapasowych jest łańcuch kopii zapasowych, rozpoczynając od pełnej kopii zapasowej i następuje liczba ciągłych przyrostowych kopii zapasowych.

## <a name="backup-reliable-services"></a>Kopii zapasowych usług Reliable Services
Tworzenie usługi ma pełną kontrolę nad kiedy się tworzenie kopii zapasowych i przechowywania kopii zapasowych.

Aby rozpocząć tworzenie kopii zapasowej, usługa musi wywołać funkcji odziedziczonego członka `BackupAsync`.  
Tworzenie kopii zapasowych jest możliwe tylko z replikami podstawowymi i wymagają one stan zapisu, aby otrzymać.

Jak pokazano poniżej, `BackupAsync` przyjmuje `BackupDescription` obiektu, w którym można określić pełną lub przyrostową kopię zapasową, a także funkcję wywołania zwrotnego, `Func<< BackupInfo, CancellationToken, Task<bool>>>` , wywoływany, gdy folder kopii zapasowej został utworzony lokalnie i jest gotowa do przeniesienia do niektórych magazynu zewnętrznego.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Żądanie do wykonania przyrostowej kopii zapasowej może zakończyć się niepowodzeniem z `FabricMissingFullBackupException`. Ten wyjątek wskazuje, że jeden z następujących czynności wykonywane:

- repliki nigdy nie miało pełnej kopii zapasowej, ponieważ jest on podstawowym,
- Niektóre rekordy dziennika, ponieważ ostatnia kopia zapasowa została obcięta lub
- Replika przekazywane `MaxAccumulatedBackupLogSizeInMB` limit.

Użytkownicy mogą zwiększyć prawdopodobieństwo jest w stanie przyrostowe kopie zapasowe, konfigurując `MinLogSizeInMB` lub `TruncationThresholdFactor`.
Te zwiększenia wartości zwiększa na użycie dysku repliki.
Aby uzyskać więcej informacji, zobacz [konfiguracji usług Reliable Services](service-fabric-reliable-services-configuration.md)

`BackupInfo` zawiera informacje dotyczące kopii zapasowych, łącznie z lokalizacji folderu, w którym środowisko uruchomieniowe jest przechowywana kopia zapasowa (`BackupInfo.Directory`). Funkcja wywołania zwrotnego można przenieść `BackupInfo.Directory` do zewnętrznego magazynu lub w innej lokalizacji.  Ta funkcja zwraca również wartość logiczna, która wskazuje, czy był w stanie pomyślnie przenieść folderu kopii zapasowej do lokalizacji docelowej.

Poniższy kod przedstawia sposób, w jaki `BackupCallbackAsync` metoda może służyć do przekazywania kopii zapasowej do usługi Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

W powyższym przykładzie `ExternalBackupStore` jest przykładową klasę, która jest używana do interfejsu z usługi Azure Blob storage i `UploadBackupFolderAsync` to metoda, która kompresuje folder i umieszcza je w magazynie obiektów Blob platformy Azure.

Należy pamiętać, że:

  - Może istnieć tylko jedna operacja tworzenia kopii zapasowej śledząc na replikę w danym momencie. Więcej niż jeden `BackupAsync` rozmowy w czasie spowoduje zgłoszenie `FabricBackupInProgressException` ograniczyć porządkowych tworzenia kopii zapasowych do jednego.
  - Jeśli replika ulegnie awarii za pośrednictwem, gdy tworzenie kopii zapasowej jest w toku, tworzenie kopii zapasowej może nie zostały zakończone. Dlatego po zakończeniu trybu failover jest odpowiedzialność za usługi, aby ponownie uruchomić tworzenie kopii zapasowej za pomocą wywołania `BackupAsync` zgodnie z potrzebami.

## <a name="restore-reliable-services"></a>Przywracanie usług Reliable Services
Ogólnie rzecz biorąc przypadki, gdy może być konieczne wykonanie operacji przywracania dzielą się na jednej z tych kategorii:

  - Utracone dane partycji usługi. Na przykład dysk dla dwóch spośród trzech replik partycji (łącznie z repliką podstawową) pobiera uszkodzony lub wyczyszczone. Nowe podstawowy może być konieczne przywrócenie danych z kopii zapasowej.
  - Całą usługę zostaną utracone. Na przykład administrator usuwa całą usługę i w związku z tym usługi i dane mają być przywracane.
  - Usługa zreplikowanych danych zawiera błędy aplikacji (na przykład z powodu błędu aplikacji). W tym przypadku usługa ma uaktualnione lub przywrócone do usunięcia przyczyną uszkodzenia i -uszkodzone dane mają zostać przywrócone.

Wiele metod są możliwe, firma Microsoft oferuje kilka przykładów na temat korzystania z `RestoreAsync` odzyskać z powyższych scenariuszy.

## <a name="partition-data-loss-in-reliable-services"></a>Utrata danych partycji usług Reliable Services
W tym przypadku środowisko uruchomieniowe może automatycznie wykryć utraty danych i wywołać `OnDataLossAsync` interfejsu API.

Tworzenie usługi musi wykonać następujące polecenie, aby odzyskać:

  - Zastąp metodę wirtualną klasę bazową `OnDataLossAsync`.
  - Znajdź najnowszej kopii zapasowej w lokalizacji zewnętrznej, który zawiera usługi tworzenia kopii zapasowych.
  - Pobieranie najnowszej kopii zapasowej (i zdekompresować kopii zapasowej do folderu kopii zapasowej, jeśli został on skompresowane).
  - `OnDataLossAsync` Metoda zapewnia `RestoreContext`. Wywołaj `RestoreAsync` interfejsu API w podanych `RestoreContext`.
  - Zwraca wartość true, jeśli przywracanie zakończyło się sukcesem.

Poniżej przedstawiono przykład implementacji `OnDataLossAsync` metody:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` przekazana do `RestoreContext.RestoreAsync` wywołanie zawiera składową o nazwie `BackupFolderPath`.
Podczas przywracania pojedynczego pełną kopię zapasową, to `BackupFolderPath` powinna być ustawiona na ścieżce lokalnej folderu, który zawiera Twoje pełnej kopii zapasowej.
Podczas przywracania pełnej kopii zapasowej i liczba przyrostowych kopii zapasowych, `BackupFolderPath` powinna być ustawiona na ścieżce lokalnej folderu, który zawiera nie tylko pełna kopia zapasowa, ale także wszystkich przyrostowych kopii zapasowych.
`RestoreAsync` Wywołanie może zgłosić `FabricMissingFullBackupException` Jeśli `BackupFolderPath` podane nie zawiera pełnej kopii zapasowej.
Może również zgłosić `ArgumentException` Jeśli `BackupFolderPath` został przerwany łańcuch przyrostowych kopii zapasowych.
Na przykład, jeśli zawiera on pełnej kopii zapasowej pierwszy przyrostowych i trzeci przyrostowej kopii zapasowej, ale nie drugiej przyrostowej kopii zapasowej.

> [!NOTE]
> Domyślnie ustawiono RestorePolicy bezpieczny.  Oznacza to, że `RestoreAsync` API zakończy się niepowodzeniem z ArgumentException jeśli wykryje, że folder kopii zapasowej zawiera stanu, który jest starszy niż lub równa stanu zawarte w tej replice.  `RestorePolicy.Force` można pominąć to sprawdzanie bezpieczeństwa. To jest określony jako część `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Usunięty lub utracony usługi
Jeśli usługa została usunięta, należy najpierw ponownie utworzyć usługę przed przywróceniem danych.  Jest ważne utworzyć usługę za pomocą tej samej konfiguracji, na przykład schemat, partycji, dzięki czemu dane można przywrócić bezproblemowo.  Gdy usługa działa interfejs API, aby przywrócić dane (`OnDataLossAsync` powyżej) musi być wywoływane na każdej partycji tej usługi. Jeden ze sposobów osiągnięcia tego polega na użyciu [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) na każdej partycji.  

Od tej pory implementacja jest taka sama, jak w powyższym scenariuszu. Każda partycja musi przywrócenia najnowszej odpowiednie kopii zapasowej z magazynu zewnętrznego. Jedno zastrzeżenie: to, że Identyfikatora partycji może teraz zmieniono, ponieważ środowisko uruchomieniowe tworzy dynamicznie identyfikatorów partycji. W związku z tym usługa musi przechowywać nazwy partycji odpowiednie informacje i usługi do identyfikowania poprawne najnowszej kopii zapasowej można przywrócić z dla każdej partycji.

> [!NOTE]
> Nie zaleca się używania `FabricClient.ServiceManager.InvokeDataLossAsync` na każdej partycji można przywrócić całą usługę od czasu, który może doprowadzić do uszkodzenia usługi klastra.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikacja danych aplikacji w uszkodzona
Jeśli uaktualnienie nowo wdrożonych aplikacji zawiera usterkę, która może spowodować uszkodzenie danych. Na przykład uaktualnienie aplikacji może uruchomić aktualizacji każdego rekordu numeru telefonu w niezawodnym słowniku za pomocą nieprawidłowy numer kierunkowy.  W tym przypadku nieprawidłowych numerach telefonów będą replikowane, ponieważ usługa Service Fabric nie ma informacji o rodzaju danych, które są przechowywane.

Pierwszą rzeczą, którą należy wykonać po wykryć takie rażące usterkę, która powoduje uszkodzenie danych jest blokowanie usługi na poziomie aplikacji, a jeśli to możliwe, Uaktualnij do wersji kodu aplikacji, który nie ma błędów.  Jednak mimo kodu usługi został rozwiązany, dane nadal może być uszkodzony i dlatego danych może być konieczne przywrócenie.  W takich przypadkach może nie być wystarczające do przywrócenia najnowszej kopii zapasowej, ponieważ najnowszej kopii zapasowych również może być uszkodzony.  W związku z tym należy znaleźć ostatniej kopii zapasowej, utworzonej przed stało się uszkodzone dane.

Jeśli nie masz pewności, których kopie zapasowe są uszkodzone, można wdrożyć nowy klaster usługi Service Fabric i przywracanie kopii zapasowych tak samo jak powyżej partycje "Usunięty lub utracony usługi" scenariusza.  Dla każdej partycji rozpocząć przywracanie kopii zapasowych od najnowszych do najmniejszej. Po odnalezieniu kopii zapasowej, która nie ma uszkodzenie Przenieś/Usuń wszystkie kopie zapasowe tej partycji, które były nowsza (od tej kopii zapasowej). Powtórz ten proces dla każdej partycji. Teraz, gdy `OnDataLossAsync` nazywa się na partycji w klastrze produkcyjnym ostatniej kopii zapasowej znaleziono w magazynie zewnętrznych zostanie pobrana przez proces powyżej.

Teraz kroki opisane w "usunięty lub utracony usługi" sekcji może służyć do przywrócenia stanu usługi do stanu przed pracowały kodu uszkodzony stan.

Należy pamiętać, że:

  - Po przywróceniu, istnieje możliwość utworzenia kopii zapasowej jest starsza niż stan partycji, zanim dane zostały utracone. W związku z tym należy przywrócić tylko w ostateczności odzyskać jak najwięcej danych.
  - Ciąg, który reprezentuje ścieżkę do folderu kopii zapasowej i ścieżki plików w folderze kopii zapasowej może być większa niż 255 znaków, w zależności od tego, czy ścieżka FabricDataRoot i długość nazwy typu aplikacji. Może to spowodować niektóre metody .NET, takie jak `Directory.Move`, aby zgłosić `PathTooLongException` wyjątku. Jeden obejście polega na bezpośrednio wywoływać interfejsy API kernel32, takich jak `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Tworzenie kopii zapasowej i przywracanie elementów Reliable Actors


Reliable Actors Framework jest oparty na usług Reliable Services. ActorService, który hostuje actor(s) jest stanowej usługi reliable service. Dzięki temu wszystkich kopii zapasowych i przywracania funkcji dostępnych usług Reliable Services jest również dostępny do elementów Reliable Actors (z wyjątkiem zachowań, które są właściwe dla dostawcy stanu). Ponieważ tworzenie kopii zapasowych zostaną wykonane na podstawie poszczególnych partycji, Stany wszystkich podmiotów w tym partycji będą tworzone kopie zapasowe (i przywracania są podobne i nastąpi na podstawie poszczególnych partycji). Do wykonywania/przywracania kopii zapasowej, właściciel usługi powinien utworzyć klasę usługi aktora niestandardowej, która pochodzi od klasy ActorService a następnie wykonaj/przywracania kopii zapasowej podobne do usług Reliable Services zgodnie z powyższym opisem w poprzedniej sekcji.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Podczas tworzenia klasy usługi aktora niestandardowe, należy zarejestrować, także podczas rejestrowania, aktor.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Domyślny dostawca stanu struktury Reliable actors jest `KvsActorStateProvider`. Przyrostowa kopia zapasowa nie jest włączona domyślnie `KvsActorStateProvider`. Przyrostowe kopie zapasowe można włączyć poprzez utworzenie `KvsActorStateProvider` odpowiednie ustawienie w jego konstruktorze i przekazanie jej do konstruktora ActorService, jak pokazano w poniższym fragmencie kodu:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Po włączeniu przyrostowej kopii zapasowej przyrostowej kopii zapasowej może zakończyć się niepowodzeniem z FabricMissingFullBackupException dla jednego z następujących powodów i konieczne będzie wykonaj pełną kopię zapasową przed przełączeniem przyrostowych kopii zapasowych:

  - Replika ma nigdy niepobrane pełnej kopii zapasowej, ponieważ stały się podstawowe.
  - Niektóre rekordy dziennika został obcięty, ponieważ ostatnia kopia zapasowa została wykonana.

Po włączeniu przyrostowej kopii zapasowej `KvsActorStateProvider` używają cyklicznego buforu do zarządzania jej rekordów dziennika bazy danych i okresowo obcina go. Jeśli kopia zapasowa nie zostanie podjęta przez użytkownika w danym okresie 45 minut, system automatycznie obcina rekordów dziennika. Ten interwał można skonfigurować, określając `logTruncationIntervalInMinutes` w `KvsActorStateProvider` konstruktora (podobnie jak podczas włączania obsługi funkcji przyrostowej kopii zapasowej). Również uzyskać obcięte rekordów dziennika, jeśli replika podstawowa musi stworzyć innej repliki, wysyłając swoje dane.

W trakcie przywracania z kopii zapasowej łańcucha, podobne do usług Reliable Services, BackupFolderPath powinna zawierać podkatalogów z podkatalogu jeden zawierający pełnej kopii zapasowej i w innych podkatalogach zawierający przyrostowych kopii zapasowych. Interfejsu API przywracania zgłosi FabricException przy użyciu odpowiedniego komunikatu o błędzie w przypadku niepowodzenia weryfikacji łańcuch kopii zapasowych. 

> [!NOTE]
> `KvsActorStateProvider` obecnie ignoruje opcję RestorePolicy.Safe. Obsługa tej funkcji jest planowana w kolejnej wersji.
> 

## <a name="testing-back-up-and-restore"></a>Testowanie kopii zapasowych i przywracania
Należy się upewnić, że krytyczne dane jest tworzona kopia zapasowa i może zostać przywrócony z. Można to zrobić, wywołując `Start-ServiceFabricPartitionDataLoss` polecenia cmdlet programu PowerShell, który może powodować utratę danych w określonej partycji, aby sprawdzić, czy dane kopii zapasowych i przywrócić funkcjonalność dla usługi działa zgodnie z oczekiwaniami.  Jest również możliwe programowo wywoływanie utraty danych i przywrócić z tego zdarzenia, jak również.

> [!NOTE]
> Można znaleźć przykład implementacji kopia zapasowa i przywrócenia jej funkcjonalności w aplikacji odwołanie sieci Web w witrynie GitHub. Zajrzyj `Inventory.Service` usługi, aby uzyskać więcej informacji.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Kulisy: szczegółowe informacje na temat tworzenia kopii zapasowych i przywracania
Poniżej przedstawiono niektóre szczegółowe informacje na temat tworzenia kopii zapasowych i przywracania.

### <a name="backup"></a>Backup
Reliable State Manager pozwala tworzyć kopie zapasowe spójne bez blokowania wszelkie odczytu lub operacji zapisu. Aby to zrobić, jego korzysta z mechanizmu stanu trwałego punktu kontrolnego i dziennika.  Reliable State Manager trwa rozmyte (uproszczonego) punkty kontrolne w pewnych punktach, aby zwolnić ciśnienia z dziennika transakcji i skrócić czas odzyskiwania.  Gdy `BackupAsync` jest wywoływana, Reliable State Manager powoduje, że wszystkie obiekty niezawodne do skopiowania ich najnowszych plików punktu kontrolnego w lokalnym folderze kopii zapasowej.  Następnie Reliable State Manager kopiuje wszystkie rekordy dziennika rozpoczyna się od wskaźnika"start" jest najnowszy rekord dziennika do folderu kopii zapasowej.  Ponieważ wszystkie rekordy dziennika do jest najnowszy rekord dziennika są uwzględnione w kopii zapasowej i Reliable State Manager zachowuje rejestrowanie zapisu z wyprzedzeniem, Reliable State Manager gwarantuje, czy wszystkie transakcje, są zatwierdzone (`CommitAsync` zwrócił się pomyślnie ) są uwzględnione w kopii zapasowej.

Każdej transakcji, które zatwierdza po `BackupAsync` została wywołana maja lub może nie być w kopii zapasowej.  Gdy lokalnego folderu kopii zapasowej została wypełniona przez platformę (oznacza to, że lokalna kopia zapasowa zostanie zakończony przez środowisko uruchomieniowe), usługi tworzenia kopii zapasowej wywołanie zwrotne jest wywoływana.  To wywołanie zwrotne jest odpowiedzialny za przenoszenie folderu kopii zapasowej do lokalizacji zewnętrznej, np. usługi Azure Storage.

### <a name="restore"></a>Przywracanie
Reliable State Manager udostępnia możliwość przywracania z kopii zapasowej za pomocą `RestoreAsync` interfejsu API.  
`RestoreAsync` Metody `RestoreContext` mogą być wywoływane tylko wewnątrz `OnDataLossAsync` metody.
Wartość logiczna, zwracany przez `OnDataLossAsync` wskazuje, czy usługa przywrócona jego stan ze źródła zewnętrznego.
Jeśli `OnDataLossAsync` zwraca wartość PRAWDA, Usługa Service Fabric odbuduje wszystkie pozostałe repliki z tego serwera podstawowego. Usługa Service Fabric zapewnia, że repliki, które otrzyma `OnDataLossAsync` wywoływanie pierwszego przejścia do roli głównej, ale nie udzielono można odczytać stanu lub zapisać stanu.
To oznacza, że implementacje klasy StatefulService `RunAsync` nie zostanie wywołany, dopóki `OnDataLossAsync` zakończy się pomyślnie.
Następnie `OnDataLossAsync` zostanie wywołana na nową podstawową.
Do momentu usługa kończy ten interfejs API pomyślnie (przez zwrócenie wartości true lub false) i zakończy się odpowiednie ponownej konfiguracji, interfejs API będzie przechowywać wywoływana pojedynczo.

`RestoreAsync` najpierw porzuca wszystkie istniejące stanu w replice podstawowej, która została wywołana na. Następnie Reliable State Manager tworzy wszystkie obiekty niezawodne, znajdujące się w folderze kopii zapasowej. Następnie niezawodne obiekty są zgodnie z instrukcjami otrzymywanymi przywracać ich punkty kontrolne w folderze kopii zapasowej. Na koniec Reliable State Manager własną stan odzyskiwania sprawności rekordy dziennika w folderze kopii zapasowej i wykonuje odzyskiwanie. W ramach procesu odzyskiwania operacje, zaczynając od "punkt początkowy", które zostały zatwierdzone rekordy dziennika w folderze kopii zapasowej są odtwarzane niezawodne obiektów. Ten krok zapewnia, że odzyskane stanu są spójne.

## <a name="next-steps"></a>Kolejne kroki
  - [Elementy Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Niezawodne usługi Szybki Start](service-fabric-reliable-services-quick-start.md)
  - [Niezawodne usługi powiadomień](service-fabric-reliable-services-notifications.md)
  - [Konfiguracji usług Reliable Services](service-fabric-reliable-services-configuration.md)
  - [Dokumentacja dla deweloperów dla elementów Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Okresowe wykonywanie kopii zapasowej i przywracanie w usłudze Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

