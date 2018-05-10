---
title: Plan analizy kondycji Azure
description: Wskazówki dotyczące wdrażania umożliwi analizy kondycji HIPAA/HITRUST
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: jomolesk
ms.openlocfilehash: e627019f6219509c21ca6245ee1f7f54f2e1669a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Zabezpieczeń platformy Azure i plan zgodności - dane kondycji HIPAA/HITRUST i AI

## <a name="overview"></a>Przegląd

**Azure zabezpieczeń i zgodności plan - dane kondycji HIPAA/HITRUST i AI oferuje wdrożenia gotowe rozwiązania Azure PaaS w taki sposób, aby pokazują, jak bezpiecznie pozyskiwania, przechowywanie, analizowanie i interakcji z danymi kondycji podczas możliwość zapewnienia zgodności z branży wymagania. Plan pomaga przyspieszenie wdrożenia w chmurze i wykorzystanie przez klientów z danymi, które jest regulowany.**

Azure zabezpieczeń i zgodności plan - dane kondycji HIPAA/HITRUST i plan AI zapewnia narzędzia i wskazówki ułatwiające wdrażanie bezpiecznej, Health Insurance Portability i Accountability Act (HIPAA) i kondycji informacji zaufania Alliance (HITRUST) gotowe Platforma jako usługa (PaaS) środowisko do wprowadzania, przechowywanie, analizowania i interakcji z osobistego i -personal medyczne rekordów w środowisku chmury bezpiecznego, wielowarstwową wdrażać jako rozwiązanie na trasie. Ilustrację architektura odwołania, a zaprojektowano w celu uproszczenia wdrażania programu Microsoft Azure. Taka architektura podana przedstawiono rozwiązania do potrzeb organizacji znalezienia podejście oparte na chmurze w celu zmniejszenia obciążenia i koszt wdrożenia.

![](images/components.png)

To rozwiązanie do wykorzystania zestawu danych przykładowych sformatowany przy użyciu szybkiego opieki zdrowotnej współdziałanie zasobów (FHIR), standard na całym świecie do wymiany informacji opieki zdrowotnej elektronicznie i zapisz go w bezpieczny sposób. Klienci mogą następnie użyć usługi Azure Machine Learning mógł korzystać z narzędzia do analizy biznesowej zaawansowanych i analiza, aby przejrzeć prognoz dla przykładowych danych. Na przykład tego rodzaju eksperymentu, które mogą ułatwić usługi Azure Machine Learning plan zawiera przykładowego zestawu danych, skrypty i narzędzia do przewidywania czas pacjenta w zakładzie szpital Twoich. 

Ten plan ma stanowić podstawę moduły do klientów dopasować do specyficznych wymagań, tworzenie nowych Azure uczenia maszynowego eksperymenty rozwiązać obu scenariuszy przypadków zastosowań klinicznych i działa. Zaprojektowano go jako bezpieczne i zgodne, po wdrożeniu; Jednakże klienci są zobowiązani do konfigurowania ról poprawnie i wdrażanie wszelkie modyfikacje. Pamiętaj o następujących kwestiach:

-   Ten plan zawiera linii bazowej, aby ułatwić klientom programu Microsoft Azure HITRUST i środowiska HIPAA.

-   Mimo że planu zaprojektowano tak, aby wyrównać je z HIPAA i HITRUST (za pośrednictwem wspólnego Framework zabezpieczeń - CSF), jego nie należy traktować zgodne, dopóki certyfikowanych przez zewnętrznego audytora na HIPAA i HITRUST wymagania dotyczące certyfikacji.

-   Klienci są odpowiedzialne za przeprowadzanie odpowiednich zabezpieczeń i zgodności przeglądy dowolnego rozwiązania utworzone przy użyciu tej architektury podstawowych.

## <a name="deploying-the-automation"></a>Wdrażanie w automatyzacji

- Aby wdrożyć rozwiązanie, postępuj zgodnie z instrukcjami podanymi w wskazówki dotyczące wdrażania. 

[![](./images/deploy.png)](https://aka.ms/healthblueprintdeploy)

Aby uzyskać szybki przegląd sposobu działania tego rozwiązania, obejrzyj ten [wideo](https://aka.ms/healthblueprintvideo) wyjaśniający i prezentowania jego wdrażania.

- Często zadawane pytania można znaleźć w [— często zadawane pytania](https://aka.ms/healthblueprintfaq) wskazówki.

-   **Diagram architektury.** Diagram przedstawia architekturę odniesienia używane do planu i przykładzie użyj scenariusz.

-   **Szablony wdrażania**. W tym wdrożeniu [szablonów usługi Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) są używane do automatycznego wdrożenia składników architektury w Microsoft Azure, określając parametry konfiguracji podczas instalacji.

-   **[Skrypty wdrażania automatycznego](https://aka.ms/healthblueprintdeploy)**. Skrypty te pomagają wdrażania rozwiązania. Skrypty obejmują:


-   Instalacja modułu i [administratora globalnego](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) skrypt instalacyjny jest używane do instalowania i sprawdź, czy wymagane moduły programu PowerShell i ról administratora globalnego są poprawnie skonfigurowane. 
-   Instalacja skryptu PowerShell służy do wdrażania rozwiązania podanego przez plik zip, który zawiera funkcje pokaz wbudowanych.

## <a name="solution-components"></a>Składniki rozwiązania


Architektura podstawowych składa się z następujących składników:

-   **[Modelu zagrożeń](https://aka.ms/healththreatmodel)**  modelu zagrożeń kompleksowe znajduje się w formacie tm7 do użytku z [narzędzia do modelowania zagrożeń Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=49168), pokazujący składniki rozwiązania, dane przepływają między nimi i zaufanie granice. Model może ułatwić klientom punkty potencjalne ryzyko w infrastrukturze systemu, podczas tworzenia składników learning maszyny lub inne zmiany.

-   **[Macierz implementację klienta](https://aka.ms/healthcrmblueprint)**  skoroszyt programu Microsoft Excel A przedstawiono odpowiednie wymagania HITRUST oraz wyjaśniono, jak firma Microsoft i klienta są odpowiedzialni za spełnienie każdej z nich.

-   **[Przegląd kondycji.](https://aka.ms/healthreviewpaper)** Rozwiązanie zostało sprawdzone przez Coalfire systems, Inc. Zgodność kondycji (HIPAA i HITRUST) wskazówki dotyczące wdrażania i przejrzyj zapewnia audytorzy\'s przeglądu rozwiązanie i uwagi dotyczące Przekształcanie plan wdrażania gotowe do produkcji.

# <a name="architectural-diagram"></a>Diagram architektury


![](images/refarch.png)

## <a name="roles"></a>Role


Plan definiuje dwie role dla użytkowników administracyjnych (Operatorzy) i trzy role dla użytkowników w zarządzaniu szpital Twoich i nad pacjentem. Szóstym roli jest zdefiniowany dla audytorzy do oceny zgodności z HIPAA i innych rozporządzeń. Azure opartej na rolach kontroli dostępu (RBAC) umożliwia precyzyjne ukierunkowanych zarządzanie dostępem dla każdego użytkownika rozwiązania za pomocą wbudowanych i niestandardowych ról. Zobacz [wprowadzenie opartej na rolach kontroli dostępu w portalu Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) i [wbudowanych ról dla kontroli dostępu opartej na rolach na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) szczegółowe informacje o RBAC, role i uprawnienia.

### <a name="site-administrator"></a>Administrator lokacji


Administrator lokacji jest odpowiedzialny za klienta subskrypcji platformy Azure. One kontrolować ogólne wdrażanie, ale nie mają dostępu do pacjenta rekordów.

-   Domyślne przypisania roli: [właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Przypisania ról niestandardowych: Brak

-   Zakres: subskrypcji

### <a name="database-analyst"></a>Analityk baz danych

Analityk bazy danych służy do administrowania wystąpienia programu SQL Server i bazy danych.
Ich nie mają dostępu do pacjenta rekordów.

-   Przypisania ról wbudowanych: [Współautor bazy danych SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [współautora serwera SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Przypisania ról niestandardowych: Brak

-   Zakres: Grupa zasobów

 ### <a name="data-scientist"></a>Naukowca danych


Naukowca danych działa usługa Azure Machine Learning. One importować, eksportować i zarządzanie danymi i uruchamianie raportów. Naukowca danych ma dostęp do danych pacjenta, ale nie ma uprawnień administracyjnych.

-   Przypisania ról wbudowanych: [współautora konta magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Przypisania ról niestandardowych: Brak

-   Zakres: Grupa zasobów

### <a name="chief-medical-information-officer-cmio"></a>Medyczne Dyrektor (CMIO)


CMIO pokrywającej dzielenia między teleinformatycznego/technologii i opieki zdrowotnej specjalistów w organizacji opieki zdrowotnej. Obowiązków zwykle zawierają przy użyciu analizy w celu określenia, czy trwa przydzielania zasobów odpowiednio w organizacji.

-   Przypisania ról wbudowanych: Brak

### <a name="care-line-manager"></a>Opieka kierownika linii


Menedżer wierszy opieki jest bezpośrednio związane z rozwagą pacjentów.
Ta rola wymaga monitorowania stanu poszczególnych pacjentów oraz zapewniania, że jest dostępny personel spełniający określone wymagania opieki nad pacjentami. Menedżer wierszy opieki jest odpowiedzialny za dodawanie i aktualizowanie pacjenta rekordów.

-   Przypisania ról wbudowanych: Brak

-   Przypisania ról niestandardowych: ma uprawnienia do uruchamiania HealthcareDemo.ps1 celu obu wprowadzenia pacjenta i.

-   Zakres: Grupa zasobów

### <a name="auditor"></a>Audytor


Audytor ocenia zgodność w zakresie. Ich nie mają bezpośredniego dostępu do sieci.

-   Przypisania ról wbudowanych: [czytnika](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Przypisania ról niestandardowych: Brak

-   Zakres: subskrypcji

## <a name="example-use-case"></a>Przykład przypadek użycia


Przypadek użycia przykładowe dołączone do tego planu przedstawiono, jak plan może służyć do umożliwienia uczenie maszynowe i analizy kondycji danych w chmurze. Contosoclinic jest mała szpital, znajduje się w Stanach Zjednoczonych. Administratorzy sieci szpital Twoich umożliwia uczenie maszynowe Azure lepiej przewidzieć czas pacjenta w czasie dopuszczeniu go, aby zwiększyć wydajność operacyjnej obciążeń i poprawić jakość opieki może udostępnić.

### <a name="predicting-length-of-stay"></a>Przewidywanie czas


Przykładowy scenariusz przypadków użycia używa usługi Azure Machine Learning do prognozowania czas pacjenta nowo dopuszczonych przez porównanie medyczne szczegóły podjętych na pacjenta pobierania zagregowane dane historyczne pacjentów poprzedniej.
Plan zawiera duży zbiór anonimowe medyczne rekordów o prezentację szkolenia i predykcyjnej możliwości rozwiązania. W środowisku produkcyjnym klientów użyje swoich własnych rekordów w celu przeszkolenia dokładniejszych prognoz odzwierciedlające unikatowe szczegółowe informacje w ich środowisku, urządzeń i pacjentów w zakresie.

### <a name="users-and-roles"></a>Użytkownicy i role


**Administrator lokacji — Alexowi**

*Adres e-mail: Alexowi\_SiteAdmin*

Alexowi przez zadanie jest do oceny, technologie, które można zmniejszyć obciążenie sieci lokalnej platformy zarządzania i zmniejszyć koszty związane z zarządzaniem. Alexowi ma zostać obliczenia Azure przez pewien czas, ale ma struggled do skonfigurowania usług, które musi spełnić wymagania zgodności HiTrust do przechowywania danych pacjenta w chmurze. Alexowi została wybrana AI kondycji Azure, aby wdrożyć rozwiązanie gotowe zgodności kondycji, został skierowana wymaganiami w celu spełnienia wymagań klienta dotyczących HiTrust.

**Dane naukowca — Magdalena**

*Poczty e-mail: Magdalena\_DataScientist*

Magdalena jest odpowiedzialny za przy użyciu i tworzenia modeli, które analizują wgląd w nad pacjentem medyczne rekordów. Magdalena korzysta SQL i R statystyczne język programowania do tworzenia modeli jej.

**Analityk bazy danych — Danny**

*Adres e-mail: Danny\_DBAnalyst*

Danny jest kontakt główny dla wszystkich elementów dotyczące programu Microsoft SQL Server, który przechowuje wszystkie dane pacjenta Contosoclinic. Danny jest doświadczonym administratorem serwera SQL, który został ostatnio zapoznanie się z bazą danych SQL Azure.

**Medyczne Dyrektor — Caroline**

Caroline pracuje Krzysztof kierownika linii szczególną uwagę i Magdalena naukowca danych do określenia, jakie czynniki wpływ pacjenta czas.
Caroline używa prognoz z rozwiązania (LOS) krótkoterminowa o długości w celu określenia, czy trwa przydzielania zasobów odpowiednio w sieci szpital Twoich. Na przykład za pomocą pulpitu nawigacyjnego, w tym rozwiązaniu.

**Krzysztof szczególną uwagę wiersza Manager —**

*Poczty e-mail: Krzysztof\_CareLineManager*

Jako osoba bezpośrednio odpowiedzialny za zarządzanie pacjenta wprowadzenia i zrzuty w Contosoclinic, Krzysztof używa prognoz generowane przez rozwiązanie LOS do zapewnienia odpowiedniego personelu zapewnienie opieki pacjentom podczas ich przebywają obiekt.

**Rewident — Han**

*Adres e-mail: Han\_rewidenta*

Han jest certyfikowanych rewidenta, który ma środowisko inspekcji dla obrazu ISO, SOC i HiTrust. Han został dzierżawione Aby przejrzeć Contosoclinc w sieci. Han można przejrzeć macierzy odpowiedzialność klienta dostarczane z rozwiązaniem do zapewnienia, że plan i LOS rozwiązania może służyć do przechowywania, przetwarzania i wyświetlania wrażliwe dane osobowe.


# <a name="design-configuration"></a>Konfiguracja projektu


W tej sekcji zawiera szczegóły domyślnych konfiguracji i środki bezpieczeństwa wbudowane planu opisanej do:

- **POZYSKIWANIE** pierwotnych źródeł danych, takich jak FHIR źródła danych
- **Magazyn** poufnych informacji
- **Analizuj** i przewidywanie wyników
- **INTERAKCJA** z wyników i prognozowanie
- **TOŻSAMOŚĆ** zarządzania rozwiązania
- **ZABEZPIECZENIA** funkcje włączone


## <a name="identity"></a>TOŻSAMOŚCI 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory i kontroli dostępu opartej na rolach (RBAC)


**Uwierzytelniania:**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) jest Microsoft\'s wielodostępne katalogami i tożsamościami zarządzania usługę w chmurze. Wszyscy użytkownicy dla rozwiązania zostały utworzone w usłudze Azure Active Directory, w tym użytkownikom dostęp do bazy danych SQL.



-   Aby aplikacja uwierzytelniania przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki tożsamości organizacji, konfiguruje automatyczne odpowiedzi wykryte podejrzane działania związane z tożsamości organizacji i sprawdza podejrzane zdarzenia i podejmuje odpowiednie działania w celu ich rozwiązywania.

-   [Azure opartej na rolach kontroli dostępu (RBAC)](/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem ukierunkowanych na platformie Azure. Subskrypcja dostęp jest ograniczony do administratora subskrypcji i usługi Azure Key Vault dostęp jest ograniczony do administratora witryny. Silne hasła (12 znaków minimalne z co najmniej jedną literę wielkich/małych, liczby i znak specjalny) są wymagane.

-   Uwierzytelnianie wieloskładnikowe jest obsługiwana po włączeniu przełącznika - enableMFA podczas wdrażania.

-   Hasła wygasają po upływie 60 dni, po włączeniu przełącznika - enableADDomainPasswordPolicy podczas wdrażania.

**Role:**

-   Rozwiązanie sprawia, że użycie [wbudowane role](/azure/role-based-access-control/built-in-roles) do zarządzania dostępem do zasobów.

-   Domyślnie wszyscy użytkownicy są przypisane określonych ról wbudowanych.

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

-   Dane przechowywane w magazynie kluczy m.in.:

    -   Klucz szczegółowe informacje o aplikacji
    -   Pacjenta klucz dostępu do magazynu danych
    -   Parametry połączenia pacjenta
    -   Nazwa tabeli danych pacjenta
    -   Punkt końcowy usługi sieci Web uczenie Maszynowe Azure
    -   Klucz interfejsu API usługi Azure ML

-   Zasady dostępu zaawansowane są konfigurowane na potrzeby
-   Zasady dostępu do magazynu kluczy są zdefiniowane z minimalne uprawnienia wymagane do kluczy i kluczy tajnych
-   Wszystkie klucze i kluczy tajnych w magazynie klucz znajduje się data wygaśnięcia
-   Wszystkie klucze w magazynie kluczy chronionych przez moduł HSM \[typu klucza HSM chronione 2048-bitowego klucza RSA =\]
-   Wszystkich użytkowników/tożsamości udzielono minimum wymaganych uprawnień przy użyciu kontroli dostępu na podstawie ról (RBAC)
-   Aplikacje nie współużytkują magazyn kluczy, chyba że ufają one sobie wzajemnie, a wymagany jest dostęp do tego samego hasła w czasie wykonywania
-   Dzienniki diagnostyczne dla usługi Key Vault są włączone w okres przechowywania dla co najmniej 365 dni.
-   Dozwolonych operacji kryptograficznych dla kluczy są ograniczone do tych wymagane

## <a name="ingest"></a>POZYSKIWANIA 

### <a name="azure-functions"></a>Azure Functions
Rozwiązania została zaprojektowana w celu użycia [usługi Azure Functions](/azure/azure-functions/) przetworzyć długość przykładowe dane nadal używane w pokaz analytics. Utworzono trzy możliwości w funkcjach.

**1. Importowania zbiorczego danych Fi danych klienta**

Korzystając z pokaz skryptu. . \\HealthcareDemo.ps1 z **BulkPatientAdmission** przełącznika w sposób opisany w **wdrażanie i uruchamianie pokaz** wykonywania następujących potoku przetwarzania:
1. **Magazyn obiektów Blob Azure** — przykładowy plik CSV pacjenta dane przekazane do magazynu
2. **Zdarzenie siatki** -zdarzeń publikuje dane do funkcji platformy Azure (importowania zbiorczego — obiekt blob zdarzeń)
3. **Funkcja Azure** — przetwarza i przechowuje dane do magazynu SQL przy użyciu funkcji bezpiecznego — zdarzenia (typ; adres url obiektu blob)
4. **Bazy danych SQL** — magazynu bazy danych dla pacjenta danych przy użyciu tagów klasyfikacji, a proces ML zostało rozpoczęte celu eksperyment uczenia.

![](images/dataflow.png)

Ponadto funkcja azure została zaprojektowana w celu odczytu i chronić wyznaczonych poufnych danych w zestawie danych przykładowych przy użyciu następujących tagów:
- dataProfile = > "ePHI"
- Właściciel = > \<administratora lokacji głównej nazwy użytkownika\>
- środowisko = > "Pilotażu"
- Dział = > "Ekosystem globalne" znakowania została zastosowana do zestawu danych przykładowych, gdzie pacjenta "nazwy" została zidentyfikowana jako zwykły tekst.

**2. Wprowadzenia nowych pacjentów**

Korzystając z pokaz skryptu. . \\HealthcareDemo.ps1 z **BulkPatientadmission** przełącznika w sposób opisany w **wdrażanie i uruchamianie pokaz** wykonywania następujących potoku przetwarzania: ![](images/securetransact.png) 
 **1. Funkcja Azure** wyzwalane, oraz funkcji żądania dla [tokenu elementu nośnego](/rest/api/) z usługi Azure Active directory.

**2. Magazyn kluczy** żądanego dla klucz tajny, który jest skojarzony z żądanego tokenu.

**3. Role Azure weryfikacji żądania i autoryzować żądanie dostępu do magazynu kluczy.

**4. Magazyn kluczy** zwraca klucz tajny, w tym przypadku parametry połączenia bazy danych SQL.

**5. Funkcja Azure** używa parametrów połączenia w celu bezpiecznego łączenia z bazą danych SQL i kontynuuje dalsze przetwarzanie do przechowywania danych ePHI.

Uzyskanie magazynu danych, wspólnego schematu interfejsu API zostało wykonane po Fast opieki zdrowotnej współdziałanie zasobów (FHIR, Wymowa fire). Funkcja podano następujące elementy FHIR programu exchange:

-   [Schemat pacjenta](https://www.hl7.org/fhir/patient.html) omówiono "kto" pacjenta.

-   [Schemat obserwacji](https://www.hl7.org/fhir/observation.html) obejmuje centralną element opieki zdrowotnej, używany do obsługi diagnostyki, monitorować postęp, określić podstawy i wzorce i nawet przechwytywania demograficznych właściwości. 

-   [Wystąpi schematu](https://www.hl7.org/fhir/encounter.html) obejmuje typy napotyka takie jak ambulatory awaryjnego, strona główna kondycji, powodujące i napotka wirtualnego.

-   [Warunek schematu](https://www.hl7.org/fhir/condition.html) zawiera szczegółowe informacje na temat warunek, problem, diagnostyki, lub inne zdarzenia, sytuacji, problem lub klinicznych pojęcie wzrosła do poziomu dotyczą.  



### <a name="event-grid"></a>Event Grid


Rozwiązanie obsługuje Azure zdarzeń siatki, pojedynczą usługę zarządzania routingu wszystkich zdarzeń z dowolnego źródła do dowolnego miejsca docelowego, jeśli:

-   [Zabezpieczeń i uwierzytelniania](/azure/event-grid/security-authentication)

-   [Kontrola dostępu oparta na rolach](/azure/event-grid/security-authentication#management-access-control) dla różnych operacji zarządzania, takich jak wyświetlanie subskrypcji zdarzeń, tworzenie nowych plików i generowania kluczy

-   Inspekcja

## <a name="store"></a>MAGAZYN 

### <a name="sql-database-and-server"></a>Baza danych SQL i serwera 


-   [Funkcji przezroczystego szyfrowania danych (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) udostępnia w czasie rzeczywistym szyfrowania i odszyfrowywania danych przechowywanych w bazie danych SQL Azure, za pomocą klucza przechowywanego w usłudze Azure Key Vault.

-   [Oceny luk w zabezpieczeniach SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) łatwo skonfigurować narzędzie, które odnajdywanie, śledzenia i skorygować potencjalnych luk w bazie danych.

-   [Wykrywanie zagrożeń bazy danych SQL](/azure/sql-database/sql-database-threat-detection) włączone.

-   [SQL Database Auditing](/azure/sql-database/sql-database-auditing) włączone.

-   [Baza danych SQL, metryki i rejestrowania diagnostycznego](/azure/sql-database/sql-database-metrics-diag-logging) włączone.

-   [Reguły zapory poziomu serwera i bazy danych](/azure/sql-database/sql-database-firewall-configure) zostały.

-   [Zawsze zaszyfrowane kolumny](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) służą do ochrony pacjenta, środkowej, podanie imion i nazwisk.
    Ponadto szyfrowania kolumny bazy danych również używa usługi Azure Active Directory (AD) do uwierzytelniania aplikacji z bazą danych SQL Azure.

-   Dostęp do bazy danych SQL i programu SQL Server jest skonfigurowany zgodnie z zasadą najniższych uprawnień.

-   Tylko adresy IP wymagane są dozwolone dostęp za pośrednictwem zapory SQL.

### <a name="storage-accounts"></a>Konta magazynu


-   [Tylko dla protokołu TLS/SSL transferu danych w ruchu](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   Dostęp anonimowy jest niedozwolony dla kontenerów.

-   Reguły alertów są skonfigurowane do śledzenia anonimowe działania.

-   HTTPS jest wymagana do uzyskiwania dostępu do zasobów konta magazynu.

-   Dane żądania uwierzytelniania jest rejestrowane i monitorowane.

-   Dane w magazynie obiektów Blob jest szyfrowane, gdy.

## <a name="analyze"></a>ANALIZOWANIE

### <a name="machine-learning"></a>Usługa Machine Learning


-   [Rejestrowanie jest włączone](/azure/machine-learning/studio/web-services-logging) do uczenia maszynowego usługi sieci web.
- przy użyciu [uczenia maszynowego](/azure/machine-learning/desktop-workbench/experimentation-service-configuration) workbench wymaga opracowania eksperymentów, który zapewnia możliwość prognozowania z zestawem rozwiązania. [Integrowanie workbench](/azure/machine-learning/desktop-workbench/using-git-ml-project) może usprawnić zarządzanie eksperymentów.

## <a name="security"></a>ZABEZPIECZENIA

### <a name="azure-security-center"></a>Azure Security Center
- [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) zapewnia scentralizowane widok stanu zabezpieczeń wszystkich zasobów platformy Azure. Jeden rzut oka należy sprawdzić, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowany i można szybko zidentyfikować wszystkie zasoby, które wymagają uwagi. 

- [Klasyfikator Azure](/azure/advisor/advisor-overview) jest konsultanta spersonalizowane chmury, ułatwiająca należy stosować najlepsze rozwiązania w celu zoptymalizowania wdrożeń platformy Azure. Analizuje konfigurację zasobów i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu wysokiej dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

### <a name="application-insights"></a>Application Insights
- [Usługa Application Insights](/azure/application-insights/app-insights-overview) jest rozszerzalną usługę zarządzania wydajności aplikacji (APM) dla deweloperów sieci web na wielu platformach. Użyj tej usługi do monitorowania aplikacji sieci Web na żywo. Wykrycia anomalii wydajności. Obejmuje ona zaawansowane narzędzia analityczne, dzięki którym możesz diagnozować problemy i zrozumieć sposób korzystania z aplikacji przez użytkowników. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

### <a name="azure-alerts"></a>Alerty systemu Azure
- [Alerty oferują metodą monitorowania usług platformy Azure i umożliwiają konfigurowanie warunków nad danymi. Alerty zapewniają również powiadomienia, gdy warunek alertu jest zgodna z danych monitorowania.

### <a name="operations-management-suite-oms"></a>Operations Management Suite (OMS)
[Operations Management Suite (znanej także jako OMS)](/azure/operations-management-suite/operations-management-suite-overview) to zbiór usług zarządzania.

-   Obszar roboczy jest włączona dla Centrum zabezpieczeń

-   Obszar roboczy jest włączony do monitorowania obciążenia

-   Monitorowanie obciążenia jest włączone dla:

    -   Tożsamość i dostęp

    -   Bezpieczeństwo i inspekcji

    -   Azure SQL DB Analytics

    -   [Analiza aplikacji sieci Web Azure](/azure/log-analytics/log-analytics-azure-web-apps-analytics) rozwiązania

    -   Key Vault Analytics

    -   Śledzenie zmian

-   [Łącznik aplikacji Insights (wersja zapoznawcza)](/azure/log-analytics/log-analytics-app-insights-connector) jest włączone

-   [Analiza dzienników działania](/azure/log-analytics/log-analytics-activity) jest włączone
