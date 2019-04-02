---
title: Przykładowe — ISO 27001 ASE/SQL obciążenia planu — kroki wdrażania
description: Wdróż kroki przykładowy plan obciążenia ISO 27001 App Service środowiska/bazą danych SQL.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4ed51ee5f8cbdc50fa65a189d8f468bd7713a74b
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804160"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Wdrażanie przykładowej planu obciążenia ISO 27001 App Service środowiska/bazą danych SQL

Aby wdrożyć przykładowy plan obciążenie Azure schematy ISO 27001 aplikacji środowiska/SQL bazy danych usługi, należy podjąć następujące kroki:

> [!div class="checklist"]
> - Wdrażanie [ISO 27001 udostępnionych usług](../iso27001-shared/index.md) przykładowy plan
> - Tworzenie nowego planu z próbki
> - Oznacz kopii przykładu jako **opublikowano**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Wdrażanie przykładowej planu ISO 27001 udostępnionych usług

Przed wdrożeniem w tym przykładzie planu [ISO 27001 udostępnionych usług](../iso27001-shared/index.md) przykładowy plan musi zostać wdrożony w subskrypcji docelowej. Bez pomyślnego wdrożenia przykładowy plan ISO 27001 udostępnionych usług, w tym przykładzie planu będzie brakować zależności infrastruktury i zakończyć się niepowodzeniem podczas wdrażania.

> [!IMPORTANT]
> W tym przykładzie plan musi być przypisany do tej samej subskrypcji co [ISO 27001 udostępnionych usług](../iso27001-shared/index.md) przykładowy plan.

## <a name="create-blueprint-from-sample"></a>Utwórz plan z próbki

Najpierw należy zaimplementować przykładowy plan przez utworzenie nowego planu w środowisku przy użyciu przykładu jako początkowego.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Z **wprowadzenie** strony po lewej stronie, wybierz opcję **Utwórz** przycisku w obszarze _Tworzenie planu_.

1. Znajdź **ISO 27001: Obciążenia środowiska ASE/SQL** przykładowy plan w ramach _inne przykłady_ i wybierz **korzystać z tej próbki**.

1. Wprowadź _podstawy_ próbki planu:

   - **Nazwa planu**: Podaj nazwę dla tej kopii przykładu planu obciążenia ISO 27001 ASE/SQL.
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
     - **Identyfikator subskrypcji usługi udostępnione**: Identyfikator subskrypcji gdzie [ISO 27001 udostępnionych usług](../iso27001-shared/index.md) przykładowy plan jest przypisany.
     - **Domyślny prefiks adresu podsieci**: Notacja CIDR podsieci domyślnej sieci wirtualnej.
       Wartość domyślna to _10.1.0.0/16_.
     - **Lokalizacja obciążenia**: Określa lokalizację, jakie artefakty są wdrażane w. Nie wszystkie usługi są dostępne we wszystkich lokalizacjach. Opcji parametru, do lokalizacji, do tego artefaktu do wdrożenia podobnej do artefaktów wdrażania tych usług.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu w ramach której jest zdefiniowany. Te parametry są [parametrów dynamicznych](../../concepts/parameters.md#dynamic-parameters) ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub artefaktu parametrów i ich opisy, zobacz [tabeli Parametry artefaktu](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów, wybierz **przypisać** w dolnej części strony. Przypisanie planu jest tworzony i rozpocznie się wdrażanie artefaktów. Wdrożenie potrwa około godziny. Aby sprawdzić stan wdrożenia, otwórz przypisanie planu.

> [!WARNING]
> Usługa Azure schematy i przykłady wbudowanych planu są **bezpłatna**. Zasoby platformy Azure są [rozliczana według produktu](https://azure.microsoft.com/pricing/). Użyj [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) można oszacować koszt zasobów wdrożonych w tym przykładzie planu.

## <a name="artifact-parameters-table"></a>Artefakt parametrów tabeli

Poniższa tabela zawiera listę planu artefaktu parametry:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Grupy zasobów usługi log Analytics|Grupa zasobów|Name (Nazwa)|**Zablokowane** -łączy **nazwa organizacji** z `-workload-log-rg` unikatowość grupy zasobów.|
|Grupy zasobów usługi log Analytics|Grupa zasobów|Lokalizacja|**Zablokowane** -używa parametru planu.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Warstwa usług|Ustawia Warstwa obszaru roboczego usługi Log Analytics. Wartość domyślna to _PerNode_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Lokalizacja|Region, używane do tworzenia obszaru roboczego usługi Log Analytics. Wartość domyślna to _zachodnie stany USA 2_.|
|Grupa zasobów sieciowych|Grupa zasobów|Name (Nazwa)|**Zablokowane** -łączy **nazwa organizacji** z `-workload-net-rg` unikatowość grupy zasobów.|
|Grupa zasobów sieciowych|Grupa zasobów|Lokalizacja|**Zablokowane** -używa parametru planu.|
|Szablon sieciowej grupy zabezpieczeń|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Prywatny adres IP zapory platformy Azure|Umożliwia skonfigurowanie prywatnego adresu IP z [zaporę platformy Azure](../../../../firewall/overview.md). Powinna być częścią notacji CIDR zdefiniowane w _ISO 27001: Usługi udostępnione_ parametru artefaktu **prefiks adresu podsieci zapory usługi Azure**. Wartość domyślna to _10.0.4.4_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Identyfikator subskrypcji usług udostępnionych|Wartość używana do włączenia wirtualną sieć równorzędną między obciążeń i usług udostępnionych.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Prefiks adresu sieci wirtualnej|Notacja CIDR dla sieci wirtualnej. Wartość domyślna to _10.1.0.0/16_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Domyślny prefiks adresu podsieci|Notacja CIDR podsieci domyślnej sieci wirtualnej. Wartość domyślna to _10.1.0.0/16_.|
|Szablon sieci wirtualnej i tabeli tras|Szablon usługi Resource Manager|Adres IP usług AD DS|Adres IP pierwszego DODAJE maszyny Wirtualnej. Ta wartość jest używana jako niestandardowe DNS sieci Wirtualnej.|
|Grupa zasobów usługi Key Vault|Grupa zasobów|Name (Nazwa)|**Zablokowane** -łączy **nazwa organizacji** z `-workload-kv-rg` unikatowość grupy zasobów.|
|Grupa zasobów usługi Key Vault|Grupa zasobów|Lokalizacja|**Zablokowane** -używa parametru planu.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Identyfikator obiektu usługi AAD|Identyfikator obiektu usługi AAD konta, które wymaga dostępu do wystąpienia usługi Key Vault. Brak wartości domyślnej wartości i nie może być puste. Aby zlokalizować tę wartość w witrynie Azure portal, wyszukaj i wybierz opcję "Użytkownicy" w obszarze _usług_. Użyj _nazwa_ pole, aby odfiltrować dla nazwy konta i wybrać tego konta. Na _profilu użytkownika_ stronie, wybierz ikonę "Kliknij, aby skopiować" _obiektu o identyfikatorze_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Jednostka SKU usługi Key Vault|Określa jednostkę SKU magazynu kluczy, który jest tworzony. Wartość domyślna to _Premium_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Nazwa użytkownika administratora serwera Azure SQL|Nazwa użytkownika umożliwiają dostęp do serwera SQL Azure. Musi odpowiadać tej samej wartości właściwości w **szablonu usługi Azure SQL Database**. Wartość domyślna to _administratora sql_.|
|Grupy zasobów w usłudze Azure SQL Database|Grupa zasobów|Name (Nazwa)|**Zablokowane** -łączy **nazwa organizacji** z `-workload-azsql-rg` unikatowość grupy zasobów.|
|Grupy zasobów w usłudze Azure SQL Database|Grupa zasobów|Lokalizacja|**Zablokowane** -używa parametru planu.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Nazwa użytkownika administratora serwera Azure SQL|Nazwa użytkownika serwera usługi Azure SQL. Musi odpowiadać tej samej wartości właściwości w **szablonu usługi Key Vault**. Wartość domyślna to _administratora sql_.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Hasło administratora w usłudze Azure SQL Server (identyfikator zasobu magazynu klucza)|Identyfikator zasobu magazynu kluczy. Użyj "/ subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" i Zastąp `{subscriptionId}` identyfikatorem subskrypcji i `{orgName}` z  **Nazwa organizacji** planu parametru.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Hasło administratora w usłudze Azure SQL Server (nazwa wpisu tajnego usługi Key Vault)|Nazwa użytkownika SQL administratora serwera. Musi być zgodna z wartością w **szablonu usługi Key Vault** właściwość **nazwę administratora serwera SQL Azure**.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Identyfikator obiektu administratora usługi AAD|Identyfikator obiektu usługi AAD użytkownika, który będzie zostaną przypisane jako administratora usługi Active Directory. Brak wartości domyślnej wartości i nie może być puste. Aby zlokalizować tę wartość w witrynie Azure portal, wyszukaj i wybierz opcję "Użytkownicy" w obszarze _usług_. Użyj _nazwa_ pole, aby odfiltrować dla nazwy konta i wybrać tego konta. Na _profilu użytkownika_ stronie, wybierz ikonę "Kliknij, aby skopiować" _obiektu o identyfikatorze_.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Identyfikator logowania administratora usługi AAD|Obecnie kont Microsoft (takich jak live.com lub outlook.com) nie może być ustawiani jako administratorzy. Tylko użytkownicy i grupy zabezpieczeń w organizacji można ustawić jako administrator. Brak wartości domyślnej wartości i nie może być puste. Aby zlokalizować tę wartość w witrynie Azure portal, wyszukaj i wybierz opcję "Użytkownicy" w obszarze _usług_. Użyj _nazwa_ pole, aby odfiltrować dla nazwy konta i wybrać tego konta. Na _profilu użytkownika_ stronie, skopiuj _nazwa_użytkownika_.|
|Grupy zasobów w środowisku usługi aplikacji|Grupa zasobów|Name (Nazwa)|**Zablokowane** -łączy **nazwa organizacji** z `-workload-ase-rg` unikatowość grupy zasobów.|
|Grupy zasobów w środowisku usługi aplikacji|Grupa zasobów|Lokalizacja|**Zablokowane** -używa parametru planu.|
|Szablon środowiska App Service Environment|Szablon usługi Resource Manager|Nazwa domeny|Nazwa usługi Active Directory utworzonych w ramach przykładu. Wartość domyślna to _contoso.com_.|
|Szablon środowiska App Service Environment|Szablon usługi Resource Manager|Lokalizacja środowiska ASE|Lokalizacja środowiska usługi aplikacji. Wartość domyślna to _zachodnie stany USA 2_.|
|Szablon środowiska App Service Environment|Szablon usługi Resource Manager|Okres przechowywania dziennika usługi Application Gateway w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|

## <a name="next-steps"></a>Kolejne kroki

Teraz, po przejrzeniu kroki, aby wdrożyć przykładowy plan obciążenia ISO 27001 App Service środowiska/bazą danych SQL, odwiedź następujące artykuły, aby dowiedzieć się więcej o architekturze i mapowanie kontrolek:

> [!div class="nextstepaction"]
> [Plan obciążenia ISO 27001 App Service środowiska/bazą danych SQL — omówienie](./index.md)
> [planu obciążenia ISO 27001 App Service środowiska/bazą danych SQL — mapowanie kontrolek](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).