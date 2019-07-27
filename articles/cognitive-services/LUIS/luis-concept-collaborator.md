---
title: Współpraca — LUIS
titleSuffix: Azure Cognitive Services
description: Usługa LUIS aplikacje wymagają jednego właściciela i współpracowników opcjonalne zezwalanie wielu użytkownikom tworzenie pojedynczej aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: diberry
ms.openlocfilehash: e53ba9f24e40837a823e6012340358113d490f46
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560784"
---
# <a name="collaborating-with-other-authors"></a>Współpraca z innymi autorami

Usługa LUIS aplikacje wymagają jednego właściciela i współpracowników opcjonalne zezwalanie wielu użytkownikom tworzenie pojedynczej aplikacji.

## <a name="luis-account"></a>Konto usługi LUIS
Konto usługi LUIS jest skojarzone z pojedynczym [Microsoft Live](https://login.live.com/) konta. Każde konto usługi LUIS otrzymuje bezpłatne [tworzenia klucza](luis-concept-keys.md#authoring-key) służące do tworzenia aplikacji usługi LUIS to konto ma dostęp do. 

Konto usługi LUIS może mieć wiele aplikacji usługi LUIS.

Zobacz [użytkownik dzierżawy usługi Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) Aby dowiedzieć się więcej na temat kont użytkowników usługi Active Directory. 

## <a name="luis-app-owner"></a>Właściciel aplikacji usługi LUIS

Konto, które tworzy aplikację, jest właścicielem, a każda aplikacja ma jednego właściciela. Właściciel znajduje się na liście na stronie **[Ustawienia](luis-how-to-collaborate.md)** aplikacji. Właściciel otrzymuje wiadomość e-mail, gdy limit przydziału punktu końcowego osiągnie 75% limitu miesięcznie. 

## <a name="authorization-roles"></a>Role autoryzacji
Usługa LUIS nie obsługuje różne role dla właścicieli i współpracowników z jednym wyjątkiem. Właściciel jest to jedyne konto, które można usunąć aplikacji.

Jeśli interesuje Cię w kontrolowaniu dostępu do modelu, należy wziąć pod uwagę model tworzenia wycinków w mniejszych aplikacje usługi LUIS, gdzie każda mniejszych aplikacja ma bardziej ograniczony zestaw współpracowników. Użyj [wysyłania](https://aka.ms/dispatch-tool) umożliwia nadrzędnego aplikacją usługi LUIS do zarządzania koordynacji między aplikacjami nadrzędnymi i podrzędnymi.

## <a name="transfer-ownership"></a>Przenoszenie własności
Usługa LUIS nie zapewnia przeniesienie prawa własności, jednak żadnych współpracownika wyeksportować aplikację, a następnie utwórz aplikację przez zaimportowanie. Należy pamiętać, że nowa aplikacja ma inny identyfikator aplikacji. Nowych potrzeb aplikacji ma być uczony, opublikowane, a nowy punkt końcowy używane.

## <a name="luis-app-collaborators"></a>Współpracownicy aplikacji usługi LUIS
Właściciel aplikacji można dodać współpracowników do aplikacji. Właściciel musi dodać adres e-mail współpracownika aplikacji  **[ustawienia](luis-how-to-collaborate.md)** . Współpracownik ma pełny dostęp do aplikacji. Jeśli współpracownik usuwa aplikację, aplikacja zostanie usunięta z konta współpracownika, ale pozostaje w właściciela konta. 

Jeśli chcesz udostępnić wiele aplikacji wraz ze współpracownikami, każda aplikacja wymaga dodano adres e-mail współpracownika. 

## <a name="managing-multiple-authors"></a>Zarządzanie wieloma autorów
[LUIS](luis-reference-regions.md#luis-website) witryny sieci Web obecnie nie oferuje poziomu transakcji tworzenia. Umożliwia autorom działają w wersjach niezależne od podstawowej wersji. W poniższych sekcjach opisano dwie różne metody.

## <a name="manage-multiple-versions-inside-the-same-app"></a>Zarządzanie wieloma wersjami wewnątrz tej samej aplikacji
Rozpocznij od [klonowania](luis-how-to-manage-versions.md#clone-a-version), z wersji podstawowy, dla każdego autora. 

Każdy autor wprowadza zmiany w swojej wersji aplikacji. Po każdego autora jest zadowolony z modelu, należy wyeksportować nowe wersje plików JSON.  

Wyeksportowane aplikacje są w formacie JSON — pliki, które można porównać zmiany. Połącz pliki, aby utworzyć pojedynczy plik JSON w nowej wersji. Zmiana **versionId** właściwości w formacie JSON oznaczającego nowej wersji scalone. Zaimportować tej wersji oryginalnej aplikacji. 

Ta metoda umożliwia jednej wersji aktywnej, jednej wersji etapu i jeden opublikowanej wersji. Możesz porównać wyniki w okienku testowania interakcyjnego w trzech wersjach.

## <a name="manage-multiple-versions-as-apps"></a>Zarządzanie wieloma wersjami jako aplikacje
[Eksportuj](luis-how-to-manage-versions.md#export-version) wersja podstawowa. Każdego autora importuje wersji. Osoby, które importuje aplikacja jest właścicielem wersji. Po ich zakończeniu modyfikowania aplikacji, eksportowanie wersji. 

Wyeksportowane aplikacje są sformatowanego JSON pliki, które można porównać z podstawowej eksportu dla zmian. Połącz pliki, aby utworzyć pojedynczy plik JSON w nowej wersji. Zmiana **versionId** właściwości w formacie JSON oznaczającego nowej wersji scalone. Zaimportować tej wersji oryginalnej aplikacji.

## <a name="collaborator-roles-vs-entity-roles"></a>Role współpracownika — role

[Role jednostek](luis-concept-roles.md) są stosowane do modelu danych aplikacji Luis. Role współpracowników dotyczą poziomów dostępu do autorstwa. 

## <a name="next-steps"></a>Następne kroki

Zrozumienie [versioning](luis-concept-version.md) pojęcia. 

Zobacz [ustawienia aplikacji](luis-how-to-collaborate.md) dowiesz się, jak zarządzać współpracowników w aplikacji usługi LUIS.

Zobacz [Dodaj adres e-mail do listy dostępu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) za pomocą interfejsów API tworzenia.
