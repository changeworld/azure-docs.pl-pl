---
title: Typowe scenariusze w zarządzaniu uprawnieniami — usługa Azure AD
description: Zapoznaj się z krokami wysokiego poziomu, które należy wykonać w przypadku typowych scenariuszy w zarządzaniu uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190555"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Typowe scenariusze w zarządzaniu uprawnieniami usługi Azure AD

Istnieje kilka sposobów konfigurowania zarządzania uprawnieniami dla organizacji. Jeśli jednak dopiero zaczynasz, warto zapoznać się z typowymi scenariuszami dla administratorów, właścicieli katalogów, menedżerów pakietów dostępu, osób zatwierdzających i żądających.

## <a name="delegate"></a>Delegate

### <a name="administrator-delegate-management-of-resources"></a>Administrator: Delegowanie zarządzania zasobami

1. [Obejrzyj wideo: Delegowanie z DZIAŁU IT do kierownika działu](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegowanie użytkowników do roli twórcy katalogu](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Twórca katalogu: delegowanie zarządzania zasobami

- [Tworzenie nowego katalogu](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Właściciel katalogu: delegowanie zarządzania zasobami

1. [Dodawanie współwłaścicieli do katalogu](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Dodawanie zasobów do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Właściciel katalogu: delegowanie zarządzania pakietami dostępu

1. [Obejrzyj wideo: Delegowanie od właściciela katalogu do menedżera pakietów dostępu](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegowanie użytkowników do roli menedżera pakietów](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Zarządzanie dostępem dla użytkowników w organizacji

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Menedżer pakietów dostępu: zezwalaj pracownikom w organizacji na żądanie dostępu do zasobów

1. [Tworzenie nowego pakietu dostępu](entitlement-management-access-package-create.md#start-new-access-package)
1. [Dodawanie grup, zespołów, aplikacji lub witryn programu SharePoint w celu uzyskania dostępu do pakietu](entitlement-management-access-package-create.md#resource-roles)
1. [Dodawanie zasad żądania umożliwiających użytkownikom w katalogu żądanie dostępu](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Określanie ustawień wygaśnięcia](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Żądacz: Żądanie dostępu do zasobów

1. [Logowanie się do portalu Mój dostęp](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Znajdź pakiet dostępu
1. [Żądanie dostępu](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Osoba zatwierdzająca: zatwierdzanie żądań do zasobów

1. [Otwórz żądanie w portalu Mój dostęp](entitlement-management-request-approve.md#open-request)
1. [Zatwierdzanie lub odmawianie żądania dostępu](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Requestor: Wyświetl zasoby, do których masz już dostęp

1. [Logowanie się do portalu Mój dostęp](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Wyświetlanie aktywnych pakietów dostępu

## <a name="govern-access-for-users-outside-your-organization"></a>Zarządzanie dostępem dla użytkowników spoza organizacji

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administrator: współpraca z zewnętrzną organizacją partnerską

1. [Przeczytaj, jak działa dostęp dla użytkowników zewnętrznych](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Przeglądanie ustawień dla użytkowników zewnętrznych](entitlement-management-external-users.md#settings-for-external-users)
1. [Dodawanie połączenia z organizacją zewnętrzną](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Menedżer pakietów dostępu: współpraca z zewnętrzną organizacją partnerską

1. [Tworzenie nowego pakietu dostępu](entitlement-management-access-package-create.md#start-new-access-package)
1. [Dodawanie grup, zespołów, aplikacji lub witryn programu SharePoint w celu uzyskania dostępu do pakietu](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Dodawanie zasad żądania, aby umożliwić użytkownikom niew katalogu żądanie dostępu](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Określanie ustawień wygaśnięcia](entitlement-management-access-package-create.md#lifecycle)
1. [Skopiuj łącze, aby zażądać pakietu dostępu](entitlement-management-access-package-settings.md)
1. Wyślij link do partnera kontaktowego partnera zewnętrznego, aby udostępnić je swoim użytkownikom

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Żądacz: Żądaj dostępu do zasobów jako użytkownik zewnętrzny

1. Znajdź link do pakietu dostępu otrzymany od kontaktu
1. [Logowanie się do portalu Mój dostęp](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Żądanie dostępu](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Osoba zatwierdzająca: zatwierdzanie żądań do zasobów

1. [Otwórz żądanie w portalu Mój dostęp](entitlement-management-request-approve.md#open-request)
1. [Zatwierdzanie lub odmawianie żądania dostępu](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Requestor: Wyświetl zasoby, do których masz już dostęp

1. [Logowanie się do portalu Mój dostęp](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Wyświetlanie aktywnych pakietów dostępu

## <a name="day-to-day-management"></a>Codzienne zarządzanie

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie zasobów dla projektu

1. [Obejrzyj wideo: Codzienne zarządzanie: Wszystko się zmieniło](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Otwórz pakiet dostępu
1. [Dodawanie lub usuwanie grup, zespołów, aplikacji lub witryn programu SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie czasu trwania projektu

1. [Obejrzyj wideo: Codzienne zarządzanie: Wszystko się zmieniło](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Otwórz pakiet dostępu
1. [Otwieranie ustawień cyklu życia](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Aktualizowanie ustawień wygaśnięcia](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie sposobu zatwierdzenia dostępu dla projektu

1. [Obejrzyj wideo: Codzienne zarządzanie: Wszystko się zmieniło](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Otwieranie istniejących zasad ustawień żądania i zatwierdzania](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Aktualizowanie ustawień zatwierdzania](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie osób dla projektu

1. [Obejrzyj wideo: Codzienne zarządzanie: Wszystko się zmieniło](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Usuwanie użytkowników, którzy nie potrzebują już dostępu](entitlement-management-access-package-assignments.md)
1. [Otwieranie istniejących zasad ustawień żądania i zatwierdzania](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Dodawanie użytkowników, którzy potrzebują dostępu](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Menedżer pakietów dostępu: bezpośrednie przypisywanie określonych użytkowników do pakietu dostępu

1. [Jeśli użytkownicy potrzebują różnych ustawień cyklu życia, dodaj nową zasadę do pakietu dostępu](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Bezpośrednio przypisz określonych użytkowników do pakietu dostępu](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Przydziały i raporty

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administrator: wyświetlanie, kto ma przypisania do pakietu dostępu

1. Otwieranie pakietu dostępu
1. [Wyświetlanie przypisań](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Archiwizowanie raportów i dzienników](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administrator: Wyświetlanie zasobów przypisanych do użytkowników

1. [Wyświetlanie pakietów dostępu dla użytkownika](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Wyświetlanie przydziałów zasobów dla użytkownika](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Administracja programowa

Za pomocą programu Microsoft Graph można również zarządzać pakietami dostępu, katalogami, zasadami, żądaniami i przydziałami.  Użytkownik w odpowiedniej roli z aplikacją, `EntitlementManagement.ReadWrite.All` która ma delegowane uprawnienie, może wywołać [interfejs API zarządzania uprawnieniami](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

## <a name="next-steps"></a>Następne kroki

- [Delegowanie i role](entitlement-management-delegate.md)
- [Proces żądania i powiadomienia e-mail](entitlement-management-process.md)
