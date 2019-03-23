---
title: Delegowanie najniższych uprawnieniach ról przez administratora zadań — Azure Active Directory | Dokumentacja firmy Microsoft
description: Role do delegowania zadań tożsamości w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59c06ae83327683942885190e4b401617dc020f9
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361374"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Role administratorów przez zadanie administratora w usłudze Azure Active Directory

W tym artykule znajdziesz informacje wymagane do ograniczenia uprawnień administratora, przypisując najniższych uprawnieniach ról w usłudze Azure Active Directory (Azure AD). Można znaleźć zadania administratora uporządkowane według obszaru funkcji oraz rolę najniższych uprawnieniach wymaganych do wykonania każdego zadania oraz role dodatkowe bez uprawnień globalnego administratora, które można wykonać zadanie.

## <a name="application-proxy"></a>Serwer proxy aplikacji

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie aplikacji serwera proxy aplikacji | Administrator aplikacji | 
Skonfigurować właściwości grupy łączników | Administrator aplikacji | 
Utwórz rejestrację aplikacji, gdy możliwości jest wyłączony dla wszystkich użytkowników | Deweloper aplikacji | Administrator aplikacji w chmurze, Administrator aplikacji
Tworzenie grupy łączników | Administrator aplikacji | 
Usuwanie grupy łączników | Administrator aplikacji | 
Wyłączanie serwera proxy aplikacji | Administrator aplikacji | 
Pobierz usługę łącznika | Administrator aplikacji | 
Przeczytaj całą konfigurację | Administrator aplikacji | 

## <a name="b2c"></a>B2C

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Tworzenie katalogów usługi Azure AD B2C | Wszyscy użytkownicy innego niż Gość ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Tworzenie aplikacji B2C | Administrator globalny | 
Tworzenie aplikacji dla przedsiębiorstw | Administrator aplikacji w chmurze | Administrator aplikacji
Tworzenie, Odczyt, aktualizowanie i usuwanie zasady usługi B2C | Administrator globalny | 
Tworzenie, Odczyt, aktualizowanie i usuwanie dostawcy tożsamości | Administrator globalny | 
Tworzenie, Odczyt, aktualizowanie i usuwanie przepływy użytkownika resetowania hasła | Administrator globalny | 
Tworzenie, Odczyt, aktualizowanie i usuwanie profilu edycji przepływy użytkownika | Administrator globalny | 
Tworzenia, odczytu, aktualizacji i usuwania przepływów użytkownika logowania | Administrator globalny | 
Tworzenie, Odczyt, aktualizowanie i usuwanie przepływu użytkownika rejestracji |Administrator globalny | 
Tworzenie, Odczyt, aktualizowanie i usuwanie atrybutów użytkownika | Administrator globalny | 
Tworzenie, Odczyt, aktualizowanie i usuwanie użytkowników | Administrator globalny ([dokumentacji](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Przeczytaj całą konfigurację | Administrator globalny | 
Dzienniki inspekcji B2C odczytu | Administrator globalny ([dokumentacji](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

## <a name="company-branding"></a>Znakowanie firmowe

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie oznaczenia marką firmy | Administrator globalny | 
Przeczytaj całą konfigurację | Odczytywanie katalogów | Domyślna rola użytkownika ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Właściwości firmy

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie właściwości firmy | Administrator globalny | 

## <a name="connect"></a>Połączenie

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Uwierzytelnianie przekazujących | Administrator globalny | 
Przeczytaj całą konfigurację | Administrator globalny | 
Bezproblemowe logowanie jednokrotne | Administrator globalny | 

## <a name="connect-health"></a>Connect Health

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Dodawanie lub usuwanie usługi | Właściciel ([dokumentacji](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Zastosować poprawki do błąd synchronizacji | Współautor ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Właściciel
Konfigurowanie powiadomień | Współautor ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Właściciel
Konfigurowanie ustawień | Właściciel ([dokumentacji](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Konfigurowanie powiadomień synchronizacji | Współautor ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Właściciel
Raporty dotyczące zabezpieczeń AD FS odczytu | Odczytywanie zabezpieczeń | Współautor, właściciel
Przeczytaj całą konfigurację | Czytnik ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Współautor, właściciel
Błędy synchronizacji odczytu | Czytnik ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Współautor, właściciel
Usługi synchronizacji odczytu | Czytnik ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Współautor, właściciel
Wyświetl metryki i alerty | Czytnik ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Współautor, właściciel
Wyświetl metryki i alerty | Czytnik ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Współautor, właściciel
Wyświetl metryki usługi synchronizacji i alerty | Czytnik ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Współautor, właściciel


## <a name="custom-domain-names"></a>Niestandardowe nazwy domen

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Zarządzanie domenami | Administrator globalny | 
Przeczytaj całą konfigurację | Odczytywanie katalogów | Domyślna rola użytkownika ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Domain Services

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Tworzenie wystąpienia usługi Azure AD Domain Services | Administrator globalny | 
Wykonywanie wszystkich zadań usługi Azure AD Domain Services | Grupy Administratorzy kontrolera domeny usługi AD Azure ([dokumentacji](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-administer-domain#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Przeczytaj całą konfigurację | Czytnika w subskrypcji platformy Azure zawierającej usługi AD DS | 

## <a name="devices"></a>Urządzenia

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Wyłączanie urządzenia | Administrator urządzenia w chmurze | 
Włączanie urządzenia | Administrator urządzenia w chmurze | 
Odczyt konfiguracji podstawowej | Domyślna rola użytkownika ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Klucze funkcji BitLocker odczytu | Odczytywanie zabezpieczeń | Hasło administratora, administrator zabezpieczeń

## <a name="enterprise-applications"></a>Aplikacje dla przedsiębiorstw

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Zgoda na wszelkie uprawnienia delegowane | Administrator aplikacji w chmurze | Administrator aplikacji
Wyrazić zgodę na uprawnienia aplikacji, nie wliczając Microsoft Graph or Azure AD Graph | Administrator aplikacji w chmurze | Administrator aplikacji
Wyrazić zgodę na uprawnienia aplikacji do programu Microsoft Graph lub programu Azure AD Graph | Administrator globalny | 
Wyrazić zgodę na uzyskiwanie dostępu do danych własne aplikacje | Domyślna rola użytkownika ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Tworzenie aplikacji dla przedsiębiorstw | Administrator aplikacji w chmurze | Administrator aplikacji
Zarządzaj serwera Proxy aplikacji | Administrator aplikacji | 
Zarządzaj ustawieniami użytkownika | Administrator globalny | 
Przejrzyj dostęp do odczytu grupę lub aplikację | Odczytywanie zabezpieczeń | Administrator zabezpieczeń, Administrator użytkowników
Przeczytaj całą konfigurację | Domyślna rola użytkownika ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Aktualizowanie przypisań aplikacji przedsiębiorstwa | Właściciel aplikacji przedsiębiorstwa ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizuj właścicieli aplikacji przedsiębiorstwa | Właściciel aplikacji przedsiębiorstwa ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizacja właściwości aplikacji przedsiębiorstwa | Właściciel aplikacji przedsiębiorstwa ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizuj aprowizacja aplikacji przedsiębiorstwa | Właściciel aplikacji przedsiębiorstwa ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizuj aplikację przedsiębiorstwa samoobsługi | Właściciel aplikacji przedsiębiorstwa ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizowanie właściwości rejestracji jednokrotnej | Właściciel aplikacji przedsiębiorstwa ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrator aplikacji w chmurze, administrator aplikacji

## <a name="groups"></a>Grupy

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Przypisywanie licencji | Administrator użytkownika | 
Utwórz grupę | Administrator użytkownika | 
Tworzenie, aktualizowanie lub usuwanie przeglądu dostępu grupy lub aplikacji | Administrator użytkownika | 
Zarządzanie wygasaniem grupy | Administrator użytkownika | 
Zarządzenie ustawieniami grup | Administrator globalny | 
Przeczytaj całą konfigurację (z wyjątkiem ukrytych członkostwa) | Odczytywanie katalogów | Domyślna rola użytkownika ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Odczytuj informacje o ukrytym członkostwie | Członek grupy | Właściciel grupy, hasło administratora, administrator programu Exchange, administrator programu SharePoint, zespoły administratora, administrator użytkowników
Przeczytaj członkostwa w grupach z ukrytym członkostwie | Administrator pomocy technicznej | Administrator użytkowników, administrator zespołów
Odwoływanie licencji | Administrator licencji | Administrator użytkownika
Zaktualizuj członkostwo w grupie | Właściciel grupy ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrator użytkownika
Właściciele grupy aktualizacji | Właściciel grupy ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrator użytkownika
Właściwości grupy aktualizacji | Właściciel grupy ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrator użytkownika

## <a name="identity-protection"></a>Identity Protection

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie powiadomień o alertach| Administrator zabezpieczeń | 
Konfigurowanie i włączanie lub wyłączanie zasad MFA| Administrator zabezpieczeń | 
Konfigurowanie i włączanie lub wyłączanie zasad ryzyka logowania| Administrator zabezpieczeń | 
Konfigurowanie i włączanie lub wyłączanie zasad ryzyka dla użytkownika | Administrator zabezpieczeń | 
Konfigurowanie skróty co tydzień | Administrator zabezpieczeń| 
Odrzuć wszystkie zdarzenia o podwyższonym ryzyku | Administrator zabezpieczeń | 
Napraw lub odrzucić luk w zabezpieczeniach | Administrator zabezpieczeń | 
Przeczytaj całą konfigurację | Odczytywanie zabezpieczeń | 
Odczyt wszystkich zdarzeń o podwyższonym ryzyku | Odczytywanie zabezpieczeń | 
Przeczytaj luk w zabezpieczeniach | Odczytywanie zabezpieczeń | 

## <a name="licenses"></a>Licencje

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Przypisywanie licencji | Administrator licencji | Administrator użytkownika
Przeczytaj całą konfigurację | Odczytywanie katalogów | Domyślna rola użytkownika ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Odwoływanie licencji | Administrator licencji | Administrator użytkownika
Wypróbuj i Kup subskrypcję | Administrator rozliczeń | 


## <a name="monitoring---audit-logs"></a>Monitorowanie — dzienniki inspekcji

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Odczytaj dzienniki inspekcji | Czytnik raportów | Czytelnik zabezpieczeń, administratora zabezpieczeń

## <a name="monitoring---sign-ins"></a>Monitorowanie — logowania

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Odczytywania dzienników logowania | Czytnik raportów | Czytelnik zabezpieczeń, administratora zabezpieczeń

## <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników | Administrator globalny | 
Wyłączanie usługi MFA | Administrator globalny | 
Włączanie usługi MFA | Administrator globalny | 
Zarządzaj ustawieniami usługi MFA | Administrator globalny | 
Wymagaj od wybranych użytkowników ponownego podania metod kontaktu | Administrator uwierzytelniania | 
Przywróć uwierzytelnianie wieloskładnikowe na wszystkich zapamiętanych urządzeniach  | Administrator uwierzytelniania | 

## <a name="mfa-server"></a>Serwer MFA

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Zablokuj/odblokuj użytkowników | Administrator globalny | 
Konfigurowanie blokady konta | Administrator globalny | 
Skonfiguruj reguły buforowania | Administrator globalny | 
Skonfiguruj alert oszustwa | Administrator globalny
Konfigurowanie powiadomień | Administrator globalny | 
Konfigurowanie jednorazowe obejście | Administrator globalny | 
Konfigurowanie ustawień połączenia telefonicznego | Administrator globalny | 
Konfigurowanie dostawców | Administrator globalny | 
Konfigurowanie ustawień serwera | Administrator globalny | 
Przeczytaj raport aktywności | Administrator globalny | 
Przeczytaj całą konfigurację | Administrator globalny | 
Odczytaj stan serwera | Administrator globalny |  

## <a name="organizational-relationships"></a>Relacje w organizacji

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Zarządzaj dostawcami tożsamości | Administrator globalny | 
Zarządzanie ustawieniami | Administrator globalny | 
Zarządzaj warunkami użytkowania | Administrator globalny | 
Przeczytaj całą konfigurację | Administrator globalny | 

## <a name="password-reset"></a>Resetowanie hasła

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie metod uwierzytelniania | Administrator globalny |
Konfigurowanie dostosowywania | Administrator globalny |
Konfigurowanie powiadomień | Administrator globalny |
Konfigurowanie integracji środowiska lokalnego | Administrator globalny |
Konfigurowanie właściwości resetowania hasła | Administrator użytkowników | Administrator globalny
Konfigurowanie rejestracji | Administrator globalny |
Przeczytaj całą konfigurację | Administrator zabezpieczeń | Administrator użytkowników |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Przypisywanie użytkowników do ról | Administrator roli uprzywilejowanej | 
Konfigurowanie ustawień roli | Administrator roli uprzywilejowanej | 
Wyświetl działania inspekcji | Czytelnik zabezpieczeń | 
Przeglądanie członkostwa w roli | Czytelnik zabezpieczeń | 

## <a name="roles-and-administrators"></a>Role i administratorzy

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Zarządzanie przypisaniami ról | Administrator roli uprzywilejowanej | 
Przegląd dostępu do odczytu roli usługi Azure AD  | Odczytywanie zabezpieczeń | Administrator zabezpieczeń, administratorem ról uprzywilejowanych
Przeczytaj całą konfigurację | Domyślna rola użytkownika ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Zabezpieczenia — metody uwierzytelniania

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie metod uwierzytelniania | Administrator globalny | 
Przeczytaj całą konfigurację | Administrator globalny | 

## <a name="security---conditional-access"></a>Zabezpieczenia — dostęp warunkowy

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Skonfiguruj usługę MFA zaufanych adresów IP | Administrator dostępu warunkowego | 
Tworzenie niestandardowych formantów | Administrator dostępu warunkowego | Administrator zabezpieczeń
Tworzenie lokalizacji o nazwie | Administrator dostępu warunkowego | Administrator zabezpieczeń
Tworzenie zasad | Administrator dostępu warunkowego | Administrator zabezpieczeń
Tworzenie warunków użytkowania | Administrator dostępu warunkowego | Administrator zabezpieczeń
Utwórz certyfikat z połączenia sieci VPN | Administrator dostępu warunkowego | Administrator zabezpieczeń
Usuwanie zasad klasycznych | Administrator dostępu warunkowego | Administrator zabezpieczeń
Usuwanie warunków użytkowania | Administrator dostępu warunkowego | Administrator zabezpieczeń
Usuń certyfikat połączenia sieci VPN | Administrator dostępu warunkowego | Administrator zabezpieczeń
Wyłączanie zasad klasycznych | Administrator dostępu warunkowego | Administrator zabezpieczeń
Zarządzać kontrolkami niestandardowymi | Administrator dostępu warunkowego | Administrator zabezpieczeń
Zarządzanie nazwane lokalizacje | Administrator dostępu warunkowego | Administrator zabezpieczeń
Zarządzaj warunkami użytkowania | Administrator dostępu warunkowego | Administrator zabezpieczeń
Przeczytaj całą konfigurację | Czytelnik zabezpieczeń | Administrator zabezpieczeń
Lokalizacje z nazwą odczytu | Czytelnik zabezpieczeń | Administrator dostępu warunkowego, administratora zabezpieczeń

## <a name="security---identity-security-score"></a>Zabezpieczenia — ocena zabezpieczeń tożsamości

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role | 
---- | --------------------- | ----------------
Przeczytaj całą konfigurację | Czytelnik zabezpieczeń | Administrator zabezpieczeń
Ocena bezpieczeństwa odczytu | Czytelnik zabezpieczeń | Administrator zabezpieczeń
Aktualizowanie stanu zdarzenia | Administrator zabezpieczeń | 

## <a name="security---risky-sign-ins"></a>Zabezpieczenia — ryzykowne logowania

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Przeczytaj całą konfigurację | Odczytywanie zabezpieczeń | 
Odczyt ryzykowne logowania | Odczytywanie zabezpieczeń | 

## <a name="security---users-flagged-for-risk"></a>Zabezpieczenia — użytkownicy oflagowani w związku z ryzykiem

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Odrzucanie wszystkich zdarzeń. | Administrator zabezpieczeń | 
Przeczytaj całą konfigurację | Odczytywanie zabezpieczeń | 
Przeczytaj użytkowników oznaczonych flagą ryzyka | Odczytywanie zabezpieczeń | 

## <a name="users"></a>Użytkownicy

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Dodawanie użytkownika do roli w katalogu | Administrator roli uprzywilejowanej | 
Dodaj użytkownika do grupy | Administrator użytkownika | 
Przypisywanie licencji | Administrator licencji | Administrator użytkownika
Tworzenie użytkownika-gościa | Osoba zapraszająca gości | Administrator użytkownika
Tworzenie użytkownika | Administrator użytkownika | 
Usuwanie użytkowników | Administrator użytkownika | 
Unieważnienie tokenów odświeżania z administratorów z ograniczonymi uprawnieniami (patrz dokumentacja) | Administrator użytkownika | 
Unieważnienie tokenów odświeżania dla użytkowników innych niż administratorzy (w dokumentacji) | Administrator haseł | Administrator użytkownika
Unieważnienie tokenów odświeżania uprzywilejowanych administratorów (w dokumentacji) | Administrator globalny | 
Odczyt konfiguracji podstawowej | Domyślna rola użytkownika ([dokumentacji](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Resetowanie hasła dla administratorów z ograniczonymi uprawnieniami (patrz dokumentacja) | Administrator użytkownika | 
Resetowanie hasła użytkowników innych niż administratorzy (w dokumentacji) | Administrator haseł | Administrator użytkownika
Resetuj hasło uprzywilejowanych administratorów | Administrator globalny | 
Odwoływanie licencji | Administrator licencji | Administrator użytkownika
Aktualizuj wszystkie właściwości, z wyjątkiem główna nazwa użytkownika | Administrator użytkownika | 
Zaktualizuj główną nazwę użytkownika dla administratorów z ograniczonymi uprawnieniami (patrz dokumentacja) | Administrator użytkownika | 
Zaktualizuj właściwość główną nazwę użytkownika w uprzywilejowanych administratorów (w dokumentacji) | Administrator globalny | 
Aktualizowanie ustawień użytkownika | Administrator globalny | 


## <a name="support"></a>Pomoc techniczna

Zadanie | Najniższych uprawnieniach roli | Dodatkowe role
---- | --------------------- | ----------------
Prześlij bilet pomocy technicznej | Administrator usługi | Administrator aplikacji, rozliczenia, Administrator, Administrator aplikacji w chmurze, Administrator do spraw zgodności, Dynamics 365 administratora, Administrator Analytics pulpitu, Administrator programu Exchange, Administrator haseł, Information Protection Administrator, Administrator usługi Intune, usługi Skype dla administratora firmy, Power BI Administrator, uprzywilejowanego uwierzytelniania administratora, Administrator programu SharePoint, administratora komunikacji zespołów, zespoły administratora, Administrator użytkowników Administrator usługi Analytics w miejscu pracy

## <a name="next-steps"></a>Kolejne kroki

* [Jak przypisać lub usunąć role administratora usługi azure AD](directory-manage-roles-portal.md)
* [Odwołanie role administratora usługi Azure AD](directory-assign-admin-roles.md)
