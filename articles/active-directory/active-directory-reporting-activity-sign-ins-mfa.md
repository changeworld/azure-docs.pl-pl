---
title: Informacje dotyczące raportowania uwierzytelniania wieloskładnikowego w witrynie Azure Portal | Microsoft Docs
description: Informacje dotyczące raportowania uwierzytelniania wieloskładnikowego w witrynie Azure Portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 03/16/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e2d48eeaa98fd60841e2f25c09eb46bb848f78df
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221900"
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Informacje dotyczące raportowania uwierzytelniania wieloskładnikowego w witrynie Azure Portal

Dzięki [raportom usługi Azure Active Directory (Azure AD)](active-directory-reporting-azure-portal.md) w witrynie [Azure Portal](https://portal.azure.com) możesz uzyskać wszystkie informacje, które pomogą ustalić, jak działa Twoje środowisko.

[Raporty aktywności logowania](active-directory-reporting-activity-sign-ins.md) dostarczają informacji na temat użycia zarządzanych aplikacji i działań logowania użytkownika, które obejmują informacje na temat użycia usługi Multi-Factor Authentication (MFA). 

Dane usługi MFA dają wgląd w sposób działania usługi MFA w Twojej organizacji. Umożliwiają one udzielenie odpowiedzi na takie pytania, jak: 

- Czy logowanie zostało zakwestionowane przez usługę MFA? 

- Jak użytkownik ukończył uwierzytelnianie MFA? 

- Dlaczego użytkownik nie mógł ukończyć uwierzytelniania MFA?  

Dzięki agregacji wszystkich danych logowania możesz lepiej zrozumieć usługę MFA w Twojej organizacji. Te dane umożliwiają udzielenie odpowiedni na takie pytania, jak: 

- Ilu użytkowników zostało zakwestionowanych przez usługę MFA?  

- Ilu użytkowników nie mogło odpowiedzieć na wezwania usługi MFA? 

- Jakie są typowe problemy z usługą MFA, na które natykają się użytkownicy końcowi? 


Dane te są dostępne za pośrednictwem witryny Azure Portal i [interfejsu API raportowania](active-directory-reporting-api-getting-started-azure-portal.md). 


## <a name="data-structure"></a>Struktura danych


Raporty działania logowania dla usługi MFA umożliwiają dostęp do następujących informacji:

**Wymagana usługa MFA:** czy usługa MFA jest wymagana do logowania, czy też nie. Usługa MFA może być wymagana ze względu na usługę MFA na użytkownika, dostęp warunkowy lub z innych przyczyn. Możliwe wartości to `Yes` lub `No`.

**Metoda uwierzytelniania usługi MFA:** metoda uwierzytelniania stosowana przez użytkownika w celu zakończenia uwierzytelniania MFA. Możliwe wartości: 

- Wiadomość SMS 

- Powiadomienie aplikacji mobilnej 

- Połączenie telefoniczne (numer telefonu uwierzytelniania) 

- Kod weryfikacyjny aplikacji mobilnej 

- Połączenie telefoniczne (numer telefonu służbowego) 

- Połączenie telefoniczne (alternatywny numer telefonu uwierzytelniania) 

**Szczegóły uwierzytelniania usługi MFA:** wyczyszczona wersja numeru telefonu, na przykład +X XXXXXXXX64. 

**Wynik usługi MFA:** więcej informacji na temat tego, czy uwierzytelnianie MFA zostało przeprowadzone pomyślnie, czy też nie:

- Jeśli uwierzytelnianie MFA powiodło się, ta kolumna zawiera więcej informacji na temat sposobu przeprowadzenia uwierzytelnienia MFA. 

- Jeśli uwierzytelnianie MFA nie powiodło się, ta kolumna zawiera przyczynę niepowodzenia. Możliwe wartości to `Satisfied` lub `Denied`. 

W poniższej sekcji przedstawiono możliwe wartości ciągów dla pola wyniku uwierzytelniania MFA.

## <a name="status-strings"></a>Ciągi stanu

Ta sekcja zawiera listę możliwych wartości ciągu stanu wyniku uwierzytelniania MFA.

### <a name="satisfied-status-strings"></a>Ciągi stanu dla powodzenia


- Azure Multi-Factor Authentication

    - ukończone w chmurze 

    - wygasłe z powodu zasad skonfigurowanych w dzierżawie 

    - wyświetlono monit o rejestrację 

    - zrealizowane przez oświadczenie w tokenie 

    - zrealizowane przez oświadczenie dostarczone przez dostawcę zewnętrznego 

    - zrealizowane przez silne uwierzytelnianie 

    - pominięte, ponieważ zrealizowany przepływ był przepływem logowania brokera systemu Windows 

    - pominięte z powodu hasła aplikacji 

    - pominięte z powodu lokalizacji 

    - pominięte z powodu zarejestrowanego urządzenia 
    
    - pominięte z powodu zapamiętanego urządzenia 

    - pomyślnie ukończono 

- Przekierowane do zewnętrznego dostawcy w celu uwierzytelnienia wieloskładnikowego 

 
### <a name="denied-status-strings"></a>Ciągi stanu niepowodzenia

Niepowodzenie uwierzytelniania Azure Multi-Factor Authentication; 

- uwierzytelnianie w toku 

- próba zduplikowanego uwierzytelnienia 

- zbyt wiele razy wprowadzono niepoprawny kod 

- nieprawidłowe uwierzytelnienie 

- nieprawidłowy kod weryfikacyjny aplikacji mobilnej 

- błąd konfiguracji 

- połączenie telefoniczne przekierowane do poczty głosowej 

- numer telefonu ma nieprawidłowy format 

- błąd usługi 

- nie można skontaktować się z numerem telefonu użytkownika 

- nie można wysłać powiadomienia aplikacji mobilnej do urządzenia 

- nie można wysłać powiadomienia aplikacji mobilnej 

- uwierzytelnienie odrzucone przez użytkownika 

- użytkownik nie odpowiedział na powiadomienie aplikacji mobilnej 

- użytkownik nie ma żadnych zarejestrowanych metod weryfikacji 

- użytkownik wprowadził nieprawidłowy kod 

- użytkownik wprowadził nieprawidłowy numer PIN 

- użytkownik rozłączył połączenie telefoniczne bez pomyślnego uwierzytelnienia 

- użytkownik jest zablokowany 

- użytkownik nigdy nie wprowadził kodu weryfikacyjnego 

- nie znaleziono użytkownika 
 
- kod weryfikacyjny został już raz użyty 



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [Raporty w usłudze Azure Active Directory](active-directory-reporting-azure-portal.md).




























