---
title: Zarządzanie współbieżnością w usłudze Microsoft Azure Storage
description: Jak zarządzać współbieżności dla usług obiektów Blob, kolejki, tabel i plików
services: storage
documentationcenter: ''
author: jasontang501
manager: tadb
editor: tysonn
ms.assetid: cc6429c4-23ee-46e3-b22d-50dd68bd4680
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.openlocfilehash: 937cca66a0af0674b868e6a87681adbea330e91c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "23927488"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Zarządzanie współbieżnością w usłudze Microsoft Azure Storage
## <a name="overview"></a>Przegląd
Nowoczesne aplikacje internetowe, na podstawie zwykle mają wielu użytkowników, wyświetlanie i aktualizowanie danych jednocześnie. Wymaga to deweloperom aplikacji dokładnie traktować jak zapewnia przewidywalną środowisko dla użytkowników końcowych, szczególnie w przypadku scenariuszy, w którym wielu użytkowników można aktualizować tych samych danych. Istnieją trzy Strategie współbieżności główne dane, które deweloperzy zazwyczaj należy wziąć pod uwagę:  

1. Optymistycznej współbieżności — aplikacji wykonywanie aktualizacji w ramach jego aktualizacji sprawdzi, czy dane zostały zmienione od aplikacji ostatniego odczytu danych. Na przykład jeśli dwóch użytkowników wyświetlania strony typu wiki aktualizacji do tej samej stronie wiki platformy musi upewnij Druga aktualizacja nie zastępuje pierwszą aktualizacją — i użytkownicy dokładnie, czy ich aktualizacja zakończyła się powodzeniem. Ta strategia jest najczęściej używana w aplikacji sieci web.
2. Pesymistyczne współbieżności — wyszukiwanie do przeprowadzenia aktualizacji aplikacji potrwa blokady obiektu uniemożliwia innym użytkownikom aktualizowanie danych do czasu zwolnienia blokady. Na przykład w przypadku replikacji danych główny/podległy gdzie tylko wzorcu wykona aktualizacje wzorca zwykle wstrzymuje wyłącznej blokady przez dłuższy czas na dane upewnij się, że nikt go zaktualizować.
3. Ostatni składnik zapisywania usługi wins — metody, która umożliwia żadnych operacji aktualizacji kontynuować bez sprawdzenia, czy inna aplikacja zaktualizował dane od aplikacji najpierw odczytać danych. Tej strategii (lub brak posiadanie strategii) jest zwykle używany, gdy dane są podzielone na partycje w taki sposób, że nie istnieje prawdopodobieństwo czy użytkownicy będą uzyskiwać dostęp do tych samych danych. Może również być przydatne przetransferowane przetwarzania strumieni danych w krótkim okresie.  

Ten artykuł zawiera omówienie sposobu platformy Azure Storage upraszcza tworzenie, zapewniając obsługę pierwszej klasie wszystkie trzy Strategie współbieżności.  

## <a name="azure-storage--simplifies-cloud-development"></a>Usługa Azure Storage — upraszcza tworzenie chmury
Usługi magazynu platformy Azure obsługuje wszystkie trzy strategie, chociaż jest charakterystyczny możliwości zapewniają pełną obsługę dla optymistycznej i pesymistyczne współbieżności, ponieważ został zaprojektowany do modelu wysoki poziom spójności, który gwarantuje, że Usługa magazynu zatwierdza wstawiania danych lub operacji aktualizacji wszystkich dalszych dostępów do że dane zostaną wyświetlone najnowszej aktualizacji. Platformy magazynu, które używają modelu spójność ostateczna ma zwłokę między podczas zapisu jest wykonywane przez jednego użytkownika i zaktualizowane dane są widoczne dla innych użytkowników, w związku z tym komplikując rozwoju aplikacji klienckich, aby uniknąć niespójności wpływu na użytkowników końcowych.  

Także wybrać strategii współbieżności odpowiednie deweloperzy należy wziąć pod uwagę sposób platforma magazynu izoluje zmian — szczególnie zmian do tego samego obiektu w transakcji. Usługi magazynu Azure używa izolacji migawki umożliwiają operacji odczytu równocześnie z operacji zapisu w obrębie jednej partycji. W przeciwieństwie do innych poziomach izolacji izolacji migawki gwarantuje, wszystkie operacje odczytu Zobacz migawkę spójności danych, nawet wtedy, gdy aktualizacje są wykonywane — zasadniczo zwracając ostatnie wartości przekazane podczas aktualizacji przetwarzania transakcji.  

## <a name="managing-concurrency-in-blob-storage"></a>Zarządzanie współbieżność w magazynie obiektów Blob
Można zdecydować się na użycie albo modelami współbieżności optymistycznej lub pesymistyczne zarządzanie dostępem do obiektów blob i kontenerów w usłudze obiektów blob. Jeśli nie zostanie jawnie wins strategii ostatniego zapisu jest ustawieniem domyślnym.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optymistycznej współbieżności dla obiektów blob i kontenerów
Usługa magazynu przypisuje identyfikator każdego obiektu przechowywane. Ten identyfikator jest aktualizowana każdorazowo operacji aktualizacji jest wykonywana na obiekt. Identyfikator jest zwracana do klienta jako część odpowiedź HTTP GET, przy użyciu nagłówka ETag (tag jednostki), który jest zdefiniowany w ramach protokołu HTTP. Wykonywanie aktualizacji na taki obiekt użytkownika może wysyłać w oryginalnym ETag oraz nagłówek warunkowy do upewnij się, że aktualizacja tylko wówczas, gdy spełnione pewne warunki — w takim przypadku warunku jest nagłówek "If-Match", która wymaga usługi magazynu, upewnij się, że wartość ETag określony w żądaniu aktualizacji jest taka sama, jak przechowywany w usłudze magazynu.  

Konspekt tego procesu jest następujący:  

1. Pobieranie obiektu blob z usługą Magazyn, odpowiedź zawiera wartość nagłówka ETag HTTP, która identyfikuje bieżąca wersja obiektu w usłudze magazynowania.
2. Podczas aktualizacji obiektu blob obejmują wartość ETag otrzymane w kroku 1 w **If-Match** nagłówek warunkowy żądania wysyłane do usługi.
3. Usługa porównuje wartość ETag w żądaniu z bieżącej wartości ETag obiektu blob.
4. Jeśli do bieżącej wartości ETag obiektu blob jest w innej wersji niż ETag w **If-Match** warunkowego nagłówka w żądaniu, usługa zwraca błąd 412 do klienta. Oznacza to klientowi inny proces zaktualizował obiektu blob od momentu pobrany klient.
5. Jeśli do bieżącej wartości ETag obiektu blob jest w tej samej wersji co ETag w **If-Match** warunkowego nagłówka w żądaniu, usługa wykonuje żądanej operacji i aktualizuje bieżącej wartości ETag obiektu blob, aby wyświetlić jego utworzeniu nowej wersji.  

Poniższy fragment C# (za pomocą biblioteki klienta usługi Storage 4.2.0) zawiera prosty przykład sposobu tworzenia **AccessCondition If-Match** na podstawie wartości ETag, który jest dostępny z właściwości obiektu blob, który został wcześniej pobrany lub wstawić. Następnie używa **AccessCondition** obiektu w momencie aktualizacji obiektu blob: **AccessCondition** dodaje obiekt **If-Match** nagłówka żądania. Jeśli inny proces zaktualizował obiektu blob, usługa blob zwraca komunikat stanu HTTP 412 (warunek wstępny nie powiodła się.). Pełny przykład można pobrać: [współbieżności zarządzania przy użyciu usługi Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the etag in response.
string orignalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No etag, provided so orignal blob is overwritten (thus generating a new etag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the orignal ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(orignalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Usługa magazynu obejmuje również obsługę dodatkowych nagłówków warunkowego takich jak **If-Modified-Since**, **If-Unmodified-Since** i **If-None-Match** oraz ich kombinacji. Aby uzyskać więcej informacji, zobacz [określenie warunkowego nagłówki dla operacji usługi Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx) w witrynie MSDN.  

W poniższej tabeli przedstawiono operacje kontenera, które warunkowego nagłówków accept takich jak **If-Match** w żądaniu, które zwracają wartość ETag w odpowiedzi.  

| Operacja | Zwraca wartość ETag kontenera | Akceptuje warunkowego nagłówki |
|:--- |:--- |:--- |
| Tworzenie kontenera |Yes |Nie |
| Pobierz właściwości kontenera |Yes |Nie |
| Pobranie metadanych kontenera |Yes |Nie |
| Ustaw metadanych kontenera |Yes |Yes |
| Pobierz ACL kontenera |Yes |Nie |
| Ustaw ACL kontenera |Yes |Tak (*) |
| Usunąć kontenera |Nie |Yes |
| Kontener dzierżawy |Yes |Yes |
| Lista obiektów blob |Nie |Nie |

(*) Uprawnienia określone przez SetContainerACL znajdują się w pamięci podręcznej i aktualizacje te uprawnienia potrwać 30 sekund propagację, w tym okresie aktualizacje nie ma gwarancji być zgodne.  

W poniższej tabeli przedstawiono operacje obiektów blob, które warunkowego nagłówków accept takich jak **If-Match** w żądaniu, które zwracają wartość ETag w odpowiedzi.

| Operacja | Zwraca wartość ETag | Akceptuje warunkowego nagłówki |
|:--- |:--- |:--- |
| Put Blob |Yes |Yes |
| Get Blob |Yes |Yes |
| Pobierz właściwości obiektu Blob |Yes |Yes |
| Ustaw właściwości obiektów Blob |Yes |Yes |
| Pobierz metadane obiektu Blob |Yes |Yes |
| Ustaw metadane obiektu Blob |Yes |Yes |
| Obiekt Blob dzierżawy (*) |Yes |Yes |
| Migawki obiektu Blob |Yes |Yes |
| Copy Blob |Yes |Tak (w przypadku obiektów blob źródłowego i docelowego) |
| Przerwij kopiowania obiektów Blob |Nie |Nie |
| Usuwanie obiektów Blob |Nie |Yes |
| Umieść bloku |Nie |Nie |
| Umieść zablokowanych |Yes |Yes |
| Pobierz listę zablokowanych |Yes |Nie |
| Umieść stronę |Yes |Yes |
| Get zakresów stron |Yes |Yes |

(*) Obiekt Blob dzierżawy nie zmienia ETag na obiektu blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pesymistyczne współbieżności dla obiektów blob
Aby zablokować obiektu blob do wyłącznego użytku, można uzyskać [dzierżawy](http://msdn.microsoft.com/library/azure/ee691972.aspx) na nim. Podczas uzyskania dzierżawy, należy określić jak długo mają być dzierżawy: może to być od 15 do 60 sekund lub bez ograniczeń czasowych, który wynosi wyłącznej blokady. Mogą odnowić dzierżawę ograniczone do jego rozszerzenia, a po zakończeniu pracy z nim można zwolnić wszystkie dzierżawy. Usługa blob automatycznie zwalnia skończoną dzierżawy podczas wygasną.  

Dzierżawy Włącz synchronizacji różne strategie do obsługi, w tym zapisu na wyłączność / udostępnione zapisu odczytu, wyłącznego / wyłącznie do odczytu i zapisu udostępnionego / odczytu na wyłączność. W przypadku, gdy istnieje dzierżawę usługi magazynu wymusza na wyłączność zapisów (put, ustaw i operacji usunięcia) wymaga zapewnienia wyłączności dla operacji odczytu jednak developer upewnić się, że wszystkie aplikacje klienckie używać Identyfikatora dzierżawy i że tylko jeden klient naraz ma identyfikator dzierżawy prawidłowe. Operacje odczytu nie zawierające wynik identyfikator dzierżawy w udostępnionym odczytów.  

Poniższy fragment kodu C# przedstawiono przykład pobierania wyłącznego dzierżawy przez 30 sekund na obiektu blob, aktualizowania zawartości obiektu blob, a następnie zwolnienie dzierżawy. Jeśli istnieje już prawidłowa dzierżawa w obiekcie blob podczas próby uzyskania nowej dzierżawy, usługa blob zwraca wynik stanu "Konflikt HTTP (409)". Poniższy fragment kodu używa **AccessCondition** obiektu w celu hermetyzacji informacji o dzierżawie, podczas wykonywania żądania można zaktualizować obiektu blob w usłudze magazynowania.  Pełny przykład można pobrać: [współbieżności zarządzania przy użyciu usługi Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Przy próbie operacji zapisu dla dzierżawionych obiektu blob bez przekazywania identyfikator dzierżawy żądanie kończy się niepowodzeniem z powodu błędu 412. Należy pamiętać, że jeśli dzierżawa wygaśnie przed wywołaniem **UploadText** metoda, ale nadal przekazać identyfikator dzierżawy, żądanie nie powiedzie się także z **412** błędu. Aby uzyskać więcej informacji o zarządzaniu dzierżawę identyfikatorów i czas wygaśnięcia dzierżawy, zobacz [Blob dzierżawy](http://msdn.microsoft.com/library/azure/ee691972.aspx) dokumentacji REST.  

Następujące operacje obiektów blob umożliwia zarządzanie pesymistyczne współbieżności dzierżawy:  

* Put Blob
* Get Blob
* Pobierz właściwości obiektu Blob
* Ustaw właściwości obiektów Blob
* Pobierz metadane obiektu Blob
* Ustaw metadane obiektu Blob
* Usuwanie obiektów Blob
* Umieść bloku
* Umieść zablokowanych
* Pobierz listę zablokowanych
* Umieść stronę
* Get zakresów stron
* Migawki obiektu Blob — identyfikator dzierżawy jest opcjonalne, jeśli istnieje dzierżawy
* Jeśli dzierżawa istnieje w docelowym obiekcie blob wymagany identyfikator dzierżawy kopiowania obiektu Blob-
* Wymagany identyfikator dzierżawy przerwania kopiowania obiektu Blob — czy nieskończone dzierżawy istnieje w docelowym obiekcie blob
* Obiekt Blob dzierżawy  

### <a name="pessimistic-concurrency-for-containers"></a>Pesymistyczne współbieżności dla kontenerów
Dzierżawy kontenerów włączyć opisane strategie synchronizacji obsługiwany na obiekty BLOB (wyłącznie zapisu / udostępnione zapisu odczytu, wyłącznego / wyłącznie do odczytu i zapisu udostępnionego / odczytu na wyłączność) jednak w przeciwieństwie do obiektów blob usługi magazynu tylko wymusza wyłączności na operacji usuwania. Można usunąć kontenera z aktywną dzierżawę, klient musi zawierać identyfikator aktywne dzierżawy z żądanie usunięcia. Wszystkie operacje kontenera powiedzie się w kontenerze dzierżawionych bez uwzględniania identyfikator dzierżawy w takim przypadku są one udostępniane operacji. Jeśli wymagana jest wyłączności aktualizacji (put lub zestawu) lub operacji odczytu następnie deweloperzy powinien zapewnić, że wszyscy klienci używają Identyfikatora dzierżawy, a tylko jednego klienta w czasie ma identyfikator dzierżawy prawidłowe.  

Następujące operacje kontenera umożliwiają zarządzanie pesymistyczne współbieżności dzierżawy:  

* Usunąć kontenera
* Pobierz właściwości kontenera
* Pobranie metadanych kontenera
* Ustaw metadanych kontenera
* Pobierz ACL kontenera
* Ustaw ACL kontenera
* Kontener dzierżawy  

Aby uzyskać więcej informacji, zobacz:  

* [Określanie warunkowego nagłówki dla operacji usługi Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Kontener dzierżawy](http://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Obiekt Blob dzierżawy ](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Zarządzanie współbieżność w usłudze tabel
Usługa tabel używa optymistycznej współbieżności kontroli jako domyślne zachowanie podczas pracy z obiektami, w przeciwieństwie do usługi obiektów blob, którym jawnie należy wybrać do sprawdzania optymistycznej współbieżności. Różnica między usługami tabeli i obiektów blob jest, czy użytkownik może zarządzać tylko zachowania jednostek współbieżności podczas, gdy usługa blob można zarządzać współbieżności kontenerów i obiektów blob.  

Aby użyć optymistycznej współbieżności i sprawdź, czy inny proces zmodyfikowane jednostki, ponieważ został pobrany z usługi Magazyn tabel, można użyć wartość ETag otrzymany zwrócona usługa tabel jednostki. Konspekt tego procesu jest następujący:  

1. Pobrać jednostki z usługi Magazyn tabel, odpowiedź zawiera wartość ETag, która określa bieżący identyfikator skojarzony z tego obiektu w usłudze magazynowania.
2. Podczas aktualizacji jednostki zawierają wartość ETag otrzymane w kroku 1 w obowiązkowe **If-Match** nagłówka żądania wysyłane do usługi.
3. Usługa porównuje wartość ETag w żądaniu z bieżącej wartości ETag jednostki.
4. Jeśli do bieżącej wartości ETag jednostki jest inny niż element ETag w obowiązkowe **If-Match** nagłówka w żądaniu, usługa zwraca błąd 412 do klienta. Oznacza to klientowi inny proces zaktualizował jednostki, ponieważ klient pobrany.
5. Jeśli do bieżącej wartości ETag obiektu jest taki sam jak element ETag w obowiązkowe **If-Match** nagłówka w żądaniu lub **If-Match** nagłówka zawiera znaki wieloznaczne (*), usługa wykonuje żądanej operacji i aktualizacji do bieżącej wartości ETag obiektu do wyświetlenia została zaktualizowana.  

Należy pamiętać, że w przeciwieństwie do usługi blob usługi tabel wymaga klienta do uwzględnienia **If-Match** nagłówek żądania aktualizacji. Warto jednak można wymusić bezwarunkowe aktualizacji (ostatni składnik zapisywania usługi wins strategii) i obejście kontrolach współbieżności, jeśli klient ustawia **If-Match** nagłówka do znaku wieloznacznego (*) w żądaniu.  

Poniższy fragment kodu C# zawiera jednostki klienta, która wcześniej została utworzona lub pobrać o zaktualizować adres e-mail użytkownika. Początkowy Wstaw lub pobrać magazynów operacji wartość ETag w obiekcie klienta, a ponieważ próbki używa tego samego wystąpienia obiektu podczas wykonywania operacji zamieniania, automatycznie wysyła wartość ETag do usługi tabel, włączanie usługi wyszukać naruszenia współbieżności. Jeśli inny proces został zaktualizowany jednostki w magazynie tabel, usługa zwraca komunikat stanu HTTP 412 (warunek wstępny nie powiodła się.).  Pełny przykład można pobrać: [współbieżności zarządzania przy użyciu usługi Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Aby jawnie wyłączyć wyboru współbieżność, należy ustawić **ETag** właściwość **pracownika** do obiektu "*" przed wykonaniem operacji Zastąp.  

```csharp
customer.ETag = "*";  
```

W poniższej tabeli przedstawiono, jak operacje jednostki tabeli użyj wartości ETag:

| Operacja | Zwraca wartość ETag | Wymaga nagłówek If-Match żądania |
|:--- |:--- |:--- |
| Zapytanie jednostek |Yes |Nie |
| Wstaw jednostkę |Yes |Nie |
| Aktualizowanie jednostek |Yes |Yes |
| Scal jednostki |Yes |Yes |
| Usuwanie jednostki |Nie |Yes |
| Wstawianie lub zastępowanie jednostki |Yes |Nie |
| Wstawianie lub scalania jednostki |Yes |Nie |

Należy pamiętać, że **wstawienia lub Zastąp jednostki** i **wstawienia lub scalania jednostki** wykonaj operacje *nie* wykonać wszelkie kontrolach współbieżności, ponieważ nie wysyłaj wartość ETag usłudze tabel.  

Ogólnie rzecz biorąc deweloperzy przy użyciu tabel polegać na optymistycznej współbieżności podczas tworzenia skalowalnych aplikacji. W razie potrzeby pesymistyczne blokowanie jednym z podejść deweloperzy mogą korzystać, w przypadku uzyskiwania dostępu do tabel przypisać wyznaczonych obiektu blob dla każdej tabeli i spróbuj wykonać dzierżawy w obiekcie blob przed działających w tabeli. Ta metoda wymaga aplikacji, aby upewnić się, wszystkie ścieżki dostępu do danych uzyskać dzierżawę przed działających w tabeli. Należy również zauważyć, czas trwania dzierżawy minimalna to 15 sekund, które wymaga zaplanowania dokładne skalowalności.  

Aby uzyskać więcej informacji, zobacz:  

* [Operacje na jednostkach](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Zarządzanie współbieżności usługi kolejki
Jeden scenariusz, w których współbieżności jest istotny w usłudze kolejkowania jest, gdy wielu klientów są pobieranie wiadomości z kolejki. Po pobraniu wiadomości z kolejki odpowiedź zawiera komunikat i wartości otrzymania pop, co jest wymagane, aby usunąć wiadomości. Komunikat nie zostanie automatycznie usunięta z kolejki, ale po jej pobraniu, nie jest widoczny dla innych klientów dla interwału czasu określonego przez parametr visibilitytimeout. Klient, który pobiera komunikat powinien usunąć wiadomość po przetworzeniu i przed przez czas określony TimeNextVisible element odpowiedzi, która jest obliczana na podstawie wartości parametru visibilitytimeout. Wartość visibilitytimeout jest dodawana do czasu, jaką wiadomość zostanie pobrana do określenia wartości TimeNextVisible.  

Usługa kolejki nie jest obsługiwane pesymistyczne albo optymistycznej współbieżności i dla tego powodu klientów, przetwarzanie wiadomości pobierane z kolejki powinien zapewnić, że komunikaty są przetwarzane w sposób idempotentności. Ostatni strategii wins moduł zapisujący jest używana do operacji aktualizacji, takich jak SetQueueServiceProperties, SetQueueMetaData, SetQueueACL i UpdateMessage.  

Aby uzyskać więcej informacji, zobacz:  

* [Interfejs API REST usługi kolejki](http://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Pobieranie wiadomości](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Współbieżność w pliku usługi zarządzania
Usługi plików jest możliwy za pomocą dwóch różnych punktów końcowych protokołu — protokołu SMB i REST. Usługi REST nie jest obsługiwane optymistyczne blokowanie albo pesymistyczne blokowanie i wszystkie aktualizacje będzie śledzić ostatni strategii wins składnika zapisywania. Mechanizmy blokady systemu plików do zarządzania dostępem do udostępnionych plików — w tym możliwość wykonywania pesymistyczne blokowanie mogą korzystać z klientów SMB, które zainstalować udziały plików. Po otwarciu pliku klienta SMB określa dostęp do plików i udziału tryb. Opcja dostępu do pliku "Write" lub "Odczytu/zapisu" wraz z trybu udziału plików "None" spowoduje plik jest zablokowany przez klienta protokołu SMB, do czasu zamknięcia pliku. Jeśli nastąpi próba wykonania operacji REST w pliku, gdzie klient protokołu SMB ma plik zablokowany usługi REST zwróci kod stanu 409 (konflikt) z kodem błędu SharingViolation.  

Jeśli klient protokołu SMB otwarty plik do usunięcia, oznacza plik jako oczekujące usunięcie do wszystkich klientów SMB są zamknięte otwartymi dojściami w tym pliku. Gdy plik jest oznaczone jako oczekujące na usunięcie, wszelkie operacje REST dla tego pliku i zwróci kod stanu 409 (konflikt) z kodem błędu SMBDeletePending. Kod stanu 404 (nie znaleziono) nie są zwracane, ponieważ jest to możliwe w dla klienta SMB usunąć flagę oczekiwanie na usunięcie przed zamknięciem pliku. Innymi słowy kod stanu 404 (nie znaleziono) są oczekiwane tylko, gdy plik został usunięty. Należy pamiętać, że plik jest w protokole SMB do czasu usunięcia stanu, jego nie będą uwzględniane w wynikach listę plików. Należy również zauważyć, że operacje REST usuwanie plików i katalogów usunąć pozostałe są przekazywane automatycznie i nie powodują stan Oczekujące usunięcie.  

Aby uzyskać więcej informacji, zobacz:  

* [Zarządzanie plikiem blokuje](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Podsumowanie i następne kroki
Usługi Magazyn Microsoft Azure został zaprojektowany na potrzeby najbardziej złożonych aplikacji online bez wymuszania złamanie lub przemyślenia założenia kluczy, takich jak spójności współbieżności i dane, które pochodzą zostały przyznane deweloperów.  

Dla aplikacji kompletnego przykładu, do którego odwołuje się ten blog:  

* [Zarządzanie za pomocą usługi Azure Storage — Przykładowa aplikacja współbieżności](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Aby uzyskać więcej informacji o usłudze Azure Storage, zobacz:  

* [Strona główna magazynu usługi Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Wprowadzenie do usługi Azure Storage](storage-introduction.md)
* Wprowadzenie do magazynu [obiektu Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tabeli](../../cosmos-db/table-storage-how-to-use-dotnet.md), [kolejek](../storage-dotnet-how-to-use-queues.md), i [plików](../storage-dotnet-how-to-use-files.md)
* Architektura magazynu — [magazynu Azure: Usługa magazynu w chmurze wysokiej dostępności z wysoki poziom spójności](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

