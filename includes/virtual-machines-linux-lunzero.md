Podczas dodawania dysków z danymi do maszyny Wirtualnej systemu Linux, mogą wystąpić błędy, jeśli dysk nie istnieje w jednostce LUN 0. Jeśli dodajesz dysk ręcznie przy użyciu `azure vm disk attach-new` polecenia i podaj jednostkę LUN (`--lun`) zamiast stosowanie platformy Azure określić odpowiednie jednostki LUN, należy zadbać, że dysku już istnieje / będzie istniał w momencie LUN 0. 

Rozważmy następujący przykład przedstawiający fragment danych wyjściowych z `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Dyski danych dwa istnieje w jednostce LUN 0 i 1 jednostki LUN (pierwszej kolumny `lsscsi` szczegóły danych wyjściowych `[host:channel:target:lun]`). Obydwa dyski powinny być accessbile z poziomu maszyny Wirtualnej. Jeśli ma ręcznie określić pierwszy dysk do dodania w jednostce LUN 1 i drugiego dysku w jednostce LUN, 2, nie widać dysków poprawnie z wewnątrz maszyny Wirtualnej.

> [!NOTE]
> Azure `host` wartość to 5 w tym przykładzie, ale to może się różnić w zależności od typu magazynu należy wybrać.
> 
> 

To zachowanie dysku nie jest problem Azure, ale w taki sposób, w którym jądra systemu Linux jest zgodny ze specyfikacją SCSI. Gdy jądra systemu Linux skanuje magistrali SCSI urządzenia podłączone, urządzenie musi zostać znaleziony o numerze LUN 0 Aby systemu kontynuując skanowanie w poszukiwaniu dodatkowych urządzeń. Sposób:

* Przejrzyj dane wyjściowe `lsscsi` po dodaniu dysku danych, aby sprawdzić, czy dysk pod numerem LUN 0.
* Jeśli dysk nie jest wyświetlany prawidłowo w ramach maszyny Wirtualnej, sprawdź, czy istnieje dysku o numerze LUN 0.

