Platforma Azure określa wersję języka Python dla swojego środowiska wirtualnego, stosując następuje priorytety:

1. wersja określona w pliku runtime.txt w folderze głównym
2. wersja określona przez ustawienie języka Python w konfiguracji aplikacji internetowej (blok **Ustawienia** > **Ustawienia aplikacji** dla aplikacji internetowej w witrynie Azure Portal)
3. python-2.7 jest wartością domyślną, jeśli nie określono żadnej z powyższych wersji

Prawidłowymi wartościami dla zawartości 

    \runtime.txt

są:

* python-2.7
* python-3.4

Jeśli wersja mikro (trzecia cyfra) jest określona, zostanie zignorowana.

