---
title: Rozwiązywanie problemów z członkostwa dynamicznego dla grupy — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące rozwiązywania problemów dynamicznego zarządzania członkostwem w grupach w usłudze Azure AD.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eededcc180d7652fd52c79b85ca3c34f65a22a4
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791560"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Rozwiązywanie problemów i eliminowanie problemów z grupy

## <a name="troubleshooting-group-creation-issues"></a>Rozwiązywanie problemów z tworzenia grupy

**Czy wyłączone Tworzenie grupy zabezpieczeń w witrynie Azure portal, ale nadal można tworzyć grupy za pomocą programu Powershell** **użytkownika mogą tworzyć grupy zabezpieczeń w portalach platformy Azure** ustawienie w kontroli portalu systemu Azure czy bez uprawnień administratora Użytkownicy mogą tworzyć grupy zabezpieczeń, w panelu dostępu lub w witrynie Azure portal. Ta funkcja nie kontroluje tworzenie grupy zabezpieczeń za pomocą programu Powershell.

Aby wyłączyć tworzenie grupy dla użytkowników bez uprawnień administratora w programie Powershell:
1. Sprawdź, czy użytkownicy niebędący administratorami mogą tworzyć grupy:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Jeśli zostanie zwrócona `UsersPermissionToCreateGroupsEnabled : True`, a następnie użytkownicy niebędący administratorami mogą tworzyć grupy. Aby wyłączyć tę funkcję:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Został wyświetlony grup maksymalny dozwolony błąd podczas próby utworzenia grupy dynamicznej w programie Powershell**<br/>
Jeśli komunikat o błędzie programu Powershell wskazującym _zasady grupy dynamicznej max dozwoloną liczbę grup osiągnięto_, oznacza to, że osiągnięto maksymalny limit grup dynamicznych w dzierżawie. Maksymalna liczba grup dynamicznych na dzierżawę wynosi 5000.

Aby utworzyć żadnych nowych grup dynamicznych, musisz najpierw usunąć niektórych istniejących grup dynamicznych. Nie ma możliwości do zwiększenia limitu.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Rozwiązywanie problemów z członkostwem dynamicznym w grupach

**Czy mogę skonfigurować regułę w grupie, ale zaktualizowani nie członkostwa w grupie**<br/>
1. Sprawdź wartości dla użytkownika lub atrybuty urządzenia w regule. Upewnij się, że istnieją użytkownicy, którzy spełniają reguły. W przypadku urządzeń Sprawdź właściwości urządzenia, aby upewnić się, wszystkie zsynchronizowane atrybuty zawierają oczekiwane wartości.<br/>
2. Sprawdź członkostwo stanu przetwarzania, aby upewnić się, jeśli został ukończony. Możesz sprawdzić [członkostwa stanu przetwarzania](groups-create-rule.md#check-processing-status-for-a-rule) i Data ostatniej aktualizacji na **Przegląd** stronie dla grupy.

Jeśli wszystko wygląda dobrze, może potrwać trochę czasu na wypełnienie grupy. W zależności od rozmiaru dzierżawy pierwsze wypełnienie grupy lub wypełnienie grupy po zmianie reguły może potrwać do 24 godzin.

**Po skonfigurowaniu reguły, ale teraz są usuwane istniejący członkowie reguły**<br/>Jest to oczekiwane zachowanie. Istniejący członkowie grupy są usuwane, gdy reguła jest włączona lub zmienione. Użytkownicy, zwrócone w wyniku oceny reguły są dodawane jako elementy członkowskie do grupy.

**Nie widzę, że członkostwo ulegnie zmianie, natychmiast po dodać lub zmienić reguły, dlaczego nie?**<br/>Ocena członkowstwa dedykowanych odbywa się okresowo w proces w tle asynchronicznego. Jak długo trwa proces zależy od liczby użytkowników w katalogu i rozmiar grupy utworzone w wyniku reguły. Zazwyczaj katalogów przy użyciu niewielkiej ich liczby użytkowników zobaczą zmiany członkostwa w grupie w mniej niż kilka minut. Katalogi z dużą liczbą użytkowników może potrwać 30 minut lub dłużej w celu wypełnienia.

**Jak wymusić, grupy, które mają być przetwarzane teraz?**<br/>
Obecnie nie ma możliwości automatycznie wyzwalać grupy, które mają być przetwarzane na żądanie. Jednak możesz ręcznie wyzwolić ponowne przetwarzanie, aktualizując regułę członkowską, aby dodać spacji na końcu.  

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