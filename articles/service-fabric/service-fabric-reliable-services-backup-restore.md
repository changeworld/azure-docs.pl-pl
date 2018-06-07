---
title: Usługa sieci szkieletowej w kopii zapasowej i przywracania | Dokumentacja firmy Microsoft
description: Dokumentacja koncepcyjna sieci szkieletowej usługi tworzenia kopii zapasowych i przywracania
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: 46f9c6129ccf99fb72a285fa4089b7b3f01f7d7b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643036"
---
# <a name="back-up-and-restore-reliable-services-and-reliable-actors"></a>Kopie zapasowe i przywracanie Reliable Services i Reliable Actors
Sieć szkieletowa usług Azure to platforma wysokiej dostępności, która replikuje stanu na wielu węzłach do obsługi tej wysokiej dostępności.  W związku z tym nawet w przypadku awarii jednego węzła w klastrze, usługi nadal dostępne. Może być wystarczające dla niektórych wbudowanych — dublowanie dostarczone przez platformę, w niektórych przypadkach pożądane jest usługi Kopia zapasowa danych (w zewnętrznym sklepie).

> [!NOTE]
> Podstawowe znaczenie ma ich kopii zapasowej i przywracanie danych (i przetestować, że działa zgodnie z oczekiwaniami), można odzyskać z scenariuszy utraty danych.
> 
> 

Na przykład usługi warto utworzyć kopię zapasową danych w celu zapewnienia ochrony przed w następujących scenariuszach:

- W przypadku trwałą utratę całego klastra sieci szkieletowej usług.
- Trwałą utratę większości replik partycji usługi
- Błędy administracyjne zgodnie z którymi stan przypadkowo pobiera usunięte lub uszkodzony. Na przykład tak zdarzyć, jeśli administrator z uprawnieniami wystarczające błędnego Usuwa usługę.
- Usterki w usłudze, które spowodować uszkodzenie danych. Na przykład może to być spowodowane uaktualnienie kodu usługi uruchamia zapisywania danych błędny niezawodnej kolekcji. W takim przypadku zarówno kod i dane mogą mieć przywrócenie do wcześniejszego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodną mieć w trybie offline przetwarzania danych do analizy biznesowej, wykonywanej oddzielnie z usługą, która generuje dane.

Funkcja Backup/Restore umożliwia usług oparty na niezawodne interfejsu API usług do tworzenia i przywracania kopii zapasowych. Kopii zapasowej interfejsów API dostarczonych przez platformę Zezwalaj na kopie zapasowe stanu partycji usługi, bez blokowania odczytu lub zapisu. Przywracanie interfejsy API umożliwiają partycji usługi stan można przywrócić z kopii zapasowej wybrany.

## <a name="types-of-backup"></a>Typy kopii zapasowych
Dostępne są dwie opcje tworzenia kopii zapasowej: pełne i przyrostowe.
Pełna kopia zapasowa jest kopii zapasowej, który zawiera wszystkie dane wymagane do odtworzenia stanu repliki: punkty kontrolne i wszystkie rekordy dziennika.
Ponieważ ma ona punkty kontrolne i dziennik, pełnej kopii zapasowej można przywracać samodzielnie.

Problem z pełnych kopii zapasowych powstaje, gdy punkty kontrolne są duże.
Na przykład repliki, który ma 16 GB pamięci stanu ma punkty kontrolne, które dodać około do 16 GB.
Jeśli mamy cel punktu odzyskiwania pięciu minut, replika musi wykonać kopię zapasową co pięć minut.
Zawsze zapasową należy skopiować 16 GB pamięci punkty kontrolne, oprócz 50 MB (można skonfigurować przy użyciu `CheckpointThresholdInMB`), przez jaką dzienniki.

![Przykład pełnej kopii zapasowej.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Rozwiązanie tego problemu jest przyrostowe kopie zapasowe, których kopia zapasowa zawiera tylko rekordów dziennika zmienione od czasu ostatniej kopii zapasowej.

![Przyrostowej kopii zapasowej przykład.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Ponieważ przyrostowe kopie zapasowe są tylko zmiany od ostatniej kopii zapasowej (nie zawiera punktów kontrolnych), mają zwykle szybsze, ale nie można przywrócić na ich własnych.
Aby przywrócić przyrostowej kopii zapasowej, wymagany jest cały łańcuch kopii zapasowych.
Łańcuch kopii zapasowych łańcuch kopii zapasowych w programie pełnej kopii zapasowej i następuje numer ciągłe przyrostowych kopii zapasowych.

## <a name="backup-reliable-services"></a>Niezawodne usługi tworzenia kopii zapasowej
Tworzenie usługi ma pełną kontrolę nad, kiedy należy utworzyć kopie zapasowe i przechowywania kopii zapasowych.

Aby rozpocząć tworzenie kopii zapasowej, usługa musi wywołanie funkcji dziedziczonego elementu członkowskiego `BackupAsync`.  
Tworzenie kopii zapasowych może być utworzona tylko z repliki podstawowej, a potrzebują stan zapisu, aby otrzymać.

Jak pokazano poniżej, `BackupAsync` przyjmuje `BackupDescription` obiektu, w którym można określić pełny lub przyrostowej kopii zapasowej, a także funkcję wywołania zwrotnego, `Func<< BackupInfo, CancellationToken, Task<bool>>>` który wywoływane, gdy folder kopii zapasowej został utworzony lokalnie i jest gotowa do przeniesienia niektórych magazynu zewnętrznego.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Wniosku przyrostowej kopii zapasowej może zakończyć się niepowodzeniem z `FabricMissingFullBackupException`. Tego wyjątku wskazuje, że przeprowadzana jest jedną z następujących czynności:

- repliki nigdy nie miała pełnej kopii zapasowej, ponieważ jest on podstawowym,
- Niektóre rekordy dziennika, ponieważ ostatnia kopia zapasowa została obcięta lub
- repliki przekazany `MaxAccumulatedBackupLogSizeInMB` limit.

Użytkownicy mogą zwiększyć prawdopodobieństwo jest w stanie przyrostowych kopii zapasowych przez skonfigurowanie `MinLogSizeInMB` lub `TruncationThresholdFactor`.
Należy pamiętać, że te zwiększenie wartości wzrostu na użycie dysku repliki.
Aby uzyskać więcej informacji, zobacz [niezawodnej usługi konfiguracji](service-fabric-reliable-services-configuration.md)

`BackupInfo` zawiera informacje dotyczące tworzenia kopii zapasowej, tym lokalizację folderu, w którym środowiska uruchomieniowego zapisana kopia zapasowa (`BackupInfo.Directory`). Funkcja wywołania zwrotnego można przenieść `BackupInfo.Directory` w zewnętrznym sklepie lub w innej lokalizacji.  Ta funkcja zwraca również wartość logiczna, która wskazuje, czy był w stanie pomyślnie Przenieś folder kopii zapasowej do lokalizacji docelowej.

Poniższy kod przedstawia sposób `BackupCallbackAsync` metody można użyć do przekazania do magazynu Azure kopii zapasowej:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

W powyższym przykładzie `ExternalBackupStore` jest klasa próbki, która jest używana na potrzeby interfejsu z magazynu obiektów Blob platformy Azure i `UploadBackupFolderAsync` metoda kompresuje folder i umieszcza je w magazynie obiektów Blob platformy Azure.

Należy pamiętać, że:

  - Może istnieć tylko jedna operacja tworzenia kopii zapasowej aktywny na replice w danym momencie. Więcej niż jeden `BackupAsync` zgłosi wywołanie naraz `FabricBackupInProgressException` Aby ograniczyć liczbę kopii zapasowych porządkowych do jednego.
  - Jeśli replika nie powiedzie się za pośrednictwem w trakcie kopii zapasowej, kopia zapasowa może nie zostały zakończone. W związku z tym po zakończeniu pracy awaryjnej jest odpowiedzialny za usługi, aby ponownie uruchomić tworzenie kopii zapasowej za pomocą `BackupAsync` odpowiednio do potrzeb.

## <a name="restore-reliable-services"></a>Przywróć niezawodne usługi
Ogólnie rzecz biorąc przypadków, gdy potrzebne do wykonania operacji przywracania należą do jednej z tych kategorii:

  - Usługa partycji utraty danych. Na przykład dysku dla replik dwóch spośród trzech dla partycji (łącznie z repliką podstawową) pobiera uszkodzony lub wyczyszczone. Nową podstawową trzeba przywrócić dane z kopii zapasowej.
  - Całej usługi zostaną utracone. Na przykład administrator spowoduje usunięcie całej usługi i w związku z tym usługa i dane potrzebne do przywrócenia.
  - Usługa zreplikowanych danych błędy aplikacji (na przykład z powodu błędów aplikacji). W takim przypadku usługa ma uaktualnione lub przywrócono Usuń przyczynę uszkodzenia, i -uszkodzenie danych ma zostać przywrócone.

Mimo że wiele metod jest możliwe, firma Microsoft oferuje kilka przykładów przy użyciu `RestoreAsync` do odzyskania z powyższych scenariuszy.

## <a name="partition-data-loss-in-reliable-services"></a>Utrata danych partycji w niezawodne usługi
W takim przypadku środowiska uruchomieniowego może automatycznie wykryć utraty danych i wywołać `OnDataLossAsync` interfejsu API.

Autor usługi musi wykonać następujące czynności, aby odzyskać:

  - Metoda wirtualna klasa podstawowa musi zostać zastąpiona `OnDataLossAsync`.
  - Znajdź najnowszej kopii zapasowej w lokalizacji zewnętrznej, który zawiera usługi tworzenia kopii zapasowych.
  - Pobieranie najnowszej kopii zapasowej (i zdekompresowany kopii zapasowej do folderu kopii zapasowej, jeśli został poddany kompresji).
  - `OnDataLossAsync` Metoda zapewnia `RestoreContext`. Wywołanie `RestoreAsync` interfejsu API na udostępnionych `RestoreContext`.
  - Zwraca wartość true, jeśli przywrócenie został pomyślnie.

Poniżej przedstawiono przykład stosowania `OnDataLossAsync` metody:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` przekazano do `RestoreContext.RestoreAsync` wywołanie zawiera element członkowski o nazwie `BackupFolderPath`.
Podczas przywracania pojedynczego pełnej kopii zapasowej, to `BackupFolderPath` powinien być ustawiony na ścieżce lokalnej folderu, który zawiera z pełnej kopii zapasowej.
Podczas przywracania pełnej kopii zapasowej i liczba przyrostowe kopie zapasowe, `BackupFolderPath` powinien być ustawiony na ścieżce lokalnej folderu, który zawiera nie tylko pełna kopia zapasowa, ale również wszystkich przyrostowe kopie zapasowe.
`RestoreAsync` Wywołanie może zgłosić `FabricMissingFullBackupException` Jeśli `BackupFolderPath` podane nie zawiera pełnej kopii zapasowej.
Można również zgłosić `ArgumentException` Jeśli `BackupFolderPath` został przerwany łańcuch przyrostowych kopii zapasowych.
Na przykład, jeśli zawiera pełną kopię zapasową pierwszy przyrostowych i trzeci przyrostowej kopii zapasowej, ale nie drugi przyrostowej kopii zapasowej.

> [!NOTE]
> Domyślnie wynosi RestorePolicy bezpieczne.  Oznacza to, że `RestoreAsync` interfejsu API powiedzie się i ArgumentException, jeśli wykryje, że folder kopii zapasowej zawiera stan, który jest starsza niż lub równe stanie zawartymi w tej replice.  `RestorePolicy.Force` można pominąć sprawdzanie bezpieczeństwa. To jest określony jako część `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Usunięte i utracone usługi
Jeśli usługa zostanie usunięty, należy najpierw ponownie utworzyć usługę przed przywróceniem danych.  Należy utworzyć usługę z taką samą konfiguracją, na przykład schemat, partycji, dzięki czemu dane można przywrócić bezproblemowo.  Gdy usługa działa, interfejsu API, aby przywrócić dane (`OnDataLossAsync` powyżej) musi być wywołana na każdej partycji tej usługi. Jednym ze sposobów osiągnięcia jest przy użyciu [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) na każdej partycji.  

Z tego punktu implementacji jest taka sama, jak w powyższym scenariuszu. Każda partycja musi przywrócić najnowsze odpowiednich kopii zapasowej z magazynu zewnętrznego. Jedno zastrzeżenie: jest, że identyfikator partycji może teraz zmieniono, ponieważ środowisko uruchomieniowe dynamicznie tworzy identyfikatorów partycji. W związku z tym usługa musi przechowywać nazwy partycji odpowiednie informacje i usługi do identyfikowania poprawne najnowszej kopii zapasowej do przywrócenia z dla każdej partycji.

> [!NOTE]
> Nie zaleca się używania `FabricClient.ServiceManager.InvokeDataLossAsync` na każdej partycji do przywrócenia całej usługi, ponieważ, który może doprowadzić do uszkodzenia sieci klastra.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikacja danych aplikacji uszkodzony
Jeśli uaktualnienie nowo wdrożonych aplikacji zawiera usterkę, która może spowodować uszkodzenie danych. Na przykład uaktualnienie aplikacji może uruchomić zaktualizować każdego rekordu numeru telefonu w słowniku niezawodnej nieprawidłowy kod obszaru.  W takim przypadku nieprawidłowych numerach telefonów będą replikowane od sieci szkieletowej usług nie został powiadomiony o rodzaju danych, które są przechowywane.

Najpierw należy wykonać po wykrywania takich rażące usterkę, która spowoduje uszkodzenie danych jest blokowanie usługi na poziomie aplikacji i, jeśli to możliwe, uaktualnienie do wersji kodu aplikacji, który nie ma błędu.  Jednak nawet po usunięciu kodu usługi danych nadal może być uszkodzony, a w związku z tym danych może być konieczne przywrócenie.  W takich przypadkach mogą nie być wystarczające do przywracania najnowszej kopii zapasowej, ponieważ najnowszej kopii zapasowych również może być uszkodzony.  W związku z tym należy znaleźć ostatniej kopii zapasowej utworzonej przed Otrzymano uszkodzone dane.

Jeśli nie masz pewności, których kopie zapasowe są uszkodzone, można wdrożyć nowy klaster sieci szkieletowej usług i przywracać kopie zapasowe dotyczy partycji, podobnie jak w powyższym "Usunięte lub usługa utracone" scenariusza.  Dla każdej partycji uruchomić przywracanie kopii zapasowych z ostatniego do najmniej. Po znalezieniu kopii zapasowej, która nie ma uszkodzenia, Przenieś lub Usuń wszystkie kopie zapasowe tej partycji, które były nowsza (od tej kopii zapasowej). Powtórz ten proces dla każdej partycji. Teraz, gdy `OnDataLossAsync` nazywa się na partycji w klastra produkcyjnego ostatniej kopii zapasowej w zewnętrznym Sklepie będzie pobierany przez proces powyżej.

Teraz, kroki w "usunięte lub usługa utracone" sekcji może służyć do przywrócenia stanu usługi do stanu przed buggy kod uszkodzony stanu.

Należy pamiętać, że:

  - Podczas przywracania, istnieje ryzyko, że trwa przywracanie kopii zapasowej jest starsza niż stan partycji przed utracono dane. W związku z tym należy przywrócić tylko w ostateczności odzyskać jak najwięcej danych.
  - Ciąg, który reprezentuje ścieżkę do folderu kopii zapasowej i ścieżki plików w folderze kopii zapasowej może być większa niż 255 znaków, w zależności od ścieżki zmiennej FabricDataRoot i długość nazwy typu aplikacji. Może to spowodować niektóre metody .NET, takie jak `Directory.Move`, aby zgłosić `PathTooLongException` wyjątku. Jeden obejściem jest bezpośrednio wywoływać interfejsy API kernel32, takich jak `CopyFile`.

## <a name="backup-and-restore-reliable-actors"></a>Kopia zapasowa i przywracanie Reliable Actors


Niezawodne Framework złośliwych użytkowników jest oparty na niezawodne usługi. ActorService, który obsługuje actor(s) jest stanowa niezawodnej usługi. W związku z tym wszystkie kopii zapasowej i przywracania funkcji dostępnych w niezawodnej usługi jest również dostępna do Reliable Actors (z wyjątkiem zachowania, które są właściwe dla dostawcy stanu). Ponieważ kopie zapasowe zostaną wykonane na podstawie na partycji, Stany wszystkich podmiotów partycji zostaną umieszczone w kopii (i przywracania jest podobny i nastąpi na podstawie na partycji). Do wykonania kopii zapasowej/przywracania, właściciel usługi należy utworzyć niestandardowe aktora klasy usługi, która pochodzi z klasy ActorService a następnie wykonaj wykonywania kopii zapasowej/przywracania podobne do niezawodne usługi zgodnie z powyższym opisem w poprzedniej sekcji.

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

Podczas tworzenia klasy usługi aktora niestandardowe, należy zarejestrować który również podczas rejestrowania aktora.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
   (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Domyślny dostawca stanu dla elementów Reliable Actors jest `KvsActorStateProvider`. Przyrostowa kopia zapasowa nie jest włączona domyślnie dla `KvsActorStateProvider`. Można włączyć przyrostowej kopii zapasowej, tworząc `KvsActorStateProvider` z odpowiednie ustawienie w Konstruktorze jego, a następnie przekazywanie do konstruktora ActorService, jak pokazano w poniższym fragmencie kodu:

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

Po włączeniu przyrostowej kopii zapasowej, biorąc przyrostowej kopii zapasowej może zakończyć się niepowodzeniem z FabricMissingFullBackupException dla jednego z następujących powodów i trzeba będzie wykonać pełnej kopii zapasowej przed zmianą przyrostowe kopie zapasowe:

  - Replika nigdy nie miało pełnej kopii zapasowej, ponieważ stał się podstawowym.
  - Niektóre rekordy dziennika został obcięty, ponieważ ostatnia kopia zapasowa została wykonana.

Po włączeniu przyrostowej kopii zapasowej `KvsActorStateProvider` nie umożliwia cykliczne buforu Zarządzanie swoje rekordy dziennika i okresowo obcina go. Jeśli żadna kopia zapasowa nie zostanie podjęta przez użytkownika w danym okresie 45 minut, system automatycznie obcina rekordów dziennika. Ten interwał można skonfigurować, określając `logTrunctationIntervalInMinutes` w `KvsActorStateProvider` konstruktora (podobnie jak w przypadku włączania przyrostowej kopii zapasowej). Rekordy dziennika również może pobrać obcięty, jeśli replika podstawowa musi kompilacji innej repliki, wysyłając jego dane.

Podczas operacji przywracania z kopii zapasowej łańcucha, podobnie jak niezawodne usługi BackupFolderPath powinien zawierać podkatalogów z podkatalogu co zawierające pełnej kopii zapasowej oraz inne podkatalogi zawierające przyrostowe kopie zapasowe. Interfejs API przywracania zgłosi FabricException z odpowiedni komunikat o błędzie w przypadku niepowodzenia weryfikacji łańcucha kopii zapasowej. 

> [!NOTE]
> `KvsActorStateProvider` obecnie ignoruje opcję RestorePolicy.Safe. Obsługa tej funkcji jest planowana w nową wersją.
> 

## <a name="testing-backup-and-restore"></a>Testowanie kopii zapasowych i przywracania
Należy się upewnić, że krytyczne dane jest tworzona kopia zapasowa i może zostać przywrócona z. Można to zrobić za pomocą `Start-ServiceFabricPartitionDataLoss` polecenia cmdlet programu PowerShell, który można wywołać utrata danych w określonej partycji, aby sprawdzić, czy dane kopii zapasowej i przywrócenia jej funkcjonalności dla usługi działa zgodnie z oczekiwaniami.  Istnieje również możliwość programowo wywołania utraty danych i Przywróć z zdarzenia oraz.

> [!NOTE]
> Można znaleźć przykładowe zastosowanie kopii zapasowej i przywrócenia jej funkcjonalności w aplikacji odwołania sieci Web w witrynie GitHub. Zapoznaj się `Inventory.Service` usługi, aby uzyskać więcej informacji.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Pod maską: więcej informacji na temat tworzenia kopii zapasowych i przywracania
Oto niektóre więcej informacji dotyczących tworzenia kopii zapasowej i przywracania.

### <a name="backup"></a>Backup
Niezawodne Menedżer stanu umożliwia tworzenie kopii zapasowych spójne bez blokowania żadnych odczytu lub operacje zapisu. Aby to zrobić, jego korzysta z mechanizmu stanu trwałego punktu kontrolnego i dziennika.  Niezawodne Menedżer stanu ma rozmytego (lekkie) punkty kontrolne w pewnych punktach, aby zwolnić wykorzystania w dzienniku transakcyjne i skrócić czas odzyskiwania.  Gdy `BackupAsync` jest wywoływana, niezawodne Menedżer stanu powoduje, że wszystkie obiekty niezawodnej można skopiować ich najnowsze pliki punktu kontrolnego do lokalnego folderu kopii zapasowej.  Następnie niezawodne Menedżer stanu kopiuje wszystkie rekordy dziennika, począwszy od wskaźnik"start" do najnowszej rekordu dziennika do folderu kopii zapasowej.  Ponieważ wszystkie rekordy dziennika do najnowszego rekordu dziennika znajdują się w kopii zapasowej i niezawodne Menedżer stanu zachowuje rejestrowania zapisu z wyprzedzeniem, niezawodnej Menedżer stanu gwarantuje, że wszystkie transakcje który są zatwierdzone (`CommitAsync` zwrócił pomyślnie ) są uwzględnione w kopii zapasowej.

Każdej transakcji, które zatwierdza po `BackupAsync` została wywołana może lub nie może być w kopii zapasowej.  Po lokalnym folderu kopii zapasowej zostały wprowadzone przez platformę (to znaczy lokalnej kopii zapasowej zostało zakończone przez środowisko uruchomieniowe), usługi tworzenia kopii zapasowej wywołania zwrotnego jest wywoływany.  To wywołanie zwrotne jest odpowiedzialny za przenoszenie folderu kopii zapasowej do lokalizacji zewnętrznej, takie jak magazyn Azure.

### <a name="restore"></a>Przywracanie
Niezawodne Menedżer stanu umożliwia przywracanie z kopii zapasowej za pomocą `RestoreAsync` interfejsu API.  
`RestoreAsync` Metoda `RestoreContext` można wywołać tylko wewnątrz `OnDataLossAsync` metody.
Bool zwrócony przez `OnDataLossAsync` wskazuje, czy usługa została przywrócona stanu ze źródła zewnętrznego.
Jeśli `OnDataLossAsync` zwraca wartość true, Service Fabric ponownie utworzy wszystkie inne replik z tego serwera podstawowego. Sieć szkieletowa usług gwarantuje, że replik, które otrzymają `OnDataLossAsync` połączeń telefonicznych z pierwszego przejścia do roli podstawowego, ale nie są przyznawane odczytać stanu lub zapisać stanu.
To oznacza, że implementacje klasy StatefulService `RunAsync` nie zostanie wywołany, dopóki `OnDataLossAsync` zakończy się pomyślnie.
Następnie `OnDataLossAsync` zostanie wywołana na nową podstawową.
Dopóki usługi zakończeniu działania tego interfejsu API pomyślnie (przez zwrócenie wartości true lub false) i zakończeniu odpowiednich ponownej konfiguracji, interfejsu API będzie przechowywać wywoływana pojedynczo.

`RestoreAsync` najpierw porzuca wszystkie istniejące stanu w replice podstawowej, która została wywołana w. Następnie niezawodnej Menedżer stanu tworzy wszystkie obiekty niezawodnej znajdujące się w folderze kopii zapasowej. Następnie niezawodnej obiektów pojawiło się polecenie Przywróć z ich punkty kontrolne w folderze kopii zapasowej. Na koniec niezawodnej Menedżer stanu odzyskuje własny stan z rekordów dziennika w folderze kopii zapasowej i wykonuje odzyskiwanie. W ramach procesu odzyskiwania operacji począwszy od "punkt początkowy", których zatwierdzania rekordy dziennika w folderze kopii zapasowej są odtwarzane niezawodnej obiektów. Ten krok zapewnia spójny stan odzyskane.

## <a name="next-steps"></a>Kolejne kroki
  - [Elementy Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Niezawodne usługi szybki start](service-fabric-reliable-services-quick-start.md)
  - [Niezawodne usługi powiadomień](service-fabric-reliable-services-notifications.md)
  - [Niezawodne usługi konfiguracji](service-fabric-reliable-services-configuration.md)
  - [Dokumentacja dla deweloperów niezawodnej kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Okresowe wykonywanie kopii zapasowej i przywracanie w usłudze Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

