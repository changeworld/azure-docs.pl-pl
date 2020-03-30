---
title: Usługi Azure Notification Hubs i migracja usługi Google Firebase Cloud Messaging (FCM)
description: W tym artykule opisano, jak usługi Azure Notification Hubs adresy Google GCM do fcm migracji.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127017"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Usługi Azure Notification Hubs i migracja wiadomości w chmurze Google Firebase

## <a name="current-state"></a>Bieżący stan

Kiedy Google ogłosił migrację z Google Cloud Messaging (GCM) do Firebase Cloud Messaging (FCM), usługi push, takie jak nasza, musiały dostosować sposób wysyłania powiadomień na urządzenia z Androidem, aby dostosować się do zmiany.

Zaktualizowaliśmy nasze zaplecze usługi, a następnie opublikowaliśmy aktualizacje naszych api i SDK w razie potrzeby. Dzięki naszej implementacji podjęliśmy decyzję o zachowaniu zgodności z istniejącymi schematami powiadomień GCM, aby zminimalizować wpływ na klienta. Oznacza to, że obecnie wysyłamy powiadomienia do urządzeń z systemem Android przy użyciu FCM w trybie FCM Legacy Mode. Ostatecznie chcemy dodać prawdziwe wsparcie dla FCM, w tym nowe funkcje i format ładunku. Jest to zmiana długoterminowa, a bieżąca migracja koncentruje się na zachowaniu zgodności z istniejącymi aplikacjami i SDK. Możesz użyć SDK GCM lub FCM w aplikacji (wraz z naszym SDK) i upewniamy się, że powiadomienie jest wysyłane poprawnie.

Niektórzy klienci otrzymali niedawno wiadomość e-mail od Google z ostrzeżeniem o aplikacjach korzystających z punktu końcowego GCM dla powiadomień. To było tylko ostrzeżenie i nic nie jest zepsute - powiadomienia o Androidzie aplikacji są nadal wysyłane do Google w celu przetworzenia, a Google nadal je przetwarza. Niektórzy klienci, którzy wyraźnie określili punkt końcowy GCM w konfiguracji usługi, nadal używali przestarzałego punktu końcowego. Zidentyfikowaliśmy już tę lukę i pracowaliśmy nad rozwiązaniem problemu, gdy Google wysłał e-mail.

Zastąpiliśmy ten przestarzały punkt końcowy i poprawka została wdrożona.

## <a name="going-forward"></a>Idąc dalej

Google FCM FAQ mówi, że nie musisz nic robić. W [FAQ FCM](https://developers.google.com/cloud-messaging/faq)Google powiedział: "SDK klientów i tokeny GCM będą nadal działać przez czas nieokreślony. Nie możesz jednak kierować reklam na najnowszą wersję usług Google Play w aplikacji na Androida, chyba że zostaniesz przeniesiony do fcm."

Jeśli aplikacja korzysta z biblioteki GCM, postępuj zgodnie z instrukcjami Google, aby przejść do biblioteki FCM w aplikacji. Nasz SDK jest zgodny z żadnym z nich, więc nie musisz niczego aktualizować w aplikacji po naszej stronie (o ile jesteś na bieżąco z naszą wersją SDK).

## <a name="questions-and-answers"></a>Pytania i odpowiedzi

Oto kilka odpowiedzi na często zadawane pytania od klientów:

**P.** Co muszę zrobić, aby być zgodnym z datą odcięcia (bieżąca data odcięcia Google to 29 maja i może ulec zmianie)?

**Odp.:** Nic. Zachowamy zgodność z istniejącym schematem powiadomień GCM. Klucz GCM będzie nadal działać normalnie, podobnie jak wszystkie SDK GCM i biblioteki używane przez aplikację.

Jeśli/kiedy zdecydujesz się uaktualnić do pakietów SDK i bibliotek FCM, aby skorzystać z nowych funkcji, klucz GCM będzie nadal działać. Możesz przełączyć się na użycie klucza FCM, jeśli chcesz, ale upewnij się, że dodajesz Firebase do istniejącego projektu GCM podczas tworzenia nowego projektu Firebase. Zagwarantuje to zgodność z klientami, którzy korzystają ze starszych wersji aplikacji, które nadal używają sdk i bibliotek GCM.

Jeśli tworzysz nowy projekt FCM i nie dołączasz do istniejącego projektu GCM, po zaktualizowaniu Centrów powiadomień o nowy klucz FCM tracisz możliwość wypychania powiadomień do bieżących instalacji aplikacji, ponieważ nowy klucz FCM nie ma łącza do starego GCM Projektu.

**P.** Dlaczego ogeneruję ten e-mail dotyczący używanych starych punktów końcowych GCM? Co muszę zrobić?

**Odp.:** Nic. Przeprowadzamy migrację do nowych punktów końcowych i wkrótce zostanie zakończona, więc nie jest konieczna żadna zmiana. Nic nie jest zepsute, nasz jeden nieodebrany punkt końcowy po prostu spowodował komunikaty ostrzegawcze od Google.

**P.** Jak przejść do nowych sdk fcm i bibliotek bez przerywania istniejących użytkowników?

Odp.: Uaktualnij w dowolnym momencie. Google nie ogłosiłjeszcze żadnych deprecation istniejących GCM SDK i bibliotek. Aby upewnić się, że nie przerywasz powiadomień wypychanych dla istniejących użytkowników, upewnij się, że podczas tworzenia nowego projektu Firebase, który kojarzysz z istniejącym projektem GCM. Zapewni to, że nowe wpisy tajne Firebase będą działać dla użytkowników korzystających ze starszych wersji aplikacji z modułami SDK i bibliotekami GCM, a także nowymi użytkownikami aplikacji z plikami SDK FCM i bibliotekami.

**P.** Kiedy mogę używać nowych funkcji i schematów FCM dla moich powiadomień?

**Odp.:** Po opublikowaniu aktualizacji do naszego API i SDK, stay tuned - spodziewamy się mieć coś dla Ciebie w najbliższych miesiącach.
