
* Konwersja wymaga ponownego uruchomienia maszyny wirtualnej, więc należy zaplanować migrację maszyn wirtualnych podczas istniejącego okna obsługi. 

* Konwersja jest nieodwracalna. 

* Należy pamiętać, że jakaś użytkownikom [Współautor·maszyny·wirtualnej](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) rola nie będzie mógł zmienić rozmiar maszyny Wirtualnej (ponieważ mogą one wstępne konwersji). Jest to spowodowane maszyny wirtualne z dyskami zarządzanych wymagają od użytkownika posiadania uprawnienia Microsoft.Compute/disks/write na dyskach systemu operacyjnego.

* Należy przetestować konwersję. Przeprowadź migrację testowej maszyny wirtualnej, zanim przeprowadzisz migrację w środowisku produkcyjnym.

* Podczas konwersji cofany jest przydział maszyny wirtualnej. Maszyna wirtualna otrzyma nowy adres IP przy jej ponownym uruchomieniu po konwersji. W razie potrzeby do maszyny wirtualnej można [przypisać statyczny adres IP](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Oryginalne wirtualne dyski twarde i konto magazynu używane przez maszynę wirtualną przed konwersją nie są usuwane. Nadal będą za nie naliczane opłaty. Aby uniknąć naliczania opłat za te artefakty, usuń oryginalne obiekty blob dysków VHD po upewnieniu się, że konwersja została zakończona.

* Przejrzyj minimalną wersję agenta maszyny Wirtualnej platformy Azure wymagane do obsługi procesu konwersji. Aby uzyskać informacje na temat sprawdzania i zaktualizuj swoją wersję agenta, zobacz [minimalnej wersji obsługę agentów maszyny Wirtualnej na platformie Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)