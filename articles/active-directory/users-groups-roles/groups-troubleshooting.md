---
title: Rozwiązywanie problemów z dynamicznymi członkostwem w grupach — Usługa Azure AD | Dokumenty firmy Microsoft
description: Porady dotyczące rozwiązywania problemów z członkostwem w grupach dynamicznych w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026541"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Rozwiązywanie problemów z grupami i rozwiązywanie ich

## <a name="troubleshooting-group-creation-issues"></a>Rozwiązywanie problemów z tworzeniem grupy

**Wyłączono tworzenie grupy zabezpieczeń w witrynie Azure portal, ale grupy nadal można tworzyć za pośrednictwem programu Powershell** **Użytkownik może tworzyć grupy zabezpieczeń w witrynach azure ustawienie** w witrynie Azure portal kontroluje, czy użytkownicy niebędący administratorami mogą tworzyć grupy zabezpieczeń w panelu programu Access lub witrynie Azure portal. Nie kontroluje tworzenia grupy zabezpieczeń za pośrednictwem programu Powershell.

Aby wyłączyć tworzenie grup dla użytkowników niebędących administratorami w programie Powershell:
1. Sprawdź, czy użytkownicy niebędący administratorami mogą tworzyć grupy:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Jeśli powróci, `UsersPermissionToCreateGroupsEnabled : True`użytkownicy niebędący administratorami mogą tworzyć grupy. Aby wyłączyć tę funkcję:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Podczas próby utworzenia grupy dynamicznej w programie Powershell wystąpił błąd z maksymalną grupą dozwoloną**<br/>
Jeśli w programie Powershell zostanie wyświetlony komunikat wskazujący _zasady grupy dynamiczne maksymalna liczba dozwolonych grup osiągnięta,_ oznacza to, że osiągnięto maksymalny limit dla grup dynamicznych w dzierżawie. Maksymalna liczba grup dynamicznych na dzierżawę wynosi 5000.

Aby utworzyć nowe grupy dynamiczne, musisz najpierw usunąć niektóre istniejące grupy dynamiczne. Nie ma sposobu, aby zwiększyć limit.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Rozwiązywanie problemów z członkostwem dynamicznym w grupach

**Skonfigurowałem regułę w grupie, ale żadne członkostwo nie jest aktualizowane w grupie**<br/>
1. Sprawdź wartości atrybutów użytkownika lub urządzenia w regule. Upewnij się, że są użytkownicy, którzy spełniają regułę. W przypadku urządzeń sprawdź właściwości urządzenia, aby upewnić się, że wszystkie zsynchronizowane atrybuty zawierają oczekiwane wartości.<br/>
2. Sprawdź stan przetwarzania członkostwa, aby potwierdzić, czy jest ono ukończone. Możesz sprawdzić [stan przetwarzania członkostwa](groups-create-rule.md#check-processing-status-for-a-rule) i ostatnią zaktualizowaną datę na stronie **Przegląd** dla grupy.

Jeśli wszystko wygląda dobrze, proszę dać trochę czasu dla grupy do zaludnienia. W zależności od rozmiaru dzierżawy pierwsze wypełnienie grupy lub wypełnienie grupy po zmianie reguły może potrwać do 24 godzin.

**Skonfigurowałem regułę, ale teraz istniejące elementy członkowskie reguły są usuwane**<br/>Jest to oczekiwane zachowanie. Istniejący członkowie grupy są usuwane, gdy reguła jest włączona lub zmieniona. Użytkownicy powracający z oceny reguły są dodawane jako członkowie grupy.

**Nie widzę zmian członkostwa natychmiast po dodaniu lub zmianie reguły, dlaczego nie?**<br/>Dedykowana ocena członkostwa odbywa się okresowo w asynchronicznej pracy w tle. Czas potrzebny na proces zależy od liczby użytkowników w katalogu i rozmiaru grupy utworzonej w wyniku reguły. Zazwyczaj katalogi z niewielką liczbą użytkowników będą widzieć zmiany członkostwa w grupie w mniej niż kilka minut. Katalogi z dużą liczbą użytkowników może potrwać 30 minut lub dłużej, aby wypełnić.

**Jak mogę wymusić teraz przetworzenie grupy?**<br/>
Obecnie nie ma możliwości automatycznego wyzwalania grupy do przetworzenia na żądanie. Można jednak ręcznie wyzwolić ponowne przetwarzanie, aktualizując regułę członkostwa, aby dodać biały znak na końcu.  

**Napotkałem błąd przetwarzania reguł**<br/>W poniższej tabeli wymieniono typowe błędy dynamicznej reguły członkostwa i sposób ich poprawiania.

| Błąd analizatora reguły | Użycie błędu | Poprawione użycie |
| --- | --- | --- |
| Błąd: Atrybut nie jest obsługiwany. |(user.invalidProperty -eq "Wartość") |(user.department -eq "wartość")<br/><br/>Upewnij się, że atrybut znajduje się na [liście obsługiwanych właściwości](groups-dynamic-membership.md#supported-properties). |
| Błąd: Operator nie jest obsługiwany w atrybucie. |(user.accountEnabled -zawiera true) |(user.accountEnabled -eq true)<br/><br/>Operator używany nie jest obsługiwany dla typu właściwości (w tym przykładzie -contains nie można używać w typie logicznym). Użyj odpowiednich operatorów dla typu właściwości. |
| Błąd: błąd kompilacji kwerendy. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match@domain.ext"* ") | 1. Zaginiony operator. Użyj predykatów sprzężenia -i lub -lub -lub<br>(user.department -eq "Sales") -lub (user.department -eq "Marketing")<br>2. Błąd w wyrażeniu regularnym używanym z -match<br>(user.userPrincipalName -match ".*@domain.ext")<br>lub alternatywnie: (user.userPrincipalName@domain.ext-match " $") |

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)