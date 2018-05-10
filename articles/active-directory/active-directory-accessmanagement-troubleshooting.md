---
title: Rozwiązywanie problemów z członkostwo dynamiczne w grupach | Dokumentacja firmy Microsoft
description: Porady dotyczące rozwiązywania problemów dynamicznego zarządzania członkostwem w grupach w usłudze Azure AD.
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
ms.reviewer: piotrci
ms.custom: it-pro
ms.openlocfilehash: 6d8d04273e9f29b2634c8b77b0268f3c7b77b1e9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Rozwiązywanie problemów z członkostwem dynamicznym w grupach
**Po skonfigurowaniu reguły w grupie, ale aktualizowany nie członkostwa w grupie**<br/>Sprawdź wartości atrybutów użytkownika dla reguły: istnieją użytkownicy, którzy spełniają zasady? Jeśli wszystko wygląda dobrze, zaczekaj chwilę, aż do wypełnienia grupy. W zależności od rozmiaru dzierżawy pierwsze wypełnienie grupy lub wypełnienie grupy po zmianie reguły może potrwać do 24 godzin.

**Po skonfigurowaniu reguły, ale teraz istniejących członków reguły zostaną usunięte**<br/>Jest to oczekiwane zachowanie. Istniejących członków grupy są usuwane, gdy reguła jest włączona lub zmienione. Użytkownicy zwrócił ewaluacyjną reguła są dodawane jako członków do grupy.     

**Nie widzę członkostwa zmian natychmiast po Dodawanie lub zmienianie reguły, dlaczego nie?**<br/>Ocenę członkostwa dedykowanych okresowo odbywa się w tle asynchronicznego. Jak długo trwa proces jest określana przez liczbę użytkowników w katalogu i rozmiar grupy utworzone w wyniku reguły. Zazwyczaj katalogi z małej liczby użytkowników zobaczą zmiany członkostwa w grupie w mniej niż kilka minut. Katalogi z dużą liczbą użytkowników może potrwać 30 minut lub dłużej, aby wypełnić.

### <a name="next-steps"></a>Kolejne kroki
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](active-directory-manage-groups.md)
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Co to jest usługa Azure Active Directory?](active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
