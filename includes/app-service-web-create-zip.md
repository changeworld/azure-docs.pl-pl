## <a name="create-a-project-zip-file"></a>Tworzenie pliku ZIP projektu

Upewnij się, że nadal znajdujesz się w katalogu głównym przykładowego projektu. Utwórz archiwum ZIP z wszystkimi elementami w projekcie. Następujące polecenie używa domyślnego narzędzia w terminalu:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Później przekaż ten plik ZIP do platformy Azure i wdróż go w usłudze App Service.