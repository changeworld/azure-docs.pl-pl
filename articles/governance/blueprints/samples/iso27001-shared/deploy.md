---
title: Przykład — ISO 27001 udostępnione usługi plan — wdrażanie kroków
description: Wdróż kroki z przykładu plan usług udostępnionych ISO 27001.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 882228c7cb49a6a419b2b5e0bb200da3391d4584
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802557"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Wdróż przykład strategii usług udostępnionych ISO 27001

Aby wdrożyć przykład strategii usług udostępnionych ISO 27001 dla planów platformy Azure, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Utwórz nowy plan z przykładu
> - Oznacz swoją kopię przykładowej publikacji jako **opublikowaną**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw Zaimplementuj przykład strategii, tworząc nowy plan w środowisku przy użyciu przykładu jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **pierwsze kroki** z lewej strony wybierz przycisk **Utwórz** w obszarze _Utwórz plan_.

1. Znajdź plik **ISO 27001: Przykład planu** usługi udostępnionej w _innych próbkach_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa**planu: Podaj nazwę kopii przykładowego planu usługi udostępnione ISO 27001.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _artefakty_ w górnej części strony lub **Następny: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zostaną zdefiniowane w dalszej części. Wybierz pozycję **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładowej

Twoja kopia przykładu strategii została teraz utworzona w Twoim środowisku. Jest on tworzony w trybie **wersji roboczej** i musi być **opublikowany** , aby można go było przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacja może ją przenieść z normy ISO 27001.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Publikuj plan** w górnej części strony. Na nowej stronie z prawej strony Podaj **wersję** kopii przykładowej strategii. Ta właściwość jest przydatna w przypadku późniejszej modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana w przykładowej strategii ISO 27001". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisz przykładową kopię

Po pomyślnym **opublikowaniu**kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. Ten krok polega na tym, że parametry są dostarczane, aby każde wdrożenie kopii przykładowej strategii było unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz plan** w górnej części strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Ustawienia podstawowe

     - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, do której została zapisana kopia przykładu planu. W przypadku wybrania więcej niż jednej subskrypcji zostanie utworzone przypisanie dla każdego z nich przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: Nazwa jest wstępnie wypełniana na podstawie nazwy planu.
       Zmień stosownie do potrzeb lub pozostaw jako is.
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji**planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Zablokuj przypisanie

     Wybierz ustawienie blokowania planu dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję zarządzanej tożsamości _przypisanej do systemu_ .

   - Parametry planu

     Parametry zdefiniowane w tej sekcji są używane przez wiele artefaktów w definicji strategii w celu zapewnienia spójności.

     - **Nazwa organizacji**: Wprowadź krótką nazwę organizacji. Ta właściwość jest używana głównie do określania nazw zasobów.
     - **Prefiks adresu podsieci usług udostępnionych**: Podaj wartość notacji CIDR do łączenia ze sobą wdrożonych zasobów.
     - **Lokalizacja usług udostępnionych**: Określa lokalizację, w której są wdrażane artefakty. Nie wszystkie usługi są dostępne we wszystkich lokalizacjach. Artefakty wdrażające takie usługi zapewniają opcję parametru dla lokalizacji, w której ma zostać wdrożony artefakt.
     - **Dozwolona lokalizacja (zasady: Inicjatywa strategii dla ISO 27001**): Wartość wskazująca dozwolone lokalizacje grup zasobów i zasobów.
     - **Log Analytics obszar roboczy dla agentów maszyny wirtualnej (zasady: Inicjatywa strategii dla ISO 27001**): Określa identyfikator zasobu obszaru roboczego. Ten parametr używa `concat` funkcji do konstruowania identyfikatora zasobu.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w ramach którego jest zdefiniowany. Parametry te są [parametrami dynamicznymi](../../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Utworzono przypisanie strategii i rozpocznie się wdrażanie artefaktów. Wdrażanie zajmuje trochę czasu. Aby sprawdzić stan wdrożenia, Otwórz przypisanie planu.

> [!WARNING]
> Usługa plany platformy Azure i wbudowane przykłady planów są **bezpłatne**. Zasoby platformy Azure są [wyceniane według produktu](https://azure.microsoft.com/pricing/). Za pomocą [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) można oszacować koszt uruchamiania zasobów wdrożonych przez ten przykład strategii.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|\[Wersja\]zapoznawcza: Wdróż VM Scale Sets Log Analytics agenta dla systemu Linux (VMSS)|Przypisanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu|Obowiązkowe Wartość domyślna to _["Brak"]_ .|
|\[Wersja\]zapoznawcza: Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux|Przypisanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu|Obowiązkowe Wartość domyślna to _["Brak"]_ .|
|\[Wersja\]zapoznawcza: Wdróż Log Analytics agenta dla systemu Windows VM Scale Sets (VMSS)|Przypisanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu|Obowiązkowe Wartość domyślna to _["Brak"]_ .|
|\[Wersja\]zapoznawcza: Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows|Przypisanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu|Obowiązkowe Wartość domyślna to _["Brak"]_ .|
|Dozwolone typy zasobów|Przypisanie zasad|Dozwolone typy zasobów|Lista typów zasobów, które mogą zostać wdrożone. Ta lista składa się ze wszystkich typów zasobów wdrożonych w usługach udostępnionych.|
|Dozwolone jednostki SKU konta magazynu|Przypisanie zasad|Dozwolone jednostki SKU magazynu|Lista dzienników diagnostycznych — dozwolone jednostki SKU konta magazynu. Wartość domyślna to _["Standard_LRS"]_ .|
|Dozwolone jednostki SKU maszyn wirtualnych|Przypisanie zasad|Lista jednostek SKU maszyn wirtualnych, które mogą zostać wdrożone. Wartość domyślna to _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Inicjatywa strategii dla normy ISO 27001|Przypisanie zasad|Typy zasobów do inspekcji dzienników diagnostycznych|Lista typów zasobów do inspekcji, jeśli nie włączono ustawienia dziennika diagnostycznego. Akceptowalne wartości można znaleźć Azure Monitor w obszarze [schematy dzienników diagnostycznych](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Grupa zasobów Log Analytics|Resource group|Name|**Zablokowany** — łączy **nazwę organizacji** z `-sharedsvsc-log-rg` , aby uczynić grupę zasobów unikatową.|
|Grupa zasobów Log Analytics|Resource group|Location|**Zablokowany** — używa parametru strategii.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Warstwa usług|Ustawia warstwę obszaru roboczego Log Analytics. Wartość domyślna to _PerNode_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Location|Region używany do tworzenia obszaru roboczego Log Analytics. Wartość domyślna to _zachodnie stany USA 2_.|
|Grupa zasobów sieciowych|Resource group|Name|**Zablokowany** — łączy **nazwę organizacji** z `-sharedsvcs-net-rg` , aby uczynić grupę zasobów unikatową.|
|Grupa zasobów sieciowych|Resource group|Location|**Zablokowany** — używa parametru strategii.|
|Szablon usługi Azure Firewall|Szablon usługi Resource Manager|Prywatny adres IP zapory platformy Azure|Konfiguruje prywatny adres IP [zapory platformy Azure](../../../../firewall/overview.md). Ta wartość jest również używana jako domyślna tabela tras w podsieci usług udostępnionych. Musi być częścią notacji CIDR zdefiniowanej w **prefiksie adresu podsieci zapory platformy Azure**. Wartość domyślna to _10.0.4.4_.|
|Szablon usługi Azure Firewall|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon sieciowej grupy zabezpieczeń|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu sieci wirtualnej|Notacja CIDR dla sieci wirtualnej. Wartość domyślna to _10.0.0.0/16_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Włącz ochronę sieci wirtualnej przed atakami DDoS|Konfiguruje ochronę DDoS dla sieci wirtualnej. Wartość domyślna to _true_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci usług udostępnionych|Notacja CIDR dla podsieci usług udostępnionych. Wartość domyślna to _10.0.0.0/24_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci DMZ|Notacja CIDR dla podsieci DMZ. Wartość domyślna to _10.0.1.0/24_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci usługi Application Gateway|Notacja CIDR dla podsieci usługi Application Gateway. Wartość domyślna to _10.0.2.0/24_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci bramy Virtual Network|Notacja CIDR dla podsieci bramy sieci wirtualnej. Wartość domyślna to _10.0.3.0/24_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci usługi Azure Firewall|Notacja CIDR dla podsieci [zapory platformy Azure](../../../../firewall/overview.md) . Powinien zawierać parametr **prywatnego adresu IP zapory platformy Azure** .|
|Grupa zasobów Key Vault|Resource group|Name|**Zablokowany** — łączy **nazwę organizacji** z `-sharedsvcs-kv-rg` , aby uczynić grupę zasobów unikatową.|
|Grupa zasobów Key Vault|Resource group|Location|**Zablokowany** — używa parametru strategii.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Nazwa użytkownika administratora serwera przesiadkowego|Nazwa użytkownika dla serwera przesiadkowego. Musi być zgodna z tą samą wartością właściwości w **szablonie serwera przesiadkowego**. Wartość domyślna to _JB-admin-User_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Serwera przesiadkowego administratora klucza SSH lub hasła|Klucz lub hasło dla konta w serwera przesiadkowego. Musi być zgodna z tą samą wartością właściwości w **szablonie serwera przesiadkowego**. Brak wartości domyślnej i nie można jej pozostawić pustej.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Nazwa użytkownika administratora domeny|Nazwa użytkownika służąca do uzyskiwania dostępu do Active Directory VM i dołączania do domeny innych maszyn wirtualnych. Musi być zgodna z wartością właściwości **użytkownik administrator domeny** w **szablonie Active Directory Domain Services**. Wartość domyślna to _domena-administrator-użytkownik_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Hasło administratora domeny|Hasło administratora domeny. Brak wartości domyślnej i nie można jej pozostawić pustej.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Identyfikator obiektu usługi AAD|Identyfikator obiektu usługi AAD konta, które wymaga dostępu do wystąpienia Key Vault. Brak wartości domyślnej i nie można jej pozostawić pustej. Aby zlokalizować tę wartość z Azure Portal, Wyszukaj i wybierz pozycję "Użytkownicy" w obszarze _usługi_. Użyj pola _Nazwa_ , aby odfiltrować nazwę konta, a następnie wybierz to konto. Na stronie _profil użytkownika_ wybierz ikonę "kliknij, aby skopiować" obok _identyfikatora obiektu_.  |
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Jednostka SKU usługi Key Vault|Określa jednostkę SKU utworzonego Key Vault. Wartość domyślna to _Premium_.|
|Grupa zasobów serwera przesiadkowego|Resource group|Name|**Zablokowany** — łączy **nazwę organizacji** z `-sharedsvcs-jb-rg` , aby uczynić grupę zasobów unikatową.|
|Grupa zasobów serwera przesiadkowego|Resource group|Location|**Zablokowany** — używa parametru strategii.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|Nazwa użytkownika administratora serwera przesiadkowego|Nazwa użytkownika używana do uzyskiwania dostępu do maszyn wirtualnych serwera przesiadkowego. Musi być zgodna z tą samą wartością właściwości w **szablonie Key Vault**. Wartość domyślna to _JB-admin-User_.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|Hasło administratora serwera przesiadkowego (identyfikator zasobu Key Vault)|Identyfikator zasobu Key Vault. Użyj "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-KV-RG/Providers/Microsoft.KeyVault/Vaults/{orgName}-sharedsvcs-KV" i Zastąp `{subscriptionId}` ciąg identyfikatorem subskrypcji i organizacją `{orgName}`  **Nazwa** parametru strategii.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|Hasło administratora serwera przesiadkowego (Key Vault nazwa klucza tajnego)|Nazwa użytkownika administratora serwera przesiadkowego. Musi być zgodna wartość właściwości **szablonu Key Vault** **serwera przesiadkowego nazwa użytkownika administratora**.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|System operacyjny serwera przesiadkowego|Określa system operacyjny maszyny wirtualnej serwera przesiadkowego. Wartość domyślna to _Windows_.|
|Grupa zasobów Active Directory Domain Services|Resource group|Name|**Zablokowany** — łączy **nazwę organizacji** z `-sharedsvcs-adds-rg` , aby uczynić grupę zasobów unikatową.|
|Grupa zasobów Active Directory Domain Services|Resource group|Location|**Zablokowany** — używa parametru strategii.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Nazwa użytkownika administratora domeny|Nazwa użytkownika dla dodaje serwera przesiadkowego. Musi być zgodna z tą samą wartością właściwości w **szablonie Key Vault**. Wartość domyślna to _Dodawanie-administratora-użytkownika_.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Hasło administratora domeny (identyfikator zasobu Key Vault)|Identyfikator zasobu Key Vault. Użyj "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-KV-RG/Providers/Microsoft.KeyVault/Vaults/{orgName}-sharedsvcs-KV" i Zastąp `{subscriptionId}` ciąg identyfikatorem subskrypcji i organizacją `{orgName}`  **Nazwa** parametru strategii.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Hasło administratora domeny (Key Vault nazwa klucza tajnego)|Nazwa użytkownika administratora domeny. Musi być zgodna wartość właściwości **szablonu Key Vault** **Nazwa użytkownika Administrator domeny**.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Nazwa domeny|Nazwa Active Directory utworzonego przez przykład. Wartość domyślna to _contoso.com_.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Administrator domeny|Nazwa użytkownika dla konta administratora i do dołączania urządzeń do domeny usługi AD. Musi być zgodna z wartością właściwości **Nazwa użytkownika administratora usługi AD** w **szablonie Key Vault**. Wartość domyślna to _domena-administrator-użytkownik_.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Hasło administratora domeny|Ustaw Key Vault szczegóły dotyczące przechowywania hasła. Brak wartości domyślnej i nie można jej pozostawić pustej.|

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków w celu wdrożenia przykładowej usługi wspólnych informacji ISO 27001 zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o architekturze i mapowaniu formantów:

> [!div class="nextstepaction"]
> [ISO 27001 współużytkowanych usług plan — Omówienie](./index.md)
> [ISO 27001 usługi udostępnione — mapowanie](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
