---
title: Instalowanie pakietów aplikacji w węzłach obliczeniowych — Usługa Azure Batch | Dokumenty firmy Microsoft
description: Użyj funkcji pakietów aplikacji usługi Azure Batch, aby łatwo zarządzać wieloma aplikacjami i wersjami do instalacji w węzłach obliczeniowych wsadowych.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30301832381bdc7b5f001eec2c449c571f9fd671
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086223"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch

Funkcja pakietów aplikacji usługi Azure Batch zapewnia łatwe zarządzanie aplikacjami zadań i ich wdrażanie w węzłach obliczeniowych w puli. Za pomocą pakietów aplikacji można przekazywać i zarządzać wieloma wersjami aplikacji uruchamianych za zadania, w tym ich plikami pomocniczymi. Następnie można automatycznie wdrożyć jedną lub więcej z tych aplikacji do węzłów obliczeniowych w puli.

W tym artykule dowiesz się, jak przekazać pakiety aplikacji i zarządzać nimi w witrynie Azure portal. Następnie dowiesz się, jak zainstalować je w węzłach obliczeniowych puli za pomocą biblioteki [Batch .NET.][api_net]

> [!NOTE]
> Pakiety aplikacji są obsługiwane we wszystkich pulach usługi Batch utworzonych po 5 lipca 2017 r. W pulach usługi Batch utworzonych między 10 marca 2016 r. a 5 lipca 2017 r. są one obsługiwane tylko w przypadku, gdy pula została utworzona za pomocą konfiguracji usługi w chmurze. Pule usługi Batch utworzone przed 10 marca 2016 r. nie obsługują pakietów aplikacji.
>
> Interfejsy API do tworzenia pakietów aplikacji i zarządzania nimi są częścią biblioteki [.NET usługi zarządzania wsadowymi.][api_net_mgmt] Interfejsy API do instalowania pakietów aplikacji w węźle obliczeniowym są częścią biblioteki [Batch .NET.][api_net] Porównywalne funkcje znajdują się w dostępnych interfejsach API partii dla innych języków. 
>
> Opisana w tym miejscu funkcja pakietów aplikacji zastępuje funkcję aplikacji wsadowych dostępną w poprzednich wersjach usługi.

## <a name="application-package-requirements"></a>Wymagania dotyczące pakietu aplikacji
Aby korzystać z pakietów aplikacji, należy [połączyć konto usługi Azure Storage](#link-a-storage-account) z kontem usługi Batch.

## <a name="about-applications-and-application-packages"></a>Informacje o aplikacjach i pakietach aplikacji
W ramach usługi Azure Batch *aplikacja* odwołuje się do zestawu plików binarnych wersjonowanych, które można automatycznie pobrać do węzłów obliczeniowych w puli. *Pakiet aplikacji* odwołuje się do *określonego zestawu* tych plików binarnych i reprezentuje daną *wersję* aplikacji.

![Diagram wysokiego poziomu aplikacji i pakietów aplikacji][1]

### <a name="applications"></a>Aplikacje
Aplikacja w uziemiać zawiera jeden lub więcej pakietów aplikacji i określa opcje konfiguracji aplikacji. Na przykład aplikacja może określić domyślną wersję pakietu aplikacji do zainstalowania w węzłach obliczeniowych i czy jej pakiety mogą być aktualizowane lub usuwane.

### <a name="application-packages"></a>Pakiety aplikacji
Pakiet aplikacji to plik zip, który zawiera pliki binarne aplikacji i pliki pomocnicze, które są wymagane do uruchomienia aplikacji przez zadania. Każdy pakiet aplikacji reprezentuje określoną wersję aplikacji.

Można określić pakiety aplikacji na poziomie puli i zadań. Można określić jeden lub więcej z tych pakietów i (opcjonalnie) wersję podczas tworzenia puli lub zadania.

* **Pakiety aplikacji puli** są wdrażane w *każdym* węźle w puli. Aplikacje są wdrażane, gdy węzeł łączy pulę i gdy jest ponownie uruchomiony lub ponownie zaimaged.
  
    Pakiety aplikacji puli są odpowiednie, gdy wszystkie węzły w puli wykonują zadania zadania. Podczas tworzenia puli można określić jeden lub więcej pakietów aplikacji, a także dodać lub zaktualizować pakiety istniejącej puli. Jeśli zaktualizujesz pakiety aplikacji istniejącej puli, należy ponownie uruchomić jego węzły, aby zainstalować nowy pakiet.
* **Pakiety aplikacji zadań** są wdrażane tylko w węźle obliczeniowym zaplanowanym do uruchomienia zadania, tuż przed uruchomieniem wiersza polecenia zadania. Jeśli określony pakiet aplikacji i wersja jest już w węźle, nie jest ponownie rozmieszczony i używany jest istniejący pakiet.
  
    Pakiety aplikacji zadań są przydatne w środowiskach puli udostępnionej, gdzie różne zadania są uruchamiane w jednej puli, a pula nie jest usuwana po zakończeniu zadania. Jeśli liczba zadań podrzędnych w zadaniu jest mniejsza niż liczba węzłów w puli, pakiety aplikacji zadania podrzędnego mogą minimalizować ilość transferowanych danych, ponieważ aplikacja jest wdrażana tylko dla węzłów, w których odbywa się uruchamianie zadań podrzędnych.
  
    Inne scenariusze, które mogą korzystać z pakietów aplikacji zadań są zadania, które uruchamiają dużą aplikację, ale tylko dla kilku zadań. Na przykład etap przetwarzania wstępnego lub zadanie scalania, w którym aplikacja przetwarzania wstępnego lub scalania jest ciężka, może korzystać z używania pakietów aplikacji zadań.

> [!IMPORTANT]
> Istnieją ograniczenia dotyczące liczby aplikacji i pakietów aplikacji w ramach konta batch i maksymalnego rozmiaru pakietu aplikacji. Aby uzyskać szczegółowe informacje na temat tych [limitów, zobacz Przydziały i limity dla usługi Azure Batch.](batch-quota-limit.md)
> 
> 

### <a name="benefits-of-application-packages"></a>Korzyści z pakietów aplikacji
Pakiety aplikacji można uprościć kod w rozwiązaniu usługi Batch i zmniejszyć obciążenie wymagane do zarządzania aplikacjami, które są uruchamiane zadania.

W pakietach aplikacji zadanie uruchamiania puli nie musi określać długiej listy plików poszczególnych zasobów do zainstalowania w węzłach. Nie trzeba ręcznie zarządzać wieloma wersjami plików aplikacji w usłudze Azure Storage lub w węzłach. Nie musisz też martwić się o generowanie [adresów URL SYGNAtury dostępu,](../storage/common/storage-dotnet-shared-access-signature-part-1.md) aby zapewnić dostęp do plików na koncie Magazynu. Usługa Batch działa w tle z usługą Azure Storage do przechowywania pakietów aplikacji i wdrażania ich w węzłach obliczeniowych.

> [!NOTE] 
> Całkowity rozmiar zadania podrzędnego uruchamiania musi wynosić 32 768 znaków, w tym pliki zasobów lub zmienne środowiskowe, lub być mniejszy. Jeśli zadanie startowe przekracza ten limit, inną opcją jest użycie pakietów aplikacji. Można również utworzyć spakowane archiwum zawierające pliki zasobów, przekazać go jako obiekt blob do usługi Azure Storage, a następnie rozpakować go z wiersza polecenia zadania początkowego. 
>
>

## <a name="upload-and-manage-applications"></a>Przesyłanie aplikacji i zarządzanie nimi
Za pomocą [witryny Azure portal][portal] lub interfejsów API zarządzania wsadem można zarządzać pakietami aplikacji na koncie usługi Batch. W następnych kilku sekcjach najpierw pokazujemy, jak połączyć konto magazynu, a następnie omówimy dodawanie aplikacji i pakietów oraz zarządzanie nimi za pomocą portalu.

### <a name="link-a-storage-account"></a>Łączenie konta magazynu
Aby korzystać z pakietów aplikacji, należy najpierw połączyć [konto usługi Azure Storage](batch-api-basics.md#azure-storage-account) z kontem usługi Batch. Jeśli konto usługi Storage nie zostało jeszcze skonfigurowane, portal Azure wyświetla ostrzeżenie przy pierwszym kliknięciu **aplikacji** na koncie usługi Batch.



![Ostrzeżenie "Brak skonfigurowania konta magazynu" w witrynie Azure portal][9]

Usługa Batch używa skojarzonego konta Magazynu do przechowywania pakietów aplikacji. Po połączeniu dwóch kont usługa Batch może automatycznie wdrażać pakiety przechowywane na połączonym koncie magazynu w węzłach obliczeniowych. Aby połączyć konto magazynu z kontem usługi Batch, kliknij pozycję **Konto magazynu** w oknie **Ostrzeżenie,** a następnie ponownie kliknij pozycję **Konto magazynu.**

![Wybieranie bloku konta magazynu w witrynie Azure portal][10]

Zaleca się utworzenie konta magazynu *specjalnie* do użytku z kontem usługi Batch i wybranie go w tym miejscu. Po utworzeniu konta magazynu można je połączyć z kontem usługi Batch za pomocą okna **Konto magazynu.**

> [!IMPORTANT] 
> - Obecnie nie można używać pakietów aplikacji z kontem usługi Azure Storage skonfigurowanym z [regułami zapory](../storage/common/storage-network-security.md).
> - Nie można używać konta usługi Azure Storage z **hierarchicznym obszarem nazw** ustawionym na **Włączone.**

Usługa Batch używa usługi Azure Storage do przechowywania pakietów aplikacji jako blokowych obiektów blob. Są [naliczane normalnie][storage_pricing] dla danych bloku obiektu blob, a rozmiar każdego pakietu nie może przekroczyć maksymalny rozmiar bloku obiektu blob. Aby uzyskać więcej informacji, zobacz [Cele skalowalności i wydajności usługi Azure Storage dla kont magazynu](../storage/blobs/scalability-targets.md). Należy wziąć pod uwagę rozmiar i liczbę pakietów aplikacji i okresowo usuwać przestarzałe pakiety, aby zminimalizować koszty.

### <a name="view-current-applications"></a>Wyświetlanie bieżących aplikacji
Aby wyświetlić aplikacje na koncie usługi Batch, kliknij pozycję menu **Aplikacje** w menu po lewej stronie podczas wyświetlania **konta usługi Batch**.

![Kafelek aplikacji][2]

Wybranie tej opcji menu powoduje otwarcie okna **Aplikacje:**

![Wyświetlanie listy aplikacji][3]

W tym oknie jest wyświetlany identyfikator każdej aplikacji na koncie oraz następujące właściwości:

* **Pakiety**: Liczba wersji skojarzonych z tą aplikacją.
* **Wersja domyślna:** Wersja aplikacji zainstalowana, jeśli nie wskażesz wersji podczas określania aplikacji dla puli. To ustawienie jest opcjonalne.
* **Zezwalaj na aktualizacje:** Wartość określająca, czy aktualizacje, usunięcia i uzupełnienia pakietu są dozwolone. Jeśli jest ustawiona na **Nie,** aktualizacje i usunięcia pakietów są wyłączone dla aplikacji. Można dodać tylko nowe wersje pakietów aplikacji. Wartość domyślna to **Tak**.

Jeśli chcesz zobaczyć strukturę plików pakietu aplikacji w węźle obliczeniowym, przejdź do konta usługi Batch w portalu. Na koncie usługi Batch przejdź do **pozycji Pule**. Wybierz pulę zawierającą węzły obliczeniowe, które Cię interesują.

![Węzły w puli][13]

Po wybraniu puli przejdź do węzła obliczeniowego, na którym jest zainstalowany pakiet aplikacji. Stamtąd szczegóły pakietu aplikacji znajdują się w folderze **aplikacji.** Dodatkowe foldery w węźle obliczeniowym zawierają inne pliki, takie jak uruchamianie zadań, pliki wyjściowe, dane wyjściowe błędów itp.

![Pliki w węźle][14]

### <a name="view-application-details"></a>Wyświetlanie szczegółów aplikacji
Aby wyświetlić szczegóły dotyczące aplikacji, wybierz aplikację w oknie **Aplikacje.**

![Szczegóły aplikacji][4]

W szczegółach aplikacji można skonfigurować następujące ustawienia dla aplikacji.

* **Zezwalaj na aktualizacje:** Określ, czy jego pakiety aplikacji mogą być aktualizowane lub usuwane. Zobacz "Aktualizowanie lub usuwanie pakietu aplikacji" w dalszej części tego artykułu.
* **Wersja domyślna:** Określ domyślny pakiet aplikacji do wdrożenia w węzłach obliczeniowych.
* **Nazwa wyświetlana:** Określ przyjazną nazwę, której rozwiązanie Batch może używać, gdy wyświetla informacje o aplikacji, na przykład w interfejsie użytkownika usługi, która jest dostarczana klientom za pośrednictwem usługi Batch.

### <a name="add-a-new-application"></a>Dodawanie nowej aplikacji
Aby utworzyć nową aplikację, dodaj pakiet aplikacji i określ nowy, unikatowy identyfikator aplikacji. Pierwszy pakiet aplikacji, który można dodać z nowym identyfikatorem aplikacji również tworzy nową aplikację.

Kliknij **pozycję Dodawanie aplikacji** > **.**

![Nowy blok aplikacji w witrynie Azure portal][5]

Okno **Nowa aplikacja** zawiera następujące pola, aby określić ustawienia nowego pakietu aplikacji i aplikacji.

**Identyfikator aplikacji**

To pole określa identyfikator nowej aplikacji, który podlega standardowym regułom sprawdzania poprawności identyfikatora wsadowego platformy Azure. Zasady dostarczania identyfikatora aplikacji są następujące:

* W węzłach systemu Windows identyfikator może zawierać dowolną kombinację znaków alfanumerycznej, łączników i podkreśleń. W węzłach systemu Linux dozwolone są tylko znaki alfanumeryczne i podkreślenia.
* Nie może zawierać więcej niż 64 znaków.
* Musi być unikatowa w ramach konta batch.
* Czy zachowanie wielkości liter i niewrażliwe na wielkości liter.

**Wersja**

To pole określa wersję przesyłanego pakietu aplikacji. Ciągi wersji podlegają następującym regułom sprawdzania poprawności:

* W węzłach systemu Windows ciąg wersji może zawierać dowolną kombinację znaków alfanumerycznej, łączników, podkreśleń i kropek. W węzłach systemu Linux ciąg wersji może zawierać tylko znaki alfanumeryczne i podkreślenia.
* Nie może zawierać więcej niż 64 znaków.
* Musi być unikatowa w aplikacji.
* Czy zachowanie wielkości liter i niewrażliwe na wielkości liter.

**Pakiet aplikacji**

To pole określa plik zip zawierający pliki binarne aplikacji i pliki pomocnicze, które są wymagane do wykonania aplikacji. Kliknij pole **Wyboru pliku** lub ikonę folderu, aby przejść do pliku zip i wybrać plik zip zawierający pliki aplikacji.

Po wybraniu pliku kliknij przycisk **OK,** aby rozpocząć przekazywanie do usługi Azure Storage. Po zakończeniu operacji przekazywania portal wyświetla powiadomienie. W zależności od rozmiaru przesyłanego pliku i szybkości połączenia sieciowego ta operacja może zająć trochę czasu.

> [!WARNING]
> Nie zamykaj okna **Nowa aplikacja** przed zakończeniem operacji przekazywania. W ten sposób zatrzymuje proces przekazywania.
> 
> 

### <a name="add-a-new-application-package"></a>Dodawanie nowego pakietu aplikacji
Aby dodać wersję pakietu aplikacji dla istniejącej aplikacji, wybierz aplikację w **oknach Aplikacje** i kliknij przycisk**Dodaj** **pakiety** > .

![Dodawanie bloku pakietów aplikacji w witrynie Azure portal][8]

Jak widać, pola są zgodne z polami **okna Nowa aplikacja,** ale pole **Identyfikator aplikacji** jest wyłączone. Podobnie jak w przypadku nowej aplikacji, określ **wersję** dla nowego pakietu, przejdź do **pliku zip pakietu aplikacji,** a następnie kliknij przycisk **OK,** aby przekazać pakiet.

### <a name="update-or-delete-an-application-package"></a>Aktualizowanie lub usuwanie pakietu aplikacji
Aby zaktualizować lub usunąć istniejący pakiet aplikacji, otwórz szczegóły aplikacji, kliknij pozycję **Pakiety**, kliknij **wielokropek** w wierszu pakietu aplikacji, który chcesz zmodyfikować, i wybierz akcję, którą chcesz wykonać.

![Aktualizowanie lub usuwanie pakietu w witrynie Azure portal][7]

**Aktualizacja**

Po **kliknięciu przycisku Aktualizuj**zostanie wyświetlona okno **pakietu Aktualizacji.** To okno jest podobne do okna **Nowy pakiet aplikacji,** jednak włączone jest tylko pole wyboru pakietu, co pozwala określić nowy plik ZIP do przekazania.

![Aktualizacja bloku pakietu w witrynie Azure portal][11]

**Usuwanie**

Po **kliknięciu przycisku Usuń**zostaniesz poproszony o potwierdzenie usunięcia wersji pakietu, a usługa Batch usunie pakiet z usługi Azure Storage. Jeśli usuniesz domyślną wersję aplikacji, ustawienie **Wersja domyślna** zostanie usunięta dla aplikacji.

![Usuń aplikację][12]

## <a name="install-applications-on-compute-nodes"></a>Instalowanie aplikacji w węzłach obliczeniowych
Teraz, gdy już wiesz, jak zarządzać pakietami aplikacji za pomocą witryny Azure Portal, możemy omówić sposób wdrażania ich do węzłów obliczeniowych i uruchamiać je za pomocą zadań usługi Batch.

### <a name="install-pool-application-packages"></a>Instalowanie pakietów aplikacji puli
Aby zainstalować pakiet aplikacji na wszystkich węzłach obliczeniowych w puli, należy określić co najmniej jeden pakiet aplikacji *dla* puli. Pakiety aplikacji, które można określić dla puli są instalowane w każdym węźle obliczeniowym, gdy ten węzeł łączy pulę i po ponownym uruchomieniu lub ponownym zaimagedowaniu węzła.

W obszarze Batch .NET określ co najmniej [jednąpulę cloudpool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref] podczas tworzenia nowej puli lub dla istniejącej puli. [Klasa ApplicationPackageReference][net_pkgref] określa identyfikator aplikacji i wersję do zainstalowania w węzłach obliczeniowych puli.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Jeśli wdrożenie pakietu aplikacji nie powiedzie się z jakiegokolwiek powodu, usługa Batch oznacza węzeł [bezużyteczny,][net_nodestate]a żadne zadania nie są zaplanowane do wykonania w tym węźle. W takim przypadku należy **ponownie uruchomić** węzeł, aby ponownie zainicjować wdrożenie pakietu. Ponowne uruchomienie węzła umożliwia również ponowne planowanie zadań w węźle.
> 
> 

### <a name="install-task-application-packages"></a>Instalowanie pakietów aplikacji zadań
Podobnie jak w puli, należy określić *odwołania do* pakietu aplikacji dla zadania. Gdy zadanie jest zaplanowane do uruchomienia w węźle, pakiet jest pobierany i wyodrębniany tuż przed wykonaniem wiersza polecenia zadania. Jeśli określony pakiet i wersja jest już zainstalowany w węźle, pakiet nie jest pobierany i używany jest istniejący pakiet.

Aby zainstalować pakiet aplikacji zadania, należy skonfigurować zadanie [CloudTask][net_cloudtask]. [Właściwość ApplicationPackageReferences:][net_cloudtask_pkgref]

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Wykonywanie zainstalowanych aplikacji
Pakiety określone dla puli lub zadania są pobierane i wyodrębniane do `AZ_BATCH_ROOT_DIR` nazwanego katalogu w węźle. Batch tworzy również zmienną środowiskową zawierającą ścieżkę do nazwanego katalogu. Wiersze polecenia zadania używają tej zmiennej środowiskowej podczas odwoływania się do aplikacji w węźle. 

W węzłach systemu Windows zmienna jest w następującym formacie:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

W węzłach Systemu Linux format jest nieco inny. Okresy (.), łączniki (-) i znaki liczbowe (#) są spłaszczane w celu podkreślenia w zmiennej środowiskowej. Należy również zauważyć, że przypadek identyfikatora aplikacji jest zachowywany. Przykład:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID`i `version` są wartości, które odpowiadają wersji aplikacji i pakietu, które zostały określone dla wdrożenia. Na przykład jeśli określono, że wersja 2.7 *blendera* aplikacji powinna być zainstalowana w węzłach systemu Windows, wiersze poleceń zadania będą używać tej zmiennej środowiskowej, aby uzyskać dostęp do swoich plików:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

W węzłach systemu Linux określ zmienną środowiskową w tym formacie. Spłaszcz kropki (.), łączniki (-) i znaki liczbowe (#) do podkreślenia i zachowaj przypadek identyfikatora aplikacji:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Podczas przekazywania pakietu aplikacji można określić domyślną wersję do wdrożenia w węzłach obliczeniowych. Jeśli określono wersję domyślną dla aplikacji, można pominąć sufiks wersji podczas odwoływania się do aplikacji. Domyślną wersję aplikacji można określić w witrynie Azure portal w oknie **Aplikacje,** jak pokazano w oknie [Przekazywanie aplikacji i zarządzanie nimi](#upload-and-manage-applications).

Jeśli na przykład ustawisz "2.7" jako domyślną wersję *dla blendera*aplikacji, a zadania będą odwoływać się do następującej zmiennej środowiskowej, węzły systemu Windows będą wykonywane w wersji 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Poniższy fragment kodu przedstawia przykładowy wiersz polecenia zadania, który uruchamia domyślną wersję aplikacji *blendera:*

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Aby uzyskać więcej informacji na temat ustawień środowiska węzła obliczeniowego, zobacz [Ustawienia środowiska dla zadań](batch-api-basics.md#environment-settings-for-tasks) w [okolicy funkcji usługi Batch.](batch-api-basics.md)
> 
> 

## <a name="update-a-pools-application-packages"></a>Aktualizowanie pakietów aplikacji puli
Jeśli istniejąca pula została już skonfigurowana z pakietem aplikacji, można określić nowy pakiet dla puli. Jeśli określisz nowe odwołanie do pakietu dla puli, zastosowanie mają następujące zasady:

* Usługa Batch instaluje nowo określony pakiet na wszystkich nowych węzłach, które dołączają do puli i na dowolnym istniejącym węźle, który jest ponownie uruchomiony lub ponownie zaimaged.
* Węzły obliczeniowe, które znajdują się już w puli podczas aktualizowania odwołań do pakietu, nie instalują automatycznie nowego pakietu aplikacji. Te węzły obliczeniowe muszą zostać ponownie uruchomione lub ponownie zaimkowane, aby otrzymać nowy pakiet.
* Po wdrożeniu nowego pakietu utworzone zmienne środowiskowe odzwierciedlają odwołania do nowego pakietu aplikacji.

W tym przykładzie istniejąca pula ma wersję 2.7 aplikacji *blendera* skonfigurowaną jako jedna z jego [Puli Chmury.][net_cloudpool] [ApplicationPackageReferences][net_cloudpool_pkgref]. Aby zaktualizować węzły puli w wersji 2.76b, określ nowy [ApplicationPackageReference][net_pkgref] z nową wersją i zaobwiń zmianę.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Teraz, gdy nowa wersja została skonfigurowana, usługa Batch instaluje wersję 2.76b do dowolnego *nowego* węzła, który dołącza do puli. Aby zainstalować 2.76b na węzłach, które są *już* w puli, uruchom ponownie lub ponownie je zamów. Należy zauważyć, że ponownie uruchomione węzły zachowują pliki z poprzednich wdrożeń pakietu.

## <a name="list-the-applications-in-a-batch-account"></a>Wyświetlanie listy aplikacji na koncie usługi Batch
Aplikacje i ich pakiety można wyświetlić na koncie usługi Batch za pomocą [aplikacji.][net_appops] [ListApplicationSummaries][net_appops_listappsummaries] metoda.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Zawijanie
Za pomocą pakietów aplikacji można pomóc klientom wybrać aplikacje dla swoich zadań i określić dokładną wersję do użycia podczas przetwarzania zadań z usługą obsługującą usługę batch. Możesz również zapewnić klientom możliwość przekazywania i śledzenia własnych aplikacji w usłudze.

## <a name="next-steps"></a>Następne kroki
* Interfejs [API REST partii][api_rest] zapewnia również obsługę pracy z pakietami aplikacji. Na przykład zobacz [applicationPackageReferences][rest_add_pool_with_packages] element w [Dodaj pulę do konta, aby][rest_add_pool] uzyskać informacje dotyczące sposobu określania pakietów do zainstalowania przy użyciu interfejsu API REST. Zobacz [Aplikacje,][rest_applications] aby uzyskać szczegółowe informacje na temat uzyskiwania informacji o aplikacji przy użyciu interfejsu API REST partii.
* Dowiedz się, jak programowo [zarządzać kontami i przydziałami usługi Azure Batch za pomocą usługi Zarządzania wsadowego .NET](batch-management-dotnet.md). Biblioteka [.NET usługi Zarządzanie wsadami][api_net_mgmt] umożliwia tworzenie i usuwanie kont dla aplikacji lub usługi Batch.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagram wysokiego poziomu pakietów aplikacji"
[2]: ./media/batch-application-packages/app_pkg_02.png "Kafelek Aplikacji w witrynie Azure portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Ostrza aplikacji w witrynie Azure portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Blok szczegółów aplikacji w witrynie Azure portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nowy blok aplikacji w witrynie Azure portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Lista rozwijana Aktualizowania lub usuwania pakietów w witrynie Azure portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nowy blok pakietu aplikacji w witrynie Azure portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Brak połączonego alertu konta magazynu"
[10]: ./media/batch-application-packages/app_pkg_10.png "Wybieranie bloku konta magazynu w witrynie Azure portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Aktualizacja bloku pakietu w witrynie Azure portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Okno dialogowe potwierdzanie pakietu w witrynie Azure portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Informacje o węźle obliczeniowym w witrynie Azure portal"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Pliki w węźle obliczeniowym wyświetlane w witrynie Azure portal"
