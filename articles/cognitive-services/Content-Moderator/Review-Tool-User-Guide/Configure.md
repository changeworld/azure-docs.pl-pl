---
title: Konfiguruj ustawienia narzędzia przeglądu Content Moderator | Dokumentacja firmy Microsoft
description: Konfigurowanie lub zespołu, tagi, łączniki, przepływy pracy i poświadczeń.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 2ffed5e561cf9988ec10ecb7e10318d148281057
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2018
ms.locfileid: "51851542"
---
# <a name="review-tool-settings"></a>Przejrzyj ustawienia narzędzia #

Na karcie ustawień na pulpicie nawigacyjnym narzędzia przeglądu, jest łatwy do definiowania i zmienić wiele składników.

![Content Moderator Przejrzyj ustawienia](images/settings-1.png)

## <a name="team-and-subteams"></a>Zespół i zespoły podrzędne ## 

Zarządzanie zespołu i zespoły podrzędne za pomocą tej karty. Może mieć tylko jeden zespół, ale możesz [tworzenie wielu zespoły podrzędne](subteams.md) i Wyślij zaproszenia do elementów członkowskich w przyszłości. Po zaproszeń zostały wysłane, można monitorować je, zmienić uprawnienia dla członków zespołu i zapraszać innych użytkowników. Po członkowie zespołu mają zaakceptował Twoje zaproszenie, można przypisać te elementy członkowskie, aby różne zespoły podrzędne. Możesz ustawić ról członków zespołu do administratorów lub recenzentów: Administratorzy mogą zapraszać innych użytkowników, gdy recenzenci nie.

![Content Moderator ustawienia zespołu](images/settings-2-team.png)

## <a name="tags"></a>Tagi ##

Jest to, gdzie można [zdefiniować niestandardowe tagi](tags.md) , wprowadzając krótki kod, nazwę i opis dla swoich tagów. Po jej utworzeniu, jest ona dostępna podczas przeglądów. Przez włączenie widoczności i Włącz, można użyć różnych znaczników dla różnych recenzji.

![Pakiet Content Moderator tagów ustawienia](images/settings-3-tags.png)

## <a name="connectors"></a>Łączniki ##

Przepływy pracy Dodawanie funkcji za pomocą łączników do komunikowania się za pomocą narzędzia do przeglądu. Narzędzie do przeglądu wywołuje interfejsy API Moderator zawartości przy użyciu domyślnego przepływu pracy dla moderowanie zawartości. Gdy rejestrujesz się narzędzie do przeglądu go auto przepisy poświadczenia Moderator, interfejsu API. Obsługuje ona również integracji innych interfejsów API, łącznika tak długo, jak łącznik jest dostępna. Udostępniliśmy kilka łączników poza pole.

[Kartą łączników](connectors.md) zarządzają łączników. Można dodać lub usuwanie łączników i znaleźć swój klucz subskrypcji dla określonego łącznika. Kliknij pozycję Połącz, aby dodać je do niestandardowych przepływów pracy. 

![Moderator łączników ustawienia zawartości](images/settings-4-connectors.png)

## <a name="workflows"></a>Przepływy ##

Zarządzaj przepływami pracy, na karcie przepływów pracy. Aby przetestować przepływów pracy, przekazywanie przykładowego pliku. Możesz również [Definiowanie niestandardowych przepływów pracy](workflows.md) obrazów i tekstu przy użyciu dostępnych łączników interfejsu API (znajdujący się na karcie łączniki). 

![Content Moderator ustawienia przepływu pracy](images/settings-5-workflows.png)

## <a name="credentials"></a>Poświadczenia ##

Ta karta zapewnia szybki dostęp do klucz subskrypcji pakietu Content Moderator, musisz użyć uwzględnione interfejsy API pakietu Content moderator (Moderowanie obrazów, Moderowanie tekstu, zarządzanie listami, przepływu pracy i przeglądu interfejsów API).
 
![Poświadczenia usługi Content Moderator](images/settings-6-credentials.png)
