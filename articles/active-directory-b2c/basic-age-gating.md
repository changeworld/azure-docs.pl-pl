---
title: Włącz wiek w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się, jak identyfikować osoby niepełnoletnie za pomocą aplikacji.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 56cbeb8e8fe21f4b39c2f5c6af43e83ae330e5d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189977"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Włącz usłudze Age Gating w usłudze Azure Active Directory B2C

>[!IMPORTANT]
>Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie należy używać funkcji dla aplikacji produkcyjnych.
>

Wiek w usłudze Azure Active Directory B2C (Azure AD B2C) umożliwia identyfikowanie osób niepełnoletnich, które chcą korzystać z aplikacji. Można zablokować nieletniego przed logowaniem się do aplikacji. Użytkownicy mogą również wrócić do aplikacji i zidentyfikować swoją grupę wiekową i ich status zgody rodzica. Usługa Azure AD B2C może blokować osoby niepełnoletnie bez zgody rodziców. Usługi Azure AD B2C można również skonfigurować, aby umożliwić aplikacji, aby zdecydować, co zrobić z osobami niepełnoletnimi.

Po włączeniu wieku w [przepływie użytkownika](user-flow-overview.md)użytkownicy są pytani, kiedy się urodzili i w jakim kraju/regionie mieszkają. Jeśli użytkownik zaloguje się, który nie wprowadził wcześniej informacji, będzie musiał wprowadzić je przy następnym loguchi. Reguły są stosowane za każdym razem, gdy użytkownik się loguje.

Usługa Azure AD B2C używa informacji wprowadzonych przez użytkownika w celu określenia, czy są one nieletniego. Pole **ageGroup** jest następnie aktualizowane na ich koncie. Wartość może `null`być `Undefined` `Minor`, `Adult`, `NotAdult`, i .  Pola **ageGroup** i **consentProvidedForMinor** są następnie używane do obliczania wartości **legalAgeGroupClassification**.

Wiek obejmuje dwie wartości wiekowe: wiek, w którym ktoś nie jest już uważany za osobę niepełnoletnią, oraz wiek, w którym małoletni musi mieć zgodę rodziców. W poniższej tabeli wymieniono reguły wieku używane do definiowania osoby niepełnoletniej i osoby niepełnoletniej wymagającej zgody.

| Kraj/region | Nazwa kraju/regionu | Wiek zgody o niewielkie przyzwolnienie | Wiek małoletni |
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
| GB | Wielka Brytania | 13 | 18 |
| GR | Grecja | 16 | 18 |
| HR | Chorwacja | 16 | 18 |
| HU | Węgry | 16 | 18 |
| IE | Irlandia | 13 | 18 |
| IT | Włochy | 16 | 18 |
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
| USA | Stany Zjednoczone | 13 | 18 |

## <a name="age-gating-options"></a>Opcje gating wieku

### <a name="allowing-minors-without-parental-consent"></a>Zezwalanie osobom niepełnoletnim bez zgody rodziców

W przypadku przepływów użytkowników, które umożliwiają rejestrację, logowanie lub oba te elementy, można zezwolić osobom niepełnoletnim bez zgody na korzystanie z aplikacji. Osoby niepełnoletnie bez zgody rodziców mogą logować się lub rejestrować się normalnie, a usługa Azure AD B2C wystawia token identyfikatora z roszczeniem **legalAgeGroupClassification.** To oświadczenie definiuje środowisko, które mają użytkownicy, takie jak zbieranie zgody rodziców i aktualizowanie **pola consentProvidedForMinor.**

### <a name="blocking-minors-without-parental-consent"></a>Blokowanie nieletnich bez zgody rodziców

W przypadku przepływów użytkowników, które umożliwiają rejestrację, logowanie lub oba te elementy, można zablokować osoby niepełnoletnie bez zgody aplikacji. Dostępne są następujące opcje obsługi zablokowanych użytkowników w usłudze Azure AD B2C:

- Wyślij JSON z powrotem do aplikacji — ta opcja wysyła odpowiedź z powrotem do aplikacji, że osoba niepełnoletnia została zablokowana.
- Pokaż stronę błędu — użytkownik jest wyświetlany stronę informującą, że nie mogą uzyskać dostępu do aplikacji.

## <a name="set-up-your-tenant-for-age-gating"></a>Skonfiguruj dzierżawę dla wieku

Aby użyć wieku gating w przepływie użytkownika, należy skonfigurować dzierżawy, aby mieć dodatkowe właściwości.

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalogu +** w górnym menu. Wybierz katalog zawierający dzierżawę.
2. Wybierz **wszystkie usługi** w lewym górnym rogu witryny Azure portal, wyszukaj i wybierz pozycję Azure AD **B2C**.
3. Wybierz **właściwości** dla dzierżawy w menu po lewej stronie.
2. W sekcji **Wiek gating** kliknij przycisk **Konfiguruj**.
3. Poczekaj na zakończenie operacji, a dzierżawa zostanie skonfigurowana do wyznaczania wieku.

## <a name="enable-age-gating-in-your-user-flow"></a>Włączanie wiekowania w przepływie użytkownika

Po skonfigurowaniu dzierżawy do używania wieku gating, można następnie użyć tej funkcji w [przepływach użytkownika,](user-flow-versions.md) gdzie jest włączona. Włącz wiek gating z następujących kroków:

1. Utwórz przepływ użytkownika, który ma włączone wiekowe gating.
2. Po utworzeniu przepływu użytkownika wybierz polecenie **Właściwości** w menu.
3. W sekcji **Wiek gating** wybierz pozycję **Włączone**.
4. Następnie decydujesz, w jaki sposób chcesz zarządzać użytkownikami identyfikującymi się jako osoby niepełnoletnie. Aby **się zarejestrować lub zalogować,** wybierz `Allow minors to access your application` lub `Block minors from accessing your application`. Jeśli zaznaczone jest blokowanie `Send a JSON back to the application` osób `Show an error message`niepełnoletnich, należy wybrać lub .




