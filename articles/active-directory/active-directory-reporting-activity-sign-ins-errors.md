---
title: Kody błędów w raportach działań związanych z logowaniem w portalu usługi Azure Active Directory | Microsoft Docs
description: Dokumentacja dotycząca kodów błędów w raportach działań związanych z logowaniem.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 47894f0a3d7b74c8d0e271afb7d2657ce7d3be0c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Kody błędów w raportach działań związanych z logowaniem w portalu usługi Azure Active Directory

Dzięki informacjom zawartym w raporcie operacji logowania użytkowników można uzyskać odpowiedzi na pytania, takie jak:

- Kto logował się za pomocą usługi Azure Active Directory?
- Do których aplikacji logowali się użytkownicy?
- Które operacje logowania zakończyły się błędem i dlaczego?

Ten temat zawiera listę kodów błędów i powiązane opisy. 

## <a name="how-can-i-display-failed-sign-ins"></a>Jak mogę wyświetlić nieudane operacje logowania? 

Pierwszym punktem wejścia do wszystkich danych działań związanych z logowaniem jest pozycja **[Logowania](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** w sekcji **Działania** usługi **Azure Active**.


![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Działania związane z logowaniem")

W raporcie logowań można wyświetlić wszystkie nieudane logowania, wybierając wartość **Niepowodzenie** w obszarze **Stan logowania**.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Działania związane z logowaniem")

Kliknięcie elementu na wyświetlonej liście powoduje otwarcie bloku **Szczegóły działań: logowania**. Ten widok zawiera wszystkie szczegóły operacji logowania śledzone przez usługę Azure Active Directory, takie jak na przykład **kod błędu logowania** i **przyczyna niepowodzenia**.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Działania związane z logowaniem")


Aby uzyskać dostęp do danych logowania, zamiast witryny Azure Portal można również użyć [interfejsu API raportowania](active-directory-reporting-api-getting-started-azure-portal.md).


W poniższej sekcji przedstawiono kompletne omówienie wszystkich możliwych błędów i powiązane opisy. 

## <a name="error-codes"></a>Kody błędów

|Błąd|Opis|
|---|---|
|50001|Nie można odnaleźć jednostki usługi o nazwie X w dzierżawie o nazwie Y. Taka sytuacja może wystąpić, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy. Lub w katalogu nie znaleziono jednostki Zasób albo jest ona nieprawidłowa.|
|50008|Brak potwierdzeń języka SAML lub zostały nieprawidłowo skonfigurowane w tokenie.|
|50011|Brak adresu zwrotnego, został nieprawidłowo skonfigurowany lub jest inny niż adresy zwrotne skonfigurowane dla aplikacji.|
|50012|Użytkownik zgłosił oszustwo podczas uwierzytelniania wieloskładnikowego.|
|50053|Konto zostało zablokowane, ponieważ użytkownik podjął zbyt wiele prób zalogowania przy użyciu niepoprawnego identyfikatora użytkownika lub hasła.|
|50054|Podczas uwierzytelniania użyto starego hasła.|
|50055|Nieprawidłowe hasło — wprowadzono nieważne hasło.|
|50057|Konto użytkownika zostało wyłączone.|
|50058|W podanych poświadczeniach nie znaleziono informacji o tożsamości użytkownika, w dzierżawie nie znaleziono użytkownika albo wysłano żądanie logowania dyskretnego, ale żaden użytkownik nie jest zalogowany lub usługa nie mogła uwierzytelnić użytkownika.|
|50072|Użytkownik musi zarejestrować się na potrzeby uwierzytelniania dwuskładnikowego (interaktywnie)|
|50074|Użytkownik nie przeszedł żądania uwierzytelniania MFA.|
|50079|Użytkownik musi zarejestrować się na potrzeby uwierzytelniania dwuskładnikowego.|
|50089|Weryfikacja tokenu przepływu nie powiodła się z powodu jego wygaśnięcia.|
|50097|Urządzenie nie jest uwierzytelnione.|
|50105|Zalogowany użytkownik nie jest przypisany do roli dla tej aplikacji.|
|50126|Nieprawidłowa nazwa lub hasło użytkownika albo nieprawidłowa nazwa lub hasło użytkownika lokalnego.|
|50131|Kod używany w przypadku różnych błędów dostępu warunkowego, takich jak zły stan urządzenia z systemem Windows albo żądanie zablokowane z powodu podejrzanych decyzji związanych z działaniami, zasadami dostępu i zasadami zabezpieczeń.|
|50133|Sesja jest nieprawidłowa z powodu wygaśnięcia lub niedawnej zmiany hasła.|
|50144|Ważność hasła użytkownika usługi Active Directory wygasła.|
|65001|Aplikacja X nie ma uprawnienia dostępu do aplikacji Y lub uprawnienie zostało odwołany. Lub użytkownik albo administrator nie wyraził zgody na używanie aplikacji z identyfikatorem X. Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu. Lub użytkownik albo administrator nie wyraził zgody na używanie aplikacji z identyfikatorem X. Wyślij do administratora dzierżawy żądanie autoryzacji dotyczące działania w imieniu aplikacji Y dla zasobu Z.|
|65005|Lista dostępu do wymaganych zasobów aplikacji nie zawiera aplikacji możliwych do wykrycia przez zasób lub aplikacja kliencka zażądała dostępu do zasobu, który nie został określony na liście dostępu do wymaganych zasobów lub usługa programu Graph zwróciła nieprawidłowe żądanie lub nie można odnaleźć zasobu.|
|70001|Nie można odnaleźć aplikacji o nazwie X w dzierżawie o nazwie Y. Taka sytuacja może wystąpić, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub żaden użytkownik w dzierżawie nie wyraził odpowiedniej zgody. Żądanie uwierzytelniania mogło zostać wysłane do nieprawidłowej dzierżawy.|
|80001|Agent uwierzytelniania nie może nawiązać połączenia z usługą Active Directory.|
|80002|Upłynął limit czasu żądania weryfikacji hasła agenta uwierzytelniania.|
|80003|Agent uwierzytelniania odebrał nieprawidłową odpowiedź.|
|80004|W żądaniu logowania użyto nieprawidłowej głównej nazwy użytkownika (UPN).|
|80005|Agent uwierzytelniania: wystąpił błąd.|
|80007|Agent uwierzytelniania nie może zweryfikować hasła użytkownika.|
|80010|Agent uwierzytelniania nie może odszyfrować hasła.|
|80011|Agent uwierzytelniania nie może pobrać klucza odszyfrowującego.|
|81001|Bilet Kerberos użytkownika jest zbyt duży.|
|81002|Nie można zweryfikować biletu Kerberos użytkownika.|
|81003|Nie można zweryfikować biletu Kerberos użytkownika.|
|81004|Próba uwierzytelniania Kerberos nie powiodła się.|
|81008|Nie można zweryfikować biletu Kerberos użytkownika.|
|81009|Nie można zweryfikować biletu Kerberos użytkownika.|
|81010|Bezproblemowe logowanie jednokrotne nie powiodło się, ponieważ ważność biletu Kerberos użytkownika wygasła lub bilet jest nieprawidłowy.|
|81011|Nie można znaleźć obiektu użytkownika w oparciu o informacje z biletu Kerberos użytkownika.|
|81012|Użytkownik próbujący zalogować się do usługi Azure AD jest inny niż użytkownik zalogowany do urządzenia.|
|81013|Nie można znaleźć obiektu użytkownika w oparciu o informacje z biletu Kerberos użytkownika.|
|90014|Kod używany w różnych przypadkach, jeśli poświadczenia nie zawierają oczekiwanego pola.|
|90093|Program Graph zwrócił niedozwolony kod błędu dla żądania.|


## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w temacie [Wprowadzenie do raportów dotyczących logowań w portalu Azure Active Directory](active-directory-reporting-activity-sign-ins.md).
