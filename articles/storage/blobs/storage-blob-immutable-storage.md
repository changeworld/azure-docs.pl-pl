---
title: Funkcja magazynu niezmiennego usługi Azure Blob Storage (wersja zapoznawcza) | Microsoft Docs
description: W usłudze Azure Storage jest teraz obsługiwane przechowywanie obiektów Blob w stanie WORM, czyli stanie uniemożliwiającym usunięcie lub zmodyfikowanie danych przez czas określony przez użytkownika. Ta funkcja umożliwia organizacjom działającym w wielu regulowanych branżach, zwłaszcza w branży brokerskiej, przechowywanie danych w sposób zgodny z amerykańskimi przepisami SEC 17a-4(f) i innymi wymogami prawnymi.
services: storage
author: sangsinh
manager: twooley
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: sangsinh
ms.openlocfilehash: 195537b271c442b954d6d6e6fa8d1491c07822e8
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970248"
---
# <a name="immutable-storage-feature-of-azure-blob-storage-preview"></a>Funkcja magazynu niezmiennego usługi Azure Blob Storage (wersja zapoznawcza)

Funkcja magazynu niezmiennego w usłudze Azure Blob Storage umożliwia użytkownikom przechowywanie danych krytycznych dla działania firmy w stanie WORM („Write Once Read Many” — jednorazowy zapis i wielokrotny odczyt) w usłudze Azure Blob Storage. Danych w tym stanie nie można usunąć ani zmodyfikować przez czas określony przez użytkownika. Przez cały okres przechowywania można tworzyć i odczytywać obiekty blob, ale nie można ich modyfikować ani usuwać.

## <a name="overview"></a>Omówienie

Funkcja magazynu niezmiennego umożliwia organizacjom działającym w wielu regulowanych branżach, zwłaszcza w branży brokerskiej, przechowywanie danych w sposób zgodny z amerykańskimi przepisami SEC 17a-4(f) i innymi wymogami prawnymi.

Typowe zastosowania tej funkcji to:

- **Zapewnienie zgodności z przepisami**: funkcja magazynu niezmiennego w usłudze Azure Blob Storage została opracowana, aby ułatwić instytucjom finansowym i podmiotom działającym w pokrewnych branżach zapewnienie zgodności z przepisami takimi jak SEC 17a-4(f), CFTC 1.31(c)-(d), FINRA itp.

- **Bezpieczne przechowywanie dokumentów**: dzięki tej funkcji usługi Blob Storage użytkownicy uzyskują maksymalną ochronę danych, gwarantującą, że dane nie zostaną zmienione ani usunięte przez żadnego użytkownika, nawet korzystającego z konta z uprawnieniami administratora.

- **Archiwizacja ze względów prawnych**: funkcja magazynu niezmiennego w usłudze Azure Blob Storage umożliwia użytkownikom przechowywanie poufnych informacji istotnych dla postępowań, procesów sądowych itp. w sposób uniemożliwiający ich naruszenie przez wymagany czas.

Funkcja magazynu niezmiennego zapewnia:

- **Obsługę zasad przechowywania na podstawie czasu:** użytkownicy mogą konfigurować zasady przechowywania danych przez określony czas.

- **Obsługę zasad archiwizacji ze względów prawnych:** jeśli okres przechowywania nie jest z góry znany, użytkownicy mogą użyć funkcji archiwizacji ze względów prawnych, aby przechowywać dane w stanie niezmiennym do momentu usunięcia stanu archiwizacji.  Po ustawieniu archiwizacji ze względów prawnych można tworzyć i odczytywać obiekty blob, ale nie można ich modyfikować ani usuwać. Każde archiwum prawne jest skojarzone ze zdefiniowanym przez użytkownika tagiem alfanumerycznym, używanym jako ciąg identyfikatora (może to być na przykład numer sprawy).

- **Obsługę wszystkich warstw magazynowania obiektów blob:** zasady WORM są niezależne od warstwy magazynowania w usłudze Azure Blob Storage i są stosowane do wszystkich warstw: gorącej, chłodnej i archiwum. Dzięki temu użytkownicy mogą przechowywać dane w warstwie najbardziej ekonomicznej w przypadku ich obciążeń, jednocześnie gwarantując ich niezmienność.

- **Konfigurację na poziomie kontenera:** funkcja magazynu niezmiennego umożliwia użytkownikom konfigurowanie zasad przechowywania na podstawie czasu i tagów archiwizacji ze względów prawnych na poziomie kontenera.  Użytkownicy mogą tworzyć i blokować zasady przechowywania na podstawie czasu, wydłużać okresy przechowywania, ustawiać i usuwać stan archiwizacji ze względów prawnych itp. przy użyciu prostych ustawień na poziomie kontenera.  Te zasady będą stosowane do wszystkich obiektów blob w kontenerze — zarówno istniejących, jak i nowych.

- **Obsługę rejestrowania inspekcji:** każdy kontener zawiera dziennik inspekcji zawierający maksymalnie pięć poleceń dotyczących przechowywania na podstawie czasu w przypadku zablokowanych zasad przechowywania na podstawie czasu oraz maksymalnie trzy wpisy dotyczące wydłużeń okresu przechowywania.  W przypadku przechowywania na podstawie czasu dziennik zawiera identyfikator użytkownika, typ polecenia, znaczniki czasu oraz okres przechowywania. W przypadku archiwizacji ze względów prawnych dziennik zawiera identyfikator użytkownika, typ polecenia, znaczniki czasu oraz tagi archiwizacji ze względów prawnych. Dziennik jest przechowywany przez cały okres istnienia kontenera, zgodnie z wymogami SEC 17a-4(f). Bardziej szczegółowy rejestr wszystkich działań warstwy kontroli zawiera [Dziennik aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Przechowywanie tych dzienników w sposób trwały, zgodnie z obowiązującymi wymogami prawnymi lub innymi, należy do obowiązków użytkownika.

 Ta funkcja jest dostępna we wszystkich publicznych regionach świadczenia usługi Azure.

## <a name="how-it-works"></a>Jak to działa

Funkcja magazynu niezmiennego w usłudze Azure Blob Storage obsługuje dwa rodzaje zasad WORM (nazywanych też zasadami magazynu niezmiennego): zasady przechowywania na podstawie czasu i zasady archiwizacji ze względów prawnych. Zobacz sekcję [Wprowadzenie](#Getting-started), aby uzyskać szczegółowe informacje dotyczące tworzenia tych zasad magazynu niezmiennego.
Po zastosowaniu zasad przechowywania na podstawie czasu lub archiwizacji ze względów prawnych w kontenerze wszystkie istniejące obiekty blob przejdą w stan niezmienny (uniemożliwiający zapisywanie i usuwanie). Również wszystkie nowe obiekty blob przekazane do kontenera przejdą w stan niezmienny.

> [!IMPORTANT]
> Aby obiekt blob był w stanie niezmiennym (uniemożliwiającym zapisywanie i usuwanie) zgodnie z wymogami SEC 17a-4(f) i innych przepisów, zasady przechowywania na podstawie czasu muszą być *zablokowane*. Zalecane jest zablokowanie zasad w rozsądnym czasie, na ogół w ciągu 24 godzin. Nie zalecamy korzystania ze stanu *odblokowanego* do żadnych celów innych niż krótkoterminowe testy funkcji.

 Po zastosowaniu zasad przechowywania na podstawie czasu w kontenerze wszystkie obiekty blob w tym kontenerze pozostaną w stanie niezmiennym przez cały *obowiązujący* okres przechowywania. Obowiązujący okres przechowywania w przypadku istniejących obiektów blob to różnica między czasem utworzenia obiektu blob a określonym przez użytkownika okresem przechowywania. W przypadku nowych obiektów blob obowiązujący okres przechowywania jest równy okresowi przechowywania określonemu przez użytkownika. Ponieważ użytkownicy mogą zmieniać okres przechowywania, obowiązujący okres przechowywania jest obliczany na podstawie ostatniej wartości okresu przechowywania ustawionej przez użytkownika.

> [!TIP]
> Na przykład: użytkownik tworzy zasady przechowywania na podstawie czasu z pięcioletnim okresem przechowywania.
> W kontenerze znajduje się jeden obiekt blob o nazwie testblob1, utworzony rok temu. Obowiązujący okres przechowywania obiektu testblob1 wyniesie cztery lata.
> Do kontenera zostaje przekazany nowy obiekt blob, testblob2. Obowiązujący okres przechowywania nowego obiektu blob wyniesie pięć lat.

### <a name="legal-holds"></a>Archiwizacja ze względów prawnych

W przypadku archiwizacji ze względów prawnych wszystkie istniejące i nowe obiekty blob pozostaną w stanie niezmiennym do momentu usunięcia stanu archiwizacji.
Więcej informacji dotyczących ustawiania i usuwania stanu archiwizacji ze względów prawnych znajdziesz w sekcji [Wprowadzenie](#Getting-started).

W kontenerze mogą być jednocześnie stosowane zasady przechowywania na podstawie czasu i archiwizacja ze względów prawnych. Wszystkie obiekty blob w tym kontenerze pozostaną w stanie niezmiennym do momentu usunięcia stanu archiwizacji ze względów prawnych nawet po upływie obowiązującego okresu przechowywania. I odwrotnie — obiekty blob pozostaną w stanie niezmiennym przez cały obowiązujący okres przechowywania nawet po usunięciu stanu archiwizacji ze względów prawnych.
W poniższej tabeli przedstawiono typy operacji na obiektach blob, których nie można wykonywać w poszczególnych przypadkach stosowania stanu niezmiennego.
Aby uzyskać szczegółowe informacje na temat interfejsu API REST dla obiektów blob, zapoznaj się z dokumentacją [interfejsów API usługi Azure Blob Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

|Scenariusz  |Stan obiektu blob  |Niedozwolone operacje na obiektach blob  |
|---------|---------|---------|
|Trwa obowiązujący okres przechowywania obiektu blob i/lub ustawiono stan archiwizacji ze względów prawnych     |Niezmienny: ochrona przed usuwaniem i zapisem         |Delete Container, Delete Blob, Put Blob1, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Upłynął obowiązujący okres przechowywania obiektu blob     |Ochrona tylko przed zapisem (operacje usuwania są dozwolone)         |Put Blob, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Stan archiwizacji ze względów prawnych został usunięty i nie ustawiono żadnych zasad przechowywania na podstawie czasu w kontenerze     |Modyfikowalny         |Brak         |
|Nie utworzono żadnych zasad WORM (przechowywania na podstawie czasu ani archiwizacji ze względów prawnych)     |Modyfikowalny         |Brak         |

> [!NOTE]
> W pierwszych dwóch scenariuszach wymienionych w powyższej tabeli dozwolone jest pierwsze użycie operacji Put Blob, Put Block List i Put Block w celu utworzenia obiektu blob, ale wszystkie kolejne operacje tego typu są niedozwolone.
> Funkcja magazynu niezmiennego jest dostępna tylko w przypadku kont ogólnego przeznaczenia w wersji 2 (GPv2) oraz kont usługi Blob Storage i wymaga utworzenia za pośrednictwem usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="pricing"></a>Cennik

Korzystanie z tej funkcji nie wiąże się z dodatkowym opłatami, a opłaty za dane w magazynie niezmiennym są obliczane tak samo, jak opłaty za zwykłe, modyfikowalne dane. Szczegółowe informacje o cenach są dostępne na [stronie cennika usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="restrictions"></a>Ograniczenia

W publicznej wersji zapoznawczej obowiązują następujące ograniczenia:

- **Nie należy przechowywać danych produkcyjnych ani danych krytycznych dla działania firmy**
- Obowiązują wszystkie ograniczenia wersji zapoznawczej i umów o zachowaniu poufności

## <a name="getting-started"></a>Wprowadzenie

Funkcja magazynu niezmiennego usługi Azure Blob Storage jest obsługiwana w najnowszych wersjach [witryny Azure Portal](http://portal.azure.com), [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) oraz programu [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May2018)

### <a name="azure-portal"></a>Azure Portal

1. Utwórz nowy kontener lub wybierz istniejący kontener, w którym będą przechowywane obiekty blob wymagające przechowywania w stanie niezmiennym.
 Kontener musi znajdować się na koncie magazynu w wersji GPv2.
2. Kliknij pozycję Zasady dostępu w ustawieniach kontenera, a następnie kliknij pozycję **+ Dodaj zasady** w sekcji zasad **Niezmienny magazyn obiektów blob**, jak pokazano poniżej.

    ![Portal](media/storage-blob-immutable-storage/portal-image-1.png)

3. Aby włączyć zasady przechowywania na podstawie czasu, wybierz pozycję Przechowywanie na podstawie czasu z menu rozwijanego.

    ![Przechowywanie](media/storage-blob-immutable-storage/portal-image-2.png)

4. Wprowadź odpowiedni okres przechowywania w dniach (wartość minimalna to jeden dzień)

    ![Okres przechowywania](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Jak widać powyżej, na początku zasady mają stan odblokowany. Dzięki temu możesz przetestować tę funkcję z krótszym okresem przechowywania, a następnie wprowadzić wymagane zmiany zasad przed ich zablokowaniem. Blokowanie jest niezbędne do zapewnienia zgodności z przepisami SEC 17a-4 i podobnymi.

5. Aby zablokować zasady, kliknij prawym przyciskiem pozycję ..., co spowoduje wyświetlenie następującego menu:

    ![Blokowanie zasad](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Kliknij pozycję Zablokuj zasady. Zobaczysz, że stan zasad zmienił się na zablokowany. Po zablokowaniu zasad nie można już ich usunąć — będzie możliwe tylko przedłużenie okresu przechowywania.

6. Aby włączyć archiwizację ze względów prawnych, kliknij pozycję + Dodaj zasady, a następnie wybierz pozycję Archiwizacja ze względów prawnych z menu rozwijanego.

    ![Archiwizacja ze względów prawnych](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Ustaw stan archiwizacji ze względów prawnych z jednym lub kilkoma tagami.

    ![Ustawianie tagów archiwizacji ze względów prawnych](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

### <a name="cli-20"></a>Interfejs wiersza polecenia 2.0

Zainstaluj [rozszerzenie interfejsu wiersza polecenia](http://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), używając polecenia `az extension add -n storage-preview`.

Jeśli rozszerzenie jest już zainstalowane, użyj następującego polecenia, aby włączyć funkcję magazynu niezmiennego: `az extension update -n storage-preview`

Do obsługi tej funkcji używane są następujące grupy poleceń (uruchom je z parametrem „-h”, aby wyświetlić polecenia): `az storage container immutability-policy` oraz `az storage container legal-hold`.

### <a name="powershell"></a>PowerShell

Funkcja magazynu niezmiennego jest dostępna w programie [PowerShell 4.4.0 w wersji zapoznawczej](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May20180).
Aby włączyć tę funkcję, wykonaj następujące czynności:

1. Upewnij się, że masz zainstalowaną najnowszą wersję programu PowerShell, używając polecenia `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Usuń wszelkie starsze instalacje programu Azure PowerShell.
3. Zainstaluj moduł AzureRM (podobnie można zainstalować platformę Azure z tego repozytorium) `Install-Module AzureRM –Repository PSGallery –AllowClobber`.
4. Zainstaluj wersję zapoznawczą poleceń cmdlet warstwy zarządzania usługi Storage `Install-Module -Name AzureRM.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`.

Niżej znajduje się przykładowy kod programu PowerShell przedstawiający sposób użycia tej funkcji.

## <a name="client-libraries"></a>Biblioteki klienta

Funkcja magazynu niezmiennego usługi Azure Blob Storage jest obsługiwana w przypadku następujących wersji bibliotek klienta:

- [Biblioteka klienta .NET (wersja zapoznawcza 7.2.0 lub nowsza](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Biblioteka klienta node.js (wersja 4.0.0 lub nowsza)](https://www.npmjs.com/package/azure-arm-storage)
- [Biblioteka klienta Python (wersja 2.0.0 Release Candidate 2 lub nowsza)](https://pypi.org/project/azure-mgmt-storage/2.0.0rc1/)

## <a name="supported-values"></a>Obsługiwane wartości

- Minimalny okres przechowywania wynosi jeden dzień, maksymalny — 400 lat.
- Maksymalna liczba kontenerów z zablokowanymi zasadami magazynu niezmiennego na jednym koncie magazynu wynosi 1000.
- Maksymalna liczba kontenerów z ustawionym stanem archiwizacji ze względów prawnych na jednym koncie magazynu wynosi 1000.
- Maksymalna liczba tagów archiwizacji ze względów prawnych w jednym kontenerze wynosi 10.
- Maksymalna długość tagu archiwizacji ze względów prawnych wynosi 23 znaki alfanumeryczne, minimalna — trzy.
- W ramach jednego kontenera można wydłużyć okres przechowywania dla zablokowanych zasad magazynu niezmiennego maksymalnie trzy razy.
- W ramach jednego kontenera z zablokowanymi zasadami magazynu niezmiennego można przechowywać maksymalnie pięć dzienników zasad przechowywania na podstawie czasu i maksymalnie 10 dzienników zasad archiwizacji ze względów prawnych; są one przechowywane przez cały okres istnienia kontenera.

## <a name="faq"></a>Często zadawane pytania

**Czy ta funkcja dotyczy tylko blokowych obiektów blob, czy także stronicowych i uzupełnialnych obiektów blob?**

Funkcja magazynu niezmiennego może być stosowana z obiektami blob dowolnego typu.  Należy jednak pamiętać, że zalecane jest używanie jej przede wszystkim w przypadku blokowych obiektów blob. Inaczej niż blokowe obiekty blob, stronicowe i uzupełnialne obiekty blob należy najpierw utworzyć poza kontenerem z zasadami WORM, a następnie je do niego skopiować.  Po skopiowaniu do kontenera z zasadami WORM nie będzie można *uzupełniać* uzupełnialnego obiektu blob ani wprowadzać zmian w stronicowym obiekcie blob.

**Czy w każdym przypadku należy utworzyć nowe konto magazynu, aby móc korzystać z tej funkcji?**

Można używać funkcji magazynu niezmiennego na wszystkich istniejących kontach typu GPv2 oraz na nowych kontach magazynu, o ile typ konta to GPv2. Ta funkcja jest dostępna tylko w przypadku usługi Blob Storage.

**Co się stanie, jeśli spróbuję usunąć kontener z *zablokowanymi* zasadami przechowywania na podstawie czasu lub z ustawionym stanem archiwizacji ze względów prawnych?**

Operacja usuwania kontenera nie powiedzie się, jeśli kontener zawiera co najmniej jeden obiekt blob z zablokowanymi zasadami przechowywania na podstawie czasu lub ustawionym stanem archiwizacji ze względów prawnych. Operacja usuwania kontenera powiedzie się, jeśli w kontenerze nie ma żadnych obiektów blob z aktywnym okresem przechowywania, ani ustawionego stanu archiwizacji ze względów prawnych. Przed usunięciem kontenera należy najpierw usunąć obiekty blob.

**Co się stanie, jeśli spróbuję usunąć konto magazynu zawierające kontener z zasadami WORM — *zablokowanymi* zasadami przechowywania na podstawie czasu lub ustawionym stanem archiwizacji ze względów prawnych?**

Usunięcie konta magazynu nie powiedzie się, jeśli zawiera ono co najmniej jeden kontener z zasadami WORM i ustawionym stanem archiwizacji ze względów prawnych lub co najmniej jeden obiekt blob z aktywnym okresem przechowywania.  Przed usunięciem konta magazynu należy najpierw usunąć wszystkie kontenery z zasadami WORM.  Aby uzyskać informacje dotyczące usuwania kontenerów, zobacz pytanie nr 2.

**Czy mogę przenosić dane pomiędzy warstwami magazynowania (gorącą, chłodną, zimną), gdy obiekt blob znajduje się w stanie niezmiennym?**

Tak, podczas przechowywania danych w stanie niezmiennym można przenosić je pomiędzy warstwami magazynowania obiektów blob za pomocą polecenia Set Blob Tier. Funkcja magazynu niezmiennego jest obsługiwana we wszystkich warstwach magazynowania: gorącej, chłodnej i zimnej.

**Co się stanie, jeśli nie uiszczę opłaty, a okres przechowywania jeszcze nie wygasł?**

W przypadku braku płatności będą stosowane zwykłe zasady przechowywania danych zgodnie z zasadami okresu prolongaty określonymi w umowie z firmą Microsoft.

**Czy jest oferowany okres próbny, umożliwiający przetestowanie tej funkcji?**

Tak, nowo utworzone zasady przechowywania mają stan *odblokowany*. W tym stanie można wprowadzać wszelkie zmiany okresu przechowywania, na przykład wydłużyć go lub skrócić, a nawet usunąć zasady. Po zablokowaniu zasady pozostają zablokowane na zawsze, co uniemożliwia ich usunięcie. Ponadto po zablokowaniu zasad nie można skrócić okresu przechowywania. Zdecydowanie zalecamy korzystanie ze stanu *odblokowanego* wyłącznie do celów testowych i zablokowanie zasad przed upływem 24 godzin, aby uniknąć ryzyka niezgodności z przepisami SEC 17a-4(f) i innymi.

**Czy ta funkcja jest dostępna w chmurach krajowych i rządowych?**

Funkcja magazynu niezmiennego jest obecnie dostępna wyłącznie w publicznych regionach świadczenia usługi Azure. Jeśli interesuje Cię konkretna chmura krajowa, wyślij wiadomość e-mail na adres azurestoragefeedback@microsoft.com.

## <a name="sample-code"></a>Przykładowy kod

Poniżej przedstawiono przykładowy, referencyjny skrypt programu PowerShell.
Ten skrypt obejmuje tworzenie nowego konta magazynu i kontenera oraz czynności takie jak ustawianie i usuwanie stanu archiwizacji ze względów prawnych, tworzenie i blokowanie zasad przechowywania na podstawie czasu (ImmutabilityPolicy), wydłużanie okresu przechowywania itp.

```powershell
\$ResourceGroup = "\<Enter your resource group\>”

\$StorageAccount = "\<Enter your storage account name\>"

\$container = "\<Enter your container name\>"

\$container2 = "\<Enter another container name\>”

\$location = "\<Enter the storage account location\>"

\# Login to the Azure Resource Manager Account

Login-AzureRMAccount

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage"

\# Create your Azure Resource Group

New-AzureRmResourceGroup -Name \$ResourceGroup -Location \$location

\# Create your Azure storage account

New-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup -StorageAccountName
\$StorageAccount -SkuName Standard_LRS -Location \$location -Kind Storage

\# Create a new container

New-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Create Container 2 with Storage Account object

\$accountObject = Get-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

New-AzureRmStorageContainer -StorageAccount \$accountObject -Name \$container2

\# Get container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Get Container with Account object

\$containerObject = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container

\#list container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

\#remove container (Add -Force to dismiss prompt)

Remove-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container2

\#with Account object

Remove-AzureRmStorageContainer -StorageAccount \$accountObject -Name
\$container2

\#with Container object

\$containerObject2 = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container2

Remove-AzureRmStorageContainer -InputObject \$containerObject2

\#Set LegalHold

Add-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag1,tag2

\#with Account object

Add-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3

\#with Container object

Add-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4,tag5

\#Clear LegalHold

Remove-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag2

\#with Account object

Remove-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3,tag5

\#with Container object

Remove-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4

\# create/update ImmutabilityPolicy

\#\# with account/container name

Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 10

\#with Account object

Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -ImmutabilityPeriod 1 -Etag \$policy.Etag

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 7

\#\# with ImmutabilityPolicy object

Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy
-ImmutabilityPeriod 5

\#get ImmutabilityPolicy

Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container

\#with Account object

Get-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container

\#with Container object

Get-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject

\#Lock ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -force

\#\# with account/container name

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -Etag \$policy.Etag

\#with Container object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -Etag \$policy.Etag -force

\#Extend ImmutabilityPolicy

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -ImmutabilityPeriod 11 -ExtendPolicy

\#\# with account/container name

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 11 -Etag \$policy.Etag -ExtendPolicy

\#with Account object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -ImmutabilityPeriod 12 -Etag
\$policy.Etag -ExtendPolicy

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 13 -Etag \$policy.Etag -ExtendPolicy

\#Remove ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

Remove-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy

\#\# with account/container name

Remove-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

Remove-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -Etag \$policy.Etag

\#with Container object

Remove-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject
-Etag \$policy.Etag
```