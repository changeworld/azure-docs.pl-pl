---
title: Usługa Azure Notification Hubs, a także możliwość migracji Google Firebase Cloud Messaging (FCM)
description: W tym artykule opisano, jak usługi Azure Notification Hubs eliminuje Google GCM do migracji usługi FCM.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787019"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Usługa Azure Notification Hubs, a także możliwość migracji Google Firebase Cloud Messaging (FCM)

## <a name="current-state"></a>Bieżący stan

Gdy Google ogłosiła jego migracji do usługi Firebase Cloud Messaging (FCM), usług powiadomień wypychanych, takich jak nasza musiała dostosować, jak firma Microsoft wysłano powiadomienia do urządzeń z systemem Android w celu uwzględnienia zmiany z Google Cloud Messaging (GCM).

Możemy zaktualizować naszej usługi zaplecza, a następnie opublikowane aktualizacje dotyczące naszych interfejsów API i zestawów SDK odpowiednio do potrzeb. W przypadku naszej implementacji wprowadziliśmy decyzję, aby zachować zgodność z istniejących schematów powiadomienia usługi GCM, aby zminimalizować wpływ na klientów. Oznacza to, firma Microsoft obecnie wysyłanie powiadomień do urządzeń z systemem Android przy użyciu usługi FCM w trybie starszej wersji usługi FCM. Ostatecznie chcemy dodać true obsługi usługi FCM, takie jak nowe funkcje i format ładunku. To zmiana długoterminowych i bieżącej migracji koncentruje się na zachowaniu zgodności z istniejącymi aplikacjami i zestawów SDK. W aplikacji (wraz z naszego zestawu SDK) mogą używać usługi GCM lub zestawów SDK usługi FCM i możemy zagwarantować, że powiadomienie jest wysyłane poprawnie.

Niektórzy klienci ostatnio odebrała wiadomość e-mail z Google ostrzeżenie dotyczące aplikacji przy użyciu punktu końcowego usługi GCM dla powiadomień. Jest to po prostu ostrzeżenie, nic nie zostanie przerwane — powiadomień systemu Android aplikacji nadal są wysyłane do firmy Google do przetwarzania i Google nadal przetwarza je. Niektórzy klienci, którzy jawnie określony punkt końcowy usługi GCM w swojej konfiguracji usługi nadal był używany przestarzały punktu końcowego. Firma Microsoft ma już określone luki i aktualnie prowadzone prace rozwiążesz ten problem, gdy Google wysłano wiadomość e-mail.

Zastąpiliśmy przestarzałe punkt końcowy i jest wdrażana poprawki.

## <a name="going-forward"></a>Idąc dalej

Często zadawane pytania na temat usługi FCM firmy Google mówi, że nie trzeba podejmować żadnych działań. W [FCM — często zadawane pytania](https://developers.google.com/cloud-messaging/faq), Google mówi "zestawów SDK klienta i GCM tokenów będą nadal działać przez czas nieokreślony. Jednak nie będzie można pod kątem najnowszą wersję usług Google Play w aplikacji systemu Android, chyba że migracji do usługi FCM."

Jeśli aplikacja korzysta z biblioteki usługi GCM, przejdź dalej i postępuj zgodnie z instrukcjami firmy Google, aby uaktualnić do biblioteki usługi FCM w swojej aplikacji. Nasz zestaw SDK jest zgodny z żadnego z nich, dzięki czemu nie trzeba aktualizacja niczego w aplikacji po naszej stronie (o ile jesteś na bieżąco z naszego zestawu SDK w wersji).

## <a name="questions-and-answers"></a>Pytania i odpowiedzi

Poniżej przedstawiono niektóre odpowiedzi na często zadawane pytania, które otrzymujemy od klientów:

**Pyt.:** Co należy zrobić, aby zapewnić ich zgodność przy dacie (Google w bieżącej dacie jest 29 maja i mogą ulec zmianie)?

**Odp.:** Brak elementów. Firma Microsoft będzie zachować zgodność z istniejącego schematu powiadomienia usługi GCM. Klucz GCM będą nadal działać w zwykły sposób, podobnie jak wszystkie zestawy SDK usługi GCM i bibliotek używanych przez aplikację.

Jeśli/gdy zdecydujesz się na uaktualnienie do usługi FCM zestawy SDK i bibliotek, aby móc korzystać z nowych funkcji, klucz GCM będą nadal działać. Można przełączyć się do za pomocą klucza usługi FCM, jeśli chcesz, ale upewnij się, że dodajesz Firebase do istniejącego projektu usługi GCM podczas tworzenia nowego projektu Firebase. Gwarantuje to zgodności z poprzednimi wersjami z Twoimi klientami, które są uruchomione starsze wersje aplikacji, które nadal korzystać z usługi GCM zestawy SDK i bibliotek.

Tworzenie nowego projektu usługi FCM i nie dołączanie do istniejącej GCM projektu, po aktualizacji usługi Notification Hubs za pomocą nowy wpis tajny usługi FCM spowoduje utratę możliwości wysyłanie powiadomień wypychanych do bieżącej instalacji aplikacji, ponieważ nowy klucz usługi FCM nie ma linku do starego usługi GCM Projekt.

**Pyt.:** Dlaczego otrzymuję tę wiadomość e-mail o stare GCM punkty końcowe używane? Co mam zrobić?

**Odp.:** Brak elementów. Firma Microsoft została migrację do nowych punktów końcowych i wkrótce zakończone, więc zmiana nie jest konieczne. Nic nie zostanie przerwane, po prostu naszych jeden punkt końcowy brakujących spowodowane komunikaty ostrzegawcze z usługi Google.

**Pyt.:** Jak można mogę przejść do nowej usługi FCM zestawy SDK i bibliotek bez przerywania istniejących użytkowników?

Odp.: W dowolnym momencie uaktualnić. Google nie ogłosił jeszcze amortyzacja istniejących zestawów SDK usługi GCM i bibliotek. Aby upewnić się, nie DZIEL powiadomień wypychanych do istniejących użytkowników, upewnij się, kiedy należy utworzyć nowy projekt Firebase, który zostanie skojarzony z istniejącego projektu usługi GCM. Pozwoli to zagwarantować Firebase nowe wpisy tajne będzie działać w przypadku użytkowników ze starszymi wersjami aplikacji za pomocą zestawów SDK usługi GCM i bibliotek, a także nowych użytkowników aplikacji za pomocą zestawów SDK usługi FCM i bibliotek.

**Pyt.:** Kiedy będzie można używać nowych funkcji usługi FCM i schematy dla Moje powiadomienia?

**Odp.:** Po opublikowaniu aktualizacji do naszego interfejsu API i zestawów SDK, cierpliwości — oczekujemy, że mają coś dla Ciebie w najbliższych miesiącach.
