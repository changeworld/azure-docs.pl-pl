---
title: Domyślne uprawnienia użytkowników — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się więcej o różnych uprawnieniach użytkowników dostępnych w usłudze Azure Active Directory.
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
ms.openlocfilehash: 227230f2d6f46fae27e2cec69d99390f5054c7db
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366259"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Jakie są domyślne uprawnienia użytkownika w usłudze Azure Active Directory?
W usłudze Azure Active Directory (Azure AD) wszystkim użytkownikom jest udzielany zestaw uprawnień domyślnych. Dostęp użytkownika składa się z typu użytkownika, jego [przypisania ról](active-directory-users-assign-role-azure-portal.md)i ich własności poszczególnych obiektów. W tym artykule opisano te uprawnienia domyślne oraz zawarto porównanie uprawnień domyślnych członka i użytkownika gościa. Domyślne uprawnienia użytkownika można zmienić tylko w ustawieniach użytkownika w usłudze Azure AD.

## <a name="member-and-guest-users"></a>Użytkownicy będący członkami i gośćmi
Zestaw otrzymanych uprawnień domyślnych zależy od tego, czy użytkownik jest macierzystym członkiem dzierżawy (użytkownik członkowski) lub czy użytkownik jest przenoszony z innego katalogu jako gość współpracy B2B (użytkownik-gość). Zobacz [Co to jest współpraca usługi Azure AD B2B?](../b2b/what-is-b2b.md)
* Użytkownicy będący członkami mogą rejestrować aplikacje, zmieniać swoje zdjęcie profilowe, numer telefonu komórkowego oraz hasło. Mogą też zapraszać gości B2B. Dodatkowo użytkownicy mogą odczytywać wszystkie informacje o katalogach (z kilkoma wyjątkami). 
* Użytkownicy-goście mają ograniczone uprawnienia do katalogów. Goście nie mogą na przykład przeglądać informacji o dzierżawie wykraczających poza informacje z własnego profilu. Jednak użytkownik gość może pobierać informacje o innym użytkowniku po podaniu głównej nazwy użytkownika lub identyfikatora objectId. Użytkownik-gość może odczytywać właściwości grup, do których należy, w tym członkostwo w grupie, niezależnie od **ustawienia Użytkownicy-Goście są ograniczone.** Gość nie może wyświetlać informacji o innych obiektach dzierżawy.

Uprawnienia domyślne dla gości są restrykcyjne. Gości można dodawać do ról administratora, dzięki czemu uzyskają oni pełne uprawnienia do odczytu i zapisu zawarte w roli. Dla gości jest dostępne jeszcze jedno ograniczenie — możliwość zapraszania innych gości. Ustawienie opcji **Goście mogą zapraszać gości** na wartość **Nie** uniemożliwia gościom zapraszanie innych gości. Aby dowiedzieć się, jak ustawiać tę opcję, zobacz [Delegate invitations for B2B collaboration](../b2b/delegate-invitations.md) (Delegowanie zaproszeń na potrzeby współpracy B2B). Aby domyślnie użytkownikom gościom były przyznawane takie same uprawnienia jak użytkownikom będącym członkami, ustaw opcję **Uprawnienia użytkowników-gości są ograniczone** na wartość **Nie**. To ustawienie domyślnie przyznaje gościom wszystkie uprawnienia użytkowników będących członkami, a także umożliwia dodawanie gości do ról administracyjnych.

## <a name="compare-member-and-guest-default-permissions"></a>Porównanie uprawnień domyślnych członka i gościa

**Obszar** | **Uprawnienia użytkownika będącego członkiem** | **Uprawnienia użytkownika gościa**
------------ | --------- | ----------
Użytkownicy i kontakty | Odczytywanie wszystkich publicznych właściwości użytkowników i kontaktów<br>Zapraszanie gości<br>Zmiana własnego hasła<br>Zarządzanie własnym numerem telefonu komórkowego<br>Zarządzanie własnym zdjęciem<br>Unieważnianie własnych tokenów odświeżania | Odczytywanie własnych właściwości<br>Odczytywanie nazwy wyświetlanej, wiadomości e-mail, nazwy logowania, zdjęcia, głównej nazwy użytkownika i właściwości typu użytkownika innych użytkowników i kontaktów<br>Zmiana własnego hasła
Grupy | Tworzenie grup zabezpieczeń<br>Tworzenie grup usługi Office 365<br>Odczytywanie wszystkich właściwości grup<br>Odczytywanie nieukrytych członkostw w grupach<br>Odczytywanie ukrytych członkostw w grupach usługi Office 365 dla dołączonej grupy<br>Zarządzanie właściwościami, własnością i członkostwem w grupach, do której należy użytkownik<br>Dodawanie gości do posiadanych grup<br>Zarządzanie dynamicznymi ustawieniami członkostwa<br>Usuwanie posiadanych grup<br>Przywracanie posiadanych grup usługi Office 365 | Odczytywanie wszystkich właściwości grup<br>Odczytywanie nieukrytych członkostw w grupach<br>Odczytywanie ukrytych członkostw w grupach usługi Office 365 dla dołączonych grup<br>Zarządzanie posiadanymi grupami<br>Dodawanie gości do posiadanych grup (jeśli jest to dozwolone)<br>Usuwanie posiadanych grup<br>Przywracanie posiadanych grup usługi Office 365<br>Odczyt właściwości grup, do których należą, w tym członkostwa.
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
Użytkownicy mogą rejestrować aplikację | Ustawienie tej opcji na Nie uniemożliwia użytkownikom tworzenie rejestracji aplikacji. Możliwość może następnie zostać przyznane z powrotem do określonych osób, dodając je do roli dewelopera aplikacji.
Zezwalaj użytkownikom na łączenie konta służbowego z linkedin | Ustawienie tej opcji na Nie uniemożliwia użytkownikom łączenie konta służbowego z kontem LinkedIn. Aby uzyskać więcej informacji, zobacz [Udostępnianie i zgoda połączeń kont LinkedIn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Możliwość tworzenia grup zabezpieczeń | Ustawienie tej opcji na wartość Nie uniemożliwia użytkownikom tworzenie grup zabezpieczeń. Administratorzy globalni i administratorzy użytkowników nadal mogą tworzyć grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md).
Możliwość tworzenia grup usługi Office 365 | Ustawienie tej opcji na wartość Nie uniemożliwia użytkownikom tworzenie grup usługi Office 365. Ustawienie tej opcji na wartość Niektóre umożliwia wybranemu zestawowi użytkowników tworzenie grup usługi Office 365. Administratorzy globalni i administratorzy użytkowników nadal będą mogli tworzyć grupy usługi Office 365. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md).
Ogranicz dostęp do portalu administracyjnego usługi Azure AD | Ustawienie tej opcji na Nie umożliwia nie-administratorom korzystanie z portalu administracyjnego usługi Azure AD do odczytu zasobów usługi Azure AD i zarządzania nimi. Tak ogranicza dostęp wszystkich administratorów do dostępu do jakichkolwiek danych usługi Azure AD w portalu administracyjnym. Ważne jest, aby pamiętać: to ustawienie nie ogranicza dostępu do danych usługi Azure AD przy użyciu programu PowerShell lub innych klientów, takich jak Visual Studio. Po ustawieniu na Tak, aby udzielić określonemu użytkownikowi niebędącemu administratorem możliwość korzystania z portalu administracyjnego usługi Azure AD przypisać dowolną rolę administracyjną, taką jak rola czytniki katalogów. Ta rola umożliwia odczytywanie podstawowych informacji katalogowych, które użytkownicy członkowi mają domyślnie (goście i jednostki usługi nie).
Możliwość odczytywania innych użytkowników | To ustawienie jest dostępne tylko w programie PowerShell. Ustawienie tej flagi na $false uniemożliwia wszystkim administratorom odczytywanie informacji o użytkowniku z katalogu. Ta flaga nie uniemożliwia odczytywania informacji o użytkowniku w innych usługach firmy Microsoft, takich jak Exchange Online. To ustawienie jest przeznaczone dla szczególnych okoliczności i ustawienie tej flagi na $false nie jest zalecane.

## <a name="object-ownership"></a>Własność obiektu

### <a name="application-registration-owner-permissions"></a>Uprawnienia właściciela przy rejestracji aplikacji
Gdy użytkownik rejestruje aplikację, jest on automatycznie dodawany jako właściciel aplikacji. Jako właściciel użytkownik może zarządzać metadanymi aplikacji, takimi jak nazwa i uprawnienia żądane przez aplikację. Właściciele mogą również zarządzać konfiguracją aplikacji specyficzną dla dzierżawy. Może to być na przykład konfiguracja logowania jednokrotnego i przypisania użytkownika. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko posiadanymi przez siebie aplikacjami.

### <a name="enterprise-application-owner-permissions"></a>Uprawnienia właściciela aplikacji przedsiębiorstwa
Gdy użytkownik doda nową aplikację dla przedsiębiorstwa, są one automatycznie dodawane jako właściciel. Jako właściciel mogą zarządzać konfiguracją aplikacji specyficzną dla dzierżawy, taką jak konfiguracja usługi SSO, inicjowanie obsługi administracyjnej i przypisania użytkowników. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko posiadanych przez siebie aplikacjami.

### <a name="group-owner-permissions"></a>Uprawnienia właściciela grupy
Gdy użytkownik tworzy grupę, jest automatycznie dodawany jako właściciel tej grupy. Jako właściciel mogą zarządzać właściwościami grupy, takimi jak nazwa, a także zarządzać członkostwem w grupie. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych i administratorów użytkowników właściciele mogą zarządzać tylko grupami, których są właścicielami. Aby dowiedzieć się, jak przypisać właściciela grupy, zobacz [Managing owners for a group](active-directory-accessmanagement-managing-group-owners.md) (Zarządzanie właścicielami grupy).

### <a name="ownership-permissions"></a>Uprawnienia własności
W poniższych tabelach opisano określone uprawnienia użytkowników elementów członkowskich usługi Azure Active Directory nad własnością obiektów. Użytkownik ma tylko te uprawnienia do obiektów, które są własnością.

#### <a name="owned-application-registrations"></a>Posiadane rejestracje aplikacji
Użytkownicy mogą wykonywać następujące akcje w zakresie rejestracji aplikacji należących do użytkownika.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/applications/audience/update | Aktualizuj właściwość applications.audience w usłudze Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aktualizuj właściwość applications.authentication w usłudze Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Aktualizuj właściwość applications.credentials w usłudze Azure Active Directory. |
| microsoft.directory/applications/delete | Usuwanie aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/owners/update | Aktualizuj właściwość applications.owners w usłudze Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Aktualizuj właściwość applications.permissions w usłudze Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.directory/applications/restore | Przywracanie aplikacji w usłudze Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Posiadane aplikacje dla przedsiębiorstw
Użytkownicy mogą wykonywać następujące akcje w posiadanych aplikacjach korporacyjnych. Aplikacja przedsiębiorstwa składa się z jednostki usługi, co najmniej jednej zasady aplikacji, a czasami obiektu aplikacji w tej samej dzierżawie jako jednostki usługi.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w auditLogs w usłudze Azure Active Directory. |
| microsoft.directory/policies/basic/update | Aktualizowanie podstawowych właściwości zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/owners/update | Aktualizuj właściwość policies.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizacja właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Zaktualizuj właściwość servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Zaktualizuj właściwość servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aktualizowanie podstawowych właściwości w usługachPrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Zaktualizuj właściwość servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Usuń servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Zaktualizuj właściwość servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w signInReports w usłudze Azure Active Directory. |

#### <a name="owned-devices"></a>Posiadane urządzenia
Użytkownicy mogą wykonywać następujące akcje na posiadanych urządzeniach.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Odczytuj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.directory/devices/disable | Wyłączanie urządzeń w usłudze Azure Active Directory. |

#### <a name="owned-groups"></a>Posiadane grupy
Użytkownicy mogą wykonywać następujące akcje w grupach należących do grupy.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Aktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aktualizowanie podstawowych właściwości w grupach w usłudze Azure Active Directory. |
| microsoft.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Aktualizuj właściwość groups.dynamicMembershipRule w usłudze Azure Active Directory. |
| microsoft.directory/groups/members/update | Aktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.directory/groups/restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aktualizuj właściwość groups.settings w usłudze Azure Active Directory. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przypisywaniu ról administratora usługi Azure AD, zobacz [Przypisywanie ról administratora użytkownika do administratora w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Zarządzaj użytkownikami](add-users-azure-active-directory.md)
