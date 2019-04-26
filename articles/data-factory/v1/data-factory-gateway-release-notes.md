---
title: Informacje o wersji bramy zarządzania danymi | Dokumentacja firmy Microsoft
description: Danych bramy zarządzania tory — informacje o wersji
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 865bfdae199bca7ebee888be527db239d34511d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60486468"
---
# <a name="release-notes-for-data-management-gateway"></a>Informacje o wersji bramy zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [może być samodzielnie hostowane środowisko IR w wersji 2](../create-self-hosted-integration-runtime.md).

Jednym z wyzwań integracji danych nowoczesnych jest przenoszenie danych do i ze środowiska lokalnego do chmury. Data Factory sprawia, że dzięki integracji z programem bramy zarządzania danymi, która jest zainstalowanie środowiska lokalnego do włączenia hybrydowe przenoszenie danych agenta.

Zobacz następujące artykuły, aby uzyskać szczegółowe informacje na temat bramy zarządzania danymi i jak z niego korzystać:

*  [Brama zarządzania danymi](data-factory-data-management-gateway.md)
*  [Przenoszenie danych lokalnie i w chmurze przy użyciu usługi Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>BIEŻĄCA WERSJA 
Firma Microsoft ma więcej zachowuje informacje o wersji w tym miejscu. Uzyskaj najnowsze informacje o wersji [tutaj](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Wcześniejszych wersjach
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Ulepszenia —
- Możesz dodać wpisy DNS do listy dozwolonych adresów usługi Service bus, a nie umieszczania na białej liście wszystkie adresy IP platformy Azure z zapory (jeśli jest to konieczne). Możesz znaleźć odpowiedniego wpisu DNS w witrynie Azure portal (Data Factory -> "Utwórz i wdróż" -> "Bram" -> "adresów serviceUrls" (w formacie JSON)
- Łącznik systemu plików HDFS obsługuje teraz publicznego certyfikatu z podpisem własnym, umożliwiając Pomiń sprawdzanie poprawności protokołu SSL.
- Poprawiono: Problem z bramą w trybie offline podczas aktualizacji (z powodu zegara)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Ulepszenia —
-   Możesz dodać wpisy DNS do listy dozwolonych, Service Bus, a nie umieszczania na białej liście wszystkie adresy IP platformy Azure z zapory (jeśli jest to konieczne). W tym miejscu więcej szczegółów.
-   Teraz możesz skopiować dane do/z bloku pojedynczego obiektu blob do 4.75 TB, który jest maksymalny obsługiwany rozmiar wynoszący blokowych obiektów blob. (wcześniej limit było 195 GB).
-   Poprawiono: Limit pamięci problem podczas rozpakowywania kilka małych plików podczas działania kopiowania.
-   Poprawiono: Indeks poza zakresem problem podczas kopiowania z bazy danych Documentdb do lokalnego programu SQL Server z funkcją idempotentności.
-   Poprawiono: Skrypt czyszczący SQL nie działa z programu SQL Server w środowisku lokalnym za pomocą Kreatora kopiowania.
-   Poprawiono: Nazwa kolumny ze spacją na końcu nie działa w działaniu kopiowania.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Ulepszenia —
- Poprawiono: Wydać z brakującymi poświadczeń na ponowny rozruch maszyny bramy.
- Poprawiono: Problem z rejestracją podczas bramy przywrócić dane przy użyciu pliku kopii zapasowej.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Ulepszenia —
- Poprawiono: Nieprawidłowy odczyt o wartości null wartość dziesiętną od firmy Oracle jako źródło.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Co nowego
- Klienci mogą przekazać opinię dotyczącą rejestrowania środowiska bramy.
- Obsługa nowego formatu kompresji: ZIP (Deflate)

### <a name="enhancements-"></a>Ulepszenia —
- Wzrost wydajności dla Oracle ujścia, źródłowy system plików HDFS.
- Poprawki dla bramy automatycznej aktualizacji, równoległe przetwarzanie pojemność bramy.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Ulepszenia
- Ulepszone i są bardziej niezawodne bramy rejestracji środowiska — teraz można śledzić stan postępu podczas procesu rejestracji bramy, co sprawia, że rejestracja środowisko zwiększyć szybkość reakcji.
- Poprawę bramy przywrócić procesu — możesz można jednak nadal odzyskać bramy, nawet jeśli nie masz bramy plik kopii zapasowej za pomocą tej aktualizacji. Będzie to wymagać zresetowania poświadczeń połączonej usługi w portalu.
- Naprawienie usterki.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Co nowego

- Teraz można przechowywać lokalnie poświadczenia źródła danych. Poświadczenia są szyfrowane. Poświadczenia źródła danych można odzyskać i przywrócić, przy użyciu pliku kopii zapasowej, który można eksportować z istniejącej bramy, a wszystkie lokalne.

### <a name="enhancements-"></a>Ulepszenia —

- Ulepszone i są bardziej niezawodne środowisko rejestracji bramy.
- Obsługa automatycznego wykrywania konfiguracji QuoteChar format tekstu w kreatorze kopiowania i zwiększania dokładności wykrywania ogólny format.

## <a name="2361002"></a>2.3.6100.2

- Obsługuje właściwości firstRowAsHeader oraz SkipLineCount automatyczne wykrywanie w kreatorze kopiowania dla plików tekstowych w systemie plików lokalnych i systemu plików HDFS.
- Ulepszenia stabilności połączenia sieciowego między bramą i Service Bus
- Kilka poprawek błędów


## <a name="2260721"></a>2.2.6072.1

*  Obsługuje ustawienie serwera proxy HTTP dla bramy przy użyciu Menedżera konfiguracji bramy. Jeśli skonfigurowano, obiektów Blob platformy Azure, Azure Table, usługa Azure Data Lake i baza danych Documentdb są dostępne za pośrednictwem serwera proxy HTTP.
*  Obsługuje nagłówek obsługi TextFormat podczas kopiowania danych z/do obiektów Blob platformy Azure, Azure Data Lake Store, System plików lokalnych i w środowisku lokalnym systemem plików HDFS.
*  Obsługuje kopiowanie danych z Uzupełnialnych obiektów Blob i stronicowych obiektów Blob oraz już obsługiwanych blokowych obiektów Blob.
*  Wprowadza nowy stan bramy **Online (ograniczone)**, co oznacza, że główne funkcje bramy działa z wyjątkiem obsługi operacji interaktywnych kreatora kopiowania.
*  Zwiększa niezawodność rejestracji bramy przy użyciu klucza rejestracji.

## <a name="216040"></a>2.1.6040.

*  Sterownik bazy danych DB2 znajduje się do pakietu instalacyjnego bramy teraz. Nie trzeba instalować jej oddzielnie.
*  Sterownik bazy danych DB2 obsługuje teraz z/OS i bazy danych DB2 for mam (AS / 400) wraz z już obsługuje platform (Linux, Unix lub Windows).
*  Obsługuje używanie usługi Azure Cosmos DB jako źródła lub miejsca docelowego dla magazynów danych w środowisku lokalnym
*  Obsługuje kopiowanie danych z/na zimno/gorąca obiektu blob magazynu wraz z konta magazynu ogólnego przeznaczenia już obsługiwane.
*  Zezwala na połączenia z serwerem SQL w środowisku lokalnym za pośrednictwem bramy przy użyciu uprawnień logowania zdalnego.  

## <a name="2060131"></a>2.0.6013.1

*  Można wybrać język/kulturę do użycia dla bramy podczas instalacji ręcznej.

*  Brama nie działa zgodnie z oczekiwaniami, możesz wysłać dzienniki bramy z ostatnich siedmiu dni do firmy Microsoft w celu ułatwienia rozwiązywania problemów tego problemu. Jeśli brama nie jest podłączony do usługi w chmurze, można zapisać i archiwizuj dzienniki bramy.  

*  Ulepszenia interfejsu użytkownika dla Menedżera konfiguracji bramy:

    *  Stan bramy należy zwiększyć wykrywalność na karcie Narzędzia główne.

    *  Formanty zreorganizowane i uproszczone.

    *  Możesz skopiować dane z magazynu przy użyciu [narzędzia do kopiowania niekorzystające z kodu](data-factory-copy-data-wizard-tutorial.md). Zobacz [kopiowania etapowego](data-factory-copy-activity-performance.md#staged-copy) Aby uzyskać szczegółowe informacje na ten temat ogólnie rzecz biorąc funkcji.
*  Możesz użyć bramy zarządzania danymi transferu danych przychodzących danych bezpośrednio z lokalną bazą danych programu SQL Server do usługi Azure Machine Learning.

*  Ulepszenia wydajności

    * Zwiększ wydajność na wyświetlanie schematu/Preview dla programu SQL Server w narzędzia do kopiowania niekorzystające z kodu.

## <a name="11259531"></a>1.12.5953.1

*  Poprawki błędów

## <a name="11159181"></a>1.11.5918.1

*  Maksymalny rozmiar dziennika zdarzeń bramy została zwiększona z 1 MB do 40 MB.

*  Okno dialogowe z ostrzeżeniem jest wyświetlany w przypadku, gdy ponowne uruchomienie jest wymagane podczas automatycznej aktualizacji bramy. Można uruchomić bezpośrednio, następnie lub później.

*  W przypadku, gdy aktualizacje automatyczne nie powiedzie się, Instalator bramy ponawia próbę automatycznego aktualizowania maksymalnie trzy razy w.

*  Ulepszenia wydajności

    * Zwiększ wydajność ładowania dużych tabel z lokalnego serwera w scenariuszu kopii niekorzystające z kodu.

*  Poprawki błędów

## <a name="11058921"></a>1.10.5892.1

*  Ulepszenia wydajności

*  Poprawki błędów

## <a name="1958652"></a>1.9.5865.2

*  Zero touch automatycznej aktualizacji funkcji
*  Nowa ikona na pasku zadań za pomocą wskaźniki stanu bramy
*  Możliwość "Aktualizuj teraz" od klienta
*  Możliwość ustawienia czasu harmonogram aktualizacji
*  Skrypt programu PowerShell do przełączania Autoaktualizacja wł. / wył.
*  Obsługa formatu JSON  
*  Ulepszenia wydajności
*  Poprawki błędów

## <a name="1858221"></a>1.8.5822.1

*  Ulepszenia dotyczące rozwiązywania problemów
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1757951"></a>1.7.5795.1

*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1757641"></a>1.7.5764.1

*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1657351"></a>1.6.5735.1

*  Obsługa środowiska lokalnego systemu plików HDFS źródło/ujście
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1656961"></a>1.6.5696.1

*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1656761"></a>1.6.5676.1

*  Narzędzia diagnostyczne pomocy technicznej programu Configuration Manager
*  Kolumny tabeli pomocy technicznej w przypadku tabelarycznych źródeł danych usługi Azure Data Factory
*  Obsługa SQL data Warehouse dla usługi Azure Data Factory
*  Obsługa Reclusive BlobSource i źródle danych Azure Data Factory
*  Obsługiwana CopyBehavior — MergeFiles PreserveHierarchy i FlattenHierarchy BlobSink oraz FileSink kopią binarne dla usługi Azure Data Factory
*  Działanie kopiowania, Raportowanie postępu dla usługi Azure Data Factory obsługuje
*  Sprawdzanie poprawności połączenia źródła danych pomocy technicznej dla usługi Azure Data Factory
*  Poprawki błędów

### <a name="1656721"></a>1.6.5672.1

*  Nazwa tabeli pomocy technicznej dla źródła danych ODBC usługi Azure Data Factory
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1656581"></a>1.6.5658.1

*  Plik pomocy technicznej ujścia usługi Azure Data Factory
*  Obsługa zachowania hierarchii w kopia binarna usługi Azure Data Factory
*  Obsługa idempotentności działania kopiowania z usługi Azure Data Factory
*  Poprawki błędów

### <a name="1656401"></a>1.6.5640.1

*  Obsługa 3 większą liczbą źródeł danych usługi Azure Data Factory (ODBC, OData, system plików HDFS)
*  Obsługa znaku cudzysłowu w analizator plików csv usługi Azure Data Factory
*  Obsługi kompresji (BZip2)
*  Poprawki błędów

### <a name="1556121"></a>1.5.5612.1

*  Obsługa pięciu relacyjnych baz danych usługi Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata i Sybase)
*  Obsługi kompresji (Gzip i Deflate)
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1455491"></a>1.4.5549.1

*  Dodaj obsługę źródeł danych programu Oracle dla usługi Azure Data Factory
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1454921"></a>1.4.5492.1

*  Ujednolicone plik binarny, który obsługuje usługi Microsoft Azure Data Factory i Office 365 w usłudze Power BI
*  Uściślij procesu interfejs użytkownika konfiguracji i rejestracji
*  Usługa Azure Data Factory — usługa Azure przychodzący i wychodzący pomocy technicznej dla źródła danych programu SQL Server

### <a name="1253031"></a>1.2.5303.1

*  Rozwiązać problem limitu czasu, aby obsługiwać więcej połączeń źródeł danych czasochłonne.

### <a name="1155268"></a>1.1.5526.8

*  Wymaga programu .NET Framework 4.5.1 jako warunek wstępny, podczas instalacji.

### <a name="1051442"></a>1.0.5144.2

*  Brak zmian, które wpływają na scenariusze usługi Azure Data Factory.
