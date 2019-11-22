---
title: Migrowanie do zasobu platformy Azure w celu tworzenia
titleSuffix: Azure Cognitive Services
description: Przeprowadź migrację do klucza zasobu tworzenia platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 44baac5eb4e8887594ba05498901ba664380005f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280770"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrowanie do klucza tworzenia zasobów platformy Azure

Language Understanding (LUIS) uwierzytelnianie autorstwa zostało zmienione z konta e-mail na zasób platformy Azure. Gdy obecnie nie jest to wymagane, przełączenie na zasób platformy Azure zostanie wymuszone w przyszłości.

## <a name="why-migrate"></a>Dlaczego warto przeprowadzić migrację?

Przy użyciu zasobu platformy Azure do tworzenia można jako właściciel zasobu kontrolować dostęp do tworzenia. Można utworzyć i nazwać zasoby tworzenia do zarządzania różnymi grupami autorów. 

Jeśli na przykład masz 2 typy tworzonych aplikacji LUIS z różnymi elementami członkowskimi, możesz utworzyć dwa różne zasoby tworzenia i przypisać współautorów. Zasób tworzenia platformy Azure kontroluje autoryzację. 

> [!Note]
> Przed rozpoczęciem migracji Współautorzy są znani jako _współpracownicy_. Po przeprowadzeniu migracji rola _współautora_ systemu Azure jest używana do obsługi tych samych funkcji.

## <a name="what-is-migrating"></a>Co to jest migracja?

Migracja obejmuje:

* Wszyscy użytkownicy LUIS, właściciele i współautorzy.
* **Wszystkie** aplikacje.
* Migracja **jednokierunkowa** .

Właściciel nie może wybrać podzestawu aplikacji do migracji, a proces nie jest odwracalny. 

Migracja nie jest: 

* Proces, który zbiera współpracowników i automatycznie przenosi lub dodaje do zasobu tworzenia platformy Azure. Jako właściciel aplikacji musisz wykonać ten krok. Ten krok wymaga uprawnień do odpowiedniego zasobu.
* Proces tworzenia i przypisywania zasobu środowiska uruchomieniowego predykcyjnego. Jeśli potrzebujesz zasobu przewidywania środowiska uruchomieniowego, który jest [oddzielnym procesem](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) i nie jest zmieniany. 

## <a name="how-are-the-apps-migrating"></a>Jak są migrowane aplikacje?

[Portal Luis](https://www.luis.ai) zawiera proces migracji. 

Zostanie wyświetlony monit o przeprowadzenie migracji, jeśli:

* Masz aplikacje w systemie uwierzytelniania poczty e-mail na potrzeby tworzenia.
* I jesteś właścicielem aplikacji. 

Można opóźnić proces migracji, anulując okno. Użytkownik jest okresowo monitowany o migrację do momentu przeprowadzenia migracji lub przekazanie ostatecznego terminu migracji. Proces migracji można rozpocząć od ikony blokady górnego paska nawigacyjnego.

## <a name="migration-for-the-app-owner"></a>Migracja dla właściciela aplikacji

### <a name="before-you-migrate"></a>Przed przeprowadzeniem migracji

* **Opcjonalnie możesz**utworzyć kopię zapasową aplikacji z listy aplikacji portalu Luis, eksportując każdą aplikację lub korzystając z [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)eksportowania.
* **Opcjonalnie**Zapisz listę collaborator's każdej aplikacji. Ta lista poczty e-mail jest świadczona jako część procesu migracji.
* **Wymagana**jest [subskrypcja platformy Azure](https://azure.microsoft.com/free/). Część procesu subskrypcji wymaga informacji dotyczących rozliczeń. Można jednak użyć warstwy cenowej bezpłatna (`F0`) w przypadku korzystania z LUIS. 

**Tworzenie aplikacji Luis jest bezpłatne**, wskazywane przez warstwę `F0`. Dowiedz się [więcej o warstwach cenowych](luis-boundaries.md#key-limits).

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się](https://azure.microsoft.com/free/). 

### <a name="migration-steps"></a>Kroki migracji

Postępuj zgodnie z [tymi krokami migracji](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Po migracji 

Po zakończeniu migracji wszystkie Twoje aplikacje LUIS są teraz przypisane do pojedynczego zasobu tworzenia LUIS.

Można utworzyć więcej zasobów autorstwa i przypisać je na stronie **zarządzaj > zasobów platformy Azure** w _portalu Luis_. 

Współautorzy można dodać do zasobu tworzenia z _Azure Portal_na stronie **Access Control (IAM)** dla tego zasobu. Aby uzyskać więcej informacji, zobacz [Dodawanie dostępu współautora](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource) .

|Portal|Przeznaczenie|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Tworzenie zasobów przewidywania i tworzenia.<br>* Przypisz współautorów.|
|[LUIS](https://www.luis.ai)|* Przeprowadź migrację do nowych zasobów tworzenia.<br>* Przypisz lub Cofnij przypisanie zasobów prognozowania i tworzenia do aplikacji z **zarządzania > stronie zasobów platformy Azure** .| 

## <a name="migration-for-the-app-contributor"></a>Migracja dla współautora aplikacji

Każdy użytkownik programu LUIS musi przeprowadzić migrację, w tym współpracowników/współautorów. 

### <a name="before-the-app-is-migrated"></a>Przed przeprowadzeniem migracji aplikacji

Możesz wyeksportować aplikację, której jesteś współpracownikiem, a następnie zaimportować aplikację z powrotem do LUIS. Proces importowania tworzy nową aplikację z nowym IDENTYFIKATORem aplikacji, dla którego jesteś właścicielem.

### <a name="after-the-app-is-migrated"></a>Po migracji aplikacji

Właściciel aplikacji musi [dodać swój adres e-mail do zasobu tworzenia platformy Azure jako współpracownika](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource). 

Po zakończeniu procesu migracji wszystkie posiadane aplikacje są dostępne na stronie **Moje aplikacje** w portalu Luis.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Klucze tworzenia LUIS są widoczne tylko w portalu LUIS po zakończeniu procesu migracji. Jeśli utworzysz klucze tworzenia, na przykład w interfejsie wiersza polecenia LUIS, użytkownik nadal musi ukończyć proces migracji. 

## <a name="next-steps"></a>Następne kroki

* [Jak przeprowadzić migrację aplikacji do zasobu tworzenia](luis-migration-authoring-steps.md)