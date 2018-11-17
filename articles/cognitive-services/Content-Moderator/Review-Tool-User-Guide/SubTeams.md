---
title: Zespoły i zespoły podrzędne Content Moderator interfejsu API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zespołów i zespoły podrzędne w Content Moderator interfejsu API dla usług Cognitive Services.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: 6e1fc08af1062ae8962ba33c6df980182175264b
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852150"
---
# <a name="team-and-subteams"></a>Zespół i zespoły podrzędne #

Przed rozpoczęciem korzystania z usługi Content Moderator, należy utworzyć zespół. Podczas logowania w górę, a nazwa Twojego zespołu, ten zespół będzie zespół domyślny. Może mieć tylko jeden zespół w narzędzia do przeglądu. Można jednak utworzyć wiele zespoły podrzędne. Zespoły podrzędne są przydatne do tworzenia eskalacji zespoły lub zespoły przeznaczone do przeglądania zawartości określonych kategorii. Na przykład możesz wysłać zawartość dla dorosłych do innego zespołu do przeglądu więcej.

W tym temacie wyjaśniono, jak utworzyć zespoły podrzędne i szybko przypisać recenzje na bieżąco. Można jednak użyć [przepływy pracy](workflows.md) można przypisać recenzji, w oparciu o określone kryteria.

## <a name="go-to-the-teams-setting"></a>Przejdź do zespołów ustawienie ##

Aby rozpocząć pracę na temat tworzenia ustalenie, wybierz **zespoły** opcję w obszarze Ustawienia.

![Ustawienia zespołu](images/0-teams-1.png)

## <a name="create-subteams"></a>Utwórz zespoły podrzędne ##

Zespół domyślny zawiera wszystkie potencjalne osób dokonujących przeglądu; zespoły podrzędne są podzbiorem zespół domyślny. Nie można przypisać ktoś ustalenie, jeśli nie są jeszcze członkowie zespołu domyślnego, dlatego musisz teraz dodać żadnych osób dokonujących przeglądu do zespołu domyślnego. Kliknij przycisk zaproszenia na stronie zespołu.

![Zaproś użytkowników](images/invite-users.png)

### <a name="1-create-a-subteam"></a>1. Utwórz ustalenie.
Przewiń w dół strony zespołu w sekcji ustalenie. Kliknij przycisk Dodaj ustalenie. 

![Dodaj ustalenie](images/1-teams-1.png)

### <a name="2-name-your-subteam"></a>2. Nazwa Twojej ustalenie.
Wprowadź nazwę ustalenie, w oknie dialogowym, a następnie kliknij przycisk Zapisz.

![Nazwa ustalenie](images/1-Teams-2.PNG)

### <a name="3-assign-members-from-your-default-team"></a>3. Przypisz elementy członkowskie zespołu domyślnego.
Kliknij przycisk Dodaj element członkowski, aby przypisać członków zespołu domyślnego do co najmniej jeden zespoły podrzędne. Na ustalenie można dodawać tylko istniejących użytkowników. Dodawanie nowych użytkowników, którzy nie znajdują się w narzędzie do przeglądu, Zaproś ich przy użyciu przycisku "Zaproś" na stronie ustawienia zespołu.

![Przypisz elementy członkowskie ustalenie](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>Przypisz przeglądów Twoje zespoły podrzędne ##

Po użytkownik ma Twoje zespoły podrzędne utworzone i przypisane członków zespołu, można uruchomić, przypisywanie obrazów i tekstu przeglądów te zespoły podrzędne. Można to zrobić w oknie przeglądu.
Przypisywanie poszczególnych obrazu do ustalenie kliknij wielokropek w prawym górnym rogu obrazu, wybierz opcję przejścia do i wybierz ustalenie.

![Przypisz Przegląd obrazu, aby subteam](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>Przełączanie między zespoły podrzędne, aby zapoznać się z przypisana zawartość ##

Jeśli jesteś członkiem co najmniej jeden zespoły podrzędne, można przełączać się między tymi zespoły podrzędne na pulpicie nawigacyjnym narzędzia do przeglądu. Aby wyświetlić wszystkie bieżące oczekujących przeglądów należących do ustalenie, wybierz wybierz ustalenie, na karcie obraz.

![Przełączanie między zespoły podrzędne](images/3-review-image-subteam-2.png)
