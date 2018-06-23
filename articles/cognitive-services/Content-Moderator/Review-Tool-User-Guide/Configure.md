---
title: Skonfiguruj ustawienia narzędzia przeglądu zawartości moderatora | Dokumentacja firmy Microsoft
description: Skonfiguruj lub pobrać zespołu, tagi, łączniki, przepływy pracy i poświadczeń.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: a3432a1d8f424fbe78570f47b774c6e7942e16b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346956"
---
# <a name="about-review-tool-settings"></a>Ustawienia narzędzia przeglądu — informacje #

Na karcie Ustawienia na pulpicie nawigacyjnym narzędzie przeglądu, jest łatwy do definiowania i zmienić wiele składników.

![Ustawienia przeglądu moderatora zawartości](images/settings-1.png)

## <a name="team-and-subteams"></a>Zespołu i Subteams ## 

Zarządzanie zespołu i subteams na tej karcie. Może mieć tylko jeden zespół, ale można [tworzenie wielu subteams](subteams.md) i wysyłanie zaproszeń do elementów członkowskich w przyszłości. Po ma wysyłane zaprasza, można monitorować je, zmienianie uprawnień dla członków zespołu i zapraszanie innych użytkowników. Po członków zespołu został zatwierdzony zaproszenie, można przypisać te elementy członkowskie do różnych subteams. Można ustawić członków zespołu role administratorów lub osoby dokonujące przeglądu: Administratorzy mogą zapraszać innych użytkowników podczas nie osoby dokonujące przeglądu.

![Ustawienia zespołu moderatora zawartości](images/settings-2-team.png)

## <a name="tags"></a>Tagi ##

Jest to, gdzie można [Zdefiniuj niestandardowe tagi](tags.md) wprowadzając krótki kod, nazwę i opis dla tagów. Po jej utworzeniu, jest ona dostępna w recenzji. Tagi różne dla różnych przeglądami służą przez włączenie widoczności i Włącz.

![Ustawienia tagów moderatora zawartości](images/settings-3-tags.png)

## <a name="connectors"></a>Łączniki ##

Przepływy pracy Dodawanie funkcji za pomocą łączników do komunikowania się za pomocą narzędzia przeglądu. Narzędzie przeglądu wywołuje interfejsy API moderatora zawartości z domyślnego przepływu pracy dla moderowanie zawartości. Gdy zarejestrujesz się narzędzie przejrzyj go auto przepisy poświadczenia moderatora interfejsu API. Obsługuje ona również integrowanie innych łącznika interfejsów API, jak długo łącznika jest dostępna. Wprowadzono kilka łączników dostępne poza pole.

[Kartę łączniki](connectors.md) zarządzają łączników. Można dodać lub usunąć łączników i znaleźć klucza subskrypcji danego łącznika. Kliknij przycisk Połącz je dodać do niestandardowych przepływów pracy. 

![Ustawienia łączniki moderatora zawartości](images/settings-4-connectors.png)

## <a name="workflows"></a>Przepływy ##

Zarządzaj przepływami pracy, na karcie przepływów pracy. Przepływy pracy można przetestować, przekazując przykładowy plik. Możesz również [Zdefiniuj niestandardowe przepływy pracy](workflows.md) obrazu i tekstu przy użyciu dostępnych łączników interfejsu API (znajdujący się na karcie łączniki). 

![Ustawienia przepływu pracy moderatora zawartości](images/settings-5-workflows.png)

## <a name="credentials"></a>Poświadczenia ##

Ta karta zapewnia szybki dostęp do klucz subskrypcji moderatora zawartości, konieczne będzie korzystanie z interfejsów API, uwzględnione z moderatora zawartości (łagodzenia obrazu, tekst łagodzenia Zarządzanie listami, przepływu pracy i przejrzyj interfejsów API).
 
![Poświadczenia moderatora zawartości](images/settings-6-credentials.png)
