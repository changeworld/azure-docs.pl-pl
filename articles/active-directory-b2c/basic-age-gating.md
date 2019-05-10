---
title: Włącz wieku w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu identyfikowania osoby nieletnie korzystanie z aplikacji.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b594dc6eadce5093c58d693492f8e86eb92ae4e3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228006"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Włącz wieku w usłudze Azure Active Directory B2C

>[!IMPORTANT]
>Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie używaj funkcji przez aplikacje produkcyjne. 
>

Wieku w usłudze Azure Active Directory (Azure AD) B2C umożliwia identyfikację osoby nieletnie, które chcą korzystać z aplikacji. Można zablokować drobnych zalogowanie się do aplikacji. Użytkownikom można wrócić do aplikacji i zidentyfikować ich grupa wiekowa i ich stan uzyskuje zgodę rodzica. Usługa Azure AD B2C można zablokować nieletnim bez zgody rodzica. Usługa Azure AD B2C można też skonfigurować aby umożliwić aplikacji zdecyduj, co należy zrobić nieletnim.

Po włączeniu wieku w swojej [przepływ użytkownika](active-directory-b2c-reference-policies.md), użytkownicy zostaną poproszeni o po ich urodzenia i kraju/regionu znajdować się w. Jeśli użytkownik loguje się, że nie zostało podane wcześniej informacje, muszą wprowadzić ją przy następnym logowaniu. Reguły są stosowane za każdym razem, gdy użytkownik loguje się.

Usługa Azure AD B2C używa tych informacji, wprowadzonych przez użytkownika, aby ustalić, czy są one pomocnicze. **Grupy wiekowej** pola jest następnie aktualizowany na jego koncie. Wartość może być `null`, `Undefined`, `Minor`, `Adult`, i `NotAdult`.  **Grupy wiekowej** i **consentProvidedForMinor** pola są następnie używane do obliczania wartości **legalAgeGroupClassification**.

Wieku obejmuje dwie wartości wieku: wiek, że ktoś nie jest już uznawany za WERSJA_POMOCNICZA i wiek, jaką pomocnicza musi mieć uzyskuje zgodę rodzica. Poniższa lista zawiera reguły wiek, które służą do definiowania WERSJA_POMOCNICZA i pomocnicza, wymagające zgody.

| Kraj/region | Nazwa kraju/regionu | Wiek pomocnicza zgody | Wiek pomocnicza |
| -------------- | ------------------- | ----------------- | --------- |
| Domyślne | Brak | Brak | 18 |
| AE | Zjednoczone Emiraty Arabskie | Brak | 21 |
| AT | Austria | 14 | 18 |
| BE | Belgia | 14 | 18 |
| BG | Bułgaria | 16 | 18 |
| BH | Bahrajn | Brak | 21 |
| CM | Kamerun | Brak | 21 |
| CY | Cypr | 16 | 18 |
| CZ | Czechy | 16 | 18 |
| DE | Niemcy | 16 | 18 |
| DK | Dania | 16 | 18 |
| EE | Estonia | 16 | 18 |
| EG | Egipt | Brak | 21 |
| ES | Hiszpania | 13 | 18 |
| PW | Francja | 16 | 18 |
| GB | Zjednoczone Królestwo | 13 | 18 |
| GR | Grecja | 16 | 18 |
| HR | Chorwacja | 16 | 18 |
| HU | Węgry | 16 | 18 |
| IE | Irlandia | 13 | 18 |
| it | Włochy | 16 | 18 |
| KR | Korea Południowa | 14 | 18 |
| LT | Litwa | 16 | 18 |
| LU | Luksemburg | 16 | 18 |
| LV | Łotwa | 16 | 18 |
| MT | Malta | 16 | 18 |
| Nie dotyczy | Namibia | Brak | 21 |
| NL | Holandia | 16 | 18 |
| PL | Polska | 13 | 18 |
| PT | Portugalia | 16 | 18 |
| RO | Rumunia | 16 | 18 |
| SE | Szwecja | 13 | 18 |
| SG | Singapur | Brak | 21 |
| SI | Słowenia | 16 | 18 |
| SK | Słowacja | 16 | 18 |
| TD | Czad | Brak | 21 |
| TH | Tajlandia | Brak | 20 |
| TW | Tajwan | Brak | 20 | 
| USA | Stany Zjednoczone Ameryki | 13 | 18 |

## <a name="age-gating-options"></a>Opcje kontroli wieku
 
### <a name="allowing-minors-without-parental-consent"></a>Dzięki czemu osoby nieletnie bez zgody rodziców

Przepływy użytkownika, zezwalających na rejestracji, logowania lub oba, istnieje możliwość Zezwalaj nieletnim bez zgody użytkownika do aplikacji. Osoby nieletnie bez zgody rodziców mogą zalogować się lub zarejestrować się, jak normalne i Azure AD B2C wystawia token Identyfikatora z **legalAgeGroupClassification** oświadczenia. To oświadczenie definiuje środowisko, które użytkownicy mają, takich jak zbieranie uzyskuje zgodę rodzica i aktualizowanie **consentProvidedForMinor** pola.

### <a name="blocking-minors-without-parental-consent"></a>Blokowanie nieletnim bez zgody rodziców

Przepływy użytkownika umożliwiające rejestracji, logowania lub oba można zablokować nieletnim bez zgody użytkownika z aplikacji. Do obsługi zablokowanych użytkowników w usłudze Azure AD B2C są następujące opcje:

- Wyślij kod JSON z powrotem do aplikacji — tę opcję, wysyła odpowiedź z powrotem do aplikacji zablokowano pomocnicze.
- Pokaż stronę błędu — użytkownik jest wyświetlana strona z informacją o tym, że nie mogą uzyskiwać dostęp do aplikacji.

## <a name="set-up-your-tenant-for-age-gating"></a>Ustaw swoją dzierżawę, do kontroli wieku

Aby użyć wieku w przepływie użytkownika, należy skonfigurować swoją dzierżawę, aby mieć dodatkowe właściwości.

1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu. Wybierz katalog, który zawiera Twojej dzierżawy. 
2. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal Wyszukaj i wybierz pozycję **usługi Azure AD B2C**.
3. Wybierz **właściwości** dla dzierżawy w menu po lewej stronie.
2. W obszarze **wieku** sekcji, kliknij pozycję **Konfiguruj**.
3. Poczekaj na zakończenie operacji i dzierżawy zostanie skonfigurowana do kontroli wieku.

## <a name="enable-age-gating-in-your-user-flow"></a>Włącz wieku przepływu użytkownika

Po Twojej dzierżawy jest do użytku wieku, następnie można użyć tej funkcji w [przepływy użytkownika](user-flow-versions.md) gdzie jest włączona. Należy włączyć wieku wykonując następujące kroki:

1. Utwórz przepływ użytkownika, który ma wieku włączone.
2. Po utworzeniu przepływu użytkownika, wybierz **właściwości** w menu.
3. W **wieku** zaznacz **włączone**.
4. Następnie zdecyduj, jak chcesz zarządzać użytkownikami, które identyfikują jako osoby niepełnoletnie. Aby uzyskać **rejestracji lub logowania**, możesz wybrać `Allow minors to access your application` lub `Block minors from accessing your application`. Jeśli wybrano blokowanie nieletnim wybierz `Send a JSON back to the application` lub `Show an error message`. 




