---
title: Kody błędów w usłudze Azure Machine Learning REST API | Dokumentacja firmy Microsoft
description: Te kody błędów mogą być zwracane przez operację usługi sieci web uczenie maszynowe Azure.
keywords: ''
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: fbedf355c6b1c45d581408a962b875544838087f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836093"
---
# <a name="machine-learning-rest-api-error-codes"></a>Kody błędów interfejsu API REST uczenia maszynowego
 
Następujące kody błędów mogą być zwracane przez operację usługi sieci web uczenie maszynowe Azure.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (kod stanu HTTP 400)
 
Podano nieprawidłowy argument.
 
Ta klasa błędy oznacza, że udostępniane innym argument był nieprawidłowy. Może to być poświadczeń lub lokalizacji magazynu Azure do coś przekazany do usługi sieci web. Zapoznaj się pola 'code' błąd w sekcji "szczegóły" do diagnozowania, które określonych argument był nieprawidłowy.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| BadParameterValue | Podana wartość parametru nie spełnia warunków reguły parametru dla parametru |
| BadSubscriptionId | Identyfikator służący do wyniku subskrypcji nie jest obecny w zasobie |
| BadVersionCall | Przekazano nieprawidłową wersję parametr w wywołaniu interfejsu API: {0}. Strona pomocy interfejsu API dla przekazanie poprawnej wersji i spróbuj ponownie. |
| BatchJobInputsNotSpecified | Następujące wymagane w żądaniu nie określono input(s): {0}. Upewnij się, że wszystkie dane wejściowe jest określona, a następnie spróbuj ponownie. |
| BatchJobInputsTooManySpecified | Żądanie określony wejść więcej niż zdefiniowana w usłudze. Lista input(s) zaakceptowane: {0}. Upewnij się, że wszystkie dane wejściowe został podany poprawnie, a następnie spróbuj ponownie. |
| BlobNameTooLong | Ścieżki do magazynu obiektów blob platformy Azure podana diagnostycznych danych wyjściowych jest za długa: {0}. Skróć ścieżkę i spróbuj ponownie. |
| BlobNotFound | Nie można uzyskać dostępu do podanego obiektu blob Azure - {0}.  Komunikat o błędzie platformy Azure: {1}. |
| ContainerIsEmpty | Podano bez nazwy kontenera magazynu systemu Azure. Podaj nazwę kontenera prawidłowe i spróbuj ponownie. |
| ContainerSegmentInvalid | Nazwa nieprawidłowym kontenerem. Podaj nazwę kontenera prawidłowe i spróbuj ponownie. |
| ContainerValidationFailed | Obiekt blob kontenera nie można sprawdzić poprawności z powodu następującego błędu: {0}. |
| DataTypeNotSupported | Nieobsługiwany typ danych pod warunkiem. Podaj prawidłowe dane typów i spróbuj ponownie. |
| DuplicateInputInBatchCall | Żądanie wsadowe jest nieprawidłowy. Nie można określić zarówno pojedynczych i wielu danych wejściowych w tym samym czasie. Usuń jeden z tych elementów z żądania i spróbuj ponownie. |
| ExpiryTimeInThePast | Podany czas wygaśnięcia jest w przeszłości: {0}. Podaj czas wygaśnięcia w przyszłości, w formacie UTC, a następnie spróbuj ponownie. Nigdy nie wygasa, należy ustawić czas wygaśnięcia na wartość NULL. |
| IncompleteSettings | Ustawienia diagnostyczne są niekompletne. |
| InputBlobRelativeLocationInvalid | Nie podano nazwy obiektu blob magazynu Azure. Podaj nazwę nieprawidłowy obiekt blob, a następnie spróbuj ponownie. |
| InvalidBlob | Nieprawidłowy obiekt blob specyfikacji dla obiekt blob: {0}. Sprawdź tego ciągu połączenia / ścieżkę względną lub specyfikacji tokenu sygnatury dostępu Współdzielonego jest poprawna i spróbuj ponownie. |
| InvalidBlobConnectionString | Ciąg połączenia dla jednego z obiektów blob wejścia/wyjścia nieprawidłowe: {0}. Rozwiązać ten problem i spróbuj ponownie. |
| InvalidBlobExtension | Odwołanie do obiektu blob: {0} nieprawidłowego lub brakującego pliku ma rozszerzenie. Obsługiwane rozszerzenia plików dla tego typu danych wyjściowych to: "{1}". |
| InvalidInputNames | Usługi danych wejściowych nazwy podanej w żądaniu: {0}. Mapowanie danych wejściowych do usługi poprawne dane wejściowe i spróbuj ponownie. |
| InvalidOutputOverrideName | Nieprawidłowe dane wyjściowe przesłaniania nazwy: {0}. Usługa nie ma danych wyjściowych węzła o tej nazwie. Przekaż nazwę węzła poprawne dane wyjściowe do zastąpienia (dotyczy rozróżniania wielkości liter). |
| InvalidQueryParameter | Nieprawidłowy parametr zapytania "{0}". {1} |
| MissingInputBlobInformation | Brak informacji o obiektu blob magazynu Azure. Podaj prawidłowe parametry połączenia i ścieżkę względną lub identyfikator URI, a następnie spróbuj ponownie. |
| MissingJobId | Brak podany identyfikator zadania. Zadanie identyfikator jest zwracany, gdy zadanie zostało przesłane po raz pierwszy. Sprawdź poprawność identyfikatora zadania, a następnie spróbuj ponownie. |
| MissingKeys | Brak kluczy dostarczona lub nie podano podstawowego lub dodatkowego klucza. |
| MissingModelPackage | Nie identyfikator pakietu modelu lub podać pakietu modelu. Podaj identyfikator pakietu prawidłowego modelu lub modelu pakietu i spróbuj ponownie. |
| MissingOutputOverrideSpecification | W żądaniu brakuje specyfikacji obiektu blob dla danych wyjściowych zastąpienia {0}. Określ lokalizację nieprawidłowy obiekt blob z żądaniem lub usuń specyfikację elementu wyjściowego, jeśli żadne przesłonięcie lokalizacji. |
| MissingRequestInput | Usługa sieci web oczekuje danych wejściowych, ale nie dane wejściowe nie został podany. Upewnij się, że znajdują się prawidłowe wartości wejściowe na podstawie opublikowanych portów wejściowych w modelu, a następnie spróbuj ponownie. |
| MissingRequiredGlobalParameters | Nie wszystkie wymagane podane parametry usługi sieci web. Sprawdź, czy parametry oczekiwanego przez moduły są poprawne i spróbuj ponownie. |
| MissingRequiredOutputOverrides | Podczas wywoływania metody punktu końcowego szyfrowanie usług, które jest wymagane do przekazania w danych wyjściowych zastąpienia dla wszystkich usług danych wyjściowych. Brak zastąpienia w tej chwili uzyskać te dane wyjściowe: {0} |
| MissingWebServiceGroupId | Podany identyfikator nie grupy usługi sieci web. Podaj identyfikator grupy usługi sieci web prawidłowe i spróbuj ponownie. |
| MissingWebServiceId | Podany identyfikator nie usługi sieci web. Usługi sieci web prawidłowy identyfikator i spróbuj ponownie. |
| MissingWebServicePackage | Nie podano pakiet usługi sieci web. Podaj pakiet usługi sieci web prawidłowe i spróbuj ponownie. |
| MissingWorkspaceId | Podany identyfikator nie obszaru roboczego. Podaj prawidłowy identyfikator obszaru roboczego, a następnie spróbuj ponownie. |
| ModelConfigurationInvalid | Nieprawidłowy model konfiguracji w pakiecie modelu. Upewnij się, Konfiguracja modelu zawiera definicję punktów końcowych danych wyjściowych, standardowy błąd punktu końcowego, a std punktu końcowego i spróbuj ponownie. |
| ModelPackageIdInvalid | Nieprawidłowy model pakietu identyfikatora. Sprawdź, czy identyfikator pakietu modelu jest poprawny i spróbuj ponownie. |
| RequestBodyInvalid | Nie podano treści żądania lub wystąpił błąd podczas deserializacji treści żądania. |
| RequestIsEmpty | Nie podano żądania. Podaj prawidłowy żądania i spróbuj ponownie. |
| UnexpectedParameter | Podane parametry nieoczekiwany. Sprawdź wszystkie nazwy parametrów są poprawne, tylko oczekiwane parametry są przekazywane, a następnie spróbuj ponownie. |
| Nieznany | Nieznany błąd. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Nie można zmienić wymagania równoczesnych żądań dla {0} usługi sieci web. |
| WebServiceIdInvalid | Podany identyfikator usługi sieci web nieprawidłowy. Identyfikator usługi sieci Web powinna być prawidłowym identyfikatorem guid. |
| WebServiceTooManyConcurrentRequestRequirement | Nie można ustawić wymaganie równoczesnych żądań więcej niż {0}. |
| WebServiceTypeInvalid | Nieprawidłowy typ sieci web usługi udostępniane. Sprawdź, czy typ usługi sieci web prawidłowy jest poprawny i spróbuj ponownie. Nieprawidłowe typy usług: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (kod stanu HTTP 400)
 
Podany argument nieprawidłowego użytkownika.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| InputMismatchError | Dane wejściowe jest niezgodna z portu wejściowego schematu. |
| InputParseError | Analizowanie wektor wejściowy nie powiodło się.  Sprawdź, czy wektor wejściowy ma nieprawidłową liczbę kolumn i typy danych.  Dodatkowe szczegóły: {0}. |
| MissingRequiredGlobalParameters | Brak parametrów oczekiwane przez usługę sieci web. Sprawdź, czy wszystkie wymagane parametry oczekiwane przez usługę sieci web są poprawne i spróbuj ponownie. |
| UnexpectedParameter | Sprawdź tylko wymagane parametry oczekiwane przez usługę sieci web zostały przesłane i spróbuj ponownie. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (kod stanu HTTP 400)
 
Żądania jest nieprawidłowy w bieżącym kontekście.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| CannotStartJob | Nie można uruchomić zadania, ponieważ jest on {0} stanu. |
| IncompatibleModel | Model jest niezgodna z wersją żądania. Wersja żądania obsługuje tylko modele danych wyjściowych jednego elementu datatable. |
| MultipleInputsNotAllowed | Model nie zezwala na wielu danych wejściowych. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (kod stanu HTTP 400)
 
Podczas wykonywania modułu Napotkano błąd wewnętrzny biblioteki.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (kod stanu HTTP 400)
 
Operacja uruchamiania modułu wystąpił błąd.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (kod stanu HTTP 400)
 
Pakiet usługi sieci web nieprawidłowy. Sprawdź podany pakiet usługi sieci web jest prawidłowy i spróbuj ponownie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| FormatError | Pakiet usługi sieci web jest nieprawidłowo sformułowany. Szczegóły: {0} |
| RuntimesError | Wykres pakietu usługi sieci web jest nieprawidłowy. Szczegóły: {0} |
| ValidationError | Wykres pakietu usługi sieci web jest nieprawidłowy. Szczegóły: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Nieautoryzowane (kod stanu HTTP 401)
 
Żądanie nie ma autoryzacji do dostępu do zasobów.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| AdminRequestUnauthorized | Brak autoryzacji |
| ManagementRequestUnauthorized | Brak autoryzacji |
| ScoreRequestUnauthorized | Podano nieprawidłowe poświadczenia. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (kod stanu HTTP 404)
 
Nie znaleziono zasobu.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| ModelPackageNotFound | Nie znaleziono pakietu modelu. Sprawdź, czy identyfikator pakietu modelu jest poprawny i spróbuj ponownie. |
| WebServiceIdNotFoundInWorkspace | Usługi sieci Web w ramach tego obszaru roboczego nie można odnaleźć. Wystąpiła niezgodność między webServiceId i workspaceId. Sprawdź usług sieci web jest częścią obszaru roboczego i spróbuj ponownie. |
| WebServiceNotFound | Nie można odnaleźć usługi sieci Web. Sprawdź, czy identyfikator usługi sieci web jest prawidłowy i spróbuj ponownie. |
| WorkspaceNotFound | Nie można odnaleźć obszaru roboczego. Sprawdź, czy obszar roboczy identyfikator jest poprawny i spróbuj ponownie. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (kod stanu HTTP 408)
 
Nie można zakończyć operacji w dozwolonym czasie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| RequestCanceled | Żądanie zostało anulowane przez klienta. |
| ScoreRequestTimeout | Upłynął limit czasu żądania wykonania. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (kod stanu HTTP 409)
 
Zasób już istnieje.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Nazwa parametru nieprawidłowe dane wyjściowe. Spróbuj zmienić nazwę kolumny i spróbuj ponownie za pomocą modułu Edytor metadanych. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (kod stanu HTTP 413)
 
Model przekroczył limit przydziału pamięci przypisane do niej.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| OutOfMemoryLimit | Model używany więcej pamięci niż była przeznaczona dla niego. Maksymalna ilość pamięci dla modelu jest {0} MB. Sprawdź, czy model problemów. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (kod stanu HTTP 500)
 
Wykonanie napotkał błąd wewnętrzny.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Wystąpiła awaria procesu kontenera z powodu błędu systemu |
| ContainerProcessTerminatedWithUnknownError | Z powodu nieznanego błędu wystąpiła awaria procesu kontenera |
| ContainerValidationFailed | Obiekt blob kontenera nie można sprawdzić poprawności z powodu następującego błędu: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nie podano żadnych argumentów. Sprawdź, czy prawidłowe argumenty zostały przesłane i spróbuj ponownie. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Identyfikator portów ={0} ma nieobsługiwany typ danych: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Generowanie swagger nie powiodło się, szczegółowe informacje: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| Nieznany |  |
| UnknownJobStatusCode | Kod stanu zadania nieznany {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, szczegóły: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (kod stanu HTTP 500)
 
Wykonanie napotkał błąd wewnętrzny. Mało pamięci systemu. Spróbuj ponownie.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (kod stanu HTTP 500)
 
Nieprawidłowy model pakietu. Sprawdź podany pakiet model jest prawidłowy i spróbuj ponownie.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (kod stanu HTTP 500)
 
Pakiet usługi sieci web nieprawidłowy. Sprawdź podany pakiet sieci web jest prawidłowy i spróbuj ponownie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| ModuleError | Wykres pakietu usługi sieci web jest nieprawidłowy. Szczegóły: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (kod stanu HTTP 503)
 
Nie można wykonać żądania, zgodnie z kontenerów jest inicjowany.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (kod stanu HTTP 503)
 
Usługa jest tymczasowo niedostępna.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| NoMoreResources | Brak dostępnych zasobów do żądania. |
| RequestThrottled | Żądanie zostało ograniczany do {0} punktu końcowego. Maksymalna współbieżności dla punktu końcowego jest {1}. |
| TooManyConcurrentRequests | Za dużo współbieżnych żądań wysyłane. |
| TooManyHostsBeingInitialized | Zbyt wiele hostów inicjowania w tym samym czasie. Należy rozważyć ograniczenie / ponawianie próby. |
| TooManyHostsBeingInitializedPerModel | Zbyt wiele hostów inicjowania w tym samym czasie. Należy rozważyć ograniczenie / ponawianie próby. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (kod stanu HTTP 504)
 
Nie można zakończyć operacji w dozwolonym czasie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| BackendInitializationTimeout | Nie można ukończyć inicjowania usługi sieci web w dozwolonym czasie. |
| BackendScoreTimeout | Nie można ukończyć wykonywania żądania usługi sieci web w dozwolonym czasie. |
 
