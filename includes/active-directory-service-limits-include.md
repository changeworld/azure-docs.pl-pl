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
ms.openlocfilehash: 3f358dedea37eb33c2d2bb26a823d3633560d3a0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850442"
---
Oto ograniczenia używania i inne limity usługi dotyczące usługi Azure Active Directory (Azure AD).

| Kategoria | Limity |
| --- | --- |
| Katalogi | Jeden użytkownik może być członkiem lub gościem maksymalnie 500 katalogów usługi Azure AD.<br/>Jeden użytkownik może utworzyć maksymalnie 20 katalogów. |
| Domeny | Możesz dodać maksymalnie 900 nazw domen zarządzanych. Jeśli konfigurujesz wszystkie domeny federacyjne w celu federacji lokalnej z usługą Active Directory, możesz dodać maksymalnie 450 nazw domeny w każdym katalogu. |
|Zasoby |<ul><li>Maksymalnie 50 000 zasobów usługi Azure AD można utworzyć w jednym katalogu przez użytkowników wersji bezpłatnej Azure Active Directory. Jeśli masz co najmniej jedną zweryfikowaną domenę, domyślny limit przydziału usługi katalogowej w usłudze Azure AD zostanie rozszerzony do zasobów 300 000 usługi Azure AD. </li><li>Użytkownik niebędący administratorem może utworzyć maksymalnie 250 zasobów usługi Azure AD. Aktywne zasoby i usunięte zasoby, które są dostępne do przywrócenia liczby w ramach tego przydziału. Do przywrócenia dostępne są tylko usunięte zasoby usługi Azure AD, które zostały usunięte z mniej niż 30 dni temu. Usunięto zasoby usługi Azure AD, które nie są już dostępne, aby przywrócić liczbę przydziałów w ramach tego przydziału w ciągu 30 dni. Jeśli masz deweloperów, którzy mogą wielokrotnie przekroczyć ten limit przydziału w wyniku zwykłych obowiązków, możesz [utworzyć i przypisać rolę niestandardową](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md) z uprawnieniem do tworzenia nieograniczonej liczby rejestracji aplikacji.</li></ul> |
| Rozszerzenia schematu |<ul><li>Rozszerzenia typu string mogą mieć maksymalnie 256 znaków. </li><li>Rozmiar rozszerzeń typu binary jest ograniczony do 256 bajtów.</li><li>Tylko 100 wartości rozszerzeń, między *wszystkimi* typami i *wszystkimi* aplikacjami, można zapisywać do dowolnego zasobu usługi Azure AD.</li><li>Jednostki User, Group, TenantDetail, Device, Application i ServcicePrincipal można rozszerzyć za pomocą jednowartościowych atrybutów typu String lub Binary.</li><li>Rozszerzenia schematu są dostępne tylko w interfejsie API programu Graph w wersji 1.21 — wersja zapoznawcza. Aby zarejestrować rozszerzenie, należy przyznać aplikacji prawa dostępu do zapisu.</li></ul> |
| Aplikacje |Właścicielami jednej aplikacji może być maksymalnie 100 użytkowników. |
|Manifest aplikacji |W manifeście aplikacji można dodać maksymalnie 1200 wpisów. |
| Grupy |<ul><li>Właścicielami jednej grupy może być maksymalnie 100 użytkowników.</li><li>Dowolna liczba zasobów usługi Azure AD może być członkiem pojedynczej grupy.</li><li>Użytkownik może być członkiem dowolnej liczby grup.</li><li>Liczba członków w grupie, których można zsynchronizować z lokalnej usługi Active Directory do usługi Azure Active Directory przy użyciu programu Azure AD Connect, jest ograniczona do 50 000 członków.</li></ul> |
| Serwer proxy aplikacji | <ul><li>Maksymalnie 500 transakcji na sekundę na aplikację serwera proxy aplikacji</li><li>Maksymalnie 750 transakcji na sekundę dla dzierżawy</li></ul><br/>Transakcja jest definiowana jako pojedyncze żądanie HTTP i odpowiedź na unikatowy zasób. W przypadku ograniczenia przepustowości klienci otrzymają odpowiedź 429 (za dużo żądań). |
| Panel dostępu |<ul><li>Nie ma limitu liczby aplikacji na użytkownika, które są widoczne w panelu dostępu. Dotyczy to przypisanych do użytkowników licencji usługi Azure AD Premium lub pakietu Enterprise Mobility Suite.</li><li>Panel dostępu zawiera maksymalnie 10 kafelków aplikacji dla każdego użytkownika. Ten limit dotyczy użytkowników, którym przypisano licencje dla Azure AD — wersja Bezpłatna planu licencji. Przykładowe kafelki aplikacji Box, Salesforce i Dropbox. Ten limit nie dotyczy kont administratorów.</li></ul> |
| Reports | W dowolnym raporcie można wyświetlić lub pobrać maksymalnie 1000 wierszy. Dodatkowe dane są obcinane. |
| Jednostki administracyjne | Zasób usługi Azure AD może być członkiem nie więcej niż 30 jednostek administracyjnych. |
| Role i uprawnienia administratora | <ul><li>Nie można dodać grupy jako [właściciela](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>Nie można przypisać grupy do [roli](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>Możliwość odczytywania informacji o katalogu innych użytkowników nie może być ograniczona poza przełącznikiem obejmującym dzierżawcę, aby wyłączyć dostęp wszystkich użytkowników niebędących administratorami do wszystkich informacji katalogu (niezalecane). Więcej informacji o uprawnieniach domyślnych znajduje się w [tym miejscu](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Przed przystąpieniem do członkostwa w roli administratora może upłynąć do 15 minut lub wylogowanie/zalogowanie się.</li></ul> |
