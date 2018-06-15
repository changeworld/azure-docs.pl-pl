Maszyna wirtualna platformy Azure obsługuje dołączanie wielu dysków danych. W tym artykule opisano elementy docelowe skalowalności i wydajności dla dysków danych maszyny Wirtualnej. Użyj następujących elementów docelowych, aby określić liczbę i rodzaj muszą spełniać wymagania dotyczące wydajności i pojemności dysku. 

> [!IMPORTANT]
> Aby uzyskać optymalną wydajność ograniczyć liczbę wysokiej wykorzystywanych dysków dołączonych do maszyny wirtualnej, aby uniknąć możliwych ograniczania. Jeśli wysokiej wszystkich dołączonych dysków nie są używane w tym samym czasie, maszyna wirtualna może obsługiwać większą liczbę dysków.

* **Dyskach zarządzanych Azure:** 

> | Zasób | Limit domyślny | Limit maksymalny |
> | --- | --- | --- |
> | Dyski Managed Disks w warstwie Standardowa | 10 000 | 50,000 |
> | Usługa Managed Disks z dyskami SSD w warstwie Standardowa | 10 000 | 50,000 |
> | Dyski Managed Disks w warstwie Premium | 10 000 | 50,000 |
> | Standard_LRS migawki | 10 000 | 50,000 |
> | Standard_ZRS migawki | 10 000 | 50,000 |
> | Premium_LRS migawki | 10 000 | 50,000 |
> | Zarządzanego obrazu | 10 000 | 50,000 |

* **Konto magazynu w warstwie Standardowa:** maksymalna całkowita liczba żądań dla konta magazynu w warstwie Standardowa to 20 000 operacji wejścia/wyjścia na sekundę (IOPS). Łączna liczba IOPS na wszystkich dyskach maszyny wirtualnej w koncie magazynu w warstwie Standardowa nie powinna przekroczyć tego limitu.
  
    Możesz orientacyjnie obliczyć liczbę intensywnie używanych dysków obsługiwanych przez jedno konto magazynu w warstwie Standardowa na podstawie limitu liczby żądań. Na przykład podstawowa warstwy maszyny Wirtualnej, maksymalna liczba dysków wysokiej wykorzystywanych jest o 66 (20 000/300 IOPS dla każdego dysku) oraz dla maszyny Wirtualnej warstwy standardowa, jest około 40 (20 000/500 IOPS dla każdego dysku). 

* **Konta magazynu w warstwie Premium:** maksymalna całkowita przepływność konta magazynu w warstwie Premium to 50 Gb/s. Całkowita przepływność na wszystkich dyskach maszyny wirtualnej nie powinna przekroczyć tego limitu.

