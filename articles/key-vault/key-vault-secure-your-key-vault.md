---
title: Zabezpieczanie własnego magazynu kluczy | Microsoft Docs
description: Zarządzaj uprawnieniami dostępu do magazynu kluczy na potrzeby zarządzania magazynami, kluczami i wpisami tajnymi. Model uwierzytelniania i autoryzacji dla magazynu kluczy i sposób zabezpieczania własnego magazynu kluczy
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
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: ambapat
ms.openlocfilehash: a3493c9e9ef6a5bafd832510f42f33cc3f07f088
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="secure-your-key-vault"></a>Zabezpieczanie własnego magazynu kluczy
Usługa Azure Key Vault to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne (takie jak certyfikaty, parametry połączenia, hasła) dla aplikacji w chmurze. Ponieważ te dane są poufne i mają krytyczne znaczenie dla prowadzonej działalności, wskazane jest zabezpieczenie dostępu do własnego magazynu kluczy, tak aby tylko autoryzowane aplikacje i użytkownicy mogli uzyskiwać do niego dostęp. Ten artykuł zawiera omówienie modelu dostępu do magazynu kluczy, wyjaśnia uwierzytelnianie i autoryzację oraz na przykładzie opisuje sposób zabezpieczania dostępu do magazynu kluczy dla aplikacji w chmurze.

## <a name="overview"></a>Omówienie
Dostęp do magazynu kluczy jest kontrolowany za pośrednictwem dwóch oddzielnych interfejsów: płaszczyzny zarządzania i płaszczyzny danych. W przypadku obu płaszczyzn wymagane jest odpowiednie uwierzytelnianie i autoryzacja, zanim obiekt wywołujący (użytkownik lub aplikacja) będzie mógł uzyskać dostęp do magazynu kluczy. Uwierzytelnianie ustala tożsamość obiektu wywołującego, podczas gdy autoryzacja określa, jakie operacje może wykonywać obiekt wywołujący.

Na potrzeby uwierzytelniania płaszczyzna zarządzania i płaszczyzna danych używają usługi Azure Active Directory. Na potrzeby autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach (RBAC), natomiast płaszczyzna danych używa zasad dostępu magazynu kluczy.

Oto krótki przegląd omówionych tematów:

[Uwierzytelnianie przy użyciu usługi Azure Active Directory](#authentication-using-azure-active-directory) — w tej sekcji opisano, jak obiekt wywołujący uwierzytelnia się za pomocą usługi Azure Active Directory w celu uzyskania dostępu do magazynu kluczy za pośrednictwem płaszczyzny zarządzania i płaszczyzny danych. 

[Płaszczyzna zarządzania i płaszczyzna danych](#management-plane-and-data-plane) — płaszczyzna zarządzania i płaszczyzna danych to dwie płaszczyzny używane do uzyskiwania dostępu do magazynu kluczy. Każda płaszczyzna dostępu obsługuje konkretne operacje. W tej sekcji opisano punkty końcowe dostępu, obsługiwane operacje i metody kontroli dostępu używane przez każdą płaszczyznę. 

[Kontrola dostępu do płaszczyzny zarządzania](#management-plane-access-control) — w tej sekcji omówiono kontrolowanie dostępu do operacji płaszczyzny zarządzania za pomocą kontroli dostępu opartej na rolach.

[Kontrola dostępu do płaszczyzny danych](#data-plane-access-control) — w tej sekcji opisano sposób kontrolowania dostępu do płaszczyzny danych za pomocą zasad dostępu magazynu kluczy.

[Przykład](#example) — w tej sekcji zamieszczono przykład opisujący sposób konfigurowania kontroli dostępu do magazynu kluczy w celu umożliwienia trzem różnym zespołom (zespołowi zabezpieczeń, deweloperom/operatorom i audytorom) wykonywania określonych zadań na potrzeby opracowywania, zarządzania i monitorowania aplikacji na platformie Azure.

## <a name="authentication-using-azure-active-directory"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory
Podczas tworzenia magazynu kluczy w subskrypcji platformy Azure zostaje on automatycznie powiązany z dzierżawą usługi Azure Active Directory dla tej subskrypcji. Wszystkie podmioty wywołujące (użytkownicy i aplikacje) muszą być zarejestrowane w tej dzierżawie, aby uzyskiwać dostęp do tego magazynu kluczy. Aplikacja lub użytkownik musi uwierzytelnić się za pomocą usługi Azure Active Directory, aby uzyskać dostęp do magazynu kluczy. Dotyczy to zarówno dostępu do płaszczyzny zarządzania, jak i płaszczyzny danych. W obu przypadkach aplikacja może uzyskiwać dostęp do magazynu kluczy na dwa sposoby:

* **dostęp użytkownika i aplikacji** — zwykle stosowany dla aplikacji, które uzyskują dostęp do magazynu kluczy w imieniu zalogowanego użytkownika. Program Azure PowerShell i witryna Azure Portal to przykłady tego typu dostępu. Istnieją dwa sposoby udzielania dostępu użytkownikom: pierwszy to udzielenie dostępu użytkownikom w celu umożliwienia im uzyskiwania dostępu do magazynu kluczy z dowolnej aplikacji, a drugi to udzielenie użytkownikom dostępu do magazynu kluczy tylko wtedy, gdy korzystają oni z określonej aplikacji (ta metoda jest nazywana tożsamością złożoną). 
* **dostęp tylko aplikacji** — dla aplikacji, które uruchamiają usługi demona, zadania w tle itp. Tożsamości aplikacji udzielany jest dostęp do magazynu kluczy.

W przypadku obu typów aplikacji aplikacja uwierzytelnia się za pomocą usługi Azure Active Directory przy użyciu dowolnej z [obsługiwanych metod uwierzytelniania](../active-directory/active-directory-authentication-scenarios.md) i uzyskuje token. Używana metoda uwierzytelniania zależy od typu aplikacji. Następnie aplikacja używa tego tokenu i wysyła żądanie interfejsu API REST do magazynu kluczy. W przypadku dostępu do płaszczyzny zarządzania żądania są przesyłane za pośrednictwem punktu końcowego usługi Azure Resource Manager. W przypadku uzyskiwania dostępu do płaszczyzny danych aplikacja komunikuje się bezpośrednio z punktem końcowym magazynu kluczy. Patrz szczegółowe informacje na temat [całego przepływu uwierzytelniania](../active-directory/active-directory-protocols-oauth-code.md). 

Nazwa zasobu, dla której aplikacja żąda tokenu, różni się w zależności od tego, czy aplikacja uzyskuje dostęp do płaszczyzny zarządzania, czy do płaszczyzny danych. W związku z tym nazwą zasobu jest punkt końcowy płaszczyzny zarządzania lub płaszczyzny danych opisany w tabeli w dalszej części tego tematu, w zależności od środowiska platformy Azure.

Jeden pojedynczy mechanizm uwierzytelniania dla płaszczyzn zarządzania i danych ma swoje własne korzyści:

* Organizacje mogą centralnie kontrolować dostęp do wszystkich magazynów kluczy w organizacji
* Jeśli użytkownik odejdzie z organizacji, natychmiast utraci dostęp do wszystkich magazynów kluczy w organizacji
* Organizacje mogą dostosowywać uwierzytelnianie za pomocą opcji w usłudze Azure Active Directory (na przykład włączyć uwierzytelnianie wieloskładnikowe w celu zwiększenia bezpieczeństwa)

## <a name="management-plane-and-data-plane"></a>Płaszczyzna zarządzania i płaszczyzna danych
Usługa Azure Key Vault to usługa platformy Azure dostępna za pośrednictwem modelu wdrażania przy użyciu usługi Azure Resource Manager. Po utworzeniu magazynu kluczy uzyskujesz kontener wirtualny, wewnątrz którego możesz utworzyć inne obiekty, takie jak klucze, wpisy tajne i certyfikaty. Następnie można uzyskiwać dostęp do tego magazynu kluczy przy użyciu płaszczyzny zarządzania i płaszczyzny danych w celu wykonywania określonych operacji. Interfejs płaszczyzny zarządzania służy do zarządzania samym magazynem kluczy,w tym do tworzenia, usuwania i aktualizowania atrybutów magazynu kluczy oraz ustawiania zasad dostępu dla płaszczyzny danych. Interfejs płaszczyzny danych służy do dodawania, usuwania, modyfikowania i używania kluczy, wpisów tajnych i certyfikatów przechowywanych w magazynie kluczy.

Dostęp do interfejsów płaszczyzny zarządzania i płaszczyzny danych jest uzyskiwany za pośrednictwem różnych punktów końcowych (patrz tabela). W drugiej kolumnie tabeli opisano nazwy DNS dla tych punktów końcowych w różnych środowiskach Azure. W trzeciej kolumnie opisano operacje, które można wykonywać na danej płaszczyźnie dostępu. Każda płaszczyzna dostępu ma również swój własny mechanizm kontroli dostępu: w przypadku płaszczyzny zarządzania kontrola dostępu jest ustawiana przy użyciu kontroli dostępu opartej na rolach (RBAC) w usłudze Azure Resource Manager, natomiast w przypadku płaszczyzny danych kontrola dostępu jest ustawiana za pomocą zasad dostępu magazynu kluczy.

| Płaszczyzna dostępu | Punkty końcowe dostępu | Operacje | Mechanizm kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania |**Cały świat:**<br> management.azure.com:443<br><br> **Chińska wersja platformy Azure:**<br> management.chinacloudapi.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> management.usgovcloudapi.net:443<br><br> **Niemiecka wersja platformy Azure:**<br> management.microsoftazure.de:443 |Tworzenie /odczyt/aktualizowanie/usuwanie magazynu kluczy <br> Ustawianie zasad dostępu dla magazynu kluczy<br>Ustawianie tagów dla magazynu kluczy |Kontrola dostępu oparta na rolach (RBAC) przy użyciu usługi Azure Resource Manager |
| Płaszczyzna danych |**Cały świat:**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Chińska wersja platformy Azure:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Niemiecka wersja platformy Azure:**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 |Dla kluczy: Odszyfruj, Szyfruj, Opakuj klucz, Odpakuj klucz, Weryfikuj, Podpisz, Pobierz, Lista, Aktualizuj, Utwórz, Importuj, Usuń, Kopia zapasowa, Przywróć<br><br> Dla wpisów tajnych: Pobierz, Lista, Ustaw, Usuń |Zasady dostępu magazynu kluczy |

Mechanizmy kontroli dostępu do płaszczyzny zarządzania i płaszczyzny danych działają niezależnie. Aby na przykład udzielić aplikacji dostępu do używania kluczy przechowywanych w magazynie kluczy, należy tylko udzielić jej uprawnień dostępu do płaszczyzny danych przy użyciu zasad dostępu magazynu kluczy i nie trzeba udzielać tej aplikacji żadnego dostępu do płaszczyzny zarządzania. Z kolei aby umożliwić użytkownikowi odczyt właściwości i tagów magazynu, ale bez dostępu do kluczy, wpisów tajnych i certyfikatów, można udzielić temu użytkownikowi uprawnienie dostępu „odczyt” przy użyciu kontroli dostępu opartej na rolach (RBAC) i nie trzeba udzielać mu żadnego dostępu do płaszczyzny danych.

## <a name="management-plane-access-control"></a>Kontrola dostępu do płaszczyzny zarządzania
Płaszczyzna zarządzania składa się z operacji, które wpływają na sam magazyn kluczy. Można na przykład utworzyć lub usunąć magazyn kluczy, a także pobrać listę magazynów w subskrypcji. Można również pobrać właściwości magazynu kluczy (takie jak SKU, znaczniki) i ustawić zasady dostępu magazynu kluczy kontrolujące użytkowników i aplikacje, które mogą uzyskiwać dostęp do kluczy i wpisów tajnych w magazynie. Kontrola dostępu do płaszczyzny zarządzania korzysta z funkcji RBAC. Pełna lista operacji magazynu kluczy, które mogą być wykonywane za pośrednictwem płaszczyzny zarządzania, znajduje się w tabeli w poprzedniej sekcji. 

### <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Każda subskrypcja platformy Azure zawiera usługę Azure Active Directory. Użytkownikom, grupom i aplikacjom z tego katalogu można udzielić dostępu do zarządzania zasobami w ramach subskrypcji platformy Azure, która używa modelu wdrażania przy użyciu usługi Azure Resource Manager. Ten typ kontroli dostępu jest określany jako kontrola dostępu oparta na rolach (RBAC). Do zarządzania tym dostępem można użyć witryny [Azure Portal](https://portal.azure.com/), [narzędzi interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md), [programu PowerShell](/powershell/azureps-cmdlets-docs) lub [interfejsów API REST usługi Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

W modelu usługi Azure Resource Manager można utworzyć magazyn kluczy w grupie zasobów i kontrolować dostęp do płaszczyzny zarządzania tego magazynu kluczy przy użyciu usługi Azure Active Directory. Na przykład można przyznać użytkownikom lub grupie możliwość zarządzania magazynami kluczy w określonej grupie zasobów.

Przypisując odpowiednie role RBAC, można udzielić użytkownikom, grupom i aplikacjom dostępu w konkretnym zakresie. Aby na przykład udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, można przypisać wstępnie zdefiniowaną rolę „Współautor magazynu kluczy” do tego użytkownika w określonym zakresie. Zakres w tym przypadku będzie subskrypcją, grupą zasobów lub określonym magazynem kluczy. Rola przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w tej subskrypcji. Rola przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów. Rola przypisana dla określonego zasobu dotyczy tylko tego zasobu. Istnieje kilka wstępnie zdefiniowanych ról (zobacz [RBAC: wbudowane role](../role-based-access-control/built-in-roles.md)), a jeśli wstępnie zdefiniowane role nie odpowiadają potrzebom, można także zdefiniować własne role.

> [!IMPORTANT]
> Należy pamiętać, że jeśli użytkownik ma uprawnienia współautora (RBAC) do płaszczyzny zarządzania magazynu kluczy, może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu magazynu kluczy, które kontrolują dostęp do płaszczyzny danych. Dlatego zalecane jest ścisłe kontrolowanie, kto ma dostęp z uprawnieniami współautora do magazynów kluczy, aby tylko upoważnione osoby mogły uzyskiwać dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz zarządzać nimi.
> 
> 

## <a name="data-plane-access-control"></a>Kontrola dostępu do płaszczyzny danych
Płaszczyzna danych magazynu kluczy składa się z operacji, które wpływają na obiekty w magazynie kluczy, takie jak klucze, wpisy tajne i certyfikaty.  Obejmuje to operacje dotyczące kluczy, takie jak tworzenie, importowanie, aktualizowanie, wyświetlanie, wykonywanie kopii zapasowych i przywracanie kluczy, operacje kryptograficzne, takie jak podpisywanie, weryfikowanie, szyfrowanie, odszyfrowywanie, kodowanie i odkodowywanie, oraz ustawianie tagów i innych atrybutów kluczy. W przypadku wpisów tajnych obejmuje operacje pobierania, ustawiania, wyświetlania i usuwania.

Dostęp do płaszczyzny danych jest udzielany przez ustawienie zasad dostępu magazynu kluczy. Użytkownik, grupa lub aplikacja muszą mieć uprawnienia współautora (RBAC) do płaszczyzny zarządzania dla magazynu kluczy, aby móc ustawić zasady dostępu dla tego magazynu kluczy. Użytkownikowi, grupie lub aplikacji można udzielić dostępu do wykonywania określonych operacji dotyczących kluczy lub wpisów tajnych w magazynie kluczy. Magazyn kluczy obsługuje maksymalnie 16 wpisów zasad dostępu dla magazynu kluczy. Utwórz grupę zabezpieczeń usługi Azure Active Directory i dodaj użytkowników do tej grupy, aby udzielić dostępu do płaszczyzny danych dla magazynu kluczy wielu użytkownikom.

### <a name="key-vault-access-policies"></a>Zasady dostępu magazynu kluczy
Zasady dostępu magazynu kluczy przyznają oddzielnie uprawnienia do kluczy, wpisów tajnych i certyfikatów. Na przykład można udzielić użytkownikowi dostępu tylko do kluczy, ale żadnych uprawnień do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych lub certyfikatów są jednak przyznawane na poziomie magazynu. Innymi słowy, zasady dostępu magazynu kluczy nie obsługują uprawnień na poziomie obiektu. W celu ustawienia zasad dostępu dla magazynu kluczy można użyć witryny [Azure Portal](https://portal.azure.com/), [narzędzi interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md), [programu PowerShell](/powershell/azureps-cmdlets-docs) lub [interfejsów API REST usługi Azure Resource Manager](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Należy pamiętać, że zasady dostępu magazynu kluczy są stosowane na poziomie magazynu. Na przykład jeśli użytkownikowi udzielono uprawnień do tworzenia i usuwania kluczy, będzie on mógł wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.
> 
> 

## <a name="example"></a>Przykład
Załóżmy, że tworzona jest aplikacja, która używa certyfikatu dla protokołu SSL, usługi Azure Storage do przechowywania danych oraz klucza RSA o długości 2048 bitów dla operacji podpisywania. Załóżmy, że ta aplikacja działa na maszynie wirtualnej (lub w zestawie skalowania maszyny wirtualnej). Możesz używać magazynu kluczy do przechowywania wszystkich wpisów tajnych aplikacji i do przechowywania certyfikatu uruchamiania, który jest używany przez aplikację do uwierzytelniania za pomocą usługi Azure Active Directory.

Oto podsumowanie wszystkich kluczy i wpisów tajnych, które mają być przechowywane w magazynie kluczy.

* **Certyfikat SSL** — używany do obsługi protokołu SSL
* **Klucz magazynu** — używany do uzyskiwania dostępu do konta magazynu
* **2048-bitowy klucz RSA** — używany dla operacji podpisywania
* **Certyfikat uruchamiania** — używany do uwierzytelniania w usłudze Azure Active Directory, aby uzyskać dostęp do magazynu kluczy w celu pobrania klucza magazynu i użycia klucza RSA do podpisywania.

Teraz określmy osoby zajmujące się zarządzaniem tą aplikacją, jej wdrażaniem oraz inspekcją. W tym przykładzie użyjemy trzech ról.

* **Zespół ds. zabezpieczeń** — są to zazwyczaj pracownicy działu IT z biura dyrektora CSO (Chief Security Officer) lub jego odpowiednika, odpowiedzialni za właściwe bezpieczne przechowywanie wpisów tajnych, takich jak certyfikaty SSL, klucze RSA używane do podpisywania, parametry połączenia dla baz danych i klucze konta magazynu.
* **Deweloperzy/operatorzy** — są to osoby, które opracowują tę aplikację, a następnie wdrażają ją na platformie Azure. Zazwyczaj ci użytkownicy nie są częścią zespołu ds. zabezpieczeń i dlatego nie powinni mieć dostępu do poufnych danych, takich jak certyfikaty SSL i klucze RSA, ale wdrażana przez nich aplikacja powinna mieć do nich dostęp.
* **Audytorzy** — zazwyczaj jest to inny zestaw osób, odizolowany od deweloperów i pracowników ogólnego działu IT. Ich zadaniem jest sprawdzanie prawidłowego użycia i obsługi certyfikatów, kluczy itp. oraz zapewnienie zgodności ze standardami bezpieczeństwa danych. 

Istnieje jeszcze jedna rola, która znajduje się poza zakresem tej aplikacji, ale która jest tutaj istotna i trzeba o niej wspomnieć — administrator subskrypcji (lub grupy zasobów). Administrator subskrypcji konfiguruje początkowe uprawnienia dostępu dla zespołu ds. zabezpieczeń. Tutaj przyjęto założenie, że administrator subskrypcji udzielił zespołowi ds. zabezpieczeń dostępu do grupy zasobów, w której znajdują się wszystkie zasoby potrzebne dla tej aplikacji.

Teraz zobaczmy, jakie akcje wykonują poszczególne role w kontekście tej aplikacji.

* **Zespół ds. zabezpieczeń**
  * Tworzenie magazynów kluczy
  * Włączenie funkcji rejestrowania magazynu kluczy
  * Dodawanie kluczy/wpisów tajnych
  * Tworzenie kopii zapasowych kluczy na potrzeby odzyskiwania po awarii
  * Ustawianie zasad dostępu magazynu kluczy w celu udzielenia uprawnień użytkownikom i aplikacjom do wykonywania określonych operacji
  * Okresowe wycofywanie (ponowne tworzenie) kluczy/wpisów tajnych
* **Deweloperzy/operatorzy**
  * Pobieranie odwołań do certyfikatów SSL i uruchamiania (odcisków palca), klucza magazynu (identyfikatora URI wpisu tajnego) i klucza podpisywania (identyfikatora URI klucza) od zespołu ds. zabezpieczeń
  * Opracowywanie i wdrażanie aplikacji, która programowo uzyskuje dostęp do kluczy i wpisów tajnych
* **Audytorzy**
  * Przeglądanie dzienników użycia w celu potwierdzenia prawidłowego użycia kluczy/wpisów tajnych i zgodności ze standardami zabezpieczeń danych

Teraz zobaczmy, jakich uprawnień dostępu do magazynu kluczy wymagają poszczególne role (i aplikacja), aby wykonywać przydzielone im zadania. 

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

Poza uprawnieniami do magazynu kluczy wszystkie trzy role potrzebują również dostępu do innych zasobów. Na przykład aby móc wdrażać maszyny wirtualne (lub aplikacje sieci Web itp.), deweloperzy/operatorzy muszą mieć również uprawnienia dostępu „Współautor” do tych typów zasobów. Audytorzy musi mieć dostęp z uprawnieniami odczytu do konta magazynu, w którym są przechowywane dzienniki magazynu kluczy.

Ponieważ ten artykuł koncentruje się na zabezpieczaniu dostępu do magazynu kluczy, zilustrowano w nim tylko odpowiednie części odnoszące się do tego tematu i pominięto szczegóły dotyczące wdrażania certyfikatów, programowego uzyskiwania dostępu do kluczy i wpisów tajnych itd. Te szczegóły zostały już omówione w innym miejscu. Wdrażanie certyfikatów przechowywanych w magazynie kluczy na maszynach wirtualnych zostało omówione we [wpisie w blogu](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) i udostępniono [przykładowy kod](https://www.microsoft.com/download/details.aspx?id=45343), który ilustruje sposób użycia certyfikatu uruchamiania do uwierzytelniania w usłudze Azure AD w celu uzyskania dostępu do magazynu kluczy.

Większość uprawnień dostępu można przyznać za pomocą witryny Azure Portal, ale w celu przyznania szczegółowych uprawnień może być konieczne użycie programu Azure PowerShell (lub interfejsu wiersza polecenia platformy Azure), aby osiągnąć oczekiwany rezultat. 

W poniższych fragmentach kodu programu PowerShell przyjęto następujące założenia:

* Administrator usługi Azure Active Directory utworzył grupy zabezpieczeń reprezentujące trzy role, a mianowicie Contoso Security Team (zespół ds. zabezpieczeń Contoso), Contoso App Devops (deweloperzy/operatorzy aplikacji Contoso), Contoso App Auditors (audytorzy aplikacji Contoso). Administrator dodał również użytkowników do grup, do których należą.
* **ContosoAppRG** to grupa zasobów, w której znajdują się wszystkie zasoby. **contosologstorage** to miejsce, w którym przechowywane są dzienniki. 
* Magazyn kluczy **ContosoKeyVault** i konto magazynu używane dla dzienników magazynu kluczy **contosologstorage** muszą znajdować się w tej samej lokalizacji platformy Azure

Najpierw administrator subskrypcji przypisuje role „Współautor magazynu kluczy” i „Administrator dostępu użytkowników” do zespołu ds. zabezpieczeń. Umożliwia to zespołowi ds. zabezpieczeń zarządzanie dostępem do innych zasobów i zarządzanie magazynami kluczy w grupie zasobów ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Poniższy skrypt pokazuje, jak zespół ds. zabezpieczeń może utworzyć magazyn kluczy, skonfigurować rejestrowanie i ustawić uprawnienia dostępu dla innych ról i aplikacji. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets all

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

Zdefiniowaną rolę niestandardową można przypisać tylko do subskrypcji, w której została utworzona grupa zasobów ContosoAppRG. Jeśli te same role niestandardowe będą używane dla innych projektów w innych subskrypcjach, do jej zakresu może być dodane więcej subskrypcji.

Przypisanie roli niestandardowej dla deweloperów/operatorów w przypadku uprawnienia „wdrażanie/akcja” obejmuje grupę zasobów. Dzięki temu tylko maszyny wirtualne utworzone w grupie zasobów ContosoAppRG uzyskają wpisy tajne (certyfikat SSL i certyfikat uruchamiania). Maszyny wirtualne, które członek zespołu deweloperów/operatorów utworzy w innej grupie zasobów, nie będą mogły pobrać tych wpisów tajnych, nawet jeśli znają identyfikatory URI wpisów tajnych.

W tym przykładzie przedstawiono prosty scenariusz. Rzeczywiste scenariusze mogą być bardziej skomplikowane i wymagać dostosowania uprawnień do magazynu kluczy na podstawie własnych potrzeb. Na przykład w tym przykładzie przyjęto założenie, że zespół ds. zabezpieczeń udostępni odwołania do kluczy i wpisów tajnych (identyfikatory URI i odciski palca), do których zespół deweloperów/operatorów musi odwoływać się w swoich aplikacjach. W związku z tym nie trzeba udzielać deweloperom/operatorom żadnego dostępu do płaszczyzny danych. Należy również zauważyć, że ten przykład koncentruje się na zabezpieczaniu magazynu kluczy. Podobne uwagi dotyczą też zabezpieczania [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/security/), [kont magazynu](../storage/common/storage-security-guide.md) i innych zasobów platformy Azure.

> [!NOTE]
> Uwaga: W tym przykładzie pokazano, jak dostęp do magazynu kluczy będzie zabezpieczany (blokowany) w środowisku produkcyjnym. Deweloperzy powinni mieć swoją własną subskrypcję lub grupę zasobów, w której mają pełne uprawnienia do zarządzania swoimi magazynami, maszynami wirtualnymi i kontem magazynu, gdzie opracowują aplikacje.
> 
> 

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
  
  To jest link do filmu wideo w witrynie Channel 9 z konferencji Microsoft Ignite 2015. W tej sesji rozmawiamy o możliwościach zarządzania dostępem i raportowania na platformie Azure i eksplorujemy najlepsze rozwiązania dotyczące zabezpieczania dostępu do subskrypcji Azure za pomocą usługi Azure Active Directory.
* [Autoryzowanie dostępu do aplikacji sieci Web przy użyciu protokołu OAuth 2.0 i usługi Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)
  
  W tym artykule opisano kompletny przepływ protokołu OAuth 2.0 używany do uwierzytelniania za pomocą usługi Azure Active Directory.
* [Interfejsy API REST zarządzania magazynem kluczy](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Dokument referencyjny dotyczący interfejsów API REST służących do programowego zarządzania magazynem kluczy, w tym ustawiania zasad dostępu magazynu kluczy.
* [Interfejsy API REST magazynu kluczy](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  Link do dokumentacji referencyjnej interfejsów API REST magazynu kluczy.
* [Kontrola dostępu do kluczy](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  Link do dokumentacji referencyjnej dotyczącej kontroli dostępu do kluczy tajnych.
* [Kontrola dostępu do kluczy tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  Link do dokumentacji referencyjnej dotyczącej kontroli dostępu do kluczy.
* [Ustawianie](https://msdn.microsoft.com/library/mt603625.aspx) i [usuwanie](https://msdn.microsoft.com/library/mt619427.aspx) zasad dostępu magazynu kluczy przy użyciu programu PowerShell
  
  Linki do dokumentacji referencyjnej dotyczącej poleceń cmdlet programu PowerShell służących do zarządzania zasadami dostępu magazynu kluczy.

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z samouczkiem wprowadzającym dla administratora, zobacz [Wprowadzenie do usługi Azure Key Vault](key-vault-get-started.md).

Aby uzyskać więcej informacji na temat rejestrowania użycia usługi Key Vault, zobacz [Funkcja rejestrowania usługi Azure Key Vault](key-vault-logging.md).

Aby uzyskać więcej informacji na temat używania kluczy i wpisów tajnych w usłudze Azure Key Vault, zobacz [Informacje o kluczach i wpisach tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Jeśli masz pytania dotyczące usługi Key Vault, odwiedź [forum usługi Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

