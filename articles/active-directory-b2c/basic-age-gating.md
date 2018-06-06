---
title: Przy użyciu wieku bramkowanie w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Informacje na temat do identyfikowania osoby nieletnie przy użyciu aplikacji.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bbf885fbcca22e42b2ec0ad7ff4e7a70aa5f5828
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711974"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Przy użyciu wieku bramkowanie w usłudze Azure AD B2C

>[!IMPORTANT]
>Ta funkcja jest dostępna w prywatnej wersji zapoznawczej.  Zobacz nasze [blogu usługi](https://blogs.msdn.microsoft.com/azureadb2c/) Aby uzyskać szczegółowe informacje, jak to staje się dostępny, lub skontaktuj się z AADB2CFeedback@microsoft.com.  Nie używaj to katalogi produkcji, przy użyciu nowych funkcji może spowodować utratę danych i może mieć nieoczekiwane działanie do czasu firma Microsoft, przejdź do ogólnej dostępności.  
>

##<a name="age-gating"></a>Wiek bramkowanie
Wiek bramkowanie umożliwia przy użyciu usługi Azure AD B2C do identyfikowania osoby nieletnie w aplikacji.  Możesz zablokować użytkownika z logowaniem do aplikacji lub zezwolić im na wróć do aplikacji z dodatkowych oświadczeń, identyfikujące grupę wiek użytkownika i ich stan zgody rodzica.  

>[!NOTE]
>Zgodę jako rodzic jest śledzony atrybut użytkownika o nazwie `consentProvidedForMinor`.  Możesz zaktualizować tę właściwość za pomocą interfejsu API programu Graph i to pole zostanie użyty podczas aktualizowania `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>Konfigurowanie w katalogu bramkowanie wieku
Aby można było używać wieku bramkowanie przepływu użytkownika, należy skonfigurować katalog na mają dodatkowe właściwości. Ta operacja może odbywać się za pośrednictwem `Properties` w menu (która będzie dostępna tylko wtedy, gdy jest częścią prywatnej wersji zapoznawczej).  
1. W rozszerzeniu usługi Azure AD B2C, kliknij **właściwości** dla dzierżawy w menu po lewej stronie.
2. W obszarze **bramkowanie wieku** kliknij na **Konfiguruj** przycisku.
3. Poczekaj na ukończenie tej operacji i katalogu zostanie ustawiona dla bramkowanie wieku.

##<a name="enabling-age-gating-in-your-user-flow"></a>Włączanie wieku bramkowanie Twojego przepływu użytkownika
Po skonfigurowaniu katalogu do użycia, wieku bramkowanie można następnie użyj tej funkcji przepływów użytkownika wersja preview.  Ta funkcja wymaga wprowadzenia zmian, które stał się niezgodny z typami istniejących przepływów użytkownika.  Włącz, wieku bramkowanie następujące czynności:
1. Tworzenie przepływu użytkownika podglądu.
2. Po utworzeniu, przejdź do **właściwości** w menu.
3. W **bramkowanie wieku** sekcji, naciśnij klawisz, aby włączyć funkcję.
4. Następnie można sposobu zarządzania użytkownikami, które identyfikują jako osoby nieletnie.

##<a name="what-does-enabling-age-gating-do"></a>Włączanie bramkowanie wieku czego służy?
Po włączeniu wieku bramkowanie Twojego przepływu użytkownika, użytkownik wystąpić zmiany.  W przypadku tworzenia konta użytkowników teraz zostanie poproszony o daty urodzenia i kraju zamieszkania wraz z atrybutów użytkownika skonfigurowane dla przepływu użytkownika.  Na logowania użytkowników, którzy wcześniej nie podano daty urodzenia i kraju zamieszkania będzie wymagane podanie tego informacje przy następnym zalogowaniu.  Te dwie wartości, usługi Azure AD B2C będzie ustalić, czy użytkownik jest pomocnicze i zaktualizuj `ageGroup` pola, możliwe wartości `null`, `Undefined`, `Minor`, `Adult`, i `NotAdult`.  `ageGroup` i `consentProvidedForMinor` pola są następnie używane do obliczania `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Wiek bramkowanie opcje
Można mieć osoby nieletnie bloku usługi Azure AD B2C bez zgody rodzica lub zezwolić im i aplikacja podejmowaniu decyzji, co należy zrobić z nimi.  

###<a name="allowing-minors-without-parental-consent"></a>Zezwolenie na obecność osoby nieletnie bez zgody rodzica
Przepływy użytkownika, które umożliwia albo Zaloguj się, zaloguj się czy oba rodzaje można zezwolić osoby nieletnie bez zgody użytkownika do aplikacji.  Dla osoby nieletnie bez zgody rodzica, są dozwolone do zalogowania się lub zarejestrować się zgodnie z normalnym i Azure AD B2C wystawia token identyfikator z `legalAgeGroupClassification` oświadczeń.  Za pomocą tego oświadczenia, można wybrać środowisko Ci użytkownicy mają, takie jak przejście przez środowisko do zbierania zgodę jako rodzic (i zaktualizuj `consentProvidedForMinor` pól).

###<a name="blocking-minors-without-parental-consent"></a>Blokowanie osoby nieletnie bez zgody rodzica
Przepływy użytkownika, które umożliwia albo Zaloguj się, zaloguj się czy oba rodzaje można zablokować osoby nieletnie bez zgody z aplikacji.  Dostępne są dwie opcje obsługi zablokowanych użytkowników w usłudze Azure AD B2C:
* Odesłania JSON do aplikacji — ta opcja zostanie wysłana odpowiedź z powrotem do aplikacji czy drugorzędnym został zablokowany.
* Pokaż stronę błędu — użytkownik pojawi się Strona informująca, że ich nie może uzyskać dostęp do aplikacji

##<a name="known-issues"></a>Znane problemy
###<a name="format-for-the-response-when-a-minor-is-blocked"></a>Format odpowiedzi po zablokowaniu pomocnicze.
Odpowiedź aktualnie nie jest poprawnie sformułowany, ten problem zostanie rozwiązany w nadchodzących aktualizacji.

###<a name="deleting-specific-attributes-that-were-added-during-setup-can-make-your-directory-unable-to-use-age-gating"></a>Usuwanie określone atrybuty, które zostały dodane podczas instalacji może być katalogu nie można użyć bramkowanie wieku.
W ustawieniach bramkowanie wieku, są skonfigurowane za pomocą opcji w katalogu Twojej `Properties`.  W przypadku usunięcia `legalCountry` lub `dateOfBirth` za pomocą wykresu, katalogu nie można już używać wieku bramkowanie i nie można ponownie utworzyć te właściwości.

###<a name="list-of-countries-is-incomplete"></a>Listę krajów jest niekompletna
Obecnie listę krajów legalCountry jest niekompletna, dodamy pozostałych krajów, w nadchodzących aktualizacji.