---
title: Instalowanie pakietów aplikacji w węzłach obliczeniowych - partii zadań Azure | Dokumentacja firmy Microsoft
description: Użyj funkcji pakiety aplikacji partii zadań Azure ułatwia zarządzanie wiele aplikacji i wersji do instalacji na partii węzły obliczeniowe.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 07/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 440f7eba99e5fa02a597ae62d5d14329f5e50af7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Wdrażanie aplikacji do wyliczenia węzłów za pomocą pakietów aplikacji partii

Funkcja pakiety aplikacji partii zadań Azure umożliwia łatwe zarządzanie aplikacjami zadań i ich wdrożenia węzłów obliczeniowych w puli. Pakiety aplikacji możesz przekazać i zarządzanie wieloma wersjami aplikacji, których uruchamianie zadań, łącznie z ich pliki pomocnicze. Można następnie automatycznie wdrożyć co najmniej jeden z tych aplikacji, do węzłów obliczeniowych w puli.

W tym artykule dowiesz się, jak przekazywać oraz zarządzać nimi pakietów aplikacji w portalu Azure. Następnie dowiesz się jak do zainstalowania go na węzłach obliczeń puli z [partiami platformy .NET] [ api_net] biblioteki.

> [!NOTE]
> 
> Pakiety aplikacji są obsługiwane we wszystkich pulach usługi Batch utworzonych po 5 lipca 2017 r. W pulach usługi Batch utworzonych między 10 marca 2016 r. a 5 lipca 2017 r. są one obsługiwane tylko w przypadku, gdy pula została utworzona za pomocą konfiguracji usługi w chmurze. Pule usługi Batch utworzone przed 10 marca 2016 r. nie obsługują pakietów aplikacji.
>
> Interfejsy API umożliwiające tworzenie i zarządzanie pakietów aplikacji należą [zarządzania partiami platformy .NET] [[api_net_mgmt]] biblioteki. Interfejsy API do instalowania pakietów aplikacji w węźle obliczeń są częścią [partiami platformy .NET] [ api_net] biblioteki.  
>
> Funkcja pakietów aplikacji, które są opisane w tym miejscu zastępuje funkcję partii aplikacje dostępne w poprzednich wersjach usługi.
> 
> 

## <a name="application-package-requirements"></a>Wymagania dotyczące pakietu aplikacji
Aby użyć pakietów aplikacji, musisz [połączyć konto usługi Azure Storage](#link-a-storage-account) do konta partii zadań.

Ta funkcja została wprowadzona w [interfejsu API REST partii] [ api_rest] wersji 2015-12-01.2.2 i odpowiadający mu [partiami platformy .NET] [ api_net] wersji biblioteki 3.1.0. Firma Microsoft zaleca zawsze używać najnowszej wersji interfejsu API podczas pracy z partii.

> [!NOTE]
> Pakiety aplikacji są obsługiwane we wszystkich pulach usługi Batch utworzonych po 5 lipca 2017 r. W pulach usługi Batch utworzonych między 10 marca 2016 r. a 5 lipca 2017 r. są one obsługiwane tylko w przypadku, gdy pula została utworzona za pomocą konfiguracji usługi w chmurze. Pule usługi Batch utworzone przed 10 marca 2016 r. nie obsługują pakietów aplikacji.
>
>

## <a name="about-applications-and-application-packages"></a>Aplikacje i pakiety aplikacji — informacje
W partii zadań Azure *aplikacji* odwołuje się do zestawu określonej wersji plików binarnych, które mogą być automatycznie pobrane do węzłów obliczeniowych w puli. *Pakiet aplikacji* odwołuje się do *określonego zestawu* tych danych binarnych i reprezentuje danego *wersji* aplikacji.

![Diagramu wysokiego poziomu aplikacji i pakietów aplikacji][1]

### <a name="applications"></a>Aplikacje
Aplikacja w partii zawiera jeden lub więcej aplikacji, pakietów i opcje konfiguracji dla aplikacji. Na przykład aplikacji można określić domyślnej wersji pakietu aplikacji do zainstalowania na węzłów obliczeniowych oraz czy jej pakietów można zaktualizować lub usunąć.

### <a name="application-packages"></a>Pakiety aplikacji
Pakiet aplikacji jest plik zip, który zawiera pliki binarne aplikacji i plików pomocniczych, które są wymagane dla zadań do uruchomienia aplikacji. Każdy pakiet aplikacji reprezentuje określoną wersję aplikacji.

Można określić pakietów aplikacji na poziomach puli i zadań. Podczas tworzenia puli lub zadania można określić co najmniej jeden z tych pakietów i (opcjonalnie) wersja.

* **Pakiety aplikacji w puli** są wdrażane na *co* węzła w puli. Gdy węzeł dołącza pulę i gdy jest ponownego rozruchu lub odtworzenia z obrazu wdrożenia aplikacji.
  
    Pakiety aplikacji w puli są odpowiednie, wszystkie węzły w puli wykonywania zadania. Podczas tworzenia puli, można dodać lub zaktualizować pakiety istniejącej puli, można określić co najmniej jednego pakietu aplikacji. Aktualizuj istniejącą pulę aplikacji pakietów, należy ponownie uruchomić jego węzły do zainstalowania nowego pakietu.
* **Zadanie pakietów aplikacji** są wdrażane tylko w węźle obliczeń, zaplanowane zadanie, wystarczy, przed uruchomieniem zadania wiersza polecenia. Jeśli podany pakiet aplikacji i wersji jest już w węźle, nie jest wdrożone i jest używany istniejący pakiet.
  
    Pakiety aplikacji zadań są przydatne w środowisku udostępnionych puli, której różne zadania są uruchamiane w jednej puli i puli nie zostanie usunięta po zakończeniu zadania. Jeśli liczba zadań podrzędnych w zadaniu jest mniejsza niż liczba węzłów w puli, pakiety aplikacji zadania podrzędnego mogą minimalizować ilość transferowanych danych, ponieważ aplikacja jest wdrażana tylko dla węzłów, w których odbywa się uruchamianie zadań podrzędnych.
  
    Inne scenariusze, które mogą korzystać z zadań pakiety aplikacji są zadań uruchamianych dużej aplikacji, ale tylko kilka zadań. Na przykład przetwarzania wstępnego etap lub zadanie scalania, gdzie aplikacja przetwarzanie wstępne lub merge jest ogromnych, może korzystać z zadań pakietów aplikacji.

> [!IMPORTANT]
> Istnieją ograniczenia liczby aplikacji i pakietów aplikacji w ramach konta usługi partia zadań i aplikacji maksymalny rozmiar pakietu. Zobacz [przydziały i limity dla usługi partia zadań Azure](batch-quota-limit.md) szczegółowe informacje o tych ograniczeniach.
> 
> 

### <a name="benefits-of-application-packages"></a>Korzyści wynikające z pakietów aplikacji
Pakiety aplikacji można uprościć kod w rozwiązaniu partii i zmniejszyć nakład pracy potrzebny do zarządzania aplikacjami, które uruchamiania zadań.

Z pakietów aplikacji zadanie uruchamiania przez pulę nie trzeba określać długą listę plików poszczególnych zasobów można zainstalować na węzłach. Nie trzeba ręcznie Zarządzanie wieloma wersjami plików aplikacji w usłudze Azure Storage lub na węzły. I nie trzeba martwić generowania [adresy URL SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) zapewniające dostęp do plików na Twoim koncie magazynu. Wsadowe działa w tle z usługą Azure Storage do przechowywania pakietów aplikacji i wdrożenia ich na węzłach obliczeniowych.

> [!NOTE] 
> Całkowity rozmiar zadania podrzędnego uruchamiania musi wynosić 32 768 znaków, w tym pliki zasobów lub zmienne środowiskowe, lub być mniejszy. Jeśli zadanie start przekracza ten limit, za pomocą pakietów aplikacji jest inną opcją. Można również utworzyć archiwum zip zawierającym pliki zasobów, przekaż go jako obiekt blob do magazynu Azure i Rozpakuj go z poziomu wiersza polecenia zadania uruchamiania. 
>
>

## <a name="upload-and-manage-applications"></a>Przekazywanie aplikacji i zarządzanie nimi
Można użyć [portalu Azure] [ portal] lub [zarządzania partiami platformy .NET](batch-management-dotnet.md) biblioteki w zakresie zarządzania pakietami aplikacji w ramach konta usługi partia zadań. W kolejnych sekcjach kilka możemy najpierw pokazują, jak połączyć konto magazynu, a następnie omówiono Dodawanie aplikacji i pakietów i zarządzania nimi przy użyciu portalu.

### <a name="link-a-storage-account"></a>Połącz konto magazynu
Aby użyć pakietów aplikacji, możesz połączyć konto magazynu Azure do konta partii zadań. Jeśli nie skonfigurowano jeszcze konta magazynu, portalu Azure wyświetli ostrzeżenie, po raz pierwszy kliknij **aplikacji** kafelka w **konto wsadowe** bloku.

> [!IMPORTANT]
> Obecnie partii obsługuje *tylko* **ogólnego przeznaczenia** typu konta magazynu, zgodnie z opisem w kroku 5, [Utwórz konto magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account)w [o Azure konta magazynu](../storage/common/storage-create-storage-account.md). Łącząc konta usługi Azure Storage do konta partii zadań, należy połączyć *tylko* **ogólnego przeznaczenia** konta magazynu.
> 
> 

![Ostrzeżenie "Nie skonfigurowano konta magazynu" w portalu Azure][9]

Usługa partia zadań używa skojarzone konto magazynu do przechowywania pakietów aplikacji. Po połączeniu dwóch kont usługi partia zadań można automatycznie wdrażać pakietów przechowywanych w ramach połączonego konta magazynu do węzłów obliczeniowych. Aby połączyć konto magazynu do konta partii zadań, kliknij **ustawienia konta magazynu** na **ostrzeżenie** bloku, a następnie kliknij przycisk **konta magazynu** na  **Konto magazynu** bloku.

![Wybierz bloku konto magazynu w portalu Azure][10]

Zaleca się utworzenie konta magazynu *w szczególności* do użytku z kontem usługi partia zadań i zaznacz je tutaj. Aby uzyskać więcej informacji o sposobie tworzenia konta magazynu, zobacz "Tworzenie konta magazynu" w [konta usługi Azure Storage](../storage/common/storage-create-storage-account.md). Po utworzeniu konta magazynu można następnie połączyć go do konta partii zadań za pomocą **konta magazynu** bloku.

> [!WARNING]
> Usługa partia zadań używa usługi Azure Storage do przechowywania pakietów aplikacji jako blokowych obiektów blob. Jesteś [rozliczany jako normalne] [ storage_pricing] dla bloku danych obiektów blob. Należy wziąć pod uwagę rozmiar i liczbę pakietów aplikacji, a okresowe usuwanie przestarzałych pakietów, aby zminimalizować koszty.
> 
> 

### <a name="view-current-applications"></a>Wyświetl bieżące aplikacje
Aby wyświetlić aplikacje w ramach konta usługi partia zadań, kliknij przycisk **aplikacji** elementu menu w menu po lewej stronie podczas przeglądania **konto wsadowe** bloku.

![Kafelek aplikacji][2]

Wybranie tej opcji menu otwiera **aplikacji** bloku:

![Lista aplikacji][3]

**Aplikacji** bloku Wyświetla identyfikator każdej aplikacji w Twoje konto oraz następujące właściwości:

* **Pakiety**: numer wersji skojarzonych z tą aplikacją.
* **Wersja domyślna**: wersja aplikacji zainstalowane, jeśli nie wskazują wersji po określeniu dla puli aplikacji. To ustawienie jest opcjonalne.
* **Zezwalaj na aktualizacje**: wartość, która określa, czy pakiet aktualizacji, usuwanie i dodatki są dozwolone. Jeśli ta wartość jest równa **nr**, pakiet aktualizacji i usunięć są wyłączone dla aplikacji. Można dodawać tylko nowe wersje pakietu aplikacji. Wartość domyślna to **Tak**.

### <a name="view-application-details"></a>Wyświetlanie szczegółów aplikacji
Aby otworzyć blok, który zawiera szczegóły aplikacji, wybierz aplikację w **aplikacji** bloku.

![Szczegóły aplikacji][4]

W bloku szczegóły aplikacji można skonfigurować następujące ustawienia dla aplikacji.

* **Zezwalaj na aktualizacje**: Określ, czy można zaktualizować lub usunąć jego pakietów aplikacji. Zobacz "Aktualizowanie lub usuwanie pakietu aplikacji" w dalszej części tego artykułu.
* **Wersja domyślna**: Określ pakiet aplikacji domyślnej, aby wdrożyć węzły obliczeniowe.
* **Nazwa wyświetlana**: Określ przyjazną nazwę, która rozwiązania partii można użyć, gdy jest on Wyświetla informacje o aplikacji, na przykład w Interfejsie użytkownika usługi, które zapewniają klientom za pośrednictwem usługi partia zadań.

### <a name="add-a-new-application"></a>Dodaj nową aplikację
Aby utworzyć nową aplikację, należy dodać pakiet aplikacji i określ aplikacji nowy, unikatowy identyfikator. Pierwszy pakiet aplikacji, który możesz dodać nowy identyfikator aplikacji tworzy nową aplikację.

Kliknij przycisk **Dodaj** na **aplikacji** bloku, aby otworzyć **nowej aplikacji** bloku.

![Nowy blok aplikacji w portalu Azure][5]

**Nowej aplikacji** blok zawiera następujące pola, aby określić ustawienia nowej aplikacji i pakietu aplikacji.

**Identyfikator aplikacji**

To pole określa identyfikator nowej aplikacji, która podlega standardowych reguł sprawdzania poprawności identyfikator partii Azure. Dostępne są następujące reguły zapewniające identyfikator aplikacji:

* W węzłach Windows identyfikator może zawierać dowolną kombinację znaków alfanumerycznych, łączniki i podkreślenia. W węzłach Linux są dozwolone tylko znaki alfanumeryczne oraz znaki podkreślenia.
* Nie może zawierać więcej niż 64 znaki.
* Musi być unikatowa w ramach konta usługi partia zadań.
* Jest zachowywanie i bez uwzględniania wielkości liter.

**Wersja**

To pole określa wersję pakietu aplikacji, które przekazujesz. Ciągi wersji obowiązują następujące reguły sprawdzania poprawności:

* W węzłach Windows ciąg wersji może zawierać dowolną kombinację znaków alfanumerycznych, łączniki, podkreślenia i kropki. W węzłach Linux ciąg wersji może zawierać tylko znaki alfanumeryczne oraz znaki podkreślenia.
* Nie może zawierać więcej niż 64 znaki.
* Musi być unikatowa w ramach aplikacji.
* To zachowanie w przypadku i bez uwzględniania wielkości liter.

**Pakiet aplikacji**

To pole określa plik zip, który zawiera pliki binarne aplikacji i plików pomocniczych, które są wymagane do wykonania aplikacji. Kliknij przycisk **wybierz plik** pola lub ikonę folderu Wyszukaj i wybierz plik zip, który zawiera pliki aplikacji.

Po wybraniu pliku, kliknij przycisk **OK** można rozpocząć przekazywania do magazynu Azure. Po zakończeniu operacji wysyłania portal wyświetla powiadomienie i zamyka bloku. W zależności od rozmiaru pliku, czy przekazujesz i szybkość połączenia sieciowego ta operacja może potrwać pewien czas.

> [!WARNING]
> Nie zamykaj **nowej aplikacji** bloku przed zakończeniem operacji przesyłania. W ten sposób zostanie zatrzymany procesu przekazywania.
> 
> 

### <a name="add-a-new-application-package"></a>Dodaj nowy pakiet aplikacji
Aby dodać nową wersję pakietu aplikacji dla istniejącej aplikacji, wybierz aplikację w **aplikacji** bloku, kliknij przycisk **pakiety**, następnie kliknij przycisk **Dodaj** otworzyć **Dodaj pakiet** bloku.

![Dodawanie bloku pakietu aplikacji w portalu Azure][8]

Jak widać, pól zgodnie z regułami **nowej aplikacji** bloku, ale **identyfikator aplikacji** pole jest wyłączone. Tak samo jak dla nowej aplikacji, określ **wersji** nowego pakietu, przejdź do Twojej **pakiet aplikacji** zip pliku, a następnie kliknij przycisk **OK** do przekazania pakietu.

### <a name="update-or-delete-an-application-package"></a>Aktualizowanie lub usuwanie pakietu aplikacji
Aby zaktualizować lub usunąć istniejący pakiet aplikacji, Otwórz szczegóły blok dla aplikacji, kliknij przycisk **pakiety** otworzyć **pakiety** bloku, kliknij przycisk **wielokropka** w wiersz pakietu aplikacji, który chcesz zmodyfikować, a następnie wybierz akcję, którą chcesz wykonać.

![Aktualizowanie lub usuwanie pakietu w portalu Azure][7]

**Aktualizacja**

Po kliknięciu **aktualizacji**, *pakiet aktualizacji* bloku jest wyświetlany. Ten blok jest podobny do *nowy pakiet aplikacji* bloku, jednak tylko pole wyboru pakietu jest włączona, co pozwoli określić nowy plik ZIP i przekazać.

![Blok pakietu aktualizacji w portalu Azure][11]

**Usuwanie**

Po kliknięciu **usunąć**, zostanie wyświetlona prośba o potwierdzenie usunięcia wersji pakietu i partii usuwa pakiet z usługi Magazyn Azure. Jeśli usuniesz domyślnej wersji aplikacji, **wersja domyślna** ustawienie zostanie usunięte z aplikacji.

![Usuwanie aplikacji ][12]

## <a name="install-applications-on-compute-nodes"></a>Instalowanie aplikacji na węzły obliczeniowe
Teraz, kiedy znasz już jak zarządzać pakietami aplikacji z portalu Azure, można omówiono sposób wdrożenia ich na węzłach obliczeniowych i uruchom je z zadań wsadowych.

### <a name="install-pool-application-packages"></a>Zainstaluj pakiety aplikacji w puli
Aby zainstalować pakiet aplikacji we wszystkich węzłach obliczeń w puli, należy określić co najmniej jeden pakiet aplikacji *odwołania* puli. Pakiety aplikacji, które określają dla puli są instalowane w każdym węźle obliczeń, w tym węźle sprzężenia puli oraz w przypadku węzła jest ponownego rozruchu lub odtworzenia z obrazu.

W partiami platformy .NET, należy określić jedną lub więcej [CloudPool][net_cloudpool].[ ApplicationPackageReferences] [ net_cloudpool_pkgref] podczas tworzenia nowej puli lub dla istniejącej puli. [ApplicationPackageReference] [ net_pkgref] klasa określa węzły obliczeniowe identyfikator i wersję aplikacji do zainstalowania w puli.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

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
> Jeśli wdrożenie pakietu aplikacji nie powiedzie się z jakiegokolwiek powodu, usługa partia zadań oznacza węzeł [bezużyteczne][net_nodestate], i zadania nie są zaplanowane do uruchomienia w tym węźle. W takim przypadku należy **ponowne uruchomienie** węzeł, aby ponownie zainicjować wdrożenia pakietu. Ponowne uruchomienie węzła umożliwia również w węźle ponownie harmonogramy zadań.
> 
> 

### <a name="install-task-application-packages"></a>Zainstaluj pakiety aplikacji zadań
Podobnie do puli, należy określić pakiet aplikacji *odwołania* zadania. Gdy zadanie jest zaplanowane do uruchomienia w węźle, pakiet jest pobrane i wyodrębnione tuż przed wykonaniem zadania wiersza polecenia. Jeśli określonego pakietu i wersja jest już zainstalowany w węźle, pakiet nie jest pobierana i jest używany istniejący pakiet.

Aby zainstalować pakiet aplikacji zadań, należy skonfigurować zadania [CloudTask][net_cloudtask].[ ApplicationPackageReferences] [ net_cloudtask_pkgref] właściwości:

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

## <a name="execute-the-installed-applications"></a>Wykonanie zainstalowanych aplikacji
Pakiety, które zostały określone dla puli lub zadania są pobrane i wyodrębnione do katalogu o nazwie w `AZ_BATCH_ROOT_DIR` węzła. Partii tworzy również zmienną środowiskową, który zawiera ścieżkę do katalogu o nazwie. Wiersze poleceń z zadań Użyj tej zmiennej środowiskowej podczas odwoływania się do aplikacji w węźle. 

W węzłach Windows zmienna jest w następującym formacie:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

W węzłach Linux format różni się nieznacznie. Kropki (.), łączniki (-) i znaki numeru (#) są jako spłaszczone podkreślenia w zmiennej środowiskowej. Zauważ również, czy zostaną zachowane w przypadku Identyfikatora aplikacji. Na przykład:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` i `version` to wartości, które odpowiadają wersji aplikacji i pakietów zostały określone dla wdrożenia. Na przykład, jeśli określono tej wersji 2.7 aplikacji *mieszarce* należy zainstalować na węzłach systemu Windows, Twoje wiersze polecenia zadań użyje tej zmiennej środowiskowej dostęp do swoich plików:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

W węzłach Linux Określ zmienną środowiskową w tym formacie. Spłaszczanie kropki (.), łączniki (-) i podkreślenia numer znaki (#) i zachowanie w przypadku Identyfikatora aplikacji:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Podczas przekazywania pakietu aplikacji, można określić domyślnej wersji do wdrożenia węzłów obliczeniowych. Jeśli określono domyślnej wersji aplikacji, sufiks wersji można pominąć w przypadku odwołania do aplikacji. Można określić domyślnej wersji aplikacji w portalu Azure, w bloku aplikacji, jak pokazano w [przekazywanie aplikacji i zarządzanie nimi](#upload-and-manage-applications).

Na przykład jeśli ustawisz "2.7", jako wersja domyślna dla aplikacji *mieszarce*i zadania odwołują się następujące zmiennej środowiskowej, a następnie węzły Windows wykona wersji 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Poniższy fragment kodu przedstawia przykład wiersz polecenia zadania, który uruchamia domyślnej wersji *mieszarce* aplikacji:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Zobacz [ustawienia środowiska dla zadań](batch-api-basics.md#environment-settings-for-tasks) w [Przegląd funkcji partii](batch-api-basics.md) Aby uzyskać więcej informacji na temat ustawień środowiska węzła obliczeń.
> 
> 

## <a name="update-a-pools-application-packages"></a>Aktualizowanie pakietów aplikacji puli
Jeśli skonfigurowano już istniejącą pulę z pakietem aplikacji, można określić nowy pakiet dla tej puli. Jeśli określisz nowe odwołanie pakietu dla puli, następujących warunków:

* Usługa partia zadań instaluje nowo określonego pakietu na wszystkie nowe węzły, które Dołącz do puli oraz na istniejący węzeł, który jest ponownego rozruchu lub odtworzenia z obrazu.
* Obliczeń węzłów, które są już w puli, gdy aktualizacja odwołania do pakietu nie są instalowane automatycznie nowy pakiet aplikacji. Obliczeniowe te węzły muszą być ponowny rozruch lub odtworzyć z obrazu do odbierania nowego pakietu.
* Po wdrożeniu nowego pakietu, zmienne środowiskowe utworzony odzwierciedlają nowego odwołania do pakietu aplikacji.

W tym przykładzie istniejącej puli ma wersji 2.7 *mieszarce* aplikacji skonfigurowany jako jeden z jego [CloudPool][net_cloudpool].[ ApplicationPackageReferences][net_cloudpool_pkgref]. Aby zaktualizować węzłów w puli z wersją 2.76b, określić nową [ApplicationPackageReference] [ net_pkgref] z nową wersję oraz zatwierdzić zmiany.

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

Teraz, nowa wersja została skonfigurowana, usługa partia zadań powoduje zainstalowanie wersji 2.76b do dowolnego *nowe* węzła, w której jest przyłączany puli. Aby zainstalować 2.76b w węzłach, które są *już* w puli, ponownego uruchamiania lub je odtworzyć. Należy zwrócić uwagę zachowanie pliki z poprzedniego wdrożenia pakietu po ponownym rozruchu węzłów.

## <a name="list-the-applications-in-a-batch-account"></a>Wyświetl listę aplikacji w ramach konta usługi partia zadań
Można wyświetlić listę aplikacji i ich pakietów w ramach konta usługi partia zadań, za pomocą [ApplicationOperations][net_appops].[ ListApplicationSummaries] [ net_appops_listappsummaries] metody.

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

## <a name="wrap-up"></a>Dobiega końca
Z pakietów aplikacji możesz pomóc klientom Wybierz aplikacje do ich zadań i określić dokładną wersję do użycia podczas przetwarzania zadania z usługą przetwarzania wsadowego. Można również określić przez klientów do przekazywania i śledzenie własnych aplikacji w usłudze.

## <a name="next-steps"></a>Kolejne kroki
* [Interfejsu API REST partii] [ api_rest] obsługuje również do pracy z pakietami aplikacji. Na przykład, zobacz [applicationPackageReferences] [ rest_add_pool_with_packages] element [Dodaj pulę, aby konto] [ rest_add_pool] informacji o sposobie określania pakiety do zainstalowania przy użyciu interfejsu API REST. Zobacz [aplikacji] [ rest_applications] szczegółowe informacje na temat sposobu uzyskiwania informacji o aplikacji przy użyciu interfejsu API REST partii.
* Dowiedz się, jak programowo [Zarządzanie kontami partii zadań Azure i przydziały zarządzania partiami platformy .NET](batch-management-dotnet.md). [Zarządzania partiami platformy .NET][api_net_mgmt] biblioteki można włączyć funkcji Tworzenie i usuwanie konta wsadowego aplikacji lub usługi.

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

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagram ogólny pakietów aplikacji"
[2]: ./media/batch-application-packages/app_pkg_02.png "Kafelka aplikacji w portalu Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Blok aplikacje w portalu Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Blok szczegółów aplikacji w portalu Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nowy blok aplikacji w portalu Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Aktualizowanie lub usuwanie pakietów listy rozwijanej w portalu Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nowy blok pakietu aplikacji w portalu Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Alert nie połączonego konta magazynu"
[10]: ./media/batch-application-packages/app_pkg_10.png "Wybierz bloku konto magazynu w portalu Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Blok pakietu aktualizacji w portalu Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Usuń okno dialogowe potwierdzenia pakiet w portalu Azure"
