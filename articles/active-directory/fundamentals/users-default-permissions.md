---
title: Domyślne uprawnienia użytkownika — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o uprawnieniach inny użytkownik, dostępne w usłudze Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: lizross
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e501860691cccc0578a0df4eec2b161b99b4c
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341363"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Jakie są domyślne uprawnienia użytkowników usługi Azure Active Directory?
W usłudze Azure Active Directory (Azure AD) wszystkim użytkownikom jest udzielany zestaw uprawnień domyślnych. Dostęp użytkownika, który składa się z typ użytkownika, ich [przypisań ról](active-directory-users-assign-role-azure-portal.md)i ich własność poszczególnych obiektów. W tym artykule opisano te uprawnienia domyślne oraz zawarto porównanie uprawnień domyślnych członka i użytkownika gościa. Domyślne uprawnienia użytkowników można zmienić tylko w ustawieniach użytkownika w usłudze Azure AD.

## <a name="member-and-guest-users"></a>Użytkownicy będący członkami i gośćmi
Zestaw domyślnych uprawnień otrzymanych zależy, czy użytkownik jest członkiem natywnych dzierżawy (użytkownika elementu członkowskiego) lub jeśli użytkownik jest kupiony z innego katalogu jako gość współpracy B2B (Gość). Zobacz [czym jest współpraca B2B w usłudze Azure AD?](../b2b/what-is-b2b.md) Aby uzyskać więcej informacji na temat dodawania użytkowników-gości.
* Użytkownicy będący członkami mogą rejestrować aplikacje, zmieniać swoje zdjęcie profilowe, numer telefonu komórkowego oraz hasło. Mogą też zapraszać gości B2B. Dodatkowo użytkownicy mogą odczytywać wszystkie informacje o katalogach (z kilkoma wyjątkami). 
* Użytkownicy-goście mają ograniczone uprawnienia katalogu. Goście nie mogą na przykład przeglądać informacji o dzierżawie wykraczających poza informacje z własnego profilu. Jednak użytkownik gość może pobierać informacje o innym użytkowniku po podaniu głównej nazwy użytkownika lub identyfikatora objectId. Użytkownik-Gość mogą odczytać właściwości grup, które należą do, w tym członkostwa w grupie, bez względu na to **uprawnienia użytkowników-gości są ograniczone** ustawienie. Gość nie można wyświetlić informacje o innych obiektów w dzierżawie.

Uprawnienia domyślne dla gości są restrykcyjne. Gości można dodawać do ról administratora, dzięki czemu uzyskają oni pełne uprawnienia do odczytu i zapisu zawarte w roli. Dla gości jest dostępne jeszcze jedno ograniczenie — możliwość zapraszania innych gości. Ustawienie opcji **Goście mogą zapraszać gości** na wartość **Nie** uniemożliwia gościom zapraszanie innych gości. Aby dowiedzieć się, jak ustawiać tę opcję, zobacz [Delegate invitations for B2B collaboration](../b2b/delegate-invitations.md) (Delegowanie zaproszeń na potrzeby współpracy B2B). Aby domyślnie użytkownikom gościom były przyznawane takie same uprawnienia jak użytkownikom będącym członkami, ustaw opcję **Uprawnienia użytkowników-gości są ograniczone** na wartość **Nie**. To ustawienie domyślnie przyznaje gościom wszystkie uprawnienia użytkowników będących członkami, a także umożliwia dodawanie gości do ról administracyjnych.

## <a name="compare-member-and-guest-default-permissions"></a>Porównanie uprawnień domyślnych członka i gościa

**Obszar** | **Uprawnienia użytkownika będącego członkiem** | **Uprawnienia użytkownika gościa**
------------ | --------- | ----------
Użytkownicy i kontakty | Odczytywanie wszystkich publicznych właściwości użytkowników i kontaktów<br>Zapraszanie gości<br>Zmiana własnego hasła<br>Zarządzanie własnym numerem telefonu komórkowego<br>Zarządzanie własnym zdjęciem<br>Unieważnianie własnych tokenów odświeżania | Odczytywanie własnych właściwości<br>Odczyt nazwę wyświetlaną, poczty e-mail, zarejestrować nazwę, zdjęcia, główną nazwę użytkownika i właściwości typu użytkownika dla innych użytkowników i kontaktów<br>Zmiana własnego hasła
Grupy | Tworzenie grup zabezpieczeń<br>Tworzenie grup usługi Office 365<br>Odczytywanie wszystkich właściwości grup<br>Odczytywanie nieukrytych członkostw w grupach<br>Odczytywanie ukrytych członkostw w grupach usługi Office 365 dla dołączonej grupy<br>Zarządzanie właściwościami, własności i członkostwa w grupach, które należą do użytkownika<br>Dodawanie gości do posiadanych grup<br>Zarządzanie dynamicznymi ustawieniami członkostwa<br>Usuwanie posiadanych grup<br>Przywracanie posiadanych grup usługi Office 365 | Odczytywanie wszystkich właściwości grup<br>Odczytywanie nieukrytych członkostw w grupach<br>Odczytywanie ukrytych członkostw w grupach usługi Office 365 dla dołączonych grup<br>Zarządzanie posiadanymi grupami<br>Dodawanie gości do posiadanych grup (jeśli jest to dozwolone)<br>Usuwanie posiadanych grup<br>Przywracanie posiadanych grup usługi Office 365<br>Odczyt właściwości grup, do których należą, w tym członkostwa.
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
Użytkownicy mogą rejestrować aplikacji | Ustawienie tej opcji nie uniemożliwia użytkownikom tworzenie rejestracje aplikacji. Następnie można udzielić zdolność do konkretnych osób przez dodanie ich do roli dewelopera aplikacji.
Zezwalaj użytkownikom na łączenie konta służbowego z usługą LinkedIn | Ustawienie tej opcji nie uniemożliwia użytkownikom łączenie ich konta firmowego lub szkolnego za pomocą konta LinkedIn. Aby uzyskać więcej informacji, zobacz [konta LinkedIn, udostępnianie danych połączeń i zgody](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Możliwość tworzenia grup zabezpieczeń | Ustawienie tej opcji na wartość Nie uniemożliwia użytkownikom tworzenie grup zabezpieczeń. Administratorzy globalni i Administratorzy użytkowników nadal mogą tworzyć grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md).
Możliwość tworzenia grup usługi Office 365 | Ustawienie tej opcji na wartość Nie uniemożliwia użytkownikom tworzenie grup usługi Office 365. Ustawienie tej opcji na wartość Niektóre umożliwia wybranemu zestawowi użytkowników tworzenie grup usługi Office 365. Administratorzy globalni i Administratorzy użytkownik nadal będzie można utworzyć grupy usługi Office 365. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md).
Ogranicz dostęp do portalu administracyjnego usługi Azure AD | Ustawienie tej opcji na tak uniemożliwia użytkownikom uzyskiwanie dostępu do usługi Azure Active Directory tylko w portalu Azure.
Możliwość odczytywania innych użytkowników | To ustawienie jest dostępne tylko w programie PowerShell. Ustawienie tej flagi na $false uniemożliwia odczytywanie informacji o użytkowniku z katalogu wszystkich innych administratorów. Ta flaga nie uniemożliwia odczytywanie informacji o użytkowniku w innych usługach firmy Microsoft, takich jak usługa Exchange Online. To ustawienie jest przeznaczona dla szczególnych okolicznościach, a ustawienie tej flagi na $false nie jest zalecane.

## <a name="object-ownership"></a>Własność obiektu

### <a name="application-registration-owner-permissions"></a>Uprawnienia właściciela przy rejestracji aplikacji
Gdy użytkownik rejestruje aplikację, jest on automatycznie dodawany jako właściciel aplikacji. Jako właściciel użytkownik może zarządzać metadanymi aplikacji, takimi jak nazwa i uprawnienia żądane przez aplikację. Właściciele mogą również zarządzać konfiguracją aplikacji specyficzną dla dzierżawy. Może to być na przykład konfiguracja logowania jednokrotnego i przypisania użytkownika. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko posiadanymi przez siebie aplikacjami.

### <a name="enterprise-application-owner-permissions"></a>Uprawnienia właściciela aplikacji przedsiębiorstwa
Po użytkownik doda nową aplikację przedsiębiorstwa, są one automatycznie dodawane jako właściciela. Jako właściciel mogą zarządzać konfiguracją specyficznym dla dzierżawy aplikacji, takich jak usługa rejestracji Jednokrotnej w konfiguracji, udostępnianie i przypisań użytkowników. Właściciel może również dodawać i usuwać innych właścicieli. Inaczej niż w przypadku administratorów globalnych właściciele mogą zarządzać tylko te aplikacje, które są właścicielami.

### <a name="group-owner-permissions"></a>Uprawnienia właściciela grupy
Gdy użytkownik tworzy grupę, jest automatycznie dodawany jako właściciel tej grupy. Jako właściciel one mogą zarządzać właściwościami grupy, takie jak nazwa, a także zarządzanie członkostwem w grupie. Właściciel może również dodawać i usuwać innych właścicieli. W odróżnieniu od Administratorzy globalni i Administratorzy użytkowników właściciele mogą zarządzać tylko grupy, które są właścicielami. Aby dowiedzieć się, jak przypisać właściciela grupy, zobacz [Managing owners for a group](active-directory-accessmanagement-managing-group-owners.md) (Zarządzanie właścicielami grupy).

### <a name="ownership-permissions"></a>Uprawnienia właściciela
W poniższych tabelach opisano określone uprawnienia w usłudze Azure Active Directory mają obiektów należących do użytkowników. Użytkownik musi tylko te uprawnienia w obiektach, które są właścicielami.

#### <a name="owned-application-registrations"></a>Rejestracje aplikacji należące do firmy
Użytkownicy mogą wykonywać następujące czynności na rejestracje aplikacji należące do firmy.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/applications/audience/update | Zaktualizuj właściwość applications.audience w usłudze Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Zaktualizuj właściwość applications.authentication w usłudze Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aktualizacja podstawowe właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Zaktualizuj właściwość applications.credentials w usłudze Azure Active Directory. |
| microsoft.directory/applications/delete | Usuwanie aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/owners/update | Zaktualizuj właściwość applications.owners w usłudze Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Zaktualizuj właściwość applications.permissions w usłudze Azure Active Directory. |
| microsoft.directory/applications/policies/update | Zaktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.directory/applications/restore | Przywracanie aplikacji w usłudze Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Aplikacje dla przedsiębiorstw należące do firmy
Użytkownicy mogą wykonywać następujące czynności na aplikacje dla przedsiębiorstw należące do firmy. Aplikacja dla przedsiębiorstw składa się z nazwy głównej usługi, co najmniej jedne zasady aplikacji i czasami obiekt aplikacji w tej samej dzierżawy jako nazwy głównej usługi.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na auditLogs w usłudze Azure Active Directory. |
| microsoft.directory/policies/basic/update | Aktualizacja właściwości podstawowe zasady w usłudze Azure Active Directory. |
| microsoft.directory/policies/delete | Usuń zasady w usłudze Azure Active Directory. |
| microsoft.directory/policies/owners/update | Zaktualizuj właściwość policies.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Zaktualizuj właściwość servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Zaktualizuj właściwość servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aktualizacja właściwości podstawowe servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Zaktualizuj właściwość servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Usuń servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Zaktualizuj właściwość servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na signInReports w usłudze Azure Active Directory. |

#### <a name="owned-devices"></a>Urządzenia należące do firmy
Użytkownicy mogą wykonywać następujące czynności na urządzenia należące do firmy.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Odczytaj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.directory/devices/disable | Wyłącz urządzenia w usłudze Azure Active Directory. |

#### <a name="owned-groups"></a>Należących do grup
Użytkownicy mogą wykonywać następujące czynności na grupy należące do firmy.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Zaktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aktualizacja podstawowe właściwości grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Zaktualizuj właściwość groups.dynamicMembershipRule w usłudze Azure Active Directory. |
| microsoft.directory/groups/members/update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.directory/groups/owners/update | Zaktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.directory/groups/restore | Przywracanie grupy w usłudze Azure Active Directory. |
| microsoft.directory/groups/settings/update | Zaktualizuj właściwość groups.settings w usłudze Azure Active Directory. |

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat sposobu przypisywania ról administratora usługi Azure AD, zobacz [przypisać użytkownika do ról administratora w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Zarządzanie użytkownikami](add-users-azure-active-directory.md)
