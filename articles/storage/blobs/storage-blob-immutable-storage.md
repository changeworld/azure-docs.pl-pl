---
title: Niezmienny magazyn obiektów blob usługi Azure Storage | Dokumentacja firmy Microsoft
description: Usługa Azure Storage oferuje obsługę ROBAK (jednokrotny zapis, Odczyt wiele) dla magazynu obiektów Blob (obiekt), umożliwiającą użytkownikom przechowywanie danych w stanie wymazanie, nie można modyfikować w określonym interwale.
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 11/05/2018
ms.author: hux
ms.component: blobs
ms.openlocfilehash: d3d83e240fec692d5aa655923637910006c7a62f
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261476"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Store strategicznych danych biznesowych w usłudze Azure Blob storage

Niezmienne magazynu dla magazynu obiektów Blob platformy Azure (obiekt) pozwala użytkownikom przechowywać dane krytyczne dla prowadzonej działalności w stanie ROBAK (jednokrotny zapis, wiele odczytu). Ten stan sprawia, że dane trwałe i nie można modyfikować dla interwału określonego przez użytkownika. Obiekty BLOB można utworzyć i odczytu, ale nie zmodyfikowany lub usunięty na czas trwania okresu przechowywania.

## <a name="overview"></a>Przegląd

Niezmienne storage pomaga, instytucje finansowe i powiązanych branżach — szczególnie dealer brokera organizacjom — bezpiecznie przechowywać dane. On również nadającego się w każdym scenariuszu, w celu ochrony danych krytycznych, przed usunięciem.  

Typowe zastosowania tej funkcji to:

- **Zgodność z przepisami**: niezmienny magazyn dla usługi Azure Blob storage pomaga organizacjom adres s 17a-4(f) CFTC 1.31(d), FINRA i innych przepisów.

- **Zabezpieczanie przechowywania dokumentów**: usługi Blob storage zapewnia, że danych nie może być zmodyfikowane lub usunięte przez dowolnego użytkownika, w tym użytkowników z uprawnieniami administracyjnymi konta.

- **Prawnych**: niezmienny magazyn dla usługi Azure Blob storage pozwala użytkownikom przechowywać poufne informacje, które mają kluczowe znaczenie dla postępowań prawnych lub śledztw w stanie odporne na żądany czas trwania.

Włącza niezmienne magazynu:

- **Obsługa zasad przechowywania na podstawie czasu**: użytkownicy ustawienia zasad w celu przechowywania danych w określonym przedziale czasu.

- **Obsługa zasad ze względów prawnych**: gdy okres przechowywania nie jest znany, użytkownicy mogą ustawić archiwizacją ze względów prawnych do przechowywania danych immutably, dopóki nie zostanie wyczyszczona prawnych.  Po ustawieniu archiwizacji ze względów prawnych można tworzyć i odczytywać obiekty blob, ale nie można ich modyfikować ani usuwać. Każde archiwum prawne jest skojarzone ze zdefiniowanym przez użytkownika tagiem alfanumerycznym, używanym jako ciąg identyfikatora (może to być na przykład numer sprawy).

- **Pomoc techniczna dla wszystkich obiektów blob warstw**: ROBAK niezależnie od warstwy magazynu obiektów Blob platformy Azure i zasad dotyczą wszystkich warstwy: gorąca, chłodna i archiwum. Użytkownicy mogą przechodzą danych do warstwy większość optymalizacji kosztów dla swoich obciążeń przy zachowaniu danych niezmienności.

- **Konfiguracji na poziomie kontenera**: użytkownicy mogą konfigurować zasady przechowywania na podstawie czasu i ze względów prawnych tagi na poziomie kontenera. Za pomocą prostych ustawień na poziomie kontenera, użytkownicy mogą tworzyć i blokowanie zasady przechowywania na podstawie czasu, Rozszerz interwały przechowywania, ustaw i wyczyść archiwizacją ze względów prawnych i wiele innych. Te zasady mają zastosowanie do wszystkich obiektów blob w kontenerze, nowych i istniejących.

- **Obsługa rejestrowania inspekcji**: każdy kontener zawiera dziennik inspekcji. Pokazuje maksymalnie pięć polecenia na podstawie czasu przechowywania dla zasad zablokowany na podstawie czasu przechowywania, przy użyciu maksymalnie trzech dzienników dla rozszerzeń interwał przechowywania. Do przechowywania danych na podstawie czasu dziennik zawiera identyfikator użytkownika, typ polecenia, sygnatury czasowe i interwał przechowywania. W przypadku archiwizacją ze względów prawnych dziennik zawiera identyfikator użytkownika, wpisz polecenie sygnatury czasowe i tagi ze względów prawnych. Ten dziennik został zachowany na potrzeby okres istnienia tego kontenera, zgodnie z wytycznymi przepisami 17a-4(f) s. [Dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) pokazuje bardziej kompleksowe dziennika wszystkie kontrolki działania płaszczyzny. Odpowiada za użytkownika trwałe, jak mogą być wymagane do celów przepisami lub innymi przechowywania tych dzienników.

Niezmienny magazyn jest włączona we wszystkich publicznych regionach platformy Azure.

## <a name="how-it-works"></a>Jak to działa

Niezmienny magazyn dla usługi Azure Blob storage obsługuje dwa typy ROBAK lub niezmienne zasad: przechowywania na podstawie czasu i archiwizacją ze względów prawnych. Aby uzyskać szczegółowe informacje dotyczące sposobu tworzenia tych niezmienne zasad, zobacz [wprowadzenie](#getting-started) sekcji.

Po zastosowaniu zasad przechowywania na podstawie czasu lub prawnych w kontenerze, wszystkie istniejące obiekty BLOB przenieść się do niezmienne (zapisu i usuwania chroniony) stanu. Wszystkie nowe obiekty BLOB, które są przekazywane do kontenera, również zostanie przeniesione do niezmiennego stanu.

> [!IMPORTANT]
> Zasady przechowywania na podstawie czasu musi być *zablokowane* dla obiektu blob w niezmienne (zapisu i usuwania chroniony) stanie 17a-4(f) s a innymi zgodność z przepisami. Zaleca się blokowanie zasady w rozsądnym czasie, zwykle w ciągu 24 godzin. Nie zalecamy używania *odblokowane* stanu w celu innym niż krótkoterminowej funkcji wersji próbnych.

Stosowania zasad przechowywania na podstawie czasu na kontenerze wszystkich obiektów blob w kontenerze pozostaną niezmiennego stanu na czas trwania *skuteczne* okres przechowywania. Obowiązujący okres przechowywania w przypadku istniejących obiektów blob to różnica między czasem utworzenia obiektu blob a określonym przez użytkownika okresem przechowywania.

W przypadku nowych obiektów blob obowiązujący okres przechowywania jest równy okresowi przechowywania określonemu przez użytkownika. Ponieważ użytkownicy mogą wydłużyć okres przechowywania, niezmienne magazynu używa najnowszą wartość okres przechowywania określony przez użytkownika do obliczenia okres przechowywania skuteczne.

> [!TIP]
> Przykład:
>
> Użytkownik tworzy zasady przechowywania na podstawie czasu z interwałem przechowywania przez okres pięciu lat.
>
> Istniejący obiekt blob w kontenerze, testblob1, utworzono rok temu. Okres przechowywania skuteczne testblob1 jest 4 lata.
>
> Do kontenera zostaje przekazany nowy obiekt blob, testblob2. Okres przechowywania obowiązywać dla tego nowy obiekt blob jest pięć lat.

### <a name="legal-holds"></a>Archiwizacja ze względów prawnych

Po ustawieniu prawnych, wszystkie istniejące i nowe obiekty BLOB są pozostanie w stanie niezmienne, dopóki nie zostanie wyczyszczona prawnych. Aby uzyskać więcej informacji na temat zestawu i archiwizacją wyczyść ze względów prawnych, zobacz [wprowadzenie](#getting-started) sekcji.

Kontener może mieć zarówno prawnych, jak i zasad przechowywania na podstawie czasu, w tym samym czasie. Wszystkie obiekty BLOB w kontenerze pozostanie w stanie niezmienne, dopóki nie zostaną wyczyszczone wszystkie archiwizacją ze względów prawnych, nawet wtedy, gdy ich okresu przechowywania skuteczne utracił ważność. Z drugiej strony obiekt blob pozostaje w niezmiennego stanu do wygaśnięcia okresu przechowywania skuteczne, nawet jeśli zostały wyczyszczone wszystkie archiwizacją ze względów prawnych.

W poniższej tabeli przedstawiono typy obiekty blob — operacje, które są wyłączone dla różnych scenariuszy niezmienne. Aby uzyskać więcej informacji, zobacz [interfejsu API usługi Azure Blob Service](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) dokumentacji.

|Scenariusz  |Stan obiektu blob  |Operacje obiektów blob nie jest dozwolone  |
|---------|---------|---------|
|Trwa obowiązujący okres przechowywania obiektu blob i/lub ustawiono stan archiwizacji ze względów prawnych     |Niezmienny: ochrona przed usuwaniem i zapisem         |Usuwanie kontenera, usuwanie obiektów Blob, umieszczania obiektu Blob<sup>1</sup>, umieść bloku<sup>1</sup>, umieść zablokowanych<sup>1</sup>, należy ustawić metadane obiektu Blob, umieść strony, należy ustawić właściwości obiektu Blob, wykonanie migawki obiektu Blob, obiektu Blob kopiowania przyrostowego Dołącz bloku         |
|Upłynął obowiązujący okres przechowywania obiektu blob     |Ochrona tylko przed zapisem (operacje usuwania są dozwolone)         |Wstawienie obiektu Blob<sup>1</sup>, umieść bloku<sup>1</sup>, umieść zablokowanych<sup>1</sup>, ustaw metadane obiektu Blob, umieść strony, ustawianie obiektu Blob kopiowania przyrostowego właściwości, wykonywanie migawki obiektu Blob, obiektów Blob, Dołącz bloku         |
|Wszystkie informacje prawne przechowuje wyczyszczone, a w kontenerze są ustawione żadne zasady przechowywania na podstawie czasu     |Modyfikowalny         |Brak         |
|Brak zasad ROBAK zostanie utworzony (na podstawie czasu przechowywania lub prawnych)     |Modyfikowalny         |Brak         |

<sup>1</sup> aplikacja może wywołać tej operacji po utworzenie obiektu blob. Wszystkie kolejne operacje na obiekcie blob są niedozwolone.

> [!NOTE]
>
> Niezmienne storage jest dostępna tylko w ogólnego przeznaczenia w wersji 2 i kont usługi Blob Storage. To konto należy utworzyć za pomocą [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="pricing"></a>Cennik

Nie ma żadnych dodatkowych opłat za używanie tej funkcji. Niezmienialnymi danymi jest rozliczana w taki sam sposób, jak regularne, mutable danych. Aby uzyskać szczegóły cennika w usłudze Azure Blob Storage, zobacz [usługi Azure Storage, cennik](https://azure.microsoft.com/pricing/details/storage/blobs/).


## <a name="getting-started"></a>Wprowadzenie

Najnowsze wersje [witryny Azure portal](http://portal.azure.com) i [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) oraz wersję zapoznawczą [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May2018) obsługiwać niezmienny magazyn dla usługi Azure Blob storage.

### <a name="azure-portal"></a>Azure Portal

1. Utwórz nowy kontener lub wybierz istniejący kontener, w którym będą przechowywane obiekty blob wymagające przechowywania w stanie niezmiennym.
 Kontener musi być na koncie GPv2 lub usługi blob storage.
2. Wybierz **zasady dostępu** w ustawieniach kontenera. Następnie wybierz pozycję **+ Dodaj zasady** w obszarze **niezmienny magazyn obiektów blob**.

    ![Ustawienia kontenera w witrynie portal](media/storage-blob-immutable-storage/portal-image-1.png)

3. Aby włączyć na podstawie czasu przechowywania, wybierz **na podstawie czasu przechowywania** z menu rozwijanego.

    !["Czas przechowywania na podstawie" wybranego w obszarze "Zasady type"](media/storage-blob-immutable-storage/portal-image-2.png)

4. Wprowadź interwał przechowywania w dniach (wartość minimalna to jeden dzień).

    ![Pole "Okres przechowywania aktualizacji do"](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Początkowy stan zasad jest odblokowany, jak pokazano na zrzucie ekranu. Testowanie funkcji z mniejszych interwał przechowywania i wprowadzić zmiany do zasad, zanim je zablokować. Blokowanie ma zasadnicze znaczenie dla zgodności z przepisami, takich jak s 17a-4.

5. Zablokuj zasady. Kliknij prawym przyciskiem myszy wielokropek (**...** ), i zostanie wyświetlone następujące menu:

    !["Blokowanie zasad" w menu](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Wybierz **zasady blokowania**, a stan zasad jest teraz wyświetlany jako zablokowany. Po zasady zostaną zablokowane, nie można usunąć, i może być tylko rozszerzenia okres przechowywania.

6. Aby włączyć archiwizacją ze względów prawnych, zaznacz **+ Dodaj zasady**. Wybierz **prawnych** z menu rozwijanego.

    !["Ze względów prawnych" z menu "Typ zasad"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Utwórz prawnych z co najmniej jednego znacznika.

    ![Pole "Nazwa tagu" w obszarze Typ zasad](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

8. Aby wyczyścić prawnych, po prostu Usuń tag.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Funkcja jest dostępna w następujących grupach polecenia: `az storage container immutability-policy` i `az storage container legal-hold`. Uruchom `-h` je, aby wyświetlić polecenia.

### <a name="powershell"></a>PowerShell

[Program PowerShell w wersji 4.4.0-preview](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May20180) obsługuje magazyn niezmienne.
Aby włączyć tę funkcję, wykonaj następujące kroki:

1. Upewnij się, że masz najnowszą wersję zainstalowanego modułu PowerShellGet: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Usuń wszelkie poprzedniej instalacji programu Azure PowerShell.
3. Instalacja usługi AzureRM: `Install-Module AzureRM –Repository PSGallery –AllowClobber`. Azure można zainstalować podobnie z tego repozytorium.
4. Zainstaluj polecenia cmdlet płaszczyzny zarządzania magazynu w wersji zapoznawczej: `Install-Module -Name AzureRM.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`.

[PowerShell przykładowy kod](#sample-powershell-code) sekcję w dalszej części tego artykułu przedstawiono użycie funkcji.

## <a name="client-libraries"></a>Biblioteki klienta

Podanych niżej bibliotek klienta obsługują niezmienny magazyn dla usługi Azure Blob storage:

- [7.2.0-preview wersji biblioteki klienckiej platformy .NET i nowsze](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Biblioteki klienta node.js w wersji 4.0.0 lub nowszy](https://www.npmjs.com/package/azure-arm-storage)
- [Biblioteka klientów języka Python w wersji 2.0.0 w wersji Release Candidate 2 lub nowszy](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Biblioteki klienta Java](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="supported-values"></a>Obsługiwane wartości

- Interwał przechowywania minimalnej to jeden dzień. Maksymalna wartość to 400 lata.
- Dla konta magazynu maksymalną liczbę kontenerów przy użyciu zablokowane zasady niezmienne wynosi 1000.
- Dla konta magazynu maksymalną liczbę kontenerów z ustawieniem prawnych wynosi 1000.
- Dla kontenera maksymalną liczbę tagów prawnych wynosi 10.
- Maksymalna długość znacznika prawnych to 23 znaków alfanumerycznych. Minimalna długość wynosi trzy znaki.
- Dla kontenera maksymalna liczba rozszerzeń interwał przechowywania zablokowane zasady niezmienne to trzy.
- Dla kontenera za pomocą zasad niezmienne zablokowane maksymalnie pięć dzienników zasad przechowywania na podstawie czasu i maksymalnie 10 prawne naciśnij i przytrzymaj zasad, które dzienniki są przechowywane przez czas trwania kontenera.

## <a name="faq"></a>Często zadawane pytania

**Czy ta funkcja dotyczy tylko blokowe obiekty BLOB, lub na stronie obiektów blob i uzupełnialnych obiektów blob oraz?**

Niezmienny magazyn może być używany z żadnym typem obiektów blob, ale zaleca się używać przede wszystkim dla blokowych obiektów blob. Inaczej niż w przypadku blokowych obiektów blob strony obiekty BLOB i uzupełnialnych obiektów blob należy utworzyć poza kontenerem ROBAK, a następnie kopiowane w. Po skopiowaniu tych obiektów blob w kontenerze ROBAK nie dalsze *dołącza* do dołączania obiektu blob lub zmiany stronicowych obiektów blob są dozwolone.

**Czy w każdym przypadku należy utworzyć nowe konto magazynu, aby móc korzystać z tej funkcji?**

Za pomocą niezmienialnych magazynu z wszystkich istniejących i nowo utworzony ogólnego przeznaczenia w wersji 2 lub kont usługi Blob Storage. Ta funkcja jest dostępna tylko w przypadku magazynu obiektów Blob.

**Co się stanie, jeśli spróbuję usunąć kontener z *zablokowanymi* zasadami przechowywania na podstawie czasu lub z ustawionym stanem archiwizacji ze względów prawnych?**

Operacja usuwania kontenera nie powiedzie się, jeśli co najmniej jeden obiekt blob istnieje zasady przechowywania na podstawie czasu zablokowane lub prawnych. Operacja usuwania kontenera powiedzie się tylko wtedy, gdy istnieje nie obiektów blob, interwał przechowywania aktywne i nie ma żadnych archiwizacją ze względów prawnych. Obiekty BLOB należy usunąć przed usunięciem kontenera.

**Co się stanie, jeśli spróbuję usunąć konto magazynu zawierające kontener z zasadami WORM — *zablokowanymi* zasadami przechowywania na podstawie czasu lub ustawionym stanem archiwizacji ze względów prawnych?**

Usunięcie konta magazynu nie powiedzie się, jeśli zawiera ono co najmniej jeden kontener z zasadami WORM i ustawionym stanem archiwizacji ze względów prawnych lub co najmniej jeden obiekt blob z aktywnym okresem przechowywania.  Należy usunąć wszystkie kontenery ROBAK, zanim będzie możliwe usunięcie konta magazynu. Instrukcje dotyczące usuwania kontenera zobacz poprzedni pytanie.

**Czy mogę przenosić dane pomiędzy warstwami magazynowania (gorącą, chłodną, zimną), gdy obiekt blob znajduje się w stanie niezmiennym?**

Tak, podczas przechowywania danych w stanie niezmiennym można przenosić je pomiędzy warstwami magazynowania obiektów blob za pomocą polecenia Set Blob Tier. Niezmienny magazyn jest obsługiwany na gorąca, chłodna i archiwalna obiektu blob.

**Co się stanie, jeśli nie uiszczę opłaty, a okres przechowywania jeszcze nie wygasł?**

W przypadku płatności zasady przechowywania zwykłych danych będzie stosowana zgodnie z przepisami w warunki i postanowienia Umowy z firmą Microsoft.

**Czy jest oferowany okres próbny, umożliwiający przetestowanie tej funkcji?**

Tak. Podczas tworzenia zasady przechowywania na podstawie czasu jest *odblokowane* stanu. W tym stanie można żądane zmiany na okres przechowywania, np. zwiększenie lub zmniejszenie i nawet usunąć zasady. Po zablokowaniu zasady pozostaje on zablokowany do momentu wygaśnięcia ważności okres przechowywania. Zapobiega to usunięcia i modyfikacji okres przechowywania. Zdecydowanie zalecamy użycie *odblokowane* stanu tylko do celów wersji próbnej i Zablokuj zasady w okresie 24-godzinnym. Poniższe wskazówki ułatwiają 17a-4(f) s i innych przepisów.

**Czy ta funkcja jest dostępna w chmurach krajowych i rządowych?**

Niezmienne storage jest obecnie dostępna tylko w regionach świadczenia publicznej usługi Azure. Jeśli interesuje Cię określonej chmury krajowe, Wyślij wiadomość e-mail azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Przykładowy kod programu PowerShell

Przykładowy skrypt programu PowerShell jest dla odwołania. Ten skrypt tworzy nowe konto magazynu i kontener. Go następnie pokazano, jak ustawić i wyczyść archiwizacją ze względów prawnych, Utwórz zablokować zasady przechowywania na podstawie czasu (znanego również jako zasady niezmienności) i wydłużyć okres przechowywania.

Skonfiguruj i przetestuj konta usługi Azure Storage:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzureRMAccount
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzureRmResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzureRmStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzureRmStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzureRmStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzureRmStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzureRmStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzureRmStorageContainer -InputObject $containerObject2
```

Ustawić lub wyczyścić archiwizacją ze względów prawnych:

```powershell
# Set a legal hold
Add-AzureRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzureRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzureRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzureRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzureRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzureRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Utwórz lub zaktualizuj zasady niezmienności:
```powershell
# with an account name or container name
Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Pobierz zasady niezmienności:
```powershell
# Get an immutability policy
Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzureRmStorageContainerImmutabilityPolicy -Container $containerObject
```

Zablokuj zasady niezmienności (Dodaj - Force, aby zamknąć wiersz):
```powershell
# with an immutability policy object
$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Rozszerz zasady niezmienności:
```powershell

# with an immutability policy object
$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Usuń zasady niezmienności (Dodaj - Force, aby zamknąć wiersz):
```powershell
# with an immutability policy object
$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzureRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
