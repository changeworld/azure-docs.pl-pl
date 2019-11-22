---
title: Nie można uzyskać dostępu do tego błędu aplikacji firmowej przy użyciu aplikacji serwera proxy aplikacji
description: Jak rozwiązać typowe problemy dotyczące dostępu do aplikacji serwera Proxy aplikacji usługi Azure AD.
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
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d61f4b4bce9b8287dc13237f071684ea5d135fa
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275466"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>"Nie może uzyskiwać dostęp do tej aplikacji firmowej" błąd, podczas korzystania z aplikacji serwera Proxy aplikacji

Ten artykuł pomaga w rozwiązywaniu typowych problemów dotyczących błędów "tej aplikacji firmowej nie można uzyskać dostępu do" z aplikacji serwera Proxy aplikacji usługi Azure AD.

## <a name="overview"></a>Omówienie

Gdy zostanie wyświetlony ten błąd, należy znaleźć kod stanu na stronę błędu. Ten kod jest prawdopodobnie jednym z poniższych kodów stanu:

- **Limit czasu bramy**: usługa serwera Proxy aplikacji nie ma dostępu do łącznika. Ten błąd zazwyczaj wskazuje na problem z przypisaniem łącznika, łącznik, lub sieci reguł wokół łącznika.
- **Zła brama**: łącznik nie może uzyskać dostępu do aplikacji zaplecza. Ten błąd może wskazywać na błędną konfiguracją aplikacji.
- **Dostęp zabroniony**: użytkownik nie ma autoryzacji do dostępu do aplikacji. Ten błąd może wystąpić, gdy użytkownik nie jest przypisany do aplikacji w usłudze Azure Active Directory lub jeśli do wewnętrznej bazy danych użytkownika nie ma uprawnień dostępu do aplikacji.

Aby znaleźć kod, spójrz na tekst w lewym dolnym rogu komunikat o błędzie dla pola "Kod stanu:". Zobacz wszystkie dodatkowe porady w dolnej części strony.

![Przykład: błąd limitu czasu bramy](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Aby uzyskać szczegółowe informacje na temat rozwiązywania problemów przyczyny tych błędów i szczegółowe informacje na temat sugerowanymi poprawkami, zobacz odpowiedniej sekcji poniżej.

## <a name="gateway-timeout-errors"></a>Błędy przekroczenia limitu czasu bramy

Limit czasu bramy występuje, gdy usługa próbuje nawiązać połączenie z łącznika i nie może w określonym limicie. Przyczyną tego błędu jest zazwyczaj aplikacji przypisane do grupy łączników z łącznikami nie pracy lub nie są otwarte Niektóre porty wymagane przez łącznik.

## <a name="bad-gateway-errors"></a>Zły błędy bramy

Błąd Zła brama wskazuje łącznika nie można uzyskać dostępu do aplikacji zaplecza. Upewnij się, czy zostały opublikowane właściwej aplikacji. Typowych błędów, które przyczyny wystąpienia tego błędu to:

- Błąd pisowni lub błędy w wewnętrznego adresu URL
- Publikowanie nie katalogu głównego aplikacji. Na przykład publikowania <http://expenses/reimbursement> , ale podczas próby dostępu do zasobu <http://expenses>
- Problemy z konfiguracją delegowanie ograniczone protokołu Kerberos (KCD)
- Problemy z aplikacją zaplecza

## <a name="forbidden-errors"></a>Zabronione błędy

Jeśli zostanie wyświetlony błąd "niedozwolone", użytkownik nie zostały przypisane do aplikacji. Ten błąd może być w usłudze Azure Active Directory lub w aplikacji zaplecza.

Aby dowiedzieć się, jak przypisać użytkowników do aplikacji na platformie Azure, zobacz [dokumentacją konfiguracji](application-proxy-add-on-premises-application.md#test-the-application).

Jeśli upewnij się, że użytkownik jest przypisany do aplikacji na platformie Azure, sprawdź konfigurację użytkownika w aplikacji zaplecza. Jeśli używasz uwierzytelniania Kerberos ograniczone delegowanie/Integrated Windows zobacz stronę Rozwiązywanie problemów z ograniczonego delegowania protokołu Kerberos, wskazówki dotyczące.

## <a name="check-the-applications-internal-url"></a>Sprawdź wewnętrzny adres URL aplikacji

Pierwszym krokiem szybki, sprawdź i rozwiązać wewnętrzny adres URL, otwierając aplikację za pomocą **aplikacje dla przedsiębiorstw**, a następnie wybierając pozycję **serwera Proxy aplikacji** menu. Upewnij się, że wewnętrzny adres URL jest używany w sieci lokalnej w celu uzyskania dostępu do aplikacji.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Sprawdź, czy aplikacja jest przypisany do działającego grupy łączników

Do weryfikowania aplikacji jest przypisany do pracy grupy łączników:

1. Otwórz aplikację w portalu, przechodząc do **usługi Azure Active Directory**, klikając na **aplikacje dla przedsiębiorstw**, następnie **wszystkich aplikacji.** Otwórz aplikację, a następnie wybierz **serwera Proxy aplikacji** menu po lewej stronie.
1. Przyjrzyj się pole grupy łączników. W przypadku Brak aktywnych łączników w grupie, jest wyświetlane ostrzeżenie. Jeśli nie widzisz żadnych ostrzeżeń, przejdź do, aby sprawdzić, czy wszystkie wymagane porty są dozwolone.
1. Jeśli Nieprawidłowa grupa łączników jest wyświetlany, wybierz za pomocą listy rozwijanej właściwej grupy, a upewnij się, że nie są już wyświetlane ostrzeżenia. Jeśli wyświetlana jest zamierzony grupy łączników, kliknij przycisk z komunikatem ostrzegawczym, aby otworzyć stronę za pomocą funkcji zarządzania łącznika.
1. W tym miejscu istnieje kilka sposobów, aby przejść do szczegółów dalszych:

   - Przenieś łącznika usługi active do grupy: Jeśli masz aktywny łącznik, powinny należeć do tej grupy, która ma linii wzroku do docelowej aplikacji zaplecza, można przenieść łącznika w przypisanej grupie. Aby to zrobić, kliknij przycisk łącznika. W polu "Grupa łączników" Użyj listy rozwijanej wybierz poprawną grupę, a następnie kliknij przycisk Zapisz.
   - Pobrać nowy łącznik dla tej grupy: na tej stronie można uzyskać link do [pobrać nowy łącznik](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Łącznik można zainstalować na komputerze przy użyciu bezpośredniego linii wzroku do aplikacji zaplecza. Zwykle łącznik jest instalowany na tym samym serwerze co aplikacja. Umożliwia pobieranie łącznika link Pobierz łącznik na komputerze docelowym. Następnie kliknij łącznik, a następnie użyć listy rozwijanej "łącznik grupy", aby upewnij się, że należy on do odpowiedniej grupy.
   - Badanie łącznik nieaktywne: Jeśli łącznik jest wyświetlana jako nieaktywny, nie jest w stanie w celu dotarcia do usługi. Ten błąd jest zwykle z powodu niektóre porty wymagane blokowane. Aby rozwiązać ten problem, należy przejść do, aby sprawdzić, czy wszystkie wymagane porty są dozwolone.

Po zakończeniu korzystania z następujące kroki, aby upewnić się, że aplikacja została przypisana do grupy z pracy łączniki, przetestuj aplikację ponownie. Jeśli nadal nie działa, przejdź do następnej sekcji.

## <a name="check-all-required-ports-are-open"></a>Sprawdź, czy wszystkie wymagane porty są otwarte

Aby sprawdzić, czy wszystkie wymagane porty są otwarte, zobacz dokumentację na otwieranie portów. Jeśli wszystkie wymagane porty są otwarte, przejdź do następnej sekcji.

## <a name="check-for-other-connector-errors"></a>Sprawdź inne błędy łącznika

Jeśli żadne z powyższych nie rozwiąże problemu, następnym krokiem jest do wyszukania problemów lub błędy łącznik sam. Możesz zobaczyć niektórych typowych błędów [dokumentu rozwiązywanie](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors).

Można również przeglądać bezpośrednio dzienniki łącznika, aby zidentyfikować błędy. Udostępnij liczbę komunikatów o błędach zalecenia dotyczące poprawki. Aby wyświetlić dzienniki, zobacz [dokumentacji łączników](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Dodatkowe rozwiązania.

Jeśli powyższe nie rozwiąże to problemu, istnieją kilku różnych przyczyn. Aby zidentyfikować problem:

Jeśli aplikacja jest skonfigurowany do używania zintegrowanego Windows Authentication (Zintegrowane), przetestuj aplikację bez logowania jednokrotnego. Jeśli nie, należy przejść do następnego akapitu. Aby sprawdzić aplikacji bez rejestracji jednokrotnej, Otwórz aplikację za pomocą **aplikacje dla przedsiębiorstw,** i przejdź do **logowania jednokrotnego** menu. Zmień listę rozwijaną z "Zintegrowane uwierzytelnianie Windows" "Azure AD logowanie jednokrotne wyłączone".

Teraz Otwórz przeglądarkę i spróbuj uzyskać dostęp do aplikacji, ponownie. Powinien pojawić się monit dla uwierzytelniania i Uzyskaj do aplikacji. Jeśli jesteś w stanie uwierzytelnić, problem dotyczy konfiguracji delegowania ograniczonego protokołu Kerberos (KCD), która umożliwia logowanie jednokrotne. Aby uzyskać więcej informacji zobacz stronę Rozwiązywanie problemów z ograniczonego delegowania protokołu Kerberos.

Jeśli będziesz kontynuować wyświetlić błąd, przejdź do maszyny, na którym zainstalowano łącznik, otwórz przeglądarkę i otwórz próbują uzyskać dostęp wewnętrzny adres URL używany dla aplikacji. Łącznik działa jak innego klienta, w tym samym komputerze. Jeśli nie można uzyskać dostępu do aplikacji, należy zbadać, dlaczego komputer ten jest nie można uzyskać dostępu do aplikacji lub używanie łącznika na serwerze, który jest w stanie uzyskać dostęp do aplikacji.

Jeśli możesz uzyskać dostęp do aplikacji na tej maszynie, aby wyszukać problemy lub błędy łącznik sam. Możesz zobaczyć niektórych typowych błędów [dokumentu rozwiązywanie](application-proxy-troubleshoot.md#connector-errors). Można również przeglądać bezpośrednio dzienniki łącznika, aby zidentyfikować błędy. Liczbę naszym komunikaty o błędach można udostępnić bardziej szczegółowe zalecenia dotyczące poprawki. Aby dowiedzieć się, jak wyświetlić dzienniki, zobacz [naszej dokumentacji łączników](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Następne kroki

[Omówienie łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
