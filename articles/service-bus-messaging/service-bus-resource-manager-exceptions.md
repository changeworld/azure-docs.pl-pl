---
title: Wyjątki usługi Azure Service Bus Resource Manager | Dokumenty firmy Microsoft
description: Lista wyjątków usługi Service Bus, które są wystawiane przez usługę Azure Resource Manager i sugerowane akcje.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: 0f328651ac4422226071d2de12e9cbc787ef64be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978271"
---
# <a name="service-bus-resource-manager-exceptions"></a>Wyjątki Menedżera zasobów usługi Service Bus

W tym artykule wymieniono wyjątki generowane podczas interakcji z usługą Azure Service Bus przy użyciu usługi Azure Resource Manager — za pośrednictwem szablonów lub bezpośrednich wywołań.

> [!IMPORTANT]
> Ten dokument jest często aktualizowany. Sprawdź aktualizacje.

Poniżej przedstawiono różne wyjątki/błędy, które są uwydrone za pośrednictwem usługi Azure Resource Manager.

## <a name="error-bad-request"></a>Błąd: Nieprawidłowe żądanie

"Złe żądanie" oznacza, że żądanie odebrane przez Menedżera zasobów nie powiodło się.

| Kod błędu | Podkod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nieprawidłowe żądanie | 40000 | SubCode=40000. Nie można ustawić właściwości *"nazwa właściwości"* podczas tworzenia kolejki, ponieważ *"nazwa obszaru nazw"* używa warstwy "Basic". Ta operacja jest obsługiwana tylko w warstwie "Standardowa" lub "Premium". | W warstwie podstawowej usługi Azure Service Bus nie można ustawić ani zaktualizować poniższych właściwości — <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>WymagaSesja</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> Włączexpress </li> <li> Do przodu </li> <li> Tematy </li> </ul> | Należy rozważyć uaktualnienie z warstwy Podstawowa do warstwy Premium lub Premium, aby korzystać z tej funkcji. |
| Nieprawidłowe żądanie | 40000 | SubCode=40000. Nie można zmienić wartości właściwości "requiresDuplicateDetection" istniejącej kolejki(lub tematu). | Wykrywanie duplikatów musi być włączone/wyłączone w momencie tworzenia jednostki. Po utworzeniu nie można zmienić parametru konfiguracji wykrywania duplikatów. | Aby włączyć wykrywanie duplikatów w poprzednio utworzonej kolejce/temacie, można utworzyć nową kolejkę/temat z wykrywaniem duplikatów, a następnie przejść dalej z oryginalnej kolejki do nowej kolejki/tematu. |
| Nieprawidłowe żądanie | 40000 | SubCode=40000. Określona wartość 16384 jest nieprawidłowa. Właściwość "MaxSizeInMegabytes", musi być jedną z następujących wartości: 1024;2048;3072;4096;5120. | Wartość MaxSizeInMegabytes jest nieprawidłowa. | Upewnij się, że MaxSizeInMegabytes jest jednym z następujących - 1024, 2048, 3072, 4096, 5120. |
| Nieprawidłowe żądanie | 40000 | SubCode=40000. Nie można zmienić partycjonowania dla kolejki/tematu. | Nie można zmienić partycjonowania dla jednostki. | Utwórz nową jednostkę (kolejkę lub temat) i włącz partycje. | 
| Nieprawidłowe żądanie | brak | Obszar nazw *"nazwa obszaru nazw"* nie istnieje. | Obszar nazw nie istnieje w ramach subskrypcji platformy Azure. | Aby rozwiązać ten błąd, zapoznaj się z poniższymi <ul> <li> Upewnij się, że subskrypcja platformy Azure jest poprawna. </li> <li> Upewnij się, że obszar nazw istnieje. </li> <li> Sprawdź, czy nazwa obszaru nazw jest poprawna (nie ma błędów pisowni ani ciągów zerowych). </li> </ul> | 
| Nieprawidłowe żądanie | 40400 | SubCode=40400. Encja docelowa automatycznego przekazywania nie istnieje. | Miejsce docelowe dla jednostki docelowej automatycznego dołchawania nie istnieje. | Jednostka docelowa (kolejka lub temat) musi istnieć przed utworzeniem źródła. Ponów próbę po utworzeniu jednostki docelowej. |
| Nieprawidłowe żądanie | 40000 | SubCode=40000. Podany czas blokady przekracza dopuszczalne maksimum "5" minut. | Czas, dla którego wiadomość może być zablokowana, musi wynosić od 1 minuty (minimum) do 5 minut (maksymalnie). | Upewnij się, że podany czas blokady wynosi od 1 min do 5 minut. |
| Nieprawidłowe żądanie | 40000 | SubCode=40000. Nie można włączyć właściwości DelayedPersistence i RequiresDuplicateDetection razem. | Jednostki z włączonym wykrywaniem duplikatów muszą być trwałe, więc trwałość nie może być opóźniona. | Dowiedz się więcej o [wykrywaniu duplikatów](duplicate-detection.md) |
| Nieprawidłowe żądanie | 40000 | SubCode=40000. Nie można zmienić wartości właściwości RequiresSession istniejącej kolejki. | Obsługa sesji powinna być włączona w momencie tworzenia encji. Po utworzeniu nie można włączać/wyłączać sesji w istniejącej jednostce (kolejce lub subskrypcji) | Usuń i ponownie utworzyć nową kolejkę (lub subskrypcję) z włączoną właściwością "RequiresSession". |
| Nieprawidłowe żądanie | 40000 | SubCode=40000. "URI_PATH" zawiera znaki, które nie są dozwolone przez usługę Service Bus. Segmenty encji mogą zawierać tylko litery, cyfry, kropki(.), łączniki(-) i podkreślenia(_). | Segmenty encji mogą zawierać tylko litery, cyfry, kropki(.), łączniki(-) i podkreślenia(_). Wszystkie inne znaki powodują niepowodzenie żądania. | Upewnij się, że w ścieżce identyfikatora URI nie ma żadnych nieprawidłowych znaków. |


## <a name="error-code-429"></a>Kod błędu: 429

Podobnie jak w HTTP, "Kod błędu 429" wskazuje "zbyt wiele żądań". Oznacza to, że określony zasób (obszar nazw) jest ograniczany z powodu zbyt wielu żądań (lub z powodu sprzecznych operacji) w tym zasobie.

| Kod błędu | Podkod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | SubCode=50004. Żądanie zostało zakończone, ponieważ obszar nazw *obszar nazw* jest ograniczany. | Ten warunek błędu jest trafiony, gdy liczba przychodzących żądań przekracza ograniczenie zasobu. | Odczekaj kilka sekund i spróbuj ponownie. <br/> <br/> Dowiedz się więcej o [przydziałach](service-bus-quotas.md) i [limitach żądań usługi Azure Resource Manager](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | SubCode=40901. Trwa kolejna operacja powodująca konflikt. | Kolejna operacja powodująca konflikt jest w toku dla tego samego zasobu/encji | Poczekaj na zakończenie bieżącej operacji w toku przed ponowną próbą. |
| 429 | 40900 | SubCode=40900. Konflikt. Żądasz operacji, która nie jest dozwolona w bieżącym stanie zasobu. | Ten warunek może zostać trafiony, gdy wiele żądań są wykonywane do wykonywania operacji na tej samej jednostce (kolejki, tematu, subskrypcji lub reguły) w tym samym czasie. | Odczekaj kilka sekund i spróbuj ponownie |
| 429 | 40901 | Żądanie *dotyczące "nazwy jednostki"* w konflikcie z innym żądaniem | Kolejna operacja powodująca konflikt jest w toku dla tego samego zasobu/encji | Poczekaj na zakończenie poprzedniej operacji przed ponowną próbą |
| 429 | 40901 | Trwa kolejne żądanie aktualizacji dla *"nazwy jednostki"* jednostki . | Kolejna operacja powodująca konflikt jest w toku dla tego samego zasobu/encji | Poczekaj na zakończenie poprzedniej operacji przed ponowną próbą |
| 429 | brak | Wystąpił konflikt zasobów. Inna operacja powodująca konflikt może być w toku. Jeśli jest to ponowna próba dla operacji nie powiodło się, oczyszczanie w tle jest nadal w toku. Spróbuj ponownie później. | Ten warunek może zostać trafiony, gdy istnieje oczekująca operacja dla tej samej jednostki. | Poczekaj na zakończenie poprzedniej operacji przed ponowną próbą. |


## <a name="error-code-not-found"></a>Kod błędu: Nie znaleziono

Ta klasa błędów wskazuje, że zasób nie został znaleziony.

| Kod błędu | Podkod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nie znaleziono | brak | Nie znaleziono *"nazwy jednostki".* | Jednostka, przeciwko której operacja nie została znaleziona. | Sprawdź, czy jednostka istnieje i spróbuj ponownie wykonać operację. |
| Nie znaleziono | brak | Nie znaleziono. Operacja nie istnieje. | Operacja, którą próbujesz wykonać, nie istnieje. | Sprawdź operację i spróbuj ponownie. |
| Nie znaleziono | brak | Żądanie przychodzące nie jest rozpoznawane jako żądanie umieszczenia zasad obszaru nazw. | Treść żądania przychodzącego jest null i dlatego nie można wykonać jako put request. | Sprawdź treść żądania, aby upewnić się, że nie jest null. | 
| Nie znaleziono | brak | Nie można odnaleźć *"nazwy jednostki"* jednostki obsługi wiadomości. | Nie można odnaleźć jednostki, dla której próbujesz wykonać operację. | Sprawdź, czy jednostka istnieje i spróbuj ponownie wykonać operację. |

## <a name="error-code-internal-server-error"></a>Kod błędu: Wewnętrzny błąd serwera

Ta klasa błędów wskazuje, że wystąpił wewnętrzny błąd serwera

| Kod błędu | Podkod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Błąd serwera wewnętrznego | 50000 | SubCode=50000. Błąd serwera wewnętrznego| Może się zdarzyć z różnych powodów. Niektóre z objawów są - <ul> <li> Żądanie klienta/treść jest uszkodzony i prowadzi do błędu. </li> <li> Limit czasu żądania klienta z powodu problemów z przetwarzaniem usługi. </li> </ul> | Aby rozwiązać ten problem <ul> <li> Upewnij się, że parametry żądań nie są null lub nieprawidłowo sformułowane. </li> <li> Ponów próbę żądania. </li> </ul> |

## <a name="error-code-unauthorized"></a>Kod błędu: Nieautoryzowane

Ta klasa błędów wskazuje na brak autoryzacji do uruchomienia polecenia.

| Kod błędu | Podkod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Brak autoryzacji | brak | Nieprawidłowa operacja w pomocniczej przestrzeni nazw. Pomocnicza przestrzeń nazw jest tylko do odczytu. | Operacja została wykonana względem pomocniczego obszaru nazw, który jest skonfigurowany jako obszar nazw tylko do odczytu. | Ponów próbę wykonania polecenia względem podstawowego obszaru nazw. Dowiedz się więcej o [pomocniczej przestrzeni nazw](service-bus-geo-dr.md) |
| Brak autoryzacji | brak | MissingToken: Nie znaleziono nagłówka autoryzacji. | Ten błąd występuje, gdy autoryzacja ma wartości null lub niepoprawne. | Upewnij się, że wartość tokenu wymieniona w nagłówku autoryzacji jest poprawna, a nie null. |