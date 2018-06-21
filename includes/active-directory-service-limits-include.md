---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 06/20/2018
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 10b5fbe0a03d3ea712edc9df603bbcea5e188a02
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296536"
---
Oto ograniczenia używania i inne limity usługi dotyczące usługi Azure Active Directory.

| Kategoria | Limity |
| --- | --- |
| Katalogi |Pojedynczego użytkownika można skojarzyć z maksymalnie 20 katalogów usługi Azure Active Directory.<br />Przykłady możliwych kombinacji: <ul> <li>Jeden użytkownik tworzy 20 katalogów.</li><li>Jeden użytkownik zostaje dodany do 20 katalogów jako członek.</li><li>Jeden użytkownik tworzy 10 katalogów, a później zostaje dodany przez inne osoby do 10 różnych katalogów.</li></ul> |
| Domeny | Możesz dodać maksymalnie 900 nazw domen zarządzanych. Jeśli podczas konfigurowania wszystkie domeny dla federacji z lokalną usługą Active Directory można dodawać nie więcej niż 450 nazwy domeny w każdym katalogu. |
| Obiekty |<ul><li>W jednym katalogu, można utworzyć maksymalnie 500 000 obiektów przez użytkowników bezpłatna wersja programu Azure Active Directory.</li><li>Użytkownik niebędący administratorem może utworzyć maksymalnie 250 obiektów.</li></ul> |
| Rozszerzenia schematu |<ul><li>Rozszerzenia typu string mogą mieć maksymalnie 256 znaków. </li><li>Rozmiar rozszerzeń typu binary jest ograniczony do 256 bajtów.</li><li>Do każdego pojedynczego obiektu można zapisać 100 wartości rozszerzeń (we WSZYSTKICH typach i WSZYSTKICH aplikacjach).</li><li>Jednostki „User”, „Group”, „TenantDetail”, „Device”, „Application” i „ServcicePrincipal” można rozszerzyć za pomocą jednowartościowych atrybutów typu „String” lub „Binary”.</li><li>Rozszerzenia schematu są dostępne tylko w interfejsie API programu Graph w wersji 1.21 — wersja zapoznawcza. Aby zarejestrować rozszerzenie, należy przyznać aplikacji prawa dostępu do zapisu.</li></ul> |
| Aplikacje |Maksymalnie 100 użytkowników może być właścicielami pojedynczej aplikacji. |
| Grupy |<ul><li>Maksymalnie 100 użytkowników mogą być właścicielami pojedynczej grupy.</li><li>Członkami jednej grupy w usłudze Azure Active Directory może być dowolna liczba obiektów.</li><li>Liczba członków w grupie, których można zsynchronizować z lokalnej usługi Active Directory do usługi Azure Active Directory, jest ograniczona do 15 000 członków przy użyciu narzędzia synchronizacji katalogów usługi Azure Active Directory (DirSync).</li><li>Liczba członków w grupie, których można zsynchronizować z lokalnej usługi Active Directory do usługi Azure Active Directory przy użyciu programu Azure AD Connect, jest ograniczona do 50 000 członków.</li></ul> |
| Panel dostępu |<ul><li>Nie ma żadnego limitu liczby aplikacji wyświetlanych w panelu dostępu na użytkownika końcowego dla przypisanych użytkownikom licencji na usługę Azure AD Premium lub pakiet Enterprise Mobility Suite.</li><li>W panelu dostępu może być wyświetlanych maksymalnie 10 kafelków aplikacji (na przykład Box, Salesforce czy Dropbox) na każdą przypisaną użytkownikowi końcowemu licencję na usługę Azure Active Directory w wersji Bezpłatna lub Podstawowa. Ten limit nie dotyczy kont administratorów.</li></ul> |
| Raporty | W dowolnym raporcie można wyświetlić lub pobrać maksymalnie 1000 wierszy. Dodatkowe dane są obcinane. |
| Jednostki administracyjne | Jeden obiekt może być członkiem maksymalnie 30 jednostek administracyjnych. |
