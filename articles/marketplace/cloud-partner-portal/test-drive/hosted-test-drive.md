---
title: Hostowana jazda testowa | Azure Marketplace
description: Jak skonfigurować hostowany dysk testowy w portalu Marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278267"
---
# <a name="hosted-test-drive"></a>Hostowana wersja testowa

Hostowany dysk testowy usuwa złożoność konfiguracji przez hosting firmy Microsoft i utrzymuje usługę, która wykonuje inicjowanie obsługi administracyjnej i usuwanie obsługi administracyjnej użytkowników dysku testowego. Ten artykuł jest przeznaczony dla wydawców, którzy mają swoją ofertę w udziale w udziale w udziale w programie AppSource lub budują nową i chcą oferować hostowany dysk testowy, który łączy się z dynamics 365 for Customer Engagement, Dynamics 365 for Finance and Operations lub Dynamics 365 Business Central Wystąpienie.

## <a name="how-to-publish-a-test-drive"></a>Jak opublikować jazdę próbną

Przejdź do istniejącej oferty lub utwórz nową ofertę.

Wybierz opcję Jazdę próbna z menu bocznego.

Wybierz \'\' opcję \'Tak,\' aby włączyć jazdę próbną.

Podaj następujące \'pola\' w sekcji Szczegóły.

- **Opis**: Opis dysku testowego. Ten tekst będzie wyświetlany użytkownikowi podczas obsługi administracyjnej dysku testowego. To pole obsługuje kod HTML, jeśli chcesz podać sformatowaną zawartość.
- **Instrukcja obsługi**: Prześlij szczegółową instrukcję obsługi (plik typu .pdf), która pomaga użytkownikom testowego dysku zrozumieć, jak korzystać z aplikacji.
- **Film demonstracyjny na dysku testowym:** opcjonalnie prześlij film, który prezentuje Twoją aplikację.

Udziel uprawnień AppSource do udostępniania i usuwania obsługi administracyjnej użytkowników dysku testowego w dzierżawie, korzystając z instrukcji znajdujących się [w tym miejscu.](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)

W tym kroku wygenerujesz \'wartości\' identyfikatora aplikacji usługi Azure AD i \'klucza\' aplikacji usługi Azure AD wymienione poniżej.

Podaj następujące \'pola\' w sekcji Konfiguracja techniczna:

- **Typ dysku testowego:** wybierz \'opcję Microsoft Hosted (przykład Dynamics 365 for Customer Engagement)". Oznacza to, że firma Microsoft będzie hostować i obsługiwać usługę, która wykonuje inicjowanie obsługi administracyjnej i anulowanie obsługi administracyjnej użytkowników dysku testowego.
- **Maksymalna liczba równoczesnych dysków testowych:** ustaw to pole na liczbę równoczesnych użytkowników, którzy mogą mieć aktywną jazdę testową w danym momencie. Każdy użytkownik będzie korzystać z licencji Dynamics, gdy jego dysk testowy jest aktywny, więc trzeba będzie upewnić się, że masz co najmniej tyle licencji Dynamics dostępnych dla użytkowników dysku testowego. Zalecana wartość 3-5.
- **Czas trwania dysku testowego (godziny):** Ustaw to pole na liczbę godzin, dla przez które będą aktywni użytkownicy Test Drive. Po tym wielu godzinach użytkownik zostanie wyrejestrowany z dzierżawy. Zalecana wartość 2-24 godzin w zależności od złożoności aplikacji. Użytkownik zawsze może zażądać innego dysku testowego, jeśli zabraknie im czasu i chcesz ponownie uzyskać dostęp do dysku testowego.
- **Adres URL wystąpienia:** podaj adres URL, do który użytkownik dysku testowego będzie początkowo nawigowany podczas uruchamiania dysku testowego. Zazwyczaj jest to adres URL wystąpienia Dynamics 365, na który znajduje się aplikacja i przykładowe dane. Przykładowa wartość:\/https: /testdrive.crm.dynamics.com
- **Identyfikator dzierżawy usługi Azure AD:** Podaj identyfikator dzierżawy platformy Azure dla wystąpienia dynamics 365. Aby pobrać tę wartość, zaloguj się \'do witryny\'  - \> Azure portal i\> przejdź do usługi Azure Active Directory Select Properties z bloku menu — skopiuj identyfikator katalogu. Przykładowa wartość: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Identyfikator aplikacji usługi Azure AD:** identyfikator aplikacji usługi Azure AD utworzonej w kroku 7.\ Przykładowa wartość: 53852862-a2ae-4e43-9461-faa49650a096
- **Klucz aplikacji usługi Azure AD:** klucz tajny dla aplikacji usługi Azure AD utworzonej w kroku 7.\ Przykładowa wartość: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Nazwa dzierżawy usługi Azure AD:** Podaj nazwę dzierżawy platformy Azure dla wystąpienia Dynamics 365. Użyj formatu \<tenantname. \>onmicrosoft.com. Przykładowa wartość: testdrive.onmicrosoft.com
- **Adres URL interfejsu API sieci Web wystąpienia:** Podaj adres URL interfejsu API sieci Web dla wystąpienia dynamics 365. Tę wartość można pobrać, logując się do wystąpienia systemu Microsoft Dynamics\> 365 i przechodząc do interfejsu Ustawienia — Dostosowywanie —\> Zasoby deweloperskie —\> Interfejs API web instancji (Skopiuj ten adres URL). Przykładowa wartość:\/https: /testdrive.crm.dynamics.com/api/data/v9.0
- **Nazwa roli:** Podaj nazwę niestandardowej roli zabezpieczeń Dynamics 365 utworzonej dla dysku testowego. Jest to rola, która zostanie przypisana do użytkowników podczas ich jazdy testowej. Przykładowa wartość: testdriverole

## <a name="next-steps"></a>Następne kroki

Gdy **aplikacja** zostanie gotowa opublikować ofertę, po przejściu certyfikacji aplikacji pojawi się **podgląd** oferty. Uruchom jazdę testową w interfejsie użytkownika i sprawdź, czy dyski testowe działają poprawnie. Gdy poczujesz się komfortowo z ofertą podglądu, teraz nadszedł czas, aby **przejść na żywo!**
