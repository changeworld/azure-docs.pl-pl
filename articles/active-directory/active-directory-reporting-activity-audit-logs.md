---
title: Raporty dotyczące inspekcji w portalu usługi Azure Active Directory | Microsoft Docs
description: Wprowadzenie do raportów dotyczących inspekcji w portalu usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 97ea32a1e0f8815accff6201251771ab8c088859
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące inspekcji w portalu usługi Azure Active Directory 

Dzięki raportom w usłudze Azure Active Directory (Azure AD) możesz uzyskać wszystkie informacje, które pomogą ustalić działanie środowiska.

Architektura raportowania w usłudze Azure AD obejmuje następujące składniki:

- **Działanie** 
    - **Działania związane z logowaniem** — informacje na temat użycia zarządzanych aplikacji i działania użytkownika związane z logowaniem
    - **Dzienniki inspekcji** — informacje o aktywności systemu obejmujące zarządzanie użytkownikami i grupami oraz zarządzane aplikacje i działania dotyczące katalogu.
- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. Aby uzyskać więcej informacji, zobacz Ryzykowne logowania.
    - **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. Aby uzyskać więcej informacji, zobacz Użytkownicy oflagowani w związku z ryzykiem.

Ten temat zawiera przegląd działań dotyczących inspekcji.
 
## <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
* Użytkownicy w roli administratora zabezpieczeń lub czytelnika zabezpieczeń
* Administratorzy globalni
* Poszczególni użytkownicy (inni niż administratorzy) mogą wyświetlać dane na temat własnych działań


## <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji w usłudze Azure Active Directory dostarczają informacji na temat aktywności systemu pod kątem zgodności.  
Pierwszym punktem wejścia do wszystkich danych inspekcji jest pozycja **Dzienniki inspekcji** znajdująca się w sekcji **Aktywność** usługi **Azure Active Directory**.

![Dzienniki inspekcji](./media/active-directory-reporting-activity-audit-logs/61.png "Dzienniki inspekcji")

Dziennik inspekcji zawiera domyślny widok listy, który pokazuje:

- datę i godzinę wystąpienia,
- inicjatora/aktora (*kto*) działania, 
- działanie (*co*), 
- element docelowy.

![Dzienniki inspekcji](./media/active-directory-reporting-activity-audit-logs/18.png "Dzienniki inspekcji")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Dzienniki inspekcji](./media/active-directory-reporting-activity-audit-logs/19.png "Dzienniki inspekcji")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Dzienniki inspekcji](./media/active-directory-reporting-activity-audit-logs/21.png "Dzienniki inspekcji")


Klikając pozycję w widoku listy, możesz uzyskać wszystkie szczegóły na jej temat.

![Dzienniki inspekcji](./media/active-directory-reporting-activity-audit-logs/22.png "Dzienniki inspekcji")


## <a name="filtering-audit-logs"></a>Dzienniki inspekcji filtrowania

Aby zawęzić zgłaszane dane do odpowiedniego poziomu, możesz odfiltrować dane inspekcji przy użyciu następujących pól:

- Zakres dat
- Zainicjowane przez (aktor)
- Kategoria
- Typ zasobu działania
- Działanie

![Dzienniki inspekcji](./media/active-directory-reporting-activity-audit-logs/23.png "Dzienniki inspekcji")


Filtr **Zakres dat** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości:

- 1 miesiąc
- 7 dni
- 24 godziny
- Niestandardowy

Po wybraniu niestandardowego przedziału czasu możesz skonfigurować godzinę rozpoczęcia i zakończenia.

Filtr **Zainicjowane przez** umożliwia określenie nazwy aktora lub jego głównej nazwy użytkownika (UPN, user principal name).

Filtr **Kategoria** umożliwia wybranie jednego z następujących filtrów:

- Wszyscy
- Kategoria podstawowa
- Katalog podstawowy
- Samoobsługowe zarządzanie hasłami
- Samoobsługowe zarządzanie grupami
- Aprowizacja kont — automatyczne przerzucanie haseł
- Zaproszeni użytkownicy
- Usługa MIM
- Identity Protection
- B2C

Filtr **Typ zasobu działania** umożliwia wybranie jednego z następujących filtrów:

- Wszyscy 
- Grupa
- Katalog
- Użytkownik
- Aplikacja
- Zasady
- Urządzenie
- Inne

Jeśli wybierzesz opcję **Grupa** w pozycji **Typ zasobu działania**, uzyskasz dostęp do dodatkowej kategorii filtru umożliwiającej podanie wartości **Źródło**:

- Azure AD
- O365


Filtr **Działanie** jest oparty na wybranej kategorii i typie zasobu działania. Możesz wybrać konkretne działanie, które chcesz zobaczyć, lub wybrać wszystkie działania. 

Listę wszystkich działań związanych z inspekcją można uzyskać przy użyciu interfejsu API programu Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, gdzie $tenantdomain to nazwa Twojej domeny. Jest ona również przedstawiona w artykule [Zdarzenia raportów inspekcji](active-directory-reporting-audit-events.md).


## <a name="audit-logs-shortcuts"></a>Skróty dzienników inspekcji

Poza usługą **Azure Active Directory** witryna Azure Portal zapewnia dwa dodatkowe punkty wejścia do danych inspekcji:

- Użytkownicy i grupy
- Aplikacje dla przedsiębiorstw

### <a name="users-and-groups-audit-logs"></a>Dzienniki inspekcji użytkowników i grup

Za pomocą raportów inspekcji opartych na użytkownikach i grupach można uzyskać odpowiedzi na pytania, takie jak:

- Jakie typy aktualizacji zostały zastosowane przez użytkowników?

- Ilu użytkowników zostało zmienionych?

- Ile haseł zostało zmienionych?

- Jakich zmian dokonał administrator w katalogu?

- Jakie grupy zostały dodane?

- Czy istnieją grupy, w których dokonano zmiany członkostwa?

- Czy właściciele grup zostali zmienieni?

- Jakie licencje zostały przypisane do grupy lub użytkownika?

Jeśli chcesz przeglądać dane inspekcji dotyczące użytkowników i grup, możesz skorzystać z widoku filtrowanego znajdującego się w obszarze **Dzienniki inspekcji** w sekcji **Aktywność** na stronie **Użytkownicy i grupy**. Ten punkt wejścia ma wartość **Użytkownicy i grupy** wstępnie wybraną dla opcji **Typ zasobu działania**.

![Dzienniki inspekcji](./media/active-directory-reporting-activity-audit-logs/93.png "Dzienniki inspekcji")

### <a name="enterprise-applications-audit-logs"></a>Dzienniki inspekcji aplikacji dla przedsiębiorstw

Za pomocą raportów inspekcji opartych na aplikacjach można uzyskać odpowiedzi na pytania, takie jak:

* Jakie aplikacje zostały dodane lub zaktualizowane?
* Jakie aplikacje zostały usunięte?
* Czy usługa w ramach aplikacji została zmieniona?
* Czy nazwy aplikacji zostały zmienione?
* Kto udzielił zezwolenia dla aplikacji?

Jeśli chcesz przeglądać dane inspekcji dotyczące aplikacji, możesz skorzystać z widoku filtrowanego znajdującego się w obszarze **Dzienniki inspekcji** w sekcji **Aktywność** bloku **Aplikacje dla przedsiębiorstw**. Ten punkt wejścia ma wartość **Aplikacje dla przedsiębiorstw** wstępnie wybraną dla opcji **Typ zasobu działania**.

![Dzienniki inspekcji](./media/active-directory-reporting-activity-audit-logs/134.png "Dzienniki inspekcji")

Możesz odfiltrować ten widok, aby wyświetlić tylko **grupy** lub **użytkowników**.

![Dzienniki inspekcji](./media/active-directory-reporting-activity-audit-logs/25.png "Dzienniki inspekcji")



## <a name="azure-ad-audit-activity-list"></a>Lista działań związanych z inspekcją usługi Azure AD

Ta sekcja zawiera listę wszystkich działań, które mogą być rejestrowane. 


|Nazwa usługi|Kategorii inspekcji|Typ zasobu działania|Działanie|
|---|---|---|---|
|Aprowizacja kont|Zarządzanie aplikacjami|Aplikacja|Administracja|
|Aprowizacja kont|Zarządzanie aplikacjami|Aplikacja|Operacja katalogu|
|Aprowizacja kont|Zarządzanie aplikacjami|Aplikacja|Eksportowanie|
|Aprowizacja kont|Zarządzanie aplikacjami|Aplikacja|Import|
|Aprowizacja kont|Zarządzanie aplikacjami|Aplikacja|Inne|
|Aprowizacja kont|Zarządzanie aplikacjami|Aplikacja|Przetwarzanie depozytu|
|Aprowizacja kont|Zarządzanie aplikacjami|Aplikacja|Akcja reguły synchronizacji|
|Serwer proxy aplikacji|Zarządzanie aplikacjami|Aplikacja|Dodawanie aplikacji|
|Serwer proxy aplikacji|Zasób|Zasób|Dodawanie certyfikatu SSL aplikacji|
|Serwer proxy aplikacji|Zasób|Zasób|Usuwanie powiązania SSL|
|Serwer proxy aplikacji|Zarządzanie aplikacjami|Aplikacja|Usuwanie aplikacji|
|Serwer proxy aplikacji|Zarządzanie katalogami|Katalog|Wyłączanie logowania jednokrotnego dla aplikacji klasycznych|
|Serwer proxy aplikacji|Zarządzanie katalogami|Katalog|Wyłączanie logowania jednokrotnego dla aplikacji klasycznych na potrzeby określonej domeny|
|Serwer proxy aplikacji|Zarządzanie katalogami|Katalog|Wyłączanie serwera proxy aplikacji|
|Serwer proxy aplikacji|Zarządzanie katalogami|Katalog|Wyłączanie uwierzytelniania przekazującego|
|Serwer proxy aplikacji|Zarządzanie katalogami|Katalog|Włączanie logowania jednokrotnego dla aplikacji klasycznych|
|Serwer proxy aplikacji|Zarządzanie katalogami|Katalog|Włączanie logowania jednokrotnego dla aplikacji klasycznych dla określonej domeny|
|Serwer proxy aplikacji|Zarządzanie katalogami|Katalog|Włączanie serwera proxy aplikacji|
|Serwer proxy aplikacji|Zarządzanie katalogami|Katalog|Włączanie uwierzytelniania przekazującego|
|Serwer proxy aplikacji|Zasób|Zasób|Rejestrowanie łącznika|
|Serwer proxy aplikacji|Zarządzanie aplikacjami|Aplikacja|Aktualizowanie aplikacji|
|Serwer proxy aplikacji|Zarządzanie aplikacjami|Aplikacja|Aktualizowanie trybu logowania jednokrotnego do aplikacji|
|Automatyczne przenoszenie haseł|Zarządzanie aplikacjami|Aplikacja|Automatyczne przenoszenie haseł|
|B2C|Zarządzanie aplikacjami|Aplikacja|Dodawanie uprawnień aplikacji w wersji 2|
|B2C|Autoryzacja|Autoryzacja|Dodawanie uprawnień aplikacji w wersji 2|
|B2C|Klucz|Klucz|Dodawanie klucza opartego na wpisie tajnym w formacie ASCII do kontenera kluczy CPIM|
|B2C|Autoryzacja|Autoryzacja|Dodawanie klucza opartego na wpisie tajnym w formacie ASCII do kontenera kluczy CPIM|
|B2C|Klucz|Klucz|Dodawanie klucza do kontenera kluczy CPIM|
|B2C|Autoryzacja|Autoryzacja|Dodawanie klucza do kontenera kluczy CPIM|
|B2C|Zasób|Zasób|AdminPolicyDatas-RemoveResources|
|B2C|Autoryzacja|Autoryzacja|AdminPolicyDatas-SetResources|
|B2C|Zasób|Zasób|AdminPolicyDatas-SetResources|
|B2C|Zasób|Zasób|AdminUserJourneys-GetResources|
|B2C|Autoryzacja|Autoryzacja|AdminUserJourneys-GetResources|
|B2C|Autoryzacja|Autoryzacja|AdminUserJourneys-RemoveResources|
|B2C|Zasób|Zasób|AdminUserJourneys-RemoveResources|
|B2C|Zasób|Zasób|AdminUserJourneys-SetResources|
|B2C|Autoryzacja|Autoryzacja|AdminUserJourneys-SetResources|
|B2C|Autoryzacja|Autoryzacja|Tworzenie dostawcy IdentityProvider|
|B2C|Zasób|Zasób|Tworzenie dostawcy IdentityProvider|
|B2C|Autoryzacja|Autoryzacja|Tworzenie aplikacji w wersji 1|
|B2C|Zarządzanie aplikacjami|Aplikacja|Tworzenie aplikacji w wersji 1|
|B2C|Zarządzanie aplikacjami|Aplikacja|Tworzenie aplikacji w wersji 2|
|B2C|Autoryzacja|Autoryzacja|Tworzenie aplikacji w wersji 2|
|B2C|Zarządzanie katalogami|Katalog|Tworzenie domen niestandardowych w ramach dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Tworzenie domen niestandardowych w ramach dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Tworzenie nowego elementu AdminUserJourney|
|B2C|Zasób|Zasób|Tworzenie nowego elementu AdminUserJourney|
|B2C|Zasób|Zasób|Tworzenie zlokalizowanego pliku JSON zasobu|
|B2C|Autoryzacja|Autoryzacja|Tworzenie zlokalizowanego pliku JSON zasobu|
|B2C|Autoryzacja|Autoryzacja|Tworzenie nowego niestandardowego dostawcy tożsamości|
|B2C|Zasób|Zasób|Tworzenie nowego niestandardowego dostawcy tożsamości|
|B2C|Zasób|Zasób|Tworzenie nowego dostawcy tożsamości|
|B2C|Autoryzacja|Autoryzacja|Tworzenie nowego dostawcy tożsamości|
|B2C|Autoryzacja|Autoryzacja|Tworzenie lub aktualizowanie zasobu katalogu B2C|
|B2C|Zasób|Zasób|Tworzenie lub aktualizowanie zasobu katalogu B2C|
|B2C|Zasób|Zasób|Tworzenie zasad|
|B2C|Autoryzacja|Autoryzacja|Tworzenie zasad|
|B2C|Autoryzacja|Autoryzacja|Tworzenie zasad trustFramework|
|B2C|Zasób|Zasób|Tworzenie zasad trustFramework|
|B2C|Autoryzacja|Autoryzacja|Tworzenie zasad trustFramework przy użyciu konfigurowalnego prefiksu|
|B2C|Zasób|Zasób|Tworzenie zasad trustFramework przy użyciu konfigurowalnego prefiksu|
|B2C|Zasób|Zasób|Tworzenie atrybutu użytkownika|
|B2C|Autoryzacja|Autoryzacja|Tworzenie atrybutu użytkownika|
|B2C|Autoryzacja|Autoryzacja|CreateTrustFrameworkPolicy|
|B2C|Zasób|Zasób|CreateTrustFrameworkPolicy|
|B2C|Autoryzacja|Autoryzacja|Tworzenie lub aktualizowanie nowego elementu AdminUserJourney|
|B2C|Zasób|Zasób|Usuwanie dostawcy tożsamości|
|B2C|Autoryzacja|Autoryzacja|Usuwanie dostawcy tożsamości|
|B2C|Zasób|Zasób|Usuwanie dostawcy tożsamości|
|B2C|Autoryzacja|Autoryzacja|Usuwanie dostawcy tożsamości|
|B2C|Zarządzanie aplikacjami|Aplikacja|Usuwanie aplikacji w wersji 1|
|B2C|Autoryzacja|Autoryzacja|Usuwanie aplikacji w wersji 1|
|B2C|Zarządzanie aplikacjami|Aplikacja|Usuwanie aplikacji w wersji 2|
|B2C|Autoryzacja|Autoryzacja|Usuwanie aplikacji w wersji 2|
|B2C|Autoryzacja|Autoryzacja|Usuwanie udzielenia uprawnień aplikacji w wersji 2|
|B2C|Zarządzanie aplikacjami|Aplikacja|Usuwanie udzielenia uprawnień aplikacji w wersji 2|
|B2C|Zasób|Zasób|Usuwanie zasobu katalogu B2C|
|B2C|Autoryzacja|Autoryzacja|Usuwanie zasobu katalogu B2C|
|B2C|Klucz|Klucz|Usuwanie kontenera kluczy CPIM|
|B2C|Autoryzacja|Autoryzacja|Usuwanie kontenera kluczy CPIM|
|B2C|Klucz|Klucz|Usuwanie kontenera kluczy|
|B2C|Zasób|Zasób|Usuwanie zasad trustFramework|
|B2C|Autoryzacja|Autoryzacja|Usuwanie zasad trustFramework|
|B2C|Zasób|Zasób|Usuwanie atrybutu użytkownika|
|B2C|Autoryzacja|Autoryzacja|Usuwanie atrybutu użytkownika|
|B2C|Autoryzacja|Autoryzacja|Włączanie funkcji B2C|
|B2C|Zarządzanie katalogami|Katalog|Włączanie funkcji B2C|
|B2C|Zasób|Zasób|Pobieranie zasobów katalogu B2C w grupie zasobów|
|B2C|Zasób|Zasób|Pobieranie zasobów katalogu B2C w ramach subskrypcji|
|B2C|Autoryzacja|Autoryzacja|Pobieranie zasobów katalogu B2C w ramach subskrypcji|
|B2C|Autoryzacja|Autoryzacja|Pobieranie niestandardowego dostawcy tożsamości|
|B2C|Zasób|Zasób|Pobieranie niestandardowego dostawcy tożsamości|
|B2C|Zasób|Zasób|Pobieranie dostawcy tożsamości|
|B2C|Autoryzacja|Autoryzacja|Pobieranie dostawcy tożsamości|
|B2C|Autoryzacja|Autoryzacja|Pobieranie aplikacji w wersji 1 i 2|
|B2C|Zarządzanie aplikacjami|Aplikacja|Pobieranie aplikacji w wersji 1 i 2|
|B2C|Autoryzacja|Autoryzacja|Pobieranie aplikacji w wersji 1|
|B2C|Zarządzanie aplikacjami|Aplikacja|Pobieranie aplikacji w wersji 1|
|B2C|Autoryzacja|Autoryzacja|Pobieranie aplikacji w wersji 1|
|B2C|Zarządzanie aplikacjami|Aplikacja|Pobieranie aplikacji w wersji 1|
|B2C|Zarządzanie aplikacjami|Aplikacja|Pobieranie aplikacji w wersji 2|
|B2C|Autoryzacja|Autoryzacja|Pobieranie aplikacji w wersji 2|
|B2C|Zarządzanie aplikacjami|Aplikacja|Pobieranie aplikacji w wersji 2|
|B2C|Autoryzacja|Autoryzacja|Pobieranie aplikacji w wersji 2|
|B2C|Zasób|Zasób|Pobieranie zasobu katalogu B2C|
|B2C|Autoryzacja|Autoryzacja|Pobieranie zasobu katalogu B2C|
|B2C|Autoryzacja|Autoryzacja|Pobieranie listy domen niestandardowych w ramach dzierżawy|
|B2C|Zarządzanie katalogami|Katalog|Pobieranie listy domen niestandardowych w ramach dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Pobieranie podróży użytkownika|
|B2C|Zasób|Zasób|Pobieranie podróży użytkownika|
|B2C|Zasób|Zasób|Pobieranie dozwolonych oświadczeń aplikacji dla podróży użytkownika|
|B2C|Autoryzacja|Autoryzacja|Pobieranie dozwolonych oświadczeń aplikacji dla podróży użytkownika|
|B2C|Zasób|Zasób|Pobieranie dozwolonych i samodzielnie określonych oświadczeń dla podróży użytkownika|
|B2C|Autoryzacja|Autoryzacja|Pobieranie dozwolonych i samodzielnie określonych oświadczeń dla podróży użytkownika|
|B2C|Zasób|Zasób|Pobieranie dozwolonych i samodzielnie określonych oświadczeń zasad|
|B2C|Autoryzacja|Autoryzacja|Pobieranie dozwolonych i samodzielnie określonych oświadczeń zasad|
|B2C|Zasób|Zasób|Pobieranie listy dozwolonych oświadczeń wyjściowych|
|B2C|Autoryzacja|Autoryzacja|Pobieranie listy dozwolonych oświadczeń wyjściowych|
|B2C|Zasób|Zasób|Pobieranie definicji zawartości dla podróży użytkownika|
|B2C|Autoryzacja|Autoryzacja|Pobieranie definicji zawartości dla podróży użytkownika|
|B2C|Autoryzacja|Autoryzacja|Pobieranie dostawców tożsamości dla określonego przepływu administracyjnego|
|B2C|Zasób|Zasób|Pobieranie dostawców tożsamości dla określonego przepływu administracyjnego|
|B2C|Klucz|Klucz|Pobieranie metadanych klucza aktywnego kontenera kluczy w formacie JWK|
|B2C|Autoryzacja|Autoryzacja|Pobieranie metadanych klucza aktywnego kontenera kluczy w formacie JWK|
|B2C|Klucz|Klucz|Pobranie metadanych kontenera kluczy|
|B2C|Zasób|Zasób|Pobieranie listy wszystkich przepływów administracyjnych|
|B2C|Autoryzacja|Autoryzacja|Pobieranie listy wszystkich przepływów administracyjnych|
|B2C|Autoryzacja|Autoryzacja|Pobieranie listy tagów wszystkich przepływów administracyjnych dla wszystkich użytkowników|
|B2C|Zasób|Zasób|Pobieranie listy tagów wszystkich przepływów administracyjnych dla wszystkich użytkowników|
|B2C|Zasób|Zasób|Pobieranie listy dzierżaw dla użytkownika|
|B2C|Autoryzacja|Autoryzacja|Pobieranie listy dzierżaw dla użytkownika|
|B2C|Zasób|Zasób|Pobieranie samodzielnie określonych oświadczeń kont lokalnych|
|B2C|Autoryzacja|Autoryzacja|Pobieranie samodzielnie określonych oświadczeń kont lokalnych|
|B2C|Zasób|Zasób|Pobieranie zlokalizowanego pliku JSON zasobu|
|B2C|Autoryzacja|Autoryzacja|Pobieranie zlokalizowanego pliku JSON zasobu|
|B2C|Autoryzacja|Autoryzacja|Pobieranie operacji dostawcy zasobów Microsoft.AzureActiveDirectory|
|B2C|Zasób|Zasób|Pobieranie operacji dostawcy zasobów Microsoft.AzureActiveDirectory|
|B2C|Zasób|Zasób|Pobieranie zasad|
|B2C|Autoryzacja|Autoryzacja|Pobieranie zasad|
|B2C|Zasób|Zasób|Pobranie zasad|
|B2C|Autoryzacja|Autoryzacja|Pobranie zasad|
|B2C|Zarządzanie katalogami|Katalog|Pobieranie właściwości zasobów dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Pobieranie właściwości zasobów dzierżawy|
|B2C|Zasób|Zasób|Pobieranie listy obsługiwanych dostawców tożsamości|
|B2C|Autoryzacja|Autoryzacja|Pobieranie listy obsługiwanych dostawców tożsamości|
|B2C|Zasób|Zasób|Pobieranie listy obsługiwanych dostawców tożsamości podróży użytkownika|
|B2C|Autoryzacja|Autoryzacja|Pobieranie listy obsługiwanych dostawców tożsamości podróży użytkownika|
|B2C|Zarządzanie katalogami|Katalog|Pobieranie informacji o dzierżawie|
|B2C|Autoryzacja|Autoryzacja|Pobieranie informacji o dzierżawie|
|B2C|Zarządzanie katalogami|Katalog|Pobieranie dozwolonych funkcji dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Pobieranie dozwolonych funkcji dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Pobranie listy niestandardowych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|B2C|Zasób|Zasób|Pobranie listy niestandardowych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|B2C|Zasób|Zasób|Pobranie listy dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Pobranie listy dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|B2C|Zasób|Zasób|Pobranie listy lokalnych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Pobranie listy lokalnych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|B2C|Zasób|Zasób|Uzyskiwanie szczegółów dzierżawy dla użytkownika na potrzeby tworzenia zasobów|
|B2C|Autoryzacja|Autoryzacja|Uzyskiwanie szczegółów dzierżawy dla użytkownika na potrzeby tworzenia zasobów|
|B2C|Autoryzacja|Autoryzacja|Pobieranie listy dzierżaw|
|B2C|Autoryzacja|Autoryzacja|Pobieranie domen dzierżaw|
|B2C|Zarządzanie katalogami|Katalog|Pobieranie domen dzierżaw|
|B2C|Zasób|Zasób|Pobieranie domyślnej obsługiwanej kultury dla elementu CPIM|
|B2C|Autoryzacja|Autoryzacja|Pobieranie domyślnej obsługiwanej kultury dla elementu CPIM|
|B2C|Zasób|Zasób|Pobieranie szczegółów przepływu administracyjnego|
|B2C|Autoryzacja|Autoryzacja|Pobieranie szczegółów przepływu administracyjnego|
|B2C|Autoryzacja|Autoryzacja|Pobieranie listy podróży użytkowników dla tej dzierżawy|
|B2C|Zasób|Zasób|Pobieranie listy podróży użytkowników dla tej dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Pobieranie zestawu dostępnych i obsługiwanych kultur dla elementu CPIM|
|B2C|Zasób|Zasób|Pobieranie zestawu dostępnych i obsługiwanych kultur dla elementu CPIM|
|B2C|Autoryzacja|Autoryzacja|Pobieranie zasad trustFramework|
|B2C|Zasób|Zasób|Pobieranie zasad trustFramework|
|B2C|Autoryzacja|Autoryzacja|Pobieranie zasad trustFramework w formacie XML|
|B2C|Zasób|Zasób|Pobieranie zasad trustFramework w formacie XML|
|B2C|Zasób|Zasób|Pobieranie atrybutu użytkownika|
|B2C|Autoryzacja|Autoryzacja|Pobieranie atrybutu użytkownika|
|B2C|Autoryzacja|Autoryzacja|Pobieranie atrybutów użytkowników|
|B2C|Zasób|Zasób|Pobieranie atrybutów użytkowników|
|B2C|Autoryzacja|Autoryzacja|Pobieranie listy podróży użytkowników|
|B2C|Zasób|Zasób|Pobieranie listy podróży użytkowników|
|B2C|Autoryzacja|Autoryzacja|GetIEFPolicies|
|B2C|Zasób|Zasób|GetIEFPolicies|
|B2C|Autoryzacja|Autoryzacja|GetIdentityProviders|
|B2C|Zasób|Zasób|GetIdentityProviders|
|B2C|Zasób|Zasób|GetTrustFrameworkPolicy|
|B2C|Autoryzacja|Autoryzacja|GetTrustFrameworkPolicy|
|B2C|Klucz|Klucz|Pobiera kontener kluczy CPIM w formacie JWK|
|B2C|Autoryzacja|Autoryzacja|Pobiera kontener kluczy CPIM w formacie JWK|
|B2C|Klucz|Klucz|Pobiera listę kontenerów kluczy w ramach dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Pobiera listę kontenerów kluczy w ramach dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Pobiera typ dzierżawy|
|B2C|Zarządzanie katalogami|Katalog|Pobiera typ dzierżawy|
|B2C|Authentication|Authentication|Wystawianie tokenu dostępu dla aplikacji|
|B2C|Authentication|Authentication|Wystawianie kodu autoryzacji dla aplikacji|
|B2C|Inne|Inne|Wystawianie kodu autoryzacji dla aplikacji|
|B2C|Authentication|Authentication|Wystawianie elementu id_token dla aplikacji|
|B2C|Inne|Inne|Wystawianie elementu id_token dla aplikacji|
|B2C|Autoryzacja|Autoryzacja|MigrateTenantMetadata|
|B2C|Zasób|Zasób|MigrateTenantMetadata|
|B2C|Zasób|Zasób|Przenoszenie zasobów|
|B2C|Autoryzacja|Autoryzacja|Stosowanie poprawek względem dostawcy tożsamości|
|B2C|Zasób|Zasób|Stosowanie poprawek względem dostawcy tożsamości|
|B2C|Zasób|Zasób|PutTrustFrameworkPolicy|
|B2C|Autoryzacja|Autoryzacja|PutTrustFrameworkPolicy|
|B2C|Autoryzacja|Autoryzacja|PutTrustFrameworkpolicy|
|B2C|Zasób|Zasób|PutTrustFrameworkpolicy|
|B2C|Zasób|Zasób|Usuwanie podróży użytkownika|
|B2C|Autoryzacja|Autoryzacja|Usuwanie podróży użytkownika|
|B2C|Autoryzacja|Autoryzacja|Przywracanie kopii zapasowej kontenera kluczy CPIM|
|B2C|Klucz|Klucz|Przywracanie kopii zapasowej kontenera kluczy CPIM|
|B2C|Zarządzanie aplikacjami|Aplikacja|Pobieranie udzieleń uprawnień aplikacji w wersji 2|
|B2C|Autoryzacja|Autoryzacja|Pobieranie udzieleń uprawnień aplikacji w wersji 2|
|B2C|Zarządzanie aplikacjami|Aplikacja|Pobieranie jednostek usług aplikacji w wersji 2 w ramach bieżącej dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Pobieranie jednostek usług aplikacji w wersji 2 w ramach bieżącej dzierżawy|
|B2C|Klucz|Klucz|Zapisywanie kontenera kluczy|
|B2C|Autoryzacja|Autoryzacja|Aktualizowanie niestandardowego dostawcy tożsamości|
|B2C|Zasób|Zasób|Aktualizowanie niestandardowego dostawcy tożsamości|
|B2C|Zasób|Zasób|Aktualizowanie dostawcy tożsamości|
|B2C|Autoryzacja|Autoryzacja|Aktualizowanie dostawcy tożsamości|
|B2C|Zasób|Zasób|Aktualizowanie lokalnego dostawcy tożsamości|
|B2C|Autoryzacja|Autoryzacja|Aktualizowanie lokalnego dostawcy tożsamości|
|B2C|Zarządzanie aplikacjami|Aplikacja|Aktualizowanie aplikacji w wersji 1|
|B2C|Autoryzacja|Autoryzacja|Aktualizowanie aplikacji w wersji 1|
|B2C|Zarządzanie aplikacjami|Aplikacja|Aktualizowanie aplikacji w wersji 2|
|B2C|Autoryzacja|Autoryzacja|Aktualizowanie aplikacji w wersji 2|
|B2C|Zarządzanie aplikacjami|Aplikacja|Aktualizowanie udzielenia uprawnień aplikacji w wersji 2|
|B2C|Autoryzacja|Autoryzacja|Aktualizowanie udzielenia uprawnień aplikacji w wersji 2|
|B2C|Zasób|Zasób|Aktualizowanie zasobu katalogu B2C|
|B2C|Zasób|Zasób|Aktualizowanie zasad|
|B2C|Autoryzacja|Autoryzacja|Aktualizowanie zasad|
|B2C|Zasób|Zasób|Aktualizowanie stanu subskrypcji|
|B2C|Zasób|Zasób|Aktualizowanie atrybutu użytkownika|
|B2C|Autoryzacja|Autoryzacja|Aktualizowanie atrybutu użytkownika|
|B2C|Klucz|Klucz|Przekazywanie zaszyfrowanego klucza CPIM|
|B2C|Autoryzacja|Autoryzacja|Przekazywanie zaszyfrowanego klucza CPIM|
|B2C|Autoryzacja|Autoryzacja|Autoryzacja użytkownika: interfejs API jest wyłączony dla zestawu funkcji dzierżawy|
|B2C|Autoryzacja|Autoryzacja|Autoryzacja użytkownika: użytkownik udzielił dostępu jako „Administrator dzierżawy”|
|B2C|Autoryzacja|Autoryzacja|Autoryzacja użytkownika: użytkownikowi udzielono praw dostępu „Uwierzytelnieni użytkownicy”|
|B2C|Authentication|Authentication|Weryfikacja poświadczeń konta lokalnego|
|B2C|Zasób|Zasób|Weryfikacja przenoszenia zasobów|
|B2C|Authentication|Authentication|Weryfikacja uwierzytelniania użytkownika|
|B2C|Zarządzanie katalogami|Katalog|Weryfikowanie, czy funkcja B2C jest włączona|
|B2C|Autoryzacja|Autoryzacja|Weryfikowanie, czy funkcja B2C jest włączona|
|B2C|Autoryzacja|Autoryzacja|Weryfikowanie, czy funkcja jest włączona|
|B2C|Zarządzanie katalogami|Katalog|Weryfikowanie, czy funkcja jest włączona|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Dodawanie elementu OAuth2PermissionGrant|
|Katalog podstawowy|Zarządzanie jednostkami administracyjnymi|AdministrativeUnit|Dodawanie jednostki administracyjnej|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Dodawanie udzielenia przypisania roli aplikacji do użytkownika|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Dodawanie przypisania roli aplikacji do grupy|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Dodawanie przypisania roli aplikacji do jednostki usługi|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Dodawanie aplikacji|
|Katalog podstawowy|Zasób|Zasób|Dodawanie urządzenia|
|Katalog podstawowy|Zasób|Zasób|Dodawanie konfiguracji urządzenia|
|Katalog podstawowy|Zarządzanie rolami|Rola|Dodawanie kwalifikujących się elementów członkowskich do roli|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Dodawanie grupy|
|Katalog podstawowy|Zarządzanie jednostkami administracyjnymi|AdministrativeUnit|Dodawanie elementu członkowskiego do jednostki administracyjnej|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Dodawanie elementu członkowskiego do grupy|
|Katalog podstawowy|Zarządzanie rolami|Rola|Dodawanie elementu członkowskiego do roli|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Dodawanie właściciela do aplikacji|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Dodawanie właściciela do grupy|
|Katalog podstawowy|Zarządzanie zasadami|Zasady|Dodawanie właściciela do zasad|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Dodawanie właściciela do jednostki usługi|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Dodawanie partnera do firmy|
|Katalog podstawowy|Zarządzanie zasadami|Zasady|Dodawanie zasad|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Dodawanie zasad do jednostki usługi|
|Katalog podstawowy|Zasób|Zasób|Dodawanie zarejestrowanego właściciela do urządzenia|
|Katalog podstawowy|Zasób|Zasób|Dodawanie zarejestrowanych użytkowników do urządzenia|
|Katalog podstawowy|Zarządzanie rolami|Rola|Dodawanie przypisania roli do definicji roli|
|Katalog podstawowy|Zarządzanie rolami|Rola|Dodawanie roli z szablonu|
|Katalog podstawowy|Zarządzanie rolami|Rola|Dodawanie członka w zakresie do roli|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Dodawanie jednostki usługi|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Dodawanie poświadczeń jednostki usługi|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Dodawanie niezweryfikowanej domeny|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Dodawanie użytkownika|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Dodawanie szczegółów dotyczących aplikacji telefonicznej do silnego uwierzytelniania użytkowników|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Dodawanie zweryfikowanej domeny|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Zmienianie licencji użytkownika|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Zmienianie hasła użytkownika|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Zgoda na aplikację|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Konwertowanie użytkownika federacyjnego na zarządzanego|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Tworzenie hasła aplikacji dla użytkownika|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Tworzenie firmy|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Tworzenie ustawień firmy|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Tworzenie ustawień grupy|
|Katalog podstawowy|Zarządzanie jednostkami administracyjnymi|AdministrativeUnit|Usuwanie jednostki administracyjnej|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Usuwanie aplikacji|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Usuwanie hasła aplikacji dla użytkownika|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Usuwanie ustawień firmy|
|Katalog podstawowy|Zasób|Zasób|Usuwanie urządzenia|
|Katalog podstawowy|Zasób|Zasób|Usuwanie konfiguracji urządzenia|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Usuwanie grupy|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Usuwanie ustawień grupy|
|Katalog podstawowy|Zarządzanie zasadami|Zasady|Usuwanie zasad|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Usuwanie użytkownika|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Obniżanie poziomu partnera|
|Katalog podstawowy|Zasób|Zasób|Urządzenie, które nie jest już zgodne|
|Katalog podstawowy|Zasób|Zasób|Urządzenie, które nie jest już zarządzane|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Usunięto katalog|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Trwale usunięto katalog|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Katalog zaplanowany do usunięcia|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Wyłączanie konta|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Włączanie silnego uwierzytelniania|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Kończenie stosowania licencji opartej na grupie wobec użytkowników|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Całkowite usuwanie aplikacji|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Całkowite usuwanie grupy|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Całkowite usuwanie użytkownika|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Podwyższanie poziomu firmy do partnera|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Przeczyszczanie właściwości usługi Rights Management|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Usuwanie elementu OAuth2PermissionGrant|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Usuwanie przypisania roli aplikacji z grupy|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Usuwanie przypisania roli aplikacji z jednostki usługi|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Usuwanie przypisania roli aplikacji z użytkownika|
|Katalog podstawowy|Zarządzanie rolami|Rola|Usuwanie kwalifikującego się elementu członkowskiego z roli|
|Katalog podstawowy|Zarządzanie jednostkami administracyjnymi|AdministrativeUnit|Usuwanie elementu członkowskiego z jednostki administracyjnej|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Usuwanie elementu członkowskiego z grupy|
|Katalog podstawowy|Zarządzanie rolami|Rola|Usuwanie elementu członkowskiego z roli|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Usuwanie właściciela z aplikacji|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Usuwanie właściciela z grupy|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Usuwanie właściciela z jednostki usługi|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Usuwanie partnera z firmy|
|Katalog podstawowy|Zarządzanie zasadami|Zasady|Usuwanie poświadczeń zasad|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Usuwanie zasad z jednostki usługi|
|Katalog podstawowy|Zasób|Zasób|Usuwanie zarejestrowanego właściciela z urządzenia|
|Katalog podstawowy|Zasób|Zasób|Usuwanie zarejestrowanych użytkowników z urządzenia|
|Katalog podstawowy|Zarządzanie rolami|Rola|Usuwanie przypisania roli z definicji roli|
|Katalog podstawowy|Zarządzanie rolami|Rola|Usuwanie elementu członkowskiego w zakresie z roli|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Usuwanie jednostki usługi|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Usuwanie poświadczeń jednostki usługi|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Usuwanie niezweryfikowanej domeny|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Usuwanie szczegółów dotyczących aplikacji telefonicznej do silnego uwierzytelniania użytkowników|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Usuwanie zweryfikowanej domeny|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Resetowanie hasła użytkownika|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Przywracanie grupy|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Przywracanie aplikacji|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Przywracanie użytkownika|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Wycofywanie zgody|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Ustaw informacje o firmie|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Ustawianie funkcji Dirsync|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Ustawianie flagi Dirsyncenabled|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Ustaw partnerstwo|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Ustawianie progu przypadkowego usunięcia|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Ustawianie lokalizacji danych dozwolonych przez firmę|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Włączanie funkcji międzynarodowej firmy|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Ustawianie funkcji katalogu w dzierżawie|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Ustawianie uwierzytelniania domeny|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Określanie ustawień federacyjnych w domenie|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Ustawianie wymuszania zmiany hasła użytkownika|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Ustawianie licencji grupy|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Ustawianie grupy do zarządzania przez użytkownika|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Ustawianie zasad haseł|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Ustawianie właściwości usługi Rights Management|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Ustawianie menedżera użytkowników|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Włączanie metadanych tokenu OATH użytkowników|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Rozpoczynanie stosowania licencji opartej na grupie wobec użytkowników|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Wyzwalanie ponownego obliczania licencji grupy|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Aktualizowanie sygnatury czasowej StsRefreshTokenValidFrom|
|Katalog podstawowy|Zarządzanie jednostkami administracyjnymi|AdministrativeUnit|Aktualizowanie jednostki administracyjnej|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Aktualizowanie aplikacji|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Aktualizowanie firmy|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Aktualizowanie ustawień firmy|
|Katalog podstawowy|Zasób|Zasób|Aktualizowanie urządzenia|
|Katalog podstawowy|Zasób|Zasób|Aktualizowanie konfiguracji urządzenia|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Aktualizowanie domeny|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Aktualizowanie zewnętrznych wpisów tajnych|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Aktualizowanie zewnętrznych wpisów tajnych|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Aktualizowanie grupy|
|Katalog podstawowy|Zarządzanie grupami|Grupa|Aktualizowanie ustawień grupy|
|Katalog podstawowy|Zarządzanie zasadami|Zasady|Aktualizowanie zasad|
|Katalog podstawowy|Zarządzanie rolami|Rola|Aktualizowanie roli|
|Katalog podstawowy|Zarządzanie aplikacjami|Aplikacja|Aktualizowanie jednostki usługi|
|Katalog podstawowy|Zarządzanie użytkownikami|Użytkownik|Aktualizowanie użytkownika|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Weryfikowanie domeny|
|Katalog podstawowy|Zarządzanie katalogami|Katalog|Sprawdzanie domeny zweryfikowanej za pomocą wiadomości e-mail|
|Identity Protection|Zarządzanie użytkownikami|Użytkownik|Administrator generuje hasło tymczasowe|
|Identity Protection|Zarządzanie użytkownikami|Użytkownik|Administratorzy wymagają od użytkowników zresetowania ich haseł|
|Identity Protection|Inne|Inne|Pobieranie pojedynczego typu zdarzenia o podwyższonym ryzyku|
|Identity Protection|Inne|Inne|Pobieranie administratorów i stanu zgody na podsumowanie tygodniowe|
|Identity Protection|Inne|Inne|Pobieranie wszystkich typów zdarzeń o podwyższonym ryzyku|
|Identity Protection|Inne|Inne|Pobieranie zdarzeń o podwyższonym ryzyku dla użytkowników wersji bezpłatnych|
|Identity Protection|Inne|Inne|Pobieranie użytkowników oflagowanych w związku z ryzykiem|
|Identity Protection|Zarządzanie katalogami|Katalog|Dołączanie|
|Identity Protection|Zarządzanie zasadami|Zasady|Ustawianie zasad rejestracji usługi MFA|
|Identity Protection|Zarządzanie zasadami|Zasady|Ustawianie zasad dotyczących ryzyka związanego z logowaniem|
|Identity Protection|Zarządzanie zasadami|Zasady|Ustawianie zasad dotyczących ryzyka związanego z użytkownikiem|
|Identity Protection|Zarządzanie katalogami|Katalog|Aktualizowanie ustawień alertów|
|Identity Protection|Zarządzanie katalogami|Katalog|Aktualizowanie ustawień podsumowania tygodniowego|
|Zaproszeni użytkownicy|Zarządzanie użytkownikami|Użytkownik|Przypisywanie użytkownika zewnętrznego do aplikacji|
|Zaproszeni użytkownicy|Inne|Inne|Przetworzone zaproszenia zbiorcze |
|Zaproszeni użytkownicy|Inne|Inne|Przekazane zaproszenia zbiorcze|
|Zaproszeni użytkownicy|Zarządzanie użytkownikami|Użytkownik|Nie wysłano wiadomości e-mail (subskrypcja użytkownika został anulowana)|
|Zaproszeni użytkownicy|Zarządzanie użytkownikami|Użytkownik|Zapraszanie użytkownika zewnętrznego|
|Zaproszeni użytkownicy|Zarządzanie użytkownikami|Użytkownik|Realizowanie zaproszenia zewnętrznego użytkownika|
|Zaproszeni użytkownicy|Zarządzanie użytkownikami|Użytkownik|Wirusowe tworzenie dzierżawy|
|Zaproszeni użytkownicy|Zarządzanie użytkownikami|Użytkownik|Wirusowe tworzenie użytkownika|
|Microsoft Identity Manager (MIM)|Zarządzanie grupami|Grupa|Dodaj członka|
|Microsoft Identity Manager (MIM)|Zarządzanie grupami|Grupa|Tworzenie grupy|
|Microsoft Identity Manager (MIM)|Zarządzanie grupami|Grupa|Usuń grupę|
|Microsoft Identity Manager (MIM)|Zarządzanie grupami|Grupa|Usuń członka|
|Microsoft Identity Manager (MIM)|Zarządzanie grupami|Grupa|Zaktualizuj grupę|
|Microsoft Identity Manager (MIM)|Zarządzanie użytkownikami|Użytkownik|Rejestracja hasła użytkownika|
|Microsoft Identity Manager (MIM)|Zarządzanie użytkownikami|Użytkownik|Resetowanie hasła użytkownika|
|Privileged Identity Management|Zarządzanie rolami|Rola|AccessReview_Review|
|Privileged Identity Management|Zarządzanie rolami|Rola|AccessReview_Update|
|Privileged Identity Management|Zarządzanie rolami|Rola|ActivationAborted|
|Privileged Identity Management|Zarządzanie rolami|Rola|ActivationApproved|
|Privileged Identity Management|Zarządzanie rolami|Rola|ActivationCanceled|
|Privileged Identity Management|Zarządzanie rolami|Rola|ActivationRequested|
|Privileged Identity Management|Zarządzanie rolami|Rola|Dodano|
|Privileged Identity Management|Zarządzanie rolami|Rola|Przypisywanie|
|Privileged Identity Management|Zarządzanie rolami|Rola|Podnoszenie poziomu uprawnień|
|Privileged Identity Management|Zarządzanie rolami|Rola|Usunięto|
|Privileged Identity Management|Zarządzanie rolami|Rola|Zmiany ustawienia roli|
|Privileged Identity Management|Zarządzanie rolami|Rola|ScanAlertsNow|
|Privileged Identity Management|Zarządzanie rolami|Rola|Rejestracja|
|Privileged Identity Management|Zarządzanie rolami|Rola|Obniżanie poziomu uprawnień|
|Privileged Identity Management|Zarządzanie rolami|Rola|UpdateAlertSettings|
|Privileged Identity Management|Zarządzanie rolami|Rola|UpdateCurrentState|
|Samoobsługowe zarządzanie grupami|Zarządzanie grupami|Grupa|Zatwierdzanie oczekującego żądania dołączenia do grupy|
|Samoobsługowe zarządzanie grupami|Zarządzanie grupami|Grupa|Anulowanie oczekującego żądania dołączenia do grupy|
|Samoobsługowe zarządzanie grupami|Zarządzanie grupami|Grupa|Tworzenie zasad zarządzania cyklem życia|
|Samoobsługowe zarządzanie grupami|Zarządzanie grupami|Grupa|Usuwanie oczekującego żądania dołączenia do grupy|
|Samoobsługowe zarządzanie grupami|Zarządzanie grupami|Grupa|Odrzucanie oczekującego żądania dołączenia do grupy|
|Samoobsługowe zarządzanie grupami|Zarządzanie grupami|Grupa|Odnawianie grupy|
|Samoobsługowe zarządzanie grupami|Zarządzanie grupami|Grupa|Żądanie dołączenia do grupy|
|Samoobsługowe zarządzanie grupami|Zarządzanie grupami|Grupa|Ustawianie właściwości grupy dynamicznej|
|Samoobsługowe zarządzanie grupami|Zarządzanie grupami|Grupa|Aktualizowanie zasad zarządzania cyklem życia|
|Samoobsługowe zarządzanie hasłami|Zarządzanie użytkownikami|Użytkownik|Zablokowano dostęp do samoobsługowego resetowania haseł|
|Samoobsługowe zarządzanie hasłami|Zarządzanie użytkownikami|Użytkownik|Zmienianie hasła (samoobsługa)|
|Samoobsługowe zarządzanie hasłami|Zarządzanie katalogami|Katalog|Wyłączanie funkcji zapisywania zwrotnego haseł dla katalogu|
|Samoobsługowe zarządzanie hasłami|Zarządzanie katalogami|Katalog|Włączanie funkcji zapisywania zwrotnego haseł dla katalogu|
|Samoobsługowe zarządzanie hasłami|Zarządzanie użytkownikami|Użytkownik|Resetowanie hasła (przez administratora)|
|Samoobsługowe zarządzanie hasłami|Zarządzanie użytkownikami|Użytkownik|Resetowanie hasła (samoobsługa)|
|Samoobsługowe zarządzanie hasłami|Zarządzanie użytkownikami|Użytkownik|Postęp działania przepływu samoobsługowego resetowania hasła|
|Samoobsługowe zarządzanie hasłami|Zarządzanie użytkownikami|Użytkownik|Postęp działania przepływu samoobsługowego resetowania hasła|
|Samoobsługowe zarządzanie hasłami|Zarządzanie użytkownikami|Użytkownik|Odblokowywanie konta użytkownika (samoobsługa)|
|Samoobsługowe zarządzanie hasłami|Zarządzanie użytkownikami|Użytkownik|Użytkownik zarejestrowany do samoobsługowego resetowania haseł|
|Warunki użytkowania|Zarządzanie zasadami|Zasady|Akceptowanie warunków użytkowania|
|Warunki użytkowania|Zarządzanie zasadami|Zasady|Tworzenie warunków użytkowania|
|Warunki użytkowania|Zarządzanie zasadami|Zasady|Odrzucanie warunków użytkowania|
|Warunki użytkowania|Zarządzanie zasadami|Zasady|Usuwanie warunków użytkowania|
|Warunki użytkowania|Zarządzanie zasadami|Zasady|Edytowanie warunków użytkowania|
|Warunki użytkowania|Zarządzanie zasadami|Zasady|Publikowanie warunków użytkowania|
|Warunki użytkowania|Zarządzanie zasadami|Zasady|Cofanie publikacji warunków użytkowania|




## <a name="next-steps"></a>Następne kroki

Omówienie funkcji raportowania można znaleźć w temacie [Raporty w usłudze Azure Active Directory](active-directory-reporting-azure-portal.md).

