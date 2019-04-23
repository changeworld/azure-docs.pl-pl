---
title: Przykład — ISO 27001 udostępnionych usług planu — wdrażanie kroki
description: Wdróż kroki przykładowy plan ISO 27001 udostępnionych usług.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d27f2495c70dbe6e10fb3adf5370a31903be3abf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792405"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Wdrażanie przykładowej planu ISO 27001 udostępnionych usług

Aby wdrożyć przykładowy plan udostępnionych usług platformy Azure schematy ISO 27001, należy podjąć następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowego planu z próbki
> - Oznacz kopii przykładu jako **opublikowano**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z próbki

Najpierw należy zaimplementować przykładowy plan przez utworzenie nowego planu w środowisku przy użyciu przykładu jako początkowego.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Z **wprowadzenie** strony po lewej stronie, wybierz opcję **Utwórz** przycisku w obszarze _Tworzenie planu_.

1. Znajdź **ISO 27001: Usługi udostępnione** przykładowy plan w ramach _inne przykłady_ i wybierz **korzystać z tej próbki**.

1. Wprowadź _podstawy_ próbki planu:

   - **Nazwa planu**: Podaj nazwę dla tej kopii przykładu planu ISO 27001 udostępnionych usług.
   - **Lokalizacja definicji**: Użyj wielokropek i wybierz grupę zarządzania, aby zapisać kopię próbki.

1. Wybierz _artefaktów_ kartę w górnej części strony lub **dalej: Artefakty** w dolnej części strony.

1. Przejrzyj listę artefaktów, które tworzą przykładowy plan. Wiele artefaktów ma parametry, które zdefiniujemy później. Wybierz **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Kopiowanie przykładowych publikowania

Utworzono kopię przykładowej planu w danym środowisku. Jest tworzony w **projekt** tryb i musi być **opublikowano** przed mogą zostać przypisane i wdrożony. Kopii przykładu planu można dostosować do danego środowiska i odpowiadające na wszystkie wymagania, ale ta zmiana może odbiegać od jego standardu ISO 27001.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć kopii przykładu planu, a następnie wybierz ją.

1. Wybierz **publikowania planu** w górnej części strony. W nowej strony po prawej stronie zapewniają **wersji** dla tej kopii przykładu planu. Ta właściwość jest przydatne w przypadku, jeśli wprowadzisz zmiany w dalszej części. Podaj **zmienić uwagi** takie jak "pierwsza wersja opublikowany z przykładu ISO 27001 planu." Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisania kopiowania próbki

Gdy kopii przykładu planu została pomyślnie **opublikowano**, może ona zostać przypisana do subskrypcji w obrębie grupy zarządzania został zapisany w. Ten krok jest, gdzie parametry są przekazywane do unikatowość każdego wdrożenia kopii przykładu planu.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć kopii przykładu planu, a następnie wybierz ją.

1. Wybierz **planu Przypisz** w górnej części strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jednej z subskrypcji, które znajdują się w grupie zarządzania należy zapisać swoją kopię przykładowy plan do. Jeśli wybierzesz więcej niż jedną subskrypcję, przypisania zostaną utworzone dla każdego przy użyciu podanych parametrów.
     - **Nazwa przypisania**: Nazwa jest wstępnie wypełniona automatycznie na podstawie nazwy planu.
       Zmień stosownie do potrzeb, lub pozostawić.
     - **Lokalizacja**: Wybierz region dla tożsamości zarządzanej, które zostały utworzone w. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji planu**: Wybierz **opublikowano** wersji kopii przykładu planu.

   - Zablokuj przypisanie

     Wybierz opcję blokady planu, ustawienie dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw wartość domyślną _przypisanej w systemie_ zarządzane opcja tożsamości.

   - Parametry strategii

     Parametry zdefiniowane w tej sekcji są używane przez wiele artefaktów w definicji planu w celu zapewnienia spójności.

     - **Nazwa organizacji**: Wprowadź nazwę krótką dla Twojej organizacji. Ta właściwość służy przede wszystkim na potrzeby nadawania nazw zasobów.
     - **Prefiks adresu podsieci usług udostępnionych**: Podaj wartość notacji CIDR ze sobą sieci wdrożonych zasobów.
     - **Usługi lokalizacji udostępnionej**: Określa lokalizację, jakie artefakty są wdrażane w. Nie wszystkie usługi są dostępne we wszystkich lokalizacjach. Opcji parametru, do lokalizacji, do tego artefaktu do wdrożenia podobnej do artefaktów wdrażania tych usług.
     - **Dozwolone lokalizacji (zasad: Planu inicjatywy dla standardu ISO 27001)**: Wartość wskazująca dozwolone lokalizacje dla grup zasobów i zasobów.
     - **Dziennik, aby przejrzeć obszar roboczy usługi Analytics agentów maszyn wirtualnych (zasad: Planu inicjatywy dla standardu ISO 27001)**: Określa identyfikator zasobu obszaru roboczego. Ten parametr używa `concat` funkcję, aby skonstruować nazwę zasobu.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu w ramach której jest zdefiniowany. Te parametry są [parametrów dynamicznych](../../concepts/parameters.md#dynamic-parameters) ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub artefaktu parametrów i ich opisy, zobacz [tabeli Parametry artefaktu](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów, wybierz **przypisać** w dolnej części strony. Przypisanie planu jest tworzony i rozpocznie się wdrażanie artefaktów. Wdrożenie potrwa około godziny. Aby sprawdzić stan wdrożenia, otwórz przypisanie planu.

> [!WARNING]
> Usługa Azure schematy i przykłady wbudowanych planu są **bezpłatna**. Zasoby platformy Azure są [rozliczana według produktu](https://azure.microsoft.com/en-us/pricing/). Użyj [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) można oszacować koszt zasobów wdrożonych w tym przykładzie planu.

## <a name="artifact-parameters-table"></a>Artefakt parametrów tabeli

Poniższa tabela zawiera listę planu artefaktu parametry:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|[Wersja zapoznawcza]\: Wdrażanie programu Log Analytics Agent for Linux VM Scale Sets (zestawu skalowania maszyn wirtualnych)|Przypisywanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych, obsługiwanych systemów operacyjnych Linux, można dodać do zakresu|(Opcjonalnie) Wartość domyślna to _["none"]_.|
|[Wersja zapoznawcza]\: Wdrażanie programu Log Analytics Agent na maszynach wirtualnych systemu Linux|Przypisywanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych, obsługiwanych systemów operacyjnych Linux, można dodać do zakresu|(Opcjonalnie) Wartość domyślna to _["none"]_.|
|[Wersja zapoznawcza]\: Wdrażanie programu Log Analytics Agent Windows VM Scale Sets (zestawu skalowania maszyn wirtualnych)|Przypisywanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych, obsługiwanych systemów operacyjnych Windows, aby dodać do zakresu|(Opcjonalnie) Wartość domyślna to _["none"]_.|
|[Wersja zapoznawcza]\: Wdrażanie programu Log Analytics Agent for Windows VMs|Przypisywanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych, obsługiwanych systemów operacyjnych Windows, aby dodać do zakresu|(Opcjonalnie) Wartość domyślna to _["none"]_.|
|Dozwolone typy zasobów|Przypisywanie zasad|Dozwolone typy zasobów|Lista typów zasobów, które mogą zostać wdrożone. Ta lista składa się z wszystkich typów zasobów wdrożonych w usługach udostępnionych.|
|Dozwolone jednostki SKU konta magazynu|Przypisywanie zasad|Dozwolone jednostki SKU magazynu|Lista danych diagnostycznych dzienników dozwolone jednostki SKU konta magazynu. Wartość domyślna to _["Standard_LRS"]_.|
|Dozwolone jednostki SKU maszyn wirtualnych|Przypisywanie zasad|Listę jednostek SKU maszyny wirtualnej mogą zostać wdrożone. Wartość domyślna to _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Inicjatywa strategii dla normy ISO 27001|Przypisywanie zasad|Typy zasobów, aby przeprowadzić inspekcję dzienników diagnostycznych|Lista typów zasobów do inspekcji, jeśli nie włączono ustawienie dzienników diagnostycznych. Dopuszczalne wartości, można znaleźć w folderze [schematy dla dzienników diagnostycznych usługi Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Grupy zasobów usługi log Analytics|Grupa zasobów|Name (Nazwa)|**Zablokowane** -łączy **nazwa organizacji** z `-sharedsvsc-log-rg` unikatowość grupy zasobów.|
|Grupy zasobów usługi log Analytics|Grupa zasobów|Lokalizacja|**Zablokowane** -używa parametru planu.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Warstwa usług|Ustawia Warstwa obszaru roboczego usługi Log Analytics. Wartość domyślna to _PerNode_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Lokalizacja|Region, używane do tworzenia obszaru roboczego usługi Log Analytics. Wartość domyślna to _zachodnie stany USA 2_.|
|Grupa zasobów sieciowych|Grupa zasobów|Name (Nazwa)|**Zablokowane** -łączy **nazwa organizacji** z `-sharedsvcs-net-rg` unikatowość grupy zasobów.|
|Grupa zasobów sieciowych|Grupa zasobów|Lokalizacja|**Zablokowane** -używa parametru planu.|
|Szablon usługi Azure Firewall|Szablon usługi Resource Manager|Prywatny adres IP zapory platformy Azure|Umożliwia skonfigurowanie prywatnego adresu IP z [zaporę platformy Azure](../../../../firewall/overview.md). Ta wartość jest również używana jako domyślna tabelę tras w podsieci usług udostępnionych. Powinna być częścią notacji CIDR zdefiniowane w **prefiks adresu podsieci zapory usługi Azure**. Wartość domyślna to _10.0.4.4_.|
|Szablon usługi Azure Firewall|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon sieciowej grupy zabezpieczeń|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Prefiks adresu sieci wirtualnej|Notacja CIDR dla sieci wirtualnej. Wartość domyślna to _10.0.0.0/16_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Włącz ochronę sieci wirtualnej przed atakami DDoS|Umożliwia skonfigurowanie ochrony przed atakami DDoS dla sieci wirtualnej. Wartość domyślna to _true_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Prefiks adresu podsieci usług udostępnionych|Notacja CIDR podsieci usług udostępnionych. Wartość domyślna to _10.0.0.0/24_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Prefiks adresu podsieci DMZ|Notacja CIDR podsieci DMZ. Wartość domyślna to _10.0.1.0/24_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Prefiks adresu podsieci usługi Application Gateway|Notacja CIDR podsieci bramy aplikacji. Wartość domyślna to _10.0.2.0/24_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Prefiks adresu podsieci bramy sieci wirtualnej|Notacja CIDR podsieci bramy sieci wirtualnej. Wartość domyślna to _10.0.3.0/24_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Prefiks adresu podsieci usługi Azure Firewall|Notacja CIDR do [zaporę platformy Azure](../../../../firewall/overview.md) podsieci. Powinien zawierać **prywatny adres IP zapory platformy Azure** parametru.|
|Grupa zasobów usługi Key Vault|Grupa zasobów|Name (Nazwa)|**Zablokowane** -łączy **nazwa organizacji** z `-sharedsvcs-kv-rg` unikatowość grupy zasobów.|
|Grupa zasobów usługi Key Vault|Grupa zasobów|Lokalizacja|**Zablokowane** -używa parametru planu.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Nazwa użytkownika administratora serwera przesiadkowego|Nazwa użytkownika dla serwera przesiadkowego. Musi odpowiadać tej samej wartości właściwości w **szablonu serwera Przesiadkowego**. Wartość domyślna to _jb administrator_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Klucz SSH lub hasło administratora serwera przesiadkowego|Klucza lub hasła konta z poziomu serwera przesiadkowego. Musi odpowiadać tej samej wartości właściwości w **szablonu serwera Przesiadkowego**. Brak wartości domyślnej wartości i nie może być puste.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Nazwa użytkownika administratora domeny|Nazwa użytkownika używana do dostępu Active Directory maszyny Wirtualnej i Dołącz do innych maszyn wirtualnych do domeny. Musi odpowiadać **administrator domeny** wartości właściwości w **szablonu usług domenowych Active Directory**. Wartość domyślna to _administrator domeny_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Hasło administratora domeny|Hasło użytkownika administratora domeny. Brak wartości domyślnej wartości i nie może być puste.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Identyfikator obiektu usługi AAD|Identyfikator obiektu usługi AAD konta, które wymaga dostępu do wystąpienia usługi Key Vault. Brak wartości domyślnej wartości i nie może być puste. Aby zlokalizować tę wartość w witrynie Azure portal, wyszukaj i wybierz opcję "Użytkownicy" w obszarze _usług_. Użyj _nazwa_ pole, aby odfiltrować dla nazwy konta i wybrać tego konta. Na _profilu użytkownika_ stronie, wybierz ikonę "Kliknij, aby skopiować" _obiektu o identyfikatorze_.  |
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Jednostka SKU usługi Key Vault|Określa jednostkę SKU magazynu kluczy, który jest tworzony. Wartość domyślna to _Premium_.|
|Serwer Przesiadkowy grupy zasobów|Grupa zasobów|Name (Nazwa)|**Zablokowane** -łączy **nazwa organizacji** z `-sharedsvcs-jb-rg` unikatowość grupy zasobów.|
|Serwer Przesiadkowy grupy zasobów|Grupa zasobów|Lokalizacja|**Zablokowane** -używa parametru planu.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|Nazwa użytkownika administratora serwera przesiadkowego|Nazwa użytkownika umożliwiają dostęp do serwera przesiadkowego maszyn wirtualnych. Musi odpowiadać tej samej wartości właściwości w **szablonu usługi Key Vault**. Wartość domyślna to _jb administrator_.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|Hasło administratora serwera Przesiadkowego (identyfikator zasobu magazynu klucza)|Identyfikator zasobu magazynu kluczy. Użyj "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" i Zastąp `{subscriptionId}` identyfikatorem subskrypcji i `{orgName}` z  **Nazwa organizacji** planu parametru.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|Hasło administratora serwera Przesiadkowego (nazwa wpisu tajnego usługi Key Vault)|Nazwa użytkownika administratora serwera przesiadkowego. Musi być zgodna z wartością w **szablonu usługi Key Vault** właściwość **nazwę administratora serwera Przesiadkowego**.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|System operacyjny serwera przesiadkowego|Określa system operacyjny z maszyną Wirtualną serwera przesiadkowego. Wartość domyślna to _Windows_.|
|Grupa zasobów w usłudze Active Directory Domain Services|Grupa zasobów|Name (Nazwa)|**Zablokowane** -łączy **nazwa organizacji** z `-sharedsvcs-adds-rg` unikatowość grupy zasobów.|
|Grupa zasobów w usłudze Active Directory Domain Services|Grupa zasobów|Lokalizacja|**Zablokowane** -używa parametru planu.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Nazwa użytkownika administratora domeny|Nazwa użytkownika dla serwera przesiadkowego usług AD DS. Musi odpowiadać tej samej wartości właściwości w **szablonu usługi Key Vault**. Wartość domyślna to _dodaje — administrator_.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Hasło administratora domeny (identyfikator zasobu magazynu klucza)|Identyfikator zasobu magazynu kluczy. Użyj "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" i Zastąp `{subscriptionId}` identyfikatorem subskrypcji i `{orgName}` z  **Nazwa organizacji** planu parametru.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Hasło administratora domeny (nazwa wpisu tajnego usługi Key Vault)|Nazwa użytkownika administratora domeny. Musi być zgodna z wartością w **szablonu usługi Key Vault** właściwość **nazwa użytkownika administratora domeny**.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Nazwa domeny|Nazwa usługi Active Directory utworzonych w ramach przykładu. Wartość domyślna to _contoso.com_.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Administrator domeny|Nazwa użytkownika dla konta administratora usługi AD i dołączania urządzenia do domeny usługi AD. Musi odpowiadać **nazwa użytkownika administratora usługi AD** wartości właściwości w **szablonu usługi Key Vault**. Wartość domyślna to _administrator domeny_.|
|Szablon usługi Active Directory Domain Services|Szablon usługi Resource Manager|Hasło administratora domeny|Ustaw szczegóły usługi Key Vault do przechowywania hasła. Brak wartości domyślnej wartości i nie może być puste.|

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy przejrzeniu kroki, aby wdrożyć przykładowy plan ISO 27001 udostępnionych usług, odwiedź następujące artykuły, aby dowiedzieć się więcej o architekturze i mapowanie kontrolek:

> [!div class="nextstepaction"]
> [ISO 27001 udostępnionych usług planu — omówienie](./index.md)
> [planu usług udostępnionych usługi ISO 27001 — mapowanie kontrolek](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).