---
title: Przykład planu usług udostępnionych ISO 27001
description: Wdrażanie kroków dla przykładu planu usług udostępnionych ISO 27001, w tym szczegółów parametru artefaktu planu.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: a9dabc99469321445006e449757a10fbc51aba87
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75920691"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Wdrażanie przykładowego planu usług udostępnionych ISO 27001

Aby wdrożyć przykład planu usług współużytkowych azure blueprints 27001, należy wykonać następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowego planu z przykładu
> - Oznaczanie kopii próbki jako **opublikowanej**
> - Przypisywanie kopii planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free) przed rozpoczęciem.

## <a name="create-blueprint-from-sample"></a>Tworzenie planu z przykładu

Najpierw zaimplementuj przykład planu, tworząc nowy plan w środowisku przy użyciu próbki jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** po lewej stronie wybierz przycisk **Utwórz** w obszarze _Tworzenie planu_.

1. Znajdź przykład planu **ISO 27001: Usługi udostępnione** w obszarze _Inne przykłady_ i wybierz **użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa planu:** Podaj nazwę kopii przykładowego planu usług udostępnionych ISO 27001.
   - **Lokalizacja definicji:** Użyj wielokropek i wybierz grupę zarządzania, aby zapisać kopię próbki.

1. Wybierz kartę _Artefakty_ u góry strony lub **Dalej: Artefakty** u dołu strony.

1. Przejrzyj listę artefaktów, które tworzą przykład planu. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu planu wybierz pozycję Zapisz wersja **robocza.**

## <a name="publish-the-sample-copy"></a>Publikowanie przykładowej kopii

Kopia przykładu planu została utworzona w twoim środowisku. Jest tworzony w trybie **roboczym** i musi zostać **opublikowany,** zanim będzie można go przypisać i wdrożyć. Kopię przykładu planu można dostosować do środowiska i potrzeb, ale ta modyfikacja może odsunąć ją od normy ISO 27001.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć kopię przykładu planu, a następnie wybierz go.

1. Wybierz **pozycję Publikuj plan** u góry strony. Na nowej stronie po prawej stronie podaj **wersję** dla kopii przykładu planu. Ta właściwość jest przydatna, jeśli dokonasz modyfikacji później. Podaj **uwagi dotyczące zmian,** takie jak "Pierwsza wersja opublikowana z przykładu planu ISO 27001". Następnie wybierz **pozycję Publikuj** u dołu strony.

## <a name="assign-the-sample-copy"></a>Przypisywanie przykładowej kopii

Po **pomyślnym**opublikowaniu kopii przykładu planu można ją przypisać do subskrypcji w grupie zarządzania, do której została zapisana. W tym kroku są dostarczane parametry, aby każde wdrożenie kopii przykładu planu unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć kopię przykładu planu, a następnie wybierz go.

1. Wybierz **pozycję Przypisz plan** u góry strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Podstawy

     - **Subskrypcje:** Wybierz jedną lub więcej subskrypcji, które znajdują się w grupie zarządzania, do której została zapisana kopia przykładu planu. Jeśli wybierzesz więcej niż jedną subskrypcję, dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania:** Nazwa jest wstępnie wypełniona na podstawie nazwy planu.
       Zmień w razie potrzeby lub zostaw, jak jest.
     - **Lokalizacja:** Wybierz region dla zarządzanej tożsamości, w którym ma zostać utworzona. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [tożsamości zarządzane dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji planu:** Wybierz **opublikowaną** wersję kopii przykładu planu.

   - Przypisanie blokady

     Wybierz ustawienie blokady planu dla środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję tożsamości _zarządzanej przypisanej systemowi._

   - Parametry strategii

     Parametry zdefiniowane w tej sekcji są używane przez wiele artefaktów w definicji planu, aby zapewnić spójność.

     - **Nazwa organizacji**: Wprowadź krótką nazwę organizacji. Ta właściwość jest używana głównie do nazewnictwa zasobów.
     - **Prefiks adresu podsieci usług udostępnionych:** Podaj wartość notacji CIDR dla sieci wdrożonych zasobów razem.
     - **Lokalizacja usług udostępnionych:** Określa, w jakiej lokalizacji są wdrażane artefakty. Nie wszystkie usługi są dostępne we wszystkich lokalizacjach. Artefakty wdrażające takie usługi zapewniają opcję parametru dla lokalizacji, w którym należy wdrożyć ten artefakt.
     - **Dozwolona lokalizacja (policy: Blueprint initiative for ISO 27001)**: Wartość wskazująca dozwolone lokalizacje dla grup zasobów i zasobów.
     - **Obszar roboczy usługi Log Analytics dla agentów maszyn wirtualnych (inicjatywa Zasada: Plan dla iso 27001)**: Określa identyfikator zasobu obszaru roboczego. Ten parametr używa `concat` funkcji do konstruowania identyfikatora zasobu.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w którym jest zdefiniowany. Parametry te są [parametry dynamiczne,](../../concepts/parameters.md#dynamic-parameters) ponieważ są one zdefiniowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktu i ich opisy, zobacz [Tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz **pozycję Przypisz** u dołu strony. Zostanie utworzone przypisanie planu i rozpocznie się wdrażanie artefaktów. Wdrożenie trwa około godziny. Aby sprawdzić stan wdrożenia, otwórz przypisanie planu.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady planu są **bezpłatne.** Zasoby platformy Azure są [wyceniane według produktu](https://azure.microsoft.com/pricing/). Kalkulator [cen](https://azure.microsoft.com/pricing/calculator/) służy do szacowania kosztów uruchomionych zasobów wdrożonych w tym przykładzie planu.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktu planu:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Linux (VMSS)|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Linux, aby dodać go do zakresu|(Opcjonalnie) Wartość domyślna to _["brak"]_.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Linux, aby dodać go do zakresu|(Opcjonalnie) Wartość domyślna to _["brak"]_.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows (VMSS)|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Windows, aby dodać go do zakresu|(Opcjonalnie) Wartość domyślna to _["brak"]_.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Windows, aby dodać go do zakresu|(Opcjonalnie) Wartość domyślna to _["brak"]_.|
|Dozwolone typy zasobów|Przypisywanie zasad|Dozwolone typy zasobów|Lista typów zasobów, które mogą zostać wdrożone. Ta lista składa się ze wszystkich typów zasobów wdrożonych w usługach udostępnionych.|
|Dozwolone jednostki SKU konta magazynu|Przypisywanie zasad|Dozwolone jednostki SKU magazynu|Lista jednostek SKU konta magazynu dzienników diagnostycznych dozwolonych. Wartość domyślna to _["Standard_LRS"]_.|
|Dozwolone jednostki SKU maszyny wirtualnej|Przypisywanie zasad|Lista jednostek SKU maszyny wirtualnej, które mogą zostać wdrożone. Wartość domyślna to _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Inicjatywa planu dla ISO 27001|Przypisywanie zasad|Typy zasobów do inspekcji dzienników diagnostycznych|Lista typów zasobów do inspekcji, jeśli ustawienie dziennika diagnostycznego nie jest włączone. Dopuszczalne wartości można znaleźć w [schematach dzienników diagnostycznych usługi Azure Monitor.](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)|
|Grupa zasobów usługi Log Analytics|Grupa zasobów|Nazwa|**Zablokowane** — łączy **nazwę organizacji,** `-sharedsvsc-log-rg` aby grupa zasobów była unikatowa.|
|Grupa zasobów usługi Log Analytics|Grupa zasobów|Lokalizacja|**Zablokowane** — używa parametru planu.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Warstwa usług|Ustawia warstwę obszaru roboczego usługi Log Analytics. Wartością domyślną jest _PerNode_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Lokalizacja|Region używany do tworzenia obszaru roboczego usługi Log Analytics. Wartość domyślna to _Zachodnie stany USA 2_.|
|Grupa zasobów sieciowych|Grupa zasobów|Nazwa|**Zablokowane** — łączy **nazwę organizacji,** `-sharedsvcs-net-rg` aby grupa zasobów była unikatowa.|
|Grupa zasobów sieciowych|Grupa zasobów|Lokalizacja|**Zablokowane** — używa parametru planu.|
|Szablon Zapory platformy Azure|Szablon usługi Resource Manager|Prywatny adres IP zapory platformy Azure|Konfiguruje prywatny adres IP [zapory platformy Azure](../../../../firewall/overview.md). Ta wartość jest również używana jako domyślna tabela tras w podsieci usług udostępnionych. Powinien być częścią notacji CIDR zdefiniowanej w **prefiksie adresu podsieci Zapory platformy Azure**. Wartość domyślna to _10.0.4.4_.|
|Szablon Zapory platformy Azure|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon sieciowej grupy zabezpieczeń|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Prefiks adresu sieci wirtualnej|Notacja CIDR dla sieci wirtualnej. Wartość domyślna to _10.0.0.0/16_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Włącz ochronę DDoS w sieci wirtualnej|Konfiguruje ochronę DDoS dla sieci wirtualnej. Wartość domyślna to _prawda_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci Usług udostępnionych|Notacja CIDR dla podsieci usług udostępnionych. Wartość domyślna to _10.0.0.0/24_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci DMZ|Notacja CIDR dla podsieci DMZ. Wartość domyślna to _10.0.1.0/24_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci bramy aplikacji|Notacja CIDR dla podsieci bramy aplikacji. Wartość domyślna to _10.0.2.0/24_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci bramy sieci wirtualnej|Notacja CIDR dla podsieci bramy sieci wirtualnej. Wartość domyślna to _10.0.3.0/24_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci Zapory platformy Azure|Notacja CIDR dla podsieci [zapory platformy Azure.](../../../../firewall/overview.md) Powinien zawierać parametr **prywatnego adresu IP zapory platformy Azure.**|
|Grupa zasobów Magazynu kluczy|Grupa zasobów|Nazwa|**Zablokowane** — łączy **nazwę organizacji,** `-sharedsvcs-kv-rg` aby grupa zasobów była unikatowa.|
|Grupa zasobów Magazynu kluczy|Grupa zasobów|Lokalizacja|**Zablokowane** — używa parametru planu.|
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Nazwa administratora Jumpbox|Nazwa użytkownika dla jumpbox. Musi odpowiadać tej samej wartości właściwości w **szablonie Jumpbox**. Domyślną wartością jest _jb-admin-user_.|
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Jumpbox admin ssh klucz lub hasło|Klucz lub hasło do konta na jumpbox. Musi odpowiadać tej samej wartości właściwości w **szablonie Jumpbox**. Nie ma wartości domyślnej i nie można jej pozostawić pustą.|
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Nazwa administratora domeny|Nazwa użytkownika używana do uzyskiwania dostępu do maszyny wirtualnej usługi Active Directory i dołączania innych maszyn wirtualnych do domeny. Musi być zgodna wartość **właściwości użytkownika administratora domeny** w **szablonie Usług domenowych Active Directory**. Domyślną wartością jest _domena-administrator-użytkownik_.|
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Hasło administratora domeny|Hasło administratora domeny. Nie ma wartości domyślnej i nie można jej pozostawić pustą.|
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Identyfikator obiektu AAD|Identyfikator obiektu usługi AAD konta, który wymaga dostępu do wystąpienia usługi Key Vault. Nie ma wartości domyślnej i nie można jej pozostawić pustą. Aby zlokalizować tę wartość w witrynie Azure portal, wyszukaj i wybierz "Użytkownicy" w obszarze _Usługi_. Użyj pola _Nazwa,_ aby odfiltrować nazwę konta i wybrać to konto. Na stronie _Profil użytkownika_ wybierz ikonę "Kliknij, aby skopiować" obok _identyfikatora obiektu_.  |
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Jednostka SKU magazynu kluczy|Określa jednostkę SKU tworzonego magazynu kluczy. Wartością domyślną jest _Premium_.|
|Grupa zasobów Jumpbox|Grupa zasobów|Nazwa|**Zablokowane** — łączy **nazwę organizacji,** `-sharedsvcs-jb-rg` aby grupa zasobów była unikatowa.|
|Grupa zasobów Jumpbox|Grupa zasobów|Lokalizacja|**Zablokowane** — używa parametru planu.|
|Szablon Jumpbox|Szablon usługi Resource Manager|Nazwa administratora Jumpbox|Nazwa użytkownika używana do uzyskiwania dostępu do maszyn wirtualnych jumpbox. Musi być zgodna z tą samą wartością właściwości w **szablonie Przechowalnia kluczy**. Domyślną wartością jest _jb-admin-user_.|
|Szablon Jumpbox|Szablon usługi Resource Manager|Hasło administratora jumpbox (identyfikator zasobu magazynu kluczy)|Identyfikator zasobu magazynu kluczy. Użyj funkcji "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" i zastąp `{subscriptionId}` identyfikatorem subskrypcji i `{orgName}` planem parametru nazwa **organizacji.**|
|Szablon Jumpbox|Szablon usługi Resource Manager|Hasło administratora Jumpbox (nazwa tajnego skarbca kluczy)|Nazwa użytkownika administratora jumpbox. Musi odpowiadać wartości w **aplikacji szablonu usługi Przechowalnia kluczy** **Nazwa administratora Jumpbox**.|
|Szablon Jumpbox|Szablon usługi Resource Manager|System operacyjny Jumpbox|Określa system operacyjny maszyny Wirtualnej jumpbox. Wartością domyślną jest _System Windows_.|
|Grupa zasobów Usług domenowych Active Directory|Grupa zasobów|Nazwa|**Zablokowane** — łączy **nazwę organizacji,** `-sharedsvcs-adds-rg` aby grupa zasobów była unikatowa.|
|Grupa zasobów Usług domenowych Active Directory|Grupa zasobów|Lokalizacja|**Zablokowane** — używa parametru planu.|
|Szablon Usług domenowych Active Directory|Szablon usługi Resource Manager|Nazwa administratora domeny|Nazwa użytkownika dla pola przeskoku ADDS. Musi być zgodna z tą samą wartością właściwości w **szablonie Przechowalnia kluczy**. Wartością domyślną jest _adds-admin-user_.|
|Szablon Usług domenowych Active Directory|Szablon usługi Resource Manager|Hasło administratora domeny (identyfikator zasobu magazynu kluczy)|Identyfikator zasobu magazynu kluczy. Użyj funkcji "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" i zastąp `{subscriptionId}` identyfikatorem subskrypcji i `{orgName}` planem parametru nazwa **organizacji.**|
|Szablon Usług domenowych Active Directory|Szablon usługi Resource Manager|Hasło administratora domeny (tajna nazwa magazynu kluczy)|Nazwa użytkownika administratora domeny. Musi odpowiadać wartości w **właściwości szablonu usługi Przechowalnia kluczy** **Nazwa użytkownika administratora domeny**.|
|Szablon Usług domenowych Active Directory|Szablon usługi Resource Manager|Nazwa domeny|Nazwa usługi Active Directory utworzonej przez przykład. Wartość domyślna to _contoso.com_.|
|Szablon Usług domenowych Active Directory|Szablon usługi Resource Manager|Użytkownik administrator domeny|Nazwa użytkownika dla konta usługi AD administratora i do łączenia urządzeń z domeną usługi AD. Musi być zgodna z wartością właściwości **właściwości nazwy użytkownika administratora usługi AD** w **szablonie Usługi Przechowalnia kluczy**. Domyślną wartością jest _domena-administrator-użytkownik_.|
|Szablon Usług domenowych Active Directory|Szablon usługi Resource Manager|Hasło administratora domeny|Ustaw szczegóły magazynu kluczy do przechowywania hasła. Nie ma wartości domyślnej i nie można jej pozostawić pustą.|

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zostały sprawdzone kroki wdrażania iso 27001 shared services planu przykładu, odwiedź następujące artykuły, aby dowiedzieć się więcej na temat architektury i mapowania kontroli:

> [!div class="nextstepaction"]
> [Plan usług udostępnionych ISO 27001 — omówienie](./index.md)
> [planu usług udostępnionych ISO 27001 — mapowanie sterowania](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
