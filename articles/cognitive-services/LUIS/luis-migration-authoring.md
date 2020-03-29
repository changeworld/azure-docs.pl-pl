---
title: Migrowanie do zasobu platformy Azure w celu tworzenia
titleSuffix: Azure Cognitive Services
description: Migrowanie do klucza zasobów tworzenia platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194513"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrowanie do klucza tworzenia zasobów platformy Azure

Uwierzytelnianie tworzenia języka (LUIS) zmieniono z konta e-mail na zasób platformy Azure. Chociaż obecnie nie jest to wymagane, przejście na zasób platformy Azure będzie wymuszane w przyszłości.

## <a name="why-migrate"></a>Dlaczego warto przeprowadzić migrację?

Za pomocą zasobu platformy Azure do tworzenia umożliwia, jako właściciel zasobu, aby kontrolować dostęp do tworzenia. Można tworzyć i nadać nazwy zasobom tworzenia, aby zarządzać różnymi grupami autorów.

Na przykład jesteś właścicielem 2 aplikacji usługi LUIS i masz różnych członków, którzy są współpracownikami w każdej aplikacji. Można utworzyć dwa różne zasoby tworzenia i przypisać każdą aplikację do każdego oddzielnego zasobu. Następnie przypisz każdemu członkowi jako współautora do odpowiedniego zasobu autora w zależności od aplikacji, nad którą współpracuje. Zasób tworzenia platformy Azure steruje autoryzacją.

> [!Note]
> Przed migracją współautorzy są znani jako _współpracownicy_ na poziomie aplikacji usługi LUIS. Po migracji rola _współautora_ platformy Azure jest używana dla tej samej funkcji, ale na poziomie zasobów platformy Azure.

## <a name="what-is-migrating"></a>Co to jest migracja?

Migracja obejmuje:

* Wszyscy użytkownicy usługi LUIS, właściciele i współautorzy.
* **Wszystkie** aplikacje.
* Migracja **jednokierunkowa.**

Właściciel nie może wybrać podzbioru aplikacji do migracji, a proces nie jest odwracalny.

Migracja nie jest:

* Proces, który zbiera współpracowników i automatycznie przenosi lub dodaje do zasobu tworzenia platformy Azure. Ty, jako właściciel aplikacji, musisz wykonać ten krok. Ten krok wymaga uprawnień do odpowiedniego zasobu.
* Proces tworzenia i przypisywania zasobu środowiska uruchomieniowego prognozowania. Jeśli potrzebujesz zasób środowiska uruchomieniowego przewidywanie, który jest [oddzielny proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) i pozostaje bez zmian.

## <a name="how-are-the-apps-migrating"></a>W jaki sposób aplikacje są migrowane?

Portal [usługi LUIS](https://www.luis.ai) udostępnia proces migracji.

Zostaniesz poproszony o migrację, jeśli:

* Masz aplikacje w systemie uwierzytelniania poczty e-mail do tworzenia.
* I jesteś właścicielem aplikacji.

Proces migracji można opóźnić, anulując okno. Okresowo jest proszony o migrację do czasu migracji lub upływu terminu migracji. Proces migracji można rozpocząć od ikony blokady górnego paska nawigacyjnego.

## <a name="migration-for-the-app-owner"></a>Migracja dla właściciela aplikacji

### <a name="before-you-migrate"></a>Przed migracją

* **Wymagane**, musisz mieć [subskrypcję platformy Azure](https://azure.microsoft.com/free/). Część procesu subskrypcji wymaga informacji rozliczeniowych. Jednak można użyć bezpłatnej`F0`( ) warstwy cenowej podczas korzystania z usługi LUIS.
* **Opcjonalnie**utwórz kopię zapasową aplikacji z listy aplikacji portalu usługi LUIS, eksportując każdą aplikację lub korzystając z interfejsu [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)eksportu .
* **Opcjonalnie**zapisz listę współpracowników każdej aplikacji. Ta lista wiadomości e-mail jest dostarczana jako część procesu migracji.


**Tworzenie aplikacji usługi LUIS jest bezpłatne,** wskazane przez warstwę. `F0` Dowiedz się [więcej o warstwach cenowych](luis-boundaries.md#key-limits).

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się](https://azure.microsoft.com/free/).

### <a name="migration-steps"></a>Kroki migracji

Wykonaj [następujące kroki migracji](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Po migracji

Po zakończeniu procesu migracji wszystkie aplikacje usługi LUIS są teraz przypisane do jednego zasobu tworzenia usługi LUIS.

Można utworzyć więcej zasobów tworzenia i przypisać z **zarządzania -> zasobów platformy Azure** strony w _portalu usługi LUIS_.

Współautorów można dodać do zasobu autora z _witryny Azure portal_, na stronie **kontroli dostępu (IAM)** dla tego zasobu. Aby uzyskać więcej informacji, zobacz [dodawanie dostępu współautora](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

|Portal|Przeznaczenie|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Tworzenie prognozowania i tworzenia zasobów.<br>* Przypisz współautorów.|
|[LUIS](https://www.luis.ai)|* Migruj do nowych zasobów tworzenia.<br>* Przypisywanie lub niespisywanie zasobów do przewidywania i tworzenia zasobów do aplikacji z **witryny Zarządzanie zasobami platformy -> Azure.**|

## <a name="migration-for-the-app-contributor"></a>Migracja dla współautora aplikacji

Każdy użytkownik usługi LUIS musi przeprowadzić migrację, w tym współpracowników/współautorów. Współpracownik musi przeprowadzić migrację, aby mieć dostęp do aplikacji.

> [!Note]
> Jeśli właściciel aplikacji usługi LUIS zmigrował i dodał współpracownika jako współautora zasobu platformy Azure, współpracownik nadal nie będzie miał dostępu do aplikacji, chyba że również migrują.

### <a name="before-the-app-is-migrated"></a>Przed migracją aplikacji

Możesz wyeksportować aplikację, na której jesteś współpracownikiem, a następnie zaimportować aplikację z powrotem do usługi LUIS. Proces importowania tworzy nową aplikację z nowym identyfikatorem aplikacji, którego jesteś właścicielem.

### <a name="after-the-app-is-migrated"></a>Po migracji aplikacji

Właściciel aplikacji musi [dodać pocztę e-mail do zasobu tworzenia platformy Azure jako współpracownik](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource).

Po zakończeniu procesu migracji wszystkie posiadane aplikacje są dostępne na stronie **Moje aplikacje** w portalu usługi LUIS.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Rozwiązywanie problemów z procesem migracji do tworzenia usługi LUIS

* Klucze tworzenia usługi LUIS są widoczne tylko w portalu usługi LUIS po zakończeniu procesu migracji. Jeśli utworzysz klucze tworzenia, takie jak interfejs wiersza polecenia usługi LUIS, użytkownik nadal musi ukończyć proces migracji w portalu usługi LUIS.
* Jeśli zmigrowany użytkownik doda niezmiagrowanego użytkownika jako współautora w zasobie platformy Azure, niezmigrowany użytkownik nie będzie miał dostępu do aplikacji, chyba że zostanie zmigrowany.
* Jeśli użytkownik niezmigrowany nie jest właścicielem do żadnych aplikacji, ale jest współpracownikiem innych aplikacji, które są własnością innych osób, a właściciele przeszli proces migracji, ten użytkownik będzie musiał przeprowadzić migrację, aby mieć dostęp do aplikacji.
* Jeśli niezmigrowany użytkownik dodał innego zmigrowanego użytkownika jako współpracownika do swojej aplikacji, wystąpi błąd, ponieważ nie będzie można dodać zmigrowanego użytkownika jako współpracownika do aplikacji. Niezmigrowany użytkownik będzie musiał przejść przez proces migracji i utworzyć zasób platformy Azure i dodać zmigrowanego użytkownika jako współautora tego zasobu.

Podczas procesu migracji pojawia się błąd, jeśli:
* Subskrypcja nie upoważnia użytkownika do tworzenia zasobów usług Cognitive Services
* Migracja negatywnie wpływa na środowisko uruchomieniowe aplikacji. Podczas migracji wszyscy współpracownicy są usuwani z twoich aplikacji i użytkownik jest usuwany jako współpracownik z innych aplikacji. Ten proces oznacza, że przypisane klucze również zostaną usunięte. Migracja zostanie zablokowana, jeśli przypisano klucze w innych aplikacjach. Usuń klucz przypisany bezpiecznie przed migracją. Jeśli wiesz, że przypisany klucz nie jest używany w czasie wykonywania, należy go usunąć, aby móc przejść do migracji.

Dostęp do listy zasobów platformy Azure aplikacji przy użyciu następującego formatu adresu URL:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>Następne kroki

* [Jak przeprowadzić migrację aplikacji do zasobu autora](luis-migration-authoring-steps.md)
