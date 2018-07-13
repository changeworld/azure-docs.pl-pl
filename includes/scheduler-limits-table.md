W poniższej tabeli opisano każdy z głównych przydziały, limity, wartości domyślne i ograniczenia w usłudze Azure Scheduler.

| Zasób | Opis elementu limit |
| --- | --- |
| **Rozmiar zadania** |Maksymalny rozmiar zadania jest 16 tys. Jeśli PUT lub PATCH powoduje zadania przekracza te limity, 400 Niewłaściwe żądanie zwróciła kod stanu. |
| **Rozmiar adresu URL żądania** |Maksymalny rozmiar adresu URL żądania wynosi 2048 znaków. |
| **Rozmiar nagłówka agregacji** |Rozmiar maksymalny łączny nagłówka wynosi 4096 znaków. |
| **Liczba w nagłówku** |Nagłówek maksymalna liczba to 50 nagłówków. |
| **Rozmiar treści** |Rozmiar maksymalny treści jest 8192 znaków. |
| **Zakres cyklu** |Maksymalna wartość cyklu zakres jest 18 miesięcy. |
| **Czas na czas rozpoczęcia** |Maksymalna liczba "przyszedł czas na czas" wynosi 18 miesięcy. |
| **Historia zadania** |Treść odpowiedzi maksymalna, przechowywanych w historii zadań wynosi 2048 bajtów. |
| **Częstotliwość** |Domyślny limit przydziału maksymalna częstotliwość to 1 godziny w kolekcji zadań w warstwie bezpłatna i 1 minuta w kolekcji standardowych zadań. Maksymalna częstotliwość jest konfigurowany na kolekcji zadań, jest niższa niż wartość maksymalna. Wszystkie zadania w kolekcji zadań są ograniczone wartość ustawioną na kolekcji zadań. Jeśli spróbujesz utworzyć zadanie z częstotliwością wyższa niż maksymalna częstotliwość na kolekcji zadań żądanie zakończy się niepowodzeniem z kodem stanu 409 konflikt. |
| **Zadania** |Domyślny limit przydziału max zadania jest 5 zadań w kolekcji zadań w warstwie bezpłatna i 50 zadań w kolekcji standardowych zadań. Maksymalna liczba zadań jest konfigurowane na kolekcji zadań. Wszystkie zadania w kolekcji zadań są ograniczone wartość ustawioną na kolekcji zadań. Jeśli spróbujesz utworzyć większą liczbę zadań niż zadania maksymalny limit przydziału, żądanie kończy się niepowodzeniem z kodem stanu 409 konflikt. |
| **Kolekcje zadań** |Maksymalna liczba kolekcji zadań na subskrypcję wynosi 200 000. |
| **Czas przechowywania historii zadań** |Historia zadania są zachowywane przez maksymalnie 2 miesiące lub maksymalnie 1000 ostatniego wykonania. |
| **Zadania ukończone i uszkodzoną przechowywania** |Zadania ukończone i uszkodzoną są przechowywane przez 60 dni. |
| **limit czasu** |Brak statycznego (nie można konfigurować) limitu czasu 60 sekund dla akcji HTTP. W przypadku dłużej uruchomionej operacji postępuj zgodnie z asynchronicznego protokołów HTTP; na przykład niezwłocznie zawrócone 202, ale nadal działa w tle. |

