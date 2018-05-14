---
title: Nie można uzyskać dostępu tego błędu aplikacji firmowych, korzystając z aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Jak rozwiązać typowe problemy z dostępem z aplikacji serwera Proxy aplikacji usługi Azure AD.
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
ms.openlocfilehash: f57b470208ab3bd071b83c0736efa739c7e70363
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Błąd "Nie można uzyskać dostępu do tej aplikacji firmowych" podczas korzystania z aplikacji serwera Proxy aplikacji

Ten artykuł ułatwia rozwiązywanie typowych problemów "nie ma dostępu tej aplikacji firmowych" Błąd aplikacji serwera Proxy aplikacji usługi Azure AD.

## <a name="overview"></a>Przegląd
Gdy zostanie wyświetlony ten błąd, należy znaleźć kod stanu na stronę błędu. Ten kod jest prawdopodobnie jednym z następujących kodów stanu:

-   **Limit czasu bramy**: nie można nawiązać połączenia z łącznika jest serwer Proxy aplikacji usługi. Ten błąd zazwyczaj wskazuje na problem z przypisaniem łącznika, łącznik, lub zasady sieci wokół łącznika.

-   **Zła brama**: łącznik nie jest w stanie nawiązać aplikacja wewnętrznej bazy danych. Ten błąd może wskazywać na błąd konfiguracji aplikacji.

-   **Dostęp zabroniony**: użytkownik nie ma uprawnień dostępu do aplikacji. Ten błąd może się zdarzyć, gdy użytkownik nie jest przypisany do aplikacji w usłudze Azure Active Directory lub jeśli do wewnętrznej bazy danych użytkownika nie ma uprawnień dostępu do aplikacji.

Aby znaleźć kod, spójrz na tekst w lewym dolnym rogu komunikat o błędzie dla pola "Kod stanu". Wyszukaj także żadnych dodatkowych porad w dolnej części strony.

   ![Błąd limitu czasu bramy](./media/application-proxy/connection-problem.png)

Aby uzyskać więcej informacji na temat rozwiązywania problemów przyczynę tych błędów oraz dodatkowe szczegóły dotyczące sugerowanej poprawki, zobacz odpowiedniej sekcji poniżej.

## <a name="gateway-timeout-errors"></a>Błędy przekroczenia limitu czasu bramy

Limit czasu bramy występuje, gdy usługa próbuje nawiązać połączenie z łącznika i nie może w określonym limicie. Przyczyną tego błędu jest zazwyczaj aplikacji przypisany do grupy łącznika z nie łączników pracy lub niektóre porty wymagane przez łącznik nie są otwarte.


## <a name="bad-gateway-errors"></a>Zły błędy bramy

Błąd bramy zły wskazuje, że łącznik jest nawiązać aplikacja wewnętrznej bazy danych. Upewnij się, opublikowano właściwej aplikacji. Typowe błędy, które są przyczyną tego błędu są:

-   Literówka lub błąd wewnętrzny adres URL

-   Publikowanie nie katalogu głównego aplikacji. Na przykład publikowania <http://expenses/reimbursement> , ale próby dostępu <http://expenses>

-   Problemy związane z konfiguracją delegowanie ograniczone protokołu Kerberos (KCD)

-   Problemy z aplikacjami wewnętrznej bazy danych

## <a name="forbidden-errors"></a>Błędy niedozwolonych

Jeśli zostanie wyświetlony błąd zabronione, użytkownik nie zostanie przypisana do aplikacji. Ten błąd może być w usłudze Azure Active Directory lub w aplikacji zaplecza.

Aby dowiedzieć się, jak można przypisać użytkowników do aplikacji na platformie Azure, zobacz [dokumentacji konfiguracji](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal#add-a-test-user).

Jeśli potwierdzisz, że użytkownik jest przypisany do aplikacji na platformie Azure, sprawdź konfigurację użytkownika w aplikacji zaplecza. Jeśli używasz uwierzytelniania systemu Windows zintegrowany/delegowania ograniczonego protokołu Kerberos, wyświetlona strona KCD Rozwiązywanie problemów z wskazówki.

## <a name="check-the-applications-internal-url"></a>Sprawdź wewnętrzny adres URL aplikacji

Pierwszym krokiem szybki, dwukrotnie Sprawdź i napraw wewnętrznego adresu URL przez otwarcie aplikacji za pomocą **aplikacje dla przedsiębiorstw**, wybierając **serwera Proxy aplikacji** menu. Sprawdź, czy wewnętrzny adres URL jest używana z sieci lokalnej do uzyskania dostępu do aplikacji.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Sprawdź, czy aplikacja jest przypisany do pracy grupy łącznika

Aby sprawdzić aplikacji jest przypisane do pracy łącznika grupy:

1.  Otwórz aplikację w portalu, przechodząc do **usługi Azure Active Directory**, klikając pozycję na **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje.** Otwórz aplikację, a następnie wybierz **serwera Proxy aplikacji** z menu po lewej stronie.

2.  Sprawdź pola grupy łącznika. Jeśli w grupie nie żadne aktywne łączników, zostanie wyświetlone ostrzeżenie. Jeśli nie widzisz wszelkie ostrzeżenia, Przenieś do "Sprawdź, czy wszystkie wymagane porty na białej".

3.  Jeśli nieprawidłowa grupy łącznika jest wyświetlany, użyj listy rozwijanej wybierz grupę poprawne, i upewnij się, że nie są już wyświetlane ostrzeżenia. Jeśli łącznik grupie jest wyświetlany, kliknij komunikat ostrzegawczy, aby otworzyć stronę z zarządzania łącznika.

4.  W tym miejscu istnieją przechodzenia na kilka sposobów:

  * Przenieś do grupy łącznika usługi active: Jeśli masz łącznika usługi active powinna należeć do tej grupy i ma procesów z wiersza do zaplecza aplikacji docelowej, można przenieść łącznika w przypisanej grupie. Aby to zrobić, kliknij przycisk łącznika. W polu "Łącznik grupy" Użyj listy rozwijanej wybierz poprawną grupę, a następnie kliknij przycisk Zapisz.

  * Pobierz nowy łącznik, dla tej grupy: na tej stronie można uzyskać łącze do [Pobierz nowy łącznik](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Zainstaluj łącznik na maszynie z bezpośredniego procesów of wiersza do wewnętrznej bazy danych aplikacji. Typicall, łącznika jest zainstalowana na tym samym serwerze co aplikacja. Umożliwia pobieranie łącznika łącze Pobierz łącznik na komputerze docelowym. Następnie kliknij łącznik i umożliwia upewnij się, że należy on do grupy prawo listy rozwijanej "łącznik grupy".

  * Zbadaj łącznik nieaktywne: Jeśli łącznik pokazuje jako nieaktywny, nie jest w stanie celu dotarcia do usługi. Ten błąd jest zwykle z powodu niektóre porty wymagane blokowane. Aby rozwiązać ten problem, Przenieś do "Sprawdź wszystkie wymagane porty są białej".

Po użyciu następujące kroki, aby upewnić się, że aplikacja jest przypisany do grupy o pracy łączników, przetestuj aplikację ponownie. Jeśli nadal nie działa ono, przejdź do następnej sekcji.

## <a name="check-all-required-ports-are-whitelisted"></a>Sprawdź, czy wszystkie wymagane porty na białej

Aby sprawdzić, czy wszystkie wymagane porty są otwarte, zobacz dokumentację na otwieranie portów. Jeśli wymagane porty są otwarte, przejdź do następnej sekcji.

## <a name="check-for-other-connector-errors"></a>Sprawdź, czy inne błędy łącznika

Jeśli żaden z powyższych rozwiązać ten problem, następnym krokiem jest Wyszukaj problemy lub błędy z łącznik sam. Można wyświetlić niektórych typowych błędów w [Rozwiązywanie problemów dotyczących dokumentu](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors). 

Można również sprawdzić bezpośrednio w dziennikach łącznika, aby zidentyfikować wszelkie błędy. Wiele komunikatów o błędach udostępnianie zalecenia dotyczące poprawki. Aby wyświetlić dzienniki, zobacz [dokumentacji łączniki](application-proxy-understand-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Dodatkowe rozwiązania

Jeśli powyższe nie rozwiąże problemu, istnieje kilka różnych przyczyn. Aby zidentyfikować problem:

Jeśli aplikacja jest skonfigurowana do używania zintegrowanego uwierzytelniania systemu Windows (IWA), przetestuj aplikację bez rejestracji jednokrotnej. Jeśli nie, aby przejść do następnego akapitu. Aby sprawdzić aplikacji bez rejestracji jednokrotnej, Otwórz aplikację za pomocą **aplikacje dla przedsiębiorstw,** i przejdź do **rejestracji jednokrotnej** menu. Zmień na liście rozwijanej "Zintegrowane uwierzytelnianie systemu Windows" do "Azure AD rejestracji jednokrotnej wyłączone". 

Teraz Otwórz przeglądarkę i spróbuj uzyskać dostęp do aplikacji ponownie. Powinien zostać wyświetlony monit o uwierzytelnienie oraz uzyskiwanie do aplikacji. Jeśli do uwierzytelniania, problem dotyczy konfiguracji delegowanie ograniczone protokołu Kerberos (KCD), która umożliwia logowanie jednokrotne. Aby uzyskać więcej informacji zobacz stronę KCD Rozwiązywanie problemów.

Jeśli będziesz kontynuować wyświetlić ten błąd, przejdź do komputera, na którym zainstalowano łącznik, otwórz przeglądarkę i próbują uzyskać dostęp wewnętrzny adres URL używany dla aplikacji. Łącznik działa jak innego klienta z tym samym komputerze. Jeśli nie można uzyskać dostęp do aplikacji, sprawdź, dlaczego tej maszynie jest w stanie połączyć aplikacji lub używać łącznika na serwerze, który jest w stanie uzyskać dostępu do aplikacji.

Jeśli można uzyskać dostęp do aplikacji z tego komputera, aby wyszukać problemy lub błędy łącznik sam. Można wyświetlić niektórych typowych błędów w [Rozwiązywanie problemów dotyczących dokumentu](active-directory-application-proxy-troubleshoot.md#connector-errors). Można również sprawdzić bezpośrednio w dziennikach łącznika, aby zidentyfikować wszelkie błędy. Wiele z naszych komunikaty o błędach można udostępnić bardziej szczegółowe zalecenia dotyczące poprawki. Aby dowiedzieć się wyświetlić dzienniki, zobacz [naszej dokumentacji łączniki](application-proxy-understand-connectors.md#under-the-hood).

## <a name="next-steps"></a>Kolejne kroki
[Zrozumienie łączniki serwera Proxy aplikacji usługi Azure AD](application-proxy-understand-connectors.md)
