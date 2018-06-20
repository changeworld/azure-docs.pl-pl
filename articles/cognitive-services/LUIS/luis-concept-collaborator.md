---
title: Zrozumienie LUIS aplikacji współpracy - Azure | Dokumentacja firmy Microsoft
description: LUIS aplikacje wymagają jednego właściciela i opcjonalnie współpracowników.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 51b3958f83cd110ace27f6ee42571c05843f5aa2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265069"
---
# <a name="collaborating"></a>Współpraca

LUIS zapewnia współpracy, aby umożliwić grupa osób do tworzenia aplikacji.

## <a name="luis-account"></a>LUIS konta
Konto LUIS jest skojarzone z jedną [Microsoft Live](https://login.live.com/) konta. Każde konto LUIS otrzymuje bezpłatny [tworzenia klucza](luis-concept-keys.md#authoring-key) do użycia na potrzeby tworzenia aplikacji LUIS konto ma dostęp do. 

Konto LUIS może mieć wiele LUIS aplikacji.

## <a name="luis-app-owner"></a>Właściciel aplikacji LUIS
Konto, które tworzy aplikację jest właścicielem. Każda aplikacja ma jednego właściciela. Właściciel znajduje się w aplikacji  **[ustawienia](luis-how-to-collaborate.md)**. To konto, które można usunąć aplikacji. Jest to również konta, które otrzymuje wiadomość e-mail po 75% miesięcznego limitu przydziału punktu końcowego. 

## <a name="transfer-ownership"></a>Przenoszenie własności
LUIS nie zapewnia przeniesienie własności, jednak żadnych współpracownika wyeksportować aplikację, a następnie utwórz aplikację przez zaimportowanie. Należy pamiętać, że nowa aplikacja ma inny identyfikator aplikacji. Nowych potrzeb aplikacji do uczenia, opublikowana, a nowy punkt końcowy, używane.

## <a name="luis-app-collaborators"></a>LUIS współpracownicy aplikacji
Właściciel aplikacji można dodać współpracowników do aplikacji. Właściciel musi dodać współpracownika adres e-mail w aplikacji  **[ustawienia](luis-how-to-collaborate.md)**. Współautor ma pełny dostęp do aplikacji. Jeśli współpracownika usuwa aplikację, aplikacja zostanie usunięty z konta współpracownika, ale pozostaje w właściciela konta. 

Jeśli chcesz udostępnić wielu aplikacjom współpracownicy każdej aplikacji musi e-mail współpracownika dodane. 

## <a name="managing-multiple-authors"></a>Zarządzanie wielu autorów
[LUIS] [ LUIS] witryny sieci Web aktualnie nie oferuje poziomu transakcji tworzenia. Umożliwia autorom działają w wersjach niezależne od podstawowej wersji. W poniższych sekcjach opisano dwie różne metody.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Zarządzanie wieloma wersjami w tej samej aplikacji
Rozpocznij od [klonowania](luis-how-to-manage-versions.md#clone-a-version), z wersji podstawowej, dla każdego autora. 

Autor każdego zmienia jego wersji aplikacji. Po uzyskaniu modelu każdego autora wyeksportować nowe wersje plików JSON.  

Wyeksportowane aplikacje są w formacie JSON pliki, które można porównać zmiany. Łączenie plików w celu utworzenia nowej wersji pojedynczy plik JSON. Zmień **versionId** właściwości w formacie JSON oznaczającego nowej wersji scalone. Importuj tej wersji w oryginalnej aplikacji. 

Ta metoda umożliwia jednej aktywnej wersji, jednej wersji etap i jeden opublikowanej wersji. Możesz porównać wyniki w okienku testowania interakcyjnego w trzech wersjach.

### <a name="manage-multiple-versions-as-apps"></a>Zarządzanie wieloma wersjami jako aplikacje
[Eksportuj](luis-how-to-manage-versions.md#export-version) wersja podstawowa. Autor każdego importuje wersji. Osoby, który importuje aplikacji jest właścicielem wersji. Po ich zakończeniu modyfikowania aplikacji, eksportowanie wersji. 

Wyeksportowane aplikacje są pliki formacie JSON, które mogą zostać porównane z podstawowej eksportu zmian. Łączenie plików w celu utworzenia nowej wersji pojedynczy plik JSON. Zmień **versionId** właściwości w formacie JSON oznaczającego nowej wersji scalone. Importuj tej wersji w oryginalnej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Zrozumienie [versioning](luis-concept-version.md) pojęcia. 

Zobacz [ustawień aplikacji](luis-how-to-collaborate.md) Aby dowiedzieć się, jak zarządzać współpracownicy w aplikacji LUIS.

Zobacz [Dodaj do listy dostępu do poczty e-mail](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) z interfejsami API tworzenia.

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website