---
title: Plan analizy kondycji platformy Azure
description: Wskazówki dotyczące wdrażania planu analizy kondycji HIPAA/HITRUST
services: security
documentationcenter: na
author: RajeevRangappa
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.topic: article
ms.date: 07/23/2018
ms.author: rarangap
ms.openlocfilehash: 70721b8bfbecaf554a9502b9ec3417fc8e561b3f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885948"
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Zabezpieczenia platformy Azure i zgodności planu — dane kondycji HIPAA/HITRUST i SI

## <a name="overview"></a>Omówienie

**Zabezpieczenia platformy Azure i zgodności planu — dane kondycji HIPAA/HITRUST i SI oferuje setką kompleksowych wdrożenie rozwiązania IaaS i PaaS platformy Azure, aby zademonstrować pozyskiwanie, przechowywanie, analizowanie, wchodzić w interakcje, tożsamość i bezpiecznie Wdrażaj rozwiązania z danymi kondycji podczas możliwość spełnianie wymagań dotyczących zgodności w branży. Planu pomaga przyspieszyć wdrażanie chmury i wykorzystania dla klientów korzystających z danych, która reguluje.**

Zabezpieczenia platformy Azure i zgodności planu — dane kondycji HIPAA/HITRUST i SI planu oferuje narzędzia i wskazówki, aby pomóc we wdrażaniu bezpiecznej, Health Insurance Portability i Accountability Act (HIPAA) i kondycji informacji zaufania Alliance (HITRUST) gotowe Platforma jako usługa (PaaS) środowisko do umożliwiając pozyskiwanie, przechowywanie, analizowanie i interakcję z rekordami medycznymi osobistych, jak i osobiste innego niż w środowisku chmury bezpieczne, obejmujące wiele warstw, wdrażane jako rozwiązania end-to-end. 

Rozwiązanie IaaS pokażemy, jak migrować rozwiązanie SQL na podstawie lokalnych na platformę Azure oraz do wykonywania uprzywilejowanego dostępu do stacji roboczej (PAW) do bezpiecznego zarządzania usługami w chmurze i rozwiązań. IaaS programu SQL Server dodaje potencjalnych eksperymentowania, które dane są importowane do maszyny Wirtualnej SQL IaaS i że maszyna wirtualna używa MSI dostęp uwierzytelniony do interakcji z usługą SQL Azure PaaS. Zarówno te przedstawiono typowe architektury referencyjnej i upraszcza wdrożenie systemu Microsoft Azure. Ta architektura podana przedstawiono rozwiązanie do potrzeb organizacji szukających podejście oparte na chmurze do zmniejszenia obciążenia i obniżyć koszty wdrożenia.

![](images/components.png)

To rozwiązanie jest przeznaczony do korzystania z zestawu danych przykładowych, sformatowany przy użyciu szybkiego opieki zdrowotnej współdziałanie zasobów (FHIR), na całym świecie standard do wymiany informacji opieki zdrowotnej w formie elektronicznej i zapisz go w bezpieczny sposób. Następnie klienci mogą używać usługi Azure Machine Learning Studio, aby móc korzystać z narzędzia do analizy biznesowej zaawansowane i analizy, aby zapoznać się z prognozy na przykładowych danych. Na przykład tego rodzaju eksperymentów, które mogą ułatwić Azure Machine Learning Studio planu zawiera przykładowy zestaw danych, skrypty i narzędzia do przewidywania długości pobytu pacjenta w funkcji szpitali. 

Ten plan ma służyć jako podstawa modułowej klienci będą mogli dostosować się do określonych wymagań, tworzenie nowych Azure eksperymentów uczenia maszynowego do rozwiązywania obu scenariuszy przypadków użycia efektywności klinicznej i operacyjnej. Zaprojektowano go jako bezpieczne i zgodne, po wdrożeniu; Jednak klienci są zobowiązani poprawnie Konfigurowanie ról i wykonywania żadnych modyfikacji. Pamiętaj o następujących kwestiach:

-   Ten plan zapewnia plan bazowy, aby pomóc klientom w HITRUST dzięki platformie Microsoft Azure i środowiskiem HIPAA.

-   Mimo że planu zaprojektowano tak, aby być dopasowane do wymogów Ustaw HIPAA i HITRUST (za pośrednictwem z wspólnej struktury zabezpieczeń — CSF), go nie należy rozważyć zgodne, dopóki certyfikowanych przez zewnętrznego audytora na wymagania dotyczące certyfikacji wymogów Ustaw HIPAA i HITRUST.

-   Klienci są zobowiązani do przeprowadzania odpowiednie przeglądy zabezpieczeń i zgodności dowolnego rozwiązania utworzone przy użyciu tej architektury podstawowe.

## <a name="deploying-the-automation"></a>Wdrażanie usługi automation

- Aby wdrożyć to rozwiązanie, wykonaj instrukcje podane w [ze wskazówkami dotyczącymi wdrażania](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md). 

- Aby uzyskać szybki przegląd sposobu działania tego rozwiązania, Obejrzyj to [wideo](https://aka.ms/healthblueprintvideo) objaśnia i demonstruje wprowadzone jej wdrożenia.

- Często zadawanych pytań znajdują się w [— często zadawane pytania](https://aka.ms/healthblueprintfaq) wskazówki.

-   **Diagram architektury.** Na diagramie przedstawiono architekturę referencyjną, używany do planu, a przykładowy scenariusz przypadków użycia.

-   [Rozszerzenie IaaS](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/README%20IaaS.md) to rozwiązanie pokażemy, jak migrować rozwiązanie SQL na podstawie lokalnych na platformę Azure oraz do wykonywania uprzywilejowanych stacji roboczej z dostępem do bezpiecznego zarządzania usługami w chmurze i rozwiązań. 

## <a name="solution-components"></a>Składniki rozwiązania


Podstawowa architektura składa się z następujących składników:

-   **[Model zagrożeń](https://aka.ms/healththreatmodel)**  model zagrożeń kompleksowe znajduje się w formacie tm7 do użytku z programem [narzędzie do modelowania zagrożeń firmy Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=49168), pokazujący składniki rozwiązania, dane przepływają między nimi i zaufanie granice. Model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas tworzenia składników usługi Machine Learning Studio lub innych modyfikacji.

-   **[Macierz implementacji klienta](https://aka.ms/healthcrmblueprint)**  skoroszytu programu Microsoft Excel zawiera odpowiednie wymagania HITRUST i wyjaśniono, jak firma Microsoft i klient jest odpowiedzialny za spotkania każdej z nich.

-   **[Przegląd kondycji.](https://aka.ms/healthreviewpaper)** Rozwiązanie został zrecenzowany przez Coalfire systems, Inc. Zgodność kondycji (ustawy HIPAA i HITRUST) wskazówki dotyczące wdrażania i przejrzyj zawiera rewidenta\'przeglądu s rozwiązanie i uwagi dotyczące przekształcania planu wdrożenie gotowe do produkcji.

## <a name="architectural-diagram"></a>Diagram architektury


![](images/ra2.png)

## <a name="roles"></a>Role


Planu definiuje dwie role użytkowników administracyjnych (operatorów) i trzy role dla użytkowników w szpitalu zarządzania i opiece nad pacjentami. Szósty rola jest zdefiniowana dla Audytor do oceny zgodności z ustawy HIPAA i innych przepisów. Azure opartej na rolach kontrola dostępu (RBAC) umożliwia precyzyjne ukierunkowanych zarządzanie dostępem dla każdego użytkownika rozwiązania przy użyciu ról wbudowanych i niestandardowych. Zobacz [wprowadzenie opartej na rolach kontrola dostępu w witrynie Azure portal](https://docs.microsoft.com/azure/role-based-access-control/overview) i [wbudowane role kontroli dostępu opartej na rolach na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) szczegółowe informacje dotyczące funkcji RBAC, role i uprawnienia.

### <a name="site-administrator"></a>Administrator witryny


Administrator lokacji jest odpowiedzialny za subskrypcję platformy Azure przez klienta. Ich kontrolować ogólne wdrażanie, ale nie mają dostępu do kartoteki pacjentów.

-   Domyślne przypisania roli: [Właściciel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Przypisania roli niestandardowej: ND

-   Zakres: Subskrypcja

### <a name="database-analyst"></a>Analityk bazy danych

Analityk bazy danych zarządza wystąpieniem programu SQL Server i bazy danych.
One nie mają dostępu do kartoteki pacjentów.

-   Przypisań ról wbudowanych: [Współautor bazy danych SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [Współautor serwera SQL Server](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Przypisania roli niestandardowej: ND

-   Zakres: ResourceGroup

### <a name="data-scientist"></a>Analityk danych


Analityk danych działa w usłudze Azure Machine Learning Studio. Mogą importować, eksportować i zarządzanie danymi i uruchamianie raportów. Analityk danych ma dostęp do danych pacjentów, ale nie ma uprawnienia administracyjne.

-   Przypisań ról wbudowanych: [Współautor konta magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Przypisania roli niestandardowej: ND

-   Zakres: ResourceGroup

### <a name="chief-medical-information-officer-cmio"></a>Dyrektor ds. informacji medycznych (ds. informacji medycznych)


Ds. informacji medycznych przełamywaniem barier między informatyką/technologią a opieki zdrowotnej specjalistów w branży opieki zdrowotnej organizacji. Jego obowiązków zwykle umieszcza się przy użyciu analizy, aby określić, jeśli zasoby są odpowiednio przydzielane w organizacji.

-   Przypisań ról wbudowanych: Brak

### <a name="care-line-manager"></a>Menedżer wierszy opieki


Menedżer wierszy opieki jest bezpośrednio zaangażowany w opiekę pacjentów.
Ta rola wymaga monitorowania stanu poszczególnych pacjentów oraz zapewniania, że jest dostępny personel spełniający określone wymagania opieki nad pacjentami. Menedżer wierszy obsługi jest odpowiedzialny za dodawanie i aktualizowania kartoteki pacjentów.

-   Przypisań ról wbudowanych: Brak

-   Przypisania roli niestandardowej: Ma uprawnienia do uruchamiania HealthcareDemo.ps1 celu obu przyjęcia pacjenta i wykonywania.

-   Zakres: ResourceGroup

### <a name="auditor"></a>Audytor


Audytor ocenia rozwiązania pod kątem zgodności. Nie mają bezpośredniego dostępu do sieci.

-   Przypisań ról wbudowanych: [Czytelnik](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Przypisania roli niestandardowej: ND

-   Zakres: Subskrypcja

## <a name="example-use-case"></a>Przykład przypadek użycia


Przypadek użycia przykład dołączone do tego planu ilustruje, jak planu można włączyć uczenia maszynowego i analizy danych kondycji w chmurze. Contosoclinic jest mały szpitalu zamieszkania w Stanach Zjednoczonych. Sieci szpitali Administratorzy chcą używać usługi Azure Machine Learning Studio, aby lepiej przewidywania długości pobytu pacjenta w czasie dopuszczeniu go, aby zwiększyć efektywność obciążenia pracą i poprawić jakość opieki, które można podać.

### <a name="predicting-length-of-stay"></a>Przewidywanie czasu pobytu


Przykładowy scenariusz przypadków użycia używa usługi Azure Machine Learning Studio do przewidywania długości pobytu pacjenta nowo dopuszczonych przez porównanie szczegóły medycznych, utworzono pacjenta pozwala szybko pobierać do zagregowanych danych historycznych, z poprzedniego pacjentów.
Planu zawiera duży zestaw anonimowe dokumentacji medycznej, aby zademonstrować szkolenia i predykcyjne możliwości rozwiązania. W przypadku wdrożenia produkcyjnego klienci będą używać własnych rekordów to w opracowywaniu rozwiązań dla dokładniejszych prognoz odzwierciedlający unikatowe szczegółowe informacje na ich środowisko, urządzeń i pacjentów.

### <a name="users-and-roles"></a>Użytkownicy i role


**Administrator witryny — Alex**

*Adres e-mail: Alex\_SiteAdmin*

Alex przez zadanie jest ocena technologii, które można zmniejszyć obciążenie zarządzanie siecią w środowisku lokalnym i zmniejszyć koszty związane z zarządzaniem. Alex ma zostać ocena platformy Azure przez pewien czas, ale ma struggled do skonfigurowania usług, które musi spełnić wymagania dotyczące zgodności HiTrust do przechowywania danych pacjentów w chmurze. Alex została wybrana Azure AI kondycji, aby wdrożyć rozwiązanie kondycja gotowych do użycia zgodności, rozpoczęto wymagania, aby spełniać wymagania klientów HiTrust.

**Analityk danych — Magdalena**

*Adres e-mail: Debra\_DataScientist*

Magdalena odpowiada za używanie i tworzenie modeli, które analizują dokumentacji medycznej, aby zapewnić wgląd w opiece nad pacjentami. Magdalena korzysta z języków SQL i statystycznego języka programowania R na potrzeby tworzenia jej modeli.

**Analityk bazy danych — Danny**

*Adres e-mail: Danny\_DBAnalyst*

Danny jest główną osobą kontaktową dla wszystkich elementów, dotyczące programu Microsoft SQL Server, która przechowuje dane pacjentów Contosoclinic. Danny jest doświadczonym administratorem serwera SQL, który został ostatnio zapoznanie się z bazą danych SQL Azure.

**Dyrektor ds — Caroline**

Caroline pracuje Chris Menedżer wierszy Care i Magdalena analityk danych, aby określić, które czynniki mają wpływ pacjentów czasu pobytu.
Caroline używa przewidywań rozwiązania (LOS) typu "długość pobytu", aby określić, jeśli zasoby są odpowiednio przydzielane w sieci szpitali. Na przykład za pomocą pulpitu nawigacyjnego, w tym rozwiązaniu.

**Care Menedżer wierszy — Chris**

*Adres e-mail: Chris\_CareLineManager*

Jako osoba bezpośrednio odpowiedzialni za zarządzanie przyjęcia pacjenta, a zrzuty w Contosoclinic, Krzysztof używa prognoz wygenerowanych przez rozwiązanie LOS aby upewnić się, że personelem są dostępne w zapewnienie obsługi pacjentów, gdy są one pozostając w obiekt.

**Audytor — Hanowi**

*Adres e-mail: Hanowi\_rewidenta*

Hanowi jest certyfikowanym rewidenta, który ma doświadczenie inspekcji dla ISO, SOC i HiTrust. Hanowi został zatrudniony do przeglądania sieci Contosoclinc firmy. Hanowi przejrzeć macierzy odpowiedzialność klienta dostarczane za pomocą rozwiązania do upewnij się, że rozwiązanie LOS i planu może służyć do przechowujących, przetwarzających i wyświetlania wrażliwe dane osobowe.


## <a name="design-configuration"></a>Konfiguracja projektu


Tej sekcji opisano szczegółowo domyślnych konfiguracji i środków bezpieczeństwa wbudowaną planu opisanych na:

- **POZYSKIWANIE** pierwotnych źródeł danych, takich jak FHIR źródła danych
- **Magazyn** poufnych informacji
- **Analizuj** i przewidywanie wyników
- **INTERAKCJA** wyniki i prognozy
- **TOŻSAMOŚĆ** zarządzania rozwiązania
- **ZABEZPIECZENIA** funkcje włączone


## <a name="identity"></a>TOŻSAMOŚĆ 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Usługa Azure Active Directory i kontroli dostępu opartej na rolach (RBAC)


**Uwierzytelnianie:**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) firmy Microsoft jest\'s wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami usługi zarządzania. Wszyscy użytkownicy dotyczące rozwiązania zostały utworzone w usługi Azure Active Directory, w tym użytkowników uzyskujących dostęp do bazy danych SQL.



-   Do aplikacji uwierzytelniania przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Usługa Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki mające wpływ na tożsamości w organizacji, umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości w organizacji i bada podejrzanych zdarzeń i Trwa odpowiednią akcję, aby je rozwiązać.

-   [Usługa Azure opartej na rolach kontrola dostępu (RBAC)](/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem ukierunkowane na platformie Azure. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji i usługi Azure Key Vault dostęp jest ograniczony do administratora witryny. Silne hasła (12 znaków minimalne z co najmniej jedną literę wielkich/małych, liczby i znak specjalny) są wymagane.

-   Uwierzytelnianie wieloskładnikowe jest obsługiwane po włączeniu przełącznika - enableMFA podczas wdrażania.

-   Hasła wygasają po upływie 60 dni, po włączeniu przełącznika - enableADDomainPasswordPolicy podczas wdrażania.

**Role:**

-   Rozwiązanie korzysta z [wbudowane role](/azure/role-based-access-control/built-in-roles) zarządzanie dostępem do zasobów.

-   Wszyscy użytkownicy są domyślnie przypisane określone wbudowane role.

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

-   Zawiera dane przechowywane w usłudze Key Vault:

    -   Klucz szczegółowe informacje o aplikacji
    -   Pacjentów klucz dostępu do magazynu danych
    -   Parametry połączenia pacjentów
    -   Nazwa tabeli danych pacjentów
    -   Punkt końcowy usługi sieci Web Azure ML
    -   Klucz interfejsu API usługi uczenie Maszynowe systemu Azure

-   Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb
-   Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych
-   Wszystkie klucze i wpisy tajne w usłudze Key Vault ma datę wygaśnięcia
-   Wszystkie klucze w usłudze Key Vault są chronione przez moduł HSM \[typu klucza = 2048 chronione przez moduł HSM-bitowego klucza RSA\]
-   Wszystkich użytkowników/tożsamości są przyznawane minimalnych wymaganych uprawnień przy użyciu kontroli dostępu na podstawie ról (RBAC)
-   Aplikacje nie należy udostępniać usługi Key Vault, chyba że ufają one sobie wzajemnie, a potrzebują dostępu do tych samych kluczy tajnych w czasie wykonywania
-   Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
-   Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane

## <a name="ingest"></a>POZYSKIWANIA 

### <a name="azure-functions"></a>Azure Functions
Rozwiązania zaprojektowano tak, aby użyć [usługi Azure Functions](/azure/azure-functions/) do przetworzenia długość przykładowe dane nadal używane w wersji demonstracyjnej usługi analytics. Utworzono trzy możliwości w funkcjach.

**1. Import zbiorczy danych phi danych klienta**

Korzystając z pokaz skryptu. . \\HealthcareDemo.ps1 z **BulkPatientAdmission** przełącznika zgodnie z opisem w **wdrażanie i uruchamianie demonstracyjnego** wykonuje następujące potoku przetwarzania:
1. **Usługa Azure Blob Storage** — przykładowy plik CSV pacjentów dane przekazane do magazynu
2. **Usługa Event Grid** -publikuje zdarzenie dane do funkcji platformy Azure (importowania zbiorczego — obiekt blob zdarzeń)
3. **Funkcja platformy Azure** — przetwarza i przechowuje dane w magazynie SQL przy użyciu funkcji bezpiecznego — zdarzenia (typ; adres url usługi blob)
4. **Baza danych SQL** — magazynu bazy danych dla danych pacjentów przy użyciu tagów dla klasyfikacji i rozpocznie się proces uczenia Maszynowego kompilowanie celu eksperymentu szkolenia.

![](images/dataflow.png)

Ponadto funkcję platformy azure został zaprojektowany do odczytywania i chronić wyznaczonym dane poufne w zestawie danych przykładowych, za pomocą następujących znaczników:
- dataProfile = > "ePHI"
- Właściciel = > \<administratora lokacji głównej nazwy użytkownika\>
- środowisko = > "Pilotażowego"
- Dział = > "Ekosystemu" znakowania został zastosowany do zestawu danych przykładowych, gdzie pacjenta "nazwy" została zidentyfikowana jako zwykły tekst.

**2. Dopuszczenie nowych pacjentów**

Korzystając z pokaz skryptu. . \\HealthcareDemo.ps1 z **BulkPatientadmission** przełącznika zgodnie z opisem w **wdrażanie i uruchamianie demonstracyjnego** wykonuje następujące potoku przetwarzania: ![](images/securetransact.png)
**1. Funkcja Azure** wyzwolone, a funkcja żądań dla [tokenu elementu nośnego](/rest/api/) z usługi Azure Active directory.

**2. Usługa Key Vault** żądane dla klucza tajnego, który jest skojarzony z żądanego tokenu.

**3. Role usługi Azure** weryfikacji żądania i autoryzować żądania dostępu do usługi Key Vault.

**4. Usługa Key Vault** zwraca klucz tajny, w tym przypadku ciąg połączenia bazy danych SQL.

**5. Funkcja Azure** używa parametrów połączenia w celu nawiązania bezpiecznego połączenia bazy danych SQL i kontynuuje dalszego przetwarzania do przechowywania danych ePHI.

Uzyskanie magazynu danych, wspólnego schematu interfejsu API został wdrożony po szybko opieki zdrowotnej współdziałanie zasobów (FHIR Wymowa fire). Funkcja podano następujące elementy programu exchange FHIR:

-   [Schemat pacjentów](https://www.hl7.org/fhir/patient.html) obejmuje "kto" informacji na temat pacjenta.

-   [Schemat obserwacji](https://www.hl7.org/fhir/observation.html) obejmuje ważnym elementem w obszarze opieki zdrowotnej, używane do obsługi diagnostyki, monitorować postęp, określić podstawy i wzorców i nawet przechwytywania charakterystyki demograficznych. 

-   [Wystąpi schematu](https://www.hl7.org/fhir/encounter.html) obejmuje typy napotka, takich jak ambulatory awaryjnego, home kondycji, powodujące i napotka wirtualnego.

-   [Warunek schematu](https://www.hl7.org/fhir/condition.html) obejmuje szczegółowe informacje o warunku, problem, diagnostyki, lub inne zdarzenia, sytuacji, problem lub clinical koncepcji, która wzrosła poziom zainteresowania.  



### <a name="event-grid"></a>Event Grid


Rozwiązanie obsługuje usługi Azure Event Grid, pojedynczą usługę zarządzania routingiem wszystkich zdarzeń z dowolnego źródła do dowolnego miejsca docelowego, udostępniając:

-   [Zabezpieczenia i uwierzytelnianie](/azure/event-grid/security-authentication)

-   [Kontrola dostępu oparta na rolach](/azure/event-grid/security-authentication#management-access-control) dla różnych operacji zarządzania, takich jak lista subskrypcji zdarzeń, tworzenie nowych plików i generowanie kluczy

-   Inspekcja

## <a name="store"></a>MAGAZYN 

### <a name="sql-database-and-server"></a>SQL Database i serwera 


-   [Przezroczyste szyfrowanie danych (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) udostępnia w czasie rzeczywistym szyfrowanie i odszyfrowywanie danych przechowywanych w bazie danych SQL Azure, za pomocą klucza przechowywanego w usłudze Azure Key Vault.

-   [Ocena luk w zabezpieczeniach SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) można łatwo skonfigurować narzędzie, które można odnaleźć, śledzenie i korygowanie potencjalnych luk w bazie danych.

-   [Wykrywanie zagrożeń SQL Database](/azure/sql-database/sql-database-threat-detection) włączone.

-   [SQL Database Auditing](/azure/sql-database/sql-database-auditing) włączone.

-   [Metryki bazy danych SQL i rejestrowania diagnostycznego](/azure/sql-database/sql-database-metrics-diag-logging) włączone.

-   [Reguły zapory poziomu serwera i bazy danych](/azure/sql-database/sql-database-firewall-configure) zostały.

-   [Zawsze zaszyfrowane kolumny](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) są używane do ochrony pacjentów nazwy pierwszego, drugie imię i nazwisko.
    Ponadto szyfrowania kolumny bazy danych również używa usługi Azure Active Directory (AD) do uwierzytelniania aplikacji w usłudze Azure SQL Database.

-   Dostęp do bazy danych SQL Database i programu SQL Server jest skonfigurowany zgodnie z zasadą najniższych uprawnień.

-   Tylko wymaganych adresów IP będą miały dostęp, za pośrednictwem zapory SQL.

### <a name="storage-accounts"></a>Konta magazynu


-   [Transferu danych w ruchu przy użyciu protokołu TLS/SSL tylko](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   Dostęp anonimowy jest niedozwolone dla kontenerów.

-   Reguły alertów są skonfigurowane do śledzenia działania anonimowe.

-   Protokół HTTPS jest wymagana do uzyskania dostępu do zasobów konta magazynu.

-   Dane żądania uwierzytelniania jest rejestrowany i monitorowane.

-   Dane w magazynie obiektów Blob są szyfrowane w stanie spoczynku.

## <a name="analyze"></a>ANALIZOWANIE

### <a name="machine-learning"></a>Usługa Machine Learning


- [Rejestrowanie jest włączone](/azure/machine-learning/studio/web-services-logging) usługi internetowe Machine Learning Studio.
- Za pomocą [usługi Machine Learning Studio](/azure/machine-learning/studio/what-is-ml-studio) wymaga tworzenie eksperymentów, które zapewniają możliwość przewidywania do zestawu rozwiązania.

## <a name="security"></a>ZABEZPIECZENIA

### <a name="azure-security-center"></a>Azure Security Center
- [Usługa Azure Security Center](https://azure.microsoft.com/services/security-center/) zapewnia scentralizowany widok stanu bezpieczeństwa wszystkich Twoich zasobów platformy Azure. Na pierwszy rzut oka może Sprawdź, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowane i można szybko zidentyfikować wszelkie zasoby, które wymagają uwagi. 

- [Usługa Azure Advisor](/azure/advisor/advisor-overview) to spersonalizowany konsultant ds. chmury, który ułatwia stosowanie najlepszych rozwiązań do optymalizacji wdrożeń platformy Azure. Analizuje konfigurację zasobów i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu wysokiej dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

### <a name="application-insights"></a>Application Insights
- [Usługa Application Insights](/azure/application-insights/app-insights-overview) to rozszerzalna Usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci web na wielu platformach. Użyj tej usługi do monitorowania aplikacji internetowej na żywo. Wykrywa anomalie wydajność. Obejmuje ona zaawansowane narzędzia analityczne, dzięki którym możesz diagnozować problemy i zrozumieć sposób korzystania z aplikacji przez użytkowników. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

### <a name="azure-alerts"></a>Alerty platformy Azure
- [Alerty](/azure/azure-monitor/platform/alerts-metric) oferują metodą monitorowania usług systemu Azure i pozwala na skonfigurowanie warunków nad danymi. Alerty zapewniają również powiadomienia, gdy warunek alertu jest zgodna z danych monitorowania.

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
[Dzienniki platformy Azure Monitor](/azure/operations-management-suite/operations-management-suite-overview) to zbiór usług zarządzania.

-   Obszar roboczy jest włączona dla usługi Security Center

-   Obszar roboczy jest włączony dla monitorowania obciążenia

-   Monitorowanie obciążenia jest włączona dla:

    -   Tożsamość i dostęp

    -   Security and Audit

    -   Azure SQL DB Analytics

    -   [Funkcja analizy usługi Azure WebApp](/azure/log-analytics/log-analytics-azure-web-apps-analytics) rozwiązania

    -   Key Vault Analytics

    -   Śledzenie zmian

-   [Łącznik usługi Application Insights (wersja zapoznawcza)](/azure/log-analytics/log-analytics-app-insights-connector) jest włączona

-   [Analiza dzienników aktywności](/azure/log-analytics/log-analytics-activity) jest włączona
