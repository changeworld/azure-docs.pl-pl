---
title: Porównanie domyślnych uprawnień użytkownika w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Porównanie — członek, gościa, właściciel aplikacji i uprawnienia właściciela grupy
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/29/2018
ms.author: curtand
ms.reviewer: vincesm
ms.openlocfilehash: 0c4f6ef9d934a414ef805ddba6d2602b03bcbf16
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="default-user-permissions-in-azure-active-directory"></a>Domyślne uprawnienia użytkowników w usłudze Azure Active Directory

W usłudze Azure Active Directory (Azure AD) wszyscy użytkownicy otrzymują zestaw domyślnych uprawnień. Typ użytkownika, składa się dostępu użytkownika ich [członkostwo w rolach](https://docs.microsoft.com/azure/active-directory/active-directory-users-assign-role-azure-portal)i ich prawa własności poszczególnych obiektów. W tym artykule opisano te uprawnienia domyślne i zawiera porównanie domyślnych ustawień użytkownika elementu członkowskiego, jak i gościa.

## <a name="member-and-guest-users"></a>Element członkowski i gościa użytkowników
Zestaw domyślnych uprawnień Odebrano zależy, jeśli użytkownik jest członkiem natywnego dzierżawy (elementu członkowskiego użytkownika), lub jeśli użytkownik jest Gość współpracy B2B (Gość). Aby uzyskać więcej informacji o współpracy B2B, zobacz [co to jest współpraca B2B usługi Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md) Aby uzyskać więcej informacji o użytkownikach gościa). 
* Użytkowników można rejestrowanie aplikacji, zarządzanie własne zdjęcie i telefon komórkowy numer profilu zmieniać swoje hasła i zaproszenie B2B gości. Ponadto użytkownicy mogą odczytywać wszystkich informacji katalogowych (z pewnymi wyjątkami). 
* Azure AD B2B Goście mają ograniczone uprawnienia katalogu. Na przykład gości nie może przejrzeć informacje od dzierżawcy poza własnych informacji o profilu. Jednak użytkownika gościa można pobrać informacji o innego użytkownika, zapewniając nazwa główna użytkownika lub identyfikator obiektu. Gość nie można wyświetlić informacji o innych obiektów dzierżawy, takie jak grupy i aplikacje.

Domyślne uprawnienia dla gości są restrykcyjne domyślnie. Goście można dodać do ról administratora, które przyznają pełną odczytu i zapisu zawartych w roli. Brak jednego dodatkowe ograniczenia dostępności, możliwości dla gości z zaproszeniem dla innych gości. Ustawienie **poprosić gości** do **nie** uniemożliwia gości zapraszanie innych gości. Zobacz [delegować zaproszeń do współpracy B2B](active-directory-b2b-delegate-invitations.md) Aby dowiedzieć się, jak. Aby przyznać użytkownikom takie same uprawnienia jak użytkowników domyślnie, ustaw **uprawnień użytkowników gościa są ograniczone** do **nr**. To ustawienie przyznaje wszystkie uprawnienia użytkownika do gości domyślnie, a także umożliwiający Goście mają zostać dodane do ról administracyjnych.

## <a name="compare-member-and-guest-default-permissions"></a>Porównaj element członkowski i gościa domyślnych uprawnień

**Obszar** | **Uprawnienia użytkownika elementu członkowskiego** | **Uprawnienia użytkownika gościa**
------------ | --------- | ----------
Użytkowników i kontaktów | Odczyt wszystkich właściwości publicznej użytkowników i kontaktów<br>Zaproś Goście<br>Zmień własne hasło<br>Zarządzanie własnym telefon komórkowy<br>Zarządzanie własne zdjęcie<br>Unieważnienie tokenów odświeżania własnych | Odczyt własnych właściwości<br>Nazwa wyświetlana odczytu, poczty e-mail, nazwy logowania, zdjęć, główna nazwa użytkownika i właściwości użytkownika typ innych użytkowników i kontaktów<br>Zmień własne hasło
Grupy   | Utwórz grupy zabezpieczeń<br>Tworzenie grup usługi Office 365<br>Odczyt wszystkich właściwości grup<br>Odczytu członkostwa w grupach ukryty<br>Ukryte członkostwa w grupach usługi Office 365 dla grupy dołączonego do odczytu<br>Zarządzanie właściwości, własność i członkostwa w grupach należące do firmy<br>Dodaj gości na grupy należące do firmy<br>Zarządzanie ustawieniami członkostwo dynamiczne<br>Usuwanie należących do grup<br>Przywróć należących do grup usługi Office 365 | Odczyt wszystkich właściwości grup<br>Odczytu członkostwa w grupach ukryty<br>Odczyt ukryte członkostwa w grupach usługi Office 365 dla połączonych grup<br>Zarządzanie grupami należące do firmy<br>Dodaj gości na grupy należące do firmy (jeśli jest to dozwolone)<br>Usuwanie należących do grup<br>Przywróć należących do grup usługi Office 365           
Aplikacje | Zarejestruj (Utwórz) nowej aplikacji<br>Właściwości odczytu zarejestrowane i aplikacje przedsiębiorstwa<br>Zarządzanie właściwości aplikacji, przydziały i poświadczeń dla aplikacje należące do firmy<br>Tworzenie lub usuwanie hasło aplikacji dla użytkownika<br>Usuwanie należących do aplikacji.<br>Przywracanie należące do aplikacji | Właściwości odczytu zarejestrowane i aplikacje przedsiębiorstwa<br>Zarządzanie właściwości aplikacji, przydziały i poświadczeń dla aplikacje należące do firmy<br>Usuwanie należących do aplikacji.<br>Przywracanie należące do aplikacji
Urządzenia | Odczyt wszystkich właściwości urządzenia<br>Zarządzanie właściwościami wszystkie urządzenia należące do firmy<br> | Brak uprawnień<br>Usuwanie urządzenia należące do firmy<br>
Katalog | Wszystkie informacje firmy do odczytu<br>Wszystkich domen do odczytu<br>Przeczytaj wszystkie partnera kontraktów | Nazwa wyświetlana do odczytu i zweryfikować domeny
Role i zakresy | Odczyt wszystkich ról administracyjnych i członkostwa<br>Odczyt wszystkich właściwości i członkostwa jednostek administracyjnych | Brak uprawnień              
Subskrypcje | Przeczytaj wszystkie subskrypcje<br>Włącz członka planu usługi | Brak uprawnień
Zasady | Odczyt wszystkich właściwości zasady<br>Wszystkie właściwości należące do firmy zasad zarządzania | Brak uprawnień

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Aby ograniczyć uprawnienia domyślne użytkowników


Domyślne uprawnienia dla elementu członkowskiego, który można ograniczyć użytkowników, w następujący sposób. Aby uzyskać więcej informacji, zobacz [aplikacji, uprawnienia i zgody w usłudze Azure Active Directory](active-directory-apps-permissions-consent.md).

Uprawnienie | Opis ustawienia
---------- | ------------
Możliwość tworzenia grup zabezpieczeń | Ta opcja nie uniemożliwia użytkownikom tworzenie grup zabezpieczeń. Globalna Administratorzy i Administratorzy konta użytkownika, nadal mogą tworzyć grupy zabezpieczeń. Zobacz [poleceń cmdlet usługi Azure Active Directory do konfigurowania ustawień grupy](active-directory-accessmanagement-groups-settings-cmdlets.md) Aby dowiedzieć się, jak.
Możliwość tworzenia grup usługi Office 365 | Ta opcja nie uniemożliwia użytkownikom tworzenie grup usługi Office 365. Ustawienie tej opcji do pewnych umożliwia użytkownikom tworzenie grup usługi Office 365 wybierz zestaw. Globalna Administratorzy i Administratorzy konta użytkownika nadal będzie mógł tworzyć grup usługi Office 365. Zobacz [poleceń cmdlet usługi Azure Active Directory do konfigurowania ustawień grupy](active-directory-accessmanagement-groups-settings-cmdlets.md) Aby dowiedzieć się, jak.
Możliwość wyrazić zgodę na aplikacje | Ta opcja nie uniemożliwia użytkownikom zgodę aplikacji. Administratorzy globalni nadal będzie wyrazić zgodę na aplikacje. Aby uzyskać więcej informacji, zobacz [aplikacji, uprawnienia i zgody w usłudze Azure Active Directory](active-directory-apps-permissions-consent.md).
Możliwość dodawania aplikacji w galerii do panelu dostępu | Ta opcja nie uniemożliwia użytkownikom dodawanie galerii aplikacji do ich panelu dostępu.
Możliwość rejestrowania (Utwórz) aplikacji | Ta opcja nie uniemożliwia tworzenie aplikacji z systemem innym niż administratorzy. Administratorzy globalni nadal będzie mógł tworzyć aplikacje. Aby uzyskać więcej informacji, zobacz [aplikacji, uprawnienia i zgody w usłudze Azure Active Directory](active-directory-apps-permissions-consent.md).
Administratorzy i użytkownicy w roli zapraszającej gościa można zaprosić gości | Ta opcja nie uniemożliwia wszystkich gości zaproszenia. Zobacz Konfigurowanie domyślnych uprawnień dla użytkowników. Aby uzyskać więcej informacji, zobacz [aplikacji, uprawnienia i zgody w usłudze Azure Active Directory](active-directory-apps-permissions-consent.md).
Elementy członkowskie można zaprosić gości | Ustawienie to nie do nie pozwala na zaproszenia gości. Globalna Administratorzy, Administratorzy konta użytkownika i Inviters gościa będzie nadal można zaprosić gości. Aby uzyskać więcej informacji, zobacz [aplikacji, uprawnienia i zgody w usłudze Azure Active Directory](active-directory-apps-permissions-consent.md).
Ogranicz dostęp do portalu administracyjnego usługi Azure AD | Ta opcja nie uniemożliwia użytkownikom uzyskiwanie dostępu do portalu usługi Azure Active Directory.
Możliwość czytania innych użytkowników | To ustawienie jest dostępne w programie PowerShell tylko. To ustawienie na $false zapobiega wszystkich innych niż administratorzy odczytywania informacji użytkownika z katalogu. Nie zapobiega odczytu informacji użytkownika w innych usługach firmy Microsoft, takich jak Exchange Online. To ustawienie jest przeznaczone specjalne okoliczności i ustawienia, które nie jest zalecane na $false.

## <a name="object-ownership"></a>Własność obiektu

### <a name="application-registration-owner-permissions"></a>Uprawnienia właściciela rejestracji aplikacji
Gdy użytkownik rejestruje aplikację, zostaną automatycznie dodani jako właściciela dla aplikacji. Jako właściciel mogą zarządzać metadanych aplikacji, takie jak nazwa i uprawnienia żądań aplikacji. Można również zarządzać konfiguracji specyficznego dla dzierżawy aplikacji, takich jak przypisania konfiguracji i użytkownik logowania jednokrotnego. Właściciel można również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właścicieli może zarządzać tylko aplikacje, w których są właścicielami. Aby przypisać właściciela rejestracji aplikacji, zobacz [rejestracji aplikacji w usłudze Azure Active Directory](active-directory-app-registration.md).

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>Uprawnienia właściciela grupy

Gdy użytkownik tworzy grupy, zostaną automatycznie dodani jako właściciela tej grupy. Jako właściciel one mogą zarządzać właściwościami grupy, takie jak nazwa, a także zarządzać członkostwem. Właściciel można również dodawać i usuwać innych właścicieli. W odróżnieniu od Administratorzy globalni i Administratorzy konta użytkownika właściciele mogą zarządzać tylko grup, do których są właścicielami. Aby przypisać właściciela grupy, zobacz [Zarządzanie właścicielami grupy](active-directory-accessmanagement-managing-group-owners.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o modyfikowaniu administratorów subskrypcji platformy Azure, zobacz [How to add or change Azure administrator roles](../billing-add-change-azure-subscription-administrator.md) (Jak dodać lub zmienić role administratora platformy Azure).
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących sposobu usługi Azure Active Directory odnosi się do subskrypcji platformy Azure, zobacz [jak subskrypcje platformy Azure są kojarzone z usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Zarządzanie użytkownikami](active-directory-create-users.md)
