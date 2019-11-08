---
title: Hostowany dysk testowy | Portal Azure Marketplace
description: Jak skonfigurować dysk testowy hostowany w portalu Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 2ce43402fb283cce09687bc7226770731f1bae0d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827514"
---
# <a name="hosted-test-drive"></a>Hostowana wersja testowa

Hostowany dysk testowy eliminuje złożoność instalacji przez hosting firmy Microsoft i utrzymuje usługę, która przeprowadza testowanie i cofa obsługę administracyjną użytkowników. Ten artykuł jest przeznaczony dla wydawców, którzy mają swoją ofertę na AppSource lub kompilują nowe i chcą oferować hostowaną stację testową, która łączy się z usługą Dynamics 365 dla zaangażowania klienta, Dynamics 365 dla finansów i operacji, lub Dynamics 365 Business Central np.

## <a name="how-to-publish-a-test-drive"></a>Jak opublikować dysk testowy

Przejdź do istniejącej oferty lub Utwórz nową ofertę.

Wybierz opcję dysk testowy z menu po stronie.

Wybierz pozycję \'tak\', aby \'włączyć opcję\' dysku testowego.

Podaj następujące pola w sekcji \'szczegóły\'.

- **Opis**: Podaj przegląd dysku testowego. Ten tekst będzie widoczny dla użytkownika podczas aprowizacji dysku testowego. To pole obsługuje kod HTML, jeśli chcesz wprowadzić sformatowaną zawartość.
- **Podręcznik użytkownika**: Przekaż szczegółowy podręcznik użytkownika (plik typu. PDF), który pomaga testować, jak użytkownicy mogą dowiedzieć się, jak korzystać z aplikacji.
- **Film wideo z pokazem dla wersji testowej**: Opcjonalnie Przekaż wideo, które prezentują Twoją aplikację.

Udziel uprawnienia AppSource do aprowizacji i anulowania aprowizacji użytkowników w dzierżawie przy użyciu instrukcji znajdujących się w [tym miejscu](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

W tym kroku wygenerujesz \'identyfikator aplikacja usługi Azure AD\' i \'aplikacja usługi Azure AD klucza\' wartości wymienionych poniżej.

Podaj następujące pola w \'\' konfiguracji technicznej:

- **Typ dysku testowego**: wybierz opcję \'obsługiwane przez firmę Microsoft (przykład Dynamics 365 dla zaangażowania klienta). Oznacza to, że firma Microsoft będzie hostować i obsługiwać usługę, która przeprowadza testowanie i cofa obsługę administracyjną użytkowników.
- **Maksymalna liczba współbieżnych dysków testowych**: Ustaw to pole na liczbę równoczesnych użytkowników, którzy mogą mieć aktywny dysk testowy w danym momencie. Każdy użytkownik będzie korzystał z licencji usługi Dynamics, gdy ich dysk testowy jest aktywny, więc należy upewnić się, że masz co najmniej następującą liczbę licencji usługi Dynamics dostępnych dla użytkowników na dysku testowym. Zalecana wartość 3-5.
- **Czas trwania dysku testowego (w godzinach)** : Ustaw dla tego pola liczbę godzin, przez które użytkownicy będą aktywni. Po upływie tylu godzin użytkownik zostanie wydzierżawiony z dzierżawy. Zalecana wartość 2-24 godzin w zależności od złożoności aplikacji. Użytkownik może zawsze zażądać innego dysku testowego, jeśli są one nieaktualne i chcą ponownie uzyskać dostęp do dysku testowego.
- **Adres URL wystąpienia**: Podaj adres URL, do którego użytkownik na dysku testowym będzie początkowo przechodził po uruchomieniu testu. Jest to zazwyczaj adres URL wystąpienia programu Dynamics 365, na którym zainstalowano aplikację i przykładowe dane. Przykładowa wartość: https:\//testdrive.crm.dynamics.com
- **Identyfikator dzierżawy usługi Azure AD**: podaj identyfikator dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Aby pobrać tę wartość, zaloguj się do Azure Portal i przejdź do \'Azure Active Directory\' -\> wybierz właściwości z bloku menu-\> Skopiuj identyfikator katalogu. Przykładowa wartość: 72f988bf-86f1-41AF-91ab-2d7cd0111234
- **Identyfikator aplikacja usługi Azure AD**: Identyfikator aplikacja usługi Azure AD utworzonego w kroku 7. \ przykładowa wartość: 53852862-a2ae-4e43-9461-faa49650a096
- **Klucz aplikacja usługi Azure AD**: wpis tajny dla aplikacja usługi Azure AD utworzonego w kroku 7. \ przykładowa wartość: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk =
- **Nazwa dzierżawy usługi Azure AD**: Podaj nazwę dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Użyj formatu \<dzierżawca.\>onmicrosoft.com. Przykładowa wartość: testdrive.onmicrosoft.com
- **Adres URL internetowego interfejsu API wystąpienia**: Podaj adres URL internetowego interfejsu API dla wystąpienia usługi Dynamics 365. Możesz pobrać tę wartość, logując się do wystąpienia systemu Microsoft Dynamics 365 i przechodząc do ustawienia-\> dostosowania —\> zasoby deweloperów-\> wystąpienie internetowego interfejsu API (Skopiuj ten adres URL). Przykładowa wartość: https:\//testdrive.crm.dynamics.com/api/data/v9.0 
- **Nazwa roli**: Podaj nazwę niestandardowej roli zabezpieczeń Dynamics 365 utworzonej dla dysku testowego. Jest to rola, która zostanie przypisana do użytkowników na ich dysku testowym. Przykładowa wartość: testdriverole

## <a name="next-steps"></a>Następne kroki

Gdy aplikacja jest gotowa do **opublikowania** oferty, po przekazaniu certyfikatu przez aplikację uzyskasz **Podgląd** oferty. Uruchom test w interfejsie użytkownika i sprawdź, czy dyski testowe działają prawidłowo. Gdy będziesz mieć doświadczenie z ofertą wersji zapoznawczej, teraz możesz **przejść na żywo** .
