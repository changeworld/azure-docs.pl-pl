---
title: Informacje o wersji dla bramy zarządzania danymi | Dokumentacja firmy Microsoft
description: Informacje o wersji tory zarządzania bramy danych
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: ac0e1945e75ee7aea346c103a671b4a47b9e5994
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048618"
---
# <a name="release-notes-for-data-management-gateway"></a>Informacje o wersji bramy zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [hosta samodzielnego środowiska uruchomieniowego integracji w wersji 2](../create-self-hosted-integration-runtime.md).

Jest jednym z wyzwań integracji nowoczesnych danych do przenoszenia danych do i z lokalnymi do chmury. Fabryka danych powoduje, że integracja z brama zarządzania danymi, czyli zainstalowanie lokalnego umożliwia przenoszenie danych hybrydowego agenta.

Zobacz następujące artykuły, aby uzyskać szczegółowe informacje na temat bramy zarządzania danymi i jak z niego korzystać:

*  [Brama zarządzania danymi](data-factory-data-management-gateway.md)
*  [Przenoszenie danych między lokalnymi i w chmurze przy użyciu fabryki danych Azure](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>BIEŻĄCA WERSJA 
Firma Microsoft nie ma więcej zachowuje informacje o wersji w tym miejscu. Pobierz informacje o najnowszej wersji [tutaj](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Starszych wersji
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Ulepszenia-
- Można dodać wpisy DNS do listy dozwolonych adresów IP usługi service bus, a nie listę dozwolonych podobnej wszystkie adresy IP platformy Azure z zapory (w razie potrzeby). Odpowiedni wpis DNS można znaleźć w portalu Azure (fabryka danych -> "Utwórz i wdróż" -> "Bramy" -> "serviceUrls" (w formacie JSON)
- System plików HDFS łącznik obsługuje teraz publicznego certyfikatu z podpisem własnym można pominąć weryfikacji protokołu SSL.
- Rozwiązany: Problem z bramą w trybie offline podczas aktualizacji (z powodu przesunięcia czasowego zegara)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Ulepszenia-
-   Można dodać wpisy DNS do listy dozwolonych adresów IP usługi Service Bus, a nie listę dozwolonych podobnej wszystkie adresy IP platformy Azure z zapory (w razie potrzeby). Więcej informacji w tym miejscu.
-   Możesz teraz skopiować dane z jednego bloku obiektu blob do 4,75 TB, czyli maksymalny obsługiwany rozmiar blokowych obiektów blob. (limit wcześniej była 195 GB).
-   Stałych: Za mało pamięci problem podczas rozpakować kilka małych plików podczas działania kopiowania.
-   Stały: Indeks jest poza zakresem problem podczas kopiowania z bazy danych dokumentów do lokalnego serwera SQL z funkcją idempotency.
-   Stały: Skrypt czyszczący SQL nie działa z lokalnym programem SQL Server z Kreatora kopiowania.
-   Stały: Nazwa kolumny z miejsca na końcu nie działa w przypadku działania kopiowania.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Ulepszenia-
- Rozwiązany: Problem z brakującymi poświadczeń na ponowny rozruch maszyny bramy.
- Stały: Problem z rejestracją podczas bramy przywracania przy użyciu pliku kopii zapasowej.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Ulepszenia-
- Stały: Niepoprawna odczytu dziesiętne wartości null z bazy danych Oracle jako źródło.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Co nowego
- Klienci mogą wyrazić swoją opinię na rejestrowanie obsługi bramy.
- Obsługuje nowy format kompresji: ZIP (Deflate)

### <a name="enhancements-"></a>Ulepszenia-
- Zwiększenie wydajności dla Oracle Sink źródłowy system plików HDFS.
- Poprawka błędu dla bramy automatycznie zaktualizować bramy wydajności przetwarzania równoległego.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Ulepszenia
- Ulepszone bardziej niezawodne i bramy rejestracji środowisko — teraz można śledzić postęp całej procedury podczas procesu rejestracji bramy, dzięki czemu usprawnia rejestracji wystąpić.
- Poprawy bramy przywrócić procesu — możesz można jednak nadal odzyskać bramy, nawet jeśli nie masz plik kopii zapasowej bramy z tej aktualizacji. To wymaga zresetowania poświadczenia połączonej usługi w portalu.
- Poprawka błędu.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Co nowego

- Teraz można przechowywać lokalnie poświadczenia źródła danych. Poświadczenia są szyfrowane. Poświadczenia źródła danych mogą zostać odzyskane i przywrócić przy użyciu kopii zapasowej pliku, który można eksportować z istniejącą bramę, wszystkie dostępne lokalnie.

### <a name="enhancements-"></a>Ulepszenia-

- Ulepszone i bardziej niezawodne środowisko rejestracji bramy.
- Obsługuje automatyczne wykrywanie QuoteChar Konfiguracja formatu tekstu w kreatorze kopiowania i zwiększa ogólną dokładność wykrywania formatu.

## <a name="2361002"></a>2.3.6100.2

- Obsługuje firstRowAsHeader i SkipLineCount automatyczne wykrywanie w kreatorze kopiowania plików tekstowych w lokalnym systemie plików i system plików HDFS.
- Zwiększenia stabilności połączenie sieciowe między bramą i usługi Service Bus
- Kilka poprawek błędów


## <a name="2260721"></a>2.2.6072.1

*  Obsługuje ustawienie serwera proxy HTTP dla bramy, używając Menedżera konfiguracji bramy. Jeśli skonfigurowana, obiektów Blob platformy Azure, tabel Azure, usługa Azure Data Lake i bazie danych dokumentów są dostępne za pośrednictwem serwera proxy HTTP.
*  Obsługuje nagłówek obsługi TextFormat podczas kopiowania danych z/do obiektów Blob platformy Azure, Azure Data Lake Store, lokalnego systemu plików i lokalnego systemu plików HDFS.
*  Obsługuje kopiowania danych z Dołącz obiektów Blob i stronicowych obiektów Blob oraz już obsługiwanych blokowych obiektów Blob.
*  Wprowadzono nowy stan bramy **Online (ograniczony)**, co oznacza, że główną funkcjonalność bramy działa z wyjątkiem operacji interakcyjne obsługę kreatora kopiowania.
*  Zwiększa niezawodność rejestracji bramy przy użyciu klucza rejestracji.

## <a name="216040"></a>2.1.6040.

*  Sterownik bazy danych DB2 znajduje się w pakiet instalacyjny bramy teraz. Nie ma potrzeby osobnego instalowania.
*  Sterownik bazy danych DB2 obsługuje teraz z/OS i bazy danych DB2 dla i (AS / 400) wraz z już obsługiwane platformy (systemu Linux, Unix i z systemem Windows).
*  Umożliwia używanie bazy danych rozwiązania Cosmos Azure jako źródła lub miejsca docelowego dla lokalnych magazynów danych
*  Kopiowanie danych z/na gorącą/chłodni obsługuje obiektu blob magazynu wraz z konta magazynu ogólnego przeznaczenia już obsługiwane.
*  Zezwala na połączenia z serwerem SQL lokalnej za pośrednictwem bramy z uprawnieniami logowania zdalnego.  

## <a name="2060131"></a>2.0.6013.1

*  Możesz wybrać języka/kultury ma być używany przez bramę podczas instalacji ręcznej.

*  Brama nie działa zgodnie z oczekiwaniami, możesz wysłać dzienniki bramy z ostatnich siedmiu dni do firmy Microsoft w celu ułatwienia rozwiązywania tego problemu. Jeśli brama nie jest połączona z usługą w chmurze, można do archiwum dzienniki bramy.  

*  Ulepszenia interfejsu użytkownika dla Menedżera konfiguracji bramy:

    *  Stan bramy należy bardziej widoczne na karcie Narzędzia główne.

    *  Formanty reorganizowane po kolei i uproszczone.

    *  Można skopiować danych z magazynu za pomocą [kopiowania kodu bez narzędzia](data-factory-copy-data-wizard-tutorial.md). Zobacz [przemieszczane kopiowania](data-factory-copy-activity-performance.md#staged-copy) szczegółowe informacje o tej funkcji w zasadzie.
*  Brama zarządzania danymi do danych wejściowych bezpośrednio z lokalną bazą danych programu SQL Server można użyć do usługi Azure Machine Learning.

*  Ulepszenia wydajności

    * Zwiększyć wydajność o wyświetlaniu schematu/Preview dla programu SQL Server w narzędziu Kopia niekorzystające z kodu.

## <a name="11259531"></a>1.12.5953.1

*  Poprawki błędów

## <a name="11159181"></a>1.11.5918.1

*  Maksymalny rozmiar w dzienniku zdarzeń bramy został zwiększony rozmiar od 1 MB do 40 MB.

*  Okno dialogowe ostrzeżenia jest wyświetlany w przypadku, gdy wymagane jest ponowne uruchomienie podczas automatyczną aktualizację bramy. Można ponownie uruchomić prawo następnie lub nowszym.

*  W przypadku, gdy aktualizacje automatyczne nie powiedzie się, Instalator bramy ponowi próbę automatycznego aktualizowania maksymalnie trzy razy w.

*  Ulepszenia wydajności

    * Zwiększenia wydajności w przypadku ładowania dużych tabel z lokalnego serwera w scenariuszu kopiowania niekorzystające z kodu.

*  Poprawki błędów

## <a name="11058921"></a>1.10.5892.1

*  Ulepszenia wydajności

*  Poprawki błędów

## <a name="1958652"></a>1.9.5865.2

*  Zero touch automatycznej aktualizacji możliwości
*  Nowa ikona na pasku zadań z wskaźniki stanu bramy
*  Możliwość "Teraz Update" od klienta
*  Możliwość określenia czasu harmonogram aktualizacji
*  Skrypt programu PowerShell dla automatycznej aktualizacji lub wyłącza przełączanie
*  Obsługa formatu JSON  
*  Ulepszenia wydajności
*  Poprawki błędów

## <a name="1858221"></a>1.8.5822.1

*  Udoskonalanie rozwiązywania problemów
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1757951"></a>1.7.5795.1

*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1757641"></a>1.7.5764.1

*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1657351"></a>1.6.5735.1

*  Obsługa lokalnego systemu plików HDFS źródło/ujście
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1656961"></a>1.6.5696.1

*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1656761"></a>1.6.5676.1

*  Narzędzia diagnostyczne pomocy technicznej programu Configuration Manager
*  Kolumny tabeli pomocy technicznej dla źródeł danych tabelarycznych dla fabryki danych Azure
*  Obsługa magazynu danych SQL dla fabryki danych Azure
*  Obsługa Reclusive BlobSource i FileSource fabryki danych Azure
*  Obsługa CopyBehavior — MergeFiles, PreserveHierarchy i FlattenHierarchy w BlobSink i FileSink kopią binarne dla fabryki danych Azure
*  Obsługuje działanie kopiowania raportowania postępu dla fabryki danych Azure
*  Walidacja łączności źródła danych pomocy technicznej dla fabryki danych Azure
*  Poprawki błędów

### <a name="1656721"></a>1.6.5672.1

*  Nazwa tabeli pomocy technicznej dla źródła danych ODBC dla fabryki danych Azure
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1656581"></a>1.6.5658.1

*  Plik pomocy technicznej Sink dla fabryki danych Azure
*  Obsługa zachowania hierarchii kopii binarne dla fabryki danych Azure
*  Obsługuje Idempotency działania kopiowania dla fabryki danych Azure
*  Poprawki błędów

### <a name="1656401"></a>1.6.5640.1

*  Obsługa 3 więcej źródeł danych dla fabryki danych Azure (ODBC, OData, system plików HDFS)
*  Obsługa znaku cudzysłowu w analizatorze składni csv fabryki danych Azure
*  Obsługa kompresji (BZip2)
*  Poprawki błędów

### <a name="1556121"></a>1.5.5612.1

*  Obsługuje pięć relacyjnych baz danych dla fabryki danych Azure (MySQL, PostgreSQL bazy danych DB2, Teradata i Sybase)
*  Obsługa kompresji (Gzip i Deflate)
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1455491"></a>1.4.5549.1

*  Dodaj obsługę źródła danych programu Oracle dla fabryki danych Azure
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1454921"></a>1.4.5492.1

*  Ujednolicone pliku binarnego, który obsługuje zarówno fabryki danych Microsoft Azure i usługi Office 365 Power BI
*  Dostosuj interfejs użytkownika konfiguracji i rejestracji procesu
*  Fabryka danych Azure — przychodzące i wychodzące Azure obsługuje dla źródła danych programu SQL Server

### <a name="1253031"></a>1.2.5303.1

*  Rozwiąż problem limitu czasu do obsługi więcej połączeń ze źródłem danych czasochłonna.

### <a name="1155268"></a>1.1.5526.8

*  Instalator wymaga programu .NET Framework 4.5.1 jako warunek wstępny.

### <a name="1051442"></a>1.0.5144.2

*  Brak zmian, które mają wpływ na scenariusze fabryki danych Azure.
