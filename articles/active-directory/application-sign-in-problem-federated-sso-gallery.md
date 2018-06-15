---
title: Problemy przy logowaniu do galerii aplikacji skonfigurowana dla federacyjnych logowanie jednokrotne | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące określonych błędów podczas logowania do aplikacji, które zostały skonfigurowane na podstawie SAML federacyjne logowanie jednokrotne z usługą Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fa1387c3eb11899bb74418b2ff1af4a5d22a73d9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "29384841"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemy przy logowaniu do galerii aplikacji skonfigurowana dla federacyjnego logowania jednokrotnego

Aby rozwiązać problem, należy sprawdzić konfigurację aplikacji w usłudze Azure AD jako wykonaj:

-   Zostały wykonane wszystkie kroki konfiguracji w galerii aplikacji usługi Azure AD.

-   Identyfikator i adres URL odpowiedzi, skonfigurowane w usłudze AAD odpowiadać ich oczekiwanych wartości w aplikacji

-   Przypisano użytkowników do aplikacji

## <a name="application-not-found-in-directory"></a>Nie można odnaleźć w katalogu aplikacji

*Błąd AADSTS70001: Aplikacji o identyfikatorze "https://contoso.com" nie został znaleziony w katalogu*.

**Możliwa przyczyna**

Wystawcę, którego atrybut wysyła z aplikacji do usługi Azure AD w żądaniu SAML jest niezgodne z wartością identyfikatora, skonfigurowane w aplikacji usługi Azure AD.

**Rozdzielczość**

Upewnij się, że atrybut wystawcy w żądaniu SAML jest zgodne identyfikator wartości ustawionej w usłudze Azure AD:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Przejdź do **domeny i adres URL** sekcji. Upewnij się, że wartość w polu tekstowym identyfikatora jest zgodne z wartość identyfikatora wyświetlony w dzienniku błędów.

Po jego jest dopasowanie wysyła wartości przez aplikację w żądaniu SAML i wartość identyfikatora zostały zaktualizowane w usłudze Azure AD, należy zalogować się do aplikacji.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adres, który jest niezgodny z adresów odpowiedzi skonfigurowanych dla aplikacji.

*Błąd AADSTS50011: Adreshttps://contoso.com"nie pasują do adresów odpowiedzi, skonfigurowana dla aplikacji.*

**Możliwa przyczyna**

Wartość AssertionConsumerServiceURL w żądaniu SAML nie jest zgodna wartość adresu URL odpowiedzi lub wzorca skonfigurowane w usłudze Azure AD. Wartość AssertionConsumerServiceURL w żądaniu SAML jest adres URL, zostanie wyświetlony w dzienniku błędów.

**Rozdzielczość**

Upewnij się, że wartość AssertionConsumerServiceURL w żądaniu SAML jest dopasowywania adresu URL odpowiedzi wartości ustawionej w usłudze Azure AD.

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Przejdź do **domeny i adres URL** sekcji. Sprawdź lub zaktualizuj tę wartość w polu tekstowym adres URL odpowiedzi służący do dopasowania wartości AssertionConsumerServiceURL w żądaniu SAML.  
    * Jeśli nie widzisz pole tekstowe adresu URL odpowiedzi, wybierz **Pokaż zaawansowane ustawienia adresu URL** wyboru.

Po jego jest dopasowanie wysyła wartości przez aplikację w żądaniu SAML i wartość adresu URL odpowiedzi zostały zaktualizowane w usłudze Azure AD, należy zalogować się do aplikacji.

## <a name="user-not-assigned-a-role"></a>Nie przypisaną rolę użytkownika

*Błąd AADSTS50105: Zalogowanemu użytkownikowi "brian@contoso.com" nie jest przypisany do roli aplikacji*.

**Możliwa przyczyna**

Użytkownikowi nie udzielono dostępu do aplikacji w usłudze Azure AD.

**Rozdzielczość**

Aby przypisać bezpośrednio co najmniej jednego użytkownika do aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikacji, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Dodaj** przycisk nad **użytkowników i grup** listy, aby otworzyć **Dodaj przydziału** okienka.

9.  Kliknij przycisk **użytkowników i grup** selektora z **Dodaj przydziału** okienka.

10. Wpisz w **Pełna nazwa** lub **adres e-mail** użytkownika planuje się przypisanie do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Zaznacz pole wyboru obok zdjęcia profilu użytkownika lub logo, aby dodać użytkownika do **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typu w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwania według nazwy lub adres e-mail** polu wyszukiwania, a następnie kliknij przycisk wyboru, aby dodać użytkownika do **wybrane** listy.

13. Po zakończeniu wybierania użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup, które ma być przypisany do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektora w **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych użytkowników.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie użytkowników, dla których wybrano mieć możliwość uruchamiania tych aplikacji za pomocą metod opisanych w sekcji Opis rozwiązania.

## <a name="not-a-valid-saml-request"></a>Nie prawidłowy SAML żądanie

*Błąd AADSTS75005: Żądanie nie jest prawidłowy komunikat protokołu Saml2.*

**Możliwa przyczyna**

Usługi Azure AD nie obsługuje SAML żądania wysyłane przez aplikację do logowania jednokrotnego. Dostępne są następujące typowe problemy:

-   Brak wymaganego pola w żądaniu SAML

-   Metoda żądania zakodowane SAML

**Rozdzielczość**

1.  Przechwycenie żądania SAML. czynności opisane w samouczku [debugowanie na języku SAML logowanie jednokrotne do aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) informacje na temat przechwytywania żądania SAML.

2.  Skontaktuj się z dostawcą aplikacji i udziału:

   -   Żądanie SAML

   -   [Wymagania dotyczące protokołu usługi AD pojedynczego logowania jednokrotnego SAML Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Należy sprawdzić poprawność ich obsługę wdrożenia usługi Azure AD SAML logowania jednokrotnego.

## <a name="no-resource-in-requiredresourceaccess-list"></a>Żaden z zasobów na liście requiredResourceAccess

*Błąd AADSTS65005: aplikacja kliencka żąda dostępu do zasobu "00000002-0000-0000-c000-000000000000'. To żądanie nie powiodło się, ponieważ klient nie określił tego zasobu na swojej liście requiredResourceAccess*.

**Możliwa przyczyna**

Obiekt application jest uszkodzony.

**Rozwiązanie: opcja 1**

Aby rozwiązać ten problem, należy dodać wartość Unikatowy identyfikator w konfiguracji usługi Azure AD. Aby dodać wartości identyfikatora, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, skonfigurowaniu logowania jednokrotnego.

7.  Po załadowaniu aplikacji, kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji

8.  W obszarze **domeny i adres URL** sekcji, sprawdź na **Pokaż zaawansowane ustawienia adresu URL**.

9.  w **identyfikator** pole tekstowe wpisz unikatowy identyfikator dla aplikacji.

10. **Zapisz** konfiguracji.


**Opcja rozpoznawania 2**

Jeśli opcja 1 powyżej zakończyło się niepowodzeniem dla Ciebie, spróbuj, usunięcie aplikacji z katalogu. Następnie należy dodać i zmiany konfiguracji aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne

7.  Kliknij przycisk **usunąć** w lewym górnym aplikacji **omówienie** okienka.

8.  Odśwież usługi Azure AD i dodaj aplikację z galerii Azure AD. Następnie należy skonfigurować aplikację

<span id="_Hlk477190176" class="anchor"></span>Po ponownej konfiguracji aplikacji, można logować się do aplikacji.

## <a name="certificate-or-key-not-configured"></a>Certyfikat lub klucz nie jest skonfigurowany

*Błąd AADSTS50003: Nie klucza podpisywania skonfigurowane.*

**Możliwa przyczyna**

Obiekt application jest uszkodzony i usługi Azure AD nie rozpoznaje certyfikatu skonfigurowane dla aplikacji.

**Rozdzielczość**

Aby usunąć i utworzyć nowy certyfikat, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

 * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Utwórz nowy certyfikat** w obszarze **SAML certyfikatu podpisywania** sekcji.

9.  Wybierz datę wygaśnięcia. Następnie kliknij przycisk **zapisać.**

10. Sprawdź **uaktywnić nowego świadectwa** do przesłonięcia aktywnego certyfikatu. Następnie kliknij przycisk **zapisać** w górnej części okienka i zaakceptować certyfikat przerzucania aktywować.

11. W obszarze **certyfikat podpisywania SAML** kliknij **Usuń** do usunięcia **nieużywane** certyfikatu.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem w przypadku dostosowywania SAML oświadczenia wysyłane do aplikacji

Aby dowiedzieć się, jak dostosować oświadczeń atrybutów SAML wysyłanych do aplikacji, zobacz [oświadczeń mapowanie w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
[Debugowanie na języku SAML logowanie jednokrotne do aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)
