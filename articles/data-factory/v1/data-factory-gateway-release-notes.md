---
title: Informacje o wersji bramy zarządzania danymi
description: Informacje o wersji bramy zarządzania danymi
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 838e523f74a21c44958ddb6dc88e4dab3526d81a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064992"
---
# <a name="release-notes-for-data-management-gateway"></a>Informacje o wersji bramy zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [środowisko uruchomieniowe integracji hostowanego samodzielnie w wersji 2](../create-self-hosted-integration-runtime.md).

Jednym z wyzwań dla nowoczesnej integracji danych jest przenoszenie danych do i z lokalnego do chmury. Fabryka danych sprawia, że ta integracja z bramą zarządzania danymi, która jest agentem, który można zainstalować lokalnie, aby umożliwić hybrydowy przenoszenie danych.

Zobacz następujące artykuły, aby uzyskać szczegółowe informacje na temat bramy zarządzania danymi i sposobu jej używania:

*  [Brama zarządzania danymi](data-factory-data-management-gateway.md)
*  [Przenoszenie danych między środowisku lokalnym a chmurą przy użyciu usługi Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUALNA WERSJA 
Nie będziemy już w tym miejscu utrzymywać informacji o wersji. Tutaj znajdziesz najnowsze [informacje](https://go.microsoft.com/fwlink/?linkid=853077) o wersji




## <a name="earlier-versions"></a>Wcześniejsze wersje
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Ulepszenia-
- Wpisy DNS można dodać do magistrali usługi whitelist zamiast umieszczać na białej liście wszystkie adresy IP platformy Azure z zapory (w razie potrzeby). Odpowiedni wpis DNS można znaleźć w witrynie Azure portal (Data Factory -> 'Author and Deploy' -> 'Gateways' -> "serviceUrls" (w JSON)
- Łącznik HDFS obsługuje teraz samodzielnie podpisany certyfikat publiczny, umożliwiając pominięcie sprawdzania poprawności TLS.
- Naprawiono: Problem z bramą w trybie offline podczas aktualizacji (z powodu pochylenia zegara)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Ulepszenia-
-   Wpisy DNS można dodać do białej listy usługi Service Bus, a nie do białej listy wszystkich adresów IP platformy Azure z zapory (w razie potrzeby). Więcej szczegółów można znaleźć tutaj.
-   Teraz można skopiować dane do/z pojedynczego bloku obiektu blob o pojemności do 4,75 TB, który jest maksymalnym obsługiwanym rozmiarem bloku blob. (wcześniejszy limit wynosił 195 GB).
-   Naprawiono: Problem z braku pamięci podczas rozpakowywania kilku małych plików podczas aktywności kopiowania.
-   Naprawiono: Indeksowanie problemu poza zakresem podczas kopiowania z bazy danych dokumentu do lokalnego programu SQL Server z funkcją idempotency.
-   Naprawiono: Skrypt oczyszczania SQL nie działa z lokalnym programem SQL Server z Kreatora kopiowania.
-   Poprawiono: Nazwa kolumny z spacją na końcu nie działa w działaniu kopiowania.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Ulepszenia-
- Naprawiono: Problem z brakującymi poświadczeniami podczas ponownego uruchamiania komputera bramy.
- Naprawiono: Problem z rejestracją podczas przywracania bramy przy użyciu pliku kopii zapasowej.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Ulepszenia-
- Poprawiono: Niepoprawny odczyt dziesiętnej wartości null z Oracle jako źródła.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Co nowego
- Klienci mogą przekazywać opinie na temat rejestrowania bramy.
- Obsługa nowego formatu kompresji: ZIP (Deflate)

### <a name="enhancements-"></a>Ulepszenia-
- Poprawa wydajności oracle sink, źródła HDFS.
- Poprawka błędu dla automatycznej aktualizacji bramy, pojemności przetwarzania równoległego bramy.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Ulepszenia 
- Ulepszone i bardziej niezawodne środowisko rejestracji bramy — teraz możesz śledzić stan postępu podczas procesu rejestracji bramy, co sprawia, że rejestracja jest bardziej responsywna.
- Usprawnienie procesu przywracania bramy — nadal można odzyskać bramę, nawet jeśli nie masz pliku kopii zapasowej bramy z tą aktualizacją. Wymagałoby to zresetowania poświadczeń usługi połączonej w portalu.
- Poprawka.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Co nowego

- Teraz można przechowywać poświadczenia źródła danych lokalnie. Poświadczenia są szyfrowane. Poświadczenia źródła danych można odzyskać i przywrócić przy użyciu pliku kopii zapasowej, który można wyeksportować z istniejącej bramy, wszystkie lokalne.

### <a name="enhancements-"></a>Ulepszenia-

- Ulepszone i bardziej niezawodne środowisko rejestracji bramy.
- Obsługa automatycznego wykrywania konfiguracji QuoteChar dla formatu tekstu w kreatorze kopiowania i popraw ogólną dokładność wykrywania formatu.

## <a name="2361002"></a>2.3.6100.2

- Obsługa automatycznego wykrywania firstRowAsHeader i SkipLineCount w kreatorze kopiowania plików tekstowych w lokalnym systemie plików i systemie HDFS.
- Zwiększenie stabilności połączenia sieciowego między bramą a usługą Service Bus
- Kilka poprawek błędów


## <a name="2260721"></a>2.2.6072.1

*  Obsługuje ustawianie serwera proxy HTTP dla bramy przy użyciu menedżera konfiguracji bramy. Jeśli jest skonfigurowany, azure blob, tabela platformy Azure, usługa Azure Data Lake i bazy danych dokumentów są dostępne za pośrednictwem serwera proxy HTTP.
*  Obsługuje obsługę nagłówka dla TextFormat podczas kopiowania danych z/do obiektu Blob platformy Azure, usługi Azure Data Lake Store, lokalnego systemu plików i lokalnego systemu plików HDFS.
*  Obsługuje kopiowanie danych z dołączania obiektów blob i page blob wraz z już obsługiwane Bloku obiektu blob.
*  Wprowadza nowy stan bramy **online (Limited),** który wskazuje, że główne funkcje bramy działa z wyjątkiem interaktywnej obsługi operacji dla Kreatora kopiowania.
*  Zwiększa niezawodność rejestracji bramy przy użyciu klucza rejestracji.

## <a name="216040"></a>2.1.6040.

*  Sterownik DB2 jest teraz dołączony do pakietu instalacyjnego bramy. Nie trzeba instalować jej oddzielnie.
*  Sterownik DB2 obsługuje teraz z/OS i DB2 dla i (AS/400) wraz z platformami już obsługiwanymi (Linux, Unix i Windows).
*  Obsługa korzystania z usługi Azure Cosmos DB jako źródła lub miejsca docelowego lokalnych magazynów danych
*  Obsługuje kopiowanie danych z/do magazynu obiektów blob na zimno/gorąco wraz z już obsługiwanym kontem magazynu ogólnego przeznaczenia.
*  Umożliwia łączenie się z lokalnym programem SQL Server za pośrednictwem bramy z uprawnieniami do zdalnego logowania.  

## <a name="2060131"></a>2.0.6013.1

*  Można wybrać język/kulturę, która ma być używana przez bramę podczas instalacji ręcznej.

*  Gdy brama nie działa zgodnie z oczekiwaniami, można wysłać dzienniki bramy z ostatnich siedmiu dni do firmy Microsoft, aby ułatwić rozwiązywanie problemu. Jeśli brama nie jest połączona z usługą w chmurze, można zapisać i zarchiwizować dzienniki bramy.  

*  Ulepszenia interfejsu użytkownika dla menedżera konfiguracji bramy:

    *  Spraw, aby stan bramy był bardziej widoczny na karcie Narzędzia główne.

    *  Zreorganizowane i uproszczone kontrole.

    *  Dane z magazynu można kopiować za pomocą [narzędzia do kopiowania bez kodu](data-factory-copy-data-wizard-tutorial.md). Zobacz [Kopia etapowa, aby](data-factory-copy-activity-performance.md#staged-copy) uzyskać szczegółowe informacje na temat tej funkcji w ogóle.
*  Bramy zarządzania danymi można używać do wprowadzania danych bezpośrednio z lokalnej bazy danych programu SQL Server do usługi Azure Machine Learning.

*  Ulepszenia wydajności

    * Zwiększ wydajność podczas wyświetlania schematu/podglądu programu SQL Server w narzędziu do kopiowania bez kodu.

## <a name="11259531"></a>1.12.5953.1

*  Poprawki błędów

## <a name="11159181"></a>1.11.5918.1

*  Maksymalny rozmiar dziennika zdarzeń bramy został zwiększony z 1 MB do 40 MB.

*  Okno dialogowe z ostrzeżeniem jest wyświetlane w przypadku, gdy podczas automatycznej aktualizacji bramy jest potrzebne ponowne uruchomienie. Można wybrać opcję ponownego uruchomienia bezpośrednio wtedy lub później.

*  W przypadku niepowodzenia automatycznej aktualizacji instalator bramy ponawia ponowną automatyczną aktualizację trzy razy przy maksymalnej wartości.

*  Ulepszenia wydajności

    * Zwiększ wydajność podczas ładowania dużych tabel z serwera lokalnego w scenariuszu kopiowania bez kodu.

*  Poprawki błędów

## <a name="11058921"></a>1.10.5892.1

*  Ulepszenia wydajności

*  Poprawki błędów

## <a name="1958652"></a>1.9.5865.2

*  Możliwość automatycznej aktualizacji bez dotykowia
*  Nowa ikona zasobnika ze wskaźnikami stanu bramy
*  Możliwość "Aktualizacja teraz" od klienta
*  Możliwość ustawiania czasu harmonogramu aktualizacji
*  Skrypt programu PowerShell do przełączania automatycznej aktualizacji włączania/wyłączania
*  Obsługa formatu JSON  
*  Ulepszenia wydajności
*  Poprawki błędów

## <a name="1858221"></a>1.8.5822.1

*  Usprawnij rozwiązywanie problemów
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1757951"></a>1.7.5795.1

*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1757641"></a>1.7.5764.1

*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1657351"></a>1.6.5735.1

*  Obsługa lokalnego źródła/ujścia hdfs
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1656961"></a>1.6.5696.1

*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1656761"></a>1.6.5676.1

*  Obsługa narzędzi diagnostycznych w programie Configuration Manager
*  Kolumny tabel obsługi tabeli tabelaryczne dla usługi Azure Data Factory
*  Obsługa usługi SQL DW dla usługi Azure Data Factory
*  Obsługa funkcji Reclusive w usługach BlobSource i FileSource for Azure Data Factory
*  Obsługa copyBehavior — MergeFiles, PreserveHierarchy i FlattenHierarchy in BlobSink and FileSink with Binary Copy for Azure Data Factory
*  Postęp raportowania aktywności kopiowania pomocy technicznej dla usługi Azure Data Factory
*  Sprawdzanie poprawności łączności źródła danych dla usługi Azure Data Factory
*  Poprawki błędów

### <a name="1656721"></a>1.6.5672.1

*  Nazwa tabeli obsługi źródła danych ODBC dla usługi Azure Data Factory
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1656581"></a>1.6.5658.1

*  Ujście plików pomocy technicznej dla usługi Azure Data Factory
*  Obsługa zachowania hierarchii w kopii binarnej dla usługi Azure Data Factory
*  Identyfikator działania obsługi kopii dla usługi Azure Data Factory
*  Poprawki błędów

### <a name="1656401"></a>1.6.5640.1

*  Obsługa 3 kolejnych źródeł danych dla usługi Azure Data Factory (ODBC, OData, HDFS)
*  Znak cudzysłowu obsługi w analizatorze csv dla usługi Azure Data Factory
*  Obsługa kompresji (BZip2)
*  Poprawki błędów

### <a name="1556121"></a>1.5.5612.1

*  Obsługa pięciu relacyjnych baz danych dla usługi Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata i Sybase)
*  Obsługa kompresji (Gzip i Deflate)
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1455491"></a>1.4.5549.1

*  Dodaj obsługę źródła danych Oracle dla usługi Azure Data Factory
*  Ulepszenia wydajności
*  Poprawki błędów

### <a name="1454921"></a>1.4.5492.1

*  Ujednolicony plik binarny obsługujący zarówno usługi Microsoft Azure Data Factory, jak i usługi Office 365 Power BI
*  Uściślanie interfejsu użytkownika konfiguracji i procesu rejestracji
*  Usługa Azure Data Factory — obsługa transferu danych przychodzących i wychodzących platformy Azure dla źródła danych programu SQL Server

### <a name="1253031"></a>1.2.5303.1

*  Rozwiąż problem z limitem czasu, aby obsługiwać bardziej czasochłonne połączenia ze źródłem danych.

### <a name="1155268"></a>1.1.5526.8

*  Wymaga programu .NET Framework 4.5.1 jako warunek wstępny podczas instalacji.

### <a name="1051442"></a>1.0.5144.2

*  Brak zmian, które wpływają na scenariusze usługi Azure Data Factory.
