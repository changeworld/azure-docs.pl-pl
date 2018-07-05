Usługa i typ Twojej subskrypcji decydują o liczbie zapytań, które możesz wykonywać na sekundę. Upewnij się, że Twoja aplikacja zawiera logikę niezbędną do nieprzekraczania tego limitu przydziału. Gdy przekroczysz liczbę zapytań na sekundę, żądanie zakończy się niepowodzeniem z kodem stanu HTTP 429. Odpowiedź będzie również zawierać nagłówek Retry-After z liczbą sekund, którą trzeba odczekać przed wysłaniem kolejnego żądania.  
  
### <a name="denial-of-service-dos-versus-throttling"></a>Odmowa usługi (DOS) a ograniczanie przepustowości

Usługa rozróżnia ataki DOS od naruszenia limitu zapytań na sekundę. Jeśli usługa podejrzewa atak metodą odmowy usługi, żądanie kończy się powodzeniem (kod stanu HTTP to 200 OK), ale treść odpowiedzi jest pusta.