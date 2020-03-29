---
title: Problem z dodawaniem aplikacji spoza galerii | Dokumenty firmy Microsoft
description: Zrozumienie typowych problemów, z którymi borykają się użytkownicy podczas dodawania niestandardowych aplikacji innych niż galeria
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38a9ef04389318d3588649117c930ff6efa3fe4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784475"
---
# <a name="problem-adding-a-non-gallery-application"></a>Problem z dodawaniem aplikacji spoza galerii

Ten artykuł pomaga zrozumieć typowe problemy, z którymi borykają się użytkownicy podczas dodawania **niestandardowych aplikacji spoza galerii** i co można zrobić, aby je rozwiązać. 

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Kliknąłem przycisk "dodaj", a moja aplikacja długo się pojawiała

W pewnych okolicznościach może upłynąć 1-2 minuty (a czasami dłużej) dla aplikacji pojawiają się po dodaniu go do katalogu. Chociaż nie jest to normalna oczekiwana wydajność, możesz zobaczyć, że dodanie aplikacji jest w toku, klikając ikonę Powiadomienia (dzwonek) w prawym górnym rogu **witryny** [Azure portal](https://portal.azure.com/) i wyszukując powiadomienie **w toku** lub **zakończone** z etykietą **Utwórz aplikację**.

Jeśli aplikacja nigdy nie zostanie dodana lub podczas klikania przycisku **Dodaj** wystąpi błąd, zostanie **wyświetlone powiadomienie** w stanie **Błąd.** Jeśli chcesz uzyskać więcej szczegółów na temat błędu, aby dowiedzieć się więcej lub udostępnić inżynierowi pomocy technicznej, możesz zobaczyć więcej informacji na temat błędu, wykonując kroki opisane w sekcji [Jak wyświetlić szczegóły powiadomienia portalu.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Kliknąłem przycisk "Dodaj", a moja aplikacja nie pojawiła się

Czasami z powodu przejściowych problemów, problemów z siecią lub błędu dodawanie aplikacji nie powiedzie się. Można powiedzieć, że dzieje się tak po kliknięciu **ikony powiadomień** (dzwonek) w prawym górnym rogu witryny Azure portal i zobaczysz czerwoną (!) ikonę obok powiadomienia **utwórz aplikację.** Oznacza to, że wystąpił błąd podczas tworzenia aplikacji.

Jeśli podczas klikania przycisku **Dodaj** wystąpi błąd, zostanie **wyświetlone powiadomienie** w stanie **Błąd.** Jeśli chcesz uzyskać więcej szczegółów na temat błędu, aby dowiedzieć się więcej lub udostępnić inżynierowi pomocy technicznej, możesz zobaczyć więcej informacji na temat błędu, wykonując kroki opisane w sekcji [Jak wyświetlić szczegóły powiadomienia portalu.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Nie wiem, jak skonfigurować aplikację po dodaniu jej

Jeśli potrzebujesz pomocy w poznawaniu aplikacji niestandardowych, [biblioteka dokumentów aplikacji usługi Azure AD applications](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) pomoże Ci dowiedzieć się więcej o logowanie jednokrotne za pomocą usługi Azure AD i jak to działa.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Jak wyświetlić szczegóły powiadomienia portalu

Możesz zobaczyć szczegóły dowolnego powiadomienia portalu, wykonując poniższe czynności:

1. kliknij ikonę **Powiadomienia** (dzwonek) w prawym górnym rogu witryny Azure portal

2. Wybierz dowolne powiadomienie w stanie **Błąd** (te z czerwonym (!) obok nich).

   >[!NOTE]
   >Nie można kliknąć powiadomień w stanie **Pomyślny** lub **W toku.**
   >
   >

4. Użyj informacji w obszarze **Szczegóły powiadomień,** aby uzyskać więcej szczegółów na temat problemu.

5. Jeśli nadal potrzebujesz pomocy, możesz również udostępnić te informacje inżynierowi pomocy technicznej lub grupie produktów, aby uzyskać pomoc dotyczącą problemu.

6. Kliknij **ikonę kopiowania** po prawej stronie pola tekstowego **Błąd kopiowania,** aby skopiować wszystkie szczegóły powiadomienia, aby udostępnić je pomocy technicznej lub inżynierowi grupy produktów.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Jak uzyskać pomoc, wysyłając szczegóły powiadomień do inżyniera pomocy technicznej

Bardzo ważne jest, aby udostępnić **wszystkie poniższe dane** inżynierowi pomocy technicznej, jeśli potrzebujesz pomocy, aby mogły ci szybko pomóc. Można to łatwo **zrobić, robiąc zrzut ekranu** lub klikając **ikonę błędu kopiowania**, znajdującą się po prawej stronie pola tekstowego **błędu kopiowania.**

## <a name="notification-details-explained"></a>Szczegóły powiadomienia Wyjaśnione

Zobacz poniższe opisy, aby uzyskać więcej informacji na temat powiadomień.

### <a name="essential-notification-items"></a>Podstawowe elementy powiadomień

- **Tytuł** – opisowy tytuł zgłoszenia
  *  Przykład — **ustawienia serwera proxy aplikacji**

- **Opis** – opis tego, co nastąpiło w wyniku operacji

  *  Przykład — **wprowadzony wewnętrzny adres URL jest już używany przez inną aplikację**

- **Identyfikator powiadomienia** – unikatowy identyfikator powiadomienia

  *  Przykład – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **Identyfikator żądania klienta** — identyfikator żądania określonego złożony przez przeglądarkę

  *  Przykład – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Time Stamp UTC** – sygnatura czasowa, podczas której wystąpiło powiadomienie, w utc

  *  Przykład – **2017-03-23T19:50:43.7583681Z**

- **Identyfikator transakcji wewnętrznej** – wewnętrzny identyfikator, którego możemy użyć do wyszukiwania błędu w naszych systemach

  *  Przykład – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** – użytkownik, który wykonał operację

  *  Przykład – **tperkins\@f128.info**

- **Identyfikator dzierżawy** — unikatowy identyfikator dzierżawy, którego użytkownik, który wykonał operację, był członkiem

  *  Przykład – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **Identyfikator obiektu użytkownika** – unikatowy identyfikator użytkownika, który wykonał operację

  *  Przykład – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Szczegółowe elementy powiadomień

- **Nazwa wyświetlana** – **(może być pusta)** bardziej szczegółowa nazwa wyświetlana błędu

  *  Przykład — **ustawienia serwera proxy aplikacji**

- **Status** – szczególny status zgłoszenia

  *  Przykład – **Nie powiodło się**

- **Identyfikator obiektu** – **(może być pusty)** identyfikator obiektu, na którym wykonano operację

  *  Przykład – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

- **Szczegóły** – szczegółowy opis tego, co wydarzyło się w wyniku operacji

  *  Przykład — **wewnętrzny adres URL `https://bing.com/` jest nieprawidłowy, ponieważ jest już używany**

- **Błąd kopiowania** — kliknij **ikonę kopiowania** po prawej stronie pola tekstowego **błędu Kopiowania,** aby skopiować wszystkie szczegóły powiadomienia, aby udostępnić je pomocy technicznej lub grupie produktów 
- inżynier ds

  *  Przykład```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```




