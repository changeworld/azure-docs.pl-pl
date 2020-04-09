---
title: Dowiedz się, jak działa inicjowanie obsługi administracyjnej usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak działa inicjowanie obsługi administracyjnej usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 241d90981ed9ba54d253e6c22c00f9e5a9197863
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884889"
---
# <a name="how-provisioning-works"></a>Jak działa aprowizacja

Automatyczne inicjowanie obsługi administracyjnej odnosi się do tworzenia tożsamości użytkowników i ról w aplikacjach w chmurze, do których użytkownicy potrzebują dostępu. Oprócz tworzenia tożsamości użytkowników automatyczne inicjowanie obsługi administracyjnej obejmuje obsługę i usuwanie tożsamości użytkowników w miarę zmiany stanu lub ról. Przed rozpoczęciem wdrażania można przejrzeć ten artykuł, aby dowiedzieć się, jak działa aprowisja usługi Azure AD i uzyskać zalecenia dotyczące konfiguracji. 

**Usługa inicjowania obsługi administracyjnej usługi Azure AD** udostępnia użytkownikom aplikacje SaaS i inne systemy, łącząc się z punktem końcowym interfejsu API zarządzania tożsamościami między domenami (SCIM) 2.0 dostarczonym przez dostawcę aplikacji. Ten punkt końcowy scim umożliwia usługi Azure AD programowo tworzyć, aktualizować i usuwać użytkowników. W przypadku wybranych aplikacji usługa inicjowania obsługi administracyjnej może również tworzyć, aktualizować i usuwać dodatkowe obiekty związane z tożsamością, takie jak grupy i role. Kanał używany do inicjowania obsługi administracyjnej między usługą Azure AD a aplikacją jest szyfrowany przy użyciu szyfrowania PROTOKOŁU TLS HTTPS.


![Usługa inicjowania obsługi administracyjnej usługi](./media/how-provisioning-works/provisioning0.PNG)
Azure AD*Rysunek 1: Usługa inicjowania obsługi administracyjnej usługi Azure AD*

![Przepływ](./media/how-provisioning-works/provisioning1.PNG)
pracy inicjowania obsługi administracyjnej użytkowników wychodzących*Rysunek 2: przepływ pracy inicjowania obsługi administracyjnej użytkownika "wychodzący" z usługi Azure AD do popularnych aplikacji SaaS*

![Przepływ](./media/how-provisioning-works/provisioning2.PNG)
pracy inicjowania obsługi administracyjnej użytkowników przychodzących*Rysunek 3: Przepływ pracy inicjowania obsługi administracyjnej użytkowników przychodzących z popularnych aplikacji zarządzania kapitałem ludzkim (HCM) do usługi Azure Active Directory i usługi Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Inicjowanie obsługi administracyjnej przy użyciu języka SCIM 2.0

Usługa inicjowania obsługi administracyjnej usługi Azure AD używa [protokołu SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) do automatycznego inicjowania obsługi administracyjnej. Usługa łączy się z punktem końcowym SCIM dla aplikacji i używa schematu obiektu użytkownika SCIM i interfejsów API REST do automatyzacji inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej użytkowników i grup. Łącznik inicjowania obsługi administracyjnej oparty na mechanizmie SCIM jest dostępna dla większości aplikacji w galerii usługi Azure AD. Podczas tworzenia aplikacji dla usługi Azure AD deweloperzy mogą używać interfejsu API zarządzania użytkownikami SCIM 2.0 do tworzenia punktu końcowego scim, który integruje usługę Azure AD do inicjowania obsługi administracyjnej. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie punktu końcowego SCIM i konfigurowanie inicjowania obsługi administracyjnej użytkowników](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Aby zażądać automatycznego łącznika inicjowania obsługi administracyjnej usługi Azure AD dla aplikacji, która obecnie go nie ma, wypełnij [żądanie aplikacji usługi Azure Active Directory](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autoryzacja

Poświadczenia są wymagane dla usługi Azure AD, aby połączyć się z interfejsem API zarządzania użytkownikami aplikacji. Podczas konfigurowania automatycznego inicjowania obsługi administracyjnej dla aplikacji, należy wprowadzić prawidłowe poświadczenia. Można znaleźć typy poświadczeń i wymagania dla aplikacji, odwołując się do samouczka aplikacji. W witrynie Azure portal można przetestować poświadczenia, korzystając z próby połączenia usługi Azure AD z aplikacją inicjującą inicjowanie obsługi administracyjnej aplikacji przy użyciu podanych poświadczeń.

Jeśli samol oparte logowanie jednokrotne jest również skonfigurowany dla aplikacji, wewnętrzny limit magazynu usługi Azure AD dla aplikacji jest 1024 bajtów. Ten limit obejmuje wszystkie certyfikaty, tajne tokeny, poświadczenia i powiązane dane konfiguracyjne skojarzone z pojedynczym wystąpieniem aplikacji (znany również jako rekord jednostki usługi w usłudze Azure AD). Po skonfigurowaniu logowania jednokrotnego opartego na saml certyfikat używany do podpisywania tokenów SAML często zużywa ponad 50% procent miejsca. Wszelkie dodatkowe elementy (tajne tokeny, identyfikatory URI, adresy e-mail powiadomień, nazwy użytkowników i hasła), które są wprowadzane podczas konfigurowania inicjowania obsługi administracyjnej użytkownika, mogą przekroczyć limit miejsca. Aby uzyskać więcej informacji, zobacz [Problem z zapisywaniem poświadczeń administratora podczas konfigurowania inicjowania obsługi administracyjnej przez użytkowników](../manage-apps/application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Atrybuty mapowania

Po włączeniu inicjowania obsługi administracyjnej dla aplikacji SaaS innej firmy portal Azure kontroluje swoje wartości atrybutów za pomocą mapowań atrybutów. Mapowania określają atrybuty użytkownika, które przepływają między usługą Azure AD i aplikacji docelowej, gdy konta użytkowników są aprowied lub zaktualizowane.

Istnieje wstępnie skonfigurowany zestaw atrybutów i mapowań atrybutów między obiektami użytkowników usługi Azure AD a obiektami użytkownika każdej aplikacji SaaS. Niektóre aplikacje zarządzają innymi typami obiektów wraz z użytkownikami, takimi jak Grupy.

Podczas konfigurowania inicjowania obsługi administracyjnej, ważne jest, aby przejrzeć i skonfigurować mapowania atrybutów i przepływów pracy, które definiują, które właściwości użytkownika (lub grupy) przepływu z usługi Azure AD do aplikacji. Przejrzyj i skonfiguruj pasującą właściwość **(Dopasuj obiekty przy użyciu tego atrybutu),** która jest używana do jednoznacznej identyfikacji i dopasowania użytkowników/grup między dwoma systemami.

Domyślne mapowania atrybutów można dostosować do potrzeb firmy. Można więc zmienić lub usunąć istniejące mapowania atrybutów lub utworzyć nowe mapowania atrybutów. Aby uzyskać szczegółowe informacje, zobacz [Dostosowywanie mapowania atrybutów inicjowania obsługi administracyjnej dla aplikacji SaaS](../manage-apps/customize-application-attributes.md).

Podczas konfigurowania inicjowania obsługi administracyjnej do aplikacji SaaS, jednym z typów mapowań atrybutów, które można określić jest mapowanie wyrażeń. W przypadku tych mapowań należy napisać wyrażenie podobne do skryptu, które umożliwia przekształcenie danych użytkowników w formaty, które są bardziej akceptowalne dla aplikacji SaaS. Aby uzyskać szczegółowe informacje, zobacz [Pisanie wyrażeń dla mapowań atrybutów](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Zakresu 
### <a name="assignment-based-scoping"></a>Zakres oparty na przydziałach

W przypadku inicjowania obsługi administracyjnej ruchu wychodzącego z usługi Azure AD do aplikacji SaaS poleganie na [przypisaniach użytkowników lub grup](../manage-apps/assign-user-or-group-access-portal.md) jest najczęstszym sposobem określenia, którzy użytkownicy są w zakresie inicjowania obsługi administracyjnej. Ponieważ przypisania użytkowników są również używane do włączania logowania jednokrotnego, tej samej metody można użyć do zarządzania dostępem i inicjowania obsługi administracyjnej. Zakres oparty na przypisaniu nie ma zastosowania do scenariuszy inicjowania obsługi administracyjnej ruchu przychodzącego, takich jak Workday i Successfactors.

* **Grupy.** Dzięki planowi licencji usługi Azure AD Premium można użyć grup, aby przypisać dostęp do aplikacji SaaS. Następnie, gdy zakres inicjowania obsługi administracyjnej jest ustawiony na **Synchronizacja tylko przypisanych użytkowników i grup,** usługa inicjowania obsługi administracyjnej usługi Azure AD będzie aprowizować lub de-aprowizowaniu użytkowników na podstawie tego, czy są członkami grupy, która jest przypisana do aplikacji. Sam obiekt grupy nie jest aprowizowana, chyba że aplikacja obsługuje obiekty grupy. Upewnij się, że grupy przypisane do aplikacji mają właściwość "SecurityEnabled" ustawiona na "False".

* **Grupy dynamiczne.** Usługa inicjowania obsługi administracyjnej użytkowników usługi Azure AD może odczytywać i aprowizować użytkowników w [grupach dynamicznych.](../users-groups-roles/groups-create-rule.md) Należy pamiętać o następujących zastrzeżeniach i zaleceniach:

  * Grupy dynamiczne mogą mieć wpływ na wydajność kompleksowej inicjowania obsługi administracyjnej z usługi Azure AD do aplikacji SaaS.

  * Szybkość, jak użytkownik w grupie dynamicznej jest aprowizowana lub de-aprowizowana w aplikacji SaaS zależy od tego, jak szybko grupa dynamiczna może oceniać zmiany członkostwa. Aby uzyskać informacje dotyczące sprawdzania stanu przetwarzania grupy dynamicznej, zobacz [Sprawdzanie stanu przetwarzania reguły członkostwa](../users-groups-roles/groups-create-rule.md).

  * Gdy użytkownik traci członkostwo w grupie dynamicznej, jest uważany za zdarzenie usuwania obsługi administracyjnej. Należy wziąć pod uwagę ten scenariusz podczas tworzenia reguł dla grup dynamicznych.

* **Grupy zagnieżdżone.** Usługa inicjowania obsługi administracyjnej użytkowników usługi Azure AD nie może odczytywać ani aprowizować użytkowników w grupach zagnieżdżonych. Usługa może odczytywać i aprowizować tylko użytkowników, którzy są bezpośrednimi członkami jawnie przypisanej grupy. To ograniczenie "przypisania grupowe do aplikacji" dotyczy również logowania jednokrotnego (zobacz [Zarządzanie dostępem do aplikacji SaaS za pomocą grupy](../users-groups-roles/groups-saasapps.md)). Zamiast tego bezpośrednio przypisać lub w inny [zakres w](define-conditional-rules-for-provisioning-user-accounts.md) grupach, które zawierają użytkowników, którzy muszą być aprowizować.

### <a name="attribute-based-scoping"></a>Zakres oparty na atrybutach 

Filtry zakresu służą do definiowania reguł opartych na atrybutach, które określają, którzy użytkownicy są aprowizowani aplikacji. Ta metoda jest często używana do inicjowania obsługi administracyjnej ruchu przychodzącego z aplikacji HCM do usługi Azure AD i usługi Active Directory. Filtry zakresu są konfigurowane jako część mapowań atrybutów dla każdego łącznika inicjowania obsługi administracyjnej użytkownika usługi Azure AD. Aby uzyskać szczegółowe informacje na temat konfigurowania filtrów zakresu opartych na atrybutach, zobacz [Inicjowanie obsługi administracyjnej aplikacji oparte na atrybutach za pomocą filtrów zakresu](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>B2B (gość) użytkownicy

Usługa inicjowania obsługi administracyjnej użytkowników usługi Azure AD umożliwia aprowizacji użytkowników B2B (lub gościa) w usłudze Azure AD do aplikacji SaaS. Jednak dla użytkowników B2B, aby zalogować się do aplikacji SaaS przy użyciu usługi Azure AD, aplikacja SaaS musi mieć jego saml oparte na logowania jednokrotnego skonfigurowane w określony sposób. Aby uzyskać więcej informacji na temat konfigurowania aplikacji SaaS do obsługi logów od użytkowników B2B, zobacz [Konfigurowanie aplikacji SaaS do współpracy B2B](../b2b/configure-saas-apps.md).

Należy zauważyć, że userPrincipalName dla użytkownika-gościa jest często@domain.comprzechowywane jako "alias#EXT# ". gdy userPrincipalName znajduje się w mapowania atrybutów jako atrybut źródłowy, #EXT# jest usuwany z userPrincipalName. Jeśli potrzebujesz #EXT# być obecny, zastąp userPrincipalName originalUserPrincipalName jako atrybut źródła. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Cykle inicjowania obsługi administracyjnej: początkowe i przyrostowe

Gdy usługa Azure AD jest systemem źródłowym, usługa inicjowania obsługi administracyjnej używa [kwerendy delta do śledzenia zmian w danych programu Microsoft Graph w](https://docs.microsoft.com/graph/delta-query-overview) celu monitorowania użytkowników i grup. Usługa inicjowania obsługi administracyjnej uruchamia początkowy cykl względem systemu źródłowego i systemu docelowego, a następnie okresowych cykli przyrostowych.

### <a name="initial-cycle"></a>Cykl początkowy

Po uruchomieniu usługi inicjowania obsługi administracyjnej pierwszy cykl:

1. Wysyłaj zapytania do wszystkich użytkowników i grup z systemu źródłowego, pobierając wszystkie atrybuty zdefiniowane w [mapowaniach atrybutów](customize-application-attributes.md).

2. Filtruj zwracanych użytkowników i grupy przy użyciu skonfigurowanych [przypisań](../manage-apps/assign-user-or-group-access-portal.md) lub [filtrów zakresu opartych na atrybutach](define-conditional-rules-for-provisioning-user-accounts.md).

3. Gdy użytkownik jest przypisany lub w zakresie inicjowania obsługi administracyjnej, usługa wysyła zapytanie do systemu docelowego dla pasującego użytkownika przy użyciu określonych [pasujących atrybutów](customize-application-attributes.md#understanding-attribute-mapping-properties). Przykład: Jeśli nazwa userPrincipal w systemie źródłowym jest pasującym atrybutem i mapuje do userName w systemie docelowym, usługa inicjowania obsługi administracyjnej wysyła zapytanie do systemu docelowego dla nazw użytkowników, które pasują do wartości nazw userPrincipal w systemie źródłowym.

4. Jeśli pasujący użytkownik nie zostanie znaleziony w systemie docelowym, jest tworzony przy użyciu atrybutów zwróconych z systemu źródłowego. Po utworzeniu konta użytkownika usługa inicjowania obsługi administracyjnej wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika. Ten identyfikator jest używany do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.

5. Jeśli zostanie znaleziony pasujący użytkownik, jest aktualizowany przy użyciu atrybutów dostarczonych przez system źródłowy. Po dopasowaniu konta użytkownika usługa inicjowania obsługi administracyjnej wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika. Ten identyfikator jest używany do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.

6. Jeśli mapowania atrybutów zawierają atrybuty "odwołanie", usługa wykonuje dodatkowe aktualizacje w systemie docelowym w celu utworzenia i połączenia obiektów, do których istnieje odwołanie. Na przykład użytkownik może mieć atrybut "Manager" w systemie docelowym, który jest połączony z innym użytkownikiem utworzonym w systemie docelowym.

7. Utrwalić znak wodny na końcu początkowego cyklu, który zapewnia punkt wyjścia dla późniejszych cykli przyrostowych.

Niektóre aplikacje, takie jak ServiceNow, G Suite i Box, obsługują nie tylko inicjowanie obsługi administracyjnej użytkowników, ale także inicjowanie obsługi administracyjnej grup i ich członków. W takich przypadkach, jeśli inicjowanie obsługi administracyjnej grupy jest włączone w [mapowaniach,](customize-application-attributes.md)usługa inicjowania obsługi administracyjnej synchronizuje użytkowników i grupy, a następnie synchronizuje członkostwa w grupie.

### <a name="incremental-cycles"></a>Cykle przyrostowe

Po początkowym cyklu, wszystkie inne cykle będą:

1. Kwerenda systemu źródłowego dla wszystkich użytkowników i grup, które zostały zaktualizowane od ostatniego znaku wodnego był przechowywany.

2. Filtruj zwracanych użytkowników i grupy przy użyciu skonfigurowanych [przypisań](../manage-apps/assign-user-or-group-access-portal.md) lub [filtrów zakresu opartych na atrybutach](define-conditional-rules-for-provisioning-user-accounts.md).

3. Gdy użytkownik jest przypisany lub w zakresie inicjowania obsługi administracyjnej, usługa wysyła zapytanie do systemu docelowego dla pasującego użytkownika przy użyciu określonych [pasujących atrybutów](customize-application-attributes.md#understanding-attribute-mapping-properties).

4. Jeśli pasujący użytkownik nie zostanie znaleziony w systemie docelowym, jest tworzony przy użyciu atrybutów zwróconych z systemu źródłowego. Po utworzeniu konta użytkownika usługa inicjowania obsługi administracyjnej wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika. Ten identyfikator jest używany do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.

5. Jeśli zostanie znaleziony pasujący użytkownik, jest aktualizowany przy użyciu atrybutów dostarczonych przez system źródłowy. Jeśli jest to nowo przypisane konto, które jest dopasowane, usługa inicjowania obsługi administracyjnej wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika. Ten identyfikator jest używany do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.

6. Jeśli mapowania atrybutów zawierają atrybuty "odwołanie", usługa wykonuje dodatkowe aktualizacje w systemie docelowym w celu utworzenia i połączenia obiektów, do których istnieje odwołanie. Na przykład użytkownik może mieć atrybut "Manager" w systemie docelowym, który jest połączony z innym użytkownikiem utworzonym w systemie docelowym.

7. Jeśli użytkownik, który był wcześniej w zakresie inicjowania obsługi administracyjnej jest usuwany z zakresu, w tym jest nieprzypisany, usługa wyłącza użytkownika w systemie docelowym za pośrednictwem aktualizacji.

8. Jeśli użytkownik, który był wcześniej w zakresie inicjowania obsługi administracyjnej jest wyłączony lub nietrwałe usunięte w systemie źródłowym, usługa wyłącza użytkownika w systemie docelowym za pośrednictwem aktualizacji.

9. Jeśli użytkownik, który był wcześniej w zakresie inicjowania obsługi administracyjnej jest na stałe usunięte w systemie źródłowym, usługa usuwa użytkownika w systemie docelowym. W usłudze Azure AD użytkownicy są na czas usunięty 30 dni po ich nietrwale usunięte.

10. Utrwalić nowy znak wodny na końcu cyklu przyrostowego, który zapewnia punkt wyjścia dla późniejszych cykli przyrostowych.

> [!NOTE]
> Opcjonalnie można wyłączyć operacje **Tworzenia,** **Aktualizacji**lub **Usuwania** przy użyciu pól wyboru **Akcje obiektu docelowego** w sekcji [Mapowania.](customize-application-attributes.md) Logika wyłączania użytkownika podczas aktualizacji jest również kontrolowana za pomocą mapowania atrybutów z pola takiego jak "accountEnabled".

Usługa inicjowania obsługi administracyjnej kontynuuje uruchamianie cykli przyrostowych back-to-back przez czas nieokreślony, w odstępach czasu zdefiniowanych w [samouczku specyficznym dla każdej aplikacji.](../saas-apps/tutorial-list.md) Cykle przyrostowe są kontynuowane do momentu wystąpienia jednego z następujących zdarzeń:

- Usługa jest ręcznie zatrzymywany przy użyciu witryny Azure portal lub przy użyciu odpowiedniego polecenia interfejsu API programu Microsoft Graph.
- Nowy cykl początkowy jest wyzwalany przy użyciu opcji **Wyczyść stan i uruchom ponownie** w witrynie Azure portal lub przy użyciu odpowiedniego polecenia interfejsu API programu Microsoft Graph. Ta akcja czyści wszystkie przechowywane znak wodny i powoduje, że wszystkie obiekty źródłowe mają być oceniane ponownie.
- Nowy cykl początkowy jest wyzwalany z powodu zmiany mapowań atrybutów lub filtrów zakresu. Ta akcja również czyści wszystkie przechowywane znak wodny i powoduje, że wszystkie obiekty źródłowe mają być oceniane ponownie.
- Proces inicjowania obsługi administracyjnej przechodzi do kwarantanny (patrz poniżej) z powodu wysokiego poziomu błędu i pozostaje w kwarantannie przez ponad cztery tygodnie. W takim przypadku usługa zostanie automatycznie wyłączona.

### <a name="errors-and-retries"></a>Błędy i ponownych prób

Jeśli błąd w systemie docelowym uniemożliwia indywidualnemu użytkownikowi dodawania, aktualizowania lub usuwania w systemie docelowym, operacja jest ponawiana w następnym cyklu synchronizacji. Jeśli użytkownik nadal zakończy się niepowodzeniem, następnie ponownych prób rozpocznie się z mniejszą częstotliwością, stopniowo skalowanie z powrotem do tylko jednej próby dziennie. Aby rozwiązać ten błąd, administratorzy muszą sprawdzić [dzienniki inicjowania obsługi administracyjnej,](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) aby ustalić główną przyczynę i podjąć odpowiednie działania. Typowe błędy mogą obejmować:

- Użytkownicy, którzy nie mają atrybutu wypełnionego w systemie źródłowym, który jest wymagany w systemie docelowym
- Użytkownicy posiadający wartość atrybutu w systemie źródłowym, dla których istnieje unikatowe ograniczenie w systemie docelowym, a ta sama wartość jest obecna w innym rekordzie użytkownika

Rozwiąż te błędy, dostosowując wartości atrybutów dla danego użytkownika w systemie źródłowym lub dostosowując mapowania atrybutów, aby nie powodować konfliktów.

### <a name="quarantine"></a>Kwarantanna

Jeśli większość lub wszystkie wywołania, które są wykonane przeciwko systemowi docelowemu konsekwentnie zakończyć się niepowodzeniem z powodu błędu (na przykład nieprawidłowe poświadczenia administratora) zadanie inicjowania obsługi administracyjnej przechodzi do stanu "kwarantanny". Ten stan jest wskazany w [raporcie podsumowania inicjowania obsługi administracyjnej](../manage-apps/check-status-user-account-provisioning.md) i za pośrednictwem poczty e-mail, jeśli powiadomienia e-mail zostały skonfigurowane w witrynie Azure portal.

W kwarantannie częstotliwość cykli przyrostowych jest stopniowo zmniejszana do raz dziennie.

Zadanie inicjowania obsługi administracyjnej kończy kwarantannę po naprawieniu wszystkich błędów naruszających przepisy i uruchomieniu następnego cyklu synchronizacji. Jeśli zadanie inicjowania obsługi administracyjnej pozostaje w kwarantannie przez więcej niż cztery tygodnie, zadanie inicjowania obsługi administracyjnej jest wyłączone. Dowiedz się więcej tutaj o stanie kwarantanny [tutaj](../manage-apps/application-provisioning-quarantine-status.md).

### <a name="how-long-provisioning-takes"></a>Jak długo trwa aprowizacja

Wydajność zależy od tego, czy zadanie inicjowania obsługi administracyjnej jest uruchomione początkowego cyklu inicjowania obsługi administracyjnej lub cyklu przyrostowego. Aby uzyskać szczegółowe informacje o tym, jak długo trwa inicjowanie obsługi administracyjnej i jak monitorować stan usługi inicjowania obsługi administracyjnej, zobacz [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Jak sprawdzić, czy użytkownicy są prawidłowo aprowizacji

Wszystkie operacje uruchamiane przez usługę inicjowania obsługi administracyjnej użytkowników są rejestrowane w [dziennikach inicjowania obsługi administracyjnej](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)usługi Azure AD (w wersji zapoznawczej). Dzienniki obejmują wszystkie operacje odczytu i zapisu wykonane w systemach źródłowych i docelowych oraz dane użytkownika, które zostały odczytane lub zapisane podczas każdej operacji. Aby uzyskać informacje na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej w witrynie Azure portal, zobacz [przewodnik raportowania inicjowania obsługi administracyjnej.](../manage-apps/check-status-user-account-provisioning.md)

## <a name="de-provisioning"></a>Usuwanie obsługi administracyjnej

Usługa inicjowania obsługi administracyjnej usługi Azure AD utrzymuje systemy źródłowe i docelowe zsynchronizowane przez usuwanie obsługi administracyjnej kont, gdy użytkownicy nie powinni mieć już dostępu. 

Usługa inicjowania obsługi administracyjnej usługi Azure AD będzie nietrwałe usunąć użytkownika w aplikacji, gdy aplikacja suupports nietrwałe usuwa (żądanie aktualizacji z active = false) i wystąpi dowolne z następujących zdarzeń:

* Konto użytkownika zostanie usunięte w usłudze Azure AD
*   Użytkownik nie jest przypisany z aplikacji
*   Użytkownik nie spełnia już filtru zakresu i wykracza poza zakres
    * Domyślnie usługa inicjowania obsługi administracyjnej usługi Azure AD nietrwałe usuwa lub wyłącza użytkowników, którzy wychodzą poza zakres. Jeśli chcesz zastąpić to zachowanie domyślne, możesz ustawić flagę, aby [pominąć usunięcia poza zakresem](../app-provisioning/skip-out-of-scope-deletions.md).
*   Właściwość AccountEnabled jest ustawiona na

Jeśli wystąpi jedno z powyższych czterech zdarzeń, a aplikacja docelowa nie obsługuje usuwania nietrwałego, usługa inicjowania obsługi administracyjnej wyśle żądanie DELETE, aby trwale usunąć użytkownika z aplikacji. 

30 dni po usunięciu użytkownika w usłudze Azure AD zostaną one trwale usunięte z dzierżawy. W tym momencie usługa inicjowania obsługi administracyjnej wyśle żądanie DELETE, aby trwale usunąć użytkownika w aplikacji. W dowolnym momencie okna 30-dniowego można [ręcznie usunąć użytkownika na stałe,](../fundamentals/active-directory-users-restore.md)co wysyła żądanie usunięcia do aplikacji.

Jeśli widzisz atrybut IsSoftDeleted w mapowania atrybutów, jest on używany do określenia stanu użytkownika i czy wysłać żądanie aktualizacji z active = false do usuwania nietrwałego użytkownika. 

## <a name="next-steps"></a>Następne kroki

[Planowanie wdrożenia automatycznego aprowizowania użytkowników](../app-provisioning/plan-auto-user-provisioning.md)

[Konfigurowanie aprowizacji dla aplikacji z galerii](../manage-apps/configure-automatic-user-provisioning-portal.md)

[Tworzenie punktu końcowego SCIM i konfigurowanie inicjowania obsługi administracyjnej podczas tworzenia własnej aplikacji](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Rozwiązywanie problemów z konfigurowaniem i inicjowania obsługi administracyjnej użytkowników do aplikacji](../manage-apps/application-provisioning-config-problem.md).
