| **Składnik** | **Wymaganie** |
| --- |---|
| Rdzenie procesora CPU| 8 |
| Pamięć RAM | 16 GB|
| Liczba dysków | 3, w tym dysku systemu operacyjnego, dysku pamięci podręcznej serwera przetwarzania i przechowywania dysku powrotu po awarii |
| Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
| Wolne miejsce na dysku (dysk przechowywania) | 600 GB|
| System operacyjny  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Ustawienia regionalne systemu operacyjnego | Angielski (en-us)|
| Wersja programu VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Zasady grupy| Nie włączaj tych zasad grupy: <br> -Uniemożliwić dostęp do wiersza polecenia. <br> -Uniemożliwić dostęp do narzędzia do edycji rejestru. <br> — Logika zaufania dla załączników. <br> -Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| IIS | -Brak istniejących domyślnej witryny sieci Web <br> -Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx) ustawienie  <br> -Brak istniejących witryny sieci Web/aplikacja nasłuchuje na porcie 443<br>|
| Typ karty Sieciowej | VMXNET3 (jeśli są wdrażane jako maszyny Wirtualnej VMware) |
| Typ adresu IP | Statyczny |
| Dostęp do Internetu | Serwer musi mieć dostęp do tych adresów URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (nie wymagane dla skalowalnych w poziomie serwerów procesu) <br> - time.nist.gov <br> - time.windows.com |
| Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych)|
