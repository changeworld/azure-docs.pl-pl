---
title: Domyślne uprawnienia użytkownika — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o uprawnieniach inny użytkownik, dostępne w usłudze Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99ceb72930d756926bbc73508b20fbde8ea31324
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561603"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Jakie są domyślne uprawnienia użytkowników usługi Azure Active Directory?
W usłudze Azure Active Directory (Azure AD) wszystkim użytkownikom jest udzielany zestaw uprawnień domyślnych. Dostęp użytkownika składa się z typu użytkownika, ich przypisań [ról](active-directory-users-assign-role-azure-portal.md)i ich własności do poszczególnych obiektów. W tym artykule opisano te uprawnienia domyślne oraz zawarto porównanie uprawnień domyślnych członka i użytkownika gościa. Domyślne uprawnienia użytkownika można zmienić tylko w ustawieniach użytkownika w usłudze Azure AD.

## <a name="member-and-guest-users"></a>Użytkownicy będący członkami i gośćmi
Zestaw odebranych uprawnień domyślnych zależy od tego, czy użytkownik jest natywnym członkiem dzierżawy (użytkownik), czy też użytkownik jest przełączany z innego katalogu jako gość współpracy B2B (gość). Zobacz artykuł [co to jest współpraca B2B w usłudze Azure AD?](../b2b/what-is-b2b.md) Aby uzyskać więcej informacji na temat dodawania użytkowników-Gości.
* Użytkownicy będący członkami mogą rejestrować aplikacje, zmieniać swoje zdjęcie profilowe, numer telefonu komórkowego oraz hasło. Mogą też zapraszać gości B2B. Dodatkowo użytkownicy mogą odczytywać wszystkie informacje o katalogach (z kilkoma wyjątkami). 
* Użytkownicy-Goście mają ograniczone uprawnienia do katalogu. Goście nie mogą na przykład przeglądać informacji o dzierżawie wykraczających poza informacje z własnego profilu. Jednak użytkownik gość może pobierać informacje o innym użytkowniku po podaniu głównej nazwy użytkownika lub identyfikatora objectId. Użytkownik-gość może odczytywać właściwości grup, do których należą, łącznie z członkostwem w grupie, niezależnie od **uprawnień użytkowników-Gości** . Gość nie może wyświetlić informacji o innych obiektach dzierżawy.

Uprawnienia domyślne dla gości są restrykcyjne. Gości można dodawać do ról administratora, dzięki czemu uzyskają oni pełne uprawnienia do odczytu i zapisu zawarte w roli. Dla gości jest dostępne jeszcze jedno ograniczenie — możliwość zapraszania innych gości. Ustawienie opcji **Goście mogą zapraszać gości** na wartość **Nie** uniemożliwia gościom zapraszanie innych gości. Aby dowiedzieć się, jak ustawiać tę opcję, zobacz [Delegate invitations for B2B collaboration](../b2b/delegate-invitations.md) (Delegowanie zaproszeń na potrzeby współpracy B2B). Aby domyślnie użytkownikom gościom były przyznawane takie same uprawnienia jak użytkownikom będącym członkami, ustaw opcję **Uprawnienia użytkowników-gości są ograniczone** na wartość **Nie**. To ustawienie domyślnie przyznaje gościom wszystkie uprawnienia użytkowników będących członkami, a także umożliwia dodawanie gości do ról administracyjnych.

## <a name="compare-member-and-guest-default-permissions"></a>Porównanie uprawnień domyślnych członka i gościa

**Obszar** | **Uprawnienia użytkownika będącego członkiem** | **Uprawnienia użytkownika gościa**
------------ | --------- | ----------
Użytkownicy i kontakty | Odczytywanie wszystkich publicznych właściwości użytkowników i kontaktów<br>Zapraszanie gości<br>Zmiana własnego hasła<br>Zarządzanie własnym numerem telefonu komórkowego<br>Zarządzanie własnym zdjęciem<br>Unieważnianie własnych tokenów odświeżania | Odczytywanie własnych właściwości<br>Odczytaj wyświetlaną nazwę, adres e-mail, nazwę logowania, Zdjęcie, główną nazwę użytkownika i właściwości typu użytkownika innych użytkowników i kontaktów<br>Zmiana własnego hasła
Grupy | Tworzenie grup zabezpieczeń<br>Tworzenie grup usługi Office 365<br>Odczytywanie wszystkich właściwości grup<br>Odczytywanie nieukrytych członkostw w grupach<br>Odczytywanie ukrytych członkostw w grupach usługi Office 365 dla dołączonej grupy<br>Zarządzanie właściwościami, własnością i członkostwem w grupach, do których należy użytkownik<br>Dodawanie gości do posiadanych grup<br>Zarządzanie dynamicznymi ustawieniami członkostwa<br>Usuwanie posiadanych grup<br>Przywracanie posiadanych grup usługi Office 365 | Odczytywanie wszystkich właściwości grup<br>Odczytywanie nieukrytych członkostw w grupach<br>Odczytywanie ukrytych członkostw w grupach usługi Office 365 dla dołączonych grup<br>Zarządzanie posiadanymi grupami<br>Dodawanie gości do posiadanych grup (jeśli jest to dozwolone)<br>Usuwanie posiadanych grup<br>Przywracanie posiadanych grup usługi Office 365<br>Odczytaj właściwości grup, do których należą, łącznie z członkostwem.
Aplikacje | Rejestrowanie (tworzenie) nowej aplikacji<br>Odczytywanie właściwości zarejestrowanych aplikacji i aplikacji dla przedsiębiorstw<br>Zarządzanie właściwościami, przydziałami i poświadczeniami posiadanych aplikacji<br>Tworzenie i usuwanie hasła aplikacji dla użytkownika<br>Usuwanie posiadanych aplikacji<br>Przywracanie posiadanych aplikacji | Odczytywanie właściwości zarejestrowanych aplikacji i aplikacji dla przedsiębiorstw<br>Zarządzanie właściwościami, przydziałami i poświadczeniami posiadanych aplikacji<br>Usuwanie posiadanych aplikacji<br>Przywracanie posiadanych aplikacji
Urządzenia | Odczytywanie wszystkich właściwości urządzenia<br>Zarządzanie wszystkimi właściwościami posiadanych urządzeń<br> | Brak uprawnień<br>Usuwanie posiadanych urządzeń<br>
Katalog | Odczytywanie wszystkich informacji o firmie<br>Odczytywanie wszystkich domen<br>Odczytywanie wszystkich kontraktów partnera | Odczytywanie nazwy wyświetlanej i zweryfikowanych domen
Role i zakresy | Odczytywanie wszystkich ról administracyjnych i członkostw<br>Odczytywanie wszystkich właściwości i członkostw jednostek administracyjnych | Brak uprawnień 
Subskrypcje | Odczytywanie wszystkich subskrypcji<br>Włączanie członka planu usługi | Brak uprawnień
Zasady | Odczytywanie wszystkich właściwości zasad<br>Zarządzanie wszystkimi właściwościami posiadanych zasad | Brak uprawnień

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Ograniczanie uprawnień domyślnych użytkowników będących członkami

Poniżej opisano sposoby ograniczania domyślnych uprawnień użytkowników będących członkami.

Uprawnienie | Wyjaśnienie ustawienia
---------- | ------------
Użytkownicy mogą rejestrować aplikację | Ustawienie tej opcji na wartość nie uniemożliwia użytkownikom tworzenie rejestracji aplikacji. Możliwość można następnie udzielić z powrotem do określonych osób przez dodanie ich do roli Deweloper aplikacji.
Zezwalaj użytkownikom na łączenie konta służbowego z usługą LinkedIn | Ustawienie tej opcji na wartość nie uniemożliwia użytkownikom łączenie konta służbowego z kontem usługi LinkedIn. Aby uzyskać więcej informacji, zobacz [konta usługi LinkedIn — udostępnianie i wyrażanie danych](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Możliwość tworzenia grup zabezpieczeń | Ustawienie tej opcji na wartość Nie uniemożliwia użytkownikom tworzenie grup zabezpieczeń. Administratorzy globalni i Administratorzy użytkowników nadal mogą tworzyć grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md).
Możliwość tworzenia grup usługi Office 365 | Ustawienie tej opcji na wartość Nie uniemożliwia użytkownikom tworzenie grup usługi Office 365. Ustawienie tej opcji na wartość Niektóre umożliwia wybranemu zestawowi użytkowników tworzenie grup usługi Office 365. Administratorzy globalni i Administratorzy użytkowników nadal będą mogli tworzyć grupy pakietu Office 365. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md).
Ogranicz dostęp do portalu administracyjnego usługi Azure AD | Ustawienie tej opcji na wartość tak uniemożliwia użytkownikom dostęp do Azure Active Directory tylko przez Azure Portal.
Możliwość odczytywania innych użytkowników | To ustawienie jest dostępne tylko w programie PowerShell. Ustawienie tej flagi na $false uniemożliwia wszystkim innym niż administratorzy odczytywanie informacji o użytkownikach z katalogu. Ta flaga nie uniemożliwia odczytywania informacji o użytkowniku w innych usługach firmy Microsoft, takich jak Exchange Online. To ustawienie jest przeznaczone dla szczególnych okoliczności i ustawienie tej flagi na $false nie jest zalecane.

## <a name="object-ownership"></a>Własność obiektu

### <a name="application-registration-owner-permissions"></a>Uprawnienia właściciela przy rejestracji aplikacji
Gdy użytkownik rejestruje aplikację, jest on automatycznie dodawany jako właściciel aplikacji. Jako właściciel użytkownik może zarządzać metadanymi aplikacji, takimi jak nazwa i uprawnienia żądane przez aplikację. Właściciele mogą również zarządzać konfiguracją aplikacji specyficzną dla dzierżawy. Może to być na przykład konfiguracja logowania jednokrotnego i przypisania użytkownika. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko posiadanymi przez siebie aplikacjami.

### <a name="enterprise-application-owner-permissions"></a>Uprawnienia właściciela aplikacji dla przedsiębiorstw
Gdy użytkownik doda nową aplikację dla przedsiębiorstw, zostanie ona automatycznie dodana jako właściciel. Jako właściciel mogą zarządzać konfiguracją aplikacji specyficzną dla dzierżawców, taką jak konfiguracja logowania jednokrotnego, Inicjowanie obsługi administracyjnej i przypisania użytkowników. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko aplikacjami, których są właścicielami.

### <a name="group-owner-permissions"></a>Uprawnienia właściciela grupy
Gdy użytkownik tworzy grupę, jest automatycznie dodawany jako właściciel tej grupy. Jako właściciel mogą zarządzać właściwościami grupy, takimi jak nazwa, a także do zarządzania członkostwem w grupie. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych i administratorów użytkowników, właściciele mogą zarządzać tylko tymi grupami. Aby dowiedzieć się, jak przypisać właściciela grupy, zobacz [Managing owners for a group](active-directory-accessmanagement-managing-group-owners.md) (Zarządzanie właścicielami grupy).

### <a name="ownership-permissions"></a>Uprawnienia własności
W poniższych tabelach opisano określone uprawnienia w Azure Active Directory użytkownicy członkowie mają do nich obiekty. Użytkownik ma tylko te uprawnienia do obiektów, które są właścicielami.

#### <a name="owned-application-registrations"></a>Rejestracje posiadanych aplikacji
Użytkownicy mogą wykonywać następujące działania dotyczące rejestracji aplikacji należących do użytkownika.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/applications/audience/update | Aktualizowanie właściwości applications.audience w usłudze Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aktualizowanie właściwości applications.authentication w usłudze Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Aktualizowanie właściwości podstawowych w aplikacjach w usłudze Active Directory Azure. |
| microsoft.directory/applications/credentials/update | Aktualizowanie właściwości applications.credentials w usłudze Azure Active Directory. |
| Microsoft. katalog/aplikacje/usuwanie | Usuwanie aplikacji w usłudze Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Update | Aktualizowanie właściwości applications.owners w usłudze Azure Active Directory. |
| Microsoft. Directory/Applications/Permissions/Update | Aktualizowanie właściwości applications.permissions w usłudze Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Update | Aktualizowanie właściwości applications.policies w usłudze Azure Active Directory. |
| Microsoft. katalog/aplikacje/przywracanie | Przywracanie aplikacji w usłudze Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Posiadane aplikacje dla przedsiębiorstw
Użytkownicy mogą wykonywać następujące działania w aplikacjach należących do przedsiębiorstwa. Aplikacja firmowa składa się z nazwy głównej usługi, co najmniej jednej zasady aplikacji, a czasami obiektu aplikacji w tej samej dzierżawie co nazwa główna usługi.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowane) w dziennikach inspekcji w usłudze Azure Active Directory. |
| Microsoft. katalog/zasady/podstawowa/aktualizacja | Aktualizowanie właściwości podstawowych w zasadach w usłudze Azure Active Directory. |
| Microsoft. Directory/zasady/usuwanie | Usuwanie zasad w usłudze Azure Active Directory. |
| Microsoft. katalog/zasady/właściciele/aktualizacja | Aktualizowanie właściwości policies.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Aktualizowanie właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Aktualizowanie właściwości servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Aktualizowanie właściwości servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aktualizowanie właściwości podstawowych elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Aktualizowanie właściwości servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Usuwanie elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Aktualizowanie właściwości servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Aktualizowanie właściwości servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Aktualizowanie właściwości servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowanych) w elemencie signInReports w usłudze Azure Active Directory. |

#### <a name="owned-devices"></a>Urządzenia należące
Użytkownicy mogą wykonywać następujące działania na urządzeniach należących do firmy.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Odczytywanie właściwości devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Wyłączanie urządzeń w usłudze Azure Active Directory. |

#### <a name="owned-groups"></a>Należące do Ciebie grupy
Użytkownicy mogą wykonywać następujące działania w grupach będących własnością.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Aktualizowanie właściwości groups.appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Aktualizowanie właściwości podstawowych w grupach w usłudze Active Directory Azure. |
| Microsoft. Directory/Groups/Delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Aktualizowanie właściwości groups.dynamicMembershipRule w usłudze Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups.members w usłudze Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizowanie właściwości groups.owners w usłudze Azure Active Directory. |
| microsoft.directory/groups/restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aktualizowanie właściwości groups.settings w usłudze Azure Active Directory. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat sposobu przypisywania ról administratora usługi Azure AD, zobacz [przypisać użytkownika do ról administratora w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Zarządzanie użytkownikami](add-users-azure-active-directory.md)
