---
title: Rozwiązywanie problemów z dynamiczne członkostwo dla grup | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące rozwiązywania problemów dynamicznego zarządzania członkostwem w grupach w usłudze Azure AD.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: ab5a3b00d063dfdd42e67247bb2cdc37866d0164
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164133"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Rozwiązywanie problemów z członkostwem dynamicznym w grupach

**Czy mogę skonfigurować regułę w grupie, ale zaktualizowani nie członkostwa w grupie**<br/>Sprawdź wartości atrybutów użytkownika w regule: istnieją użytkownicy, którzy spełniają reguły? Jeśli wszystko wygląda dobrze, może potrwać trochę czasu na wypełnienie grupy. W zależności od rozmiaru dzierżawy pierwsze wypełnienie grupy lub wypełnienie grupy po zmianie reguły może potrwać do 24 godzin.

**Po skonfigurowaniu reguły, ale teraz są usuwane istniejący członkowie reguły**<br/>Jest to oczekiwane zachowanie. Istniejący członkowie grupy są usuwane, gdy reguła jest włączona lub zmienione. Użytkownicy, zwrócone w wyniku oceny reguły są dodawane jako elementy członkowskie do grupy.

**Nie widzę, że członkostwo ulegnie zmianie, natychmiast po dodać lub zmienić reguły, dlaczego nie?**<br/>Ocena członkowstwa dedykowanych odbywa się okresowo w proces w tle asynchronicznego. Jak długo trwa proces zależy od liczby użytkowników w katalogu i rozmiar grupy utworzone w wyniku reguły. Zazwyczaj katalogów przy użyciu niewielkiej ich liczby użytkowników zobaczą zmiany członkostwa w grupie w mniej niż kilka minut. Katalogi z dużą liczbą użytkowników może potrwać 30 minut lub dłużej w celu wypełnienia.

**Wystąpił błąd przetwarzania reguły**<br/>W poniższej tabeli wymieniono typowe błędy reguły członkostwa dynamicznego i ich rozwiązania.

| Błąd analizatora reguły | Błąd użycia | Poprawiony użycia |
| --- | --- | --- |
| Błąd: Atrybut nie jest obsługiwane. |(user.invalidProperty - eq "Value") |(user.department - eq "value")<br/><br/>Upewnij się, że atrybut jest [obsługiwane listy właściwości](groups-dynamic-membership.md#supported-properties). |
| Błąd: Operator nie jest obsługiwany dla atrybutu. |(user.accountEnabled — zawiera wartość true) |(user.accountEnabled - eq true)<br/><br/>Operator używany nie jest obsługiwana dla typu właściwości (w tym przykładzie-zawiera nie można użyć typu boolean). Prawidłowe operatory na użytek typ właściwości. |
| Błąd: Błąd kompilacji zapytania. | 1. (user.department - eq "Sprzedaż") (user.department - eq "Marketing")<br>2. (user.userPrincipalName-zgodny "*@domain.ext") | 1. Brak operatora. Użyj - i - lub dołączyć dwa predykatów<br>(user.department - eq "Sprzedaż")- lub (user.department - eq "Marketing")<br>2. Błąd w wyrażeniu regularnym, w ramach - dopasowania<br>(user.userPrincipalName-zgodny ". *@domain.ext")<br>lub też: (user.userPrincipalName-zgodny "@domain.ext$") |

## <a name="next-steps"></a>Kolejne kroki

Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
