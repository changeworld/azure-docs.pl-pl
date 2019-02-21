---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/19/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 619dd7f3b01e2e7ce71e945fce77aa73cb87f264
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443323"
---
Oto ograniczenia używania i inne limity usługi dotyczące usługi Azure Active Directory (Azure AD).

| Kategoria | Limity |
| --- | --- |
| Katalogi | Jeden użytkownik może należeć do maksymalnie 500 katalogów usługi Azure AD jako element członkowski lub gościa.<br/>Jeden użytkownik może utworzyć maksymalnie 20 katalogów. |
| Domeny | Możesz dodać maksymalnie 900 nazw domen zarządzanych. Jeśli konfigurujesz wszystkie domeny dla federacji z usługą Active Directory w środowisku lokalnym, możesz dodać nie więcej niż 450 nazwy domeny w każdym katalogu. |
| Obiekty |<ul><li>Użytkownicy wersji bezpłatnej usługi Azure Active Directory w jednym katalogu można utworzyć maksymalnie 500 000 obiektów.</li><li>Użytkownik niebędący administratorem może utworzyć maksymalnie 250 obiektów.</li></ul> |
| Rozszerzenia schematu |<ul><li>Rozszerzenia typu string mogą mieć maksymalnie 256 znaków. </li><li>Rozmiar rozszerzeń typu binary jest ograniczony do 256 bajtów.</li><li>Do każdego pojedynczego obiektu można zapisać 100 wartości rozszerzeń (we WSZYSTKICH typach i WSZYSTKICH aplikacjach).</li><li>Jednostki „User”, „Group”, „TenantDetail”, „Device”, „Application” i „ServcicePrincipal” można rozszerzyć za pomocą jednowartościowych atrybutów typu „String” lub „Binary”.</li><li>Rozszerzenia schematu są dostępne tylko w interfejsie API programu Graph w wersji 1.21 — wersja zapoznawcza. Aby zarejestrować rozszerzenie, należy przyznać aplikacji prawa dostępu do zapisu.</li></ul> |
| Aplikacje |Maksymalnie 100 użytkowników może być właścicielami jednej aplikacji. |
| Grupy |<ul><li>Maksymalnie 100 użytkowników może być właścicielami jednej grupy.</li><li>Dowolna liczba obiektów mogą być członkami jednej grupy.</li><li>Użytkownik może należeć do dowolnej liczby grup.</li><li>Liczba członków w grupie, których można zsynchronizować z lokalnej usługi Active Directory do usługi Azure Active Directory za pomocą usługi Azure AD Connect jest ograniczony do członków 50 tys.</li></ul> |
| Panel dostępu |<ul><li>Nie ma żadnego limitu liczby aplikacji wyświetlanych w panelu dostępu na użytkownika końcowego dla przypisanych użytkownikom licencji na usługę Azure AD Premium lub pakiet Enterprise Mobility Suite.</li><li>Maksymalnie 10 kafelków aplikacji (przykłady: Box, Salesforce czy Dropbox) są widoczne w panelu dostępu dla każdego użytkownika końcowego dla przypisanych użytkownikom licencji bezpłatnych lub wersji usługi Azure AD podstawowa usługi Azure Active Directory. Ten limit nie dotyczy kont administratorów.</li></ul> |
| Raporty | W dowolnym raporcie można wyświetlić lub pobrać maksymalnie 1000 wierszy. Dodatkowe dane są obcinane. |
| Jednostki administracyjne | Jeden obiekt może być członkiem maksymalnie 30 jednostek administracyjnych. |
| Role administratora i uprawnienia | <li>Nie można dodać grupy jako właściciela<li>Nie można przypisać grupę do roli<li>Nie można zmienić domyślne uprawnienia użytkowników poza przełączników dzierżawy (ustawień użytkownika w usłudze Azure AD) |
