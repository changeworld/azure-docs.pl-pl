---
title: Zespoły i subteams w interfejsie API zawartości moderatora | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zespołów i subteams w interfejsie API zawartości moderatora kognitywnych usług.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: 161c7cd8bac07d5ffc138297d98a40317a8d88fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346977"
---
# <a name="team-and-subteams"></a>Zespołu i Subteams #

Przed użyciem moderatora zawartości, należy utworzyć zespół. Podczas logowania w górę, a nazwa Twojego zespołu, tego zespołu staną się zespołu domyślne. W narzędziu przeglądu może mieć tylko jeden zespół. Można jednak utworzyć wiele subteams. Subteams są przydatne do tworzenia zespołów Eskalacja lub zespołów przeznaczona do przeglądania zawartości określonych kategorii. Można na przykład wysłać zawartość dla dorosłych na inny zespół do przeglądu więcej.

W tym temacie wyjaśniono, jak utworzyć subteams i szybko przypisać przeglądami na bieżąco. Można jednak użyć [przepływy pracy](workflows.md) można przypisać przeglądami na podstawie określonych kryteriów.

## <a name="go-to-the-teams-setting"></a>Przejdź do zespołów ustawienie ##

Aby rozpocząć tworzenia ustalenie, wybierz **zespołów** opcję w obszarze Ustawienia.

![Ustawień zespołu](images/0-teams-1.png)

## <a name="create-subteams"></a>Utwórz subteams ##

Domyślny zawiera wszystkie osoby dokonujące przeglądu potencjalnych; subteams są podzbiorami domyślnego zespołu. Nie można przypisać ktoś ustalenie, jeśli nie są już członkami zespołu domyślne, należy dodać wszystkie osoby dokonujące przeglądu do zespołu domyślne teraz. Kliknij przycisk zaproszenia na stronie zespołu.

![Zaprosić użytkowników](images/invite-users.png)

### <a name="1-create-a-subteam"></a>1. Utwórz ustalenie.
Przewiń w dół strony zespołu do sekcji ustalenie. Kliknij przycisk Dodaj ustalenie. 

![Dodaj ustalenie](images/1-teams-1.png)

### <a name="2-name-your-subteam"></a>2. Nazwa użytkownika ustalenie.
Wprowadź nazwę ustalenie, w oknie dialogowym, a następnie kliknij przycisk Zapisz.

![Nazwa ustalenie](images/1-Teams-2.PNG)

### <a name="3-assign-members-from-your-default-team"></a>3. Przypisz elementy członkowskie z zespołem domyślnej.
Kliknij przycisk Dodaj element członkowski w celu przypisania członków zespołu domyślne do subteams co najmniej jeden. Istniejący użytkownicy można dodawać tylko do ustalenie. Dodawanie nowych użytkowników, którzy nie znajdują się w narzędziu przeglądu, poproś przy użyciu przycisku "Zaproszenia" na stronie ustawień zespołu.

![Przypisz ustalenie elementy członkowskie](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>Przypisz przeglądami do Twojej subteams ##

Raz masz Twojej subteams utworzone i przypisania członków zespołu, możesz uruchomić Przypisywanie obrazu i tekstu przegląda do tych subteams. Można to zrobić w oknie przeglądu.
Można przypisać poszczególnych obrazów na ustalenie, kliknij przycisk wielokropka w prawym górnym rogu obrazu, wybierz, aby przejść do i wybierz ustalenie.

![Przypisz subteam przeglądu obrazu](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>Przełączanie między subteams do przeglądania zawartości przypisanej ##

Jeśli jesteś członkiem co najmniej jeden subteams można przełączać się między tych subteams z narzędzia pulpitu nawigacyjnego przeglądu. Aby wyświetlić wszystkie bieżące oczekujących przeglądami należących do ustalenie, wybierz wybierz ustalenie na karcie obraz.

![Przełączanie między subteams](images/3-review-image-subteam-2.png)
