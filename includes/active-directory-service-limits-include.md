---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554692"
---
Oto ograniczenia używania i inne limity usługi dotyczące usługi Azure Active Directory (Azure AD).

| Kategoria | Limity |
| --- | --- |
| Katalogi | Jeden użytkownik może należeć do maksymalnie 500 katalogów usługi Azure AD jako element członkowski lub gościa.<br/>Jeden użytkownik może utworzyć maksymalnie 20 katalogów. |
| Domeny | Możesz dodać maksymalnie 900 nazw domen zarządzanych. Po skonfigurowaniu wszystkich domen dla federacji z usługą Active Directory w środowisku lokalnym, możesz dodać nie więcej niż 450 nazwy domeny w każdym katalogu. |
| Obiekty |<ul><li>Użytkownicy wersji bezpłatnej usługi Azure Active Directory w jednym katalogu można utworzyć maksymalnie 500 000 obiektów.</li><li>Użytkownik niebędący administratorem może utworzyć maksymalnie 250 obiektów. Aktywne obiekty i usuniętych obiektów, które są dostępne do przywrócenia wliczane tego przydziału. Tylko usunięte obiekty, które zostały usunięte mniej niż 30 dni temu są dostępne do przywrócenia. Usunięte obiekty, które nie są już dostępne do przywrócenia wliczane ten limit przydziału wartość jednej czwartej przez 30 dni. Być może [przypisać rolę administratora](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) do użytkowników bez uprawnień administratora, które mogą być wielokrotnie przekroczenia tego przydziału obowiązków zawodowych regularne.</li></ul> |
| Rozszerzenia schematu |<ul><li>Rozszerzenia typu String mogą mieć maksymalnie 256 znaków. </li><li>Rozszerzenia typu binary jest ograniczony do 256 bajtów.</li><li>Tylko 100 wartości rozszerzenia, na *wszystkich* typów i *wszystkich* aplikacji, mogą być zapisywane do każdego pojedynczego obiektu.</li><li>Tylko użytkownik, grupa, TenantDetail, urządzenia, aplikacji i ServicePrincipal można rozszerzyć za pomocą typu string lub jednowartościowych atrybutów typu binary.</li><li>Rozszerzenia schematu są dostępne tylko w wersji 1.21 interfejsu API programu Graph w wersji zapoznawczej. Aby zarejestrować rozszerzenie, należy przyznać aplikacji prawa dostępu do zapisu.</li></ul> |
| Aplikacje |Maksymalnie 100 użytkowników może być właścicielami jednej aplikacji. |
| Grupy |<ul><li>Maksymalnie 100 użytkowników może być właścicielami jednej grupy.</li><li>Dowolna liczba obiektów mogą być członkami jednej grupy.</li><li>Użytkownik może należeć do dowolnej liczby grup.</li><li>Liczba członków w grupie, które można zsynchronizować z usługą Active Directory lokalnych z usługą Azure Active Directory przy użyciu usługi Azure AD Connect jest ograniczona do 50 000 członków.</li></ul> |
| Panel dostępu |<ul><li>Nie ma żadnego limitu liczby aplikacji, które są widoczne w panelu dostępu dla poszczególnych użytkowników. Dotyczy to przypisanych użytkownikom licencji usługi Azure AD Premium lub pakietu Enterprise Mobility Suite.</li><li>Maksymalnie 10 kafelków aplikacji są widoczne w panelu dostępu dla każdego użytkownika. Ten limit dotyczy osób, które są przypisane bezpłatne licencje lub usługi Azure AD podstawowa wersje usługi Azure Active Directory. Przykładami kafelków aplikacji Box, Salesforce czy Dropbox. To ograniczenie nie dotyczy kont administratorów.</li></ul> |
| Raporty | W dowolnym raporcie można wyświetlić lub pobrać maksymalnie 1000 wierszy. Dodatkowe dane są obcinane. |
| Jednostki administracyjne | Jeden obiekt może być członkiem maksymalnie 30 jednostek administracyjnych. |
| Role administratora i uprawnienia | <li>Nie można dodać grupy jako właściciela.<li>Nie można przypisać grupę do roli.<li>Domyślnie użytkownik, którego nie można zmienić uprawnienia, z wyjątkiem przełączników dzierżawy, czyli ustawień użytkownika w usłudze Azure AD. |
