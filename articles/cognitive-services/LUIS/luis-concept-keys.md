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
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 5a6c87da7ae62af54990e0a1a2c62065717a201a
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256951"
---
# <a name="authoring-and-runtime-keys"></a>Klucze tworzenia i środowiska uruchomieniowego


>[!NOTE]
>Przed kontynuowaniem [Wykonaj migrację](luis-migration-authoring.md) wszelkich aplikacji, które nie używają zasobu tworzenia platformy Azure.

LUIS używa dwóch typów zasobów platformy Azure, każdy typ ma klucze: 
 
* [Tworzenie w celu utworzenia](#programmatic-key) intencji, jednostek i etykiet wyrażenia długości, uczenia i publikowania. Gdy wszystko będzie gotowe do opublikowania aplikacji LUIS, potrzebujesz [klucza punktu końcowego przewidywania dla środowiska uruchomieniowego](luis-how-to-azure-subscription.md) przypisanego do aplikacji.
* [Klucz punktu końcowego przewidywania dla środowiska uruchomieniowego](#prediction-endpoint-runtime-key). Aplikacje klienckie, takie jak rozmowa bot, wymagają dostępu do **punktu końcowego przewidywania zapytań** środowiska uruchomieniowego za pomocą tego klucza. 

|Klucz|Cel|Usługa poznawcze`kind`|Usługa poznawcze`type`|
|--|--|--|--|
|[Tworzenie klucza](#programmatic-key)|Tworzenie, uczenie, publikowanie, testowanie.|`LUIS.Authoring`|`Cognitive Services`|
|[Klucz środowiska uruchomieniowego punktu końcowego przewidywania](#prediction-endpoint-runtime-key)| Przepowiadaj zapytania do środowiska uruchomieniowego punktu końcowego za pomocą wypowiedź użytkownika, aby określić intencje i jednostki.|`LUIS`|`Cognitive Services`|

LUIS udostępnia również [klucz początkowy](luis-how-to-azure-subscription.md#starter-key) z 1000 transakcji na miesiąc przewidywania. 

Chociaż nie musisz tworzyć obu kluczy w tym samym czasie, jest to znacznie prostsze.

Ważne jest, aby tworzyć aplikacje LUIS w [regionach](luis-reference-regions.md#publishing-regions) , w których chcesz publikować i wysyłać zapytania.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>Tworzenie klucza

Klucz tworzenia jest tworzony automatycznie podczas tworzenia konta LUIS i jest bezpłatny. Po rozpoczęciu pracy z usługą LUIS masz jeden klucz startowy dla wszystkich aplikacji LUIS dla każdego [regionu](luis-reference-regions.md)tworzenia. Celem klucza tworzenia jest zapewnienie uwierzytelniania w celu zarządzania aplikacją LUIS lub testowania zapytań dotyczących punktów końcowych przewidywania. 

Tworzenie kluczy tworzenia w Azure Portal umożliwia kontrolowanie uprawnień do zasobu tworzenia przez przypisanie osób do [roli współautor](#contributions-from-other-authors). Aby dodać współautorów, musisz mieć uprawnienia na poziomie subskrypcji platformy Azure. 

Aby znaleźć klucz tworzenia, zaloguj się do [Luis](luis-reference-regions.md#luis-website) i kliknij nazwę konta w prawym górnym pasku nawigacyjnym, aby otworzyć **Ustawienia konta**.

![Tworzenie klucza](./media/luis-concept-keys/authoring-key.png)

Jeśli chcesz wykonać **zapytania środowiska uruchomieniowego**, utwórz zasób usługi Azure [Luis](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Dla wygody wiele przykładów korzysta z [klucza początkowego](#starter-prediction-endpoint-runtime-key) , ponieważ oferuje on kilka bezpłatnych wywołań punktów końcowych przewidywania w swoim [limicie przydziału](luis-boundaries.md#key-limits).  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>Klucz środowiska uruchomieniowego punktu końcowego przewidywania 

Gdy są potrzebne **zapytania punktu końcowego środowiska uruchomieniowego**, utwórz zasób Language UNDERSTANDING (Luis), a następnie przypisz go do aplikacji Luis. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Po zakończeniu procesu tworzenia zasobu [Przypisz klucz](luis-how-to-azure-subscription.md) do aplikacji. 

* Klucz środowiska uruchomieniowego (punkt końcowy przewidywania zapytania) umożliwia przydział trafień punktów końcowych na podstawie planu użycia określonego podczas tworzenia klucza czasu wykonywania. Zobacz [ceny usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) Aby uzyskać informacje o cenach.

* Klucz czasu wykonywania może być używany dla wszystkich aplikacji LUIS lub określonych aplikacji LUIS. 
* Nie należy używać klucza środowiska uruchomieniowego do tworzenia aplikacji LUIS. 

### <a name="starter-prediction-endpoint-runtime-key"></a>Klucz środowiska uruchomieniowego punktu końcowego przewidywania początkowego

**Początkowy** klucz punktu końcowego przewidywania jest dostępny bezpłatnie i obejmuje zapytania dotyczące punktów końcowych przewidywania 1000. Po zastosowaniu tych zapytań należy utworzyć własny zasób przewidywanego punktu końcowego dla Language Understanding.  

Jest to specjalny zasób utworzony przez Ciebie. Nie jest ona wyświetlana na liście zasobów platformy Azure, ponieważ jest ona przeznaczona do tymczasowego klucza początkowego. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>Użyj klucza środowiska uruchomieniowego w zapytaniu
Punkt końcowy LUIS środowiska uruchomieniowego akceptuje dwa style zapytania, oba używają klucza środowiska uruchomieniowego punktu końcowego przewidywania, ale w różnych miejscach.

Punkt końcowy używany do uzyskiwania dostępu do środowiska uruchomieniowego używa poddomeny, która jest unikatowa dla regionu zasobu, `{region}` w poniższej tabeli. 

|zlecenia|Przykład lokalizacji adresu url i klucz|
|--|--|
|[GET](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>wartość ciągu zapytania `runtime-key`<br><br>Zmień wartość kwerendy punktu końcowego dla `runtime-key` z tworzenia klucza (starter), do nowego klucza punktu końcowego, aby można było używać współczynnik przydziału klucza punktu końcowego usługi LUIS. Jeśli klucz zostanie utworzony i przypisz klucza, ale nie zmieniaj wartości kwerendy punktu końcowego dla `runtime-key`, nie używasz przydziału klucza punktu końcowego.|
|[POST](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> wartość nagłówka `Ocp-Apim-Subscription-Key`<br>Jeśli utworzysz klucz środowiska uruchomieniowego i przypiszesz klucz środowiska uruchomieniowego, ale nie zmienisz wartości zapytania `Ocp-Apim-Subscription-Key`punktu końcowego dla, nie używasz klucza czasu wykonywania.|

Identyfikator aplikacji używane w poprzednich adresów URL, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, jest publiczny aplikacja IoT, używana do [pokaz interaktywny](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="assignment-of-the-runtime-key"></a>Przypisanie klucza czasu wykonywania

Klucz środowiska uruchomieniowego można [przypisać](luis-how-to-azure-subscription.md) w [portalu Luis](https://www.luis.ai) lub za pośrednictwem odpowiednich interfejsów API. 

## <a name="key-limits"></a>Ograniczenia klucza

Można utworzyć maksymalnie 10 kluczy tworzenia na region na subskrypcję. 

Zobacz [limity kluczy](luis-boundaries.md#key-limits) i [regiony platformy Azure](luis-reference-regions.md). 

Regiony publikowania różnią się od tworzenia regionów. Upewnij się, że tworzysz aplikację w regionie tworzenia odpowiadającą regionowi publikowania, który ma znajdować się aplikacja kliencka.

## <a name="key-limit-errors"></a>Błędy limitu klucza
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

|Tworzenie dostępu zawiera|Uwagi|
|--|--|
|Dodawanie lub usuwanie punktu końcowego kluczy||
|Eksportowanie wersji||
|Eksportuj dzienniki punktu końcowego||
|Importowanie wersji||
|Upublicznić aplikacji|Gdy aplikacja jest publiczny, każda osoba z kluczem tworzenia lub punkt końcowy kwerendy aplikacji.|
|Modyfikowanie modelu|
|Publikowanie|
|Przejrzyj wypowiedzi punktu końcowego dla [aktywne uczenie](luis-how-to-review-endpoint-utterances.md)|
|Szkolenie|

### <a name="prediction-endpoint-runtime-access"></a>Dostęp w czasie wykonywania przewidywania punktu końcowego

Dostęp do zapytania do punktu końcowego przewidywania jest kontrolowany przez ustawienie na stronie **Informacje o aplikacji** w sekcji **Zarządzanie** . 

![Zestaw aplikacji na publiczną](./media/luis-concept-security/set-application-as-public.png)

|[Prywatnych punktów końcowych](#runtime-security-for-private-apps)|[Publiczny punkt końcowy](#runtime-security-for-public-apps)|
|:--|:--|
|Dostępne dla właściciela i współautorów|Dostępne dla właściciela, współautorów i innych osób, które wiedzą o IDENTYFIKATORze aplikacji|

Można kontrolować, kto widzi klucz środowiska uruchomieniowego LUIS, wywołując go w środowisku serwer-serwer. Usługa LUIS korzystają z robota, połączenie między bot i LUIS jest już bezpieczne. Bezpośrednie wywoływanie punktu końcowego usługi LUIS należy utworzyć interfejs API po stronie serwera (takich jak Azure [funkcja](https://azure.microsoft.com/services/functions/)) przy użyciu kontrolowanego dostępu (takich jak [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy interfejs API po stronie serwera jest wywoływany i uwierzytelniany, a Autoryzacja jest weryfikowana, należy przekazać wywołanie do LUIS. Chociaż ta strategia nie zapobiega atakom typu man-in-the-Middle, zasłania klucz i adres URL punktu końcowego od użytkowników, umożliwia śledzenie dostępu i umożliwia dodanie rejestrowania odpowiedzi na punkt końcowy (na przykład [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla prywatnych aplikacji

Środowisko uruchomieniowe aplikacji prywatnej jest dostępne tylko dla następujących:

|Klucz i użytkownika|Wyjaśnienie|
|--|--|
|Klucz tworzenia właściciela| Maksymalnie 1000 punkt końcowy trafień|
|Współautorzy/klucze autorstwa| Maksymalnie 1000 punkt końcowy trafień|
|Dowolny klucz przypisany do LUIS przez autora lub współpracownika/współautora|Na podstawie warstwy użycia klucza|

#### <a name="runtime-security-for-public-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla aplikacji publicznych

Po skonfigurowaniu aplikacji jako publiczne, _wszelkie_ prawidłowe LUIS tworzenia klucza lub klucza punktu końcowego usługi LUIS mogą wysyłać zapytania swojej aplikacji, tak długo, jak klucz nie został użyty przydział całego punktu końcowego.

Użytkownik, który nie jest właścicielem lub współautorem, może uzyskać dostęp do środowiska uruchomieniowego aplikacji publicznej tylko wtedy, gdy podano identyfikator aplikacji. Usługa LUIS nie ma publicznej _rynku_ lub w inny sposób wyszukiwania dla aplikacji w sieci publicznej.  

Opublikowaniu publicznych aplikacji we wszystkich regionach, aby użytkownik z kluczem zależności od regionu zasobu usługi LUIS dostęp do aplikacji w niezależnie od regionu jest skojarzony z kluczem zasobów.

## <a name="transfer-of-ownership"></a>Przeniesienie prawa własności

**W przypadku [tworzenia aplikacji migrowanych zasobów](luis-migration-authoring.md)** : 

**W przypadku aplikacji, które nie zostały jeszcze zmigrowane**: Wyeksportuj aplikację jako plik JSON. Inny użytkownik LUIS może zaimportować aplikację, w związku z czym staje się właścicielem aplikacji. Nowa aplikacja będzie mieć inny identyfikator aplikacji.  

## <a name="securing-the-endpoint"></a>Zabezpieczanie punktu końcowego 

Można kontrolować, kto może wyświetlać klucz punktu końcowego środowiska uruchomieniowego LUIS prognoz, wywołując go w środowisku serwer-serwer. Usługa LUIS korzystają z robota, połączenie między bot i LUIS jest już bezpieczne. Bezpośrednie wywoływanie punktu końcowego usługi LUIS należy utworzyć interfejs API po stronie serwera (takich jak Azure [funkcja](https://azure.microsoft.com/services/functions/)) przy użyciu kontrolowanego dostępu (takich jak [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy wywoływany jest interfejs API po stronie serwera i uwierzytelnianie i autoryzacja są weryfikowane, Przekaż wywołań do usługi LUIS. Chociaż ta strategia nie zapobiegać atakom typu man-in--middle, zaciemnia punktu końcowego od swoich użytkowników, pozwala na śledzenie dostępu i umożliwia dodawanie rejestrowania odpowiedzi punktu końcowego (takie jak [usługi Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Następne kroki

* Zrozumienie [versioning](luis-concept-version.md) pojęcia. 
* Dowiedz się [, jak tworzyć klucze](luis-how-to-azure-subscription.md).
