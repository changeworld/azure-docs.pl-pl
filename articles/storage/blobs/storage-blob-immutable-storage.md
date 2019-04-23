---
title: Niezmienny magazyn obiektów blob usługi Azure Storage | Dokumentacja firmy Microsoft
description: Usługa Azure Storage oferuje obsługę ROBAK (jednokrotny zapis, Odczyt wiele) dla magazynu obiektów Blob (obiekt), umożliwiającą użytkownikom przechowywanie danych w stanie wymazanie, nie można modyfikować w określonym interwale.
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 04/18/2019
ms.author: hux
ms.subservice: blobs
ms.openlocfilehash: 7fd9992db79b2517256d85ca3fd8f3bf409afa48
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996034"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Store strategicznych danych biznesowych w usłudze Azure Blob storage

Niezmienny magazyn dla usługi Azure Blob storage pozwala użytkownikom przechowywać obiekty strategicznych danych biznesowych w stanie ROBAK (jednokrotny zapis, wiele odczytu). Ten stan sprawia, że dane trwałe i nie można modyfikować dla interwału określonego przez użytkownika. Obiekty BLOB można utworzyć i odczytać, ale nie zmodyfikowany lub usunięty na czas trwania okresu przechowywania. Niezmienny magazyn jest włączona dla ogólnego przeznaczenia w wersji 2 i kont usługi Blob Storage we wszystkich regionach platformy Azure.

## <a name="overview"></a>Omówienie

Niezmienne storage pomaga organizacji opieki zdrowotnej, instytucje finansowe i powiązanych branżach — szczególnie dealer brokera organizacjom — bezpiecznie przechowywać dane. On również nadającego się w każdym scenariuszu, w celu ochrony danych krytycznych, modyfikacji lub usunięcia. 

Typowe zastosowania tej funkcji to:

- **Zgodność z przepisami**: Niezmienny magazyn dla usługi Azure Blob storage ułatwia organizacjom adres s 17a-4(f), CFTC 1.31(d), FINRA i innych przepisów. Oficjalny dokument techniczny, kojarzy Cohasset szczegóły jak niezmienne adresów pamięci masowej te wymagania prawne jest dostępny do pobrania za pośrednictwem [Portal zaufania usługi Microsoft](https://aka.ms/AzureWormStorage). [Centrum zaufania systemu Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) zawiera szczegółowe informacje na temat naszych związane ze zgodnością.

- **Zabezpieczanie przechowywania dokumentu**: Niezmienny magazyn dla usługi Azure Blob storage zapewnia, że danych nie może być zmodyfikowane lub usunięte przez dowolnego użytkownika, w tym użytkowników z uprawnieniami administracyjnymi konta.

- **Prawnych**: Niezmienny magazyn dla usługi Azure Blob storage pozwala użytkownikom przechowywać poufne informacje, które mają kluczowe znaczenie dla postępowań prawnych lub business na użytek w stanie odporną na manipulacje określonego czasu trwania do momentu usunięcia blokady. Ta funkcja nie jest ograniczona tylko do przypadków użycia prawnych, ale może również być uważane za wstrzymanie oparte na zdarzeniach lub blokowanie przedsiębiorstwa, których potrzeba ochrony danych na podstawie wyzwalaczy zdarzeń lub zasad firmy jest wymagane.

Niezmienny magazyn obsługuje następujące funkcje:

- **[Obsługa zasad przechowywania na podstawie czasu](#time-based-retention)**: Użytkownicy mogą ustawić zasady przechowywania danych w określonym przedziale czasu. Podczas zasady przechowywania na podstawie czasu ustawiony, obiektów blob można utworzyć i odczytać, ale nie zmodyfikowane lub usunięte. Po upływie okresu przechowywania obiektów blob można usunąć, ale nie zostały zastąpione.

- **[Obsługa zasad ze względów prawnych](#legal-holds)**: Jeśli okres przechowywania nie jest znany, użytkownicy mogą ustawić archiwizacją ze względów prawnych do przechowywania danych immutably, dopóki nie zostanie wyczyszczona prawnych.  Jeśli ustawiono zasady prawnych, obiektów blob można utworzyć i odczytać, ale nie zmodyfikowane lub usunięte. Każdy prawnych jest skojarzony z zdefiniowanych przez użytkownika alfanumeryczne tag (na przykład identyfikator przypadku, Nazwa zdarzenia, itp.), który jest używany jako ciąg identyfikatora. 

- **Pomoc techniczna dla wszystkich obiektów blob warstw**: ROBAK niezależnie od warstwy magazynu obiektów Blob platformy Azure i zasad dotyczą wszystkich warstwy: gorąca, chłodna i archiwum. Użytkownicy mogą przechodzą danych do warstwy większość optymalizacji kosztów dla swoich obciążeń przy zachowaniu danych niezmienności.

- **Konfiguracji na poziomie kontenera**: Użytkownicy mogą konfigurować zasady przechowywania na podstawie czasu i ze względów prawnych tagi na poziomie kontenera. Przy użyciu prostych ustawień na poziomie kontenera, użytkowników można utworzyć i Zablokuj zasady przechowywania na podstawie czasu, rozszerzyć interwały przechowywania, zestaw i archiwizacją wyczyść ze względów prawnych i nie tylko. Te zasady mają zastosowanie do wszystkich obiektów blob w kontenerze, nowych i istniejących.

- **Obsługa rejestrowania inspekcji**: Każdy kontener zawiera dziennik inspekcji zasad. Pokazuje maksymalnie siedem na podstawie czasu przechowywania polecenia dla zasad przechowywania na podstawie czasu zablokowana i zawiera identyfikator użytkownika, typ polecenia, sygnatury czasowe i interwał przechowywania. W przypadku archiwizacją ze względów prawnych dziennik zawiera identyfikator użytkownika, wpisz polecenie sygnatury czasowe i tagi ze względów prawnych. Ten dziennik został zachowany na potrzeby okres istnienia zasady, zgodnie z wytycznymi przepisami 17a-4(f) s. [Dziennika aktywności platformy Azure](../../azure-monitor/platform/activity-logs-overview.md) pokazują dziennik bardziej kompleksowe wszystkich działań płaszczyzna kontroli; podczas włączania [dzienniki diagnostyczne platformy Azure](../../azure-monitor/platform/diagnostic-logs-overview.md) zachowuje i pokazuje operacje płaszczyzny danych. Odpowiada za użytkownika trwałe, jak mogą być wymagane do celów przepisami lub innymi przechowywania tych dzienników.

## <a name="how-it-works"></a>Jak to działa

Niezmienny magazyn dla usługi Azure Blob storage obsługuje dwa typy ROBAK lub niezmienne zasad: przechowywania na podstawie czasu i archiwizacją ze względów prawnych. Jeśli zasady przechowywania na podstawie czasu lub prawnych jest stosowany do kontenera, wszystkie istniejące obiekty BLOB przenieść się do niezmiennego stanu ROBAK w mniej niż 30 sekund. Wszystkie nowe obiekty BLOB, które są przekazywane do tego kontenera zostanie również przenieść do niezmiennego stanu. Po wszystkich obiektów blob zostały przeniesione do niezmiennego stanu, potwierdzono niezmienne zasad i wszystkie zastąpienia lub usuń operacje dla istniejących i nowych obiektów w kontenerze niezmienialnych nie są dozwolone.

Kontener i usuwanie kont również nie są dozwolone w przypadku wszystkie obiekty BLOB, chronione przez zasady niezmienne. Operacja usuwania kontenera nie powiedzie się, jeśli co najmniej jeden obiekt blob istnieje zasady przechowywania na podstawie czasu zablokowane lub prawnych. Usunięcie konta magazynu nie powiedzie się, jeśli zawiera ono co najmniej jeden kontener z zasadami WORM i ustawionym stanem archiwizacji ze względów prawnych lub co najmniej jeden obiekt blob z aktywnym okresem przechowywania. 

### <a name="time-based-retention"></a>Na podstawie czasu przechowywania

> [!IMPORTANT]
> Zasady przechowywania na podstawie czasu musi być *zablokowane* dla obiektu blob w niezmienne (zapisu i usuwania chroniony) stanie 17a-4(f) s a innymi zgodność z przepisami. Zaleca się blokowanie zasady w rozsądnym czasie, zwykle w ciągu 24 godzin. Nie zalecamy używania *odblokowane* stanu w celu innym niż krótkoterminowej funkcji wersji próbnych.

Stosowania zasad przechowywania na podstawie czasu na kontenerze wszystkich obiektów blob w kontenerze pozostaną niezmiennego stanu na czas trwania *skuteczne* okres przechowywania. Okres przechowywania obowiązujące dla istniejących obiektów blob jest równy różnicy czas modyfikacji obiektu blob i okres przechowywania określony przez użytkownika.

W przypadku nowych obiektów blob obowiązujący okres przechowywania jest równy okresowi przechowywania określonemu przez użytkownika. Ponieważ użytkownicy mogą wydłużyć okres przechowywania, niezmienne magazynu używa najnowszą wartość okres przechowywania określony przez użytkownika do obliczenia okres przechowywania skuteczne.

> [!TIP]
> **Przykład:** Użytkownik tworzy zasady przechowywania na podstawie czasu z interwałem przechowywania przez okres pięciu lat.
>
> Istniejący obiekt blob w kontenerze, w tym _testblob1_, utworzono rok temu. Okres przechowywania skuteczne _testblob1_ jest 4 lata.
>
> Nowy obiekt blob _testblob2_, teraz zostanie przekazany do kontenera. Okres przechowywania obowiązywać dla tego nowy obiekt blob jest pięć lat.

### <a name="legal-holds"></a>Archiwizacja ze względów prawnych

Po ustawieniu prawnych, wszystkie istniejące i nowe obiekty BLOB są pozostanie w stanie niezmienne, dopóki nie zostanie wyczyszczona prawnych. Aby uzyskać więcej informacji na temat zestawu i archiwizacją wyczyść ze względów prawnych, zobacz [wprowadzenie](#getting-started) sekcji.

Kontener może mieć zarówno prawnych, jak i zasad przechowywania na podstawie czasu, w tym samym czasie. Wszystkie obiekty BLOB w kontenerze pozostanie w stanie niezmienne, dopóki nie zostaną wyczyszczone wszystkie archiwizacją ze względów prawnych, nawet wtedy, gdy ich okresu przechowywania skuteczne utracił ważność. Z drugiej strony obiekt blob pozostaje w niezmiennego stanu do wygaśnięcia okresu przechowywania skuteczne, nawet jeśli zostały wyczyszczone wszystkie archiwizacją ze względów prawnych.

W poniższej tabeli przedstawiono typy obiekty blob — operacje, które są wyłączone dla różnych scenariuszy niezmienne. Aby uzyskać więcej informacji, zobacz [interfejsu API usługi Azure Blob Service](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) dokumentacji.

|Scenariusz  |Stan obiektu blob  |Operacje obiektów blob nie jest dozwolone  |
|---------|---------|---------|
|Trwa obowiązujący okres przechowywania obiektu blob i/lub ustawiono stan archiwizacji ze względów prawnych     |Niezmienny: ochrona przed usuwaniem i zapisem         | Wstawienie obiektu Blob<sup>1</sup>, umieść bloku<sup>1</sup>, umieść zablokowanych<sup>1</sup>, usuń metadane obiektu Blob zestaw kontenerów, Usuń obiekt Blob, umieść strony, ustaw właściwości obiektu Blob, wykonanie migawki obiektu Blob, obiektu Blob kopiowania przyrostowego Dołącz bloku         |
|Upłynął obowiązujący okres przechowywania obiektu blob     |Ochrona tylko przed zapisem (operacje usuwania są dozwolone)         |Wstawienie obiektu Blob<sup>1</sup>, umieść bloku<sup>1</sup>, umieść zablokowanych<sup>1</sup>, ustaw metadane obiektu Blob, umieść strony, ustawianie obiektu Blob kopiowania przyrostowego właściwości, wykonywanie migawki obiektu Blob, obiektów Blob, Dołącz bloku         |
|Wszystkie informacje prawne przechowuje wyczyszczone, a w kontenerze są ustawione żadne zasady przechowywania na podstawie czasu     |Modyfikowalny         |Brak         |
|Brak zasad ROBAK zostanie utworzony (na podstawie czasu przechowywania lub prawnych)     |Modyfikowalny         |Brak         |

<sup>1</sup> aplikacja pozwala te operacje utworzyć nowy obiekt blob jeden raz. Wszystkie kolejne zastąpić operacje na istniejącą ścieżkę obiektu blob w kontenerze niezmienialnych nie są dozwolone.

## <a name="supported-values"></a>Obsługiwane wartości

### <a name="time-based-retention"></a>Na podstawie czasu przechowywania
- Dla konta magazynu maksymalną liczbę kontenerów zasadami zablokowany na podstawie czasu niezmienne wynosi 1000.
- Interwał przechowywania minimalną jest 1 dzień. Wartość maksymalna to 146,000 dni (400 lat).
- Dla kontenera maksymalna liczba edycji rozszerzenie interwał przechowywania, zablokowane na podstawie czasu niezmienne zasad wynosi 5.
- Dla kontenera maksymalnie 7 dzienników inspekcji zasad przechowywania na podstawie czasu są przechowywane przez czas trwania zasad.

### <a name="legal-hold"></a>Prawnych
- Dla konta magazynu maksymalną liczbę kontenerów z ustawieniem prawnych wynosi 1000.
- Dla kontenera maksymalną liczbę tagów prawnych wynosi 10.
- Minimalna długość znacznika prawnych wynosi 3 znaki alfanumeryczne. Maksymalna długość wynosi 23 znaków alfanumerycznych.
- Dla kontenera maksymalnie 10 prawne przytrzymaj zasady inspekcji, dzienniki są zachowywane w czasie trwania operacji zasad.

## <a name="pricing"></a>Cennik

Nie ma żadnych dodatkowych opłat za używanie tej funkcji. Niezmienialnymi danymi jest rozliczana w taki sam sposób, jak regularne, mutable danych. Aby uzyskać szczegóły cennika w usłudze Azure Blob Storage, zobacz [usługi Azure Storage, cennik](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Wprowadzenie
Niezmienne storage jest dostępna tylko w przypadku ogólnego przeznaczenia w wersji 2 i kont usługi Blob Storage. Te konta muszą być zarządzane za pośrednictwem [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Aby uzyskać informacje na temat aktualizowania istniejącego konta magazynu ogólnego przeznaczenia w wersji 1, zobacz [podnoszenie poziomu konta magazynu](../common/storage-account-upgrade.md).

Najnowsze wersje [witryny Azure portal](https://portal.azure.com), [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), i [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases) obsługiwać niezmienny magazyn dla usługi Azure Blob storage. [Obsługa bibliotek klienta](#client-libraries) jest również udostępniany.

### <a name="azure-portal"></a>Azure Portal

1. Utwórz nowy kontener lub wybierz istniejący kontener, w którym będą przechowywane obiekty blob wymagające przechowywania w stanie niezmiennym.
 Kontener musi być na koncie GPv2 lub usługi blob storage.
2. Wybierz **zasady dostępu** w ustawieniach kontenera. Następnie wybierz pozycję **+ Dodaj zasady** w obszarze **niezmienny magazyn obiektów blob**.

    ![Ustawienia kontenera w witrynie portal](media/storage-blob-immutable-storage/portal-image-1.png)

3. Aby włączyć na podstawie czasu przechowywania, wybierz **na podstawie czasu przechowywania** z menu rozwijanego.

    !["Czas przechowywania na podstawie" wybranego w obszarze "Zasady type"](media/storage-blob-immutable-storage/portal-image-2.png)

4. Wprowadź interwał przechowywania w dniach (dopuszczalne wartości 1 i 146000 dni).

    ![Pole "Okres przechowywania aktualizacji do"](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Początkowy stan zasad jest odblokowany, dzięki czemu możesz przetestować funkcję i wprowadzanie zmian z zasadami, zanim je zablokować. Zasady blokowania ma zasadnicze znaczenie dla zgodności z przepisami, takich jak s 17a-4.

5. Zablokuj zasady. Kliknij prawym przyciskiem myszy wielokropek (**...** ), i za pomocą dodatkowych akcji zostanie wyświetlone następujące menu:

    !["Blokowanie zasad" w menu](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. Wybierz **zasady blokowania** i upewnij się, blokady. Zasada jest zablokowany i nie można usunąć, może być tylko rozszerzenia okres przechowywania. Usuwa obiekt blob i zastąpienia są niedozwolone. 

    ![Upewnij się, "Zablokuj policy" w menu](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. Aby włączyć archiwizacją ze względów prawnych, zaznacz **+ Dodaj zasady**. Wybierz **prawnych** z menu rozwijanego.

    !["Ze względów prawnych" z menu "Typ zasad"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Utwórz prawnych z co najmniej jednego znacznika.

    ![Pole "Nazwa tagu" w obszarze Typ zasad](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. Aby wyczyścić prawnych, po prostu usunąć tag identyfikatora zastosowane prawnych.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Funkcja jest dostępna w następujących grupach polecenia: `az storage container immutability-policy` i `az storage container legal-hold`. Uruchom `-h` je, aby wyświetlić polecenia.

### <a name="powershell"></a>PowerShell

Moduł Az.Storage (wersja zapoznawcza) obsługuje magazyn niezmienne.  Aby włączyć tę funkcję, wykonaj następujące kroki:

1. Upewnij się, że masz najnowszą wersję zainstalowanego modułu PowerShellGet: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Usuń wszelkie poprzedniej instalacji programu Azure PowerShell.
3. Instalowanie programu Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.
4. Zainstaluj wersję zapoznawczą modułu programu PowerShell dla usługi Azure Storage: `Install-Module Az.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

[PowerShell przykładowy kod](#sample-powershell-code) sekcję w dalszej części tego artykułu przedstawiono użycie funkcji.

## <a name="client-libraries"></a>Biblioteki klienta

Podanych niżej bibliotek klienta obsługują niezmienny magazyn dla usługi Azure Blob storage:

- [7.2.0-preview wersji biblioteki klienckiej platformy .NET i nowsze](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Biblioteki klienta node.js w wersji 4.0.0 lub nowszy](https://www.npmjs.com/package/azure-arm-storage)
- [Biblioteka klientów języka Python w wersji 2.0.0 w wersji Release Candidate 2 lub nowszy](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Biblioteki klienta Java](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>Często zadawane pytania

**Można podać dokumentacji ROBAK zgodności?**

Tak. Zapewnienie zgodności dokumentu Microsoft przechowywane wiodące przedsiębiorstwa niezależną ocenę, która specjalizuje się w rekordy zarządzania i informacje infrastruktur Cohasset partnerów, aby ocenić niezmienny magazyn obiektów Blob platformy Azure i jego zgodność z wymaganiami określonymi w branży usług finansowych. Cohasset zweryfikować, czy Azure niezmienne magazynu obiektów Blob, gdy jest używana do przechowywania na podstawie czasu obiektów blob w stanie ROBAK spełnia wymagania magazynu odpowiednie reguły CFTC 1.31(c)-(d), 4511 reguły FINRA i reguły s 17a-4. Microsoft celem tego zestawu reguł, ponieważ stanowią one najbardziej wskazówki globalnie dla przechowywania rekordów dla instytucji finansowych. Raport Cohasset jest dostępny w [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage).

**Czy ta funkcja dotyczy tylko blokowe obiekty BLOB, lub na stronie obiektów blob i uzupełnialnych obiektów blob oraz?**

Niezmienny magazyn może być używany z żadnym typem obiektów blob, ale zaleca się używać przede wszystkim dla blokowych obiektów blob. Inaczej niż w przypadku blokowych obiektów blob strony obiekty BLOB i uzupełnialnych obiektów blob należy utworzyć poza kontenerem ROBAK, a następnie kopiowane w. Po skopiowaniu tych obiektów blob w kontenerze ROBAK nie dalsze *dołącza* do dołączania obiektu blob lub zmiany stronicowych obiektów blob są dozwolone.

**Należy utworzyć nowe konto magazynu, aby użyć tej funkcji?**

Nie można użyć magazynu niezmienne z istniejących i nowo utworzony ogólnego przeznaczenia w wersji 2 lub kont usługi Blob storage. Ta funkcja jest przeznaczone do użycia z blokowych obiektów blob na kontach GPv2 i Blob Storage. Konta magazynu w wersji 1 ogólnego przeznaczenia nie są obsługiwane, ale można łatwo uaktualnić do ogólnego przeznaczenia w wersji 2. Aby uzyskać informacje na temat aktualizowania istniejącego konta magazynu ogólnego przeznaczenia w wersji 1, zobacz [podnoszenie poziomu konta magazynu](../common/storage-account-upgrade.md).

**Czy mogę stosować prawnych i zasady przechowywania na podstawie czasu?**

Tak, kontener może mieć zarówno prawnych, jak i zasad przechowywania na podstawie czasu w tym samym czasie. Wszystkie obiekty BLOB w kontenerze pozostanie w stanie niezmienne, dopóki nie zostaną wyczyszczone wszystkie archiwizacją ze względów prawnych, nawet wtedy, gdy ich okresu przechowywania skuteczne utracił ważność. Z drugiej strony obiekt blob pozostaje w niezmiennego stanu do wygaśnięcia okresu przechowywania skuteczne, nawet jeśli zostały wyczyszczone wszystkie archiwizacją ze względów prawnych.

**Czy zasady prawnych tylko w przypadku postępowania, lub czy istnieją inne scenariusze użycia?**

Nie, prawne przechowywania jest po prostu ogólny termin używany dla zasad przechowywania bez oparte na czasie. Nie musi być używane wyłącznie dla postępowań prawnych dotyczące postępowania. Prawne zasady blokady są przydatne do wyłączenie zastępowania i usuwania ochrony przedsiębiorstwa ważne dane ROBAK, gdy okres przechowywania jest nieznany. Może używać go jako zasady przedsiębiorstwa do ochrony Twojej obciążeń o znaczeniu krytycznym ROBAK lub użyj go jako zasad tymczasowych przed wyzwalacz zdarzenia niestandardowe wymaga użycia zasad przechowywania na podstawie czasu. 

**Co się stanie, jeśli spróbuję usunąć kontener z *zablokowanymi* zasadami przechowywania na podstawie czasu lub z ustawionym stanem archiwizacji ze względów prawnych?**

Operacja usuwania kontenera nie powiedzie się, jeśli co najmniej jeden obiekt blob istnieje zasady przechowywania na podstawie czasu zablokowane lub prawnych. Operacja usuwania kontenera powiedzie się tylko wtedy, gdy istnieje nie obiektów blob, interwał przechowywania aktywne i nie ma żadnych archiwizacją ze względów prawnych. Obiekty BLOB należy usunąć przed usunięciem kontenera.

**Co się stanie, jeśli spróbuję usunąć konto magazynu zawierające kontener z zasadami WORM — *zablokowanymi* zasadami przechowywania na podstawie czasu lub ustawionym stanem archiwizacji ze względów prawnych?**

Usunięcie konta magazynu nie powiedzie się, jeśli zawiera ono co najmniej jeden kontener z zasadami WORM i ustawionym stanem archiwizacji ze względów prawnych lub co najmniej jeden obiekt blob z aktywnym okresem przechowywania. Należy usunąć wszystkie kontenery ROBAK, zanim będzie możliwe usunięcie konta magazynu. Instrukcje dotyczące usuwania kontenera zobacz poprzedni pytanie.

**Czy mogę przenosić dane pomiędzy warstwami magazynowania (gorącą, chłodną, zimną), gdy obiekt blob znajduje się w stanie niezmiennym?**

Tak, można użyć polecenia Ustawianie warstwy obiektu Blob do przenoszenia danych we wszystkie warstwy obiektu blob, zachowując dane w zgodne niezmiennego stanu. Niezmienny magazyn jest obsługiwany na gorąca, chłodna i archiwalna obiektu blob.

**Co się stanie, jeśli nie uiszczę opłaty, a okres przechowywania jeszcze nie wygasł?**

W przypadku płatności zasady przechowywania zwykłych danych będzie stosowana zgodnie z przepisami w warunki i postanowienia Umowy z firmą Microsoft.

**Czy jest oferowany okres próbny, umożliwiający przetestowanie tej funkcji?**

Tak. Podczas tworzenia zasady przechowywania na podstawie czasu jest *odblokowane* stanu. W tym stanie można żądane zmiany na okres przechowywania, np. zwiększenie lub zmniejszenie i nawet usunąć zasady. Po zablokowaniu zasady pozostaje on zablokowany do momentu wygaśnięcia ważności okres przechowywania. Te zablokowane zasady uniemożliwia usunięcia i modyfikacji okres przechowywania. Zdecydowanie zalecamy użycie *odblokowane* stanu tylko do celów wersji próbnej i Zablokuj zasady w okresie 24-godzinnym. Poniższe wskazówki ułatwiają 17a-4(f) s i innych przepisów.

**Czy można używać usuwania nietrwałego wraz z zasady niezmienne obiektów blob?**

Tak. [Usuwanie nietrwałe dla usługi Azure Blob storage](storage-blob-soft-delete.md) ma zastosowanie do wszystkich kontenerów na koncie magazynu, niezależnie od tego, czy prawnych lub zasady przechowywania na podstawie czasu. Zalecamy włączenie usuwania nietrwałego, aby uzyskać dodatkową ochronę przed wszystkie niezmienne zasady ROBAK są stosowane i potwierdzone. 

**Gdzie jest funkcja?**

Niezmienne storage jest dostępna w regionach świadczenia publicznej platformy Azure (Chiny) i dla instytucji rządowych. Jeśli w Twoim regionie nie ma dostępnego magazynu niezmienne, skontaktuj się z pomocy technicznej i wiadomości e-mail azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Przykładowy kod programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Przykładowy skrypt programu PowerShell jest dla odwołania. Ten skrypt tworzy nowe konto magazynu i kontener. Go następnie pokazano, jak ustawić i wyczyść archiwizacją ze względów prawnych, Utwórz zablokować zasady przechowywania na podstawie czasu (znanego również jako zasady niezmienności) i wydłużyć okres przechowywania.

Skonfiguruj i przetestuj konta usługi Azure Storage:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Ustawić lub wyczyścić archiwizacją ze względów prawnych:

```powershell
# Set a legal hold
Add-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Utwórz lub zaktualizuj zasady niezmienności:
```powershell
# with an account name or container name
Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Pobierz zasady niezmienności:
```powershell
# Get an immutability policy
Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzStorageContainerImmutabilityPolicy -Container $containerObject
```

Zablokuj zasady niezmienności (Dodaj - Force, aby zamknąć wiersz):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Rozszerz zasady niezmienności:
```powershell

# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Usuń zasady niezmienności (Dodaj - Force, aby zamknąć wiersz):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
