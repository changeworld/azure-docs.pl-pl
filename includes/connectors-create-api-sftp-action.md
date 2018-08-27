Teraz, że dodano wyzwalacza, jego czas się czymś interesujących danych, który jest generowany przez wyzwalacz. Wykonaj następujące kroki, aby dodać **SFTP - folder wyodrębniania** akcji. Ta akcja wyodrębnia zawartość pliku, po spełnieniu warunków zdefiniowane. 

Aby skonfigurować tej akcji, należy podać następujące informacje. Można zauważyć, że to łatwy w użyciu dane generowane przez wyzwalacz jako dane wejściowe dla niektórych właściwości dla nowego pliku:

| SFTP - extract właściwości folderu | Opis |
| --- | --- |
| Ścieżka pliku archiwum źródłowego |Jest to ścieżka do pliku wyodrębniania. Możesz wybierz jedną z tokenów z wcześniejszej operacji lub przeglądać serwer SFTP w celu znalezienia ścieżkę pliku. |
| Ścieżka folderu docelowego |Jest to ścieżka, w którym zostaną umieszczone wyodrębnionych plików. Można wybrać jedną z tokenów z wcześniejszych działań, jako ścieżkę docelową lub przejdź do serwera SFTP i wybierz ścieżkę. |
| Zastąpić? |Wskazuje, jeśli plik z taką samą nazwę jak wyodrębnionych plików znajduje się w ścieżka folderu docelowego, jeśli istniejący plik powinny być zastąpione, czy nie. |

Rozpocznijmy od dodawania akcji, aby wyodrębnić pliki, jeśli wcześniej zdefiniowaną warunek to *True*. 

1. Wybierz **Dodaj akcję**.        
   ![Obraz warunku akcji SFTP 6](./media/connectors-create-api-sftp/condition-6.png)   
2. Wybierz **SFTP - folder wyodrębniania** akcji      
   ![Obraz warunku akcji SFTP 7](./media/connectors-create-api-sftp/condition-7.png)   
3. Wybierz **ścieżka pliku archiwum źródłowego**              
   ![Obraz warunku akcji SFTP 9](./media/connectors-create-api-sftp/condition-9.png)   
4. Wybierz **ścieżka pliku** tokenu. Oznacza to, że ścieżkę pliku, który znaleźć wyzwalacz będzie używany jako ścieżka pliku archiwum źródłowego.           
   ![Obraz warunku akcji SFTP 10](./media/connectors-create-api-sftp/condition-10.png)   
5. Wybierz **ścieżka folderu docelowego**           
   ![Obraz warunku akcji SFTP 11](./media/connectors-create-api-sftp/condition-11.png)   
6. Wybierz **ścieżka pliku** tokenu. Oznacza to, że ścieżkę pliku, który znaleźć wyzwalacz będzie używany jako ścieżka docelowa dla wyodrębnionych plików.   
7. Wprowadź *\ExtractedFile* w **ścieżka folderu docelowego** kontroli. W tym zaraz po token ścieżki pliku w formancie ścieżka folderu docelowego.         
   ![Obraz warunku akcji SFTP 12](./media/connectors-create-api-sftp/condition-12.png)   
8. Wprowadź *True* w **chcesz go zastąpić?* sterowania, aby wskazać, że istniejące pliki powinny być zastąpione, jeśli mają taką samą nazwę jak wyodrębnionych plików.      
   ![Obraz warunku akcji SFTP 13](./media/connectors-create-api-sftp/condition-13.png)   
9. Zapisz zmiany w przepływie pracy  

