---
title: Jakie są domyślne uprawnienia użytkowników usługi Azure Active Directory? | Microsoft Docs
description: Dowiedz się więcej o uprawnieniach inny użytkownik, dostępne w usłudze Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: lizross
ms.reviewer: vincesm
custom: it-pro
ms.openlocfilehash: 9a9bd33dce4b2be9e15df2745dd25338cdb42702
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730483"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Jakie są domyślne uprawnienia użytkowników usługi Azure Active Directory?

W usłudze Azure Active Directory (Azure AD) wszystkim użytkownikom jest udzielany zestaw uprawnień domyślnych. Na dostęp użytkownika mają wpływ typ użytkownika, [członkostwa w ramach roli](https://docs.microsoft.com/azure/active-directory/active-directory-users-assign-role-azure-portal) i prawa własności do poszczególnych obiektów. W tym artykule opisano te uprawnienia domyślne oraz zawarto porównanie uprawnień domyślnych członka i użytkownika gościa.

## <a name="member-and-guest-users"></a>Użytkownicy będący członkami i gośćmi
Zestaw otrzymanych domyślnych uprawnień zależy od tego, czy użytkownik jest natywnym członkiem dzierżawy (użytkownikiem będącym członkiem), czy gościem współpracy B2B (użytkownikiem gościem). Aby uzyskać więcej informacji na temat współpracy B2B i użytkowników gości, zobacz [Czym jest współpraca B2B w usłudze Azure AD?](../b2b/what-is-b2b.md). 
* Użytkownicy będący członkami mogą rejestrować aplikacje, zmieniać swoje zdjęcie profilowe, numer telefonu komórkowego oraz hasło. Mogą też zapraszać gości B2B. Dodatkowo użytkownicy mogą odczytywać wszystkie informacje o katalogach (z kilkoma wyjątkami). 
* Użytkownicy goście B2B usługi Azure AD mają ograniczone uprawnienia do katalogu. Goście nie mogą na przykład przeglądać informacji o dzierżawie wykraczających poza informacje z własnego profilu. Jednak użytkownik gość może pobierać informacje o innym użytkowniku po podaniu głównej nazwy użytkownika lub identyfikatora objectId. Gość nie może wyświetlać informacji o innych obiektach dzierżawy, takich jak grupy i aplikacje.

Uprawnienia domyślne dla gości są restrykcyjne. Gości można dodawać do ról administratora, dzięki czemu uzyskają oni pełne uprawnienia do odczytu i zapisu zawarte w roli. Dla gości jest dostępne jeszcze jedno ograniczenie — możliwość zapraszania innych gości. Ustawienie opcji **Goście mogą zapraszać gości** na wartość **Nie** uniemożliwia gościom zapraszanie innych gości. Aby dowiedzieć się, jak ustawiać tę opcję, zobacz [Delegate invitations for B2B collaboration](../b2b/delegate-invitations.md) (Delegowanie zaproszeń na potrzeby współpracy B2B). Aby domyślnie użytkownikom gościom były przyznawane takie same uprawnienia jak użytkownikom będącym członkami, ustaw opcję **Uprawnienia użytkowników-gości są ograniczone** na wartość **Nie**. To ustawienie domyślnie przyznaje gościom wszystkie uprawnienia użytkowników będących członkami, a także umożliwia dodawanie gości do ról administracyjnych.

## <a name="compare-member-and-guest-default-permissions"></a>Porównanie uprawnień domyślnych członka i gościa

**Obszar** | **Uprawnienia użytkownika będącego członkiem** | **Uprawnienia użytkownika gościa**
------------ | --------- | ----------
Użytkownicy i kontakty | Odczytywanie wszystkich publicznych właściwości użytkowników i kontaktów<br>Zapraszanie gości<br>Zmiana własnego hasła<br>Zarządzanie własnym numerem telefonu komórkowego<br>Zarządzanie własnym zdjęciem<br>Unieważnianie własnych tokenów odświeżania | Odczytywanie własnych właściwości<br>Odczytywanie nazwy wyświetlanej, adresu e-mail, nazwy logowania, zdjęcia, głównej nazwy użytkownika i właściwości typu użytkownika innych użytkowników i kontaktów<br>Zmiana własnego hasła
Grupy | Tworzenie grup zabezpieczeń<br>Tworzenie grup usługi Office 365<br>Odczytywanie wszystkich właściwości grup<br>Odczytywanie nieukrytych członkostw w grupach<br>Odczytywanie ukrytych członkostw w grupach usługi Office 365 dla dołączonej grupy<br>Zarządzanie właściwościami, własnością i członkostwem w posiadanych grupach<br>Dodawanie gości do posiadanych grup<br>Zarządzanie dynamicznymi ustawieniami członkostwa<br>Usuwanie posiadanych grup<br>Przywracanie posiadanych grup usługi Office 365 | Odczytywanie wszystkich właściwości grup<br>Odczytywanie nieukrytych członkostw w grupach<br>Odczytywanie ukrytych członkostw w grupach usługi Office 365 dla dołączonych grup<br>Zarządzanie posiadanymi grupami<br>Dodawanie gości do posiadanych grup (jeśli jest to dozwolone)<br>Usuwanie posiadanych grup<br>Przywracanie posiadanych grup usługi Office 365 
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
Możliwość tworzenia grup zabezpieczeń | Ustawienie tej opcji na wartość Nie uniemożliwia użytkownikom tworzenie grup zabezpieczeń. Administratorzy globalni i administratorzy kont użytkowników mogą nadal tworzyć grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md).
Możliwość tworzenia grup usługi Office 365 | Ustawienie tej opcji na wartość Nie uniemożliwia użytkownikom tworzenie grup usługi Office 365. Ustawienie tej opcji na wartość Niektóre umożliwia wybranemu zestawowi użytkowników tworzenie grup usługi Office 365. Administratorzy globalni i administratorzy kont użytkowników będą nadal mogli tworzyć grupy usługi Office 365. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md).
Ogranicz dostęp do portalu administracyjnego usługi Azure AD | Ustawienie tej opcji nie uniemożliwia użytkownikom uzyskiwanie dostępu do usługi Azure Active Directory.
Możliwość odczytywania innych użytkowników | To ustawienie jest dostępne tylko w programie PowerShell. Ustawienie tej opcji na wartość $false uniemożliwia użytkownikom nieadministracyjnym odczytywanie informacji o użytkowniku z katalogu. Nie zapobiega to jednak odczytywaniu informacji o użytkowniku w innych usługach firmy Microsoft, takich jak Exchange Online. To ustawienie powinno być używane tylko w specjalnych okolicznościach i ustawianie go na wartość $false nie jest zalecane.

## <a name="object-ownership"></a>Własność obiektu

### <a name="application-registration-owner-permissions"></a>Uprawnienia właściciela przy rejestracji aplikacji
Gdy użytkownik rejestruje aplikację, jest on automatycznie dodawany jako właściciel aplikacji. Jako właściciel użytkownik może zarządzać metadanymi aplikacji, takimi jak nazwa i uprawnienia żądane przez aplikację. Właściciele mogą również zarządzać konfiguracją aplikacji specyficzną dla dzierżawy. Może to być na przykład konfiguracja logowania jednokrotnego i przypisania użytkownika. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko posiadanymi przez siebie aplikacjami.

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>Uprawnienia właściciela grupy

Gdy użytkownik tworzy grupę, jest automatycznie dodawany jako właściciel tej grupy. Właściciel może zarządzać właściwościami grupy, takimi jak nazwa, a także zarządzać członkostwem. Właściciel może również dodawać i usuwać innych właścicieli. W odróżnieniu od administratorów globalnych i administratorów kont użytkowników właściciele mogą zarządzać tylko posiadanymi przez siebie grupami. Aby dowiedzieć się, jak przypisać właściciela grupy, zobacz [Managing owners for a group](active-directory-accessmanagement-managing-group-owners.md) (Zarządzanie właścicielami grupy).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat sposobu przypisywania ról administratora usługi Azure AD, zobacz [przypisać użytkownika do ról administratora w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Zarządzanie użytkownikami](add-users-azure-active-directory.md)
