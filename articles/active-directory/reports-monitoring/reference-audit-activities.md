---
title: Informacje o działaniach inspekcji usługi Azure Active Directory (Azure AD) | Microsoft Docs
description: Omówienie działań inspekcji, które mogą być rejestrowane w dziennikach inspekcji usługi Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: aaa60a7737b7781a21e23516d139332f10bdf448
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306012"
---
# <a name="azure-ad-audit-activity-reference"></a>Informacje o działaniach inspekcji usługi Azure AD

Za pomocą raportów usługi Azure Active Directory (Azure AD) możesz uzyskać informacje potrzebne do określenia sposobu działania środowiska.

Architektura raportowania w usłudze Azure AD obejmuje następujące składniki:

- **Raporty dotyczące działań** 
    - [Logowania](concept-sign-ins.md) — zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników
    - [Dzienniki inspekcji](concept-audit-logs.md) — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. 
    
- **Raporty dotyczące zabezpieczeń** 
    - [Ryzykowne logowania](concept-risky-sign-ins.md) — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. 
    - [Użytkownicy oflagowani w związku z ryzykiem](concept-user-at-risk.md) — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. 

W tym artykule wymieniono działania inspekcji, które mogą być rejestrowane w dziennikach inspekcji.

## <a name="access-reviews"></a>Przeglądy dostępu

|Kategorii inspekcji|Działanie|
|---|---|
|Kontrole uprawnień dostępu|Zakończono przegląd dostępu|
|Kontrole uprawnień dostępu|Dodawanie osoby zatwierdzającej do zatwierdzenia żądania|
|Kontrole uprawnień dostępu|Dodawanie recenzenta do przeglądu dostępu|
|Kontrole uprawnień dostępu|Zastosuj przegląd dostępu|
|Kontrole uprawnień dostępu|Utwórz przegląd dostępu|
|Kontrole uprawnień dostępu|Tworzenie programu|
|Kontrole uprawnień dostępu|Tworzenie zatwierdzenia żądania|
|Kontrole uprawnień dostępu|Usuwanie przeglądu dostępu|
|Kontrole uprawnień dostępu|Usuwanie programu|
|Kontrole uprawnień dostępu|Połączenie kontroli programu|
|Kontrole uprawnień dostępu|Dodawanie do przeglądów dostępu usługi Azure AD|
|Kontrole uprawnień dostępu|Usuwanie recenzenta z przeglądu dostępu|
|Kontrole uprawnień dostępu|Żądanie zatrzymania przeglądu|
|Kontrole uprawnień dostępu|Żądaj zastosowania wyniku przeglądu|
|Kontrole uprawnień dostępu|Przeglądanie członkostwa w roli funkcji RBAC|
|Kontrole uprawnień dostępu|Przeglądanie przypisania aplikacji|
|Kontrole uprawnień dostępu|Przeglądanie członkostwa w grupie|
|Kontrole uprawnień dostępu|Przeglądanie żądania zatwierdzenia żądania|
|Kontrole uprawnień dostępu|Odłączanie kontroli programu|
|Kontrole uprawnień dostępu|Aktualizowanie przeglądu dostępu|
|Kontrole uprawnień dostępu|Aktualizowanie stanu dołączania przeglądów usługi Azure AD|
|Kontrole uprawnień dostępu|Aktualizowanie ustawień powiadomień e-mail dla przeglądu dostępu|
|Kontrole uprawnień dostępu|Aktualizuj ustawienie liczby cykli przeglądu dostępu|
|Kontrole uprawnień dostępu|Aktualizowanie ustawienia czasu trwania cyklu przeglądu dostępu w dniach|
|Kontrole uprawnień dostępu|Aktualizuj ustawienie typu zakończenia cyklu przeglądu dostępu|
|Kontrole uprawnień dostępu|Aktualizowanie ustawienia typu cyklu przeglądu dostępu|
|Kontrole uprawnień dostępu|Aktualizuj ustawienia przypomnień dla kontroli uprawnień dostępu|
|Kontrole uprawnień dostępu|Aktualizowanie programu|
|Kontrole uprawnień dostępu|Aktualizowanie zatwierdzenia żądania|
|Kontrole uprawnień dostępu|Użytkownik wyłączony|

## <a name="account-provisioning"></a>Aprowizacja kont

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie aplikacjami|Pobieranie udzieleń uprawnień aplikacji w wersji 2|
|Zarządzanie aplikacjami|Pobieranie jednostek usług aplikacji w wersji 2 w ramach bieżącej dzierżawy|
|Zarządzanie aplikacjami|Aktualizowanie aplikacji w wersji 1|
|Zarządzanie aplikacjami|Aktualizowanie aplikacji w wersji 2|
|Zarządzanie aplikacjami|Aktualizowanie udzielenia uprawnień aplikacji w wersji 2|
|Zarządzanie aplikacjami|Dodawanie elementu OAuth2PermissionGrant|
|Zarządzanie aplikacjami|Dodawanie przypisania roli aplikacji do jednostki usługi|

## <a name="application-proxy"></a>Serwer proxy aplikacji

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie aplikacjami|Dodawanie aplikacji|
|Zarządzanie aplikacjami|Dodawanie właściciela do aplikacji|
|Zarządzanie aplikacjami|Dodawanie właściciela do jednostki usługi|
|Zarządzanie aplikacjami|Dodawanie zasad do jednostki usługi|
|Zarządzanie katalogiem|Dodawanie jednostki usługi|
|Zarządzanie katalogiem|Dodawanie poświadczeń jednostki usługi|
|Zarządzanie katalogiem|Zgoda na aplikację|
|Zarządzanie katalogiem|Usuń aplikację|
|Zarządzanie katalogiem|Całkowite usuwanie aplikacji|
|Zarządzanie katalogiem|Usuwanie elementu OAuth2PermissionGrant|
|Zarządzanie katalogiem|Usuwanie przypisania roli aplikacji z jednostki usługi|
|Zarządzanie katalogiem|Usuwanie właściciela z aplikacji|
|Resource|Usuwanie właściciela z jednostki usługi|
|Resource|Usuwanie zasad z jednostki usługi|
|Resource|Usuwanie jednostki usługi|


## <a name="automated-password-rollover"></a>Automatyczne przenoszenie haseł

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie aplikacjami|Usuwanie poświadczeń jednostki usługi|


## <a name="b2c"></a>B2C

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie aplikacjami|Przywracanie aplikacji|
|Zarządzanie aplikacjami|Wycofywanie zgody|
|Zarządzanie aplikacjami|Aktualizowanie aplikacji|
|Zarządzanie aplikacjami|Aktualizowanie zewnętrznych wpisów tajnych|
|Zarządzanie aplikacjami|Aktualizowanie jednostki usługi|
|Zarządzanie aplikacjami|Wystawianie tokenu dostępu dla aplikacji|
|Zarządzanie aplikacjami|Wystawianie kodu autoryzacji dla aplikacji|
|Zarządzanie aplikacjami|Wystawianie elementu id_token dla aplikacji|
|Zarządzanie aplikacjami|Weryfikacja poświadczeń konta lokalnego|
|Zarządzanie aplikacjami|Weryfikacja uwierzytelniania użytkownika|
|Zarządzanie aplikacjami|Dodawanie uprawnień aplikacji w wersji 2|
|Zarządzanie aplikacjami|Dodawanie klucza opartego na wpisie tajnym w formacie ASCII do kontenera kluczy CPIM|
|Zarządzanie aplikacjami|Dodawanie klucza do kontenera kluczy CPIM|
|Zarządzanie aplikacjami|AdminPolicyDatas-SetResources|
|Zarządzanie aplikacjami|AdminUserJourneys-GetResources|
|Zarządzanie aplikacjami|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|Tworzenie dostawcy IdentityProvider|
|Authentication|Tworzenie aplikacji w wersji 1|
|Authentication|Tworzenie aplikacji w wersji 2|
|Authentication|Tworzenie domen niestandardowych w ramach dzierżawy|
|Authorization|Tworzenie nowego elementu AdminUserJourney|
|Authorization|Tworzenie zlokalizowanego pliku JSON zasobu|
|Authorization|Tworzenie nowego niestandardowego dostawcy tożsamości|
|Authorization|Tworzenie nowego dostawcy tożsamości|
|Authorization|Tworzenie lub aktualizowanie zasobu katalogu B2C|
|Authorization|Utwórz zasady|
|Authorization|Tworzenie zasad trustFramework|
|Authorization|Tworzenie zasad trustFramework przy użyciu konfigurowalnego prefiksu|
|Authorization|Tworzenie atrybutu użytkownika|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|Tworzenie lub aktualizowanie nowego elementu AdminUserJourney|
|Authorization|Usuwanie dostawcy tożsamości|
|Authorization|Usuwanie dostawcy tożsamości|
|Authorization|Usuwanie aplikacji w wersji 1|
|Authorization|Usuwanie aplikacji w wersji 2|
|Authorization|Usuwanie udzielenia uprawnień aplikacji w wersji 2|
|Authorization|Usuwanie zasobu katalogu B2C|
|Authorization|Usuwanie kontenera kluczy CPIM|
|Authorization|Usuwanie zasad trustFramework|
|Authorization|Usuwanie atrybutu użytkownika|
|Authorization|Włączanie funkcji B2C|
|Authorization|Pobieranie zasobów katalogu B2C w ramach subskrypcji|
|Authorization|Pobieranie niestandardowego dostawcy tożsamości|
|Authorization|Pobieranie dostawcy tożsamości|
|Authorization|Pobieranie aplikacji w wersji 1 i 2|
|Authorization|Pobieranie aplikacji w wersji 1|
|Authorization|Pobieranie aplikacji w wersji 1|
|Authorization|Pobieranie aplikacji w wersji 2|
|Authorization|Pobieranie aplikacji w wersji 2|
|Authorization|Pobieranie zasobu katalogu B2C|
|Authorization|Pobieranie listy domen niestandardowych w ramach dzierżawy|
|Authorization|Pobieranie podróży użytkownika|
|Authorization|Pobieranie dozwolonych oświadczeń aplikacji dla podróży użytkownika|
|Authorization|Pobieranie dozwolonych i samodzielnie określonych oświadczeń dla podróży użytkownika|
|Authorization|Pobieranie dozwolonych i samodzielnie określonych oświadczeń zasad|
|Authorization|Pobieranie listy dozwolonych oświadczeń wyjściowych|
|Authorization|Pobieranie definicji zawartości dla podróży użytkownika|
|Authorization|Pobieranie dostawców tożsamości dla określonego przepływu administracyjnego|
|Authorization|Pobieranie metadanych klucza aktywnego kontenera kluczy w formacie JWK|
|Authorization|Pobieranie listy wszystkich przepływów administracyjnych|
|Authorization|Pobieranie listy tagów wszystkich przepływów administracyjnych dla wszystkich użytkowników|
|Authorization|Pobieranie listy dzierżaw dla użytkownika|
|Authorization|Pobieranie samodzielnie określonych oświadczeń kont lokalnych|
|Authorization|Pobieranie zlokalizowanego pliku JSON zasobu|
|Authorization|Pobieranie operacji dostawcy zasobów Microsoft.AzureActiveDirectory|
|Authorization|Pobieranie zasad|
|Authorization|Pobranie zasad|
|Authorization|Pobieranie właściwości zasobów dzierżawy|
|Authorization|Pobieranie listy obsługiwanych dostawców tożsamości|
|Authorization|Pobieranie listy obsługiwanych dostawców tożsamości podróży użytkownika|
|Authorization|Pobieranie informacji o dzierżawie|
|Authorization|Pobieranie dozwolonych funkcji dzierżawy|
|Authorization|Pobranie listy niestandardowych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Authorization|Pobranie listy dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Authorization|Pobranie listy lokalnych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Authorization|Uzyskiwanie szczegółów dzierżawy dla użytkownika na potrzeby tworzenia zasobów|
|Authorization|Pobieranie listy dzierżaw|
|Authorization|Pobieranie domen dzierżaw|
|Authorization|Pobieranie domyślnej obsługiwanej kultury dla elementu CPIM|
|Authorization|Pobieranie szczegółów przepływu administracyjnego|
|Authorization|Pobieranie listy podróży użytkowników dla tej dzierżawy|
|Authorization|Pobieranie zestawu dostępnych i obsługiwanych kultur dla elementu CPIM|
|Authorization|Pobieranie zasad trustFramework|
|Authorization|Pobieranie zasad trustFramework w formacie XML|
|Authorization|Pobieranie atrybutu użytkownika|
|Authorization|Pobieranie atrybutów użytkowników|
|Authorization|Pobieranie listy podróży użytkowników|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|Pobiera kontener kluczy CPIM w formacie JWK|
|Authorization|Pobiera listę kontenerów kluczy w ramach dzierżawy|
|Authorization|Pobiera typ dzierżawy|
|Authorization|MigrateTenantMetadata|
|Authorization|Stosowanie poprawek względem dostawcy tożsamości|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|Usuwanie podróży użytkownika|
|Authorization|Przywracanie kopii zapasowej kontenera kluczy CPIM|
|Authorization|Pobieranie udzieleń uprawnień aplikacji w wersji 2|
|Authorization|Pobieranie jednostek usług aplikacji w wersji 2 w ramach bieżącej dzierżawy|
|Authorization|Aktualizowanie niestandardowego dostawcy tożsamości|
|Authorization|Aktualizowanie dostawcy tożsamości|
|Authorization|Aktualizowanie lokalnego dostawcy tożsamości|
|Authorization|Aktualizowanie aplikacji w wersji 1|
|Authorization|Aktualizowanie aplikacji w wersji 2|
|Authorization|Aktualizowanie udzielenia uprawnień aplikacji w wersji 2|
|Authorization|Aktualizowanie zasad|
|Authorization|Aktualizowanie atrybutu użytkownika|
|Authorization|Przekazywanie zaszyfrowanego klucza CPIM|
|Authorization|Autoryzacja użytkownika: Interfejs API został wyłączony dla dzierżawcy FeatureSet|
|Authorization|Autoryzacja użytkownika: Użytkownik przyznał dostęp jako "Administrator dzierżawy"|
|Authorization|Autoryzacja użytkownika: Użytkownikowi udzielono praw dostępu "uwierzytelnieni użytkownicy"|
|Authorization|Weryfikowanie, czy funkcja B2C jest włączona|
|Authorization|Sprawdź, czy funkcja jest włączona|
|Authorization|Tworzenie programu|
|Authorization|Usuwanie programu|
|Authorization|Połączenie kontroli programu|
|Authorization|Dodawanie do przeglądów dostępu usługi Azure AD|
|Authorization|Odłączanie kontroli programu|
|Authorization|Aktualizowanie programu|
|Authorization|Wyłączanie logowania jednokrotnego dla aplikacji klasycznych|
|Authorization|Wyłączanie logowania jednokrotnego dla aplikacji klasycznych na potrzeby określonej domeny|
|Authorization|Wyłącz serwer proxy aplikacji|
|Authorization|Wyłączanie uwierzytelniania przekazującego|
|Authorization|Włączanie logowania jednokrotnego dla aplikacji klasycznych|
|Zarządzanie katalogiem|Włączanie logowania jednokrotnego dla aplikacji klasycznych dla określonej domeny|
|Zarządzanie katalogiem|Włącz serwer proxy aplikacji|
|Zarządzanie katalogiem|Włączanie uwierzytelniania przekazującego|
|Zarządzanie katalogiem|Tworzenie domen niestandardowych w ramach dzierżawy|
|Zarządzanie katalogiem|Włączanie funkcji B2C|
|Zarządzanie katalogiem|Pobieranie listy domen niestandardowych w ramach dzierżawy|
|Zarządzanie katalogiem|Pobieranie właściwości zasobów dzierżawy|
|Zarządzanie katalogiem|Pobieranie informacji o dzierżawie|
|Zarządzanie katalogami|Pobieranie dozwolonych funkcji dzierżawy|
|Zarządzanie katalogiem|Pobieranie domen dzierżaw|
|Klucz|Pobiera typ dzierżawy|
|Klucz|Weryfikowanie, czy funkcja B2C jest włączona|
|Klucz|Sprawdź, czy funkcja jest włączona|
|Klucz|Dodawanie partnera do firmy|
|Klucz|Dodawanie niezweryfikowanej domeny|
|Klucz|Dodawanie zweryfikowanej domeny|
|Klucz|Tworzenie firmy|
|Klucz|Tworzenie ustawień firmy|
|Klucz|Usuwanie ustawień firmy|
|Klucz|Obniżanie poziomu partnera|
|Klucz|Usunięto katalog|
|Inne|Trwale usunięto katalog|
|Inne|Katalog zaplanowany do usunięcia|
|Resource|Podwyższanie poziomu firmy do partnera|
|Resource|Przeczyszczanie właściwości usługi Rights Management|
|Resource|Usuwanie partnera z firmy|
|Resource|Usuwanie niezweryfikowanej domeny|
|Resource|Usuwanie zweryfikowanej domeny|
|Resource|Ustaw informacje o firmie|
|Resource|Ustawianie funkcji Dirsync|
|Resource|Ustawianie flagi Dirsyncenabled|
|Resource|Ustaw partnerstwo|
|Resource|Ustawianie progu przypadkowego usunięcia|
|Resource|Ustawianie lokalizacji danych dozwolonych przez firmę|
|Resource|Włączanie funkcji międzynarodowej firmy|
|Resource|Ustawianie funkcji katalogu w dzierżawie|
|Resource|Ustawianie uwierzytelniania domeny|
|Resource|Określanie ustawień federacyjnych w domenie|
|Resource|Ustawianie zasad haseł|
|Resource|Ustawianie właściwości usługi Rights Management|
|Resource|Aktualizowanie firmy|
|Resource|Aktualizowanie ustawień firmy|
|Resource|Aktualizacja domeny|
|Resource|Weryfikuj domenę|
|Resource|Sprawdzanie domeny zweryfikowanej za pomocą wiadomości e-mail|
|Resource|Dołączanie|
|Resource|Aktualizowanie ustawień alertów|
|Resource|Aktualizowanie ustawień podsumowania tygodniowego|
|Resource|Wyłączanie funkcji zapisywania zwrotnego haseł dla katalogu|
|Resource|Włączanie funkcji zapisywania zwrotnego haseł dla katalogu|
|Resource|Dodawanie przypisania roli aplikacji do grupy|
|Resource|Dodaj grupę|
|Resource|Dodaj członka do grupy|
|Resource|Dodawanie właściciela do grupy|
|Resource|Tworzenie ustawień grupy|
|Resource|Usuwanie grupy|
|Resource|Usuwanie ustawień grupy|
|Resource|Kończenie stosowania licencji opartej na grupie wobec użytkowników|
|Resource|Całkowite usuwanie grupy|
|Resource|Usuwanie przypisania roli aplikacji z grupy|
|Resource|Usuń członka z grupy|
|Resource|Usuwanie właściciela z grupy|
|Resource|Przywracanie grupy|
|Resource|Ustawianie licencji grupy|
|Resource|Ustawianie grupy do zarządzania przez użytkownika|
|Resource|Rozpoczynanie stosowania licencji opartej na grupie wobec użytkowników|
|Resource|Wyzwalanie ponownego obliczania licencji grupy|
|Resource|Aktualizowanie grupy|
|Resource|Aktualizowanie ustawień grupy|
|Resource|Dodaj członka|
|Resource|Utwórz grupę|
|Resource|Usuń grupę|
|Resource|Usuń członka|
|Resource|Aktualizuj grupę|
|Resource|Zatwierdzanie oczekującego żądania dołączenia do grupy|
|Resource|Anulowanie oczekującego żądania dołączenia do grupy|
|Resource|Tworzenie zasad zarządzania cyklem życia|
|Resource|Usuwanie oczekującego żądania dołączenia do grupy|
|Resource|Odrzucanie oczekującego żądania dołączenia do grupy|
|Resource|Odnów grupę|
|Resource|Żądanie dołączenia do grupy|
|Resource|Ustawianie właściwości grupy dynamicznej|
|Resource|Aktualizowanie zasad zarządzania cyklem życia|
|Resource|Dodawanie klucza opartego na wpisie tajnym w formacie ASCII do kontenera kluczy CPIM|
|Resource|Dodawanie klucza do kontenera kluczy CPIM|
|Resource|Usuwanie kontenera kluczy CPIM|
|Resource|Usuwanie kontenera kluczy|
|Resource|Pobieranie metadanych klucza aktywnego kontenera kluczy w formacie JWK|
|Resource|Pobranie metadanych kontenera kluczy|
|Resource|Pobiera kontener kluczy CPIM w formacie JWK|
|Resource|Pobiera listę kontenerów kluczy w ramach dzierżawy|
|Resource|Przywracanie kopii zapasowej kontenera kluczy CPIM|
|Resource|Zapisywanie kontenera kluczy|
|Resource|Przekazywanie zaszyfrowanego klucza CPIM|
|Resource|Wystawianie kodu autoryzacji dla aplikacji|
|Resource|Wystawianie elementu id_token dla aplikacji|


## <a name="core-directory"></a>Katalog podstawowy

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie jednostkami administracyjnymi|Pobierz typ wykrywania pojedynczego ryzyka|
|Zarządzanie jednostkami administracyjnymi|Pobieranie administratorów i stanu zgody na podsumowanie tygodniowe|
|Zarządzanie jednostkami administracyjnymi|Pobierz wszystkie typy wykrywania ryzyka|
|Zarządzanie jednostkami administracyjnymi|Pobierz bezpłatne wykrycia ryzyka dla użytkowników|
|Zarządzanie jednostkami administracyjnymi|Pobieranie użytkowników oflagowanych w związku z ryzykiem|
|Zarządzanie aplikacjami|Przetworzone zaproszenia zbiorcze|
|Zarządzanie aplikacjami|Przekazane zaproszenia zbiorcze|
|Zarządzanie aplikacjami|Dodawanie właściciela do zasad|
|Zarządzanie aplikacjami|Dodawanie zasad|
|Zarządzanie aplikacjami|Usuwanie zasad|
|Zarządzanie aplikacjami|Usuwanie poświadczeń zasad|
|Zarządzanie aplikacjami|Aktualizowanie zasad|
|Zarządzanie aplikacjami|Ustawianie zasad rejestracji usługi MFA|
|Zarządzanie aplikacjami|Ustawianie zasad dotyczących ryzyka związanego z logowaniem|
|Zarządzanie aplikacjami|Ustawianie zasad dotyczących ryzyka związanego z użytkownikiem|
|Zarządzanie aplikacjami|Akceptowanie warunków użytkowania|
|Zarządzanie aplikacjami|Tworzenie warunków użytkowania|
|Zarządzanie aplikacjami|Odrzucanie warunków użytkowania|
|Zarządzanie aplikacjami|Usuwanie warunków użytkowania|
|Zarządzanie aplikacjami|Edytowanie warunków użytkowania|
|Zarządzanie aplikacjami|Publikowanie warunków użytkowania|
|Zarządzanie aplikacjami|Cofanie publikacji warunków użytkowania|
|Zarządzanie aplikacjami|Dodawanie certyfikatu SSL aplikacji|
|Zarządzanie aplikacjami|Usuwanie powiązania SSL|
|Zarządzanie aplikacjami|Rejestrowanie łącznika|
|Zarządzanie aplikacjami|AdminPolicyDatas-RemoveResources|
|Zarządzanie aplikacjami|AdminPolicyDatas-SetResources|
|Zarządzanie aplikacjami|AdminUserJourneys-GetResources|
|Zarządzanie katalogiem|AdminUserJourneys-RemoveResources|
|Zarządzanie katalogiem|AdminUserJourneys-SetResources|
|Zarządzanie katalogiem|Tworzenie dostawcy IdentityProvider|
|Zarządzanie katalogiem|Tworzenie nowego elementu AdminUserJourney|
|Zarządzanie katalogiem|Tworzenie zlokalizowanego pliku JSON zasobu|
|Zarządzanie katalogiem|Tworzenie nowego niestandardowego dostawcy tożsamości|
|Zarządzanie katalogiem|Tworzenie nowego dostawcy tożsamości|
|Zarządzanie katalogiem|Tworzenie lub aktualizowanie zasobu katalogu B2C|
|Zarządzanie katalogiem|Utwórz zasady|
|Zarządzanie katalogiem|Tworzenie zasad trustFramework|
|Zarządzanie katalogiem|Tworzenie zasad trustFramework przy użyciu konfigurowalnego prefiksu|
|Zarządzanie katalogiem|Tworzenie atrybutu użytkownika|
|Zarządzanie katalogiem|CreateTrustFrameworkPolicy|
|Zarządzanie katalogiem|Usuwanie dostawcy tożsamości|
|Zarządzanie katalogiem|Usuwanie dostawcy tożsamości|
|Zarządzanie katalogiem|Usuwanie zasobu katalogu B2C|
|Zarządzanie katalogiem|Usuwanie zasad trustFramework|
|Zarządzanie katalogiem|Usuwanie atrybutu użytkownika|
|Zarządzanie katalogiem|Pobieranie zasobów katalogu B2C w grupie zasobów|
|Zarządzanie katalogiem|Pobieranie zasobów katalogu B2C w ramach subskrypcji|
|Zarządzanie katalogiem|Pobieranie niestandardowego dostawcy tożsamości|
|Zarządzanie katalogiem|Pobieranie dostawcy tożsamości|
|Zarządzanie katalogiem|Pobieranie zasobu katalogu B2C|
|Zarządzanie katalogiem|Pobieranie podróży użytkownika|
|Zarządzanie katalogiem|Pobieranie dozwolonych oświadczeń aplikacji dla podróży użytkownika|
|Zarządzanie katalogiem|Pobieranie dozwolonych i samodzielnie określonych oświadczeń dla podróży użytkownika|
|Zarządzanie katalogiem|Pobieranie dozwolonych i samodzielnie określonych oświadczeń zasad|
|Zarządzanie katalogiem|Pobieranie listy dozwolonych oświadczeń wyjściowych|
|Zarządzanie katalogiem|Pobieranie definicji zawartości dla podróży użytkownika|
|Zarządzanie katalogiem|Pobieranie dostawców tożsamości dla określonego przepływu administracyjnego|
|Zarządzanie katalogiem|Pobieranie listy wszystkich przepływów administracyjnych|
|Zarządzanie katalogiem|Pobieranie listy tagów wszystkich przepływów administracyjnych dla wszystkich użytkowników|
|Zarządzanie grupami|Członkowie grupy pobierania zbiorczego — uruchomiono|
|Zarządzanie grupami|Członkowie grupy pobierania zbiorczego — zakończono|
|Zarządzanie grupami|Członkowie grupy importu zbiorczego — uruchomiono|
|Zarządzanie grupami|Członkowie grupy importu zbiorczego — zakończono|
|Zarządzanie grupami|Zbiorcze usuwanie członków grupy — rozpoczęto|
|Zarządzanie grupami|Zbiorcze usuwanie członków grupy — zakończono|
|Zarządzanie grupami|Grupy pobierania zbiorczego — rozpoczęte|
|Zarządzanie grupami|Grupy pobierania zbiorczego — zakończono|
|Zarządzanie grupami|Pobieranie listy dzierżaw dla użytkownika|
|Zarządzanie grupami|Pobieranie samodzielnie określonych oświadczeń kont lokalnych|
|Zarządzanie grupami|Pobieranie zlokalizowanego pliku JSON zasobu|
|Zarządzanie grupami|Pobieranie operacji dostawcy zasobów Microsoft.AzureActiveDirectory|
|Zarządzanie grupami|Pobieranie zasad|
|Zarządzanie grupami|Pobranie zasad|
|Zarządzanie grupami|Pobieranie listy obsługiwanych dostawców tożsamości|
|Zarządzanie grupami|Pobieranie listy obsługiwanych dostawców tożsamości podróży użytkownika|
|Zarządzanie grupami|Pobranie listy niestandardowych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Zarządzanie grupami|Pobranie listy dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Zarządzanie grupami|Pobranie listy lokalnych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Zarządzanie grupami|Uzyskiwanie szczegółów dzierżawy dla użytkownika na potrzeby tworzenia zasobów|
|Zarządzanie grupami|Pobieranie domyślnej obsługiwanej kultury dla elementu CPIM|
|Zarządzanie grupami|Pobieranie szczegółów przepływu administracyjnego|
|Zarządzanie grupami|Pobieranie listy podróży użytkowników dla tej dzierżawy|
|Zarządzanie grupami|Pobieranie zestawu dostępnych i obsługiwanych kultur dla elementu CPIM|
|Zarządzanie grupami|Pobieranie zasad trustFramework|
|Zarządzanie grupami|Pobieranie zasad trustFramework w formacie XML|
|Zarządzanie grupami|Pobieranie atrybutu użytkownika|
|Zarządzanie zasadami|Pobieranie atrybutów użytkowników|
|Zarządzanie zasadami|Pobieranie listy podróży użytkowników|
|Zarządzanie zasadami|GetIEFPolicies|
|Zarządzanie zasadami|GetIdentityProviders|
|Zarządzanie zasadami|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|Przenoszenie zasobów|
|Resource|Stosowanie poprawek względem dostawcy tożsamości|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|Usuwanie podróży użytkownika|
|Resource|Aktualizowanie niestandardowego dostawcy tożsamości|
|Resource|Aktualizowanie dostawcy tożsamości|
|Resource|Aktualizowanie lokalnego dostawcy tożsamości|
|Resource|Aktualizowanie zasobu katalogu B2C|
|Resource|Aktualizowanie zasad|
|Resource|Aktualizowanie stanu subskrypcji|
|Zarządzanie rolami|Aktualizowanie atrybutu użytkownika|
|Zarządzanie rolami|Weryfikacja przenoszenia zasobów|
|Zarządzanie rolami|Dodawanie urządzenia|
|Zarządzanie rolami|Dodawanie konfiguracji urządzenia|
|Zarządzanie rolami|Dodawanie zarejestrowanego właściciela do urządzenia|
|Zarządzanie rolami|Dodawanie zarejestrowanych użytkowników do urządzenia|
|Zarządzanie rolami|Usuń urządzenie|
|Zarządzanie rolami|Usuwanie konfiguracji urządzenia|
|Zarządzanie rolami|Urządzenie, które nie jest już zgodne|
|Zarządzanie rolami|Urządzenie, które nie jest już zarządzane|
|Zarządzanie użytkownikami|AccessReview_Review|
|Zarządzanie użytkownikami|AccessReview_Update|
|Zarządzanie użytkownikami|ActivationAborted|
|Zarządzanie użytkownikami|ActivationApproved|
|Zarządzanie użytkownikami|ActivationCanceled|
|Zarządzanie użytkownikami|ActivationRequested|
|Zarządzanie użytkownikami|Dodawanie kwalifikujących się elementów członkowskich do roli|
|Zarządzanie użytkownikami|Dodawanie elementu członkowskiego do roli|
|Zarządzanie użytkownikami|Dodawanie przypisania roli do definicji roli|
|Zarządzanie użytkownikami|Dodawanie roli z szablonu|
|Zarządzanie użytkownikami|Dodawanie członka w zakresie do roli|
|Zarządzanie użytkownikami|Dodano|
|Zarządzanie użytkownikami|Przypisywanie|
|Zarządzanie użytkownikami|Tworzenie zbiorcze użytkowników — rozpoczęto|
|Zarządzanie użytkownikami|Tworzenie zbiorczo użytkowników — zakończono|
|Zarządzanie użytkownikami|Usuwanie zbiorcze użytkowników — rozpoczęto|
|Zarządzanie użytkownikami|Usuwanie zbiorcze użytkowników — zakończono|
|Zarządzanie użytkownikami|Użytkownicy pobierania zbiorczego — uruchomiono|
|Zarządzanie użytkownikami|Użytkownicy pobierania zbiorczego — zakończono|
|Zarządzanie użytkownikami|Przywrócenie zbiorcze usuniętych użytkowników — rozpoczęto|
|Zarządzanie użytkownikami|Przywracanie zbiorcze — usunięto użytkowników — zakończono|
|Zarządzanie użytkownikami|Zaproszeni użytkownicy zbiorczi — uruchomiono|
|Zarządzanie użytkownikami|Zaproszeni użytkownicy zbiorczi — zakończono|
|Zarządzanie użytkownikami|Usuwanie zarejestrowanego właściciela z urządzenia|
|Zarządzanie użytkownikami|Usuwanie zarejestrowanych użytkowników z urządzenia|
|Zarządzanie użytkownikami|Usuwanie kwalifikującego się elementu członkowskiego z roli|
|Zarządzanie użytkownikami|Usuwanie członka z roli|
|Zarządzanie użytkownikami|Usuwanie przypisania roli z definicji roli|
|Zarządzanie użytkownikami|Usuwanie elementu członkowskiego w zakresie z roli|
|Zarządzanie użytkownikami|Zaktualizuj urządzenie|
|Zarządzanie użytkownikami|Aktualizowanie konfiguracji urządzenia|
|Zarządzanie użytkownikami|Aktualizowanie roli|






## <a name="identity-protection"></a>Ochrona tożsamości

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie katalogiem|Podnieś|
|Zarządzanie katalogiem|Usunięto|
|Zarządzanie katalogiem|Zmiany ustawienia roli|
|Inne|ScanAlertsNow|
|Inne|Rejestracja|
|Inne|Obniżanie poziomu uprawnień|
|Inne|UpdateAlertSettings|
|Inne|UpdateCurrentState|
|Zarządzanie zasadami|Zakończono przegląd dostępu|
|Zarządzanie zasadami|Dodawanie osoby zatwierdzającej do zatwierdzenia żądania|
|Zarządzanie zasadami|Dodawanie recenzenta do przeglądu dostępu|
|Zarządzanie użytkownikami|Zastosuj przegląd dostępu|
|Zarządzanie użytkownikami|Utwórz przegląd dostępu|


## <a name="invited-users"></a>Zaproszeni użytkownicy

|Kategorii inspekcji|Działanie|
|---|---|
|Inne|Tworzenie zatwierdzenia żądania|
|Inne|Usuwanie przeglądu dostępu|
|Zarządzanie użytkownikami|Usuwanie recenzenta z przeglądu dostępu|
|Zarządzanie użytkownikami|Żądaj zastosowania wyniku przeglądu|
|Zarządzanie użytkownikami|Żądanie zatrzymania przeglądu|
|Zarządzanie użytkownikami|Przeglądanie przypisania aplikacji|
|Zarządzanie użytkownikami|Przeglądanie członkostwa w grupie|
|Zarządzanie użytkownikami|Przeglądanie członkostwa w roli funkcji RBAC|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie grupami|Przeglądanie żądania zatwierdzenia żądania|
|Zarządzanie grupami|Aktualizowanie przeglądu dostępu|
|Zarządzanie grupami|Aktualizowanie ustawień powiadomień e-mail dla przeglądu dostępu|
|Zarządzanie grupami|Aktualizuj ustawienie liczby cykli przeglądu dostępu|
|Zarządzanie grupami|Aktualizowanie ustawienia czasu trwania cyklu przeglądu dostępu w dniach|
|Zarządzanie użytkownikami|Aktualizuj ustawienie typu zakończenia cyklu przeglądu dostępu|
|Zarządzanie użytkownikami|Aktualizowanie ustawienia typu cyklu przeglądu dostępu|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Kategorii inspekcji|Działanie|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Dodano|
|PIM|AddedOutsidePIM|
|PIM|Przypisywanie|
|PIM|DismissAlert|
|PIM|Podnieś|
|PIM|ReactivateAlert|
|PIM|Usunięto|
|PIM|RemovedOutsidePIM|
|PIM|Żądanie zatrzymania przeglądu|
|PIM|Zmiany ustawienia roli|
|PIM|ScanAlertsNow|
|PIM|Rejestracja|
|PIM|Anulować przypisania|
|PIM|Obniżanie poziomu uprawnień|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Samoobsługowe zarządzanie grupami

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie grupami|Resetowanie hasła użytkownika|
|Zarządzanie grupami|Przywróć użytkownika|
|Zarządzanie grupami|Ustawianie wymuszania zmiany hasła użytkownika|
|Zarządzanie grupami|Ustawianie menedżera użytkowników|
|Zarządzanie grupami|Włączanie metadanych tokenu OATH użytkowników|
|Zarządzanie grupami|Aktualizowanie sygnatury czasowej StsRefreshTokenValidFrom|
|Zarządzanie grupami|Aktualizowanie zewnętrznych wpisów tajnych|
|Zarządzanie grupami|Aktualizuj użytkownika|
|Zarządzanie grupami|Administrator generuje hasło tymczasowe|


## <a name="self-service-password-management"></a>Samoobsługowe zarządzanie hasłami

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie katalogiem|Administratorzy wymagają od użytkowników zresetowania ich haseł|
|Zarządzanie katalogiem|Przypisywanie użytkownika zewnętrznego do aplikacji|
|Zarządzanie użytkownikami|Nie wysłano wiadomości e-mail (subskrypcja użytkownika został anulowana)|
|Zarządzanie użytkownikami|Zapraszanie użytkownika zewnętrznego|
|Zarządzanie użytkownikami|Realizowanie zaproszenia zewnętrznego użytkownika|
|Zarządzanie użytkownikami|Wirusowe tworzenie dzierżawy|
|Zarządzanie użytkownikami|Wirusowe tworzenie użytkownika|
|Zarządzanie użytkownikami|Rejestracja hasła użytkownika|
|Zarządzanie użytkownikami|Resetowanie hasła użytkownika|
|Zarządzanie użytkownikami|Zablokowano dostęp do samoobsługowego resetowania haseł|


## <a name="terms-of-use"></a>Warunki użytkowania

|Kategorii inspekcji|Działanie|
|---|---|
|Warunki użytkowania|Akceptowanie warunków użytkowania|
|Warunki użytkowania|Tworzenie warunków użytkowania|
|Warunki użytkowania|Odrzucanie warunków użytkowania|
|Warunki użytkowania|Usuń zgodę|
|Warunki użytkowania|Usuwanie warunków użytkowania|
|Warunki użytkowania|Edytowanie warunków użytkowania|
|Warunki użytkowania|Warunki użytkowania|
|Warunki użytkowania|Trwałe usuwanie warunków użytkowania|
|Warunki użytkowania|Publikowanie warunków użytkowania|
|Warunki użytkowania|Cofanie publikacji warunków użytkowania|


## <a name="next-steps"></a>Następne kroki

- [Przegląd raportów usługi Azure AD](overview-reports.md).
- [Raport dotyczący dzienników inspekcji](concept-audit-logs.md). 
- [Programistyczny dostęp do raportów usługi Azure AD](concept-reporting-api.md)
