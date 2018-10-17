---
title: Zabezpieczanie własnego magazynu kluczy platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzaj uprawnieniami dostępu do usługi key vault do zarządzania usługi Azure Key Vault, klucze i wpisy tajne. Model uwierzytelniania i autoryzacji dla magazynu kluczy i sposób zabezpieczania własnego magazynu kluczy.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 4a1de3c011f1f8cfa1ea9246efad4ebb7f9e3a76
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364527"
---
# <a name="secure-your-key-vault"></a>Zabezpieczanie własnego magazynu kluczy
Usługa Azure Key Vault to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne (takie jak certyfikaty, parametry połączenia, hasła). Ponieważ te dane są poufne i krytyczne dla działania, musi zostać zabezpieczony dostęp do Twoich magazynów kluczy, dzięki czemu tylko autoryzacji aplikacji i użytkowników, aby uzyskać dostęp. 

Ten artykuł zawiera omówienie modelu dostępu do magazynu kluczy. Wyjaśnia uwierzytelnianie i autoryzację, a w tym artykule opisano sposób zabezpieczania dostępu do magazynu kluczy.

## <a name="overview"></a>Przegląd
Dostęp do magazynu kluczy jest kontrolowany za pośrednictwem dwóch oddzielnych interfejsów: płaszczyzny zarządzania i płaszczyzny danych. W przypadku obu płaszczyzn odpowiednie uwierzytelnianie i autoryzacja są wymagane zanim obiekt wywołujący (użytkownik lub aplikacja) mogą uzyskać dostęp do magazynu kluczy. Uwierzytelnianie ustala tożsamość elementu wywołującego, podczas gdy autoryzacja określa operacje, które może wykonywać obiekt wywołujący.

Na potrzeby uwierzytelniania płaszczyzna zarządzania i płaszczyzna danych używają usługi Azure Active Directory. Na potrzeby autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach (RBAC), natomiast płaszczyzna danych używa zasad dostępu magazynu kluczy.

Oto krótki przegląd omówionych tematów:

[Uwierzytelnianie przy użyciu usługi Azure Active Directory](#authentication-using-azure-active-directory) — w tej sekcji opisano, jak obiekt wywołujący uwierzytelnia się za pomocą usługi Azure Active Directory w celu uzyskania dostępu do magazynu kluczy za pośrednictwem płaszczyzny zarządzania i płaszczyzny danych. 

[Płaszczyzna zarządzania i płaszczyzna danych](#management-plane-and-data-plane) — płaszczyzna zarządzania i płaszczyzna danych to dwie płaszczyzny używane do uzyskiwania dostępu do magazynu kluczy. Każda płaszczyzna dostępu obsługuje konkretne operacje. W tej sekcji opisano punkty końcowe dostępu, obsługiwane operacje i metody kontroli dostępu używane przez każdą płaszczyznę. 

[Kontrola dostępu do płaszczyzny zarządzania](#management-plane-access-control) — w tej sekcji omówiono kontrolowanie dostępu do operacji płaszczyzny zarządzania za pomocą kontroli dostępu opartej na rolach.

[Kontrola dostępu do płaszczyzny danych](#data-plane-access-control) — w tej sekcji opisano sposób kontrolowania dostępu do płaszczyzny danych za pomocą zasad dostępu magazynu kluczy.

[Przykład](#example) — w tym przykładzie przedstawiono sposób konfigurowania kontroli dostępu do magazynu kluczy w celu umożliwienia trzem różnym zespołom (zespół ds. zabezpieczeń, deweloperom/operatorom i audytorów) do wykonywania określonych zadań w celu opracowywania, zarządzania i monitorowania aplikacji na platformie Azure.

## <a name="authentication-using-azure-active-directory"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory
Po utworzeniu magazynu kluczy w subskrypcji platformy Azure ma automatycznie skojarzone z dzierżawą usługi Azure Active Directory dla subskrypcji. Wszystkie podmioty wywołujące (Użytkownicy i aplikacje) muszą być zarejestrowane w tej dzierżawie i muszą zostać uwierzytelnione na dostęp do magazynu kluczy. To wymaganie dotyczy zarówno płaszczyzny i dane dostępu do płaszczyzny zarządzania. W obu przypadkach aplikacja może uzyskiwać dostęp do magazynu kluczy na dwa sposoby:

* **dostęp użytkownika i aplikacji** — używane z aplikacji uzyskujących dostęp do magazynu kluczy w imieniu zalogowanego użytkownika. Program Azure PowerShell i witryny Azure portal to przykłady tego typu dostępu. Istnieją dwa sposoby udzielania dostępu użytkownikom: 
- Udzielić dostępu do użytkowników, dzięki czemu mogą uzyskiwać dostęp do usługi key vault z poziomu dowolnej aplikacji.
- Tylko wtedy, gdy korzystają z określonej aplikacji (nazywane złożone tożsamości), należy udzielić użytkownikowi dostępu do magazynu kluczy.

* **dostęp tylko do aplikacji** — używane z aplikacjami, które działały jako demon usługi lub zadania w tle. Tożsamości aplikacji udzielany jest dostęp do magazynu kluczy.

W przypadku obu typów aplikacji aplikacja uwierzytelnia się za pomocą usługi Azure Active Directory przy użyciu dowolnej z [obsługiwanych metod uwierzytelniania](../active-directory/develop/authentication-scenarios.md) i uzyskuje token. Używana metoda uwierzytelniania zależy od typu aplikacji. Następnie aplikacja używa tego tokenu i wysyła żądanie interfejsu API REST do magazynu kluczy. Żądania płaszczyzny zarządzania są przesyłane za pośrednictwem punktu końcowego usługi Azure Resource Manager. W przypadku uzyskiwania dostępu do płaszczyzny danych aplikacja komunikuje się bezpośrednio z punktem końcowym magazynu kluczy. Patrz szczegółowe informacje na temat [całego przepływu uwierzytelniania](../active-directory/develop/v1-protocols-oauth-code.md). 

Nazwa zasobu, dla której aplikacja żąda tokenu, różni się w zależności od tego, czy aplikacja uzyskuje dostęp do płaszczyzny zarządzania, czy do płaszczyzny danych. W związku z tym nazwą zasobu jest punkt końcowy płaszczyzny zarządzania lub płaszczyzny danych opisany w tabeli w dalszej części tego tematu, w zależności od środowiska platformy Azure.

Jeden pojedynczy mechanizm uwierzytelniania dla płaszczyzn zarządzania i danych ma swoje własne korzyści:

* Organizacje mogą centralnie kontrolować dostęp do wszystkich magazynów kluczy w organizacji
* Jeśli użytkownik odejdzie z organizacji, natychmiast utraci dostęp do wszystkich magazynów kluczy w organizacji
* Organizacje mogą dostosowywać uwierzytelnianie za pomocą opcji w usłudze Azure Active Directory (na przykład włączyć uwierzytelnianie wieloskładnikowe w celu zwiększenia bezpieczeństwa)

## <a name="management-plane-and-data-plane"></a>Płaszczyzna zarządzania i płaszczyzna danych
Usługa Azure Key Vault to usługa platformy Azure dostępna za pośrednictwem modelu wdrażania usługi Azure Resource Manager. Podczas tworzenia magazynu kluczy uzyskujesz kontener wirtualny do przechowywania poufnych obiekty, takie jak klucze, wpisy tajne i certyfikaty. Określone operacje są wykonywane na magazynie kluczy przy użyciu interfejsów płaszczyzny zarządzania płaszczyzna i danych. Płaszczyzny zarządzania służy do zarządzania samą usługę key vault. Dotyczy to również operacji, takich jak zarządzanie atrybutów i ustawienie zasady dostępu do płaszczyzny danych. Interfejs płaszczyzny danych służy do dodawania, usuwania, modyfikowania i używanie kluczy, wpisów tajnych i certyfikatów przechowywanych w magazynie kluczy.

Interfejsów płaszczyzny płaszczyzny i dane zarządzania są dostępne za pośrednictwem różnych punktów końcowych, wymienionych poniżej. Druga kolumna w tym artykule opisano nazwy DNS dla tych punktów końcowych w różnych środowiskach Azure. W trzeciej kolumnie opisano operacje, które można wykonywać na danej płaszczyźnie dostępu. Każda płaszczyzna dostępu ma również swój własny mechanizm kontroli dostępu. Kontrola dostępu do płaszczyzny zarządzania można ustawić przy użyciu kontroli dostępu usłudze Azure Resource Manager (RBAC). Kontrola dostępu do płaszczyzny danych jest ustawiana za pomocą zasad dostępu magazynu kluczy.

| Płaszczyzna dostępu | Punkty końcowe dostępu | Operacje | Mechanizm kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania |**Cały świat:**<br> management.azure.com:443<br><br> **Usługa Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> management.usgovcloudapi.net:443<br><br> **Niemiecka wersja platformy Azure:**<br> management.microsoftazure.de:443 |Tworzenie /odczyt/aktualizowanie/usuwanie magazynu kluczy <br> Ustawianie zasad dostępu dla magazynu kluczy<br>Ustawianie tagów dla magazynu kluczy |Kontrola dostępu oparta na rolach (RBAC) przy użyciu usługi Azure Resource Manager |
| Płaszczyzna danych |**Cały świat:**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Usługa Azure China 21Vianet:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Niemiecka wersja platformy Azure:**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 |Dla kluczy: Odszyfruj, Szyfruj, Opakuj klucz, Odpakuj klucz, Weryfikuj, Podpisz, Pobierz, Lista, Aktualizuj, Utwórz, Importuj, Usuń, Kopia zapasowa, Przywróć<br><br> Dla wpisów tajnych: Pobierz, Lista, Ustaw, Usuń |Zasady dostępu magazynu kluczy |

Zarządzanie płaszczyzna i danych płaszczyzna kontroli dostępu działać niezależnie. Na przykład jeśli użytkownik chce udzielić aplikacji dostępu do używania kluczy w magazynie kluczy, wystarczy przyznać dostęp do płaszczyzny danych. Dostęp jest udzielany przez zasady dostępu magazynu kluczy. Z drugiej strony użytkownika, który wymaga odczytywania właściwości magazynu i tagi, ale nie dostęp do danych (kluczy, wpisów tajnych lub certyfikatów), wymaga dostępu do płaszczyzny kontroli. Dostęp jest udzielany przez przypisanie "" dostęp do odczytu dla użytkownika, korzystając z modelu RBAC.

## <a name="management-plane-access-control"></a>Kontrola dostępu do płaszczyzny zarządzania
Płaszczyzna zarządzania składa się z operacji, które wpływają na samym magazynem kluczy, takie jak:

- Tworzenie lub usuwanie magazynu kluczy.
- Pobieranie listy magazynów w ramach subskrypcji.
- Pobieranie właściwości magazynu kluczy (takie jak SKU, znaczniki).
- Ustawianie zasad dostępu magazynu kluczy, które kontrolki użytkownika i aplikacji dostępu do kluczy i wpisów tajnych.

Kontrola dostępu do płaszczyzny zarządzania korzysta z funkcji RBAC. Zobacz pełną listę operacji magazynu kluczy, które mogą być wykonywane za pośrednictwem płaszczyzny zarządzania, w tabeli w poprzedniej sekcji. 

### <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Każda subskrypcja platformy Azure zawiera usługę Azure Active Directory. Użytkownikom, grupom i aplikacjom z tego katalogu można udzielić dostępu do zarządzania zasobami w ramach subskrypcji platformy Azure, która używa modelu wdrażania przy użyciu usługi Azure Resource Manager. Ten typ kontroli dostępu jest określany jako kontrola dostępu oparta na rolach (RBAC). Do zarządzania tym dostępem można użyć witryny [Azure Portal](https://portal.azure.com/), [narzędzi interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md), [programu PowerShell](/powershell/azureps-cmdlets-docs) lub [interfejsów API REST usługi Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Utworzysz magazyn kluczy w zasobów grupy i kontroli dostępu do płaszczyzny zarządzania za pomocą usługi Azure Active Directory. Na przykład można przyznać użytkownikom lub grupie możliwość zarządzania magazynami kluczy w grupie zasobów.

Przypisując odpowiednie role RBAC mogą przyznawać dostęp użytkownikom, grupom i aplikacjom, które w określonym zakresie. Na przykład aby udzielić dostępu użytkownika do zarządzania magazynami kluczy, przypisaniu wstępnie zdefiniowaną rolę "Współautor magazynu klucz" do tego użytkownika w określonym zakresie. Zakres w tym przypadku będzie subskrypcją, grupą zasobów lub określonego magazynu kluczy. Rola przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w tej subskrypcji. Rola przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów. Rola przypisana dla określonego zasobu ma zastosowanie do tego zasobu. Istnieje kilka wstępnie zdefiniowanych ról (zobacz [RBAC: wbudowane role](../role-based-access-control/built-in-roles.md)). Jeśli wstępnie zdefiniowaną rolę nie odpowiadają potrzebom, można zdefiniować własną rolę.

> [!IMPORTANT]
> Należy pamiętać, że jeśli użytkownik ma uprawnienia współautora (RBAC) do płaszczyzny zarządzania magazynu kluczy, może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu magazynu kluczy, które kontrolują dostęp do płaszczyzny danych. Dlatego zalecane jest ścisłe kontrolowanie, kto ma dostęp z uprawnieniami współautora do magazynów kluczy, aby tylko upoważnione osoby mogły uzyskiwać dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz zarządzać nimi.
> 
> 

## <a name="data-plane-access-control"></a>Kontrola dostępu do płaszczyzny danych
Operacje płaszczyzny danych usługi Key vault dotyczą przechowywanych obiektów, takich jak klucze, wpisy tajne i certyfikaty. Kluczowe operacje obejmują tworzenie, importowanie, aktualizowanie, lista, tworzenie kopii zapasowej i przywracanie kluczy. Operacje kryptograficzne znak, sprawdź, szyfrowania, odszyfrowywania, opakowywanie, Odkodowywanie, ustawianie tagów i innych atrybutów kluczy. Podobnie, operacje na klucze tajne to między innymi get, set, listy, Usuń.

Dostęp do płaszczyzny danych jest udzielany przez ustawienie zasad dostępu magazynu kluczy. Użytkownik, grupa lub aplikacja muszą mieć uprawnienia współautora (RBAC) do płaszczyzny zarządzania dla magazynu kluczy, aby móc ustawić zasady dostępu dla tego magazynu kluczy. Użytkownikowi, grupie lub aplikacji można udzielić dostępu do wykonywania określonych operacji dotyczących kluczy lub wpisów tajnych w magazynie kluczy. Magazyn kluczy obsługuje maksymalnie 1024 wpisów zasad dostępu dla magazynu kluczy. Utwórz grupę zabezpieczeń usługi Azure Active Directory i dodaj użytkowników do tej grupy, aby udzielić dostępu do płaszczyzny danych dla magazynu kluczy wielu użytkownikom.

### <a name="key-vault-access-policies"></a>Zasady dostępu magazynu kluczy
Zasady dostępu magazynu kluczy przyznają oddzielnie uprawnienia do kluczy, wpisów tajnych i certyfikatów. Na przykład można udzielić użytkownikowi dostępu tylko do kluczy i nie uprawnień do wpisów tajnych. Uprawnienia do dostępu do kluczy, wpisów tajnych lub certyfikatów są na poziomie magazynu. Zasady dostępu magazynu kluczy nie obsługuje szczegółowych uprawnień na poziomie obiektu, takie jak określony klucz/wpis tajny/certyfikat. W celu ustawienia zasad dostępu dla magazynu kluczy można użyć witryny [Azure Portal](https://portal.azure.com/), [narzędzi interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md), [programu PowerShell](/powershell/azureps-cmdlets-docs) lub [interfejsów API REST usługi Azure Resource Manager](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Należy pamiętać, że zasady dostępu magazynu kluczy są stosowane na poziomie magazynu. Na przykład jeśli użytkownikowi udzielono uprawnień do tworzenia i usuwania kluczy, będzie on mógł wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.

Oprócz zastosowania zasad dostępu, dostęp do warstwy danych można również ograniczyć przy użyciu [punktów końcowych sieci wirtualnej dla usługi Azure Key Vault](key-vault-overview-vnet-service-endpoints.md), konfigurując [zapory i reguły sieci wirtualnej](key-vault-network-security.md) na potrzeby dodatkowej warstwy zabezpieczeń.

## <a name="example"></a>Przykład
Załóżmy, że projektujesz aplikację, która używa certyfikatu protokołu SSL, usługi Azure storage do przechowywania danych i klucza szyfrowania RSA 2048-bitowego dla operacji podpisywania. Załóżmy, że ta aplikacja jest uruchomiona w maszynie wirtualnej platformy Azure (lub zestawu skalowania maszyn wirtualnych). Używanie magazynu kluczy do przechowywania wszystkich wpisów tajnych aplikacji i przechowywania certyfikatu uruchamiania, używanych przez aplikację do uwierzytelniania za pomocą usługi Azure Active Directory.

Poniżej przedstawiono podsumowanie typów kluczy i wpisów tajnych przechowywanych w magazynie kluczy:

* **Certyfikat SSL** — używane do obsługi protokołu SSL
* **Klucz magazynu** — używany do uzyskiwania dostępu do konta magazynu
* **RSA 2048-bitowy klucz** — używane dla operacji podpisywania
* **Certyfikatu uruchamiania** — używany do uwierzytelniania w usłudze Azure Active Directory. Po nadaniu prawa dostępu można pobrać klucza magazynu i użycia klucza RSA do podpisywania.

Teraz określmy osoby, które są wdrażanie i zarządzanie inspekcji tej aplikacji. W tym przykładzie użyjemy trzech ról.

* **Zespół ds. zabezpieczeń** — zwykle informatykami z wartość biura dyrektora CSO (Dyrektor ds. bezpieczeństwa) lub równoważne. Ten zespół jest odpowiedzialny za właściwe bezpieczne przechowywanie wpisów tajnych. Na przykład certyfikatów SSL, klucze RSA używane do podpisywania, parametry połączenia i klucze konta magazynu.
* **Deweloperzy/Operatorzy** -spamerzy opracowywanie aplikacji, a następnie wdrożyć go na platformie Azure. Zazwyczaj nie są częścią zespołu ds. zabezpieczeń i dlatego nie powinny mieć dostępu do poufnych danych, takich jak certyfikaty SSL i klucze RSA. Tylko wdrażana przez nich aplikacja powinna mieć dostęp do tych obiektów.
* **Audytorzy** — zazwyczaj inny zestaw osób, odizolowany od deweloperów i pracowników ogólnego działu IT. Ich zadaniem jest przeglądanie użycia i obsługi certyfikatów, kluczy i wpisów tajnych w celu zapewnienia zgodności ze standardami zabezpieczeń. 

Istnieje jeszcze jedna rola, która znajduje się poza zakresem tej aplikacji, ale odpowiedni w tym miejscu mają być wymienione. Ta rola jest subskrypcji (lub grupy zasobów) administratora. Administrator subskrypcji konfiguruje początkowe uprawnienia dostępu dla zespołu ds. zabezpieczeń. Administrator subskrypcji udziela dostępu do zespołu ds. zabezpieczeń, za pomocą grupy zasobów, która zawiera zasoby wymagane przez tę aplikację.

Teraz zobaczmy, jakie akcje wykonują poszczególne role w kontekście tej aplikacji.

* **Zespół ds. zabezpieczeń**
  * Tworzenie magazynów kluczy
  * Włączenie funkcji rejestrowania magazynu kluczy
  * Dodawanie kluczy/wpisów tajnych
  * Tworzenie kopii zapasowych kluczy na potrzeby odzyskiwania po awarii
  * Ustawianie zasad dostępu magazynu kluczy w celu udzielenia uprawnień użytkownikom i aplikacjom do wykonywania określonych operacji
  * Okresowe wycofywanie (ponowne tworzenie) kluczy/wpisów tajnych
* **Deweloperzy/operatorzy**
  * Pobieranie odwołań do ładowania początkowego i SSL certyfikatów (odcisków palca) klucza magazynu (identyfikatora URI wpisu tajnego) i klucza podpisywania (identyfikatora URI klucza) od zespołu ds. zabezpieczeń
  * Opracowywanie i wdrażanie aplikacji, która programowo uzyskuje dostęp do kluczy i wpisów tajnych
* **Audytorzy**
  * Przeglądanie dzienników użycia w celu potwierdzenia prawidłowego użycia kluczy/wpisów tajnych i zgodności ze standardami zabezpieczeń danych

Teraz zobaczmy, jakie uprawnienia są wymagane dla poszczególnych ról i aplikacji, aby wykonywać przydzielone im zadania. 

| Rola użytkownika | Uprawnienia do płaszczyzny zarządzania | Uprawnienia do płaszczyzny danych |
| --- | --- | --- |
| Zespół ds. zabezpieczeń |Współautor magazynu kluczy |Klucze: wykonywanie kopii zapasowej, tworzenie, usuwanie, pobieranie, importowanie, wyświetlanie, przywracanie <br> Wpisy tajne: wszystkie |
| Deweloperzy/operatorzy |Uprawnienia do wdrażania magazynu kluczy, tak aby wdrażane przez nich maszyny wirtualne mogły pobierać wpisy tajne z magazynu kluczy |Brak |
| Audytorzy |Brak |Klucze: wyświetlanie<br>Wpisy tajne: wyświetlanie |
| Aplikacja |Brak |Klucze: podpisywanie<br>Wpisy tajne: pobieranie |

> [!NOTE]
> Audytorzy muszą mieć uprawnienia do wyświetlania kluczy i wpisów tajnych, aby móc dokonywać inspekcji atrybutów kluczy i wpisów tajnych, które nie są emitowane w dziennikach, takich jak tagi oraz daty aktywacji i wygaśnięcia.
> 
> 

Oprócz uprawnień usługi key vault wszystkie trzy role potrzebują również dostępu do innych zasobów. Na przykład aby móc wdrażać maszyny wirtualne (lub aplikacje internetowe itp.), deweloperzy/operatorzy muszą mieć również uprawnienia dostępu „Współautor” do tych typów zasobów. Audytorzy muszą "" dostęp do odczytu do konta magazynu gdzie są przechowywane dzienniki magazynu kluczy.

Ponieważ ten artykuł koncentruje się na zabezpieczaniu dostępu do magazynu kluczy, firma Microsoft pokazują tylko pojęć odnoszących się do tego tematu. Szczegóły dotyczące wdrażanie certyfikatów, programowego dostępu do kluczy i wpisów tajnych i inne, są omówione w innym miejscu. Na wystąpienie:

- Wdrażanie certyfikatów przechowywanych w magazynie kluczy na maszynach wirtualnych zostało omówione w [wpis w blogu: Wdrażanie certyfikatów na maszynach wirtualnych z zarządzanego przez klienta usługi Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)
- [Pobierania próbek klienta usługi Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) ilustruje sposób użycia certyfikatu uruchamiania do uwierzytelniania w usłudze Azure AD do dostępu do magazynu kluczy.

Można udzielić większość uprawnień dostępu przy użyciu witryny Azure portal. Aby udzielić szczegółowych uprawnień, może być konieczne Użyj programu Azure PowerShell lub interfejsu wiersza polecenia, aby osiągnąć oczekiwany rezultat. 

W poniższych fragmentach kodu programu PowerShell przyjęto następujące założenia:

* Administrator usługi Azure Active Directory utworzył grupy zabezpieczeń, które reprezentują trzy role (zespół ds. zabezpieczeń firmy Contoso, Contoso App Devops, audytorzy aplikacji Contoso). Administrator dodał również użytkowników do grup, do których należą.
* **ContosoAppRG** to grupa zasobów, w której znajdują się wszystkie zasoby. **contosologstorage** to miejsce, w którym przechowywane są dzienniki. 
* Magazyn kluczy **ContosoKeyVault** i konto magazynu używane dla dzienników magazynu kluczy **contosologstorage** muszą znajdować się w tej samej lokalizacji platformy Azure

Najpierw administrator subskrypcji przypisuje role „Współautor magazynu kluczy” i „Administrator dostępu użytkowników” do zespołu ds. zabezpieczeń. Te role umożliwiają zespół ds. zabezpieczeń Zarządzanie dostępem do innych zasobów i Zarządzanie magazynami kluczy w grupie zasobów ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Poniższy skrypt pokazuje, jak zespół ds. zabezpieczeń można utworzyć magazyn kluczy i konfigurowanie rejestrowania i uprawnienia dostępu. Zobacz [klucze, wpisy tajne i certyfikaty usługi Azure Key Vault](about-keys-secrets-and-certificates.md) szczegółowe informacje na temat usługi Key Vault zasady uprawnień dostępu.

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Zdefiniowaną rolę niestandardową można przypisać tylko do subskrypcji, w której została utworzona grupa zasobów ContosoAppRG. Jeśli te same role niestandardowe będą używane dla innych projektów w innych subskrypcjach, jej zakresu może być dodane więcej subskrypcji.

Przypisanie roli niestandardowej uprawnienia deweloperów/operatorów "Wdrażanie/Akcja" obejmuje grupę zasobów. Dzięki temu tylko maszyny wirtualne utworzone w grupie zasobów ContosoAppRG, aby mieć dostęp do wpisy tajne (certyfikat SSL i certyfikat uruchamiania). Maszyny wirtualne utworzone w innej grupie zasobów przez członka zespołu deweloperów/operatorów nie będziesz mieć dostępu do tych kluczy tajnych, nawet jeśli mają one wpisu tajnego identyfikatorów URI.

Ten przykład pokazuje Prosty scenariusz. Rzeczywiste scenariusze mogą być bardziej skomplikowane i może być konieczne dostosowanie uprawnień do magazynu kluczy zgodnie z potrzebami. W tym przykładzie przyjęto założenie, że zespół ds. zabezpieczeń udostępni klucza i wpisu tajnego odwołania (identyfikatory URI i odciski palca), które deweloperów/operatorów musi odwoływać się w swoich aplikacjach. Deweloperzy/operatorzy nie wymagają żadnych dostęp do płaszczyzny danych. Ten przykład koncentruje się na zabezpieczaniu magazynu kluczy. Podobnie należy rozważyć zapewnienie [maszyn wirtualnych z systemem](https://azure.microsoft.com/services/virtual-machines/security/), [kont magazynu](../storage/common/storage-security-guide.md)i innych zasobów platformy Azure.

> [!NOTE]
> Uwaga: W tym przykładzie pokazano, jak dostęp do magazynu kluczy będzie zabezpieczany (blokowany) w środowisku produkcyjnym. Deweloperzy powinni mieć swoją własną subskrypcję lub grupę zasobów, w której mają pełne uprawnienia do zarządzania swoimi magazynami, maszynami wirtualnymi i kontem magazynu, gdzie opracowują aplikacje.

Zalecane jest aby zabezpieczyć dostęp do magazynu kluczy dalsze przez [Konfigurowanie usługi Key Vault zapory i sieci wirtualne](key-vault-network-security.md).

## <a name="resources"></a>Zasoby
* [Kontrola dostępu oparta na rolach w usłudze Azure Active Directory](../role-based-access-control/role-assignments-portal.md)
  
  W tym artykule objaśniono funkcję kontroli dostępu opartej na rolach w usłudze Azure Active Directory i sposób jej działania.
* [Kontrola dostępu oparta na rolach (RBAC): wbudowane role](../role-based-access-control/built-in-roles.md)
  
  W tym artykule omówiono szczegółowo wszystkie wbudowane role dostępne w RBAC.
* [Omówienie wdrażania przy użyciu usługi Resource Manager oraz wdrażania klasycznego](../azure-resource-manager/resource-manager-deployment-model.md)
  
  W tym artykule opisano model wdrażania przy użyciu usługi Azure Resource Manager i klasyczny model wdrażania oraz wyjaśniono zalety korzystania z usługi Resource Manager i grup zasobów.
* [Zarządzanie kontrolą dostępu opartą na rolach za pomocą programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
  
  W tym artykule wyjaśniono, jak zarządzać kontrolą dostępu opartą na rolach przy użyciu programu Azure PowerShell
* [Zarządzanie kontrolą dostępu opartą na rolach za pomocą interfejsu API REST](../role-based-access-control/role-assignments-rest.md)
  
  W tym artykule przedstawiono sposób zarządzania kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu API REST.
* [Kontrola dostępu oparta na rolach dla platformy Microsoft Azure — konferencja Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Ta 2015 Microsoft Ignite konferencji wideo omawia zarządzania dostępem i możliwości raportowania w systemie Azure. Analizuje ona również najlepsze rozwiązania dotyczące zabezpieczania dostępu do subskrypcji platformy Azure przy użyciu usługi Azure Active Directory.
* [Autoryzowanie dostępu do aplikacji internetowych przy użyciu protokołu OAuth 2.0 i usługi Azure Active Directory](../active-directory/develop/v1-protocols-oauth-code.md)
  
  W tym artykule opisano kompletny przepływ protokołu OAuth 2.0 używany do uwierzytelniania za pomocą usługi Azure Active Directory.
* [Interfejsy API REST zarządzania magazynem kluczy](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Dokument referencyjny dotyczący interfejsów API REST służących do programowego zarządzania magazynem kluczy, w tym ustawiania zasad dostępu magazynu kluczy.
* [Interfejsy API REST magazynu kluczy](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  Link do dokumentacji referencyjnej interfejsów API REST magazynu kluczy.
* [Kontrola dostępu do kluczy](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  Link do dokumentacji referencyjnej dotyczącej kontroli dostępu do kluczy tajnych.
* [Kontrola dostępu do kluczy tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  Link do dokumentacji referencyjnej dotyczącej kontroli dostępu do kluczy.
* [Ustawianie](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) i [usuwanie](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) zasad dostępu magazynu kluczy przy użyciu programu PowerShell
  
  Linki do dokumentacji referencyjnej dotyczącej poleceń cmdlet programu PowerShell służących do zarządzania zasadami dostępu magazynu kluczy.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie zapór i sieci wirtualnych usługi Key Vault](key-vault-network-security.md)

Aby zapoznać się z samouczkiem wprowadzającym dla administratora, zobacz [Wprowadzenie do usługi Azure Key Vault](key-vault-get-started.md).

Aby uzyskać więcej informacji na temat rejestrowania użycia usługi Key Vault, zobacz [Funkcja rejestrowania usługi Azure Key Vault](key-vault-logging.md).

Aby uzyskać więcej informacji na temat używania kluczy i wpisów tajnych w usłudze Azure Key Vault, zobacz [Informacje o kluczach i wpisach tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Jeśli masz pytania dotyczące usługi Key Vault, odwiedź [forum usługi Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

