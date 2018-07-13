```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|maxPollingInterval|60000|Maksymalny interwał w milisekundach między sondowaniami kolejki.| 
|visibilityTimeout|0|Odstęp czasu między kolejnymi próbami podczas przetwarzania komunikatu nie powiedzie się.| 
|batchSize|16|Liczba komunikatów w kolejce, które środowisko uruchomieniowe usługi Functions pobiera jednocześnie przetwarzane równolegle. Gdy liczba przetwarzanych wyświetlona w dół `newBatchThreshold`, środowisko uruchomieniowe inna partia pobiera i uruchamia przetwarzanie tych wiadomości. Dlatego jest maksymalna liczba współbieżnych komunikatów przetwarzanych dla każdej funkcji `batchSize` oraz `newBatchThreshold`. Ten limit dotyczy oddzielnie poszczególnych funkcji wyzwalanej przez kolejkę. <br><br>Jeśli chcesz uniknąć wykonywania równoległego dla wiadomości otrzymanych w jednej kolejki można ustawić `batchSize` 1. Jednak to ustawienie pozwala wyeliminować współbieżności tylko tak długo, jak aplikacja funkcji zostanie uruchomiona na jednej maszynie wirtualnej (VM). Jeśli aplikacja funkcji skalowania do wielu maszyn wirtualnych, każda maszyna wirtualna może uruchomić jedno wystąpienie każdej funkcji wyzwalanej przez kolejkę.<br><br>Maksymalna `batchSize` wynosi 32. | 
|maxDequeueCount|5|Liczba prób przetwarzania komunikatu przed jego przeniesieniem do skażone kolejki.| 
|newBatchThreshold|batchSize/2|Zawsze, gdy liczba komunikatów przetwarzanych jednocześnie uzyskuje na ten numer, środowisko uruchomieniowe pobiera inna partia.| 



