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
ms.openlocfilehash: 2b52604d4ad20fed83c4649f046722ed45e766c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097697"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Konfigurowanie samoobsługowego zarządzania grupami w Azure Active Directory 

Można umożliwić użytkownikom tworzenie własnych grup zabezpieczeń lub grup usługi Office 365 w usłudze Azure Active Directory (Azure AD) oraz zarządzanie nimi. Właściciel grupy może zatwierdzać lub odrzucać żądania członkostwa, a także delegować kontrolę nad członkostwem w grupie. Funkcje samoobsługowego zarządzania grupami są niedostępne w przypadku grup zabezpieczeń obsługujących pocztę lub listy dystrybucyjnej.

## <a name="self-service-group-membership-defaults"></a>Ustawienia domyślne członkostwa w grupach samoobsługi

Po utworzeniu grup zabezpieczeń w Azure Portal lub przy użyciu programu Azure AD PowerShell tylko właściciele grupy mogą aktualizować członkostwo. Grupy zabezpieczeń utworzone w [panelu dostępu](https://account.activedirectory.windowsazure.com/r#/joinGroups) i wszystkie grupy pakietu Office 365 są dostępne do przyłączenia do wszystkich użytkowników, niezależnie od tego, czy są zatwierdzone przez właściciela, czy z autozatwierdzania. W panelu dostępu można zmienić opcje członkostwa podczas tworzenia grupy.

Grupy utworzone w | Zachowanie domyślne grupy zabezpieczeń | Zachowanie domyślne grupy Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Tylko właściciele mogą dodawać członków<br>Widoczne, ale niedostępne do przyłączenia w panelu dostępu | Otwórz do dołączenia dla wszystkich użytkowników
[Azure Portal](https://portal.azure.com) | Tylko właściciele mogą dodawać członków<br>Widoczne, ale niedostępne do przyłączenia w panelu dostępu<br>Właściciel nie jest przypisywany automatycznie podczas tworzenia grupy | Otwórz do dołączenia dla wszystkich użytkowników
[Panel dostępu](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Otwórz do dołączenia dla wszystkich użytkowników<br>Opcje członkostwa można zmienić po utworzeniu grupy | Otwórz do dołączenia dla wszystkich użytkowników<br>Opcje członkostwa można zmienić po utworzeniu grupy

## <a name="self-service-group-management-scenarios"></a>Scenariusze samoobsługowego zarządzania grupami

* **Delegowane zarządzanie grupami** Przykładem może być administrator zarządzający dostępem do aplikacji SaaS używanej w firmie. Zarządzanie prawami dostępu jest coraz większym obciążeniem, więc administrator zwraca się do właściciela firmy o utworzenie nowej grupy. Administrator przypisuje aplikacji dostęp do nowej grupy i dodaje do grupy wszystkie osoby, które już uzyskują dostęp do aplikacji. Właściciel firmy może dodawać kolejnych użytkowników, dla których aplikacja jest automatycznie aprowizowana. Właściciel firmy nie musi czekać na administratora w celu zarządzania dostępem dla użytkowników. Jeśli administrator przyznaje to samo uprawnienie do Menedżera w innej grupie biznesowej, może również zarządzać dostępem dla swoich własnych członków grupy. Właściciel biznesowy i Menedżer nie mogą wyświetlać ani zarządzać członkostwem w grupach poszczególnych grup. Administrator nadal może wyświetlać wszystkich użytkowników mających dostęp do aplikacji i w razie potrzeby blokować prawa dostępu.
* **Samoobsługowe zarządzanie grupami** Przykładem tego scenariusza jest dwóch użytkowników mających niezależnie skonfigurowane witryny usługi SharePoint Online. Każdy z nich chce przydzielić dostęp do swojej witryny zespołowi drugiego użytkownika. W tym celu mogą utworzyć jedną grupę w usłudze Azure AD i wybrać tę grupę do przydzielenia dostępu do witryn w usłudze SharePoint Online. Gdy inna osoba będzie chciała uzyskać dostęp, wprowadzi żądanie w Panelu dostępu, a po zatwierdzeniu automatycznie uzyska dostęp do obu witryn usługi SharePoint Online. Jeden z użytkowników może stwierdzić później, że wszystkie osoby mające dostęp do witryny powinny również mieć dostęp do określonej aplikacji SaaS. Administrator aplikacji SaaS może dodać do witryny programu SharePoint Online prawa dostępu dla aplikacji. Od tego momentu zatwierdzenie żądania dostępu spowoduje przyznanie dostępu do obu witryn usługi SharePoint Online oraz do tej aplikacji SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Włączanie samoobsługi użytkowników w grupie

1. Zaloguj się w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz pozycję **Użytkownicy i grupy**, a następnie wybierz pozycję **Ustawienia grupy**.
3. Dla pozycji **Włączono samoobsługowe zarządzanie grupami** ustaw opcję **Tak**.
4. Dla pozycji **Użytkownicy mogą tworzyć grupy zabezpieczeń** lub **Użytkownicy mogą tworzyć grupy usługi Office 365** ustaw opcję **Tak**.
   * W przypadku włączenia tych ustawień wszyscy użytkownicy w katalogu mogą tworzyć nowe grupy zabezpieczeń i dodawać do nich członków. Te nowe grupy będą również wyświetlane na panelu dostępu dla innych użytkowników. Jeśli ustawienie zasad grupy to umożliwia, inni użytkownicy mogą tworzyć żądania, aby dołączyć do tych grup. 
   * Jeśli te ustawienia są wyłączone, użytkownicy nie mogą tworzyć grup ani zmieniać istniejących grup, których są właścicielami. Jednak nadal mogą zarządzać członkostwem w tych grupach i zatwierdzać żądania dołączenia do ich grup pochodzące od innych użytkowników.

Dodatkowo możesz użyć opcji **Użytkownicy, którzy mogą zarządzać grupami zabezpieczeń** i **Użytkownicy, którzy mogą zarządzać grupami usługi Office 365**, aby dokładniej sterować dostępem użytkowników do samoobsługowego zarządzania grupami. W przypadku włączenia opcji **Użytkownicy mogą tworzyć grupy** wszyscy użytkownicy w dzierżawie mogą tworzyć nowe grupy i dodawać do nich członków. Nie można określić osób, które mogą tworzyć własne grupy. Można określić tylko osoby, które będą miały właściciela grupy.

Ustawiając **użytkowników, którzy mogą używać samoobsługi dla grup zabezpieczeń** i **użytkowników, którzy mogą zarządzać grupami pakietu Office 365** **, należy**umożliwić wszystkim użytkownikom w dzierżawie tworzenie nowych grup.

Możesz również użyć ustawienia **Grupa, która może zarządzać grupami zabezpieczeń** lub **Grupa, która może zarządzać grupami usługi Office 365**, aby określić jedną grupę, której członkowie mogą używać samoobsługi.

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](groups-settings-cmdlets.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
