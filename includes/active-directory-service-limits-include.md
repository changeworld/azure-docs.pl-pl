---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 3b1019d45f4fee60e0e197f283ef38f4f3fca875
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66143065"
---
Oto ograniczenia używania i inne limity usługi dotyczące usługi Azure Active Directory (Azure AD).

| Category | Limity |
| --- | --- |
| Katalogi | Jeden użytkownik może być członkiem lub gościem maksymalnie 500 katalogów usługi Azure AD.<br/>Jeden użytkownik może utworzyć maksymalnie 20 katalogów. |
| Domeny | Możesz dodać maksymalnie 900 nazw domen zarządzanych. Jeśli konfigurujesz wszystkie domeny federacyjne w celu federacji lokalnej z usługą Active Directory, możesz dodać maksymalnie 450 nazw domeny w każdym katalogu. |
| Obiekty |<ul><li>Użytkownicy usługi Azure Active Directory w wersji Bezpłatna domyślnie mogą utworzyć maksymalnie 50 000 obiektów w jednym katalogu. Jeśli masz co najmniej jedną zweryfikowaną domenę, domyślny limit przydziału usługi katalogowej w usłudze Azure AD jest rozszerzony do 300 000 obiektów. </li><li>Użytkownik niebędący administratorem może utworzyć maksymalnie 250 obiektów. Do tego limitu przydziału są wliczane aktywne i usunięte obiekty dostępne do przywrócenia. Do przywrócenia są dostępne tylko obiekty, które zostały usunięte mniej niż 30 dni temu. Usunięte obiekty, które nie są już dostępne do przywrócenia, są wliczane do tego limitu przydziału w wartości jednej czwartej przez 30 dni. Spróbuj [przypisać rolę administratora](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) do użytkowników bez uprawnień administratora, którzy mogą wielokrotnie przekraczać ten limit przydziału w ramach codziennych zadań.</li></ul> |
| Rozszerzenia schematu |<ul><li>Rozszerzenia typu string mogą mieć maksymalnie 256 znaków. </li><li>Rozmiar rozszerzeń typu binary jest ograniczony do 256 bajtów.</li><li>Do każdego pojedynczego obiektu można zapisać tylko 100 wartości rozszerzeń (we *wszystkich* typach i *wszystkich* aplikacjach).</li><li>Jednostki User, Group, TenantDetail, Device, Application i ServcicePrincipal można rozszerzyć za pomocą jednowartościowych atrybutów typu String lub Binary.</li><li>Rozszerzenia schematu są dostępne tylko w interfejsie API programu Graph w wersji 1.21 — wersja zapoznawcza. Aby zarejestrować rozszerzenie, należy przyznać aplikacji prawa dostępu do zapisu.</li></ul> |
| Aplikacje |Właścicielami jednej aplikacji może być maksymalnie 100 użytkowników. |
| Grupy |<ul><li>Właścicielami jednej grupy może być maksymalnie 100 użytkowników.</li><li>Członkami jednej grupy może być dowolna liczba obiektów.</li><li>Użytkownik może być członkiem dowolnej liczby grup.</li><li>Liczba członków w grupie, których można zsynchronizować z lokalnej usługi Active Directory do usługi Azure Active Directory przy użyciu programu Azure AD Connect, jest ograniczona do 50 000 członków.</li></ul> |
| Serwer proxy aplikacji | <ul><li>Maksymalnie 500 transakcji na sekundę na aplikację serwera Proxy aplikacji</li><li>Maksymalnie 750 transakcji na sekundę dla dzierżawy</li></ul><br/>Transakcja jest definiowana jako żądanie http jednym i odpowiadać na unikatowy zasób. Gdy dławiona, klienci będą otrzymywać odpowiedzi 429 (zbyt wiele żądań). |
| Panel dostępu |<ul><li>Nie ma limitu liczby aplikacji na użytkownika, które są widoczne w panelu dostępu. Dotyczy to przypisanych do użytkowników licencji usługi Azure AD Premium lub pakietu Enterprise Mobility Suite.</li><li>Panel dostępu zawiera maksymalnie 10 kafelków aplikacji dla każdego użytkownika. Ten limit dotyczy użytkowników z przypisanymi licencjami usługi Azure AD — wersja Podstawowa w usłudze Azure Active Directory. Przykładowe kafelki aplikacji Box, Salesforce i Dropbox. Ten limit nie dotyczy kont administratorów.</li></ul> |
| Raporty | W dowolnym raporcie można wyświetlić lub pobrać maksymalnie 1000 wierszy. Dodatkowe dane są obcinane. |
| Jednostki administracyjne | Jeden obiekt może być członkiem maksymalnie 30 jednostek administracyjnych. |
| Role i uprawnienia administratora | <li>Nie można dodać grupy jako właściciela.<li>Nie można przypisać grupy do roli.<li>Domyślnie nie można zmienić uprawnień użytkownika z wyjątkiem przełączników dzierżawy, czyli ustawień użytkownika w usłudze Azure AD. |
