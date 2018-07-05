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
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: aaded644635ab93cef9323ad38d1d150b15fe743
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450066"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Rozwiązywanie problemów z członkostwem dynamicznym w grupach
**Czy mogę skonfigurować regułę w grupie, ale zaktualizowani nie członkostwa w grupie**<br/>Sprawdź wartości atrybutów użytkownika w regule: istnieją użytkownicy, którzy spełniają reguły? Jeśli wszystko wygląda dobrze, może potrwać trochę czasu na wypełnienie grupy. W zależności od rozmiaru dzierżawy pierwsze wypełnienie grupy lub wypełnienie grupy po zmianie reguły może potrwać do 24 godzin.

**Po skonfigurowaniu reguły, ale teraz są usuwane istniejący członkowie reguły**<br/>Jest to oczekiwane zachowanie. Istniejący członkowie grupy są usuwane, gdy reguła jest włączona lub zmienione. Użytkownicy, zwrócone w wyniku oceny reguły są dodawane jako elementy członkowskie do grupy.     

**Nie widzę, że członkostwo ulegnie zmianie, natychmiast po dodać lub zmienić reguły, dlaczego nie?**<br/>Ocena członkowstwa dedykowanych odbywa się okresowo w proces w tle asynchronicznego. Jak długo trwa proces zależy od liczby użytkowników w katalogu i rozmiar grupy utworzone w wyniku reguły. Zazwyczaj katalogów przy użyciu niewielkiej ich liczby użytkowników zobaczą zmiany członkostwa w grupie w mniej niż kilka minut. Katalogi z dużą liczbą użytkowników może potrwać 30 minut lub dłużej w celu wypełnienia.

### <a name="next-steps"></a>Kolejne kroki
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](../active-directory-apps-index.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../connect/active-directory-aadconnect.md)
