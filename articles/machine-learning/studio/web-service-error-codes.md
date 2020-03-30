---
title: Kody błędów interfejsu API REST — Usługa Azure Machine Learning Studio (klasyczna) | Dokumenty firmy Microsoft
description: Te kody błędów mogą zostać zwrócone przez operację w usłudze sieci web usługi Azure Machine Learning.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217771"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Kody błędów interfejsu API usługi Azure Machine Learning Studio (klasyczne)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
Następujące kody błędów mogą zostać zwrócone przez operację w usłudze azure machine learning studio (klasycznej).
 
## <a name="badargument-http-status-code-400"></a>BadArgument (kod stanu HTTP 400)
 
Podany nieprawidłowy argument.
 
Ta klasa błędów oznacza, że argument podany gdzieś był nieprawidłowy. Może to być poświadczenie lub lokalizacja usługi Azure Storage do usługi przekazanej do usługi sieci web. Proszę spojrzeć na błąd "kod" pole w sekcji "szczegóły", aby zdiagnozować, który konkretny argument był nieprawidłowy.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| Wartość BadParameter | Podana wartość parametru nie spełnia reguły parametru parametru |
| BadSubscriptionId (Zły nieprawidłowysubskrypcjaid) | Identyfikator subskrypcji używany do oceniania nie jest obecny w zasobie |
| BadVersionCall (Polski) | Nieprawidłowy parametr wersji został przekazany {0}podczas wywołania interfejsu API: . Sprawdź stronę pomocy interfejsu API pod kątem przekazania poprawnej wersji i spróbuj ponownie. |
| BatchJobInputsOkreślony | W żądaniu nie określono następujących wymaganych danych {0}wejściowych: . Upewnij się, że wszystkie dane wejściowe są określone i spróbuj ponownie. |
| PartiaJobInputsTooWykreślony | Żądanie określone więcej danych wejściowych niż zdefiniowane w usłudze. Lista zaakceptowanych danych wejściowych: {0}. Upewnij się, że wszystkie dane wejściowe są określone poprawnie i spróbuj ponownie. |
| Nazwa obiektu BlobNameTooLong | Ścieżka magazynu obiektów blob platformy Azure {0}przewidziana dla danych wyjściowych diagnostyki jest za długa: . Skróć ścieżkę i spróbuj ponownie. |
| Obiekt BlobNotFound | Nie można uzyskać dostępu do {0}dostarczonego obiektu blob platformy Azure — .  Komunikat o {1}błędzie platformy Azure: . |
| KontenerIsEmpty | Nie podano nazwy kontenera magazynu platformy Azure. Podaj prawidłową nazwę kontenera i spróbuj ponownie. |
| ContainerSegmentInvalid | Nieprawidłowa nazwa kontenera. Podaj prawidłową nazwę kontenera i spróbuj ponownie. |
| ContainerValidationFailed | Sprawdzanie poprawności kontenera obiektów blob nie powiodło się z powodu tego błędu: {0}. |
| DataTypeNotSupported | Nieobsługiwał typ danych. Podaj prawidłowe typy danych i spróbuj ponownie. |
| DuplicateInputInBatchCall (DuplicateInPutInBatchCall) | Żądanie wsadowe jest nieprawidłowe. Nie można określić jedno i wiele danych wejściowych jednocześnie. Usuń jeden z tych elementów z żądania i spróbuj ponownie. |
| ExpiryTimeInThePast | Podany czas wygaśnięcia jest {0}w przeszłości: . Podaj przyszły czas wygaśnięcia w czasie UTC i spróbuj ponownie. Aby nigdy nie wygasnąć, ustaw czas wygaśnięcia na NULL. |
| Niekompletnestawy | Ustawienia diagnostyki są niekompletne. |
| Dane Danych Nienależne | Nie podano nazwy obiektu blob magazynu platformy Azure. Podaj prawidłową nazwę obiektu blob i spróbuj ponownie. |
| InvalidBlob (Nieprawidłowyblob) | Nieprawidłowa specyfikacja obiektu {0}blob dla obiektu blob: . Sprawdź, czy parametry połączenia / ścieżka względna lub specyfikacja tokenu sygnatury dostępu Współdzielonego są poprawne i spróbuj ponownie. |
| InvalidBlobConnectionString (Nieprawidłowe łączenie sznurek) | Ciąg połączenia określony dla jednego z wejściowych/wyjściowych obiektów blob jest nieprawidłowy: {0}. Popraw to i spróbuj ponownie. |
| Nieprawidłowa blichwiana | Odwołanie do obiektu {0} blob: ma nieprawidłowe lub brakujące rozszerzenie pliku. Obsługiwane rozszerzenia plików dla tego typu{1}danych wyjściowych to: " ". |
| Nazwy InvalidInput | Nieprawidłowa nazwa(-y) wejściowa {0}usługi(-e) określona w żądaniu: . Zamapuj dane wejściowe na poprawne dane wejściowe usługi i spróbuj ponownie. |
| Nazwa nazwy nieprawidłowej nazwy najemnej | Nieprawidłowa nazwa zastąpienia {0}danych wyjściowych: . Usługa nie ma węzła wyjściowego o tej nazwie. Proszę przekazać w poprawnej nazwie węzła wyjściowego, aby zastąpić (rozróżnia wielkość liter ma zastosowanie). |
| Inwalidyparametr | Nieprawidłowy parametr{0}kwerendy ' '. {1} |
| MissingInputBlobInformation | Brak informacji o obiektach blob magazynu platformy Azure. Podaj prawidłowy ciąg połączenia i ścieżkę względną lub identyfikator URI i spróbuj ponownie. |
| BrakJobId | Nie podano identyfikatora pracy. Identyfikator zadania jest zwracany, gdy zadanie zostało przesłane po raz pierwszy. Sprawdź, czy identyfikator zadania jest poprawny i spróbuj ponownie. |
| Braki Klucze | Nie podano kluczy ani jednego z klucza podstawowego lub pomocniczego nie jest podany. |
| BrakmodelPakiet | Nie podano identyfikatora pakietu modelu ani pakietu modelu. Podaj prawidłowy identyfikator pakietu modelu lub pakiet modelu i spróbuj ponownie. |
| BrakOutputOverrideSpecyfikacja | W żądaniu brakuje specyfikacji obiektu blob dla zastąpienia danych wyjściowych {0}. Określ prawidłową lokalizację obiektu blob z żądaniem lub usuń specyfikację wyjściową, jeśli nie jest wymagane zastąpienie lokalizacji. |
| BrakrequestInput | Usługa sieci web oczekuje danych wejściowych, ale nie podano żadnych danych wejściowych. Upewnij się, że prawidłowe dane wejściowe są dostarczane na podstawie opublikowanych portów wejściowych w modelu i spróbuj ponownie. |
| Brak wymaganychglobalparametrów | Nie wszystkie wymagane parametry usługi sieci web. Sprawdź, czy parametry oczekiwane dla modułów są poprawne i spróbuj ponownie. |
| Brak wymaganychzamiestoverrides | Podczas wywoływania punktu końcowego usługi zaszyfrowanej jest obowiązkowe do przekazywania w zastąpienia danych wyjściowych dla wszystkich wyjść usługi. Brak przesłonięcia w tej chwili dla tych wyjść:{0} |
| Brakasiecigrupy UsługWeb | Nie podano identyfikatora grupy usług sieci web. Podaj prawidłowy identyfikator grupy usług sieci web i spróbuj ponownie. |
| MissingWebServiceId (łaknienie Usługi UsługiWebId | Nie podano identyfikatora usługi sieci web. Podaj prawidłowy identyfikator usługi sieci web i spróbuj ponownie. |
| BrakWebServicePackage | Brak pakietu usługi sieci web. Podaj prawidłowy pakiet usługi sieci web i spróbuj ponownie. |
| Brakuchównyzd przestrzeni prac | Nie podano identyfikatora obszaru roboczego. Podaj prawidłowy identyfikator obszaru roboczego i spróbuj ponownie. |
| Wartość konfiguracji modeluInwalid | Nieprawidłowa konfiguracja modelu w pakiecie modelu. Upewnij się, że konfiguracja modelu zawiera wyjściową definicję punktu końcowego, punkt końcowy błędu std i punkt końcowy std out i spróbuj ponownie. |
| ModelPackageIdInvalid | Nieprawidłowy identyfikator pakietu modelu. Sprawdź, czy identyfikator pakietu modelu jest poprawny i spróbuj ponownie. |
| RequestBodyInvalid (ŻądajBodyInvalid) | Nie podano treści żądania lub błędu w deserializacji treści żądania. |
| WniosekIsempty | Nie podano żadnego żądania. Podaj prawidłowe żądanie i spróbuj ponownie. |
| Nieoczekiwanyparametr | Dostarczone nieoczekiwane parametry. Sprawdź, czy wszystkie nazwy parametrów są wpisane poprawnie, przekazywane są tylko oczekiwane parametry i spróbuj ponownie. |
| NieznanyError | Nieznany błąd. |
| UżytkownikParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Nie można zmienić wymagań {0} dotyczących równoczesnych żądań dla usługi sieci web. |
| Wartość WebServiceIdInvalid | Podany nieprawidłowy identyfikator usługi sieci web. Identyfikator usługi sieci Web powinien być prawidłowym identyfikatorem guid. |
| WebServiceTooManyConcurrentRequestRequirement | Nie można ustawić wymagania równoczesnego żądania na więcej niż {0}. |
| Wartość WebServiceTypeInvalid | Podany nieprawidłowy typ usługi sieci web. Sprawdź, czy prawidłowy typ usługi sieci web jest poprawny i spróbuj ponownie. Prawidłowe typy {0}usług internetowych: . |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (kod stanu HTTP 400)
 
Podany nieprawidłowy argument użytkownika.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| InputMismatchError | Dane wejściowe nie są zgodne ze schematem portu wejściowego. |
| InputParseError | Analizowanie wektora wejściowego nie powiodło się.  Sprawdź, czy wektor wejściowy ma poprawną liczbę kolumn i typów danych.  Dodatkowe szczegóły: {0}. |
| Brak wymaganychglobalparametrów | Brakuje parametrów oczekiwanych przez usługę sieci web. Sprawdź, czy wszystkie wymagane parametry oczekiwane przez usługę sieci web są poprawne i spróbuj ponownie. |
| Nieoczekiwanyparametr | Sprawdź, czy tylko wymagane parametry oczekiwane przez usługę sieci web są przekazywane i spróbuj ponownie. |
| UżytkownikParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>Nieprawidłowy działanie (kod stanu HTTP 400)
 
Żądanie jest nieprawidłowe w bieżącym kontekście.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| Nie można rozpocząć pracy | Nie można uruchomić zadania, {0} ponieważ jest w stanie. |
| NiekompatybilnyModel | Model jest niezgodny z wersją żądania. Wersja żądania obsługuje tylko pojedyncze modele wyjściowe zdatną do danych. |
| MultipleInputsNietone | Model nie zezwala na wiele wejść. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (kod stanu HTTP 400)
 
Wykonanie modułu napotkał błąd biblioteki wewnętrznej.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (kod stanu HTTP 400)
 
Wykonanie modułu napotkał błąd.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (kod stanu HTTP 400)
 
Nieprawidłowy pakiet usługi sieci web. Sprawdź, czy dostarczony pakiet usług sieci web jest poprawny i spróbuj ponownie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| FormatError | Pakiet usługi sieci web jest zniekształcony. Szczegóły:{0} |
| Środowisko uruchomienioweError | Wykres pakietu usługi sieci web jest nieprawidłowy. Szczegóły:{0} |
| Validationerror | Wykres pakietu usługi sieci web jest nieprawidłowy. Szczegóły:{0} |
 
## <a name="unauthorized-http-status-code-401"></a>Nieautoryzowane (kod stanu HTTP 401)
 
Żądanie jest nieautoryzowane, aby uzyskać dostęp do zasobu.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| AdminRequestNie nieautoryzowane | Brak autoryzacji |
| ZarządzanieRequestNie nieautoryzowane | Brak autoryzacji |
| WynikRequestNieautoryzowane | Podane nieprawidłowe poświadczenia. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (kod stanu HTTP 404)
 
Nie znaleziono zasobu.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| ModelPackageNotfound | Nie znaleziono pakietu modelu. Sprawdź, czy identyfikator pakietu modelu jest poprawny i spróbuj ponownie. |
| Obszar roboczy Usługi WebServiceIdNotFoundInWorkspace | Nie znaleziono usługi sieci Web w tym obszarze roboczym. Istnieje niezgodność między webServiceId i workspaceId. Sprawdź, czy usługa sieci web jest częścią obszaru roboczego i spróbuj ponownie. |
| WebServiceNotFound (WebServiceNotFound) | Nie znaleziono usługi sieci Web. Sprawdź, czy identyfikator usługi sieci web jest poprawny i spróbuj ponownie. |
| Obszar roboczyZakrycie | Nie znaleziono obszaru roboczego. Sprawdź, czy identyfikator obszaru roboczego jest poprawny i spróbuj ponownie. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (kod stanu HTTP 408)
 
Nie można ukończyć operacji w dozwolonym czasie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| RequestCanceled (Wyliczył) | Żądanie zostało anulowane przez klienta. |
| ScoreRequestTimeout ( ScoreRequestTimeout ) | Limit czasu żądania wykonania. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (kod stanu HTTP 409)
 
Zasób już istnieje.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Nieprawidłowa nazwa parametru wyjściowego. Spróbuj użyć modułu edytora metadanych, aby zmienić nazwę kolumn i spróbuj ponownie. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (kod stanu HTTP 413)
 
Model przekroczył przypisany do niego przydział pamięci.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| OutOfMemoryLimit | Model zużywał więcej pamięci niż został zawłaszczony dla niego. Maksymalna dozwolona pamięć {0} dla modelu to MB. Sprawdź, czy w modelu nie ma problemów. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (kod stanu HTTP 500)
 
Wykonanie napotkało błąd wewnętrzny.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| AdminAuthenticationNieważone |  |
| ZapleczeArgumentError |  |
| BackendBadRequest (BackendBadRequest) |  |
| Konfiguracja klastra |  |
| ContainerProcessTerminatedWithSystemError | Proces kontenera uległ awarii z błędem systemu |
| ContainerProcessTerminatedWithUnknownError | Proces kontenera uległ awarii z nieznanym błędem |
| ContainerValidationFailed | Sprawdzanie poprawności kontenera obiektów blob nie powiodło się z powodu tego błędu: {0}. |
| DeleteWebServiceResourceFailed |  |
| WyjątekDeserializationError |  |
| FailedGettingApiDocument |  |
| Usługa failedStoringWebService |  |
| InvalidMemoryConfiguration (Konfiguracja nieprawidłowego | InvalidMemoryConfiguration, ConfigValue:{0} |
| Nieprawidłowa konfiguracja usługi Usługi Nieobjętej źródłemCache |  |
| Nieprawidłowa konfiguracja funkcji ponownego przyrządu źródłowego |  |
| InvalidWebServiceResources |  |
| Brakzakańcinstance | Nie przedstawiono żadnych argumentów. Sprawdź, czy prawidłowe argumenty są przekazywane i spróbuj ponownie. |
| ModelPackageInvalid |  |
| ModułWykonany |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| Dane wyjścioweWnienienie |  |
| PortDataTypeNotSupported | Port id={0} ma nieobsługicony {1}typ danych: . |
| Zawężanie zapasu zasobów |  |
| ResourceLoadFailed (Nieuwzłoczeszanie zasobów) |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Generowanie Swagger nie powiodło się, Szczegóły:{0} |
| UnexpectedScoreStatus (Stan nieoczekiwany) |  |
| NieznanyBackendErrorReponse |  |
| NieznanyError |  |
| NieznanyDobStatusCode | Nieznany kod {0}stanu zadania . |
| NieznanyModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| Usługa WebServicePackageInvalid | InvalidWebServicePackage, szczegóły:{0} |
| Autoryzacja pracownikówPowikszona |  |
| PracownikNiereachowalne |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (kod stanu HTTP 500)
 
Wykonanie napotkało błąd wewnętrzny. Brak pamięci w systemie. Spróbuj ponownie.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (kod stanu HTTP 500)
 
Nieprawidłowy pakiet modelu. Sprawdź, czy dostarczony pakiet modelu jest poprawny i spróbuj ponownie.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (kod stanu HTTP 500)
 
Nieprawidłowy pakiet usługi sieci web. Sprawdź, czy dostarczony pakiet internetowy jest poprawny i spróbuj ponownie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| ModułError | Wykres pakietu usługi sieci web jest nieprawidłowy. Szczegóły:{0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InicjowanieKontenery (kod stanu HTTP 503)
 
Żądanie nie może wykonać, ponieważ kontenery są inicjowane.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>UsługaNiedostępna (kod stanu HTTP 503)
 
Usługa jest tymczasowo niedostępna.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| NoMoreResources | Brak zasobów dostępnych dla żądania. |
| WniosekThrottled | Żądanie zostało ograniczone {0} dla punktu końcowego. Maksymalna współbieżność dla punktu {1}końcowego to . |
| TooManyConcurrentRequests | Zbyt wiele równoczesnych żądań wysłanych. |
| TooManyHostsBeingInitialized | Zbyt wiele hostów jest inicjowane w tym samym czasie. Należy wziąć pod uwagę ograniczanie / ponowienie próby. |
| TooManyHostsBeingInitializedPerModel | Zbyt wiele hostów jest inicjowane w tym samym czasie. Należy wziąć pod uwagę ograniczanie / ponowienie próby. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>Limit czasu bramy (kod stanu HTTP 504)
 
Nie można ukończyć operacji w dozwolonym czasie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| Limit czasu wewnętrznej | Nie można ukończyć inicjowania usługi sieci web w dozwolonym czasie. |
| BackendScoreTimeout (BackendScoreTimeout) | Nie można ukończyć wykonywania żądania usługi sieci web w dozwolonym czasie. |
 
