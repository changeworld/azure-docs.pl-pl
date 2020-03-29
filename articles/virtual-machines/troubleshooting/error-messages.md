---
title: Typowe kody błędów maszyn wirtualnych na platformie Azure | Dokumenty firmy Microsoft
description: Opis niektórych typowych kodów błędów napotkanych podczas udostępniania maszyn wirtualnych i zarządzania nimi na platformie Azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: f5639d1cf94c77d699dc6de9841698b045ac1f96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543022"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Omówienie typowych komunikatów o błędach występujących podczas zarządzania maszynami wirtualnymi na platformie Azure

W tym artykule opisano niektóre z najczęstszych kodów błędów i komunikatów, które mogą wystąpić podczas tworzenia maszyn wirtualnych (VM) na platformie Azure lub zarządzania nimi.

>[!NOTE]
> Możesz zostawić komentarze na tej stronie w celu uzyskania opinii lub opinii [na platformie Azure](https://feedback.azure.com/forums/216843-virtual-machines) za pomocą #azerrormessage tagu.

## <a name="error-response-format"></a>Format odpowiedzi na błędy 
Maszyny wirtualne platformy Azure używają następującego formatu JSON do reagowania na błędy:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner level error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Odpowiedź na błąd zawsze zawiera kod stanu i obiekt błędu. Każdy obiekt błędu zawsze zawiera kod błędu i komunikat. Jeśli maszyna wirtualna jest tworzona za pomocą szablonu, obiekt błędu zawiera również sekcję szczegółów, która zawiera wewnętrzny poziom kodów błędów i komunikatu. Zwykle najbardziej wewnętrzny poziom komunikatu o błędzie jest błąd główny.


## <a name="common-virtual-machine-management-errors"></a>Typowe błędy zarządzania maszynami wirtualnymi

W tej sekcji wymieniono typowe komunikaty o błędach, które mogą wystąpić podczas zarządzania maszynami wirtualnymi:

|  Kod błędu  |  Komunikat o błędzie  |  
|  :------| :-------------|  
|  AcquireDiskLeasePoniejpokojone  |  Nie można uzyskać dzierżawy podczas{0}tworzenia dysku ' {1}' przy użyciu obiektu blob z identyfikatorem URI . Obiekt Blob jest już używany.  |  
|  Alokacja Nieugięta  |  Alokacja nie powiodła się. Spróbuj zmniejszyć rozmiar maszyny Wirtualnej lub liczbę maszyn wirtualnych, ponów próbę później lub spróbuj wdrożyć w innym zestawie dostępności lub innej lokalizacji platformy Azure.  |  
|  Alokacja Nieugięta  |  Alokacja maszyny Wirtualnej nie powiodła się z powodu błędu wewnętrznego. Ponów próbę później lub spróbuj wdrożyć w innej lokalizacji.  |
|  ArtifactNotFound (ArtifactNotFound)  |  Nie można odnaleźć rozszerzenia maszyny Wirtualnej z wydawcą '{0}' i type '{1}' w lokalizacji '.{2}  |
|  ArtifactNotFound (ArtifactNotFound)  |  Nie można{0}odnaleźć rozszerzenia z wydawcą ' ', type '{1}i type handler version '{2}' w repozytorium rozszerzeń.  |
|  ArtifactVersionNotFound  |  W repozytorium artefaktów nie znaleziono żadnej wersji, która{0}spełnia żądaną wersję ' '.  |
|  ArtifactVersionNotFound  |  W repozytorium artefaktów nie znaleziono żadnej wersji, która{0}spełnia żądaną wersję '{1}' dla{2}rozszerzenia maszyny Wirtualnej z wydawcą ' ' i type ' '.  |
|  AttachDiskWhileBeingDetached  |  Nie można dołączyć{0}dysku danych{1}' ' do maszyny Wirtualnej ',ponieważ dysk jest obecnie odłączony. Poczekaj, aż dysk zostanie całkowicie odłączony, a następnie spróbuj ponownie.  |
|  BadRequest  |  Zestawy dostępności wyrównane nie są jeszcze obsługiwane w tym regionie.  |
|  BadRequest  |  Dodawanie maszyny Wirtualnej z dyskami zarządzanymi do niezarządzanego zestawu dostępności lub dodawanie maszyny Wirtualnej z dyskami opartymi na obiekcie blob do zarządzanego zestawu dostępności nie jest obsługiwane. Utwórz zestaw dostępności z zestawem właściwości "zarządzanych", aby dodać do niej maszynę wirtualną z dyskami zarządzanymi.  |
|  BadRequest  |  Dyski zarządzane nie są obsługiwane w tym regionie.  |
|  BadRequest  |  Wiele VMExtensions na program obsługi nie{0}jest obsługiwany dla typu systemu operacyjnego ' . VMExtension{1}' z{2}programem obsługi ' ' już dodane lub określone w danych wejściowych.  |
|  BadRequest  |  Operacja{0}' ' nie jest{1}obsługiwana w zasobie ' ' z dyskami zarządzanymi.  |
|  CertyfikatImproperlyFormatted  |  W ramach operacji JSON klucza tajnego pobrane z {0} niego znajduje się pole danych, które nie jest poprawnie sformatowanym plikiem PFX lub podane hasło nie powoduje prawidłowego dekodowania pliku PFX.  |
|  CertyfikatImproperlyFormatted  |  Dane pobrane {0} z nie jest deserializable do JSON.  |
|  Konflikt  |  Zmiana rozmiaru dysku jest dozwolona tylko podczas tworzenia maszyny Wirtualnej lub gdy maszyna wirtualna jest cofnięta.  |
|  KonfliktUserInput  |  Dysk{0}' ' nie może być dołączony, ponieważ{1}dysk jest już własnością maszyny Wirtualnej '.  |
|  KonfliktUserInput  |  Grupy zasobów źródłowych i docelowych są takie same.  |
|  KonfliktUserInput  |  Konta magazynu źródłowego {0} i docelowego dysku są różne.  |
|  KontenerAlreadyOnLease  |  Istnieje już dzierżawa kontenera magazynu zawierającego obiekt {0}blob z identyfikatorem URI .  |
|  CrossSubscriptionMoveWithKeyVaultŹródło  |  Żądanie przenieś zasobów zawiera zasoby KeyVault, do {0}których odwołuje się co najmniej jeden s w żądaniu. Obecnie nie jest to obsługiwane w ucho podczas subskrypcji Krzyżowej Move. Sprawdź szczegóły błędu dla identyfikatorów zasobów KeyVault.  |
|  DiagnostykaOperacjaInternalError  |  Wystąpił błąd wewnętrzny podczas przetwarzania profilu {0}diagnostyki maszyny Wirtualnej .  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Obiekt {0} Blob jest już używany przez inny{1}dysk należący do maszyny Wirtualnej ' . Metadane obiektu blob można sprawdzić pod kątem informacji dotyczących odwołania do dysku.  |
|  DiskBlobNotFound (Odnaleźć dysk)  |  Nie można odnaleźć {0} obiektu blob VHD z identyfikatorem URI dla dysku '{1}.  |
|  DiskBlobNotFound (Odnaleźć dysk)  |  Nie można odnaleźć {0}obiektu blob VHD z identyfikatorem URI .  |
|  Tagi DiskEncryptionKeySecretMissingTags  |  {0}secret nie ma {1} tagów. Zaktualizuj tajną wersję, dodaj wymagane tagi i ponów próbę.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Rozpakowanie wartości {0} tajnej {1} przy użyciu klucza nie powiodło się.  |
|  DiskImageNotReady (Narzędzie DiskImageNotReady)  |  Obraz {0} dysku {1} jest w stanie. Ponów próbę, gdy obraz jest gotowy.  |
|  Program DiskPreparationError  |  Wystąpił co najmniej jeden błąd podczas przygotowywania dysków maszyn wirtualnych. Zobacz widok wystąpienia dysku, aby uzyskać szczegółowe informacje.  |
|  Program DiskProcessingError  |  Przetwarzanie dysku zostało zatrzymane, ponieważ maszyna wirtualna ma inne dyski na dyskach, które uległy awarii.  |
|  ImageBlobNotFound (ObrazBlobNotFound)  |  Nie można odnaleźć {0} obiektu blob VHD z identyfikatorem URI dla dysku '{1}.  |
|  ImageBlobNotFound (ObrazBlobNotFound)  |  Nie można odnaleźć {0}obiektu blob VHD z identyfikatorem URI .  |
|  Typ niepoprawny dyskskus  |  Obiekty BLOB dysku mogą być tylko typu stronicowego obiektu blob. Obiekt {0} blob{1}dla dysku ' ' jest typu bloku blob.  |
|  Typ niepoprawny dyskskus  |  Obiekty BLOB dysku mogą być tylko typu stronicowego obiektu blob. Obiekt {0} Blob jest{1}typu ' '.  |
|  Niepoprawny Typ Ujeżdna  |  Obiekty BLOB dysku mogą być tylko typu stronicowego obiektu blob. Obiekt {0} blob{1}dla dysku ' ' jest typu bloku blob.  |
|  Niepoprawny Typ Ujeżdna  |  Obiekty BLOB dysku mogą być tylko typu stronicowego obiektu blob. Obiekt {0} Blob jest{1}typu ' '.  |
|  InternalOperationError  |  Nie można rozpoznać konta {0}magazynu . Upewnij się, że został utworzony za pośrednictwem dostawcy zasobów magazynu w tej samej lokalizacji co zasób obliczeniowy.  |
|  InternalOperationError  |  {0}zadania nie powiodły się.  |
|  InternalOperationError  |  Wystąpił błąd podczas sprawdzania poprawności profilu{0}sieciowego maszyny Wirtualnej ' .  |
|  Typ konta Nieprawidłowego  |  Typ konta {0} jest nieprawidłowy.  |
|  Nieprawidłowyparametr  |  Wartość parametru {0} jest nieprawidłowa.  |
|  Nieprawidłowyparametr  |  Określone hasło administratora jest niedozwolone.  |
|  Nieprawidłowyparametr  |  "Podane hasło musi {0}być{1} między - znaki {2} długie i musi spełniać co najmniej wymagania dotyczące złożoności hasła z następujących czynności: <ol><li> Zawiera znak wielkich liter</li><li>Zawiera małe litery</li><li>Zawiera cyfrę</li><li>Zawiera znak specjalny.</li></ol>  |
|  Nieprawidłowyparametr  |  Określona nazwa użytkownika administratora jest niedozwolona.  |
|  Nieprawidłowyparametr  |  Nie można dołączyć istniejącego dysku systemu operacyjnego, jeśli maszyna wirtualna jest tworzona na podstawie obrazu platformy lub użytkownika.  |
|  Nieprawidłowyparametr  |  Nazwa {0} kontenera jest nieprawidłowa. Nazwy kontenerów muszą mieć długość od 3 do 63 znaków i mogą zawierać tylko małe litery alfanumeryczne i łącznik. Łącznik musi być poprzedzony i następuje znak alfanumeryczny.  |
|  Nieprawidłowyparametr  |  Nazwa {0} kontenera {1} w adresie URL jest nieprawidłowa. Nazwy kontenerów muszą mieć długość od 3 do 63 znaków i mogą zawierać tylko małe litery alfanumeryczne i łącznik. Łącznik musi być poprzedzony i następuje znak alfanumeryczny.  |
|  Nieprawidłowyparametr  |  Nazwa obiektu blob {0} w adresie URL zawiera ukośnik. Obecnie nie jest to obsługiwane dla dysków.  |
|  Nieprawidłowyparametr  |  Identyfikator URI {0} nie wygląda na poprawne identyfikatora URI obiektu blob.  |
|  Nieprawidłowyparametr  |  Dysk o{0}nazwie ' ' używa już {1}tej samej jednostki LUN: .  |
|  Nieprawidłowyparametr  |  Dysk o{0}nazwie ' już istnieje.  |
|  Nieprawidłowyparametr  |  Nie można określić zastąpienia obrazu użytkownika dla dysku już zdefiniowanego w określonym odwołaniu do obrazu.  |
|  Nieprawidłowyparametr  |  Dysk o{0}nazwie ' ' już używa {1}tego samego adresu URL VHD .  |
|  Nieprawidłowyparametr  |  Określona liczba {0} domen błędów {1} musi {2}leżyć w zakresie do .  |
|  Nieprawidłowyparametr  |  Typ {0} licencji jest nieprawidłowy. Prawidłowe typy licencji to: Windows_Client lub Windows_Server, rozróżniana wielkość liter.  |
|  Nieprawidłowyparametr  |  Nazwa hosta {0} systemu Linux nie może przekraczać długości znaków ani zawierać następujących znaków: {1}.  |
|  Nieprawidłowyparametr  |  Ścieżka docelowa dla kluczy publicznych Ssh {0} jest obecnie ograniczona do wartości domyślnej z powodu znanego problemu w agencie inicjowania obsługi administracyjnej systemu Linux.  |
|  Nieprawidłowyparametr  |  Dysk w jednostce LUN {0} już istnieje.  |
|  Nieprawidłowyparametr  |  Subskrypcja {0} żądania musi być {1} zgodna z subskrypcją zawartą w identyfikatorze dysku zarządzanego.  |
|  Nieprawidłowyparametr  |  Dane niestandardowe w OSProfile muszą być w kodowaniu {0} Base64 i z maksymalną długością znaków.  |
|  Nieprawidłowyparametr  |  Nazwa obiektu blob w adresie URL {0} musi kończyć się rozszerzeniem '.{1}  |
|  Nieprawidłowyparametr  |  {0}' nie jest prawidłowym przechwyconym prefiksem nazwy obiektu blob przechwyconego VHD. Prawidłowy prefiks pasuje{1}do wyrażenia regularnego ' ".  |
|  Nieprawidłowyparametr  |  Certyfikaty nie można dodać do maszyny Wirtualnej, jeśli agent maszyny Wirtualnej nie jest aprowizowana.  |
|  Nieprawidłowyparametr  |  Dysk w jednostce LUN {0} już istnieje.  |
|  Nieprawidłowyparametr  |  Nie można utworzyć maszyny Wirtualnej, {0} ponieważ żądany rozmiar nie jest dostępny w klastrze, w którym aktualnie przydzielono zestaw dostępności. Dostępne rozmiary to: {1}. Dowiedz się więcej o strategii https://aka.ms/azure-resizevmzmiany rozmiaru maszyn wirtualnych na stronie .  |
|  Nieprawidłowyparametr  |  Żądany rozmiar {0} maszyny Wirtualnej nie jest dostępny w bieżącym regionie. Rozmiary dostępne w bieżącym regionie {1}to: . Dowiedz się więcej o dostępnych rozmiarach maszyn https://aka.ms/azure-regionswirtualnych w każdym regionie pod adresem .  |
|  Nieprawidłowyparametr  |  Żądany rozmiar {0} maszyny Wirtualnej nie jest dostępny w bieżącym regionie. Dowiedz się więcej o dostępnych rozmiarach maszyn https://aka.ms/azure-regionswirtualnych w każdym regionie pod adresem .  |
|  Nieprawidłowyparametr  |  Nazwa użytkownika administratora systemu {0} Windows nie może być większa niż długa na {1}znaki, kończy się kropką(.) ani zawierać następujących znaków: .  |
|  Nieprawidłowyparametr  |  Nazwa komputera z systemem {0} Windows nie może być większa niż znaki, {1}być całkowicie numeryczna lub zawierać następujące znaki: .  |
|  BrakMoveDependentResources  |  Żądanie przenoszenia zasobów nie zawiera wszystkich zasobów zależnych. Sprawdź szczegóły błędu brakujących identyfikatorów zasobów.  |
|  MoveResourcesHaveInvalidState  |  Żądanie Przenieś zasoby zawiera maszyny wirtualne, które są skojarzone z nieprawidłowymi kontami magazynu. Sprawdź szczegóły dotyczące tych identyfikatorów zasobów i nazw kont magazynu, do których istnieje odwołanie.  |
|  MoveResourcesHavePendingOperations  |  Żądanie przenoszenia zasobów zawiera zasoby, dla których operacja jest w toku. Sprawdź szczegóły dotyczące tych identyfikatorów zasobów. Ponów próbę wykonania operacji po zakończeniu oczekujących operacji.  |
|  MoveResourcesNotFound  |  Żądanie przenoszenia zasobów zawiera zasoby, których nie można odnaleźć. Sprawdź szczegóły dotyczące tych identyfikatorów zasobów.  |
|  NetworkingInternalOperationError  |  Nieznany błąd alokacji sieci.  |
|  NetworkingInternalOperationError  |  Nieznany błąd alokacji sieci  |
|  NetworkingInternalOperationError  |  Wystąpił błąd wewnętrzny podczas przetwarzania profilu sieciowego maszyny Wirtualnej.  |
|  NotFound  |  Nie można {0} odnaleźć zestawu dostępności.  |
|  NotFound  |  Źródło virtual{0}machine ' ' określony w żądaniu nie istnieje w tej lokalizacji platformy Azure.  |
|  NotFound  |  Nie znaleziono {0} dzierżawy z identyfikatorem.  |
|  NotFound  |  Nie {0} można odnaleźć obrazu.  |
|  Notsupported  |  Typ licencji {0}jest , ale {1} obiekt blob obrazu nie pochodzi z lokalnego.  |
|  Operacja Nieuwolna  |  Nie można {0} usunąć zestawu dostępności. Przed usunięciem zestawu dostępności upewnij się, że nie zawiera żadnej maszyny Wirtualnej.  |
|  Operacja Nieuwolna  |  Zmiana zestawu jednostek SKU zestawu dostępności z "Wyrównane" na "Klasyczny" jest niedozwolona.  |
|  Operacja Nieuwolna  |  Nie można modyfikować rozszerzeń na maszynie Wirtualnej, gdy maszyna wirtualna nie jest uruchomiona.  |
|  Operacja Nieuwolna  |  Akcja Przechwytywanie jest obsługiwana tylko na maszynie wirtualnej z dyskami opartymi na obiekcie blob. Użyj interfejsów API zasobów "Obraz", aby utworzyć obraz z zarządzanej maszyny wirtualnej.  |
|  Operacja Nieuwolna  |  Nie {0} można utworzyć zasobu na podstawie obrazu, {1} dopóki obraz nie zostanie pomyślnie utworzony.  |
|  Operacja Nieuwolna  |  Aktualizacje szyfrowaniaStawienia nie jest dozwolone, gdy maszyna wirtualna jest przydzielana, Ponów próbę po zdezynkowaniu maszyny Wirtualnej  |
|  Operacja Nieuwolna  |  Dodawanie dysku zarządzanego do maszyny Wirtualnej z dyskami opartymi na obiekcie blob nie jest obsługiwane.  |
|  Operacja Nieuwolna  |  Maksymalna liczba dysków z danymi, które mogą być dołączone {0}do maszyny Wirtualnej o tym rozmiarze, to .  |
|  Operacja Nieuwolna  |  Dodawanie dysku opartego na obiekcie blob do maszyny Wirtualnej z dyskami zarządzanymi nie jest obsługiwane.  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest{1}dozwolona na obrazie '' ponieważ obraz jest oznaczony do usunięcia. Operację usuwania można ponowić tylko próbę usunięcia (lub poczekać na jej ukończenie).  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona na maszynie Wirtualnej ',{1}ponieważ maszyna wirtualna jest uogólniona.  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona{1}jako kolekcja punktów przywracania ' jest oznaczona do usunięcia.  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona{1}w rozszerzeniu maszyny Wirtualnej '' ponieważ jest oznaczona do usunięcia. Operację usuwania można ponowić tylko próbę usunięcia (lub poczekać na jej ukończenie).  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona, ponieważ maszyny wirtualne{1}{2}' są aprowied przy użyciu obrazu ' .  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona,{1}ponieważ zestaw scaleset maszyny{2}wirtualnej ' jest obecnie używany obraz ' " .  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona na maszynie Wirtualnej ',{1}ponieważ maszyna wirtualna jest oznaczona do usunięcia. Operację usuwania można ponowić tylko próbę usunięcia (lub poczekać na jej ukończenie).  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona na maszynie Wirtualnej ',{1}ponieważ maszyna wirtualna jest cofnięta lub oznaczona jako cofnięta.  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona na maszynie Wirtualnej ',{1}ponieważ maszyna wirtualna jest uruchomiona. Wyłącz jawnie w przypadku zamknięcia maszyny Wirtualnej z wnętrza systemu operacyjnego gościa.  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona na maszynie Wirtualnej ',{1}ponieważ maszyna wirtualna nie jest cofnięta alokacja.  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona na maszynie Wirtualnej '{1}' ponieważ maszyna wirtualna ma rozszerzenie '{2}w stanie awarii.  |
|  Operacja Nieuwolna  |  Operacja{0}' ' nie jest dozwolona na maszynie Wirtualnej ',{1}ponieważ trwa inna operacja.  |
|  Operacja Nieuwolna  |  Operacja '{0}' wymaga maszyny{1}wirtualnej ' do uogólnione.  |
|  Operacja Nieuwolna  |  Operacja wymaga maszyny Wirtualnej do uruchomienia (lub ustawiona do uruchomienia).  |
|  Operacja Nieuwolna  |  Dysk o {0}rozmiarze GB, który {1}jest mniejszy niż rozmiar GB odpowiedniego dysku w obrazie, jest niedozwolony.  |
|  Operacja Nieuwolna  |  Rozszerzenia zestawu skalowania maszyny{0}Wirtualnej programu handler ' można dodać tylko w momencie tworzenia zestawu skalowania maszyny Wirtualnej.  |
|  Operacja Nieuwolna  |  Rozszerzenia zestawu skalowania maszyny{0}Wirtualnej programu obsługi ' można usunąć tylko w momencie usuwania zestawu skalowania maszyny Wirtualnej.  |
|  Operacja Nieuwolna  |  VM{0}' ' jest już przy użyciu dysków zarządzanych.  |
|  Operacja Nieuwolna  |  VM{0}' ' należy do zestawu dostępności{1}"Classic" ' . Zaktualizuj zestaw dostępności, aby użyć jednostki SKU "Wyrównane", a następnie ponów próbę konwersji.  |
|  Operacja Nieuwolna  |  Maszyna wirtualna utworzona na podstawie obrazu nie może mieć dysków opartych na obiektach blob. Wszystkie dyski muszą być dyskami zarządzanymi.  |
|  Operacja Nieuwolna  |  Nie można ukończyć operacji przechwytywania, ponieważ maszyna wirtualna nie jest uogólniona.  |
|  Operacja Nieuwolna  |  Operacje zarządzania na{0}maszynie Wirtualnej ' są niedozwolone, ponieważ dyski maszyn wirtualnych są konwertowane na dyski zarządzane.  |
|  Operacja Nieuwolna  |  Bieżąca operacja zmienia stan zasilania {0} {1}maszyny wirtualnej na . Proszę wykonać {2} operację po pewnym czasie.  |
|  Operacja Nieuwolna  |  Nie można dodać ani zaktualizować maszyny Wirtualnej. Żądany rozmiar {0} maszyny Wirtualnej może nie być dostępny w istniejącej jednostce alokacji. Dowiedz się więcej o strategii https://aka.ms/azure-resizevmzmiany rozmiaru maszyn wirtualnych na stronie .  |
|  Operacja Nieuwolna  |  Nie można zmienić rozmiaru maszyny Wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępny w klastrze, w którym aktualnie przydzielono zestaw dostępności. Dostępne rozmiary to: {1}. Dowiedz się więcej o strategii https://aka.ms/azure-resizevmzmiany rozmiaru maszyn wirtualnych na stronie .  |
|  Operacja Nieuwolna  |  Nie można zmienić rozmiaru maszyny Wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępny w klastrze, w którym maszyna wirtualna jest aktualnie przydzielona. Aby zmienić rozmiar maszyny {1} Wirtualnej, aby uzyskać przydział (jest to operacja Zatrzymaj w witrynie Azure portal) i spróbuj ponownie wykonać operację ponownego rozmiaru. Dowiedz się więcej o strategii https://aka.ms/azure-resizevmzmiany rozmiaru maszyn wirtualnych na stronie .  |
|  OSProvisioningClientError  |  Inicjowanie obsługi administracyjnej{0}systemu operacyjnego nie powiodło się dla maszyny Wirtualnej '' ponieważ system operacyjny gościa jest obecnie aprowizowana.  |
|  OSProvisioningClientError  |  Inicjowanie obsługi administracyjnej dla maszyny Wirtualnej '{0}' nie powiodło się. Szczegóły błędu: {1} Upewnij się, że obraz został prawidłowo przygotowany (uogólniony). <ul><li>Instrukcje dotyczące systemu Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Generowanie klucza hosta SSH nie powiodło się. Szczegóły błędu: {0}. Aby rozwiązać ten problem sprawdź, czy agent systemu Linux jest poprawnie skonfigurowany. <ul><li>Instrukcję można sprawdzić pod adresem:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Nazwa użytkownika określona dla maszyny Wirtualnej jest nieprawidłowa dla tej dystrybucji systemu Linux. Szczegóły błędu: {0}.  |
|  OSProvisioningInternalError  |  Inicjowanie obsługi administracyjnej{0}systemu operacyjnego nie powiodło się dla maszyny Wirtualnej ' ' z powodu błędu wewnętrznego.  |
|  OSProvisioningTimedOut  |  Inicjowanie obsługi administracyjnej dla maszyny Wirtualnej '{0}' nie zakończył się w wyznaczonym czasie. Maszyna wirtualna może jeszcze zakończyć inicjowania obsługi administracyjnej pomyślnie. Sprawdź stan inicjowania obsługi administracyjnej później.  |
|  OSProvisioningTimedOut  |  Inicjowanie obsługi administracyjnej dla maszyny Wirtualnej '{0}' nie zakończył się w wyznaczonym czasie. Maszyna wirtualna może jeszcze zakończyć inicjowania obsługi administracyjnej pomyślnie. Sprawdź stan inicjowania obsługi administracyjnej później. Upewnij się również, że obraz został odpowiednio przygotowany (uogólniony).   <ul><li>Instrukcje dotyczące systemu Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instrukcje dla Linuksa:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Inicjowanie obsługi administracyjnej dla maszyny Wirtualnej '{0}' nie zakończył się w wyznaczonym czasie. Jednak agent gościa maszyny Wirtualnej został wykryty uruchomiony. Sugeruje to, że system operacyjny gościa nie został odpowiednio przygotowany do użycia jako obraz maszyny Wirtualnej (z CreateOption=FromImage). Aby rozwiązać ten problem, użyj dysku VHD, tak jak w przypadku CreateOption=Attach= Dołącz lub przygotuj go poprawnie do użycia jako obraz:   <ul><li>Instrukcje dotyczące systemu Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instrukcje dla Linuksa:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest (OverConstrainedAllocationRequest)  |  Wymagany rozmiar maszyny Wirtualnej nie jest obecnie dostępny w wybranej lokalizacji.  |
|  UpdateUpdate ResourceUpdateOnPlatformUpdate  |  Zasób nie może być aktualizowany w tej chwili ze względu na trwającą aktualizację platformy. Spróbuj ponownie później.  |
|  Mitowanie konta magazynu  |  Konto magazynu{0}' ' ' nie obsługuje obiektów blob stron, które są wymagane do tworzenia dysków.  |
|  Mitowanie konta magazynu  |  Konto magazynu{0}' ' przekroczyło przydzielony przydział.  |
|  StorageAccountLocationMismatch  |  Nie można rozpoznać konta {0}magazynu . Upewnij się, że został utworzony za pośrednictwem dostawcy zasobów magazynu w tej samej lokalizacji co zasób obliczeniowy.  |
|  StorageAccountNotFound  |  Nie {0} znaleziono konta magazynu. Upewnij się, że konto magazynu nie jest usuwany i należy do tej samej lokalizacji platformy Azure co maszyna wirtualna.  |
|  Konto magazynuZapoznano  |  Użyj konta magazynu zarządzanego przez dostawcę zasobów magazynu. Korzystanie {0} z nie jest obsługiwane.  |
|  StorageAccountOperationInternalError  |  Wystąpił błąd wewnętrzny podczas {0}uzyskiwania dostępu do konta magazynu .  |
|  Pamięć magazynuKonsubskrypcjaSubscriptionMismatch  |  Konto {0} magazynu nie należy {1}do subskrypcji .  |
|  StorageAccountTooBusy  |  Konto magazynu{0}' ' jest obecnie zbyt zajęty. Rozważ użycie innego konta.  |
|  Obsługiwane konto magazynu  |  Dysk {0} używa, {1} który jest kontem magazynu obiektów Blob. Ponów próbę wykonania konta magazynu ogólnego przeznaczenia.  |
|  Obsługiwane konto magazynu  |  Konto {0} magazynu {1} jest typu. Diagnostyka rozruchu obsługuje {2} typy kont magazynu.  <ul><li>Ten błąd występuje, jeśli używasz konta magazynu w wersji premium do diagnostyki rozruchowej. Aby uzyskać więcej informacji, zobacz [Jak korzystać z diagnostyki rozruchu](boot-diagnostics.md). </li></ul> |
|  SubskrypcjaNotAuthorizedForImage  |  Subskrypcja nie jest autoryzowana.  |
|  TargetDiskBlobAlreadyExists (TargetDiskBlobAlreadyExists)  |  Obiekt {0} blob już istnieje. Podaj inny identyfikator URI obiektu blob,{1}aby utworzyć nowy pusty dysk danych ' ' .  |
|  TargetDiskBlobAlreadyExists (TargetDiskBlobAlreadyExists)  |  Operacja przechwytywania nie może {0} być kontynuowana, ponieważ obiekt blob obrazu docelowego już istnieje, a flaga do zastępowanie obiektów blob VHD nie jest ustawiona. Usuń obiekt blob lub ustaw flagę, aby zastąpić obiekty BLOB i ponowić próbę.  |
|  TargetDiskBlobAlreadyExists (TargetDiskBlobAlreadyExists)  |  Operacja przechwytywania nie może {0} być kontynuowana, ponieważ obiekt blob obrazu docelowego ma aktywną dzierżawę.   |
|  TargetDiskBlobAlreadyExists (TargetDiskBlobAlreadyExists)  |  Obiekt {0} blob już istnieje. Podaj inny identyfikator URI obiektu{1}blob jako obiekt docelowy dla dysku ' .  |
|  TooManyVMRedeploymentZapyty  |  Odebrano zbyt wiele żądań ponownego rozmieszczenia{0}dla maszyn wirtualnych ' lub maszyn wirtualnych w tym samym zestawie dostępności z tą maszyną wirtualną. Spróbuj ponownie później.  |
|  VHDSizeInvalid  |  Określona wartość rozmiaru {0} dysku{1}dla dysku {2} ' ' z obiektem blob jest nieprawidłowa. Rozmiar dysku musi {3} {4}być między i .  |
|  VMAgentStatusCommunicationError  |  VM{0}' ' nie zgłosił stanu agenta maszyny Wirtualnej lub rozszerzeń. Sprawdź, czy maszyna wirtualna ma uruchomionego agenta maszyny Wirtualnej i może ustanowić połączenia wychodzące z usługą Azure Storage.  |
|  VMArtifactRepositoryInternalError  |  Wystąpił błąd podczas komunikowania się z repozytorium artefaktów w celu pobrania szczegółów artefaktu maszyny Wirtualnej.  |
|  VMArtifactRepositoryInternalError  |  Wystąpił błąd wewnętrzny podczas pobierania danych artefaktu maszyny Wirtualnej z repozytorium artefaktów.  |
|  VMExtensionHandlerNonTransientError  |  Program{0}obsługi ' ' zgłosił błąd{1}rozszerzenia maszyny Wirtualnej ' ' z kodem błędu terminalu '{2}' i komunikatem o błędzie: '{3}  |
|  VMExtensionManagementInternalError  |  Wystąpił błąd wewnętrzny podczas przetwarzania{0}rozszerzenia maszyny Wirtualnej ' .  |
|  VMExtensionManagementInternalError  |  Wystąpiło wiele błędów podczas przygotowywania rozszerzeń maszyn wirtualnych. Zobacz widok wystąpienia rozszerzenia maszyny Wirtualnej, aby uzyskać szczegółowe informacje.  |
|  VMExtensionProvisioningError  |  Maszyna wirtualna zgłosiła błąd{0}podczas przetwarzania rozszerzenia ' . Komunikat o{1}błędzie: " ".  |
|  VMExtensionProvisioningError  |  Wiele rozszerzeń maszyn wirtualnych nie może być aprowizowana na maszynie Wirtualnej. Szczegółowe informacje można znaleźć w widoku wystąpienia rozszerzenia maszyny Wirtualnej.  |
|  VMExtensionProvisioningTimeout  |  Aprowizowanie rozszerzenia{0}maszyny Wirtualnej ' " ma limit czasu. Instalacja rozszerzenia może trwać zbyt długo lub nie można uzyskać stanu rozszerzenia.  |
|  VMMarketplaceInvalidInput  |  Tworzenie maszyny wirtualnej z obrazu spoza portalu Marketplace nie wymaga informacji plan, usuń informacje plan w żądaniu. Nazwa dysku systemu {0}operacyjnego to .  |
|  VMMarketplaceInvalidInput  |  Informacje o zakupie nie są zgodne. Nie można wdrożyć obrazu marketplace. Nazwa dysku systemu {0}operacyjnego to .  |
|  VMMarketplaceInvalidInput  |  Tworzenie maszyny wirtualnej z obrazu marketplace wymaga informacji plan w żądaniu. Nazwa dysku systemu {0}operacyjnego to .  |
|  VMNotFound (Wirtualny Odkryj  |  Nie można{0}odnaleźć maszyny Wirtualnej.  |
|  VMRedeploymentPokryty  |  Ponowne wdrożenie{0}maszyny Wirtualnej nie powiodło się z powodu błędu wewnętrznego. Spróbuj ponownie później.  |
|  VMRedeploymentTimedOut  |  Ponowne rozmieszczenie maszyny Wirtualnej '{0}' nie zakończyło się w wyznaczonym czasie. Może zakończyć się pomyślnie w pewnym momencie. W przeciwnym razie można ponowić próbę żądania.  |
|  VMStartTimedOut  |  VM{0}' ' nie uruchomi się w wyznaczonym czasie. Maszyna wirtualna może nadal uruchomić pomyślnie. Sprawdź stan zasilania później.  |


## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
