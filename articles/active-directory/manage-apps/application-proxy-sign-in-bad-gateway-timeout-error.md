---
title: Nie można uzyskać dostępu do tego błędu aplikacji firmowych za pomocą aplikacji Proxy aplikacji
description: Jak rozwiązać typowe problemy z dostępem do aplikacji serwera proxy aplikacji usługi Azure AD.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275466"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Błąd "Nie można uzyskać dostępu do tej aplikacji firmowych" podczas korzystania z aplikacji proxy aplikacji

Ten artykuł ułatwia rozwiązywanie typowych problemów z błędem "Nie można uzyskać dostępu do tej aplikacji firmowej" w aplikacji serwera proxy aplikacji usługi Azure AD.

## <a name="overview"></a>Omówienie

Po wyświetleniu tego błędu znajdź kod stanu na stronie błędu. Ten kod jest prawdopodobnie jednym z następujących kodów stanu:

- **Limit czasu bramy:** Usługa serwera proxy aplikacji nie może dotrzeć do łącznika. Ten błąd zazwyczaj wskazuje na problem z przypisaniem łącznika, samym łącznikiem lub regułami sieci wokół łącznika.
- **Bad Gateway:** Łącznik nie może dotrzeć do aplikacji wewnętrznej bazy danych. Ten błąd może wskazywać na błędną konfigurację aplikacji.
- **Zabronione**: Użytkownik nie jest upoważniony do dostępu do aplikacji. Ten błąd może się zdarzyć, gdy użytkownik nie jest przypisany do aplikacji w usłudze Azure Active Directory lub jeśli na wewnętrznej bazy danych użytkownik nie ma uprawnień dostępu do aplikacji.

Aby znaleźć kod, spójrz na tekst w lewym dolnym rogu komunikatu o błędzie dla pola "Kod stanu". Poszukaj również dodatkowych wskazówek na dole strony.

![Przykład: błąd limitu czasu bramy](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Szczegółowe informacje na temat rozwiązywania problemów z główną przyczyną tych błędów oraz więcej informacji na temat sugerowanych poprawek można znaleźć w odpowiedniej sekcji poniżej.

## <a name="gateway-timeout-errors"></a>Błędy limitu czasu bramy

Limit czasu bramy występuje, gdy usługa próbuje dotrzeć do łącznika i nie może w oknie limitu czasu. Ten błąd jest zazwyczaj spowodowany przez aplikację przypisaną do grupy łączników bez łączników roboczych lub niektóre porty wymagane przez łącznik nie są otwarte.

## <a name="bad-gateway-errors"></a>Nieprawidłowe błędy bramy

Błąd nieprawidłowej bramy wskazuje, że łącznik nie może dotrzeć do aplikacji wewnętrznej bazy danych. upewnij się, że została opublikowana poprawna aplikacja. Typowe błędy, które powodują ten błąd są:

- Literówka lub pomyłka w wewnętrznym adresie URL
- Nie publikowanie katalogu głównego aplikacji. Na przykład <http://expenses/reimbursement> publikowanie, ale próba uzyskania dostępu<http://expenses>
- Problemy z konfiguracją delegowania ograniczonego protokołu Kerberos (KCD)
- Problemy z aplikacją wewnętrznej bazy danych

## <a name="forbidden-errors"></a>Zabronione błędy

Jeśli zostanie wyświetlony zabroniony błąd, użytkownik nie został przypisany do aplikacji. Ten błąd może być w usłudze Azure Active Directory lub w aplikacji wewnętrznej bazy danych.

Aby dowiedzieć się, jak przypisać użytkowników do aplikacji na platformie Azure, zobacz [dokumentację konfiguracji](application-proxy-add-on-premises-application.md#test-the-application).

Jeśli potwierdzisz, że użytkownik jest przypisany do aplikacji na platformie Azure, sprawdź konfigurację użytkownika w aplikacji wewnętrznej bazy danych. Jeśli używasz protokołu Kerberos Ograniczone delegowanie/zintegrowane uwierzytelnianie systemu Windows, zobacz KCD rozwiązywania problemów strony wskazówki.

## <a name="check-the-applications-internal-url"></a>Sprawdź wewnętrzny adres URL aplikacji

W pierwszym szybkim kroku należy dokładnie sprawdzić i naprawić wewnętrzny adres URL, otwierając aplikację za pośrednictwem **aplikacji Enterprise Applications,** a następnie wybierając menu **Serwer proxy aplikacji.** Sprawdź, czy wewnętrzny adres URL jest używany z sieci lokalnej w celu uzyskania dostępu do aplikacji.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Sprawdź, czy aplikacja jest przypisana do działającej grupy łączników

Aby sprawdzić, czy aplikacja jest przypisana do działającej grupy łączników:

1. Otwórz aplikację w portalu, przechodząc do **usługi Azure Active Directory,** klikając pozycję **Aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje.** Otwórz aplikację, a następnie wybierz pozycję **Serwer proxy aplikacji** z lewego menu.
1. Spójrz na pole Grupa łączników. Jeśli w grupie nie ma żadnych aktywnych łączników, zostanie wyświetlone ostrzeżenie. Jeśli nie widzisz żadnych ostrzeżeń, przejdź dalej, aby sprawdzić, czy wszystkie wymagane porty są dozwolone.
1. Jeśli wyświetlana jest nieprawidłowa grupa łączników, użyj listy rozwijanej, aby wybrać właściwą grupę i upewnij się, że nie są już wyświetlane żadne ostrzeżenia. Jeśli wyświetlana jest planowana grupa łączników, kliknij komunikat ostrzegawczy, aby otworzyć stronę z zarządzaniem łącznikami.
1. W tym miejscu, istnieje kilka sposobów, aby wiercić w dalszej:

   - Przenieś aktywny łącznik do grupy: Jeśli masz aktywny łącznik, który powinien należeć do tej grupy i ma linię wzroku do docelowej aplikacji wewnętrznej bazy danych, możesz przenieść łącznik do przypisanej grupy. Aby to zrobić, kliknij łącznik. W polu "Grupa łączników" użyj listy rozwijanej, aby wybrać właściwą grupę, a następnie kliknij przycisk Zapisz.
   - Pobierz nowy łącznik dla tej grupy: Na tej stronie możesz pobrać [łącze](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download)do pobrania nowego łącznika . Zainstaluj złącze na komputerze z bezpośrednim celownikiem do aplikacji wewnętrznej bazy danych. Zazwyczaj łącznik jest zainstalowany na tym samym serwerze co aplikacja. Użyj łącza Pobierz łącznika, aby pobrać łącznik na komputer docelowy. Następnie kliknij łącznik i użyj listy rozwijanej "Grupa łączników", aby upewnić się, że należy ona do odpowiedniej grupy.
   - Zbadaj nieaktywny łącznik: Jeśli łącznik jest wyświetlany jako nieaktywny, nie może dotrzeć do usługi. Ten błąd jest zazwyczaj ze względu na niektóre wymagane porty są blokowane. Aby rozwiązać ten problem, przejdź dalej, aby sprawdzić, czy wszystkie wymagane porty są dozwolone.

Po użyciu tych kroków, aby upewnić się, że aplikacja jest przypisana do grupy z łącznikami roboczymi, ponownie przetestuj aplikację. Jeśli nadal nie działa, przejdź do następnej sekcji.

## <a name="check-all-required-ports-are-open"></a>Sprawdź, czy wszystkie wymagane porty są otwarte

Aby sprawdzić, czy wszystkie wymagane porty są otwarte, zapoznaj się z dokumentacją dotyczącą otwierania portów. Jeśli wszystkie wymagane porty są otwarte, przejdź do następnej sekcji.

## <a name="check-for-other-connector-errors"></a>Sprawdź, czy nie ma innych błędów łącznika

Jeśli żaden z powyższych rozwiązać problem, następnym krokiem jest wyszukać problemy lub błędy z łącznika, sam. W [dokumencie Rozwiązywanie problemów](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors)można zobaczyć kilka typowych błędów .

Można również spojrzeć bezpośrednio na dzienniki łącznika, aby zidentyfikować wszelkie błędy. Wiele komunikatów o błędach udostępnia konkretne zalecenia dotyczące poprawek. Aby wyświetlić dzienniki, zapoznaj się z [dokumentacją łączników](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Dodatkowe rezolucje

Jeśli powyższe nie rozwiązał problemu, istnieje kilka różnych możliwych przyczyn. Aby zidentyfikować problem:

Jeśli aplikacja jest skonfigurowana do używania zintegrowanego uwierzytelniania systemu Windows (IWA), przetestuj aplikację bez logowania jednokrotnego. Jeśli nie, przejdź do następnego akapitu. Aby sprawdzić aplikację bez logowania jednokrotnego, otwórz aplikację za pośrednictwem **aplikacji enterprise applications** i przejdź do menu **logowanie jednokrotne.** Zmień z listy rozwijanej z "Zintegrowane uwierzytelnianie systemu Windows" na "Azure AD logowanie jednokrotne wyłączone".

Teraz otwórz przeglądarkę i spróbuj ponownie uzyskać dostęp do aplikacji. Powinien zostać wyświetlony monit o uwierzytelnienie i dostać się do aplikacji. Jeśli można uwierzytelnić, problem dotyczy konfiguracji delegowania ograniczonego protokołu Kerberos (KCD), która umożliwia logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz stronę Rozwiązywanie problemów z KCD.

Jeśli nadal widzisz błąd, przejdź do komputera, na którym jest zainstalowany łącznik, otwórz przeglądarkę i spróbuj uzyskać wewnętrzny adres URL używany dla aplikacji. Łącznik działa jak inny klient z tego samego komputera. Jeśli nie możesz uzyskać dostępu do aplikacji, sprawdź, dlaczego ten komputer nie może uzyskać dostępu do aplikacji, lub użyj łącznika na serwerze, który jest w stanie uzyskać dostęp do aplikacji.

Jeśli można dotrzeć do aplikacji z tego komputera, aby wyszukać problemy lub błędy z łącznikiem, sam. W [dokumencie Rozwiązywanie problemów](application-proxy-troubleshoot.md#connector-errors)można zobaczyć kilka typowych błędów . Można również spojrzeć bezpośrednio na dzienniki łącznika, aby zidentyfikować wszelkie błędy. Wiele z naszych komunikatów o błędach może udostępniać bardziej szczegółowe zalecenia dotyczące poprawek. Aby dowiedzieć się, jak przeglądać dzienniki, zapoznaj się z [naszą dokumentacją łączników.](application-proxy-connectors.md#under-the-hood)

## <a name="next-steps"></a>Następne kroki

[Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
