---
title: Badanie ryzyka ochrony tożsamości usługi Azure Active Directory
description: Dowiedz się, jak badać ryzykownych użytkowników, wykrywania i logowania w usłudze Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253483"
---
# <a name="how-to-investigate-risk"></a>Jak: Badanie ryzyka

Ochrona tożsamości udostępnia organizacjom trzy raporty, których mogą używać do badania zagrożeń tożsamości w ich środowisku. Raporty te są **ryzykowne użytkowników,** **ryzykowne logowania**i **wykrywania ryzyka.** Badanie zdarzeń jest kluczem do lepszego zrozumienia i zidentyfikowania wszelkich słabych punktów strategii bezpieczeństwa.

Wszystkie trzy raporty umożliwiają pobieranie zdarzeń w pliku . Format CSV do dalszej analizy poza witryną Azure portal. Ryzykowne raporty użytkowników i ryzykowne logowania umożliwiają pobieranie najnowszych 2500 wpisów, podczas gdy raport wykrywania ryzyka umożliwia pobieranie najnowszych rekordów 5000.

Organizacje mogą korzystać z integracji interfejsu API programu Microsoft Graph do agregowania danych z innymi źródłami, do których mogą mieć dostęp jako organizacja.

Trzy raporty znajdują się w **witrynie Azure portal** > **Azure Active Directory** > **Security**.

## <a name="navigating-the-reports"></a>Poruszanie się po raportach

Każdy raport zostanie uruchomiony z listą wszystkich wykryć dla okresu wyświetlanego w górnej części raportu. Każdy raport umożliwia dodawanie lub usuwanie kolumn na podstawie preferencji administratora. Administratorzy mogą pobrać dane w pliku . CSV lub . format JSON. Raporty można filtrować za pomocą filtrów w górnej części raportu.

Wybranie poszczególnych wpisów może włączyć dodatkowe wpisy w górnej części raportu, takie jak możliwość potwierdzenia logowania jako zagrożonego lub bezpiecznego, potwierdzenia użytkownika jako zagrożonego lub odrzucenia ryzyka użytkownika.

Wybranie poszczególnych wpisów powoduje rozwinięcie okna szczegółów poniżej wykrywania. Widok szczegółów umożliwia administratorom badanie i wykonywanie akcji przy każdym wykrywaniu. 

![Przykładowy raport ochrony tożsamości przedstawiający ryzykowne logowania i szczegóły](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Ryzykowni użytkownicy

Dzięki informacjom dostarczonym przez raport ryzykownych użytkowników administratorzy mogą znaleźć:

- Którzy użytkownicy są zagrożeni, mieli skorygowane ryzyko lub ryzyko zostało odrzucone?
- Szczegółowe informacje o wykrywaniu
- Historia wszystkich ryzykownych logów
- Historia ryzyka
 
Administratorzy mogą następnie podjąć działania w przypadku tych zdarzeń. Administratorzy mogą wybrać:

- Resetowanie hasła użytkownika
- Potwierdź naruszenie zabezpieczeń użytkownika
- Odrzucanie ryzyka użytkownika
- Blokowanie użytkownikowi logowania
- Zbadaj dalej przy użyciu narzędzia Azure ATP

## <a name="risky-sign-ins"></a>Ryzykowne logowania

Raport ryzykownych logów zawiera dane podlegające filtrowaniu przez okres do ostatnich 30 dni (1 miesiąc).

Dzięki informacjom dostarczonym przez raport ryzykownych logów administratorzy mogą znaleźć:

- Które logowania są klasyfikowane jako zagrożone, potwierdzone zagrożone, potwierdzone bezpieczne, odrzucone lub naprawione.
- Poziomy ryzyka w czasie rzeczywistym i zagregowane związane z próbami logowania.
- Wyzwalane typy wykrywania
- Zastosowano zasady dostępu warunkowego
- Szczegóły usługi MFA
- Informacje o urządzeniu
- Informacje o aplikacji
- Informacje o lokalizacji

Administratorzy mogą następnie podjąć działania w przypadku tych zdarzeń. Administratorzy mogą wybrać:

- Potwierdzanie kompromisu logowania
- Potwierdzanie bezpieczeństwa logowania

## <a name="risk-detections"></a>Wykrycia ryzyka

Raport wykrywania ryzyka zawiera dane podlegające filtrowaniu przez okres do ostatnich 90 dni (3 miesiące).

Dzięki informacjom dostarczonym w raporcie wykrywania ryzyka administratorzy mogą znaleźć:

- Informacje o każdym wykrywaniu ryzyka, w tym o typie.
- Inne zagrożenia wywołane w tym samym czasie
- Lokalizacja próby logowania
- Połącz się z bardziej szczegółowymi łączem z programem Microsoft Cloud App Security (MCAS).

Administratorzy mogą następnie powrócić do raportu ryzyka lub logowania użytkownika, aby podjąć działania na podstawie zebranych informacji.

## <a name="next-steps"></a>Następne kroki

- [Dostępne zasady ograniczania ryzyka](concept-identity-protection-policies.md)

- [Włączanie zasad logowania i ryzyka użytkownika](howto-identity-protection-configure-risk-policies.md)
