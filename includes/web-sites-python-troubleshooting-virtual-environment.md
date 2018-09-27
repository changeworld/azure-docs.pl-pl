Skrypt wdrożenia pominie etap tworzenia środowiska wirtualnego platformy Azure, jeśli wykryje, że zgodne środowisko wirtualne już istnieje.  Może to znacznie przyspieszyć wdrożenie.  Pakiety, które zostały już zainstalowane, zostaną pominięte przez narzędzie pip.

W niektórych sytuacjach może zajść potrzeba wymuszenia usunięcia tego środowiska wirtualnego.  Należy to zrobić, jeśli środowisko wirtualne ma być częścią repozytorium.  Można to zrobić także w sytuacji, gdy mają zostać usunięte niektóre pakiety lub mają zostać przetestowane zmiany w pliku requirements.txt.

Istnieje kilka opcji zarządzania istniejącym środowiskiem wirtualnym na platformie Azure:

### <a name="option-1-use-ftp"></a>Opcja 1: użycie protokołu FTP
Za pomocą klienta FTP należy połączyć się z serwerem, by można było usunąć folder env.  Należy zwrócić uwagę, że niektóre klienty FTP (na przykład przeglądarki sieci Web) mogą pracować w trybie tylko do odczytu i nie pozwalają na usuwanie folderów, dlatego należy upewnić się, że używany klient FTP ma taką możliwość.  Nazwa hosta FTP i nazwa użytkownika są wyświetlane w bloku aplikacji internetowej w witrynie [Azure Portal](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Opcja 2: przełączenie w czasie wykonywania
Ten wariant wykorzystuje fakt, że skrypt wdrożenia spowoduje usunięcie folderu env, jeśli nie odpowiada on żądanej wersji języka Python.  Spowoduje to skutecznie usunięcie istniejącego środowiska i utworzenie nowego.

1. Przełącz się na inną wersję języka Python (za pośrednictwem pliku runtime.txt lub bloku **Ustawienia aplikacji** w witrynie Azure Portal)
2. wyślij pewne zmiany do usługi git (ignoruj błędy instalacji narzędzia pip, jeśli wystąpią)
3. Przełącz się do początkowej wersji języka Python
4. ponownie wyślij pewne zmiany do usługi git

### <a name="option-3-customize-deployment-script"></a>Opcja 3: dostosowywanie skryptu wdrożenia
Jeśli skrypt wdrożenia został dostosowany, można zmienić kod w pliku deploy.cmd, aby wymusić usunięcie folderu env.

