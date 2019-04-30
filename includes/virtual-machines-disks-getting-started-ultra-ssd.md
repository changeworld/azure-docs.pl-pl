---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3b596e5bad8202d88ea06c7eee114bec1063a35f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075693"
---
# <a name="enabling-azure-ultra-ssds"></a>Włączanie platformy Azure, najwyższej dysków SSD

Azure ultra SSD dostarczać wysokiej przepływności, wysoka operacje We/Wy i magazynu dyskowego niskie opóźnienia dla maszyn wirtualnych IaaS platformy Azure. Ta nowa oferta zapewnia początku wydajności wiersza w poziomach dostępności jako ofert istniejących dysków. Dodatkowe korzyści ultra SSD obejmują możliwość dynamicznie zmieniać wydajność dysku wraz z obciążeń, bez konieczności ponownego uruchamiania maszyn wirtualnych. Największa dyski SSD są odpowiednie dla obciążeń intensywnie korzystających z danych, takich jak SAP HANA, najwyższej warstwy bazy danych i transakcji duże obciążenia.

Obecnie najwyższej dyski SSD są w wersji zapoznawczej, więc użytkownik musi [rejestrowanie](https://aka.ms/UltraSSDPreviewSignUp) w wersji zapoznawczej, aby można było uzyskiwać do nich dostęp.

Po zatwierdzeniu Uruchom jedno z poniższych poleceń, aby określić, która strefa w regionie wschodnie stany USA 2 ultra dysku, aby wdrożyć:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

INTERFEJS WIERSZA POLECENIA: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

Odpowiedź będzie miała podobny do poniższego, formularza, gdzie X jest strefa będzie używana do wdrażania w regionie wschodnie stany USA 2. X może być 1, 2 lub 3.

|ResourceType  |Name (Nazwa)  |Lokalizacja  |Strefy  |Ograniczenie  |Możliwości  |Wartość  |
|---------|---------|---------|---------|---------|---------|---------|
|dyski     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Jeśli nie ma odpowiedzi w poleceniu, oznacza to rejestrację, aby ta funkcja jest nadal oczekujące na zatwierdzenie lub niezatwierdzone jeszcze.

Skoro już wiesz, które strefy do wdrożenia, postępuj zgodnie z instrukcjami wdrażania, w tym artykule, aby uzyskać pierwszy maszyn wirtualnych wdrożonych przy użyciu najwyższej dysków SSD.

## <a name="deploying-an-ultra-ssd"></a>Wdrażanie ultra dyski SSD

Najpierw Ustal, rozmiar maszyny Wirtualnej do wdrożenia. W ramach tej wersji zapoznawczej obsługiwane są tylko rodziny DsV3 i EsV3 maszyn wirtualnych. Znajduje się w drugiej tabeli, w tym [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) Aby uzyskać szczegółowe informacje o tych rozmiarach maszyn wirtualnych.
Także odwoływać się do przykładu [Utwórz Maszynę wirtualną przy użyciu wielu SSD ultra](https://aka.ms/UltraSSDTemplate), który wskazuje, jak utworzyć Maszynę wirtualną za pomocą wielu ultra dysków SSD.

Poniżej opisano zmiany nowych/zmodyfikowanych szablonu usługi Resource Manager: **apiVersion** dla `Microsoft.Compute/virtualMachines` i `Microsoft.Compute/Disks` musi być ustawiona jako `2018-06-01` (lub nowsza).

Określ dysku jednostka Sku UltraSSD_LRS, pojemności dysku, operacje We/Wy i przepustowości w MB/s, aby utworzyć dysk największa. Oto przykład, który tworzy dysk z 1024 GiB (GiB = 2 ^ 30 bajtów), 80 000 operacji We/Wy i 1200 MB/s (MB/s = 10 ^ 6 bajtów na sekundę):

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

Dodaj dodatkowe możliwości we właściwościach maszyny Wirtualnej, aby wskazać jej ultra włączone (dotyczą [przykładowe](https://aka.ms/UltraSSDTemplate) pełny szablon usługi Resource Manager):

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

Po zaaprowizowaniu maszyny Wirtualnej można podzielić na partycje i sformatować dyski z danymi i skonfigurować je dla obciążeń.

## <a name="additional-ultra-ssd-scenarios"></a>Dodatkowe scenariusze SSD ultra

- Podczas tworzenia maszyny Wirtualnej ultra SSD niejawnie tworzone są również. Jednak te dyski zostaną odebrane wartości domyślnej (500) na SEKUNDĘ i przepływność (8 MiB/s).
- Dodatkowe SSD ultra można dołączyć do zgodnych maszyn wirtualnych.
- Największa SSD obsługuje dopasowywanie atrybuty wydajności dysku (operacje We/Wy i przepływność) w czasie wykonywania bez odłączeniem dysku od maszyny wirtualnej. Po wystawieniu wydajności operacji zmiany rozmiaru dysku na dysku może potrwać do godziny rzeczywiście zostały wprowadzone zmiany.
- Pojemność dysku rośnie wymagają cofnięcie przydziału maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz spróbować nowy typ dysku, a nie jeszcze zarejestrowanym użytkownikiem wersji zapoznawczej, [żądania dostępu za pośrednictwem tej ankiety](https://aka.ms/UltraSSDPreviewSignUp).
