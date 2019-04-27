---
title: Typowe kody błędów maszyny Wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Zrozumieć niektóre typowe kody błędów napotkanych podczas obsługi administracyjnej i zarządzanie maszynami wirtualnymi na platformie Azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: 5945be210812a6cbc24c9a3bb12414be5212be17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711207"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Zrozumienie typowe komunikaty o błędach, gdy można zarządzać maszynami wirtualnymi na platformie Azure

W tym artykule opisano niektóre najbardziej typowe kody błędów i komunikaty, które można napotkać podczas tworzenia lub zarządzać maszyn wirtualnych (VM) na platformie Azure.

>[!NOTE]
> Można zamieszczać komentarze na tej stronie, aby przekazać opinie, lub za pomocą [opinii platformy Azure](https://feedback.azure.com/forums/216843-virtual-machines) #azerrormessage znacznika.

## <a name="error-response-format"></a>Błąd formatu odpowiedzi 
Maszyny wirtualne platformy Azure, użyj następującego formatu JSON dla odpowiedzi na błąd:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Odpowiedź na błąd zawsze zawiera kod stanu i obiekt błędu. Każdy obiekt błędu zawsze zawiera kod błędu oraz komunikat. Jeśli maszyna wirtualna jest tworzona przy użyciu szablonu, obiekt błędu zawiera także sekcji Szczegóły, który zawiera wewnętrzny poziom nawiasów kody błędów i komunikatów. Zwykle najbardziej wewnętrzny poziom komunikat o błędzie jest błąd głównego.


## <a name="common-virtual-machine-management-errors"></a>Typowe błędy zarządzania maszyny wirtualnej

W tej sekcji przedstawiono typowe komunikaty o błędach, które można napotkać podczas zarządzania maszynami wirtualnymi:

|  Kod błędu  |  Komunikat o błędzie  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nie można ustanowić dzierżawy podczas tworzenia dysku "{0}" za pomocą obiektu blob o identyfikatorze URI {1}. Obiekt blob jest już używana.  |  
|  AllocationFailed  |  Alokacja nie powiodła się. Spróbuj zmniejszyć rozmiar maszyny Wirtualnej lub liczbę maszyn wirtualnych, spróbuj ponownie później lub spróbuj wdrożyć do innego zestawu dostępności lub innej lokalizacji platformy Azure.  |  
|  AllocationFailed  |  Alokacja maszyny Wirtualnej nie powiodło się z powodu błędu wewnętrznego. Spróbuj ponownie później lub spróbuj wdrożyć w innej lokalizacji.  |
|  ArtifactNotFound  |  Rozszerzenie maszyny Wirtualnej o wydawcy "{0}"i typie"{1}"nie można znaleźć w lokalizacji"{2}".  |
|  ArtifactNotFound  |  Rozszerzenie o wydawcy "{0}", wpisz "{1}" i wersji programu obsługi typu "{2}" nie można znaleźć w repozytorium rozszerzeń.  |
|  ArtifactVersionNotFound  |  Nie znaleziono wersji w repozytorium artefaktów odpowiadającej żądanej wersji "{0}".  |
|  ArtifactVersionNotFound  |  Nie znaleziono wersji w repozytorium artefaktów odpowiadającej żądanej wersji "{0}"dla rozszerzenia maszyny Wirtualnej o wydawcy"{1}"i typie"{2}".  |
|  AttachDiskWhileBeingDetached  |  Nie można dołączyć dysk danych "{0}"do maszyny Wirtualnej"{1}", ponieważ dysk jest obecnie odłączany. Zaczekaj, aż dysk zostanie całkowicie odłączony, a następnie spróbuj ponownie.  |
|  BadRequest  |  Wyrównane "zestawy dostępności nie są jeszcze obsługiwane w tym regionie.  |
|  BadRequest  |  Dodanie maszyny Wirtualnej z zarządzanymi dyskami do niezarządzanego zestawu dostępności lub dodanie maszyny Wirtualnej z dyskami opartymi na obiektach blob do zarządzanego zestawu dostępności nie jest obsługiwane. Utwórz zestaw dostępności z właściwością "zarządzana", ustaw, aby można było dodać Maszynę wirtualną z dyskami zarządzanymi do niego.  |
|  BadRequest  |  Dyski zarządzane nie są obsługiwane w tym regionie.  |
|  BadRequest  |  Wiele VMExtensions na program obsługi nie jest obsługiwana dla typu systemu operacyjnego "{0}". VMExtension "{1}"za pomocą programu obsługi"{2}" został już dodany lub określony w danych wejściowych.  |
|  BadRequest  |  Operacja "{0}"nie jest obsługiwana dla zasobu"{1}" z dyskami zarządzanymi.  |
|  CertificateImproperlyFormatted  |  Reprezentacja JSON klucza tajnego pobierane {0} ma pole danych, który nie jest poprawnie sformatowanym plikiem PFX lub podane hasło nie dekoduje prawidłowo pliku PFX.  |
|  CertificateImproperlyFormatted  |  Dane pobrane z {0} nie da się rozszeregować na formacie JSON.  |
|  Konflikt  |  Zmiana rozmiaru dysku jest dozwolona tylko podczas tworzenia maszyny wirtualnej lub po cofnięciu jej przydziału.  |
|  ConflictingUserInput  |  Dysk "{0}"nie można dołączyć, ponieważ dysk jest już własnością maszyny Wirtualnej"{1}".  |
|  ConflictingUserInput  |  Źródłowa grupa zasobów jest taka sama jak docelowa.  |
|  ConflictingUserInput  |  Konta magazynu źródłowego i docelowego dla dysku {0} są różne.  |
|  ContainerAlreadyOnLease  |  Istnieje już dzierżawa na kontenerze magazynu przechowującym obiekt blob z identyfikatorem URI {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Żądanie przeniesienia zasobów zawiera zasoby KeyVault, które są przywoływane przez co najmniej jeden {0}s w żądaniu. Jest to nieobsługiwane obecnie w obejmującej wiele subskrypcji przenoszenia. Sprawdź, czy szczegóły błędu dla identyfikatorów zasobów KeyVault.  |
|  DiagnosticsOperationInternalError  |  Wystąpił błąd wewnętrzny podczas przetwarzania profilu diagnostyki maszyny Wirtualnej {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Obiekt blob {0} jest już używany przez inny dysk należący do maszyny Wirtualnej "{1}". Można zbadać metadane obiektu blob, aby uzyskać informacje na dysku.  |
|  DiskBlobNotFound  |  Nie można odnaleźć obiektu blob dysku VHD za pomocą identyfikatora URI {0} dla dysku "{1}".  |
|  DiskBlobNotFound  |  Nie można odnaleźć obiektu blob dysku VHD za pomocą identyfikatora URI {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} nie ma klucza tajnego {1} tagów. Zaktualizuj wersję klucza tajnego, Dodaj wymagane tagi i spróbuj ponownie.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Dekodowanie wpisu tajnego {0} wartość z użyciem klucza {1} nie powiodło się.  |
|  DiskImageNotReady  |  Obraz dysku {0} znajduje się w {1} stanu. Spróbuj ponownie, gdy obraz będzie gotowy.  |
|  DiskPreparationError  |  Co najmniej jeden błąd wystąpił podczas przygotowywania dysków maszyny Wirtualnej. Zobacz widok wystąpienia dysku, aby uzyskać szczegółowe informacje.  |
|  DiskProcessingError  |  Przetwarzanie danych na dysku zostało zatrzymane, ponieważ maszyna wirtualna ma inne uszkodzone dyski.  |
|  ImageBlobNotFound  |  Nie można odnaleźć obiektu blob dysku VHD za pomocą identyfikatora URI {0} dla dysku "{1}".  |
|  ImageBlobNotFound  |  Nie można odnaleźć obiektu blob dysku VHD za pomocą identyfikatora URI {0}.  |
|  IncorrectDiskBlobType  |  Obiektu blob dysku mogą być tylko stronicowym obiektem blob. Obiekt blob {0} dla dysku "{1}" ma typ blokowy obiekt blob.  |
|  IncorrectDiskBlobType  |  Obiektu blob dysku mogą być tylko stronicowym obiektem blob. Obiekt blob {0} jest typu "{1}".  |
|  IncorrectImageBlobType  |  Obiektu blob dysku mogą być tylko stronicowym obiektem blob. Obiekt blob {0} dla dysku "{1}" ma typ blokowy obiekt blob.  |
|  IncorrectImageBlobType  |  Obiektu blob dysku mogą być tylko stronicowym obiektem blob. Obiekt blob {0} jest typu "{1}".  |
|  InternalOperationError  |  Nie można rozpoznać konta magazynu {0}. Upewnij się, że został on utworzony za pośrednictwem dostawcy zasobów magazynu w tej samej lokalizacji co zasób obliczeniowy.  |
|  InternalOperationError  |  {0} zadania poszukiwania celu nie powiodło się.  |
|  InternalOperationError  |  Wystąpił błąd podczas weryfikowania profil sieci maszyny wirtualnej "{0}".  |
|  InvalidAccountType  |  AccountType {0} jest nieprawidłowy.  |
|  InvalidParameter  |  Wartość parametru {0} jest nieprawidłowy.  |
|  InvalidParameter  |  Określone hasło administratora jest niedozwolone.  |
|  InvalidParameter  |  "Podane hasło musi składać się {0}-{1} znaków i musi spełniać co najmniej {2} o wymagania dotyczące złożoności hasła z następujących wartości: <ol><li> Zawiera wielką literę</li><li>Zawiera małą literę</li><li>Zawiera zawierać cyfrę</li><li>Zawiera znaki specjalne.</li></ol>  |
|  InvalidParameter  |  Określona nazwa użytkownika administratora jest niedozwolona.  |
|  InvalidParameter  |  Nie można podłączyć istniejącego dysku z systemem operacyjnym, jeśli maszyna wirtualna jest utworzona z obrazu użytkownika lub platformy.  |
|  InvalidParameter  |  Nazwa kontenera {0} jest nieprawidłowy. Nazwa kontenera musi mieć długość 3 – 63 znaków i może zawierać tylko małe znaki alfanumeryczne i łącznik. Łącznik musi być poprzedzony i następuje znak alfanumeryczny.  |
|  InvalidParameter  |  Nazwa kontenera {0} w adresie URL {1} jest nieprawidłowy. Nazwa kontenera musi mieć długość 3 – 63 znaków i może zawierać tylko małe znaki alfanumeryczne i łącznik. Łącznik musi być poprzedzony i następuje znak alfanumeryczny.  |
|  InvalidParameter  |  Nazwa obiektu blob w adresie URL {0} zawiera ukośnik odwrotny. Nie jest to obecnie obsługiwane w przypadku dysków.  |
|  InvalidParameter  |  Identyfikator URI {0} nie wygląda na prawidłowy identyfikator URI obiektu blob.  |
|  InvalidParameter  |  Dysk o nazwie "{0}" używa już tego samego numeru LUN: {1}.  |
|  InvalidParameter  |  Dysk o nazwie "{0}" już istnieje.  |
|  InvalidParameter  |  Nie można określić elementów przesłaniających obraz użytkownika dla dysku, który zdefiniowano w określonym odwołaniu do obrazu.  |
|  InvalidParameter  |  Dysk o nazwie "{0}" używa już tego samego adresu URL wirtualnego dysku twardego {1}.  |
|  InvalidParameter  |  Liczba domen błędów określonego {0} musi należeć do zakresu {1} do {2}.  |
|  InvalidParameter  |  Typ licencji {0} jest nieprawidłowy. Prawidłowy typ licencji to: Windows_Client lub Windows_Server, z uwzględnieniem wielkości liter.  |
|  InvalidParameter  |  Nazwa hosta systemu Linux nie może przekraczać {0} znaków długości ani zawierać następujących znaków: {1}.  |
|  InvalidParameter  |  Ścieżka docelowa kluczy publicznych Ssh jest obecnie ograniczona do wartości domyślnej {0} z powodu znanego błędu dotyczącego aprowizacji agenta systemu Linux.  |
|  InvalidParameter  |  Dysku o numerze LUN {0} już istnieje.  |
|  InvalidParameter  |  Subskrypcja {0} żądania muszą być zgodne subskrypcji {1} zawarte w identyfikatorze dysku zarządzanego.  |
|  InvalidParameter  |  Niestandardowe dane w OSProfile muszą być w kodowaniu Base64, jak i o maksymalnej długości {0} znaków.  |
|  InvalidParameter  |  Nazwa obiektu blob w adresie URL {0} musi kończyć się znakiem "{1}" rozszerzenia.  |
|  InvalidParameter  |  {0}"nie jest prawidłowy prefiks nazwy przechwyconego obiektu blob wirtualnego dysku twardego. Nieprawidłowy prefiks jest zgodny z wyrażeniem regularnym "{1}".  |
|  InvalidParameter  |  Nie można dodać certyfikaty dla maszyny wirtualnej, jeśli nie ustanowiono agenta maszyny Wirtualnej.  |
|  InvalidParameter  |  Dysku o numerze LUN {0} już istnieje.  |
|  InvalidParameter  |  Nie można utworzyć maszyny Wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępny w klastrze, w którym zestaw dostępności jest aktualnie przydzielony. Dostępne rozmiary to: {1}. Więcej informacji na maszyn wirtualnych, zmiana rozmiaru w strategii https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  Żądany rozmiar maszyny Wirtualnej {0} nie jest dostępny w bieżącym regionie. Rozmiary dostępne w bieżącym regionie to: {1}. Dowiedz się więcej informacji na temat dostępnych rozmiarów maszyn wirtualnych w każdym z regionów w https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Żądany rozmiar maszyny Wirtualnej {0} nie jest dostępny w bieżącym regionie. Dowiedz się więcej informacji na temat dostępnych rozmiarów maszyn wirtualnych w każdym z regionów w https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Nazwa użytkownika administratora Windows nie może być więcej niż {0} znaków długie, kończyć się kropką (.) ani zawierać następujących znaków: {1}.  |
|  InvalidParameter  |  Nazwa komputera Windows nie może mieć więcej niż {0} znaków długie, być całkowicie numeryczna ani zawierać następujących znaków: {1}.  |
|  MissingMoveDependentResources  |  Żądanie przeniesienia zasobów zawiera zasoby zależne. Sprawdź szczegóły błędów dotyczących brakujących identyfikatorów zasobów.  |
|  MoveResourcesHaveInvalidState  |  Żądanie przeniesienia zasobów zawiera maszyny wirtualne, które są skojarzone z kontami magazynu nieprawidłowy. Sprawdź szczegóły dotyczące tych identyfikatorów zasobów i nazwy konta magazynu do którego istnieje odwołanie.  |
|  MoveResourcesHavePendingOperations  |  Żądanie przeniesienia zasobów obejmuje zasoby, dla których operacja oczekuje. Sprawdź szczegóły identyfikatorów tych zasobów. Ponów operację po ukończeniu oczekujących operacji.  |
|  MoveResourcesNotFound  |  Żądanie przeniesienia zasobów zawiera zasoby, których nie można znaleźć. Sprawdź szczegóły identyfikatorów tych zasobów.  |
|  NetworkingInternalOperationError  |  Nieznany błąd alokacji sieci.  |
|  NetworkingInternalOperationError  |  Nieznany błąd alokacji sieci  |
|  NetworkingInternalOperationError  |  Wystąpił błąd podczas przetwarzania profilu sieciowego maszyny wirtualnej.  |
|  NotFound  |  Zestaw dostępności {0} nie można odnaleźć.  |
|  NotFound  |  Źródłowej maszyny wirtualnej "{0}" określony w żądaniu nie istnieje w tej lokalizacji platformy Azure.  |
|  NotFound  |  Dzierżawy o identyfikatorze {0} nie można odnaleźć.  |
|  NotFound  |  Obraz {0} nie można odnaleźć.  |
|  NotSupported  |  Typ licencji to {0}, ale obiekt blob obrazu {1} nie pochodzi ze środowiska lokalnego.  |
|  OperationNotAllowed  |  Zestaw dostępności {0} nie można jej usunąć. Przed usunięciem zestawu dostępności upewnij się, że nie zawiera żadnych maszyn wirtualnych.  |
|  OperationNotAllowed  |  Zmienianie zestawu dostępności jednostki SKU z "Wyrównanej" do "Classic" jest niedozwolone.  |
|  OperationNotAllowed  |  Nie można modyfikować rozszerzeń w maszynie wirtualnej, kiedy maszyna wirtualna nie działa.  |
|  OperationNotAllowed  |  Akcja przechwytywania jest obsługiwana tylko na maszynie wirtualnej z dyskami opartymi na obiektach blob. Użyj interfejsów API zasobu "Image", aby utworzyć obraz z zarządzanej maszyny wirtualnej.  |
|  OperationNotAllowed  |  Zasób {0} nie można utworzyć za pomocą obrazu {1} aż obraz został pomyślnie utworzony.  |
|  OperationNotAllowed  |  Aktualizacje do elementu encryptionSettings nie jest dozwolone, gdy maszyna wirtualna jest przydzielany, spróbuj ponownie po cofnięciu przydziału maszyny Wirtualnej  |
|  OperationNotAllowed  |  Dodanie zarządzanego dysku do maszyny wirtualnej z dyskami opartymi na obiektach blob nie jest obsługiwane.  |
|  OperationNotAllowed  |  Maksymalna liczba dysków danych dozwolonych do podłączenia do maszyny Wirtualnej tego rozmiaru to {0}.  |
|  OperationNotAllowed  |  Dodanie dysku opartego na obiektach blob do maszyny wirtualnej z dyskami zarządzanymi nie jest obsługiwane.  |
|  OperationNotAllowed  |  Operacja "{0}"jest niedozwolone w obrazie"{1}", ponieważ obraz jest oznaczony do usunięcia. Możesz tylko ponowić próbę wykonania operacji usunięcia (lub odczekać trwającą do ukończenia).  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ maszyna wirtualna jest uogólniona.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona jako kolekcję punktów przywracania"{1}" jest oznaczona do usunięcia.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona względem rozszerzenia maszyny Wirtualnej"{1}" ponieważ jest ona oznaczona do usunięcia. Możesz tylko ponowić próbę wykonania operacji usunięcia (lub odczekać trwającą do ukończenia).  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona ponieważ maszyny wirtualne"{1}"są aprowizowane za pomocą obrazu"{2}".  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona od zestawu skalowania maszyn wirtualnych"{1}"używa obecnie obrazu"{2}".  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ maszyna wirtualna jest oznaczona do usunięcia. Możesz tylko ponowić próbę wykonania operacji usunięcia (lub odczekać trwającą do ukończenia).  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ cofnięto alokację lub oznaczone, aby cofnąć przydział maszyny Wirtualnej.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ maszyna wirtualna jest uruchomiona. Skontaktuj się z zasilania poza jawnie w przypadku, gdy Zamknij maszynę Wirtualną w systemie operacyjnym gościa.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ nie cofnięto przydziału maszyny Wirtualnej.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ maszyna wirtualna ma rozszerzenie"{2}" ze stanem wskazującym niepowodzenie.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ inna operacja jest w toku.  |
|  OperationNotAllowed  |  Operacja "{0}"wymaga aby maszyna wirtualna"{1}" była uogólniona.  |
|  OperationNotAllowed  |  Operacja wymaga uruchomienia maszyny wirtualnej (lub ustawienia jej w celu uruchomienia).  |
|  OperationNotAllowed  |  Dysk o rozmiarze {0}GB, który jest mniejszy niż rozmiar {1}GB odpowiadającego mu dysku w obrazie, jest niedozwolone.  |
|  OperationNotAllowed  |  Rozszerzenia zestawu skali maszyny Wirtualnej programu obsługi "{0}" można dodać tylko w momencie tworzenia zestawu skalowania maszyn wirtualnych.  |
|  OperationNotAllowed  |  Rozszerzenia zestawu skali maszyny Wirtualnej programu obsługi "{0}" można usunąć tylko w momencie usuwania zestawu skali maszyny Wirtualnej.  |
|  OperationNotAllowed  |  Maszyna wirtualna "{0}" jest już używane dyski zarządzane.  |
|  OperationNotAllowed  |  Maszyna wirtualna "{0}"należy do zestawu dostępności "Classic" "{1}". Zaktualizuj zestaw dostępności, aby używać "Wyrównanej" jednostki SKU, a następnie ponów próbę konwersji.  |
|  OperationNotAllowed  |  Maszyna wirtualna utworzona na podstawie obrazu nie może mieć dyski opartymi na obiektach blob. Wszystkie dyski muszą być dyskami zarządzanymi.  |
|  OperationNotAllowed  |  Nie można ukończyć operacji przechwytywania, ponieważ to nie jest uogólniona maszyna wirtualna.  |
|  OperationNotAllowed  |  Operacje zarządzania na maszynie Wirtualnej "{0}" są niedozwolone, ponieważ dyski maszyny Wirtualnej są konwertowane na dyski zarządzane.  |
|  OperationNotAllowed  |  Trwająca operacja powoduje zmianę stanu zasilania maszyny wirtualnej {0} do {1}. Wykonaj operację {2} po pewnym czasie.  |
|  OperationNotAllowed  |  Nie można dodać ani zaktualizować maszyny Wirtualnej. Żądany rozmiar maszyny Wirtualnej {0} mogą nie być dostępne w istniejącej jednostce alokacji. Więcej informacji na maszyn wirtualnych, zmiana rozmiaru w strategii https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nie można zmienić rozmiaru maszyny Wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępny w klastrze, w którym zestaw dostępności jest aktualnie przydzielony. Dostępne rozmiary to: {1}. Więcej informacji na maszyn wirtualnych, zmiana rozmiaru w strategii https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nie można zmienić rozmiaru maszyny Wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępny w klastrze, w którym maszyna wirtualna jest aktualnie przydzielonych. Aby zmienić rozmiar maszyny Wirtualnej, aby {1} Cofnij Przydział (jest to operacja Zatrzymaj w witrynie Azure portal), a następnie spróbuj ponownie wykonać operację zmiany rozmiaru. Więcej informacji na maszyn wirtualnych, zmiana rozmiaru w strategii https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Aprowizacja systemu operacyjnego nie powiodła się dla maszyny Wirtualnej "{0}", ponieważ system operacyjny gościa jest aktualnie aprowizowany.  |
|  OSProvisioningClientError  |  Aprowizacja systemu operacyjnego dla maszyny Wirtualnej "{0}" nie powiodło się. Szczegóły błędu: {1} Upewnij się, że obraz został poprawnie przygotowany (uogólniony). <ul><li>Instrukcje Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Generowania klucza hosta SSH nie powiodło się. Szczegóły błędu: {0}. Aby rozwiązać ten problem, sprawdź się, jeśli agent systemu Linux jest prawidłowo skonfigurowane. <ul><li>Możesz sprawdzić zgodnie z instrukcjami na: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Nazwa użytkownika określona dla maszyny Wirtualnej jest nieprawidłowa dla tej dystrybucji systemu Linux. Szczegóły błędu: {0}.  |
|  OSProvisioningInternalError  |  Aprowizacja systemu operacyjnego nie powiodła się dla maszyny Wirtualnej "{0}" z powodu błędu wewnętrznego.  |
|  OSProvisioningTimedOut  |  Aprowizacja systemu operacyjnego dla maszyny Wirtualnej "{0}" nie została zakończona w wyznaczonym czasie. Maszyna wirtualna może nadal pomyślnie zakończyć aprowizację. Sprawdź później stan inicjowania obsługi administracyjnej.  |
|  OSProvisioningTimedOut  |  Aprowizacja systemu operacyjnego dla maszyny Wirtualnej "{0}" nie została zakończona w wyznaczonym czasie. Maszyna wirtualna może nadal pomyślnie zakończyć aprowizację. Sprawdź później stan inicjowania obsługi administracyjnej. Ponadto upewnij się, że obraz został poprawnie przygotowany (uogólniony).   <ul><li>Instrukcje Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instrukcje dla systemu Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Aprowizacja systemu operacyjnego dla maszyny Wirtualnej "{0}" nie została zakończona w wyznaczonym czasie. Jednak agent gościa maszyny Wirtualnej zostało wykryte, uruchomione. Sugeruje to, system operacyjny gościa nie został poprawnie przygotowany do użycia jako obraz maszyny Wirtualnej (z właściwością CreateOption = FromImage). Aby rozwiązać ten problem, użyj wirtualnego dysku twardego, ponieważ jest z właściwością CreateOption = Attach lub odpowiednio przygotować go do użycia jako obraz:   <ul><li>Instrukcje Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instrukcje dla systemu Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Wymagany rozmiar maszyny Wirtualnej nie jest obecnie dostępna w wybranej lokalizacji.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Nie można zaktualizować zasobu w tej chwili z powodu trwającej platformy aktualizacji. Spróbuj ponownie później.  |
|  StorageAccountLimitation  |  Konto magazynu "{0}" nie obsługuje stronicowych obiektów blob, które są wymagane do tworzenia dysków.  |
|  StorageAccountLimitation  |  Konto magazynu "{0}" przekroczyła limit przydziału.  |
|  StorageAccountLocationMismatch  |  Nie można rozpoznać konta magazynu {0}. Upewnij się, że został on utworzony za pośrednictwem dostawcy zasobów magazynu w tej samej lokalizacji co zasób obliczeniowy.  |
|  StorageAccountNotFound  |  Konto magazynu {0} nie można odnaleźć. Upewnij się, konto magazynu nie jest usuwana i należy do tej samej lokalizacji platformy Azure co maszyna wirtualna.  |
|  StorageAccountNotRecognized  |  Użyj konta magazynu, zarządzana przez dostawcę zasobów magazynu. Korzystanie z {0} nie jest obsługiwane.  |
|  StorageAccountOperationInternalError  |  Wystąpił błąd wewnętrzny podczas uzyskiwania dostępu do konta magazynu {0}.  |
|  StorageAccountSubscriptionMismatch  |  Konto magazynu {0} nie należy do subskrypcji {1}.  |
|  StorageAccountTooBusy  |  Konto magazynu "{0}" jest obecnie zbyt zajęte. Należy wziąć pod uwagę przy użyciu innego konta.  |
|  StorageAccountTypeNotSupported  |  Dysk {0} używa {1} czyli konta usługi Blob storage. Spróbuj ponownie przy użyciu konta magazynu ogólnego przeznaczenia.  |
|  StorageAccountTypeNotSupported  |  Konto magazynu {0} jest {1} typu. Obsługuje diagnostyki rozruchu {2} typy kont magazynu.  <ul><li>Ten błąd występuje, jeśli używasz konta usługi premium storage dla diagnostyki rozruchu. Aby uzyskać więcej informacji, zobacz [sposób używania diagnostyki rozruchu](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Subskrypcja nie została autoryzowana.  |
|  TargetDiskBlobAlreadyExists  |  Obiekt blob {0} już istnieje. Podaj inny identyfikator URI, aby utworzyć nowy pusty dysk danych obiektu blob "{1}".  |
|  TargetDiskBlobAlreadyExists  |  Przechwytywanie nie można kontynuować operacji, ponieważ obiekt blob obrazu docelowego {0} już istnieje i nie jest ustawiona flaga, aby zastępować obiekty BLOB dysków VHD. Usuń obiekt blob lub ustawić flagę, aby zastąpić obiekty BLOB dysków VHD, a następnie spróbuj ponownie.  |
|  TargetDiskBlobAlreadyExists  |  Przechwytywanie nie można kontynuować operacji, ponieważ obiekt blob obrazu docelowego {0} ma aktywną dzierżawę.   |
|  TargetDiskBlobAlreadyExists  |  Obiekt blob {0} już istnieje. Podaj inny identyfikator URI obiektu blob jako cel dysku "{1}".  |
|  TooManyVMRedeploymentRequests  |  Odebrano zbyt wiele żądań ponownego wdrożenia, dla maszyny Wirtualnej "{0}" lub maszyn wirtualnych w tej samej availabilityset, z tą maszyną Wirtualną. Spróbuj ponownie później.  |
|  VHDSizeInvalid  |  Określona wartość rozmiaru dysku z {0} dla dysku "{1}" z obiektem blob {2} jest nieprawidłowy. Rozmiar dysku musi należeć do zakresu od {3} i {4}.  |
|  VMAgentStatusCommunicationError  |  Maszyna wirtualna "{0}" nie zgłosił stan agenta maszyny Wirtualnej lub rozszerzenia. Sprawdź, czy maszyna wirtualna ma działającego agenta maszyny Wirtualnej i może nawiązywać wychodzące połączenia do usługi Azure storage.  |
|  VMArtifactRepositoryInternalError  |  Podczas komunikacji z repozytorium artefaktów w celu pobrania szczegółów artefaktu maszyny wirtualnej wystąpił błąd.  |
|  VMArtifactRepositoryInternalError  |  Wystąpił błąd wewnętrzny podczas pobierania danych artefaktu maszyny wirtualnej z repozytorium artefaktów.  |
|  VMExtensionHandlerNonTransientError  |  Procedura obsługi "{0}"zgłosiła błąd dla rozszerzenia maszyny Wirtualnej"{1}"z kodem błędu terminala"{2}" i komunikat o błędzie: "{3}"  |
|  VMExtensionManagementInternalError  |  Wystąpił błąd wewnętrzny podczas przetwarzania rozszerzenia maszyny Wirtualnej "{0}".  |
|  VMExtensionManagementInternalError  |  Wystąpiło wiele błędów podczas przygotowywania rozszerzeń maszyny Wirtualnej. Zobacz widok wystąpienia rozszerzenia maszyny Wirtualnej, aby uzyskać szczegółowe informacje.  |
|  VMExtensionProvisioningError  |  Maszyna wirtualna zgłosiła błąd podczas przetwarzania rozszerzenia "{0}". Komunikat o błędzie: "{1}".  |
|  VMExtensionProvisioningError  |  Wielu rozszerzeń maszyny Wirtualnej nie można aprowizować na maszynie Wirtualnej. Zobacz widok wystąpienia rozszerzenia maszyny Wirtualnej, aby uzyskać szczegółowe informacje.  |
|  VMExtensionProvisioningTimeout  |  Inicjowanie obsługi rozszerzenia maszyny Wirtualnej "{0}" Upłynął limit czasu. Instalacja rozszerzenia może trwać zbyt długo lub nie można uzyskać stanu rozszerzenia.  |
|  VMMarketplaceInvalidInput  |  Tworzenie maszyny wirtualnej na podstawie obrazu niepochodzącego z witryny Marketplace nie wymaga informacji o planie; Usuń tę informację w żądaniu. Nazwa dysku systemu operacyjnego to {0}.  |
|  VMMarketplaceInvalidInput  |  Informacje o zakupie nie jest zgodny. Nie można wdrożyć za pomocą obrazu portalu Marketplace. Nazwa dysku systemu operacyjnego to {0}.  |
|  VMMarketplaceInvalidInput  |  Tworzenie maszyny wirtualnej na podstawie obrazu z witryny Marketplace wymaga informacji o planie w żądaniu. Nazwa dysku systemu operacyjnego to {0}.  |
|  VMNotFound  |  Maszyna wirtualna "{0}" nie można odnaleźć.  |
|  VMRedeploymentFailed  |  Maszyna wirtualna "{0}" ponowne wdrożenie nie powiodło się z powodu błędu wewnętrznego. Spróbuj ponownie później.  |
|  VMRedeploymentTimedOut  |  Ponowne wdrożenie maszyny Wirtualnej "{0}" nie zostało zakończone w przydzielonym czasie. Zakończenie pomyślnie w pewnym czasie. W przeciwnym wypadku można ponowić próbę żądania.  |
|  VMStartTimedOut  |  Maszyna wirtualna "{0}' nie rozpoczął się w wyznaczonym czasie. Maszyna wirtualna nadal może uruchomić się pomyślnie. Sprawdź później stan zasilania.  |


## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz więcej pomocy, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.