---
title: Kody błędów interfejsu API REST — Azure Machine Learning Studio (klasyczny) | Microsoft Docs
description: Te kody błędów mogą być zwracane przez operację usługi sieci web Azure Machine Learning.
keywords: ''
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 9e25f2fbc10eb07cc71f2a7bd34247c0191f61b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217771"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klasyczny) kody błędów interfejsu API REST

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
Operacje w usłudze sieci Web Azure Machine Learning Studio (klasycznej) mogą zwrócić następujące kody błędów.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (kod stanu HTTP 400)
 
Podano nieprawidłowy argument.
 
Ta klasa błędy oznacza, że gdzieś podany argument był nieprawidłowy. Może to być poświadczenia lub lokalizacji magazynu platformy Azure na coś przekazany do usługi sieci web. Zapoznaj się z błędem "kod" w sekcji "Szczegóły", aby zdiagnozować, który konkretny argument był nieprawidłowy.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| BadParameterValue | Podana wartość parametru nie spełnia warunków reguły parametru w parametrze |
| BadSubscriptionId | Identyfikator służący do generowania wyników na subskrypcji nie jest obecny w zasobach |
| BadVersionCall | Podczas wywołania interfejsu API przekazano nieprawidłowy parametr wersji: {0}. Strona pomocy interfejsu API do przekazywania poprawnej wersji i spróbuj ponownie. |
| BatchJobInputsNotSpecified | Następujące wymagane dane wejściowe nie zostały określone przy użyciu żądania: {0}. Upewnij się, że wszystkie dane wejściowe jest określony i spróbuj ponownie. |
| BatchJobInputsTooManySpecified | Żądanie określone więcej danych wejściowych niż zdefiniowane w usłudze. Lista zaakceptowanych danych wejściowych (s): {0}. Upewnij się, że wszystkie dane wejściowe jest prawidłowa i spróbuj ponownie. |
| BlobNameTooLong | Ścieżka magazynu obiektów blob platformy Azure podana dla danych wyjściowych diagnostyki jest zbyt długa: {0}. Skróć ścieżkę i spróbuj ponownie. |
| BlobNotFound | Nie można uzyskać dostępu do podanego obiektu blob platformy Azure — {0}.  Komunikat o błędzie platformy Azure: {1}. |
| ContainerIsEmpty | Brak nazwy kontenera usługi Azure storage została dostarczona. Podaj nazwę prawidłowego kontenera, a następnie spróbuj ponownie. |
| ContainerSegmentInvalid | Nieprawidłowej nazwy kontenera. Podaj nazwę prawidłowego kontenera, a następnie spróbuj ponownie. |
| ContainerValidationFailed | Sprawdzanie poprawności kontenera obiektów BLOB nie powiodło się z powodu błędu: {0}. |
| DataTypeNotSupported | Nieobsługiwany typ danych pod warunkiem. Podaj prawidłowe dane typy, a następnie spróbuj ponownie. |
| DuplicateInputInBatchCall | Żądanie wsadowe jest nieprawidłowe. Nie można określić zarówno pojedynczych i wielu danych wejściowych w tym samym czasie. Usuń jeden z tych elementów z żądania i spróbuj ponownie. |
| ExpiryTimeInThePast | Czas wygaśnięcia podany w przeszłości: {0}. Podaj czas wygaśnięcia w przyszłości, w formacie UTC, a następnie spróbuj ponownie. Nigdy nie wygasa, ustaw czas wygaśnięcia na wartość NULL. |
| IncompleteSettings | Ustawienia diagnostyczne są niekompletne. |
| InputBlobRelativeLocationInvalid | Nie podano nazwy obiektu blob magazynu platformy Azure. Podaj nazwę prawidłowy obiekt blob, a następnie spróbuj ponownie. |
| InvalidBlob | Nieprawidłowa specyfikacja obiektu BLOB dla obiektu BLOB: {0}. Sprawdź te parametry połączenia / ścieżkę względną lub specyfikacji tokenu sygnatury dostępu Współdzielonego jest poprawny i spróbuj ponownie. |
| InvalidBlobConnectionString | Parametry połączenia określone dla jednego z danych wejściowych/wyjściowych obiektów BLOB są nieprawidłowe: {0}. Rozwiązać ten problem i spróbuj ponownie. |
| InvalidBlobExtension | Odwołanie do obiektu BLOB: {0} zawiera nieprawidłowe lub brak rozszerzenia pliku. Obsługiwane rozszerzenia plików dla tego typu danych wyjściowych to: "{1}". |
| InvalidInputNames | W żądaniu określono nieprawidłowe nazwy wejściowe usługi: {0}. Mapowanie danych wejściowych do usługi poprawne dane wejściowe i spróbuj ponownie. |
| InvalidOutputOverrideName | Nieprawidłowa nazwa przesłonięcia danych wyjściowych: {0}. Usługa nie ma węzła danych wyjściowych o tej nazwie. Przekaż nazwę węzła poprawne dane wyjściowe do zastąpienia (dotyczy rozróżnianie wielkości liter). |
| InvalidQueryParameter | Nieprawidłowy parametr zapytania "{0}". {1} |
| MissingInputBlobInformation | Brak informacji o obiekcie blob magazynu platformy Azure. Podaj prawidłowe parametry połączenia i ścieżką względną lub identyfikator URI i spróbuj ponownie. |
| MissingJobId | Brak podany identyfikator zadania. Zadanie identyfikator jest zwracany, jeśli zadanie zostało przesłane po raz pierwszy. Sprawdź, czy identyfikatora zadania jest poprawny i spróbuj ponownie. |
| MissingKeys | Brak kluczy podane lub jednego z podstawowej maszyny wirtualnej lub klucz pomocniczy jest niedostępny. |
| MissingModelPackage | Nie identyfikator pakietu modelu lub model pakietów. Podaj identyfikator pakietu prawidłowego modelu lub modelu pakietu i spróbuj ponownie. |
| MissingOutputOverrideSpecification | W żądaniu brakuje specyfikacji obiektu BLOB dla zastąpienia danych wyjściowych {0}. Określ lokalizację prawidłowy obiekt blob z żądaniem lub usuń specyfikację dane wyjściowe, jeśli żadne przesłonięcie lokalizacji. |
| MissingRequestInput | Usługa sieci web oczekuje danych wejściowych, ale nie dostarczono żadnych dane wejściowe. Upewnij się, że podano prawidłowe dane wejściowe na podstawie opublikowanych portów wejściowych w modelu, a następnie spróbuj ponownie. |
| MissingRequiredGlobalParameters | Nie wszystkie wymagane parametry usługi sieci web, pod warunkiem. Sprawdź, czy parametry oczekiwanego przez moduły są poprawne i spróbuj ponownie. |
| MissingRequiredOutputOverrides | Podczas wywoływania punktu końcowego szyfrowanie usług, które jest wymagane, aby przekazać dane wyjściowe wartości zastąpień dla wszystkich usług w danych wyjściowych. W tej chwili brak zastąpień dla następujących danych wyjściowych: {0} |
| MissingWebServiceGroupId | Podany identyfikator żadna grupa usługi sieci web. Podaj identyfikator grupy usługi web prawidłową i spróbuj ponownie. |
| MissingWebServiceId | Podany identyfikator żadnej usługi sieci web. Usługi sieci web prawidłowy identyfikator i spróbuj ponownie. |
| MissingWebServicePackage | Nie podano pakiet usługi sieci web. Podaj pakiet usługi sieci web prawidłową i spróbuj ponownie. |
| MissingWorkspaceId | Podany identyfikator żadnego obszaru roboczego. Podaj prawidłowy identyfikator obszaru roboczego, a następnie spróbuj ponownie. |
| ModelConfigurationInvalid | Nieprawidłowy model konfiguracji w pakiecie modelu. Upewnij się, Konfiguracja modelu zawiera dane wyjściowe punkty końcowe: definicji punktu końcowego błędów standardowe, i standardowe out punktu końcowego i spróbuj ponownie. |
| ModelPackageIdInvalid | Nieprawidłowy identyfikator pakietu modelu. Sprawdź, czy identyfikator pakietu modelu jest poprawny, i spróbuj ponownie. |
| RequestBodyInvalid | Nie treści żądania, pod warunkiem lub wystąpił błąd podczas deserializacji treści żądania. |
| RequestIsEmpty | Żądanie nie podano. Podaj prawidłowy żądania i spróbuj ponownie. |
| UnexpectedParameter | Podano nieoczekiwany parametrów. Sprawdź wszystkie nazwy parametrów jest poprawna, tylko oczekiwanych parametrów są przekazywane, a następnie spróbuj ponownie. |
| Nieznany błąd zostanie | Nieznany błąd. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Nie można zmienić wymagań współbieżnych żądań dla usługi sieci Web {0}. |
| WebServiceIdInvalid | Podany identyfikator usługi sieci web nieprawidłowy. Identyfikator usługi sieci Web musi być prawidłowym identyfikatorem guid. |
| WebServiceTooManyConcurrentRequestRequirement | Nie można ustawić współbieżnego wymagania żądania na więcej niż {0}. |
| WebServiceTypeInvalid | Nieprawidłowy typ sieci web usługi udostępniane. Sprawdź, czy typ usługi sieci web prawidłowe jest poprawny i spróbuj ponownie. Prawidłowe typy usługi sieci Web: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (kod stanu HTTP 400)
 
Podany argument nieprawidłowego użytkownika.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| InputMismatchError | Dane wejściowe jest niezgodna z portem wejściowym schematu. |
| InputParseError | Analizowanie danych wejściowych wektora nie powiodło się.  Sprawdź, czy wektor wejściowy ma poprawną liczbę kolumn i typy danych.  Dodatkowe szczegóły: {0}. |
| MissingRequiredGlobalParameters | Brak parametrów oczekiwaną przez usługę sieci web. Sprawdź, czy wszystkie wymagane parametry oczekiwaną przez usługę sieci web są prawidłowe i spróbuj ponownie. |
| UnexpectedParameter | Sprawdź tylko wymagane parametry oczekiwaną przez usługę sieci web są przekazywane i spróbuj ponownie. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>Invalidoperation powodujący zakończenie działania (kod stanu HTTP 400)
 
Żądanie jest nieprawidłowy w bieżącym kontekście.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| CannotStartJob | Nie można uruchomić zadania, ponieważ jest ono w stanie {0}. |
| IncompatibleModel | Model jest niezgodna z wersją żądania. Wersja żądania obsługuje tylko modele danych wyjściowych jednego elementu datatable. |
| MultipleInputsNotAllowed | Model nie zezwala na wielu danych wejściowych. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (kod stanu HTTP 400)
 
Wykonanie modułu wystąpił błąd wewnętrznej biblioteki.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (kod stanu HTTP 400)
 
Operacja uruchamiania modułu wystąpił błąd.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (kod stanu HTTP 400)
 
Pakiet usługi sieci web nieprawidłowy. Sprawdź poprawność pakietu usługi sieci web, pod warunkiem i spróbuj ponownie.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| FormatError | Pakiet usługi sieci web jest źle sformułowany. Szczegóły: {0} |
| RuntimesError | Wykres pakietów usługi sieci web jest nieprawidłowy. Szczegóły: {0} |
| ValidationError | Wykres pakietów usługi sieci web jest nieprawidłowy. Szczegóły: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Nieautoryzowany (kod stanu HTTP 401)
 
Żądanie nie ma autoryzacji do dostępu do zasobu.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| AdminRequestUnauthorized | Brak autoryzacji |
| ManagementRequestUnauthorized | Brak autoryzacji |
| ScoreRequestUnauthorized | Podano nieprawidłowe poświadczenia. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (kod stanu HTTP 404)
 
Nie znaleziono zasobu.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| ModelPackageNotFound | Nie odnaleziono pakietu modelu. Upewnij się, że identyfikator pakietu modelu jest poprawny i spróbuj ponownie. |
| WebServiceIdNotFoundInWorkspace | Usługa sieci Web w obszarze nie znaleziono obszaru roboczego. Występuje niezgodność między webServiceId i identyfikator obszaru roboczego. Sprawdź usługę sieci web, pod warunkiem jest częścią obszaru roboczego i spróbuj ponownie. |
| WebServiceNotFound | Nie można odnaleźć usługi sieci Web. Sprawdź, czy identyfikator usługi sieci web jest poprawny i spróbuj ponownie. |
| WorkspaceNotFound | Nie znaleziono obszaru roboczego. Sprawdź, czy identyfikator obszaru roboczego jest prawidłowy i spróbuj ponownie. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (kod stanu HTTP 408)
 
Nie można zakończyć operacji w dozwolonym czasie.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| RequestCanceled | Żądanie zostało anulowane przez klienta. |
| ScoreRequestTimeout | Upłynął limit czasu wykonywania żądania. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (kod stanu HTTP 409)
 
Zasób już istnieje.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Nazwa parametru nieprawidłowe dane wyjściowe. Spróbuj użyć modułu Edytor metadanych zmiana nazw kolumn i spróbuj ponownie. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (kod stanu HTTP 413)
 
Model przekroczył limit przydziału pamięci do niej przypisany.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| OutOfMemoryLimit | Model używany więcej pamięci niż była przeznaczona dla niego. Maksymalna dozwolona ilość pamięci dla modelu wynosi {0} MB. Sprawdź, czy model problemów. |
 
## <a name="internalerror-http-status-code-500"></a>Błąd wewnętrzny (kod stanu HTTP 500)
 
Wykonanie napotkał błąd wewnętrzny.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Proces kontenera, który uległ awarii z powodu błędu systemu |
| ContainerProcessTerminatedWithUnknownError | Proces kontenera, który uległ awarii z powodu nieznanego błędu |
| ContainerValidationFailed | Sprawdzanie poprawności kontenera obiektów BLOB nie powiodło się z powodu błędu: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nie podano argumentów. Sprawdź, czy prawidłowe argumenty są przekazywane, a następnie spróbuj ponownie. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Identyfikator portu ={0} ma nieobsługiwany typ danych: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Generowanie struktury Swagger nie powiodło się, szczegóły: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| Nieznany błąd zostanie |  |
| UnknownJobStatusCode | Nieznany kod stanu zadania {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, szczegóły: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (kod stanu HTTP 500)
 
Wykonanie napotkał błąd wewnętrzny. Mało pamięci systemu. Spróbuj ponownie później.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (kod stanu HTTP 500)
 
Nieprawidłowy model pakiet. Sprawdź podany pakiet modelu jest poprawny i spróbuj ponownie.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (kod stanu HTTP 500)
 
Pakiet usługi sieci web nieprawidłowy. Sprawdź poprawność pakiet sieci web, pod warunkiem i spróbuj ponownie.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| ModuleError | Wykres pakietów usługi sieci web jest nieprawidłowy. Szczegóły: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (kod stanu HTTP 503)
 
Jako kontenery są inicjowania, nie można wykonać żądania.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (kod stanu HTTP 503)
 
Usługa jest tymczasowo niedostępna.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| NoMoreResources | Brak dostępnych zasobów do żądania. |
| RequestThrottled | Żądanie zostało ograniczone dla {0} punktu końcowego. Maksymalna współbieżność punktu końcowego jest {1}. |
| TooManyConcurrentRequests | Wysłano zbyt wiele żądań równoczesnych. |
| TooManyHostsBeingInitialized | Zbyt wiele hostów inicjowania w tym samym czasie. Należy rozważyć ograniczenie / ponawianie próby. |
| TooManyHostsBeingInitializedPerModel | Zbyt wiele hostów inicjowania w tym samym czasie. Należy rozważyć ograniczenie / ponawianie próby. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (kod stanu HTTP 504)
 
Nie można zakończyć operacji w dozwolonym czasie.
 
| Kod błędu | Dla użytkownika komunikat o |
| ---------- |--------------|
| BackendInitializationTimeout | Nie można wykonać inicjowania usługi sieci web w dozwolonym czasie. |
| BackendScoreTimeout | Nie można ukończyć wykonywania żądania usługi sieci web w dozwolonym czasie. |
 
