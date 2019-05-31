---
title: Instalowanie pakietów aplikacji w węzłach obliczeniowych — usługi Azure Batch | Dokumentacja firmy Microsoft
description: Użyj funkcji pakietów aplikacji usługi Azure Batch w prosty sposób zarządzać wielu aplikacji i wersji do instalacji w usłudze Batch węzłów obliczeniowych.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c17835a4155e97395e8ae1b8e9ba6d2a42433f71
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298741"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Wdrażanie aplikacji do węzłów za pomocą pakietów aplikacji usługi Batch obliczeniowych

Funkcja pakietów aplikacji usługi Azure Batch umożliwia łatwe zarządzanie aplikacjami zadań i wdrażanie ich w węzłach obliczeniowych w puli. Za pomocą pakietów aplikacji można przekazać i zarządzanie wielu wersji aplikacji, które są uruchamiane przez podzadania, łącznie z ich pliki pomocnicze. Można następnie automatycznie wdrożyć co najmniej jeden z tych aplikacji w węzłach obliczeniowych w puli.

W tym artykule dowiesz się, jak przekazywać i zarządzanie pakietami aplikacji w witrynie Azure portal. Następnie dowiesz się, jak mają być instalowane w węzłach obliczeniowych w puli za pomocą [platformy .NET usługi Batch] [ api_net] biblioteki.

> [!NOTE]
> Pakiety aplikacji są obsługiwane we wszystkich pulach usługi Batch utworzonych po 5 lipca 2017 r. W pulach usługi Batch utworzonych między 10 marca 2016 r. a 5 lipca 2017 r. są one obsługiwane tylko w przypadku, gdy pula została utworzona za pomocą konfiguracji usługi w chmurze. Pule usługi Batch utworzone przed 10 marca 2016 r. nie obsługują pakietów aplikacji.
>
> Interfejsy API do tworzenia i zarządzania pakiety aplikacji są częścią [Batch Management .NET] [ api_net_mgmt] biblioteki. Instalowanie pakietów aplikacji w węźle obliczeniowym interfejsy API są częścią [platformy .NET usługi Batch] [ api_net] biblioteki. Porównywalne funkcje są uwzględnione w dostępnych interfejsów API usługi Batch dla innych języków. 
>
> Funkcja pakietów aplikacji, które są opisane w tym miejscu zastępuje funkcję aplikacji usługi Batch dostępnych w poprzednich wersjach usługi.

## <a name="application-package-requirements"></a>Wymagań dotyczących pakietu aplikacji
Aby użyć pakietów aplikacji, musisz [Łączenie konta usługi Azure Storage](#link-a-storage-account) z kontem usługi Batch.

## <a name="about-applications-and-application-packages"></a>Aplikacje i pakiety aplikacji — informacje
W ramach usługi Azure Batch *aplikacji* odwołuje się do zestawu określonej wersji plików binarnych, które można automatycznie pobrać do węzłów obliczeniowych w puli. *Pakiet aplikacji* odwołuje się do *określony zestaw* tych danych binarnych i reprezentuje danego *wersji* aplikacji.

![Diagramu wysokiego poziomu aplikacji i pakietów aplikacji][1]

### <a name="applications"></a>Aplikacje
Aplikacja w usłudze Batch zawiera jeden lub więcej aplikacji, pakietów i określa opcje konfiguracji dla aplikacji. Na przykład aplikacji można określić wersji pakietu aplikacji domyślnej do zainstalowania w węzłach obliczeniowych i czy jego pakietów mogą zostać zaktualizowane lub usunięte.

### <a name="application-packages"></a>Pakiety aplikacji
Pakiet aplikacji jest plik zip, który zawiera pliki binarne aplikacji i plików pomocniczych, które są wymagane dla zadań podrzędnych do uruchomienia aplikacji. Każdy pakiet aplikacji reprezentuje określoną wersję aplikacji.

Można określić pakiety aplikacji na poziomie puli i zadania podrzędnego. Podczas tworzenia puli lub zadań można określić co najmniej jeden z tych pakietów oraz (opcjonalnie) wersji.

* **Pakiety aplikacji w puli** są wdrażane *co* węźle w puli. Aplikacje są wdrażane, gdy węzeł dołącza puli, a gdy zostanie ponownie uruchomiony lub odtworzony z obrazu.
  
    Pakietów aplikacji puli są odpowiednie w przypadku, gdy wszystkich węzłów w puli wymaga wykonania zadania. Podczas tworzenia puli, można dodać lub zaktualizować pakiety istniejącej puli, można określić co najmniej jeden pakiet aplikacji. Jeśli zaktualizujesz istniejącej puli, pakiety aplikacji, należy ponownie uruchomić jego węzły, aby zainstalować nowy pakiet.
* **Pakiety aplikacji zadania** są wdrażane tylko w węźle obliczeniowym, zaplanowane do uruchomienia zadania, po prostu, przed uruchomieniem wiersza polecenia zadania podrzędnego. Jeśli podany pakiet aplikacji i wersji jest już w węźle, go ponownie nie jest wdrażana i jest używany istniejący pakiet.
  
    Pakiety aplikacji zadania podrzędnego są przydatne w środowiskach z udostępnioną pulą, gdzie różne zadania są uruchamiane w jednej puli, a pula nie zostanie usunięta, gdy zadanie jest zakończone. Jeśli liczba zadań podrzędnych w zadaniu jest mniejsza niż liczba węzłów w puli, pakiety aplikacji zadania podrzędnego mogą minimalizować ilość transferowanych danych, ponieważ aplikacja jest wdrażana tylko dla węzłów, w których odbywa się uruchamianie zadań podrzędnych.
  
    Inne scenariusze, które mogą korzystać z pakiety aplikacji zadania podrzędnego są zadań uruchamianych dużych aplikacji, ale tylko kilka zadań. Na przykład etapie przetwarzania wstępnego lub zadanie scalania, gdy aplikacja wstępnego przetwarzania lub scalania jest niewielkich, mogą korzystać z pakiety aplikacji zadania podrzędnego.

> [!IMPORTANT]
> Istnieją ograniczenia liczby aplikacji i pakietów aplikacji w ramach konta usługi Batch, a na aplikację maksymalny rozmiar pakietu. Zobacz [przydziały i limity dla usługi Azure Batch](batch-quota-limit.md) szczegółowe informacje na temat tych ograniczeń.
> 
> 

### <a name="benefits-of-application-packages"></a>Korzyści z pakietu aplikacji
Pakiety aplikacji można uproszczenie kodu w rozwiązaniu usługi Batch i zmniejszyć nakład pracy potrzebny do zarządzania aplikacjami, uruchamianych przez podzadania.

Za pomocą pakietów aplikacji zadania podrzędnego uruchamiania w puli nie musi określić długą listę plików pojedynczego zasobu do zainstalowania w węzłach. Nie trzeba ręcznie zarządzać wielu wersji plików aplikacji w usłudze Azure Storage oraz węzłów. I nie musisz martwić się o generowania [adresów URL sygnatury dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) zapewnienie dostępu do plików na koncie magazynu. Batch działają w tle z usługi Azure Storage do przechowywania pakietów aplikacji i wdrażania ich w węzłach obliczeniowych.

> [!NOTE] 
> Całkowity rozmiar zadania podrzędnego uruchamiania musi wynosić 32 768 znaków, w tym pliki zasobów lub zmienne środowiskowe, lub być mniejszy. Jeśli zadanie podrzędne uruchamiania przekracza ten limit, następnie za pomocą pakietów aplikacji jest inną opcją. Można również utworzyć skompresowane archiwum zawierające pliki zasobów, przekaż go jako obiekt blob do usługi Azure Storage i Rozpakuj go z wiersza polecenia zadania podrzędnego uruchamiania. 
>
>

## <a name="upload-and-manage-applications"></a>Przekazywanie aplikacji i zarządzanie nimi
Możesz użyć [witryny Azure portal] [ portal] lub interfejsów API zarządzania usługi Batch w zakresie zarządzania pakietami aplikacji na koncie usługi Batch. W kolejnych sekcjach najpierw przedstawiono, jak połączyć konto usługi Storage, a następnie omówiono Dodawanie aplikacji i pakietów oraz zarządzania nimi za pomocą portalu.

### <a name="link-a-storage-account"></a>Połączyć konto usługi Storage
Aby użyć pakietów aplikacji, musisz najpierw połączyć [konta usługi Azure Storage](batch-api-basics.md#azure-storage-account) z kontem usługi Batch. Jeśli nie skonfigurowano jeszcze konta magazynu, witryny Azure portal wyświetla ostrzeżenie po raz pierwszy kliknij **aplikacji** na koncie usługi Batch.



![Ostrzeżenie "Nie skonfigurowano konta magazynu" w witrynie Azure portal][9]

Usługa Batch używa skojarzone konto magazynu do przechowywania pakietów aplikacji. Po połączeniu dwóch kont usługi Batch można automatycznie wdrażać pakiety, przechowywane w połączonym koncie usługi Storage do węzłów obliczeniowych. Aby połączyć konto usługi Storage z kontem usługi Batch, kliknij **konta magazynu** na **ostrzeżenie** okna, a następnie kliknij przycisk **konta magazynu** ponownie.

![Wybierz blok konto magazynu w witrynie Azure portal][10]

Zaleca się utworzenie konta magazynu *specjalnie* do użytku z kontem usługi Batch i wybierz go tutaj. Po utworzeniu konta magazynu można następnie połączyć go z kontem usługi Batch za pomocą **konta magazynu** okna.

> [!NOTE] 
> Obecnie nie można użyć pakietów aplikacji za pomocą konta usługi Azure Storage, który jest skonfigurowany z [reguły zapory](../storage/common/storage-network-security.md).
> 

Usługa Batch używa usługi Azure Storage do przechowywania pakietów aplikacji jako blokowe obiekty BLOB. Jesteś [rozliczane jako normalny] [ storage_pricing] dla blokowych obiektów blob danych, a rozmiar każdego pakietu nie może przekraczać [bloku maksymalny rozmiar obiektu blob](../storage/common/storage-scalability-targets.md#azure-blob-storage-scale-targets). Należy wziąć pod uwagę rozmiar i liczba pakietów do aplikacji i umożliwia okresowe usuwanie przestarzałych pakietów, aby zminimalizować koszty.
> 
> 

### <a name="view-current-applications"></a>Wyświetl bieżące aplikacje
Aby wyświetlić aplikacje na koncie usługi Batch, kliknij **aplikacje** element menu w menu po lewej stronie podczas przeglądania sieci **konto usługi Batch**.

![Kafelek aplikacji][2]

Wybranie tej opcji menu otwiera **aplikacje** okna:

![Wyświetlanie listy aplikacji][3]

To okno wyświetla identyfikator każdej aplikacji w koncie oraz następujące właściwości:

* **Pakiety**: Numer wersji skojarzonej z tą aplikacją.
* **Domyślna wersja**: Wersja aplikacji zainstalowane, jeśli nie wskazują wersji po określeniu aplikacji dla puli. To ustawienie jest opcjonalne.
* **Zezwalaj na aktualizacje**: Wartość, która określa, czy pakiet aktualizacji, usuwania i dodatki są dozwolone. Jeśli jest ono ustawione na **nie**, pakiet aktualizacji i usuwania są wyłączone dla aplikacji. Można dodawać tylko nowe wersje pakietu aplikacji. Wartość domyślna to **Tak**.

Jeśli chcesz wyświetlić strukturę pliku pakietu aplikacji w węźle obliczeń, przejdź do konta usługi Batch w portalu. Z kontem usługi Batch, przejdź do **pule**. Wybierz pulę, która zawiera węzły obliczeniowe, których interesuje Cię.

![Węzły w puli][13]

Po wybraniu puli, przejdź do węzła obliczeniowego zainstalowanego pakietu aplikacji. W tym miejscu, szczegółowe informacje o pakiecie aplikacji znajdują się w **aplikacje** folderu. Dodatkowe foldery w węźle obliczeniowym zawierają inne pliki, takie jak zadania uruchamiania, pliki wyjściowe, dane wyjściowe błędu itp.

![Pliki w węźle][14]

### <a name="view-application-details"></a>Wyświetlanie szczegółów aplikacji
Aby wyświetlić szczegółowe informacje dotyczące aplikacji, wybierz aplikację w **aplikacje** okna.

![Szczegóły aplikacji][4]

W szczegółach aplikacji można skonfigurować następujące ustawienia dla aplikacji.

* **Zezwalaj na aktualizacje**: Określ, czy jego pakiety aplikacji mogą zostać zaktualizowane lub usunięte. Zobacz "Aktualizowanie lub usuwanie pakietu aplikacji" w dalszej części tego artykułu.
* **Domyślna wersja**: Określ domyślny pakiet aplikacji, aby wdrożyć węzły obliczeniowe.
* **Nazwa wyświetlana**: Określ przyjazną nazwę, która rozwiązania usługi Batch można użyć, gdy na przykład wyświetla informacje o aplikacji, w Interfejsie użytkownika usługi, która zapewnia klientom za pomocą usługi Batch.

### <a name="add-a-new-application"></a>Dodaj nową aplikację
Aby utworzyć nową aplikację, Dodawanie pakietu aplikacji i podaj identyfikator nowych, unikatowych aplikacji. Pierwszy pakiet aplikacji, który możesz dodać nowy identyfikator aplikacji jest także utworzenie nowej aplikacji.

Kliknij pozycję **Aplikacje** > **Dodaj**.

![Nowy blok aplikacji w witrynie Azure portal][5]

**Nową aplikację** okno zawiera następujące pola, aby określić ustawienia nowej aplikacji i pakietów aplikacji.

**Identyfikator aplikacji**

To pole określa identyfikator nową aplikację, która podlega standardowych reguł sprawdzania poprawności Identyfikatora partii Azure. Dostępne są następujące reguły zapewniające identyfikator aplikacji:

* W węzłach Windows identyfikator może zawierać dowolną kombinację znaków alfanumerycznych, łączniki i podkreślenia. W węzłach systemu Linux dozwolone są tylko znaki alfanumeryczne i znaki podkreślenia.
* Nie może zawierać więcej niż 64 znaki.
* Musi być unikatowa w ramach konta usługi Batch.
* Jest zachowywanie oraz bez uwzględniania wielkości liter.

**Wersja**

To pole określa numer wersji pakietu aplikacji, które przekazujesz. Ciągi wersji jest zależna od następujących reguł sprawdzania poprawności:

* W węzłach Windows ciąg wersji może zawierać dowolną kombinację znaków alfanumerycznych, łączniki, podkreślenia i okresów. W przypadku węzłów systemu Linux ciąg wersji może zawierać tylko znaki alfanumeryczne i znaki podkreślenia.
* Nie może zawierać więcej niż 64 znaki.
* Musi być unikatowa w obrębie aplikacji.
* To zachowywanie oraz bez uwzględniania wielkości liter.

**Pakiet aplikacji**

To pole określa plik zip, który zawiera pliki binarne aplikacji i plików pomocniczych, które są wymagane do wykonania aplikacji. Kliknij przycisk **wybierz plik** pola lub ikonę folderu, wyszukaj i wybierz plik zip, który zawiera pliki aplikacji.

Po wybraniu pliku kliknij **OK** aby rozpocząć przekazywanie do usługi Azure Storage. Po zakończeniu operacji przekazywania portalu wyświetli powiadomienie. W zależności od rozmiaru pliku który podczas przekazywania i szybkość połączenia sieciowego ta operacja może trochę potrwać.

> [!WARNING]
> Nie zamykaj **nową aplikację** okno przed zakończeniem operacji przekazywania. Ten sposób powoduje zatrzymanie procesu przekazywania.
> 
> 

### <a name="add-a-new-application-package"></a>Dodaj nowy pakiet aplikacji
Aby dodać wersję pakietu aplikacji dla istniejącej aplikacji, wybierz aplikację w **aplikacje** systemu windows, a następnie kliknij przycisk **pakietów** > **Dodaj**.

![Dodawanie bloku pakietu aplikacji w witrynie Azure portal][8]

Jak widać, pola odpowiadają **nową aplikację** okna, ale **identyfikator aplikacji** pole jest wyłączone. Tak jak w nowej aplikacji określ **wersji** dla Twojego nowego pakietu, przejdź do usługi **pakiet aplikacji** zip pliku, a następnie kliknij przycisk **OK** do przekazania pakietu.

### <a name="update-or-delete-an-application-package"></a>Aktualizowanie lub usuwanie pakietu aplikacji
Aby zaktualizować lub usunąć istniejący pakiet aplikacji, Otwórz szczegóły dla aplikacji, kliknij przycisk **pakietów**, kliknij przycisk **wielokropka** w wierszu pakiet aplikacji, którą chcesz zmodyfikować, a następnie wybierz pozycję Akcja, którą chcesz wykonać.

![Aktualizowanie lub usuwanie pakietu w witrynie Azure portal][7]

**Aktualizacja**

Po kliknięciu **aktualizacji**, **pakiet aktualizacji** systemu windows jest wyświetlana. Jest to podobne do **nowy pakiet aplikacji** okna, jednak tylko pola wyboru pakietów jest włączone, dzięki czemu można określić nowy plik ZIP do przekazania.

![Bloku pakietu aktualizacji w witrynie Azure portal][11]

**Usuwanie**

Po kliknięciu **Usuń**, zostanie wyświetlona prośba o potwierdzenie usunięcia wersja pakietu i Batch usuwa pakiet z usługi Azure Storage. Jeśli usuniesz domyślną wersję aplikacji, **domyślną wersję** ustawienie zostanie usunięte z aplikacji.

![Usuwanie aplikacji ][12]

## <a name="install-applications-on-compute-nodes"></a>Instalowanie aplikacji w węzłach obliczeniowych
Teraz, gdy wyjaśniono sposób zarządzania pakietami aplikacji w witrynie Azure portal, można omawiać sposób wdrażania ich do węzłów obliczeniowych i uruchamiaj je za pomocą zadania podrzędne usługi Batch.

### <a name="install-pool-application-packages"></a>Instalowanie pakietów aplikacji puli
Aby zainstalować pakiet aplikacji na wszystkich węzłach obliczeniowych w puli, należy określić co najmniej jeden pakiet aplikacji *odwołania* dla puli. Pakiety aplikacji, które określisz dla puli są instalowane na każdym węźle obliczeniowym, gdy węzeł dołącza do puli i w przypadku, gdy węzeł jest ponownie uruchamiany lub odtwarzany z obrazu.

Na platformie .NET usługi Batch, należy określić co najmniej jedną [CloudPool][net_cloudpool].[ ApplicationPackageReferences] [ net_cloudpool_pkgref] podczas tworzenia nowej puli lub do istniejącej puli. [ApplicationPackageReference] [ net_pkgref] klasa określa identyfikator aplikacji i wersji, aby zainstalować w ramach puli węzłów obliczeniowych.

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
> Jeśli wdrożenie pakietu aplikacji nie powiedzie się z jakiegokolwiek powodu, usługa partia zadań oznacza węzeł [bezużyteczne][net_nodestate], oraz zadania nie są zaplanowane do wykonania, w tym węźle. W takim przypadku należy **ponowne uruchomienie** węzeł, aby ponownie zainicjować wdrożenia pakietu. Ponowne uruchomienie węzła umożliwia również planowanie zadań, ponownie w węźle.
> 
> 

### <a name="install-task-application-packages"></a>Zainstaluj pakiety aplikacji zadania podrzędnego
Podobnie jak w puli, należy określić pakiet aplikacji *odwołania* dla zadania. Gdy zadanie jest zaplanowane do uruchomienia w węźle, pakiet jest pobrane i wyodrębnione przed wykonaniem wiersza polecenia zadania podrzędnego. Jeśli określonego pakietu lub wersja jest już zainstalowany w węźle, pakiet nie został pobrany i jest używany istniejący pakiet.

Aby zainstalować pakiet aplikacji zadanie, należy skonfigurować zadania podrzędnego [CloudTask][net_cloudtask].[ ApplicationPackageReferences] [ net_cloudtask_pkgref] właściwości:

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

## <a name="execute-the-installed-applications"></a>Wykonaj zainstalowanych aplikacji
Pakiety, które zostały określone dla puli lub zadania są pobierane i wyodrębnione do katalogu o nazwie w ramach `AZ_BATCH_ROOT_DIR` węzła. Usługa Batch również tworzy zmienną środowiskową, która zawiera ścieżkę do katalogu o nazwie. Twoje wiersze poleceń podzadań Użyj tej zmiennej środowiskowej podczas odwoływania się do aplikacji w węźle. 

W węzłach Windows zmiennej znajduje się w następującym formacie:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

W węzłach systemu Linux format jest nieco inne. Kropki (.), łączniki (-) i znaki numeru (#) są spłaszczone do podkreślenia w zmiennej środowiskowej. Należy również zauważyć, że jest zachowywany w przypadku Identyfikatora aplikacji. Na przykład:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` i `version` są wartościami, które odnoszą się do aplikacji oraz wersją pakietu został określony dla wdrożenia. Na przykład, jeśli określono tę wersję 2.7 aplikacji *blender* powinny być instalowane w węzłach Windows swoje wiersze poleceń podzadań użyje tej zmiennej środowiskowej na dostęp do swoich plików:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

W węzłach systemu Linux należy określić zmienną środowiskową w tym formacie. Spłaszczanie kropki (.), łączniki (-) i liczba znaki (#) i podkreślenia oraz zachowanie wielkości liter identyfikatora aplikacji:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Podczas przekazywania pakietu aplikacji można określić domyślną wersję, aby wdrożyć węzły obliczeniowe. Jeśli wybrano domyślną wersję aplikacji, można pominąć sufiks wersji w przypadku odwołania do aplikacji. Możesz określić domyślną wersję aplikacji w witrynie Azure portal w **aplikacje** okna, jak pokazano na [przekazywanie aplikacji i zarządzanie nimi](#upload-and-manage-applications).

Na przykład jeśli ustawisz "2.7" jako domyślną wersję aplikacji *blender*, zadania, odwołanie następującą zmienną środowiskową, a następnie węzły Windows będą wykonywane w wersji 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Poniższy fragment kodu przedstawia przykładowy wiersz polecenia zadania, który uruchamia domyślną wersją *blender* aplikacji:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Zobacz [ustawienia środowiska dla podzadań](batch-api-basics.md#environment-settings-for-tasks) w [omówienie funkcji usługi Batch](batch-api-basics.md) Aby uzyskać więcej informacji na temat ustawień środowiska węzła obliczeniowego.
> 
> 

## <a name="update-a-pools-application-packages"></a>Aktualizowanie pakietów aplikacji puli
Jeśli już skonfigurowano istniejącej puli przy użyciu pakietu aplikacji, można określić nowy pakiet dla puli. Jeśli określisz nowe odwołanie do pakietu dla puli, mają zastosowanie następujące wymagania:

* Usługa Batch instaluje nowo określonego pakietu na wszystkich nowych węzłów, które są przyłączone do puli, a na istniejący węzeł, który jest ponownie uruchamiany lub odtwarzany z obrazu.
* Obliczeniowe węzły, które znajdują się już w puli, podczas aktualizacji odwołania do pakietu nie instalują automatycznie nowy pakiet aplikacji. Te obliczenia węzłów musi być uruchomiona ponownie lub odtworzony z obrazu, aby otrzymać nowy pakiet.
* Po wdrożeniu nowego pakietu zmienne środowiskowe utworzony odzwierciedlają nowe odwołania do pakietu aplikacji.

W tym przykładzie istniejącej puli jest wersja 2.7 *blender* aplikacji skonfigurowany jako jeden z jego [CloudPool][net_cloudpool].[ ApplicationPackageReferences][net_cloudpool_pkgref]. Aby zaktualizować węzłów w puli za pomocą wersji 2.76b, określić nową [ApplicationPackageReference] [ net_pkgref] przy użyciu nowej wersji oraz zatwierdzić zmiany.

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

Teraz, gdy nowa wersja została skonfigurowana, usługa Batch instaluje wersję 2.76b do dowolnego *nowe* węzeł, który dołącza do puli. Aby zainstalować 2.76b na węzłach, które są *już* w puli, ponownego uruchamiania lub odtworzyć je. Należy pamiętać, zachowanie plików z poprzedniego wdrożenia pakietu po ponownym rozruchu węzłów.

## <a name="list-the-applications-in-a-batch-account"></a>Wyświetlanie listy aplikacji na koncie usługi Batch
Możesz wyświetlić listę aplikacji i ich pakiety na koncie usługi Batch za pomocą [ApplicationOperations][net_appops].[ ListApplicationSummaries] [ net_appops_listappsummaries] metody.

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

## <a name="wrap-up"></a>Podsumowanie
Za pomocą pakietów aplikacji możesz pomóc klientom Wybierz aplikacje do swoich zadań i określić dokładną wersję do użycia podczas przetwarzania zadania przy użyciu usługi wykorzystujących usługę Batch. Można również udostępniać możliwości dla klientów do przekazywania i śledzić własne aplikacje w usłudze.

## <a name="next-steps"></a>Kolejne kroki
* [Interfejsu API REST usługi Batch] [ api_rest] oferuje również obsługę do pracy za pomocą pakietów aplikacji. Na przykład zobacz [applicationPackageReferences] [ rest_add_pool_with_packages] element [Dodawanie puli do konta] [ rest_add_pool] informacji o sposobie określania pakiety do zainstalowania przy użyciu interfejsu API REST. Zobacz [aplikacje] [ rest_applications] szczegółowe informacje na temat sposobu uzyskiwania informacji o aplikacji przy użyciu interfejsu API REST usługi Batch.
* Dowiedz się, jak programowo [Zarządzanie kontami usługi Azure Batch i przydziałów za pomocą usługi Batch Management .NET](batch-management-dotnet.md). [Batch Management .NET] [ api_net_mgmt] biblioteki można włączyć funkcji tworzenia i usuwania konta dla aplikacji usługi Batch lub usługi.

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

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagram ogólny pakiety aplikacji"
[2]: ./media/batch-application-packages/app_pkg_02.png "Kafelek aplikacji w witrynie Azure portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Bloku aplikacje w witrynie Azure portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Bloku szczegółów aplikacji w witrynie Azure portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nowy blok aplikacji w witrynie Azure portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Aktualizowanie lub usuwanie pakietów listy rozwijanej w witrynie Azure portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nowy blok pakietu aplikacji w witrynie Azure portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Alert nie połączonego konta magazynu"
[10]: ./media/batch-application-packages/app_pkg_10.png "Wybierz blok konto magazynu w witrynie Azure portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Bloku pakietu aktualizacji w witrynie Azure portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Usuwanie w witrynie Azure portal, okno dialogowe potwierdzenia pakietu"
[13]: ./media/batch-application-packages/package-file-structure.png "Obliczenia informacje o węźle w witrynie Azure portal"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Pliki w węźle obliczeniowym, wyświetlany w witrynie Azure portal"
