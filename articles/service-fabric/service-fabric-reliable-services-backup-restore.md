---
title: Service Fabric Backup and Restore (Tworzenie kopii zapasowej i przywracanie w usłudze Service Fabric)
description: Dokumentacja koncepcyjna dla kopii zapasowej i przywracania sieci szkieletowej usługi, usługa konfigurowania kopii zapasowej niezawodnych usług stanowych i wiarygodnych aktorów.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: ac6bb14517b67a4b308460583e8c9fb99a2df9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922777"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Tworzenie kopii zapasowych i przywracanie niezawodnych usług i niezawodnych podmiotów
Usługa Azure Service Fabric to platforma o wysokiej dostępności, która replikuje stan w wielu węzłach, aby utrzymać tę wysoką dostępność.  W związku z tym nawet jeśli jeden węzeł w klastrze nie powiedzie się, usługi nadal są dostępne. Chociaż ta wbudowana nadmiarowość zapewniana przez platformę może być wystarczająca dla niektórych, w niektórych przypadkach pożądane jest, aby usługa kopii zapasowej danych (do magazynu zewnętrznego).

> [!NOTE]
> Jest bardzo ważne, aby wykonać kopię zapasową i przywrócić dane (i przetestować, że działa zgodnie z oczekiwaniami), dzięki czemu można odzyskać ze scenariuszy utraty danych.
> 

> [!NOTE]
> Firma Microsoft zaleca użycie [okresowej kopii zapasowej i przywracania](service-fabric-backuprestoreservice-quickstart-azurecluster.md) do konfigurowania kopii zapasowej danych niezawodnych usług stanowych i wiarygodnych aktorów. 
> 


Na przykład usługa może chcieć uzyskać kopii zapasowej danych w celu ochrony przed następującymi scenariuszami:

- W przypadku trwałej utraty całego klastra sieci szkieletowej usług.
- Trwała utrata większości replik partycji usługi
- Błędy administracyjne, przez które stan przypadkowo zostanie usunięty lub uszkodzony. Na przykład może się to zdarzyć, jeśli administrator z wystarczającym uprawnieniem błędnie usuwa usługę.
- Błędy w usłudze, które powodują uszkodzenie danych. Na przykład może się to zdarzyć, gdy uaktualnienie kodu usługi rozpoczyna zapisywanie wadliwych danych do niezawodnej kolekcji. W takim przypadku kod i dane mogą być przywrócone do wcześniejszego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodne, aby mieć w trybie offline przetwarzania danych dla analizy biznesowej, która odbywa się oddzielnie od usługi, która generuje dane.

Funkcja tworzenia/przywracania umożliwia usługom zbudowanym na interfejsie API niezawodnych usług tworzenie i przywracanie kopii zapasowych. Zapasowe interfejsy API udostępniane przez platformę umożliwiają tworzenie kopii zapasowych stanu partycji usługi bez blokowania operacji odczytu lub zapisu. Interfejsy API przywracania umożliwiają przywrócenie stanu partycji usługi z wybranej kopii zapasowej.

## <a name="types-of-backup"></a>Typy kopii zapasowych
Dostępne są dwie opcje tworzenia kopii zapasowych: Pełny i Przyrostowy.
Pełna kopia zapasowa to kopia zapasowa zawierająca wszystkie dane wymagane do odtworzenia stanu repliki: punktów kontrolnych i wszystkich rekordów dziennika.
Ponieważ ma punkty kontrolne i dziennik, pełna kopia zapasowa może zostać przywrócona przez siebie.

Problem z pełnymi kopiami zapasowymi pojawia się, gdy punkty kontrolne są duże.
Na przykład replika, która ma 16 GB stanu będzie miał punkty kontrolne, które sumują się około 16 GB.
Jeśli mamy cel punktu odzyskiwania na pięć minut, replika musi być archiwizowane co pięć minut.
Za każdym razem, gdy kopie zapasowe musi skopiować 16 GB punktów kontrolnych oprócz 50 MB (konfigurowalne przy użyciu `CheckpointThresholdInMB`) warto dzienników.

![Przykład pełnej kopii zapasowej.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Rozwiązaniem tego problemu są przyrostowe kopie zapasowe, w których kopia zapasowa zawiera tylko zmienione rekordy dziennika od czasu ostatniej kopii zapasowej.

![Przykład przyrostowej kopii zapasowej.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Ponieważ przyrostowe kopie zapasowe są tylko zmiany od ostatniej kopii zapasowej (nie obejmuje punktów kontrolnych), wydają się być szybsze, ale nie można przywrócić na własną rękę.
Aby przywrócić przyrostową kopię zapasową, wymagany jest cały łańcuch kopii zapasowych.
Łańcuch kopii zapasowych to łańcuch kopii zapasowych, począwszy od pełnej kopii zapasowej, a następnie szereg ciągłych przyrostowych kopii zapasowych.

## <a name="backup-reliable-services"></a>Tworzenie kopii zapasowych niezawodnych usług
Autor usługi ma pełną kontrolę nad tym, kiedy wykonać kopie zapasowe i gdzie będą przechowywane kopie zapasowe.

Aby uruchomić kopię zapasową, usługa musi wywołać `BackupAsync`funkcję dziedziczonego elementu członkowskiego .  
Kopie zapasowe mogą być wykonane tylko z replik podstawowych i wymagają one stanu zapisu, które mają być przyznane.

Jak pokazano `BackupAsync` poniżej, `BackupDescription` przyjmuje obiekt, w którym można określić pełną lub przyrostową kopię `Func<< BackupInfo, CancellationToken, Task<bool>>>` zapasową, a także funkcję wywołania zwrotnego, która jest wywoływana, gdy folder kopii zapasowej został utworzony lokalnie i jest gotowy do przeniesienia do niektórych zewnętrznych magazynów.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Żądanie do podjęcia przyrostowej kopii zapasowej `FabricMissingFullBackupException`może zakończyć się niepowodzeniem z . Ten wyjątek wskazuje, że dzieje się jedna z następujących czynności:

- replika nigdy nie wykonać pełnej kopii zapasowej, ponieważ stała się
- niektóre rekordy dziennika od czasu obciętej ostatniej kopii zapasowej lub
- replika `MaxAccumulatedBackupLogSizeInMB` przekroczyła limit.

Użytkownicy mogą zwiększyć prawdopodobieństwo, że będą mogli wykonywać `MinLogSizeInMB` przyrostowe kopie zapasowe, konfigurując lub `TruncationThresholdFactor`.
Zwiększenie tych wartości zwiększa użycie dysku repliki.
Aby uzyskać więcej informacji, zobacz [Konfiguracja niezawodnych usług](service-fabric-reliable-services-configuration.md)

`BackupInfo`zawiera informacje dotyczące kopii zapasowej, w tym lokalizację folderu,`BackupInfo.Directory`w którym środowisko wykonawcze zapisywał kopię zapasową ( ). Funkcja wywołania zwrotnego `BackupInfo.Directory` może przenieść do magazynu zewnętrznego lub innej lokalizacji.  Ta funkcja zwraca również bool, który wskazuje, czy był w stanie pomyślnie przenieść folder kopii zapasowej do lokalizacji docelowej.

Poniższy kod pokazuje, `BackupCallbackAsync` jak metoda może służyć do przekazywania kopii zapasowej do usługi Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

W poprzednim przykładzie `ExternalBackupStore` jest przykładową klasą, która jest używana `UploadBackupFolderAsync` do interfejsu z magazynem obiektów Blob platformy Azure i jest metodą, która kompresuje folder i umieszcza go w magazynie obiektów Blob platformy Azure.

Należy pamiętać, że:

  - W danym momencie może być dostępna tylko jedna operacja tworzenia kopii zapasowej na replikę. Więcej niż `BackupAsync` jedno wywołanie `FabricBackupInProgressException` naraz zostanie zrzucine, aby ograniczyć kopie zapasowe pokładowe do jednego.
  - Jeśli replika nie powiedzie się podczas wykonywania kopii zapasowej, kopia zapasowa może nie została ukończona. W związku z tym po zakończeniu pracy awaryjnej jest to odpowiedzialność `BackupAsync` usługi do ponownego uruchomienia kopii zapasowej przez wywołanie w razie potrzeby.

## <a name="restore-reliable-services"></a>Przywracanie niezawodnych usług
Ogólnie rzecz biorąc przypadki, gdy może być konieczne wykonanie operacji przywracania, należą do jednej z następujących kategorii:

  - Partycja usługi utraciła dane. Na przykład dysk dla dwóch z trzech replik dla partycji (w tym repliki podstawowej) zostanie uszkodzony lub wyczyszczony. Nowy podstawowy może być konieczne przywrócenie danych z kopii zapasowej.
  - Cała usługa zostanie utracona. Na przykład administrator usuwa całą usługę, a tym samym usługę i dane muszą zostać przywrócone.
  - Usługa replikowała uszkodzone dane aplikacji (na przykład z powodu błędu aplikacji). W takim przypadku usługa musi zostać uaktualniona lub przywrócona, aby usunąć przyczynę uszkodzenia, a dane nieukorowne muszą zostać przywrócone.

Chociaż wiele podejść są możliwe, oferujemy `RestoreAsync` kilka przykładów na temat korzystania z powyższych scenariuszy.

## <a name="partition-data-loss-in-reliable-services"></a>Utrata danych partycji w niezawodnych usługach
W takim przypadku środowisko wykonawcze automatycznie wykryje `OnDataLossAsync` utratę danych i wywoła interfejs API.

Autor usługi musi wykonać następujące czynności, aby odzyskać:

  - Zastąd wystąż metodę wirtualnej klasy podstawowej `OnDataLossAsync`.
  - Znajdź najnowszą kopię zapasową w lokalizacji zewnętrznej, która zawiera kopie zapasowe usługi.
  - Pobierz najnowszą kopię zapasową (i odłóż kopię zapasową do folderu kopii zapasowej, jeśli została skompresowana).
  - Metoda `OnDataLossAsync` zapewnia `RestoreContext`. Wywołanie `RestoreAsync` interfejsu API `RestoreContext`na podany .
  - Powrót prawda, jeśli przywrócenie było sukcesem.

Poniżej przedstawiono przykładową implementację `OnDataLossAsync` metody:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription`przekazywane do `RestoreContext.RestoreAsync` wywołania zawiera `BackupFolderPath`element członkowski o nazwie .
Podczas przywracania pojedynczej pełnej kopii zapasowej należy ustawić `BackupFolderPath` ścieżkę lokalną folderu zawierającego pełną kopię zapasową.
Podczas przywracania pełnej kopii zapasowej i wielu przyrostowych kopii zapasowych, należy ustawić na ścieżkę lokalną folderu, `BackupFolderPath` który zawiera nie tylko pełną kopię zapasową, ale także wszystkie przyrostowe kopie zapasowe.
`RestoreAsync`wywołanie `FabricMissingFullBackupException` może `BackupFolderPath` zgłosić, jeśli dostarczone nie zawiera pełnej kopii zapasowej.
Można również `ArgumentException` zgłosić, jeśli `BackupFolderPath` ma uszkodzony łańcuch przyrostowych kopii zapasowych.
Na przykład jeśli zawiera pełną kopię zapasową, pierwszą przyrostową i trzecią przyrostową kopię zapasową, ale nie drugą przyrostową kopię zapasową.

> [!NOTE]
> RestorePolicy jest domyślnie ustawiona na Bezpieczne.  Oznacza to, `RestoreAsync` że interfejs API zakończy się niepowodzeniem z ArgumentException, jeśli wykryje, że folder kopii zapasowej zawiera stan, który jest starszy lub równy stanowi zawartemu w tej replice.  `RestorePolicy.Force`można pominąć tę kontrolę bezpieczeństwa. Jest to określone `RestoreDescription`jako część .
> 

## <a name="deleted-or-lost-service"></a>Usługa usunięta lub utracona
Jeśli usługa zostanie usunięta, należy najpierw ponownie utworzyć usługę, zanim dane będą mogły zostać przywrócone.  Ważne jest, aby utworzyć usługę z tej samej konfiguracji, na przykład schemat partycjonowania, dzięki czemu dane mogą być przywracane bezproblemowo.  Po uruchomieniu usługi interfejsu API do`OnDataLossAsync` przywracania danych (powyżej) musi być wywoływana na każdej partycji tej usługi. Jednym ze sposobów osiągnięcia tego celu jest przy użyciu [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) na każdej partycji.  

Od tego momentu implementacja jest taka sama jak w powyższym scenariuszu. Każda partycja musi przywrócić najnowszą odpowiednią kopię zapasową z magazynu zewnętrznego. Jednym z zastrzeżenia jest to, że identyfikator partycji może teraz ulec zmianie, ponieważ środowisko wykonawcze tworzy identyfikatory partycji dynamicznie. W związku z tym usługa musi przechowywać odpowiednie informacje o partycji i nazwę usługi, aby zidentyfikować poprawną najnowszą kopię zapasową, aby przywrócić z każdej partycji.

> [!NOTE]
> Nie zaleca się `FabricClient.ServiceManager.InvokeDataLossAsync` używania na każdej partycji, aby przywrócić całą usługę, ponieważ może to spowodować uszkodzenie stanu klastra.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikacja uszkodzonych danych aplikacji
Jeśli nowo wdrożone uaktualnienie aplikacji ma błąd, który może spowodować uszkodzenie danych. Na przykład uaktualnienie aplikacji może rozpocząć aktualizowanie każdego rekordu numeru telefonu w słowniku niezawodnym z nieprawidłowym numerem kierunkowym.  W takim przypadku nieprawidłowe numery telefonów będą replikowane, ponieważ sieci szkieletowej usług nie jest świadomy charakteru danych, które są przechowywane.

Pierwszą rzeczą do zrobienia po wykryciu takiego rażącego błędu, który powoduje uszkodzenie danych jest zamrożenie usługi na poziomie aplikacji i, jeśli to możliwe, uaktualnienie do wersji kodu aplikacji, która nie ma błędu.  Jednak nawet po naprawieniu kodu usługi dane mogą być nadal uszkodzone, a zatem dane mogą wymagać przywrócenia.  W takich przypadkach może nie wystarczyć do przywrócenia najnowszej kopii zapasowej, ponieważ najnowsze kopie zapasowe mogą być również uszkodzone.  W związku z tym musisz znaleźć ostatnią kopię zapasową, która została wykonana, zanim dane zostały uszkodzone.

Jeśli nie masz pewności, które kopie zapasowe są uszkodzone, można wdrożyć nowy klaster sieci szkieletowej usług i przywrócić kopie zapasowe partycji, których dotyczy problem, podobnie jak w powyższym scenariuszu "Usunięto lub utracona usługa".  Dla każdej partycji rozpocznij przywracanie kopii zapasowych z najnowszych do najmniej. Po znalezieniu kopii zapasowej, która nie ma uszkodzenia, przenieś/usuń wszystkie kopie zapasowe tej partycji, które były nowsze (niż ta kopia zapasowa). Powtórz ten proces dla każdej partycji. Teraz, `OnDataLossAsync` gdy jest wywoływana na partycji w klastrze produkcyjnym, ostatnia kopia zapasowa znaleziona w magazynie zewnętrznym będzie pobrana przez powyższy proces.

Teraz kroki w sekcji "Usunięto lub utracona usługa" może służyć do przywrócenia stanu usługi do stanu, zanim kod buggy uszkodzony stan.

Należy pamiętać, że:

  - Podczas przywracania istnieje prawdopodobieństwo, że przywracana kopia zapasowa jest starsza niż stan partycji przed utratą danych. Z tego powodu należy przywrócić tylko w ostateczności, aby odzyskać jak najwięcej danych, jak to możliwe.
  - Ciąg reprezentujący ścieżkę folderu kopii zapasowej i ścieżki plików wewnątrz folderu kopii zapasowej mogą być większe niż 255 znaków, w zależności od ścieżki FabricDataRoot i długości nazwy typu aplikacji. Może to spowodować niektóre metody `Directory.Move`.NET, `PathTooLongException` takie jak , aby zgłosić wyjątek. Jednym z rozwiązań jest bezpośrednie wywołanie interfejsów API `CopyFile`kernel32, takich jak .

## <a name="back-up-and-restore-reliable-actors"></a>Twoj kopię zapasową i przywracanie wiarygodnych aktorów


Reliable Actors Framework jest zbudowany na podstawie niezawodnych usług. ActorService, który obsługuje aktora(ów) jest stanową niezawodną usługą. W związku z tym wszystkie funkcje tworzenia kopii zapasowych i przywracania dostępne w niezawodne usługi są również dostępne dla wiarygodnych aktorów (z wyjątkiem zachowań, które są specyficzne dla dostawcy stanu). Ponieważ kopie zapasowe będą podejmowane na podstawie partycji, stany dla wszystkich podmiotów w tej partycji będą archiwizowane (i przywrócenie jest podobne i nastąpi na podstawie partycji). Aby wykonać kopię zapasową/przywracanie, właściciel usługi należy utworzyć klasę usługi aktora niestandardowego, która wywodzi się z ActorService klasy, a następnie wykonać kopię zapasową/przywrócić podobne do niezawodnych usług, jak opisano powyżej w poprzednich sekcjach.

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

Podczas tworzenia klasy usługi aktora niestandardowego, należy zarejestrować, że również podczas rejestrowania aktora.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Domyślnym dostawcą stanu `KvsActorStateProvider`dla wiarygodnych aktorów jest . Przyrostowa kopia zapasowa nie jest `KvsActorStateProvider`domyślnie włączona dla programu . Przyrostową kopię zapasową można włączyć, tworząc `KvsActorStateProvider` z odpowiednim ustawieniem w konstruktorze, a następnie przekazując ją do konstruktora ActorService, jak pokazano w poniższym fragmentie kodu:

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

Po włączeniu przyrostowej kopii zapasowej, wykonywanie przyrostowej kopii zapasowej może zakończyć się niepowodzeniem z FabricMissingFullBackupException z jednego z następujących powodów i trzeba będzie wykonać pełną kopię zapasową przed podjęciem przyrostowej kopii zapasowej(-ów):

  - Replika nigdy nie podjęła pełnej kopii zapasowej, ponieważ stał się podstawowym.
  - Niektóre rekordy dziennika zostały obcięte od czasu ostatniej kopii zapasowej.

Gdy przyrostowa kopia zapasowa `KvsActorStateProvider` jest włączona, nie używa buforu cyklicznego do zarządzania rekordami dziennika i okresowo obcina go. Jeśli użytkownik nie wykonać kopii zapasowej przez okres 45 minut, system automatycznie obcina rekordy dziennika. Ten interwał można skonfigurować, określając `logTruncationIntervalInMinutes` w `KvsActorStateProvider` konstruktorze (podobnie jak podczas włączania przyrostowej kopii zapasowej). Rekordy dziennika może również uzyskać obcięty, jeśli replika podstawowa musi zbudować inną replikę, wysyłając wszystkie swoje dane.

Podczas przywracania z łańcucha kopii zapasowych, podobnie jak usługi niezawodne, BackupFolderPath powinien zawierać podkatalogi z jednym podkatalogiem zawierającym pełną kopię zapasową, a inne podkatalogi zawierające przyrostowe kopie zapasowe. Interfejs API przywracania zniesie FabricException z odpowiednim komunikatem o błędzie, jeśli sprawdzanie poprawności łańcucha kopii zapasowej zakończy się niepowodzeniem. 

> [!NOTE]
> `KvsActorStateProvider`obecnie ignoruje opcję RestorePolicy.Safe. Obsługa tej funkcji jest planowana w nadchodzącej wersji.
> 

## <a name="testing-back-up-and-restore"></a>Testowanie kopii zapasowej i przywracania
Ważne jest, aby upewnić się, że kopie zapasowe danych krytycznych są archiwizowane i mogą być przywracane z. Można to zrobić, wywołując `Start-ServiceFabricPartitionDataLoss` polecenie cmdlet w programie PowerShell, które mogą wywoływać utratę danych w określonej partycji, aby sprawdzić, czy funkcja tworzenia kopii zapasowych i przywracania danych dla usługi działa zgodnie z oczekiwaniami.  Istnieje również możliwość programowo wywołać utratę danych i przywrócić z tego zdarzenia, jak również.

> [!NOTE]
> Przykładową implementację funkcji tworzenia kopii zapasowych i przywracania można znaleźć w aplikacji odwołania do sieci Web w usłudze GitHub. Więcej informacji `Inventory.Service` można znaleźć w usłudze.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Pod maską: więcej szczegółów na temat tworzenia kopii zapasowych i przywracania
Oto kilka szczegółów dotyczących tworzenia kopii zapasowych i przywracania.

### <a name="backup"></a>Tworzenie kopii zapasowych
Menedżer stanu niezawodne zapewnia możliwość tworzenia spójnych kopii zapasowych bez blokowania żadnych operacji odczytu lub zapisu. Aby to zrobić, wykorzystuje mechanizm trwałości punktu kontrolnego i dziennika.  Menedżer stanu niezawodne zajmuje rozmyte (lekkie) punkty kontrolne w niektórych punktach, aby zmniejszyć presję z dziennika transakcyjnego i skrócić czas odzyskiwania.  Po `BackupAsync` wywołaniu, Menedżer stanu niezawodne instruuje wszystkie niezawodne obiekty, aby skopiować swoje najnowsze pliki punktu kontrolnego do lokalnego folderu kopii zapasowej.  Następnie Menedżer stanu niezawodnego kopiuje wszystkie rekordy dziennika, począwszy od "wskaźnika początkowego" do najnowszego rekordu dziennika do folderu kopii zapasowej.  Ponieważ wszystkie rekordy dziennika do najnowszego rekordu dziennika są zawarte w kopii zapasowej i Menedżer stanu niezawodnego zachowuje rejestrowanie z wyprzedzeniem zapisu, Menedżer stanu niezawodnego gwarantuje, że wszystkie transakcje, które zostały zatwierdzone (powrócił`CommitAsync` pomyślnie) są uwzględniane w kopii zapasowej.

Każda transakcja, która `BackupAsync` zatwierdza po został wywołany może lub nie może być w kopii zapasowej.  Po zapełnieniu lokalnego folderu kopii zapasowej przez platformę (oznacza to, że lokalna kopia zapasowa jest wypełniana przez środowisko wykonawcze), wywoływane jest wywołanie wywołania zwrotnego kopii zapasowej usługi.  To wywołanie zwrotne jest odpowiedzialny za przeniesienie folderu kopii zapasowej do lokalizacji zewnętrznej, takich jak usługa Azure Storage.

### <a name="restore"></a>Przywracanie
Menedżer stanu niezawodne zapewnia możliwość przywracania z `RestoreAsync` kopii zapasowej przy użyciu interfejsu API.  
Metoda `RestoreAsync` na `RestoreContext` może być wywołana `OnDataLossAsync` tylko wewnątrz metody.
Bool zwrócony `OnDataLossAsync` przez wskazuje, czy usługa przywróciła swój stan ze źródła zewnętrznego.
Jeśli `OnDataLossAsync` zwraca wartość true, sieci szkieletowej usług będzie odbudować wszystkie inne repliki z tego podstawowego. Sieci szkieletowej usług zapewnia, `OnDataLossAsync` że repliki, które będą odbierać wywołanie pierwszego przejścia do roli podstawowej, ale nie mają statusu odczytu lub zapisu.
Oznacza to, że dla realizatorów StatefulService nie zostanie wywołana, `RunAsync` dopóki `OnDataLossAsync` nie zakończy się pomyślnie.
Następnie `OnDataLossAsync` zostanie wywołana na nowy podstawowy.
Dopóki usługa zakończy ten interfejs API pomyślnie (przez zwrócenie true lub false) i kończy odpowiednią ponowną konfigurację, interfejs API będzie nadal wywoływany po jednym na raz.

`RestoreAsync`najpierw porzuca cały istniejący stan w repliki podstawowej, która została wywołana. Następnie Menedżer stanu niezawodne tworzy wszystkie niezawodne obiekty, które istnieją w folderze kopii zapasowej. Następnie Reliable obiekty są instruowane, aby przywrócić z ich punktów kontrolnych w folderze kopii zapasowej. Na koniec Menedżer stanu niezawodnego odzyskuje swój własny stan z rekordów dziennika w folderze kopii zapasowej i wykonuje odzyskiwanie. W ramach procesu odzyskiwania operacje rozpoczynające się od "punktu początkowego", które zostały zatwierdzone rekordy dziennika w folderze kopii zapasowej są odtwarzane do Reliable obiektów. Ten krok zapewnia, że stan odzyskane jest spójne.

## <a name="next-steps"></a>Następne kroki
  - [Elementy Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Szybki start niezawodnych usług](service-fabric-reliable-services-quick-start.md)
  - [Powiadomienia o niezawodnych usługach](service-fabric-reliable-services-notifications.md)
  - [Konfiguracja niezawodnych usług](service-fabric-reliable-services-configuration.md)
  - [Informacje dla dewelopera dotyczące niezawodnych kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Okresowe wykonywanie kopii zapasowej i przywracanie w usłudze Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

