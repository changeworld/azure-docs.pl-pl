---
title: Klucze tworzenia i wykonywania — LUIS
titleSuffix: Azure Cognitive Services
description: LUIS używa dwóch kluczy, klucza tworzenia do tworzenia modelu i klucza czasu wykonywania do wykonywania zapytań dotyczących punktu końcowego przewidywania za pomocą wyrażenia długości użytkownika.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 70e58077fa40ce685324cd24b447886ec3411034
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703179"
---
# <a name="authoring-and-runtime-keys"></a>Klucze tworzenia i środowiska uruchomieniowego


>[!NOTE]
>Przed kontynuowaniem [Wykonaj migrację](luis-migration-authoring.md) wszelkich aplikacji, które nie używają zasobu tworzenia platformy Azure.

LUIS używa dwóch typów zasobów platformy Azure, każdy typ ma klucze: 
 
* [Tworzenie w celu utworzenia](#programmatic-key) intencji, jednostek i etykiet wyrażenia długości, uczenia i publikowania. Gdy wszystko będzie gotowe do opublikowania aplikacji LUIS, potrzebujesz [klucza punktu końcowego przewidywania dla środowiska uruchomieniowego](luis-how-to-azure-subscription.md) przypisanego do aplikacji.
* [Klucz punktu końcowego przewidywania dla środowiska uruchomieniowego](#prediction-endpoint-runtime-key). Aplikacje klienckie, takie jak rozmowa bot, wymagają dostępu do **punktu końcowego przewidywania zapytań** środowiska uruchomieniowego za pomocą tego klucza. 

|Key|Cel|Usługa poznawcze `kind`|Usługa poznawcze `type`|
|--|--|--|--|
|[Klucz tworzenia](#programmatic-key)|Tworzenie, uczenie, publikowanie, testowanie.|`LUIS.Authoring`|`Cognitive Services`|
|[Klucz środowiska uruchomieniowego punktu końcowego przewidywania](#prediction-endpoint-runtime-key)| Przepowiadaj zapytania do środowiska uruchomieniowego punktu końcowego za pomocą wypowiedź użytkownika, aby określić intencje i jednostki.|`LUIS`|`Cognitive Services`|

LUIS udostępnia również [klucz początkowy](luis-how-to-azure-subscription.md#starter-key) z 1000 transakcji na miesiąc przewidywania. 

Chociaż nie musisz tworzyć obu kluczy w tym samym czasie, jest to znacznie prostsze.

Ważne jest, aby tworzyć aplikacje LUIS w [regionach](luis-reference-regions.md#publishing-regions) , w których chcesz publikować i wysyłać zapytania.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>Klucz tworzenia

Klucz tworzenia jest tworzony automatycznie podczas tworzenia konta LUIS i jest bezpłatny. Po rozpoczęciu pracy z usługą LUIS masz jeden klucz startowy dla wszystkich aplikacji LUIS dla każdego [regionu](luis-reference-regions.md)tworzenia. Celem klucza tworzenia jest zapewnienie uwierzytelniania w celu zarządzania aplikacją LUIS lub testowania zapytań dotyczących punktów końcowych przewidywania. 

Tworzenie kluczy tworzenia w Azure Portal umożliwia kontrolowanie uprawnień do zasobu tworzenia przez przypisanie osób do [roli współautor](#contributions-from-other-authors). Aby dodać współautorów, musisz mieć uprawnienia na poziomie subskrypcji platformy Azure. 

Aby znaleźć klucz tworzenia, zaloguj się do [Luis](luis-reference-regions.md#luis-website) i kliknij nazwę konta w prawym górnym pasku nawigacyjnym, aby otworzyć **Ustawienia konta**.

![Klucz tworzenia](./media/luis-concept-keys/authoring-key.png)

Jeśli chcesz wykonać **zapytania środowiska uruchomieniowego**, utwórz zasób usługi Azure [Luis](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Dla wygody wiele przykładów korzysta z [klucza początkowego](#starter-prediction-endpoint-runtime-key) , ponieważ oferuje on kilka bezpłatnych wywołań punktów końcowych przewidywania w swoim [limicie przydziału](luis-boundaries.md#key-limits).  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>Klucz środowiska uruchomieniowego punktu końcowego przewidywania 

Gdy są potrzebne **zapytania punktu końcowego środowiska uruchomieniowego**, utwórz zasób Language UNDERSTANDING (Luis), a następnie przypisz go do aplikacji Luis. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Po zakończeniu procesu tworzenia zasobu [Przypisz klucz](luis-how-to-azure-subscription.md) do aplikacji. 

* Klucz środowiska uruchomieniowego (punkt końcowy przewidywania zapytania) umożliwia przydział trafień punktów końcowych na podstawie planu użycia określonego podczas tworzenia klucza czasu wykonywania. Aby uzyskać informacje o cenach, zobacz [Cennik usługi Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) .

* Klucz czasu wykonywania może być używany dla wszystkich aplikacji LUIS lub określonych aplikacji LUIS. 
* Nie należy używać klucza środowiska uruchomieniowego do tworzenia aplikacji LUIS. 

### <a name="starter-prediction-endpoint-runtime-key"></a>Klucz środowiska uruchomieniowego punktu końcowego przewidywania początkowego

**Początkowy** klucz punktu końcowego przewidywania jest dostępny bezpłatnie i obejmuje zapytania dotyczące punktów końcowych przewidywania 1000. Po zastosowaniu tych zapytań należy utworzyć własny zasób przewidywanego punktu końcowego dla Language Understanding.  

Jest to specjalny zasób utworzony przez Ciebie. Nie jest ona wyświetlana na liście zasobów platformy Azure, ponieważ jest ona przeznaczona do tymczasowego klucza początkowego. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>Użyj klucza środowiska uruchomieniowego w zapytaniu
Punkt końcowy LUIS środowiska uruchomieniowego akceptuje dwa style zapytania, oba używają klucza środowiska uruchomieniowego punktu końcowego przewidywania, ale w różnych miejscach.

Punkt końcowy służący do uzyskiwania dostępu do środowiska uruchomieniowego używa poddomeny, która jest unikatowa dla regionu zasobu, co oznacza `{region}` w poniższej tabeli. 


#### <a name="v2-prediction-endpointtabv2"></a>[Punkt końcowy przewidywania wersji 2](#tab/V2)

|Czasownik|Przykładowy adres URL i lokalizacja klucza|
|--|--|
|[Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`|
|[POUBOJOWEGO](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`|

#### <a name="v3-prediction-endpointtabv3"></a>[Punkt końcowy przewidywania v3](#tab/V3)

|Czasownik|Przykładowy adres URL i lokalizacja klucza|
|--|--|
|[Pobierz](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a91e54c9db63d589f433)|`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?runtime-key=your-endpoint-key-here&query=turn%20on%20the%20lights`|
|[POUBOJOWEGO](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a5830f741b27cd03a061)| `https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict`| 

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * * 

**Pobierz**: Zmień wartość zapytania punktu końcowego dla `runtime-key` z klucza tworzenia (Starter) na nowy klucz punktu końcowego, aby użyć stawki przydziału klucza punktu końcowego Luis. Jeśli utworzysz klucz i przypiszesz klucz, ale nie zmienisz wartości zapytania punktu końcowego dla `runtime-key`, nie używasz limitu przydziału klucza punktu końcowego.

**Wpis**: Zmień wartość nagłówka `Ocp-Apim-Subscription-Key`<br>Jeśli utworzysz klucz środowiska uruchomieniowego i przypiszesz klucz środowiska uruchomieniowego, ale nie zmienisz wartości zapytania punktu końcowego dla `Ocp-Apim-Subscription-Key`, nie używasz klucza czasu wykonywania.

Identyfikator aplikacji użyty w poprzednich adresach URL, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, to publiczna aplikacja IoT używana do [prezentacji interaktywnej](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="assignment-of-the-runtime-key"></a>Przypisanie klucza czasu wykonywania

Klucz środowiska uruchomieniowego można [przypisać](luis-how-to-azure-subscription.md) w [portalu Luis](https://www.luis.ai) lub za pośrednictwem odpowiednich interfejsów API. 

## <a name="key-limits"></a>Limity kluczy

Można utworzyć maksymalnie 10 kluczy tworzenia na region na subskrypcję. 

Zobacz [limity kluczy](luis-boundaries.md#key-limits) i [regiony platformy Azure](luis-reference-regions.md). 

Publikowanie regionów różni się od regionów tworzenia. Upewnij się, że tworzysz aplikację w regionie tworzenia odpowiadającą regionowi publikowania, który ma znajdować się aplikacja kliencka.

## <a name="key-limit-errors"></a>Błędy limitu kluczy
W przypadku przekroczenia limitu przydziału transakcji na sekundę (TPS) pojawia się błąd HTTP 429. W przypadku przekroczenia limitu przydziału transakcji na miesiąc (TPS) pojawia się błąd HTTP 403. 

## <a name="contributions-from-other-authors"></a>Wkłady od innych autorów



Zarządzanie udziałami ze współpracowników zależy od bieżącego stanu aplikacji.

**W przypadku [tworzenia migrowanych aplikacji zasobów](luis-migration-authoring.md)** : _Współautorzy_ są zarządzani w Azure Portal dla zasobu tworzenia przy użyciu strony **Kontrola dostępu (IAM)** . Dowiedz się, [jak dodać użytkownika](luis-how-to-collaborate.md)przy użyciu adresu e-mail współpracownika i roli _współautor_ . 

W **przypadku aplikacji, które nie zostały jeszcze zmigrowane**: Wszyscy _współpracownicy_ są ZARZĄDZAni w portalu LUIS na stronie **Zarządzanie > współpracownikami** .

### <a name="contributor-roles-vs-entity-roles"></a>Role współautora ról podmiotów

[Role jednostek](luis-concept-roles.md) są stosowane do modelu danych aplikacji Luis. Role współpracownik/współautor stosują się do poziomów dostępu autorstwa. 

## <a name="moving-or-changing-ownership"></a>Przeniesienie lub zmiana własności

Aplikacja jest definiowana przez zasoby platformy Azure, które są określane przez subskrypcję właściciela. 

Możesz przenieść swoją aplikację LUIS. Skorzystaj z następujących zasobów dokumentacji w Azure Portal lub interfejs wiersza polecenia platformy Azure:

* [Przenoszenie aplikacji między zasobami tworzenia LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Przenieś zasób do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/resource-group-move-resources.md)
* [Przenoszenie zasobu w ramach tej samej subskrypcji lub między subskrypcjami](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* [Przenieś własność](../../billing/billing-subscription-transfer.md) subskrypcji 

## <a name="access-for-private-and-public-apps"></a>Dostęp do aplikacji prywatnych i publicznych

W przypadku aplikacji **prywatnej** dostęp do środowiska uruchomieniowego jest dostępny dla właścicieli i współautorów. W przypadku aplikacji **publicznej** dostęp do środowiska uruchomieniowego jest dostępny dla wszystkich użytkowników, którzy korzystają z własnej [usługi poznawczej](../cognitive-services-apis-create-account.md) platformy Azure lub zasobu środowiska uruchomieniowego [Luis](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) , i mają identyfikator aplikacji publicznej. 

Obecnie nie istnieje katalog aplikacji publicznych.

### <a name="authoring-access"></a>Dostęp do autorstwa
Dostęp do aplikacji z portalu [Luis](luis-reference-regions.md#luis-website) lub [interfejsów API tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) jest kontrolowany przez zasób tworzenia platformy Azure. 

Właściciel i wszyscy Współautorzy mają dostęp do tworzenia aplikacji. 

|Dostęp do tworzenia obejmuje|Uwagi|
|--|--|
|Dodaj lub Usuń klucze punktów końcowych||
|Eksportowanie wersji||
|Eksportowanie dzienników punktów końcowych||
|Wersja importowana||
|Tworzenie aplikacji jako publicznej|Gdy aplikacja jest publiczna, każda osoba mająca klucz tworzenia lub punktu końcowego może wysyłać zapytania do aplikacji.|
|Modyfikuj model|
|wycofywan|
|Zapoznaj się z punktem końcowym wyrażenia długości na potrzeby [aktywnego uczenia](luis-how-to-review-endpoint-utterances.md)|
|Trasy|

### <a name="prediction-endpoint-runtime-access"></a>Dostęp w czasie wykonywania przewidywania punktu końcowego

Dostęp do zapytania do punktu końcowego przewidywania jest kontrolowany przez ustawienie na stronie **Informacje o aplikacji** w sekcji **Zarządzanie** . 

![Ustaw aplikację na publiczną](./media/luis-concept-security/set-application-as-public.png)

|[Prywatny punkt końcowy](#runtime-security-for-private-apps)|[Publiczny punkt końcowy](#runtime-security-for-public-apps)|
|:--|:--|
|Dostępne dla właściciela i współautorów|Dostępne dla właściciela, współautorów i innych osób, które wiedzą o IDENTYFIKATORze aplikacji|

Można kontrolować, kto widzi klucz środowiska uruchomieniowego LUIS, wywołując go w środowisku serwer-serwer. Jeśli używasz LUIS z bot, połączenie między bot i LUIS jest już bezpieczne. W przypadku bezpośredniego wywoływania punktu końcowego LUIS należy utworzyć interfejs API po stronie serwera (na przykład [funkcję](https://azure.microsoft.com/services/functions/)platformy Azure) z dostępem kontrolowanym (na przykład [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy interfejs API po stronie serwera jest wywoływany i uwierzytelniany, a Autoryzacja jest weryfikowana, należy przekazać wywołanie do LUIS. Chociaż ta strategia nie zapobiega atakom typu man-in-the-Middle, zasłania klucz i adres URL punktu końcowego od użytkowników, umożliwia śledzenie dostępu i umożliwia dodanie rejestrowania odpowiedzi na punkt końcowy (na przykład [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla prywatnych aplikacji

Środowisko uruchomieniowe aplikacji prywatnej jest dostępne tylko dla następujących:

|Klucz i użytkownik|Wyjaśnienie|
|--|--|
|Klucz autorstwa właściciela| Do 1000 trafień punktów końcowych|
|Współautorzy/klucze autorstwa| Do 1000 trafień punktów końcowych|
|Dowolny klucz przypisany do LUIS przez autora lub współpracownika/współautora|Na podstawie warstwy użycia klucza|

#### <a name="runtime-security-for-public-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla aplikacji publicznych

Gdy aplikacja zostanie skonfigurowana jako publiczna, _dowolny_ prawidłowy klucz Luis Authoring lub klucz punktu końcowego Luis może wysyłać zapytania do aplikacji, o ile klucz nie użył całego limitu przydziału punktów końcowych.

Użytkownik, który nie jest właścicielem lub współautorem, może uzyskać dostęp do środowiska uruchomieniowego aplikacji publicznej tylko wtedy, gdy podano identyfikator aplikacji. LUIS nie ma publicznego _rynku_ ani innego sposobu wyszukiwania aplikacji publicznej.  

Publiczna aplikacja jest publikowana we wszystkich regionach, dzięki czemu użytkownik z kluczem zasobów LUIS opartym na regionie może uzyskać dostęp do aplikacji w dowolnym regionie, który jest skojarzony z kluczem zasobu.

## <a name="transfer-of-ownership"></a>Przeniesienie własności

**W przypadku [tworzenia aplikacji migrowanych zasobów](luis-migration-authoring.md)** : jako właściciel zasobu można dodać `contributor`.

**W przypadku aplikacji, które nie zostały jeszcze zmigrowane**: wyeksportuj aplikację jako plik JSON. Inny użytkownik LUIS może zaimportować aplikację, w związku z czym staje się właścicielem aplikacji. Nowa aplikacja będzie mieć inny identyfikator aplikacji.  

## <a name="securing-the-endpoint"></a>Zabezpieczanie punktu końcowego 

Można kontrolować, kto może wyświetlać klucz punktu końcowego środowiska uruchomieniowego LUIS prognoz, wywołując go w środowisku serwer-serwer. Jeśli używasz LUIS z bot, połączenie między bot i LUIS jest już bezpieczne. W przypadku bezpośredniego wywoływania punktu końcowego LUIS należy utworzyć interfejs API po stronie serwera (na przykład [funkcję](https://azure.microsoft.com/services/functions/)platformy Azure) z dostępem kontrolowanym (na przykład [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy wywoływany jest interfejs API po stronie serwera, a uwierzytelnianie i autoryzacja są weryfikowane, należy przekazać wywołanie do LUIS. Chociaż ta strategia nie zapobiega atakom typu man-in-the-Middle, zasłania swój punkt końcowy od użytkowników, umożliwia śledzenie dostępu i umożliwia dodanie rejestrowania odpowiedzi na punkt końcowy (na przykład [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Następne kroki

* Opis pojęć związanych z [przechowywaniem wersji](luis-concept-version.md) . 
* Dowiedz się [, jak tworzyć klucze](luis-how-to-azure-subscription.md).
