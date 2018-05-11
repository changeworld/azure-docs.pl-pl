---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/25/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: db241c1a3c8bfd15e13ae0bd9f1cdf4c92c7081d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
>[!NOTE]
> Możesz pozostawić komentarze na tej stronie opinii lub za pomocą [Azure opinii](https://feedback.azure.com/forums/216843-virtual-machines) #azerrormessage znacznika.

## <a name="error-response-format"></a>Format odpowiedzi błędu 
Maszyny wirtualne platformy Azure, użyj następującego formatu JSON dla odpowiedzi komunikat o błędzie:

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

Odpowiedzi na błąd zawsze zawiera kod stanu i obiektu błędu. Każdy obiekt błąd zawsze zawiera kod błędu i komunikatu. Jeśli maszyna wirtualna została utworzona przy użyciu szablonu, obiekt błędu zawiera także sekcji Szczegóły, która zawiera wewnętrzny poziom nawiasów kody błędów i komunikatów. Najbardziej wewnętrzny poziom komunikat o błędzie jest zazwyczaj awarii głównego.


## <a name="common-virtual-machine-management-errors"></a>Typowe błędy zarządzania maszyny wirtualnej

Ta sekcja zawiera typowe komunikaty o błędach, które można napotkać podczas zarządzania maszyn wirtualnych:

|  Kod błędu  |  Komunikat o błędzie  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nie można ustanowić dzierżawy podczas tworzenia dysku "{0}" za pomocą obiektu blob o identyfikatorze URI {1}. Obiekt blob jest już używana.  |  
|  AllocationFailed  |  Alokacja nie powiodła się. Zmniejsz rozmiar maszyny Wirtualnej lub liczbę maszyn wirtualnych, spróbuj ponownie później lub spróbuj przeprowadzić wdrożenie w innym zestawie dostępności lub innej lokalizacji platformy Azure.  |  
|  AllocationFailed  |  Alokacja maszyny Wirtualnej nie powiodła się z powodu błędu wewnętrznego. Spróbuj ponownie później lub spróbuj wdrożenia w innej lokalizacji.  |
|  ArtifactNotFound  |  Rozszerzenia maszyny Wirtualnej o wydawcy "{0}"i typie"{1}"nie można odnaleźć w lokalizacji"{2}".  |
|  ArtifactNotFound  |  Rozszerzenie o wydawcy "{0}", typ "{1}" i wersji programu obsługi typu "{2}" nie można odnaleźć w repozytorium rozszerzeń.  |
|  ArtifactVersionNotFound  |  Nie znaleziono wersji w repozytorium artefaktów odpowiadającej żądanej wersji "{0}".  |
|  ArtifactVersionNotFound  |  Nie znaleziono wersji w repozytorium artefaktów odpowiadającej żądanej wersji "{0}"dla rozszerzenia maszyny Wirtualnej o wydawcy"{1}"i typie"{2}".  |
|  AttachDiskWhileBeingDetached  |  Nie można dołączyć dysku danych "{0}"do maszyny Wirtualnej"{1}", ponieważ dysk jest aktualnie odłączona. Poczekaj, aż dysk zostanie całkowicie odłączony, a następnie spróbuj ponownie.  |
|  BadRequest  |  Wyrównane "zestawów dostępności nie są jeszcze obsługiwane w tym regionie.  |
|  BadRequest  |  Dodawanie maszyny wirtualnej z dyskami zarządzanych do niezarządzanych zestawu dostępności lub dodanie z dyskami na podstawie obiektu blob do zarządzanych zestawu dostępności maszyny wirtualnej nie jest obsługiwane. Utwórz zbiór dostępności z właściwością "zarządzany" ustawiona, aby dodać maszyny Wirtualnej z dyskami zarządzanych do niego.  |
|  BadRequest  |  Dyski zarządzane nie są obsługiwane w tym regionie.  |
|  BadRequest  |  Wiele Vmextension na program obsługi nie jest obsługiwana dla typu systemu operacyjnego "{0}". VMExtension "{1}"z programem obsługi"{2}" już dodany lub określony w danych wejściowych.  |
|  BadRequest  |  Operacja "{0}"nie jest obsługiwana dla zasobu"{1}" z dyskami zarządzanych.  |
|  CertificateImproperlyFormatted  |  Reprezentacja JSON klucza tajnego uzyskana z {0} ma pole danych, która nie jest poprawnie sformatowanym plikiem PFX, albo podane hasło nie dekoduje prawidłowo pliku PFX.  |
|  CertificateImproperlyFormatted  |  Dane pobrane z {0} nie da się rozszeregować na formacie JSON.  |
|  Konflikt  |  Zmiana rozmiaru dysku jest dozwolona tylko podczas tworzenia maszyny wirtualnej lub po cofnięciu jej przydziału.  |
|  ConflictingUserInput  |  Dysk "{0}"nie można dołączyć, ponieważ dysk jest już przypisany do maszyny Wirtualnej"{1}".  |
|  ConflictingUserInput  |  Źródłowa grupa zasobów jest taka sama jak docelowa.  |
|  ConflictingUserInput  |  Konta magazynu źródłowego i docelowego dla dysku {0} są różne.  |
|  ContainerAlreadyOnLease  |  Istnieje już dzierżawa na kontenerze magazynu przechowującym obiekt blob z identyfikatorem URI {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Żądanie przeniesienia zasobów zawiera zasoby KeyVault odwołuje się co najmniej jeden {0}s w żądaniu. Nie jest to obsługiwane obecnie między subskrypcjami przenoszenia. Sprawdź, czy szczegóły błędu dla identyfikatorów zasobów KeyVault.  |
|  DiagnosticsOperationInternalError  |  Wystąpił błąd wewnętrzny podczas przetwarzania profilu diagnostyki maszyny Wirtualnej {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Obiekt blob {0} jest już używana przez inny dysk należący do maszyny Wirtualnej "{1}". Możesz zbadać metadane obiektu blob, aby uzyskać informacje na dysku.  |
|  DiskBlobNotFound  |  Nie można odnaleźć obiektu blob dysku VHD z identyfikatorem URI {0} dla dysku "{1}".  |
|  DiskBlobNotFound  |  Nie można odnaleźć obiektu blob dysku VHD z identyfikatorem URI {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} nie ma klucza tajnego {1} tagów. Zaktualizuj wersję klucza tajnego, Dodaj wymagane tagi i spróbuj ponownie.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Dekodowanie SECRET {0} wartość z użyciem klucza {1} nie powiodło się.  |
|  DiskImageNotReady  |  Obraz dysku {0} w {1} stanu. Spróbuj ponownie, gdy obraz będzie gotowy.  |
|  DiskPreparationError  |  Wystąpił co najmniej jeden błąd podczas przygotowywania dysków maszyny Wirtualnej. Zobacz widok wystąpienia dysku, aby uzyskać szczegółowe informacje.  |
|  DiskProcessingError  |  Przetwarzanie danych na dysku zostało zatrzymane, ponieważ maszyna wirtualna ma inne uszkodzone dyski.  |
|  ImageBlobNotFound  |  Nie można odnaleźć obiektu blob dysku VHD z identyfikatorem URI {0} dla dysku "{1}".  |
|  ImageBlobNotFound  |  Nie można odnaleźć obiektu blob dysku VHD z identyfikatorem URI {0}.  |
|  IncorrectDiskBlobType  |  Obiekty BLOB dysków może być tylko typu stronicowych obiektów blob. Obiekt blob {0} dla dysku "{1}" ma typ blokowy obiekt blob.  |
|  IncorrectDiskBlobType  |  Obiekty BLOB dysków może być tylko typu stronicowych obiektów blob. Obiekt blob {0} jest typu "{1}".  |
|  IncorrectImageBlobType  |  Obiekty BLOB dysków może być tylko typu stronicowych obiektów blob. Obiekt blob {0} dla dysku "{1}" ma typ blokowy obiekt blob.  |
|  IncorrectImageBlobType  |  Obiekty BLOB dysków może być tylko typu stronicowych obiektów blob. Obiekt blob {0} jest typu "{1}".  |
|  InternalOperationError  |  Nie można rozpoznać konta magazynu {0}. Upewnij się, że został on utworzony za pośrednictwem dostawcy zasobów magazynu w tej samej lokalizacji co zasób obliczeniowy.  |
|  InternalOperationError  |  {0} zadania poszukiwania celu nie powiodło się.  |
|  InternalOperationError  |  Wystąpił błąd podczas sprawdzania poprawności profilu sieciowego maszyny Wirtualnej "{0}".  |
|  InvalidAccountType  |  AccountType {0} jest nieprawidłowy.  |
|  InvalidParameter  |  Wartość parametru {0} jest nieprawidłowa.  |
|  InvalidParameter  |  Określone hasło administratora jest niedozwolone.  |
|  InvalidParameter  |  "Podane hasło musi należeć do zakresu od {0}-{1} znaków i musi spełniać co najmniej {2} wymagań złożoności hasła z następujących czynności: <ol><li> Zawiera wielką literę</li><li>Zawiera małą literę</li><li>Zawiera cyfrę</li><li>Zawiera znaki specjalne.</li></ol>  |
|  InvalidParameter  |  Określona nazwa użytkownika administratora jest niedozwolona.  |
|  InvalidParameter  |  Nie można podłączyć istniejącego dysku z systemem operacyjnym, jeśli maszyna wirtualna jest utworzona z obrazu użytkownika lub platformy.  |
|  InvalidParameter  |  Nazwa kontenera {0} jest nieprawidłowy. Nazwy kontenerów muszą być 3 do 63 znaków długości i mogą zawierać tylko małe znaki alfanumeryczne i łączniki. Łącznik musi być poprzedzone i następuje znak alfanumeryczny.  |
|  InvalidParameter  |  Nazwa kontenera {0} w adresie URL {1} jest nieprawidłowy. Nazwy kontenerów muszą być 3 do 63 znaków długości i mogą zawierać tylko małe znaki alfanumeryczne i łączniki. Łącznik musi być poprzedzone i następuje znak alfanumeryczny.  |
|  InvalidParameter  |  Nazwa obiektu blob w adresie URL {0} zawiera ukośnika. Jest to obecnie nieobsługiwane w przypadku dysków.  |
|  InvalidParameter  |  Identyfikator URI {0} wygląda być poprawny identyfikator URI obiektu blob.  |
|  InvalidParameter  |  Dysk o nazwie "{0}" używa już tego samego numeru LUN: {1}.  |
|  InvalidParameter  |  Dysk o nazwie "{0}" już istnieje.  |
|  InvalidParameter  |  Nie można określić elementów przesłaniających obraz użytkownika dla dysku, który zdefiniowano w określonym odwołaniu do obrazu.  |
|  InvalidParameter  |  Dysk o nazwie "{0}" używa już tego samego adresu URL wirtualnego dysku twardego {1}.  |
|  InvalidParameter  |  Określona liczba domen błędów {0} musi należeć do zakresu {1} do {2}.  |
|  InvalidParameter  |  Typ licencji {0} jest nieprawidłowy. Prawidłowy typ licencji są: Windows_Client lub Windows_Server, z uwzględnieniem wielkości liter.  |
|  InvalidParameter  |  Nazwa hosta systemu Linux nie może przekraczać {0} znaków długości ani zawierać następujących znaków: {1}.  |
|  InvalidParameter  |  Ścieżka docelowa kluczy publicznych Ssh jest obecnie ograniczony do wartości domyślnej {0} ze względu na znany problem w agenta inicjowania obsługi administracyjnej systemu Linux.  |
|  InvalidParameter  |  Dysk pod numerem LUN {0} już istnieje.  |
|  InvalidParameter  |  Subskrypcja {0} żądania musi odpowiadać subskrypcji {1} zawarte w identyfikatorze dysku zarządzanego.  |
|  InvalidParameter  |  Niestandardowe dane w OSProfile muszą być w kodowaniu Base64, a maksymalna długość {0} znaków.  |
|  InvalidParameter  |  Nazwa obiektu blob w adresie URL {0} musi kończyć się "{1}" rozszerzenia.  |
|  InvalidParameter  |  {0}"nie jest prawidłowy prefiks nazwy przechwyconego obiektu blob dysku VHD. Prawidłowy prefiks zgodny z wyrażeniem regularnym "{1}".  |
|  InvalidParameter  |  Nie można dodać certyfikatów do maszyny Wirtualnej, jeśli nie zainicjowano agenta maszyny Wirtualnej.  |
|  InvalidParameter  |  Dysk pod numerem LUN {0} już istnieje.  |
|  InvalidParameter  |  Nie można utworzyć maszyny Wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępne w klastrze, gdzie jest obecnie przydzielony zestawu dostępności. Są dostępne rozmiary: {1}. Przeczytaj więcej VM na zmianę rozmiaru strategii w https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  Żądany rozmiar maszyny Wirtualnej {0} nie jest dostępny w bieżącym obszarze. Dostępne rozmiary bieżącego obszaru są: {1}. Dowiedz się więcej na dostępne rozmiary maszyn wirtualnych w każdym regionie na https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Żądany rozmiar maszyny Wirtualnej {0} nie jest dostępny w bieżącym obszarze. Dowiedz się więcej na dostępne rozmiary maszyn wirtualnych w każdym regionie na https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Nazwa użytkownika administratora systemu Windows nie może mieć więcej niż {0} znaków długie, kończyć się kropką (.) ani zawierać następujących znaków: {1}.  |
|  InvalidParameter  |  Nazwa komputera systemu Windows nie może mieć więcej niż {0} znaków długie, być całkowicie numeryczna ani zawierać następujących znaków: {1}.  |
|  MissingMoveDependentResources  |  Żądanie przeniesienia zasobów zawiera zasoby zależne. Sprawdź szczegóły błędów dotyczących brakujących identyfikatorów zasobów.  |
|  MoveResourcesHaveInvalidState  |  Żądanie przeniesienia zasobów zawiera maszyny wirtualne, które są skojarzone z kontami magazynu nieprawidłowy. Sprawdź, czy szczegółowe informacje dotyczące tych identyfikatorów zasobów i nazwy konta magazynu do którego istnieje odwołanie.  |
|  MoveResourcesHavePendingOperations  |  Żądanie przeniesienia zasobów zawiera zasoby, dla których operacja oczekuje. Sprawdź szczegóły identyfikatorów tych zasobów. Ponów operację po zakończeniu operacji oczekujących.  |
|  MoveResourcesNotFound  |  Żądanie przeniesienia zasobów zawiera zasoby, których nie można znaleźć. Sprawdź szczegóły identyfikatorów tych zasobów.  |
|  NetworkingInternalOperationError  |  Nieznany błąd alokacji sieci.  |
|  NetworkingInternalOperationError  |  Nieznany błąd alokacji sieci  |
|  NetworkingInternalOperationError  |  Wystąpił błąd podczas przetwarzania profilu sieciowego maszyny wirtualnej.  |
|  Nie odnaleziono  |  Do zestawu dostępności {0} nie można odnaleźć.  |
|  Nie odnaleziono  |  Źródłowej maszyny wirtualnej "{0}" określony w żądaniu nie istnieje w tej lokalizacji platformy Azure.  |
|  Nie odnaleziono  |  Dzierżawcy o identyfikatorze {0} nie można odnaleźć.  |
|  Nie odnaleziono  |  Obraz {0} nie można odnaleźć.  |
|  NotSupported  |  Typ licencji to {0}, ale obiekt blob obrazu {1} nie pochodzi z lokalnymi.  |
|  OperationNotAllowed  |  Zestaw dostępności {0} nie można usunąć. Przed usunięciem zestawu danych o dostępności upewnij się, że nie zawiera żadnej maszyny Wirtualnej.  |
|  OperationNotAllowed  |  Zmiana zestawu dostępności jednostki SKU z 'Wyrównany' do 'Klasycznym' jest niedozwolona.  |
|  OperationNotAllowed  |  Nie można modyfikować rozszerzeń w maszynie wirtualnej, kiedy maszyna wirtualna nie działa.  |
|  OperationNotAllowed  |  Akcji przechwytywania jest obsługiwana tylko na maszynie wirtualnej z dyskami na podstawie obiektu blob. Użyj zasobu "Image" interfejsy API do utworzenia obrazu z zarządzanego maszyny wirtualnej.  |
|  OperationNotAllowed  |  Zasób {0} nie może zostać utworzony na podstawie obrazu {1} dopóki obraz został utworzony pomyślnie.  |
|  OperationNotAllowed  |  Można aktualizować elementu encryptionSettings nie jest dozwolona, gdy maszyna wirtualna jest przydzielany, spróbuj ponownie po cofnięciu przydziału maszyny Wirtualnej  |
|  OperationNotAllowed  |  Dodanie zarządzanego dysku do maszyny wirtualnej z dyskami opartymi na obiektach blob nie jest obsługiwane.  |
|  OperationNotAllowed  |  Maksymalna liczba dysków danych może zostać dołączony do tego rozmiaru maszyny Wirtualnej jest {0}.  |
|  OperationNotAllowed  |  Dodanie dysku opartego na obiektach blob do maszyny wirtualnej z zarządzanymi dyskami nie jest obsługiwane.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona w obrazie"{1}" ponieważ obraz jest oznaczony do usunięcia. Można tylko spróbuj ponownie wykonać operację usuwania (lub poczekaj, aż trwającej co do ukończenia).  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}" ponieważ uogólniona maszyna wirtualna.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona jako kolekcja punktu przywracania"{1}" jest oznaczona do usunięcia.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona dla rozszerzenia maszyny Wirtualnej"{1}", ponieważ jest ona oznaczona do usunięcia. Można tylko spróbuj ponownie wykonać operację usuwania (lub poczekaj, aż trwającej co do ukończenia).  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolone od maszyny wirtualnej"{1}"jest inicjowana przy użyciu obrazu"{2}".  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolone od ScaleSet maszyny wirtualnej"{1}"jest obecnie korzystanie z obrazu"{2}".  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ maszyna wirtualna została oznaczona do usunięcia. Można tylko spróbuj ponownie wykonać operację usuwania (lub poczekaj, aż trwającej co do ukończenia).  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}" od maszyny Wirtualnej jest alokację lub oznaczone do cofnięcia alokacji.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ maszyna wirtualna jest uruchomiona. Określ zasilania poza jawnie w przypadku zamykania maszyny Wirtualnej z wewnątrz systemu operacyjnego gościa.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ nie cofnięciu przydziału maszyny Wirtualnej.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ maszyna wirtualna ma rozszerzenie"{2}" nie powiodło się stan.  |
|  OperationNotAllowed  |  Operacja "{0}"nie jest dozwolona na maszynie Wirtualnej"{1}", ponieważ inna operacja jest w toku.  |
|  OperationNotAllowed  |  Operacja "{0}"wymaga maszyny wirtualnej"{1}" Aby być uogólniony.  |
|  OperationNotAllowed  |  Operacja wymaga uruchomienia maszyny wirtualnej (lub ustawienia jej w celu uruchomienia).  |
|  OperationNotAllowed  |  Dysk o rozmiarze {0}GB, który jest mniejszy niż rozmiar {1}GB odpowiedniego dysku w obrazie, jest niedozwolone.  |
|  OperationNotAllowed  |  Rozszerzenia zestawu skali maszyny Wirtualnej programu obsługi "{0}" można dodać tylko w momencie tworzenia zestawu skali maszyny Wirtualnej.  |
|  OperationNotAllowed  |  Rozszerzenia zestawu skali maszyny Wirtualnej programu obsługi "{0}" można usunąć tylko w momencie usuwania zestawu skali maszyny Wirtualnej.  |
|  OperationNotAllowed  |  Maszyna wirtualna "{0}" jest już przy użyciu dysków zarządzanych.  |
|  OperationNotAllowed  |  Maszyna wirtualna "{0}"należy do zestawu dostępności 'Klasycznym' "{1}". Zaktualizuj wartość "wyrównany' SKU, a następnie ponów próbę wykonania konwersji dostępności.  |
|  OperationNotAllowed  |  Utworzony na podstawie obrazu maszyny Wirtualnej nie może mieć obiektu blob na podstawie dysków. Wszystkie dyski muszą być dysków zarządzanych.  |
|  OperationNotAllowed  |  Nie można ukończyć operacji przechwytywania, ponieważ to nie jest uogólniona maszyna wirtualna.  |
|  OperationNotAllowed  |  Operacje zarządzania na maszynie Wirtualnej "{0}" są niedozwolone, ponieważ konwersji dysków maszyny Wirtualnej do zarządzanych dysków.  |
|  OperationNotAllowed  |  Trwającą operacją zmiany stanu zasilania maszyny wirtualnej {0} do {1}. Wykonaj operację {2} po pewnym czasie.  |
|  OperationNotAllowed  |  Nie można dodać lub zaktualizować maszyny Wirtualnej. Żądany rozmiar maszyny Wirtualnej {0} mogą nie być dostępne w istniejącej jednostki alokacji. Przeczytaj więcej VM na zmianę rozmiaru strategii w https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Można zmienić rozmiaru maszyny Wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępne w klastrze, gdzie jest obecnie przydzielony zestawu dostępności. Są dostępne rozmiary: {1}. Przeczytaj więcej VM na zmianę rozmiaru strategii w https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Można zmienić rozmiaru maszyny Wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępne w klastrze, w którym aktualnego przydziału maszyny Wirtualnej. Aby zmienić rozmiar maszyny Wirtualnej, aby {1} można cofnąć alokacji (jest to operacja zatrzymania w portalu Azure), a następnie spróbuj ponownie wykonać operację zmiany rozmiaru. Przeczytaj więcej VM na zmianę rozmiaru strategii w https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Inicjowanie obsługi administracyjnej systemu operacyjnego nie powiodła się dla maszyny Wirtualnej "{0}", ponieważ system operacyjny gościa jest aktualnie aprowizowany.  |
|  OSProvisioningClientError  |  Inicjowania obsługi systemu operacyjnego dla maszyny Wirtualnej "{0}" nie powiodło się. Szczegóły błędu: {1} upewnij się, że obraz został poprawnie przygotowany (uogólniony). <ul><li>Instrukcje dla systemu Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Generowanie klucza SSH hosta nie powiodło się. Szczegóły błędu: {0}. Aby rozwiązać ten problem Sprawdź, czy agenta systemu Linux jest poprawnie skonfigurowana. <ul><li>Możesz sprawdzić zgodnie z instrukcjami w temacie: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/ </li></ul> |
|  OSProvisioningClientError  |  Nazwa użytkownika dla maszyny Wirtualnej jest nieprawidłowy dla tej dystrybucji systemu Linux. Szczegóły błędu: {0}.  |
|  OSProvisioningInternalError  |  Inicjowanie obsługi administracyjnej systemu operacyjnego nie powiodła się dla maszyny Wirtualnej "{0}" z powodu błędu wewnętrznego.  |
|  OSProvisioningTimedOut  |  Inicjowania obsługi systemu operacyjnego dla maszyny Wirtualnej "{0}" nie została ukończona w wyznaczonym czasie. Maszyna wirtualna może nadal pomyślnie zakończyć inicjowanie obsługi. Sprawdź później stan inicjowania obsługi administracyjnej.  |
|  OSProvisioningTimedOut  |  Inicjowania obsługi systemu operacyjnego dla maszyny Wirtualnej "{0}" nie została ukończona w wyznaczonym czasie. Maszyna wirtualna może nadal pomyślnie zakończyć inicjowanie obsługi. Sprawdź później stan inicjowania obsługi administracyjnej. Sprawdź także, czy obraz został poprawnie przygotowany (uogólniony).   <ul><li>Instrukcje dla systemu Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instrukcje dla systemu Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Inicjowania obsługi systemu operacyjnego dla maszyny Wirtualnej "{0}" nie została ukończona w wyznaczonym czasie. Agent gościa maszyny Wirtualnej Wykryto jednak uruchomiona. Sugeruje to system operacyjny gościa nie został poprawnie przygotowany do użycia jako obraz maszyny Wirtualnej (CreateOption = FromImage). Aby rozwiązać ten problem, użyj wirtualnego dysku twardego, ponieważ jest z CreateOption = Attach lub prawidłowo przygotowania go do użycia jako obraz:   <ul><li>Instrukcje dla systemu Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instrukcje dla systemu Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Wymagany rozmiar maszyny wirtualnej nie jest obecnie dostępny w wybranej lokalizacji.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Nie można zaktualizować zasobu w tej chwili z powodu trwającej platformy aktualizacji. Spróbuj ponownie później.  |
|  StorageAccountLimitation  |  Konto magazynu "{0}" nie obsługuje stronicowych obiektów blob, które są wymagane do tworzenia dysków.  |
|  StorageAccountLimitation  |  Konto magazynu "{0}" przekroczył limit przydziału.  |
|  StorageAccountLocationMismatch  |  Nie można rozpoznać konta magazynu {0}. Upewnij się, że został on utworzony za pośrednictwem dostawcy zasobów magazynu w tej samej lokalizacji co zasób obliczeniowy.  |
|  StorageAccountNotFound  |  Konto magazynu {0} nie można odnaleźć. Upewnij się, konto magazynu nie zostanie usunięta i należy do tej samej lokalizacji platformy Azure co maszyna wirtualna.  |
|  StorageAccountNotRecognized  |  Użyj konta magazynu zarządzanego przez dostawcę zasobów magazynu. Użycie {0} nie jest obsługiwane.  |
|  StorageAccountOperationInternalError  |  Wystąpił błąd wewnętrzny podczas uzyskiwania dostępu do konta magazynu {0}.  |
|  StorageAccountSubscriptionMismatch  |  Konto magazynu {0} nie należy do subskrypcji {1}.  |
|  StorageAccountTooBusy  |  Konto magazynu "{0}" jest obecnie zbyt zajęty. Należy wziąć pod uwagę przy użyciu innego konta.  |
|  StorageAccountTypeNotSupported  |  Dysk {0} używa {1} czyli konta magazynu obiektów Blob. Spróbuj ponownie z konta magazynu ogólnego przeznaczenia.  |
|  StorageAccountTypeNotSupported  |  Konto magazynu {0} jest {1} typu. Obsługuje diagnostyki rozruchu {2} typy kont magazynu.  <ul><li>Ten błąd występuje, jeśli używasz konta magazynu w warstwie premium dla diagnostyki rozruchu. Aby uzyskać więcej informacji, zobacz [sposobu korzystania z diagnostyki rozruchu](../articles/virtual-machines/windows/boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Subskrypcja nie została autoryzowana.  |
|  TargetDiskBlobAlreadyExists  |  Obiekt blob {0} już istnieje. Podaj inny identyfikator URI, aby utworzyć nowy pusty dysk danych obiektu blob "{1}".  |
|  TargetDiskBlobAlreadyExists  |  Przechwytywanie nie można kontynuować operacji, ponieważ obiekt blob obrazu docelowego {0} już istnieje i nie jest ustawiona flaga, aby zastępować obiekty BLOB dysków VHD. Usuń obiekt blob, albo Ustaw flagę, aby zastępować obiekty BLOB dysków VHD, a następnie spróbuj ponownie.  |
|  TargetDiskBlobAlreadyExists  |  Przechwytywanie nie można kontynuować operacji, ponieważ obiekt blob obrazu docelowego {0} ma aktywną dzierżawę na nim.   |
|  TargetDiskBlobAlreadyExists  |  Obiekt blob {0} już istnieje. Podaj inny identyfikator URI obiektu blob jako cel dysku "{1}".  |
|  TooManyVMRedeploymentRequests  |  Odebrano zbyt dużą liczbę żądań dla maszyny Wirtualnej "{0}" lub maszyn wirtualnych w tym samym zestawie dostępności o tej maszyny Wirtualnej. Spróbuj ponownie później.  |
|  VHDSizeInvalid  |  Wartość rozmiaru dysku z {0} dla dysku "{1}" z obiektu blob {2} jest nieprawidłowy. Rozmiar dysku musi należeć do zakresu od {3} i {4}.  |
|  VMAgentStatusCommunicationError  |  Maszyna wirtualna "{0}" nie zgłosiła stanu agenta maszyny Wirtualnej lub rozszerzenia. Sprawdź, czy maszyna wirtualna ma działającego agenta maszyny Wirtualnej i może nawiązywać wychodzące połączenia z magazynem platformy Azure.  |
|  VMArtifactRepositoryInternalError  |  Podczas komunikacji z repozytorium artefaktów w celu pobrania szczegółów artefaktu maszyny wirtualnej wystąpił błąd.  |
|  VMArtifactRepositoryInternalError  |  Wystąpił błąd wewnętrzny podczas pobierania danych artefaktu maszyny wirtualnej z repozytorium artefaktów.  |
|  VMExtensionHandlerNonTransientError  |  Program obsługi "{0}"zgłosiła błąd dla rozszerzenia maszyny Wirtualnej"{1}"z kodem błędu terminala"{2}" i komunikatem o błędzie: "{3}"  |
|  VMExtensionManagementInternalError  |  Wystąpił błąd wewnętrzny podczas przetwarzania rozszerzenia maszyny Wirtualnej "{0}".  |
|  VMExtensionManagementInternalError  |  Wystąpiło wiele błędów podczas przygotowywania rozszerzeń maszyny Wirtualnej. Zobacz widok wystąpienia rozszerzenia maszyny Wirtualnej, aby uzyskać szczegółowe informacje.  |
|  VMExtensionProvisioningError  |  Maszyna wirtualna zgłosiła błąd podczas przetwarzania rozszerzenia "{0}". Komunikat o błędzie: "{1}".  |
|  VMExtensionProvisioningError  |  Wiele rozszerzeń maszyny Wirtualnej nie może być inicjowana na Maszynie wirtualnej. Zobacz widok wystąpienia rozszerzenia maszyny Wirtualnej, aby uzyskać szczegółowe informacje.  |
|  VMExtensionProvisioningTimeout  |  Inicjowanie obsługi rozszerzenia maszyny Wirtualnej "{0}" Upłynął limit czasu. Instalacja rozszerzenia może trwać zbyt długo lub nie można uzyskać stanu rozszerzenia.  |
|  VMMarketplaceInvalidInput  |  Tworzenie maszyny wirtualnej z obrazu niepochodzącego z witryny Marketplace nie wymaga informacji o planie, Usuń informacji o planie w żądaniu. Nazwa dysku systemu operacyjnego jest {0}.  |
|  VMMarketplaceInvalidInput  |  Informacje o zakupie nie jest zgodna. Nie można wdrożyć z obrazu witryny Marketplace. Nazwa dysku systemu operacyjnego jest {0}.  |
|  VMMarketplaceInvalidInput  |  Tworzenie maszyny wirtualnej z obrazu witryny Marketplace wymaga informacji o planie w żądaniu. Nazwa dysku systemu operacyjnego jest {0}.  |
|  VMNotFound  |  Maszyna wirtualna "{0}" nie można odnaleźć.  |
|  VMRedeploymentFailed  |  Maszyna wirtualna "{0}" ponowne wdrożenie nie powiodło się z powodu błędu wewnętrznego. Spróbuj ponownie później.  |
|  VMRedeploymentTimedOut  |  Ponowne wdrożenie maszyny Wirtualnej "{0}" nie zostało zakończone w przydzielonym czasie. Go może zakończyła się pomyślnie w pewnym czasie. W przeciwnym wypadku można ponowić żądanie.  |
|  VMStartTimedOut  |  Maszyna wirtualna "{0}" nie uruchomiła się w wyznaczonym czasie. Maszyna wirtualna nadal może uruchomić się pomyślnie. Sprawdź później stan zasilania.  |


## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz więcej pomocy, skontaktuj się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.