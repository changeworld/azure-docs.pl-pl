---
title: Konfigurowanie samoobsługowego zarządzania grupami — Azure Active Directory | Microsoft Docs
description: Tworzenie grup zabezpieczeń lub grup Office 365 w usłudze Azure Active Directory oraz zarządzanie nimi i żądanie członkostwa w grupach zabezpieczeń lub grupach usługi Office 365
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b860257fd1b3f0897152dc3d48bff0c7e1d3d994
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469865"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Konfigurowanie samoobsługowego zarządzania grupami w usłudze Azure Active Directory 

Możesz umożliwić użytkownikom tworzenie i zarządzanie nimi w ich własnych grupach zabezpieczeń lub grup usługi Office 365 w usłudze Azure Active Directory (Azure AD). Właściciel grupy można zatwierdzać lub odrzucać żądania członkostwa lub można delegować kontrolę nad członkostwa w grupie. Funkcje zarządzania grupami samoobsługi nie są dostępne dla list dystrybucyjnych lub grup zabezpieczeń z włączoną obsługą poczty.

## <a name="self-service-group-membership-defaults"></a>Domyślne ustawienia członkostwa grup samoobsługi

Jeśli grupy zabezpieczeń są tworzone w witrynie Azure portal lub przy użyciu programu PowerShell usługi Azure AD, tylko właściciele grupy można zaktualizować członkostwa. Grupy zabezpieczeń utworzonej w [panelu dostępu](https://account.activedirectory.windowsazure.com/r#/joinGroups) i wszystkie grupy usługi Office 365 są dostępne do dołączenia do dla wszystkich użytkowników, czy zatwierdzone przez właściciela zatwierdzane automatycznie. W panelu dostępu można zmienić opcji członkostwa, podczas tworzenia grupy.

Grupy utworzone w | Zachowanie domyślne grupy zabezpieczeń | Zachowanie domyślne grupy usługi Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Tylko właściciele mogą dodawać członków<br>Widoczny, ale nie jest dostępna do dołączenia do panelu dostępu | Otwórz w na wszystkich użytkowników
[Azure Portal](https://portal.azure.com) | Tylko właściciele mogą dodawać członków<br>Widoczny, ale nie jest dostępna do dołączenia do panelu dostępu<br>Właściciel nie jest przypisany automatycznie podczas tworzenia grupy | Otwórz w na wszystkich użytkowników
[Panel dostępu](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Otwórz w na wszystkich użytkowników<br>Opcje członkostwa można zmienić po utworzeniu grupy | Otwórz w na wszystkich użytkowników<br>Opcje członkostwa można zmienić po utworzeniu grupy

## <a name="self-service-group-management-scenarios"></a>Scenariusze zarządzania grupami samoobsługi

* **Delegowane zarządzanie grupami** Przykładem może być administrator zarządzający dostępem do aplikacji SaaS używanej w firmie. Zarządzanie prawami dostępu jest coraz większym obciążeniem, więc administrator zwraca się do właściciela firmy o utworzenie nowej grupy. Administrator przypisuje nowej grupie dostęp do aplikacji i dodaje do grupy wszystkie osoby, które już uzyskują dostęp do aplikacji. Właściciel firmy może dodawać kolejnych użytkowników, dla których aplikacja jest automatycznie aprowizowana. Właściciel firmy nie musi czekać na administratora w celu zarządzania dostępem dla użytkowników. Jeśli administrator nada to samo uprawnienie menedżerowi w innej grupie biznesowej, a następnie osoba zarządzać dostępem dla ich własnych członków grupy. Właściciel firmy ani Menedżera można wyświetlać lub Zarządzanie członkostwami grup siebie nawzajem. Administrator nadal może wyświetlać wszystkich użytkowników mających dostęp do aplikacji i w razie potrzeby blokować prawa dostępu.
* **Samoobsługowe zarządzanie grupami** Przykładem tego scenariusza jest dwóch użytkowników mających niezależnie skonfigurowane witryny usługi SharePoint Online. Każdy z nich chce przydzielić dostęp do swojej witryny zespołowi drugiego użytkownika. W tym celu mogą utworzyć jedną grupę w usłudze Azure AD i wybrać tę grupę do przydzielenia dostępu do witryn w usłudze SharePoint Online. Gdy inna osoba będzie chciała uzyskać dostęp, wprowadzi żądanie w Panelu dostępu, a po zatwierdzeniu automatycznie uzyska dostęp do obu witryn usługi SharePoint Online. Jeden z użytkowników może stwierdzić później, że wszystkie osoby mające dostęp do witryny powinny również mieć dostęp do określonej aplikacji SaaS. Administrator aplikacji SaaS może dodać do witryny programu SharePoint Online prawa dostępu dla aplikacji. Od tego momentu zatwierdzenie żądania dostępu spowoduje przyznanie dostępu do obu witryn usługi SharePoint Online oraz do tej aplikacji SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Włączanie samoobsługi użytkowników w grupie

1. Zaloguj się w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz pozycję **Użytkownicy i grupy**, a następnie wybierz pozycję **Ustawienia grupy**.
3. Dla pozycji **Włączono samoobsługowe zarządzanie grupami** ustaw opcję **Tak**.
4. Dla pozycji **Użytkownicy mogą tworzyć grupy zabezpieczeń** lub **Użytkownicy mogą tworzyć grupy usługi Office 365** ustaw opcję **Tak**.
   * W przypadku włączenia tych ustawień wszyscy użytkownicy w katalogu mogą tworzyć nowe grupy zabezpieczeń i dodawać do nich członków. Te nowe grupy będą również wyświetlane na panelu dostępu dla innych użytkowników. Jeśli ustawienie zasad grupy to umożliwia, inni użytkownicy mogą tworzyć żądania, aby dołączyć do tych grup. 
   * Jeśli te ustawienia są wyłączone, użytkownicy nie mogą tworzyć grup ani zmieniać istniejących grup, których są właścicielami. Jednak nadal mogą zarządzać członkostwem w tych grupach i zatwierdzać żądania dołączenia do ich grup pochodzące od innych użytkowników.

Dodatkowo możesz użyć opcji **Użytkownicy, którzy mogą zarządzać grupami zabezpieczeń** i **Użytkownicy, którzy mogą zarządzać grupami usługi Office 365**, aby dokładniej sterować dostępem użytkowników do samoobsługowego zarządzania grupami. W przypadku włączenia opcji **Użytkownicy mogą tworzyć grupy** wszyscy użytkownicy w dzierżawie mogą tworzyć nowe grupy i dodawać do nich członków. Nie można określić użytkowników indywidualnych, którzy mogą tworzyć własne grupy. Indywidualnych można określić tylko w przypadku wprowadzania inny członek grupy jako właściciela grupy.

Ustawiając **użytkowników, którzy mogą używać samoobsługi dla grup zabezpieczeń** i **użytkowników, którzy mogą zarządzać grupami usługi Office 365** do **tak**, umożliwi wszystkim użytkownikom w swojej dzierżawie, aby utworzyć nowy element grupy.

Możesz również użyć ustawienia **Grupa, która może zarządzać grupami zabezpieczeń** lub **Grupa, która może zarządzać grupami usługi Office 365**, aby określić jedną grupę, której członkowie mogą używać samoobsługi.

## <a name="next-steps"></a>Kolejne kroki

Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](groups-settings-cmdlets.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
