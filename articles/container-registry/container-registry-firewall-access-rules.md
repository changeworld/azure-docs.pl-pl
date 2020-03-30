---
title: Reguły dostępu do zapory
description: Skonfiguruj reguły, aby uzyskać dostęp do rejestru kontenerów platformy Azure zza zapory, umożliwiając dostęp do interfejsu API REST ("białej listy") i nazw domen punktów końcowych magazynu lub zakresów adresów IP specyficznych dla usługi.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168020"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurowanie reguł w celu uzyskiwania dostępu do rejestru kontenerów platformy Azure za zaporą

W tym artykule wyjaśniono, jak skonfigurować reguły na zaporze, aby umożliwić dostęp do rejestru kontenerów platformy Azure. Na przykład urządzenie usługi Azure IoT Edge za zaporą lub serwerem proxy może być konieczne uzyskiwać dostęp do rejestru kontenerów w celu wyciągnięcia obrazu kontenera. Lub zablokowany serwer w sieci lokalnej może wymagać dostępu do wypychania obrazu.

Jeśli zamiast tego chcesz skonfigurować reguły dostępu do sieci przychodzącej w rejestrze kontenerów tylko w ramach sieci wirtualnej platformy Azure lub z publicznego zakresu adresów IP, zobacz [Ograniczanie dostępu do rejestru kontenerów platformy Azure z sieci wirtualnej](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>Informacje o punktach końcowych rejestru

Aby pobierać lub wypychać obrazy lub inne artefakty do rejestru kontenerów platformy Azure, klient, taki jak demon platformy Docker, musi wchodzić w interakcje za pośrednictwem protokołu HTTPS z dwoma różnymi punktami końcowymi.

* **Punkt końcowy interfejsu API interfejsu API rest rejestru** — operacje uwierzytelniania i zarządzania rejestrami są obsługiwane za pośrednictwem publicznego punktu końcowego interfejsu API REST rejestru. Ten punkt końcowy jest nazwą serwera logowania rejestru lub skojarzonym zakresem adresów IP. 

* **Punkt końcowy magazynu** — platforma Azure [przydziela magazyn obiektów blob](container-registry-storage.md) w kontach usługi Azure Storage w imieniu każdego rejestru, aby zarządzać danymi dla obrazów kontenerów i innych artefaktów. Gdy klient uzyskuje dostęp do warstw obrazu w rejestrze kontenerów platformy Azure, żądaje przy użyciu punktu końcowego konta magazynu dostarczonego przez rejestr.

Jeśli rejestr jest [replikowany geograficznie,](container-registry-geo-replication.md)klient może być konieczne do interakcji z REST i punktów końcowych magazynu w określonym regionie lub w wielu regionach replikowanych.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Zezwalaj na dostęp do rest i nazw domen magazynu

* **PUNKT KOŃCOWY REST** — zezwalaj na dostęp do w pełni kwalifikowanej nazwy serwera logowania rejestru, takiej jak`myregistry.azurecr.io`
* **Magazyn (dane) punkt końcowy** — zezwalaj na dostęp do wszystkich kont magazynu obiektów blob platformy Azure przy użyciu symbolu wieloznacznego`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Zezwalaj na dostęp według zakresu adresów IP

Jeśli twoja organizacja ma zasady zezwalane tylko na dostęp do określonych adresów IP lub zakresów adresów, pobierz [zakresy adresów IP platformy Azure i tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519).

Aby znaleźć zakresy adresów IP punktu końcowego ACR REST, dla których musisz zezwolić na dostęp, wyszukaj **azurecontainerRegistry** w pliku JSON.

> [!IMPORTANT]
> Zakresy adresów IP dla usług platformy Azure mogą ulec zmianie, a aktualizacje są publikowane co tydzień. Pobierz plik JSON regularnie i dokonać niezbędnych aktualizacji w regułach dostępu. Jeśli scenariusz obejmuje konfigurowanie reguł grupy zabezpieczeń sieci w sieci wirtualnej platformy Azure, aby uzyskać dostęp do rejestru kontenerów platformy Azure, użyj [tagu usługi](#allow-access-by-service-tag) **AzureContainerRegistry.**
>

### <a name="rest-ip-addresses-for-all-regions"></a>Adresy IP REST dla wszystkich regionów

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>Adresy IP REST dla określonego regionu

Wyszukaj określony region, taki jak **AzureContainerRegistry.AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Przechowywanie adresów IP dla wszystkich regionów

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Przechowywanie adresów IP dla określonych regionów

Wyszukaj określony region, taki jak **Storage.AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Zezwalaj na dostęp według tagu usługi

W sieci wirtualnej platformy Azure użyj reguł zabezpieczeń sieci do filtrowania ruchu z zasobu, takiego jak maszyna wirtualna do rejestru kontenerów. Aby uprościć tworzenie reguł sieci platformy Azure, użyj [tagu usługi](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry** . Tag usługi reprezentuje grupę prefiksów adresów IP, aby uzyskać dostęp do usługi platformy Azure globalnie lub na region platformy Azure. Tag jest automatycznie aktualizowany po zmianie adresów. 

Na przykład utwórz regułę wychodzącej grupy zabezpieczeń sieciowej z docelowym dziennikiem **AzureContainerRegistry,** aby zezwolić na ruch do rejestru kontenerów platformy Azure. Aby zezwolić na dostęp do tagu usługi tylko w określonym regionie, określ region w następującym formacie: **AzureContainerRegistry**. [*nazwa regionu*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurowanie reguł zapory klienta dla mcr

Jeśli chcesz uzyskać dostęp do rejestru kontenerów firmy Microsoft (MCR) zza zapory, zapoznaj się ze wskazówkami dotyczącymi konfigurowania [reguł zapory klienta MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR jest podstawowym rejestrem dla wszystkich obrazów dokowanych opublikowanych przez firmę Microsoft, takich jak obrazy systemu Windows Server.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących zabezpieczeń sieci w zakresie platformy Azure](../security/fundamentals/network-best-practices.md)

* Dowiedz się więcej o [grupach zabezpieczeń](/azure/virtual-network/security-overview) w sieci wirtualnej platformy Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

