```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|maxBatchSize|64|Maksymalna otrzymana liczba zdarzeń na pętli odbioru.|
|prefetchCount|Nie dotyczy|Domyślnie PrefetchCount używanej przez podstawowej klasy EventProcessorHost.| 
|batchCheckpointFrequency|1|Liczba zdarzeń wsadów przetwarzana przed utworzeniem punktu kontrolnego EventHub kursora.| 
