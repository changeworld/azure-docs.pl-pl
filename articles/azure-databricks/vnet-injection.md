---
title: Wdrażanie usługi Azure Databricks w usłudze virtual network (wersja zapoznawcza)
description: W tym artykule opisano sposób wdrażania usługi Azure Databricks z siecią wirtualną, znany także jako iniekcji sieci wirtualnej.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003449"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>Wdrażanie usługi Azure Databricks w usłudze virtual network (wersja zapoznawcza)

Domyślnym wdrożeniu usługi Azure Databricks jest w pełni zarządzana usługa platformy Azure: wszystkie zasoby płaszczyzny danych, w tym usługi virtual network (VNet), zostały wdrożone do grupy zasobów zablokowane. Jeśli wymagane jest dostosowanie sieci, jednak można wdrożyć zasoby usługi Azure Databricks w Twojej sieci wirtualnej (również o nazwie iniekcji sieci wirtualnej), gdy pozwala na:

* Usługa Azure Databricks połączyć się z innymi usługami platformy Azure (np. usługi Azure Storage) w bardziej bezpieczny sposób za pomocą punktów końcowych usługi.
* Łączenie z danymi w środowisku lokalnym źródła do użycia z usługą Azure Databricks, korzystając z zalet trasy zdefiniowane przez użytkownika.
* Łączenie usługi Azure Databricks do wirtualnego urządzenia sieciowego, aby sprawdzić cały ruch wychodzący i podejmuj działania zgodnie z opisem w zezwalania i odmowy reguły.
* Konfigurowanie usługi Azure Databricks w celu korzystania z niestandardowego serwera DNS.
* Skonfiguruj reguły Sieciowej grupy zabezpieczeń sieci można określić ograniczenia ruchu wychodzącego.
* Wdrażanie klastrów usługi Azure Databricks w istniejącej sieci wirtualnej.

Wdrażanie zasobów usługi Azure Databricks w sieci wirtualnej umożliwia także korzystanie z zalet elastyczne zakresy CIDR (dowolne miejsce między /16-/ 24 dla sieci wirtualnej i między /18-/ 26 dla podsieci).

  > [!NOTE]
  > Nie można zastąpić sieci wirtualnej do istniejącego obszaru roboczego. Jeśli bieżący obszar roboczy nie spełnia wymagań wymagana liczba aktywne węzły klastra, należy utworzyć inny obszar roboczy, w większych sieci wirtualnej. Postępuj zgodnie z [te szczegółowe kroki migracji](howto-regional-disaster-recovery.md#detailed-migration-steps) można skopiować zasobów (notesów, konfiguracje klastrów, zadań) ze starego do nowego obszaru roboczego.

## <a name="virtual-network-requirements"></a>Wymagania dotyczące sieci wirtualnej

Można użyć interfejsu wdrożenia obszaru roboczego usługi Azure Databricks w witrynie Azure portal, aby automatycznie skonfigurować istniejącą sieć wirtualną z podsieciami wymagane, sieciowej grupy zabezpieczeń i ustawienia umieszczania na białej liście, lub skorzystać z usługi Azure Resource Manager Szablony do konfigurowania sieci wirtualnej i wdrażanie obszaru roboczego.

Sieć wirtualną, która jest wdrożeniem obszaru roboczego usługi Azure Databricks w celu musi spełniać następujące wymagania:

### <a name="location"></a>Lokalizacja

Sieć wirtualna musi znajdować się w tej samej lokalizacji co obszar roboczy usługi Azure Databricks.

### <a name="subnets"></a>Podsieci

Sieć wirtualna musi zawierać dwie podsieci do usługi Azure Databricks w wersji dedykowanej:

   1. Podsieci prywatnej przy użyciu skonfigurowanego sieciowej grupy zabezpieczeń, która umożliwia komunikację wewnętrzną klastra

   2. Podsieci publicznej za pomocą skonfigurowanego sieciowej grupy zabezpieczeń, która umożliwia komunikację za pomocą na płaszczyźnie kontroli usługi Azure Databricks.

### <a name="address-space"></a>Przestrzeń adresowa

Blok CIDR między /16-prefiksie/24 dla sieci wirtualnej i blok CIDR między /18-/26 dla podsieci prywatnej i publicznej.

### <a name="whitelisting"></a>Umieszczanie na liście dozwolonych

Cały ruch wychodzący i przychodzący między podsieciami i na płaszczyźnie kontroli usługi Azure Databricks, musi być umieszczona na białej liście.

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji opisano, jak utworzyć obszar roboczy usługi Azure Databricks w witrynie Azure portal i wdrożyć ją w swoje własne istniejącej sieci wirtualnej. Usługa Azure Databricks aktualizuje sieć wirtualną przy użyciu dwóch nowych podsieci i sieciowych grup zabezpieczeń przy użyciu zakresy CIDR podanych przez Ciebie, dozwolonych dla ruchu przychodzącego i ruchu wychodzącego podsieci i wdraża obszaru roboczego sieć wirtualna została zaktualizowana.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć sieć wirtualną, do którego zostanie wdrożona obszaru roboczego usługi Azure Databricks. Możesz użyć istniejącej sieci wirtualnej lub utworzyć nową, ale sieć wirtualna musi znajdować się w tym samym regionie, co obszar roboczy usługi Azure Databricks, który ma zostać utworzone. Zakres CIDR między /16-prefiksie/24 jest wymagany dla sieci wirtualnej.

  > [!Warning]
  > Obszar roboczy mniejszego virtual Network — oznacza to, niższy zakres CIDR — może zabraknąć adresy IP (przestrzeni sieci) znacznie szybciej niż obszar roboczy z większych sieci wirtualnej. Na przykład obszar roboczy o prefiksie/24 sieć wirtualną i /26 podsieci, które mogą mieć maksymalnie 64 węzłów aktywne w czasie, podczas gdy obszar roboczy z /20 sieć wirtualną i /22 podsieci mogą znajdować się maksymalnie 1024 węzłów.

  Podsieci zostaną utworzone automatycznie skonfigurować obszar roboczy, gdy będziesz mieć możliwość zapewnienia zakres CIDR podsieci podczas konfiguracji.

## <a name="configure-the-virtual-network"></a>Konfigurowanie sieci wirtualnej

1. W witrynie Azure portal wybierz **+ Utwórz zasób > Analytics > usługi Azure Databricks** aby otworzyć okno dialogowe z usługi Azure Databricks.

2. Wykonaj kroki konfiguracji opisane w kroku 2: Tworzenie obszaru roboczego usługi Azure Databricks w Getting Started Guide, a następnie wybierz obszar roboczy wdrożenia usługi Azure Databricks w opcjach sieci wirtualnej.

   ![Tworzenie usługi Azure Databricks](./media/vnet-injection/create-databricks-service.png)

3. Wybierz sieć wirtualną, z którego chcesz użyć.

   ![Opcje sieci wirtualnej](./media/vnet-injection/select-vnet.png)

4. Podaj zakresy CIDR w bloku między /18-/26 na dwie podsieci, dedykowanego dla usługi Azure Databricks:

   * Podsieci publicznej zostanie utworzony przy użyciu skojarzonej sieciowej grupy zabezpieczeń, która umożliwia komunikację za pomocą na płaszczyźnie kontroli usługi Azure Databricks.
   * Podsieci prywatnej zostanie utworzony przy użyciu skojarzonej sieciowej grupy zabezpieczeń, która umożliwia komunikację wewnętrzną klastra.

5. Kliknij przycisk **Utwórz** do wdrożenia obszaru roboczego usługi Azure Databricks z siecią wirtualną.

## <a name="advanced-resource-manager-configurations"></a>Konfiguracje Menedżera zasobów zaawansowane

Jeśli chcesz mieć większą kontrolę nad konfiguracją sieci wirtualnej — na przykład chcesz używać istniejących podsieci, korzystaj z istniejących grup zabezpieczeń sieci lub utworzyć własne reguły zabezpieczeń — możesz użyć poniższych szablonów usługi Azure Resource Manager zamiast wdrażanie konfiguracji i obszar roboczy sieć wirtualną w portalu.

### <a name="all-in-one"></a>Wszystko w jednym

Aby utworzyć sieć wirtualną, sieciowe grupy zabezpieczeń i obszaru roboczego usługi Azure Databricks, wszystko w jednym, użyj [All-in-one szablonu dla obszarów roboczych usługi Databricks sieci wirtualnej wprowadzony](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Korzystając z tego szablonu, nie musisz wykonać wszelkie ręcznego umieszczania na białej liście podsieci ruchu.

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Aby tworzenie sieciowych grup zabezpieczeń przy użyciu wymaganych reguł dla istniejącej sieci wirtualnej, należy użyć [szablonu grupy zabezpieczeń sieci do iniekcji sieci wirtualnej usługi Databricks](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

Korzystając z tego szablonu, nie musisz wykonać wszelkie ręcznego umieszczania na białej liście podsieci ruchu.

### <a name="virtual-network"></a>Sieć wirtualna

Aby utworzyć sieć wirtualną przy użyciu odpowiednich podsieci publicznych i prywatnych, użyj [szablonu wirtualnego sieci do iniekcji sieci wirtualnej usługi Databricks](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Możesz użyć tego szablonu bez również przy użyciu szablonu grupy zabezpieczeń sieci, należy ręcznie dodać reguły umieszczania na białej liście do sieciowych grup zabezpieczeń, używane z siecią wirtualną.

### <a name="azure-databricks-workspace"></a>Obszar roboczy usługi Azure Databricks

Aby wdrożyć obszar roboczy usługi Azure Databricks w istniejącej sieci wirtualnej, podsieci publicznych i prywatnych i poprawnie skonfigurowane sieciowe grupy zabezpieczeń już skonfigurowane, należy użyć [szablon Obszar roboczy do iniekcji sieci wirtualnej usługi Databricks](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Możesz użyć tego szablonu bez również przy użyciu szablonu grupy zabezpieczeń sieci, należy ręcznie dodać reguły umieszczania na białej liście do sieciowych grup zabezpieczeń, używane z siecią wirtualną.

## <a name="whitelisting-subnet-traffic"></a>Ruch z podsieci umieszczania na białej liście

Jeśli nie używasz [witryny Azure portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) lub [szablonów usługi Azure Resource Manager](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) do tworzenia sieci grup zabezpieczeń, użytkownik będzie musiał ręcznie umieścić na liście dozwolonych następujące ruchu w podsieci.

|Direction|Protokół|Element źródłowy|Port źródłowy|Element docelowy|Port docelowy|
|---------|--------|------|-----------|-----------|----------------|
|Przychodzący|\*|VirtualNetwork|\*|\*|\*|
|Przychodzący|\*|IP translatora adresów Sieciowych na płaszczyźnie kontroli|\*|\*|22|
|Przychodzący|\*|IP translatora adresów Sieciowych na płaszczyźnie kontroli|\*|\*|5557|
|Wychodzący|\*|\*|\*|IP aplikacji sieci Web|\*|
|Wychodzący|\*|\*|\*|SQL (numer seryjny)|\*|
|Wychodzący|\*|\*|\*|Magazyn (numer seryjny)|\*|
|Wychodzący|\*|\*|\*|VirtualNetwork|\*|

Ruchu przy użyciu następujących adresów IP w podsieci z listy dozwolonych adresów. SQL (magazynu metadanych) i magazynu (artefaktów i dziennika), należy używać Sql i Storage [tagów usług](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

|Region świadczenia usługi Azure Databricks|Usługa|Publiczny adres IP|
|-----------------------|-------|---------|
|Wschodnie stany USA|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Wschodnie stany USA 2|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Środkowo-północne stany USA|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Środkowe stany USA|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Środkowo-południowe stany USA|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|Zachodnie stany USA|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|Zachodnie stany USA 2|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|Kanada Środkowa|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|40.85.223.25/32 </br></br>13.71.184.74/32|
|Kanada Wschodnia|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|40.85.223.25/32 </br></br>13.71.184.74/32|
|Zachodnie Zjednoczone Królestwo|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|51.140.203.27/32 </br></br>51.140.204.4/32|
|Południowe Zjednoczone Królestwo|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europa Zachodnia|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|23.100.0.135/32 </br></br>52.232.19.246/32|
|Europa Północna|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|23.100.0.135/32 </br></br>52.232.19.246/32|
|Indie Środkowe|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|Indie Południowe|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|Indie Zachodnie|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|Azja Południowo-Wschodnia|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|52.187.0.85/32 </br></br>52.187.145.107/32|
|Azja Wschodnia|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|52.187.0.85/32 </br></br>52.187.145.107/32|
|Australia Wschodnia|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia Południowo-Wschodnia|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia Środkowa|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia Środkowa 2|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Japonia Wschodnia|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|13.78.19.235/32 </br></br>52.246.160.72/32|
|Japonia Zachodnia|Płaszczyzna kontroli translatora adresów Sieciowych </br></br>Aplikacja sieci Web|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="workspace-launch-errors"></a>Błędy uruchamiania obszaru roboczego

Uruchamianie obszaru roboczego w niestandardowa sieć wirtualna nie powiedzie się w usłudze Azure Databricks logowania na ekranie z następującym błędem: **"Po wystąpił błąd podczas tworzenia obszaru roboczego. Upewnij się, że niestandardowa konfiguracja sieci jest poprawny i spróbuj ponownie."**

Ten błąd jest spowodowany przez konfigurację sieci niespełniające wymagań. Upewnij się, czy wykonano instrukcje przedstawione w tym temacie, podczas tworzenia obszaru roboczego.

### <a name="cluster-creation-errors"></a>Błędy tworzenia klastra

**Wystąpienia jest niedostępny: Zasobów nie jest dostępny za pośrednictwem protokołu SSH.**

Możliwa przyczyna: blokowany jest ruch z płaszczyznę kontroli dla pracowników. Napraw, zapewniając, że reguły zabezpieczeń ruchu przychodzącego spełniają wymagania. Jeśli wdrażasz do istniejącej sieci wirtualnej podłączonej do sieci lokalnej, należy przejrzeć konfigurację przy użyciu informacji podanych w obszar roboczy usługi Azure Databricks nawiązywania połączenia z siecią lokalną.

**Błąd uruchamiania nieoczekiwany: Napotkano nieoczekiwany błąd podczas konfigurowania klastra. Spróbuj ponownie, a jeśli problem nie zniknie, skontaktuj się usługi Azure Databricks. Komunikat o błędzie wewnętrznym: Przekroczono limit czasu podczas wprowadzania do węzła.**

Możliwa przyczyna: blokowany jest ruch z procesów roboczych do punktów końcowych usługi Azure Storage. Napraw, zapewniając, że zasady zabezpieczeń dla ruchu wychodzącego spełniają wymagania. Jeśli używasz niestandardowych serwerów DNS, również sprawdzić stan serwerów DNS w sieci wirtualnej.

**Błąd uruchamiania dostawcy chmury: Podczas konfigurowania klastra wystąpił błąd dostawcy chmury. Zobacz Przewodnik usługi Azure Databricks, aby uzyskać więcej informacji. Kod błędu systemu Azure: AuthorizationFailed/InvalidResourceReference.**

Możliwa przyczyna: sieci wirtualnej lub podsieci już istnieje. Upewnij się, że istnieje sieć wirtualna i podsieci.

**Klaster jest zakończony. Przyczyna: Niepowodzenie uruchamiania Spark: Nie można uruchomić w czasie platformy Spark. Ten problem może być spowodowane nieprawidłowo Magazyn metadanych Hive, nieprawidłowe konfiguracje Spark lub nieprawidłowe działanie skryptów init. Można znaleźć w dziennikach sterownik Spark, aby rozwiązać ten problem, a jeśli problem nie zniknie, skontaktuj się usługi Databricks. Komunikat o błędzie wewnętrznym: Nie można uruchomić aparatu Spark: Sterownik nie powiodło się w czasie.**

Możliwa przyczyna: Kontener nie może komunikować się hostingu wystąpienie lub konta magazynu z DBFS. Naprawić poprzez dodanie niestandardowych tras do podsieci dla konta magazynu DBFS z następnego przeskoku jest Internet.

### <a name="notebook-command-errors"></a>Błędy polecenia notesu

**Polecenie nie odpowiada.**

Możliwa przyczyna: komunikacja procesu roboczego do procesu roboczego jest zablokowana. Napraw, upewniając się, że reguły zabezpieczeń ruchu przychodzącego do wymagań.

**Notes przepływ pracy zakończy się niepowodzeniem z wyjątkiem: com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

Możliwa przyczyna: ruch od pracowników do aplikacji sieci Web usługi Azure Databricks jest zablokowany. Napraw, upewniając się, że zasady zabezpieczeń dla ruchu wychodzącego do wymagań.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
