---
title: Obsługiwana wersja testowa | Dokumentacja firmy Microsoft
description: Jak skonfigurować Zachowaj wersję testową hostowane witryny Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 1ab9fcd50ad7081f8047d62e545287fa75db75e4
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811190"
---
# <a name="hosted-test-drive"></a>Hostowanej wersji testowej

Testowej hostowanych eliminuje złożoność konfiguracji w ramach hostingu firmy Microsoft i obsługa usługi przeprowadzające użytkownika wersji testowej aprowizacji i cofania aprowizacji. Ten artykuł jest przeznaczony dla wydawców z opisem oferty w usłudze AppSource lub tworzysz nową i chcesz zaoferować wersję testową hostowanej, która łączy się z usługą Dynamics 365 for Customer Engagement, Dynamics 365 for Finance oraz Operations lub Dynamics 365 Business Central wystąpienie.

## <a name="how-to-publish-a-test-drive"></a>Jak opublikować wersję testową

Przejdź do istniejącej oferty, lub Utwórz nową ofertę.

Z menu po stronie, wybierz opcję wersji testowej.

Wybierz \'tak\' dla \'włączyć wersję testową\' opcji.

Podaj następujące pola \'szczegóły\' sekcji.

- **Opis**: Przegląd wersji testowej. Ten tekst jest wyświetlany użytkownikowi podczas wersji testowej jest aprowizowana. To pole obsługuje HTML, jeśli chcesz zapewnić sformatowaną zawartość.
- **Podręcznik użytkownika**: przekazywanie Podręcznik użytkownika szczegółowe (plik PDF typu), która umożliwia użytkownikom wersji testowej zrozumieć sposób korzystania z aplikacji.
- **Test Drive pokaz wideo**: Opcjonalnie Przekaż wideo, w których aplikacja.

AppSource Udziel uprawnień do aprowizacja i anulowanie aprowizacji użytkowników wersji testowej w Twojej dzierżawie, korzystając z instrukcji znajdujących się [tutaj](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

W tym kroku spowoduje wygenerowanie \'identyfikator aplikacji usługi Azure AD\' i \'klucz aplikacji usługi Azure AD\' wartości wymienione poniżej.

Podaj następujące pola \'konfiguracji technicznej\' sekcji:

- **Typ wersji testowej**: Wybierz \'Hosted firmy Microsoft (np. Dynamics 365 for Customer Engagement) "opcji. Oznacza to, że firma Microsoft hosta i obsługa usługi przeprowadzające użytkownika wersji testowej aprowizacji i cofania aprowizacji.
- **Maksymalna liczba współbieżnych testu dyski**: ustawienie to pole liczby równoczesnych użytkowników, które mogą mieć aktywne wersji testowej w dowolnym czasie. Każdy użytkownik zużyje licencją Dynamics podczas ich wersji testowej jest aktywny, więc należy upewnić się, że masz co najmniej następującą liczbę licencji Dynamics dostępne dla użytkowników wersji testowej. Zalecanej wartości wynoszącej 3 – 5.
- **Dysk czas trwania testu (godziny)**: Ustaw to pole do liczby godzin użytkowników wersji testowej zostanie uaktywniona dla. Po tym wiele godzin użytkownik będzie można anulować aprowizację z dzierżawy. Zalecana wartość 2 – 24 godzin w zależności od złożoności aplikacji. Użytkownik może zażądać zawsze innej wersji testowej, gdyby zabrakło czasu i chcesz ponownie uzyskać dostęp w wersji testowej.
- **Adres URL wystąpienia**: Podaj adres URL, który użytkownika wersji testowej początkowo nastąpi przejście po uruchomieniu wersji testowej. Zazwyczaj jest to adres URL wystąpienia usługi Dynamics 365, która aplikacja ma i przykładowe dane są zainstalowane na urządzeniu. Przykładowa wartość:https://testdrive.crm.dynamics.com
- **Identyfikator dzierżawy usługi Azure AD**: Podaj identyfikator dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Do pobierania tej wartości, zaloguj się do witryny Azure portal i przejdź do \'usługi Azure Active Directory\'  - \> wybierz właściwości z menu bloku -\> skopiuj nazwę katalogu. Przykładowa wartość: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Identyfikator aplikacji w usłudze Azure AD**: identyfikator aplikacji usługi Azure AD został utworzony w kroku 7. \ Przykładowa wartość: 53852862-a2ae-4e43-9461-faa49650a096
- **Klucz aplikacji w usłudze Azure AD**: klucz tajny dla aplikacji usługi Azure AD utworzonej w kroku 7. \ Przykładowa wartość: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk =
- **Nazwa dzierżawy usługi Azure AD**: Podaj nazwę dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Użyj formatu \<tenantname.\> onmicrosoft.com. Przykładowa wartość: testdrive.onmicrosoft.com
- **Adres URL interfejsu API sieci Web wystąpienia**: Podaj adres URL interfejsu API sieci Web dla wystąpienia usługi Dynamics 365. Tę wartość można pobrać, logując się do swojego wystąpienia programu Microsoft Dynamics 365 i przechodząc do ustawienia -\> dostosowania —\> zasoby dla deweloperów —\> wystąpienia interfejsu API sieci Web (kopia tego adresu URL). Przykładowa wartość:  https://testdrive.crm.dynamics.com/api/data/v9.0 
- **Nazwa roli**: Podaj nazwę roli zabezpieczeń niestandardowej Dynamics 365 utworzone dla wersji testowej. Jest to rola, która zostanie przypisana do użytkowników podczas ich wersji testowej. Przykładowa wartość: testdriverole

## <a name="next-steps"></a>Kolejne kroki

Gdy będzie gotowe **publikowania** oferty, po aplikacji został przekazany certyfikacji, będziesz mieć **Podgląd** oferty. Uruchom wersję testową w interfejsie użytkownika, a następnie sprawdź, czy wersji testowych działają prawidłowo. Gdy masz doświadczenie z ofertą użytkownika (wersja zapoznawcza), nadszedł czas na **emisji na żywo!**
