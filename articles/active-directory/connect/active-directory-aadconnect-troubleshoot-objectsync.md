---
title: 'Azure AD Connect: Rozwiązywanie problemów z synchronizacją obiektu | Dokumentacja firmy Microsoft'
description: Ten temat zawiera procedurę rozwiązywania problemów z synchronizacją obiektu przy użyciu zadania dotyczące rozwiązywania problemów.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: billmath
ms.openlocfilehash: 9945ad30cc7d8882d8b99f6b4278f2063ab4b7f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Rozwiązywanie problemów z synchronizacją obiektu z synchronizacji Azure AD Connect
Ten dokument zawiera kroki umożliwiające rozwiązywanie problemów z synchronizacją obiektu przy użyciu zadania dotyczące rozwiązywania problemów.

## <a name="troubleshooting-task"></a>Rozwiązywanie problemów z zadania
Dla usługi Azure Active Directory (AAD) wdrożenia Uzyskuj dostęp do wersji 1.1.749.0 lub wyższym, korzystając z zadania dotyczące rozwiązywania problemów w Kreatorze rozwiązywać problemy z synchronizacją obiektu. W przypadku wcześniejszych wersji, należy rozwiązać ręcznie, zgodnie z opisem [tutaj](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Uruchom zadanie rozwiązywania problemów w Kreatorze
Aby uruchomić zadanie rozwiązywania problemów w kreatorze, wykonaj następujące czynności:

1.  Otwórz sesję programu Windows PowerShell na serwerze programu Azure AD Connect z opcji Uruchom jako administrator.
2.  Uruchom `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted`.
3.  Uruchom Kreatora programu Azure AD Connect.
4.  Przejdź do strony dodatkowych zadań, wybierz Rozwiązywanie problemów, a następnie kliknij przycisk Dalej.
5.  Na stronie Rozwiązywanie problemów kliknij przycisk Uruchom, aby uruchomić menu rozwiązywania problemów w programie PowerShell.
6.  W menu głównym wybierz Rozwiązywanie problemów z synchronizacji obiektu.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Rozwiązywanie problemów z parametrów wejściowych
Następujące parametry wejściowe są wymagane przez zadanie rozwiązywania problemów:
1.  **Nazwa wyróżniająca obiektu** — jest to nazwa wyróżniająca obiektu, który wymaga Rozwiązywanie problemów
2.  **Nazwa łącznika usługi AD** — jest to nazwa lasu usługi AD, w którym znajduje się obiekt powyżej.
3.  Poświadczenia administratora globalnego dzierżawy usługi Azure AD ![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Zrozumienie wyników zadania dotyczące rozwiązywania problemów
Rozwiązywania problemów z zadań wykonuje następujące testy:

1.  Wykryj niezgodność nazwy UPN, jeśli obiekt jest synchronizowany z usługą Azure Active Directory
2.  Sprawdź, czy obiekt jest filtrowana z powodu filtrowania domeny
3.  Sprawdź, czy obiekt jest filtrowana powodu do filtrowania jednostki Organizacyjnej
4.  Sprawdź, czy synchronizacji obiektu jest zablokowany z powodu połączona Skrzynka pocztowa
5. Sprawdź, czy obiekt jest grupy dystrybucji dynamiczne, które nie mają być synchronizowane

Pozostałej części tej sekcji opisano określone wyniki, które są zwracane przez zadania. W każdym przypadku zadania zawiera analizę następuje zalecane działania, aby rozwiązać ten problem.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Wykryj niezgodność nazwy UPN, jeśli obiekt jest synchronizowany z usługą Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Sufiks nazwy UPN nie są weryfikowane przez dzierżawy Azure AD
Gdy UserPrincipalName (UPN) / sufiksu alternatywnego Identyfikatora logowania nie jest weryfikowany z dzierżawą usługi Azure AD, a następnie usługi Azure Active Directory zastępuje sufiksy nazw UPN nazwy domeny domyślnej "onmicrosoft.com".

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Zmiana sufiks głównej nazwy użytkownika z jednej domeny federacyjnej do innej domeny federacyjnej
Usługa Azure Active Directory nie zezwalaj na synchronizację UserPrincipalName (UPN) / domeny federacyjnej zmiany sufiksu alternatywnego Identyfikatora logowania z jednej domeny federacyjnej na inny. Dotyczy to domen, są weryfikowane za pomocą dzierżawy Azure AD, które mają typ uwierzytelniania jako federacyjne.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Dzierżawy DirSync funkcja "SynchronizeUpnForManagedUsers" Azure AD jest wyłączona
Po wyłączeniu funkcji DirSync dzierżawy Azure AD "SynchronizeUpnForManagedUsers" Azure Active Directory nie zezwala na aktualizacje synchronizacji do UserPrincipalName/alternatywnego Identyfikatora logowania dla kont licencjonowanym użytkownikiem z uwierzytelnianiem zarządzanych.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Obiekt jest filtrowana z powodu filtrowania domeny
### <a name="domain-is-not-configured-to-sync"></a>Domena nie jest skonfigurowana do synchronizacji
Obiekt jest poza zakresem z powodu domeny nie jest skonfigurowany. W poniższym przykładzie obiekt jest zsynchronizowany zakresu należącego do domeny jest odfiltrowane z synchronizacji.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domena jest skonfigurowana do synchronizacji, ale nie ma profilów/uruchamianie wykonywania kroków
Obiekt jest poza zakresem jak brakuje domeny uruchomić profile/uruchamianie kroków. W poniższym przykładzie obiekt jest zsynchronizowany zakresu należącego do domeny jest brak pełne importowanie profilu uruchamiania wykonywania czynności.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Obiekt jest filtrowana powodu do filtrowania jednostki Organizacyjnej
Obiekt jest zakresem zsynchronizowane z powodu konfiguracji filtrowania jednostki Organizacyjnej. W poniższym przykładzie obiekt należy do jednostki Organizacyjnej = NoSync, DC = bvtadwbackdc, DC = com.  Tej jednostki Organizacyjnej nie jest uwzględniony w zakresie synchronizacji.</br>

![JEDNOSTKI ORGANIZACYJNEJ](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch7.png)

## <a name="linked-mailbox-issue"></a>Połączone problem skrzynki pocztowej
Połączona Skrzynka pocztowa powinien być skojarzony z konta głównego zewnętrznego znajduje się w innym lesie, konto zaufane. Jeśli brak takiego zewnętrznego konta głównego, a następnie Azure AD Connect nie będzie synchronizować użytkownika konta odpowiada połączoną skrzynkę pocztową w lesie programu Exchange do dzierżawy usługi Azure AD.</br>
![Połączona Skrzynka pocztowa](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Dynamiczna grupa dystrybucji problem
Z powodu różnych różnice między lokalnymi usługi Active Directory i Azure Active Directory, Azure AD Connect nie synchronizuje się grupy dynamiczną dystrybucję do dzierżawy usługi Azure AD.

![Grupy dystrybucji dynamiczne](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch13.png)

## <a name="html-report"></a>Raport HTML
Oprócz analizowania obiektu, rozwiązywania problemów zadań generuje raport HTML, który zawiera wszystko, co wiadomo o obiekcie. Ten raport HTML można udostępniać zespołem pomocy technicznej w celu dalszego rozwiązywania problemów, jeśli to konieczne.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch8.png)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
