Poniższa tabela zawiera opis głównych przydziałów, limity, wartości domyślne i limity w harmonogramie Azure.

| Zasób | Opis elementu limit |
| --- | --- |
| **Rozmiar zadania** |Maksymalny rozmiar zadania jest 16 KB. Jeśli PUT lub POPRAWKĘ powoduje większe niż limity te zadania, 400 Niewłaściwe żądanie kod stanu jest zwracany. |
| **Rozmiar adresu URL żądania** |Maksymalny rozmiar adresu URL żądania jest 2048 znaków. |
| **Rozmiar nagłówka agregacji** |Rozmiar maksymalny łączny nagłówka to 4096 znaków. |
| **Liczba nagłówka** |Liczba maksymalna nagłówka jest 50 nagłówków. |
| **Rozmiar treści** |Rozmiar maksymalny treści to 8192 znaków. |
| **Zakres cyklu** |Maksymalna wartość cyklu zakres jest 18 miesięcy. |
| **Czas na czas rozpoczęcia** |Maksymalna liczba "czasu godzina rozpoczęcia" to 18 miesięcy. |
| **Historia zadania** |Treść odpowiedzi maksymalną przechowywanych w historii zadań wynosi 2048 bajtów. |
| **Częstotliwość** |Przydział maksymalnej częstotliwości domyślny jest 1 godzina bezpłatna kolekcja zadań i 1 minuta w kolekcji standardowych zadania. Częstotliwość max jest konfigurowany na kolekcja zadań jest niższa niż wartość maksymalna. Wszystkie zadania w kolekcji zadań są ograniczone wartość ustawioną w kolekcji zadań. Jeśli próba utworzenia zadania z częstotliwością wyższa niż wartość maksymalna częstotliwości na kolekcji zadań żądanie zakończy się niepowodzeniem z kodem stanu 409 konflikt. |
| **Zadania** |Domyślnego przydziału maksymalnej zadania jest 50 zadań w kolekcji standardowych zadania i 5 zadania w bezpłatna kolekcja zadań. Maksymalna liczba zadań jest konfigurowane na kolekcji zadań. Wszystkie zadania w kolekcji zadań są ograniczone wartość ustawioną w kolekcji zadań. Jeśli próbujesz utworzyć większą liczbę zadań niż zadania maksymalny limit przydziału, żądanie kończy się niepowodzeniem z kodem stanu 409 konflikt. |
| **Kolekcje zadań** |Maksymalna liczba kolekcji zadań dla subskrypcji to 200 000. |
| **Przechowywanie historii zadań** |Jest zachowywana Historia zadania, dla maksymalnie dwa miesiące lub maksymalnie 1000 ostatniego wykonania. |
| **Przechowywanie zadań została zakończona i błędnej** |Zadania ukończone i błędnej są przechowywane przez 60 dni. |
| **Timeout** |Brak statyczne (nie można konfigurować) żądania limitu czasu 60 sekund dla akcji HTTP. W przypadku operacji dłużej wykonaj asynchroniczne protokołów HTTP; na przykład zwracać 202 natychmiast, ale nadal działa w tle. |

