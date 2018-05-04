W poniższej tabeli przedstawiono limity skojarzone z różnymi warstwami usług (S1, S2, S3, F1). Aby uzyskać informacje o koszcie każdej *jednostki* w każdej z warstw, zobacz [Cennik usługi IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Zasób | Standardowa S1 | Standardowa S2 | Standardowa S3 | Bezpłatna F1 |
| --- | --- | --- | --- | --- |
| Komunikaty/dzień |400 000 |6 000 000 |300 000 000 |8000 |
| Maksymalna liczba jednostek |200 |200 |10 |1 |

> [!NOTE]
> Jeśli przewidujesz używanie ponad 200 jednostek z centrum w warstwie S1 lub S2 albo 10 jednostek z centrum w warstwie S3, skontaktuj się z pomocą techniczną firmy Microsoft.
> 
> 

W poniższej tabeli przedstawiono limity dotyczące zasobów usługi IoT Hub:

| Zasób | Limit |
| --- | --- |
| Maksymalna liczba płatnych centrów IoT na subskrypcję platformy Azure |10 |
| Maksymalna liczba bezpłatnych centrów IoT na subskrypcję platformy Azure |1 |
| Maksymalna liczba znaków w identyfikator urządzenia | 128 |
| Maksymalna liczba tożsamości urządzeń<br/> zwróconych w pojedynczym wywołaniu |1000 |
| Maksymalny okres przechowywania komunikatów usługi IoT Hub dla komunikatów przesyłanych z urządzenia do chmury |7 dni |
| Maksymalny rozmiar komunikatu przesyłanego z urządzenia do chmury |256 KB |
| Maksymalny rozmiar partii przesyłanej z urządzenia do chmury |256 KB |
| Maksymalna liczba komunikatów w partii przesyłanej z urządzenia do chmury |500 |
| Maksymalny rozmiar komunikatu przesyłanego z chmury do urządzenia |64 KB |
| Maksymalny czas wygaśnięcia komunikatu przesyłanego z chmury do urządzenia |2 dni |
| Maksymalna liczba dostarczonych komunikatów przesyłanych <br/> z chmury do urządzenia |100 |
| Maksymalna liczba dostarczonych komunikatów zwrotnych <br/> w odpowiedzi na komunikaty przesłane z chmury do urządzenia |100 |
| Maksymalny czas wygaśnięcia komunikatów zwrotnych <br/> w odpowiedzi na komunikaty przesłane z chmury do urządzenia |2 dni |
| Maksymalny rozmiar bliźniaczej reprezentacji urządzenia <br/> (tagi, zgłoszone właściwości i żądane właściwości) | 8 KB |
| Maksymalny rozmiar wartości ciągu bliźniaczej reprezentacji urządzenia | 4 KB |
| Maksymalna głębokość obiektu w bliźniaczej reprezentacji urządzenia | 5 |
| Maksymalny rozmiar ładunku metody bezpośredniej | 128 KB |
| Maksymalny okres przechowywania historii zadań | 30 dni |
| Maksymalna liczba równoczesnych zadań | 10 (warstwa S3), 5 (warstwa S2), 1 (warstwa S1) |
| Maksymalna liczba dodatkowych punktów końcowych | 10 (warstwy S1, S2 i S3) |
| Maksymalna liczba reguł rozsyłania komunikatów | 100 (warstwy S1, S2 i S3) |


> [!NOTE]
> Jeśli potrzebujesz więcej niż 10 płatnych centrów IoT w subskrypcji platformy Azure, skontaktuj się z pomocą techniczną firmy Microsoft.


> [!NOTE]
> Maksymalna liczba urządzeń, którymi możesz połączyć się z jednego centrum IoT jest obecnie, 500 000. Jeśli chcesz podwyższyć ten limit, skontaktuj się z [Microsoft Support](https://azure.microsoft.com/support/options/).

Usługa IoT Hub ogranicza żądania po przekroczeniu następujących limitów przydziału:

| Ograniczenie | Wartość na centrum |
| --- | --- |
| Operacje rejestru tożsamości <br/> (tworzenie, przywracanie, wyświetlanie, usuwanie), <br/> import/eksport zbiorczy lub pojedynczy |83.33/sec/Unit (jednostka-5000/min) (dla S3) <br/> 1.67/sec/Unit (jednostka/100/min) (dla S1 i S2). |
| Połączenia urządzenia |6000/sek/jednostkę (warstwa S3), 120/sek/jednostkę (warstwa S2), 12/sek/jednostkę (warstwa S1). <br/>Minimum 100/sek. |
| Liczba elementów wysłanych z urządzenia do chmury |6000/sek/jednostkę (warstwa S3), 120/sek/jednostkę (warstwa S2), 12/sek/jednostkę (warstwa S1). <br/>Minimum 100/sek. |
| Liczba elementów wysłanych z chmury do urządzenia | 83.33/sec/Unit (5000/min/jednostka) (S3) 1.67/sec/unit (jednostka/100/min) (dla S1 i S2). |
| Liczba odebranych elementów wysłanych z chmury do urządzenia |16.67/sec/unit (50000/min/jednostka) (S3) 833.33/sec/Unit (1000/min/jednostka) (S1 i S2). |
| Operacje przekazywania plików |Plik 83.33 przekazywania s powiadomienia jednostki 1.67 (5000/min/jednostka) (S3), plik przekazywania powiadomień/s/jednostka (jednostka/100/min) (S1 i S2). <br/> Jednocześnie może istnieć 10 000 identyfikatorów URI sygnatury dostępu współdzielonego dla konta usługi Azure Storage.<br/> Jednocześnie może istnieć 10 identyfikatorów URI sygnatury dostępu współdzielonego. |
| Metody bezpośrednie | 24MB/s/jednostki (w przypadku S3), 480KB/s/jednostka (S2) 160KB/s/jednostki (S1)<br/> Oparte na 8KB ograniczania rozmiaru miernika. |
| Liczba odczytów bliźniaczej reprezentacji urządzenia | 50/sek/jednostkę (warstwa S3), maksymalnie 10/sek lub 1/sek/jednostkę (warstwa S2), 10/sek (warstwa S1) |
| Liczba aktualizacji bliźniaczej reprezentacji urządzenia | 50/sek/jednostkę (warstwa S3), maksymalnie 10/sek lub 1/sek/jednostkę (warstwa S2), 10/sek (warstwa S1) |
| Operacje zadań <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 83.33/sec/Unit (5000/min/jednostka) (S3) 1.67/sec/unit (jednostka/100/min) (dla S2), 1.67/sec/unit (jednostka/100/min) (dla S1) |
| Przepływność operacji zadań poszczególnych urządzeń | 50/sek/jednostkę (warstwa S3), maksymalnie 10/sek lub 1/sek/jednostkę (warstwa S2), 10/sek (warstwa S1) |
