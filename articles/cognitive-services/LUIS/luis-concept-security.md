---
title: Zabezpieczenia podczas współpracy
titleSuffix: Language Understanding - Azure Cognitive Services
description: Tworzenie dostępu jest dostępna dla właścicieli i współpracowników. Dla aplikacji sieci prywatnej dostępu do punktu końcowego jest dostępna dla właścicieli i współpracowników.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 5abc2026362e19b406b34a4321e766c3229cc0b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894188"
---
# <a name="authoring-and-endpoint-user-access"></a>Dostęp użytkownika do tworzenia i punktu końcowego
Tworzenie dostępu jest dostępna dla właścicieli i współpracowników. Dla aplikacji sieci prywatnej dostępu do punktu końcowego jest dostępna dla właścicieli i współpracowników. W przypadku publicznych aplikacji dostęp do punktu końcowego jest dostępny dla wszystkich, ma swoje własne konto usługi LUIS, która zawiera identyfikator aplikacji publicznych. 

## <a name="access-to-authoring"></a>Dostęp do tworzenia
Dostęp do aplikacji z [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web lub [Tworzenie interfejsów API](https://aka.ms/luis-authoring-apis) jest kontrolowana przez właściciela aplikacji. 

Właściciela i współpracowników wszystkie mają dostęp do tworzenia aplikacji. 

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

## <a name="access-to-endpoint"></a>Dostęp do punktu końcowego

Dostęp do kwerendy punktu końcowego jest kontrolowany przez ustawienie na **informacje o aplikacji** strony w **Zarządzaj** sekcji. 

![Zestaw aplikacji na publiczną](./media/luis-concept-security/set-application-as-public.png)

|[Prywatnych punktów końcowych](#private-app-endpoint-security)|[Publiczny punkt końcowy](#public-app-endpoint-access)|
|:--|:--|
|Dostępne do właściciela i współpracowników|Dostępne dla właściciela, współpracowników i każdy else który wie, identyfikator aplikacji|

### <a name="private-app-endpoint-security"></a>Zabezpieczenia punktu końcowego aplikacji prywatnych

Punkt końcowy aplikacji prywatnych jest dostępna tylko dla następujących czynności:

|Klucz i użytkownika|Wyjaśnienie|
|--|--|
|Klucz tworzenia właściciela| Maksymalnie 1000 punkt końcowy trafień|
|Współpracowników tworzenia kluczy| Maksymalnie 1000 punkt końcowy trafień|
|Dowolny klawisz, przypisany do usługi LUIS przez autor lub Współautor|Na podstawie warstwy użycia klucza|

#### <a name="microsoft-user-accounts"></a>Konta użytkowników firmy Microsoft

Autorzy i współpracowników można przypisać klucze prywatne aplikacją usługi LUIS. Konto Microsoft, które tworzy klucz usługi LUIS w witrynie Azure portal musi być właścicielem aplikacji lub Współautor aplikacji. Nie można przypisać klucza prywatnego aplikacji z innego konta platformy Azure.

Zobacz [użytkownik dzierżawy usługi Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) Aby dowiedzieć się więcej na temat kont użytkowników usługi Active Directory. 

### <a name="public-app-endpoint-access"></a>Dostęp do punktu końcowego publicznego aplikacji

Po skonfigurowaniu aplikacji jako publiczne, _wszelkie_ prawidłowe LUIS tworzenia klucza lub klucza punktu końcowego usługi LUIS mogą wysyłać zapytania swojej aplikacji, tak długo, jak klucz nie został użyty przydział całego punktu końcowego.

Użytkownik, który nie jest właścicielem lub współpracownika, dostęp tylko do aplikacji publicznej Jeśli podany identyfikator aplikacji. Usługa LUIS nie ma publicznej _rynku_ lub w inny sposób wyszukiwania dla aplikacji w sieci publicznej.  

Opublikowaniu publicznych aplikacji we wszystkich regionach, aby użytkownik z kluczem zależności od regionu zasobu usługi LUIS dostęp do aplikacji w niezależnie od regionu jest skojarzony z kluczem zasobów.

## <a name="microsoft-user-accounts"></a>Konta użytkowników firmy Microsoft

Autorzy i współpracowników dodać klucze do usługi LUIS na stronie publikowania. Konto Microsoft, które tworzy klucz usługi LUIS w witrynie Azure portal musi być właścicielem aplikacji lub Współautor aplikacji. 

Zobacz [użytkownik dzierżawy usługi Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) Aby dowiedzieć się więcej na temat kont użytkowników usługi Active Directory. 

## <a name="securing-the-endpoint"></a>Zabezpieczanie punktu końcowego 

Można kontrolować, kto może wyświetlać klucza punktu końcowego usługi LUIS, wywołując ją w środowisku serwer serwer. Usługa LUIS korzystają z robota, połączenie między bot i LUIS jest już bezpieczne. Bezpośrednie wywoływanie punktu końcowego usługi LUIS należy utworzyć interfejs API po stronie serwera (takich jak Azure [funkcja](https://azure.microsoft.com/services/functions/)) przy użyciu kontrolowanego dostępu (takich jak [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy wywoływany jest interfejs API po stronie serwera i uwierzytelnianie i autoryzacja są weryfikowane, Przekaż wywołań do usługi LUIS. Chociaż ta strategia nie zapobiegać atakom typu man-in--middle, zaciemnia punktu końcowego od swoich użytkowników, pozwala na śledzenie dostępu i umożliwia dodawanie rejestrowania odpowiedzi punktu końcowego (takie jak [usługi Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Zgodności zabezpieczeń
 
[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>Kolejne kroki

Zobacz [najlepszych rozwiązań](luis-concept-best-practices.md) dowiesz się, jak na potrzeby prognoz najlepszych intencje i podmioty.
