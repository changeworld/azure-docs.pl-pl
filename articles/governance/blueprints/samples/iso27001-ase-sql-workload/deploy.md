---
title: Wdrażanie przykładowego planu obciążenia ASE/SQL zgodnie z programem ISO 27001
description: Wdrażanie kroków przykładowego planu obciążenia środowiska usługi aplikacji ISO 27001/bazy danych SQL, w tym szczegółów parametru artefaktu planu.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 6b8f3b753f1dd8cfbc247a77f2004e3c4d3423bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922579"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Wdrażanie przykładowego planu obciążenia środowiska usługi aplikacji ISO 27001/bazy danych SQL

Aby wdrożyć przykład planu azure plany ISO 27001 środowisko usługi aplikacji/sql bazy danych plan planu, należy wykonać następujące kroki:

> [!div class="checklist"]
> - Wdrażanie przykładowego planu [usług udostępnionych ISO 27001](../iso27001-shared/index.md)
> - Tworzenie nowego planu z przykładu
> - Oznaczanie kopii próbki jako **opublikowanej**
> - Przypisywanie kopii planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free) przed rozpoczęciem.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Wdrażanie przykładowego planu usług udostępnionych ISO 27001

Przed wdrożeniem tego przykładu planu przykład planu [usług współużytkowanych ISO 27001](../iso27001-shared/index.md) należy wdrożyć w subskrypcji docelowej. Bez pomyślnego wdrożenia przykładu planu usług udostępnionych ISO 27001 w tym przykładzie planu zabraknie zależności infrastruktury i nie powidziele podczas wdrażania.

> [!IMPORTANT]
> Ten przykład planu musi być przypisany w tej samej subskrypcji co przykład planu [usług udostępnionych ISO 27001.](../iso27001-shared/index.md)

## <a name="create-blueprint-from-sample"></a>Tworzenie planu z przykładu

Najpierw zaimplementuj przykład planu, tworząc nowy plan w środowisku przy użyciu próbki jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** po lewej stronie wybierz przycisk **Utwórz** w obszarze _Tworzenie planu_.

1. Znajdź przykład planu **schematu obciążenia ISO 27001: ASE/SQL** w obszarze _Inne przykłady_ i wybierz **użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa planu:** Podaj nazwę kopii przykładowego planu obciążenia ASE/SQL ISO 27001.
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
     - **Identyfikator subskrypcji usługi udostępnionej:** Identyfikator subskrypcji, w którym przypisano przykład planu [usług udostępnionych ISO 27001.](../iso27001-shared/index.md)
     - **Domyślny prefiks adresu podsieci:** Notacja CIDR dla domyślnej podsieci sieci wirtualnej.
       Wartość domyślna to _10.1.0.0/16_.
     - **Lokalizacja obciążenia:** Określa, w jakiej lokalizacji są wdrażane artefakty. Nie wszystkie usługi są dostępne we wszystkich lokalizacjach. Artefakty wdrażające takie usługi zapewniają opcję parametru dla lokalizacji, w którym należy wdrożyć ten artefakt.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w którym jest zdefiniowany. Parametry te są [parametry dynamiczne,](../../concepts/parameters.md#dynamic-parameters) ponieważ są one zdefiniowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktu i ich opisy, zobacz [Tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz **pozycję Przypisz** u dołu strony. Zostanie utworzone przypisanie planu i rozpocznie się wdrażanie artefaktów. Wdrożenie trwa około godziny. Aby sprawdzić stan wdrożenia, otwórz przypisanie planu.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady planu są **bezpłatne.** Zasoby platformy Azure są [wyceniane według produktu](https://azure.microsoft.com/pricing/). Kalkulator [cen](https://azure.microsoft.com/pricing/calculator/) służy do szacowania kosztów uruchomionych zasobów wdrożonych w tym przykładzie planu.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktu planu:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Grupa zasobów usługi Log Analytics|Grupa zasobów|Nazwa|**Zablokowane** — łączy **nazwę organizacji,** `-workload-log-rg` aby grupa zasobów była unikatowa.|
|Grupa zasobów usługi Log Analytics|Grupa zasobów|Lokalizacja|**Zablokowane** — używa parametru planu.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Warstwa usług|Ustawia warstwę obszaru roboczego usługi Log Analytics. Wartością domyślną jest _PerNode_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Lokalizacja|Region używany do tworzenia obszaru roboczego usługi Log Analytics. Wartość domyślna to _Zachodnie stany USA 2_.|
|Grupa zasobów sieciowych|Grupa zasobów|Nazwa|**Zablokowane** — łączy **nazwę organizacji,** `-workload-net-rg` aby grupa zasobów była unikatowa.|
|Grupa zasobów sieciowych|Grupa zasobów|Lokalizacja|**Zablokowane** — używa parametru planu.|
|Szablon sieciowej grupy zabezpieczeń|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Prywatny adres IP zapory platformy Azure|Konfiguruje prywatny adres IP [zapory platformy Azure](../../../../firewall/overview.md). Powinien być częścią notacji CIDR zdefiniowanej w _iso 27001:_ Parametr artefaktu usługi **udostępnionej usługi Azure subnet prefiks adresu**. Wartość domyślna to _10.0.4.4_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Identyfikator subskrypcji usług udostępnionych|Wartość używana do włączania komunikacji równorzędnej sieci wirtualnej między obciążeniem a usługami udostępnionymi.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Prefiks adresu sieci wirtualnej|Notacja CIDR dla sieci wirtualnej. Wartość domyślna to _10.1.0.0/16_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|Domyślny prefiks adresu podsieci|Notacja CIDR dla domyślnej podsieci sieci wirtualnej. Wartość domyślna to _10.1.0.0/16_.|
|Szablon tabeli sieci wirtualnej i trasy|Szablon usługi Resource Manager|DODAJE adres IP|Adres IP pierwszej maszyny Wirtualnej DODAJE. Ta wartość jest używana jako niestandardowy system DNS sieci wirtualnej.|
|Grupa zasobów Magazynu kluczy|Grupa zasobów|Nazwa|**Zablokowane** — łączy **nazwę organizacji,** `-workload-kv-rg` aby grupa zasobów była unikatowa.|
|Grupa zasobów Magazynu kluczy|Grupa zasobów|Lokalizacja|**Zablokowane** — używa parametru planu.|
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Identyfikator obiektu AAD|Identyfikator obiektu usługi AAD konta, który wymaga dostępu do wystąpienia usługi Key Vault. Nie ma wartości domyślnej i nie można jej pozostawić pustą. Aby zlokalizować tę wartość w witrynie Azure portal, wyszukaj i wybierz "Użytkownicy" w obszarze _Usługi_. Użyj pola _Nazwa,_ aby odfiltrować nazwę konta i wybrać to konto. Na stronie _Profil użytkownika_ wybierz ikonę "Kliknij, aby skopiować" obok _identyfikatora obiektu_.|
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Jednostka SKU magazynu kluczy|Określa jednostkę SKU tworzonego magazynu kluczy. Wartością domyślną jest _Premium_.|
|Szablon Przechowalni kluczy|Szablon usługi Resource Manager|Nazwa administratora programu Azure SQL Server|Nazwa użytkownika używana do uzyskiwania dostępu do programu Azure SQL Server. Musi być zgodna z tą samą wartością właściwości w **szablonie bazy danych SQL platformy Azure**. Wartość domyślna to _sql-admin-user_.|
|Grupa zasobów usługi Azure SQL Database|Grupa zasobów|Nazwa|**Zablokowane** — łączy **nazwę organizacji,** `-workload-azsql-rg` aby grupa zasobów była unikatowa.|
|Grupa zasobów usługi Azure SQL Database|Grupa zasobów|Lokalizacja|**Zablokowane** — używa parametru planu.|
|Szablon bazy danych SQL platformy Azure|Szablon usługi Resource Manager|Nazwa administratora programu Azure SQL Server|Nazwa użytkownika programu Azure SQL Server. Musi być zgodna z tą samą wartością właściwości w **szablonie Przechowalnia kluczy**. Wartość domyślna to _sql-admin-user_.|
|Szablon bazy danych SQL platformy Azure|Szablon usługi Resource Manager|Hasło administratora programu Azure SQL Server (identyfikator zasobu magazynu kluczy)|Identyfikator zasobu magazynu kluczy. Użyj funkcji "/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" i zastąpić `{subscriptionId}` `{orgName}` identyfikatorem subskrypcji i parametrem planu **nazwy organizacji.**|
|Szablon bazy danych SQL platformy Azure|Szablon usługi Resource Manager|Hasło administratora programu Azure SQL Server (nazwa tajnego magazynu kluczy)|Nazwa użytkownika administratora programu SQL Server. Musi być zgodna wartość w **właściwości szablonu usługi Azure** SQL Server nazwa **administratora .**|
|Szablon bazy danych SQL platformy Azure|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon bazy danych SQL platformy Azure|Szablon usługi Resource Manager|Identyfikator obiektu administratora usługi AAD|Identyfikator obiektu usługi AAD użytkownika, który zostanie przypisany jako administrator usługi Active Directory. Nie ma wartości domyślnej i nie można jej pozostawić pustą. Aby zlokalizować tę wartość w witrynie Azure portal, wyszukaj i wybierz "Użytkownicy" w obszarze _Usługi_. Użyj pola _Nazwa,_ aby odfiltrować nazwę konta i wybrać to konto. Na stronie _Profil użytkownika_ wybierz ikonę "Kliknij, aby skopiować" obok _identyfikatora obiektu_.|
|Szablon bazy danych SQL platformy Azure|Szablon usługi Resource Manager|Logowanie administratora AAD|Obecnie kont Microsoft (takich jak live.com lub outlook.com) nie można ustawić jako administratora. Tylko użytkownicy i grupy zabezpieczeń w organizacji można ustawić jako administratora. Nie ma wartości domyślnej i nie można jej pozostawić pustą. Aby zlokalizować tę wartość w witrynie Azure portal, wyszukaj i wybierz "Użytkownicy" w obszarze _Usługi_. Użyj pola _Nazwa,_ aby odfiltrować nazwę konta i wybrać to konto. Na stronie _Profil użytkownika_ skopiuj _nazwę użytkownika_.|
|Grupa zasobów środowiska usługi app service|Grupa zasobów|Nazwa|**Zablokowane** — łączy **nazwę organizacji,** `-workload-ase-rg` aby grupa zasobów była unikatowa.|
|Grupa zasobów środowiska usługi app service|Grupa zasobów|Lokalizacja|**Zablokowane** — używa parametru planu.|
|Szablon środowiska usługi app service|Szablon usługi Resource Manager|Nazwa domeny|Nazwa usługi Active Directory utworzonej przez przykład. Wartość domyślna to _contoso.com_.|
|Szablon środowiska usługi app service|Szablon usługi Resource Manager|Lokalizacja ASE|Lokalizacja środowiska usługi app service. Wartość domyślna to _Zachodnie stany USA 2_.|
|Szablon środowiska usługi app service|Szablon usługi Resource Manager|Przechowywanie dziennika bramy aplikacji w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|

## <a name="next-steps"></a>Następne kroki

Teraz, po przejrzeniu kroków wdrażania przykładowego planu obciążenia środowiska usługi aplikacji ISO 27001/bazy danych SQL, odwiedź następujące artykuły, aby dowiedzieć się więcej o mapowaniu architektury i sterowania:

> [!div class="nextstepaction"]
> [Plan obciążenia środowiska usługi aplikacji ISO 27001/bazy danych SQL — omówienie](./index.md)
> [planu obciążenia środowiska usługi aplikacji ISO 27001/bazy danych SQL — mapowanie sterowania](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).