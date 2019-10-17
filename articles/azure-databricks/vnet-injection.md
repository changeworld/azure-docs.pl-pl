---
title: Wdrażanie usługi Azure Databricks w sieci wirtualnej
description: W tym artykule opisano sposób wdrażania Azure Databricks w sieci wirtualnej, znanej również jako iniekcja wirtualna.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 0bb3221c201e6dd4dd17cca8ef7e3ed3331de228
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432664"
---
# <a name="deploy-azure-databricks-in-your-virtual-network"></a>Wdrażanie usługi Azure Databricks w sieci wirtualnej

Domyślne wdrożenie Azure Databricks to w pełni zarządzana usługa na platformie Azure: wszystkie zasoby płaszczyzny danych, w tym sieć wirtualną (VNet), są wdrażane w zablokowanej grupie zasobów. W przypadku konieczności dostosowania sieci można jednak wdrożyć zasoby Azure Databricks w własnej sieci wirtualnej (nazywanej również iniekcją VNet), gdy program umożliwia:

* Łączenie Azure Databricks z innymi usługami platformy Azure (np. Azure Storage) w bezpieczniejszy sposób za pomocą punktów końcowych usługi.
* Połącz się z lokalnymi źródłami danych, które mają być używane z Azure Databricks, korzystając z tras zdefiniowanych przez użytkownika.
* Połącz Azure Databricks z sieciowym urządzeniem wirtualnym, aby sprawdzić cały ruch wychodzący i podjąć działania zgodnie z regułami zezwalania i odmowy.
* Skonfiguruj Azure Databricks do korzystania z niestandardowej usługi DNS.
* Skonfiguruj reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), aby określić ograniczenia ruchu wychodzącego.
* Wdróż Azure Databricks klastrów w istniejącej sieci wirtualnej.

Wdrożenie Azure Databricks zasobów do własnej sieci wirtualnej pozwala także wykorzystać elastyczne zakresy CIDR (w dowolnym miejscu między/16-/24 dla sieci wirtualnej i między/18-/26 dla podsieci).

  > [!NOTE]
  > Nie można zamienić sieci wirtualnej dla istniejącego obszaru roboczego. Jeśli bieżący obszar roboczy nie może obsłużyć wymaganej liczby aktywnych węzłów klastra, Utwórz inny obszar roboczy w większej sieci wirtualnej. Postępuj zgodnie z [tymi szczegółowymi krokami migracji](howto-regional-disaster-recovery.md#detailed-migration-steps) , aby skopiować zasoby (notesy, konfiguracje klastrów, zadania) ze starego do nowego obszaru roboczego.

## <a name="virtual-network-requirements"></a>Wymagania dotyczące sieci wirtualnej

Korzystając z Azure Databricks interfejsu wdrożenia obszaru roboczego w Azure Portal, można automatycznie skonfigurować istniejącą sieć wirtualną z wymaganymi podsieciami, sieciową grupą zabezpieczeń i ustawieniami listy dozwolonych. można też użyć Azure Resource Manager szablony służące do konfigurowania sieci wirtualnej i wdrażania obszaru roboczego.

Sieć wirtualna, w której wdrożono obszar roboczy Azure Databricks, musi spełniać następujące wymagania:

### <a name="location"></a>Lokalizacja

Sieć wirtualna musi znajdować się w tej samej lokalizacji co obszar roboczy Azure Databricks.

### <a name="subnets"></a>Podsieci

Sieć wirtualna musi zawierać dwie podsieci dedykowane Azure Databricks:

   1. Podsieć prywatna ze skonfigurowaną sieciową grupą zabezpieczeń, która umożliwia komunikację wewnętrzną klastra

   2. Podsieć Publiczna ze skonfigurowaną sieciową grupą zabezpieczeń, która umożliwia komunikację z płaszczyzną kontroli Azure Databricks.

### <a name="address-space"></a>Przestrzeń adresowa

Blok CIDR między/16-/24 dla sieci wirtualnej i blok CIDR między/18-/26 dla podsieci prywatnych i publicznych.

### <a name="whitelisting"></a>Umieszczanie na liście dozwolonych

Cały ruch wychodzący i przychodzący między podsieciami i Azure Databricksą płaszczyzną kontroli musi być listy dozwolonych.

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji opisano sposób tworzenia obszaru roboczego Azure Databricks w Azure Portal i wdrażania go we własnej istniejącej sieci wirtualnej. Azure Databricks aktualizuje sieć wirtualną przy użyciu dwóch nowych podsieci i sieciowych grup zabezpieczeń, korzystając z udostępnionych przez siebie zakresów CIDR, dozwolonych ruch przychodzący i wychodzący podsieci oraz wdraża obszar roboczy w zaktualizowanej sieci wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć sieć wirtualną, do której zostanie wdrożony obszar roboczy Azure Databricks. Możesz użyć istniejącej sieci wirtualnej lub utworzyć nową, ale sieć wirtualna musi znajdować się w tym samym regionie co obszar roboczy Azure Databricks, który ma zostać utworzony. Dla sieci wirtualnej wymagany jest zakres CIDR między/16-/24.

  > [!Warning]
  > Obszar roboczy z mniejszą siecią wirtualną — czyli niższym zakresem CIDR — może wykroczyć adresy IP (miejsce sieciowe) szybciej niż obszar roboczy z większą siecią wirtualną. Na przykład obszar roboczy z siecią wirtualną/24 i/26 podsieci może mieć maksymalnie 64 węzłów jednocześnie, podczas gdy obszar roboczy z siecią wirtualną/20 i/22 podsieci mogą zawierać maksymalnie 1024 węzły.

  Podsieci zostaną utworzone automatycznie podczas konfigurowania obszaru roboczego, a ty będziesz mieć możliwość zapewnienia zakresu CIDR dla podsieci podczas konfiguracji.

## <a name="configure-the-virtual-network"></a>Konfigurowanie sieci wirtualnej

1. W Azure Portal wybierz pozycję **+ Utwórz zasób > Analytics > Azure Databricks** , aby otworzyć okno dialogowe usługi Azure Databricks.

2. Wykonaj czynności konfiguracyjne opisane w sekcji Krok 2. Tworzenie obszaru roboczego Azure Databricks w przewodniku Wprowadzenie, a następnie wybierz obszar roboczy Wdróż Azure Databricks w Virtual Network opcji.

   ![Tworzenie usługi Azure Databricks](./media/vnet-injection/create-databricks-service.png)

3. Wybierz sieć wirtualną, której chcesz użyć.

   ![Opcje sieci wirtualnej](./media/vnet-injection/select-vnet.png)

4. Podaj zakresy CIDR w bloku między/18-/26 dla dwóch podsieci, Azure Databricks:

   * Podsieć publiczna zostanie utworzona za pomocą skojarzonej sieciowej grupy zabezpieczeń, która umożliwia komunikację z płaszczyzną kontroli Azure Databricks.
   * Podsieć prywatna zostanie utworzona za pomocą skojarzonej sieciowej grupy zabezpieczeń, która umożliwia komunikację wewnętrzną klastra.

5. Kliknij przycisk **Utwórz** , aby wdrożyć obszar roboczy Azure Databricks w sieci wirtualnej.

## <a name="advanced-resource-manager-configurations"></a>Zaawansowane konfiguracje Menedżera zasobów

Jeśli chcesz mieć większą kontrolę nad konfiguracją sieci wirtualnej — na przykład chcesz użyć istniejących podsieci, użyć istniejących sieciowych grup zabezpieczeń lub utworzyć własne reguły zabezpieczeń — możesz użyć następujących szablonów Azure Resource Manager zamiast Konfiguracja sieci wirtualnej portalu i wdrożenie obszaru roboczego.

### <a name="all-in-one"></a>Wszystko w jednym

Aby utworzyć sieć wirtualną, sieciowe grupy zabezpieczeń i Azure Databricks w jednym obszarze roboczym, użyj [szablonu wszystko w jednym dla obszaru roboczego wstrzyknięte obszary robocze](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

W przypadku korzystania z tego szablonu nie trzeba wykonywać żadnych ręcznych listy dozwolonych ruchu w podsieci.

### <a name="network-security-groups"></a>Sieciowe grupy zabezpieczeń

Aby utworzyć sieciowe grupy zabezpieczeń z regułami wymaganymi dla istniejącej sieci wirtualnej, użyj [szablonu sieciowej grupy zabezpieczeń dla iniekcji sieci wirtualnej datakostka](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

W przypadku korzystania z tego szablonu nie trzeba wykonywać żadnych ręcznych listy dozwolonych ruchu w podsieci.

### <a name="virtual-network"></a>Sieć wirtualna

Aby utworzyć sieć wirtualną z odpowiednimi podsieciami publicznymi i prywatnymi, użyj [szablonu Virtual Network na potrzeby iniekcji sieci wirtualnej datakostka](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Jeśli użyjesz tego szablonu bez również używania szablonu sieciowe grupy zabezpieczeń, musisz ręcznie dodać reguły listy dozwolonych do sieciowych grup zabezpieczeń używanych z siecią wirtualną.

### <a name="azure-databricks-workspace"></a>Azure Databricks obszar roboczy

Aby wdrożyć obszar roboczy Azure Databricks w istniejącej sieci wirtualnej z podsieciami publicznymi i prywatnymi oraz poprawnie skonfigurowanymi grupami zabezpieczeń sieci, użyj [szablonu obszaru roboczego dla iniekcji wirtualnej datakostki](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Jeśli użyjesz tego szablonu bez również używania szablonu sieciowe grupy zabezpieczeń, musisz ręcznie dodać reguły listy dozwolonych do sieciowych grup zabezpieczeń używanych z siecią wirtualną.

## <a name="whitelisting-subnet-traffic"></a>Ruch podsieci listy dozwolonych

Jeśli nie korzystasz z szablonów [Azure Portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) lub [Azure Resource Manager](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) do tworzenia grup zabezpieczeń sieci, musisz ręcznie dozwolonych następujący ruch w podsieciach.

|Kierunek|Protocol (Protokół)|Źródło|Port źródłowy|Cel|Port docelowy|
|---------|--------|------|-----------|-----------|----------------|
|Przychodzące|\*|VirtualNetwork|\*|\*|\*|
|Przychodzące|\*|Płaszczyzna kontroli translator adresów sieciowych IP|\*|\*|22|
|Przychodzące|\*|Płaszczyzna kontroli translator adresów sieciowych IP|\*|\*|5557|
|Wychodzące|\*|\*|\*|Webapp IP|\*|
|Wychodzące|\*|\*|\*|SQL (tag usługi)|\*|
|Wychodzące|\*|\*|\*|Magazyn (tag usługi)|\*|
|Wychodzące|\*|\*|\*|VirtualNetwork|\*|

Dozwolonych ruch podsieci przy użyciu następujących adresów IP. W przypadku usług SQL (magazynu metadanych) i magazynu (artefaktu i magazynu dzienników) należy używać [tagów usługi](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)SQL i magazynu.

|Region Azure Databricks|Usługa|Publiczny adres IP|
|-----------------------|-------|---------|
|Wschodnie stany USA|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Wschodnie stany USA 2|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Północno-środkowe stany USA|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Środkowe stany USA|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Południowo-środkowe stany USA|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Zachodnie stany USA|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Zachodnie stany USA 2|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Kanada Środkowa|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Kanada Wschodnia|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Zachodnie Zjednoczone Królestwo|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Południowe Zjednoczone Królestwo|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europa Zachodnia|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Europa Północna|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Indie Środkowe|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Indie Południowe|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Indie Zachodnie|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Azja Południowo-wschodnia|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Azja Wschodnia|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Australia Wschodnia|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia Południowo-Wschodnia|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia Środkowa|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia Środkowa 2|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Japonia Wschodnia|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Japonia Zachodnia|Płaszczyzna kontroli translator adresów sieciowych </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="workspace-launch-errors"></a>Błędy uruchamiania obszaru roboczego

Uruchamianie obszaru roboczego w niestandardowej sieci wirtualnej kończy się niepowodzeniem na ekranie logowania Azure Databricks z powodu następującego błędu: **"Wystąpił błąd podczas tworzenia obszaru roboczego. Upewnij się, że konfiguracja sieci niestandardowej jest poprawna, i spróbuj ponownie.**

Ten błąd jest spowodowany przez konfigurację sieci, która nie spełnia wymagań. Upewnij się, że wykonano instrukcje przedstawione w tym temacie podczas tworzenia obszaru roboczego.

### <a name="cluster-creation-errors"></a>Błędy tworzenia klastra

**Wystąpienia nieosiągalne: zasoby były nieosiągalne za pośrednictwem protokołu SSH.**

Możliwa przyczyna: Zablokowano ruch od płaszczyzny kontroli do pracowników. Rozwiąż problem, upewniając się, że reguły zabezpieczeń ruchu przychodzącego spełniają wymagania. Jeśli wdrażasz program do istniejącej sieci wirtualnej podłączonej do sieci lokalnej, Przejrzyj konfigurację, korzystając z informacji podanych w temacie łączenie obszaru roboczego Azure Databricks z siecią lokalną.

**Nieoczekiwany błąd uruchamiania: Wystąpił nieoczekiwany błąd podczas konfigurowania klastra. Ponów próbę i skontaktuj się z Azure Databricks, jeśli problem będzie nadal występował. Komunikat o błędzie wewnętrznym: przekroczenie limitu czasu podczas umieszczania węzła.**

Możliwa przyczyna: Zablokowano ruch od pracowników do punktów końcowych usługi Azure Storage. Naprawianie przez zapewnienie, że reguły zabezpieczeń ruchu wychodzącego spełniają wymagania. W przypadku korzystania z niestandardowych serwerów DNS sprawdź również stan serwerów DNS w sieci wirtualnej.

**Niepowodzenie uruchomienia dostawcy chmury: Wystąpił błąd dostawcy w chmurze podczas konfigurowania klastra. Aby uzyskać więcej informacji, zobacz Przewodnik Azure Databricks. Kod błędu platformy Azure: AuthorizationFailed/InvalidResourceReference.**

Możliwa przyczyna: Sieć wirtualna lub podsieci już nie istnieją. Upewnij się, że sieć wirtualna i podsieci istnieją.

**Klaster został przerwany. Przyczyna: błąd uruchamiania platformy Spark: nie można uruchomić platformy Spark w czasie. Przyczyną tego problemu może być wadliwe działanie magazyn metadanych Hive, nieprawidłowe konfiguracje platformy Spark lub nieprawidłowe działanie skryptów inicjacji. Zapoznaj się z dziennikami sterowników platformy Spark, aby rozwiązać ten problem, i skontaktuj się z nimi, jeśli problem będzie nadal występował. Komunikat o błędzie wewnętrznym: nie można uruchomić platformy Spark: nie można uruchomić sterownika w czasie.**

Możliwa przyczyna: kontener nie może komunikować się z wystąpieniem hostingu lub kontem magazynu DBFS. Rozwiąż przez dodanie niestandardowej trasy do podsieci dla konta magazynu DBFS z następnym przeskokiem z Internetu.

### <a name="notebook-command-errors"></a>Błędy poleceń notesu

**Polecenie nie odpowiada**

Możliwa przyczyna: komunikacja między procesami roboczymi jest zablokowana. Aby rozwiązać ten problem, upewnij się, że reguły zabezpieczeń dla ruchu przychodzącego spełniają wymagania.

**Przepływ pracy notesu kończy się niepowodzeniem z powodu wyjątku: com. datakosteks. Workflowexception: org. Apache. http. poł. ConnectTimeoutException**

Możliwa przyczyna: Zablokowano ruch od pracowników do Azure Databricks WEBAPP. Napraw, aby upewnić się, że reguły zabezpieczeń ruchu wychodzącego spełniają wymagania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
