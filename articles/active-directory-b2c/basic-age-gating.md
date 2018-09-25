---
title: Stosowania kontroli wieku w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu identyfikowania osoby nieletnie korzystanie z aplikacji.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9a9a86d445deaea4872615f443ad53f76638a758
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056526"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Stosowania kontroli wieku w usłudze Azure AD B2C

>[!IMPORTANT]
>Ta funkcja jest dostępna w prywatnej wersji zapoznawczej.  Zobacz nasze [blog usługi](https://blogs.msdn.microsoft.com/azureadb2c/) Aby uzyskać szczegółowe informacje, ponieważ staje się dostępna, lub skontaktuj się z pomocą AADB2CPreview@microsoft.com.  Nie używaj tego na katalogi w środowisku produkcyjnym, przy użyciu tych nowych funkcji może spowodować utratę danych i może mieć nieoczekiwane zachowanie, dopóki nie możemy przejść do ogólnej dostępności.  
>

##<a name="age-gating"></a>Wieku
Wieku pozwala na używanie usługi Azure AD B2C do identyfikowania osoby nieletnie w aplikacji.  Istnieje możliwość użytkownik może zalogować się do aplikacji lub zezwolić im na wróć do aplikacji za pomocą dodatkowe oświadczenia, które identyfikują użytkownika grupa wiekowa i ich stan uzyskuje zgodę rodzica.  

>[!NOTE]
>Uzyskuje zgodę rodzica jest śledzona w atrybut użytkownika o nazwie `consentProvidedForMinor`.  Możesz zaktualizować tę właściwość za pomocą interfejsu API programu Graph, a następnie użyje tego pola podczas aktualizowania `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>Konfigurowanie katalogu do kontroli wieku
Aby można było używać wieku w przepływie użytkownika, należy skonfigurować katalog na mają dodatkowe właściwości. Ta operacja może odbywać się za pośrednictwem `Properties` w menu, (które będą dostępne tylko wtedy, gdy jesteś częścią prywatnej wersji zapoznawczej).  
1. W rozszerzeniu usługi Azure AD B2C, kliknij pozycję **właściwości** dla dzierżawy w menu po lewej stronie.
2. W obszarze **wieku** sekcji, kliknij pozycję **Konfiguruj** przycisku.
3. Poczekaj na zakończenie operacji i katalogu zostanie skonfigurowana do kontroli wieku.

##<a name="enabling-age-gating-in-your-user-flow"></a>Włączanie wieku przepływu użytkownika
Po skonfigurowaniu katalogu do użycia w wieku następnie umożliwia tej funkcji w przepływach użytkowników wersji (wersja zapoznawcza).  Ta funkcja wymaga wprowadzenia zmian, które niezgodne z istniejących typów przepływy użytkownika.  Należy włączyć wieku wykonując następujące kroki:
1. Utwórz przepływ użytkownika (wersja zapoznawcza).
2. Po jego utworzeniu, przejdź do obszaru **właściwości** w menu.
3. W **wieku** sekcji, naciśnij przełącznik, aby włączyć tę funkcję.
4. Następnie możesz, jak chcesz zarządzać użytkownikami, które identyfikują jako osoby niepełnoletnie.

##<a name="what-does-enabling-age-gating-do"></a>Do czego służy Włączanie wieku?
Po włączeniu wieku przepływu użytkownika, użytkownik objęci zmianami.  Na rejestracji użytkownicy teraz zostanie poproszony o ich daty urodzenia i kraju zamieszkania wraz z atrybutów użytkownika skonfigurowane dla przepływu użytkownika.  Na temat logowania użytkownicy, którzy nie zostały wprowadzone poprzednio, Data urodzenia i kraju zamieszkania pojawi się prośba uzyskać te informacje podczas następnego logowania.  Te dwie wartości, usługi Azure AD B2C będzie ustalić, czy użytkownik jest pomocnicze i zaktualizuj `ageGroup` pola wartość może być `null`, `Undefined`, `Minor`, `Adult`, i `NotAdult`.  `ageGroup` i `consentProvidedForMinor` pola są następnie używane do obliczania `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Opcje kontroli wieku
Istnieje możliwość ma usługę Azure AD B2C Blokuj nieletnim bez zgody rodziców lub zezwolić im — aplikacja będzie podejmować decyzje dotyczące postępowania z nimi.  

###<a name="allowing-minors-without-parental-consent"></a>Dzięki czemu osoby nieletnie bez zgody rodziców
Przepływy użytkownika, które umożliwia albo Zaloguj się, zaloguj się lub oba istnieje możliwość Zezwalaj nieletnim bez zgody użytkownika do aplikacji.  Dla osoby nieletnie bez zgody rodziców, mają zezwolenie na rejestrowanie się lub zarejestrować się, jak normalne i Azure AD B2C wystawia token Identyfikatora z `legalAgeGroupClassification` oświadczenia.  Za pomocą tego oświadczenia, możesz wybrać środowisko, które Ci użytkownicy mają, takich jak przeprowadzając zbierać zgody rodziców (i aktualizowanie `consentProvidedForMinor` pola).

###<a name="blocking-minors-without-parental-consent"></a>Blokowanie nieletnim bez zgody rodziców
Przepływy użytkownika, które umożliwia albo Zaloguj się, zaloguj się lub oba można zablokować nieletnim bez zgody użytkownika z aplikacji.  Dostępne są dwie opcje do obsługi zablokowanych użytkowników w usłudze Azure AD B2C:
* Wyślij kod JSON z powrotem do aplikacji — ta opcja zostanie wysłana odpowiedź z powrotem do aplikacji zablokowano pomocnicze.
* Pokaż stronę błędu - użytkownika pojawi się Strona z informacją o tym, że nie mogą uzyskiwać dostęp do aplikacji