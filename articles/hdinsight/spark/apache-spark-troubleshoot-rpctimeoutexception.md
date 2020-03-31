---
title: RpcTimeoutException for Apache Spark oszczędności - Azure HDInsight
description: Widzisz 502 błędy podczas przetwarzania dużych zestawów danych przy użyciu serwera oszczędności Apache Spark
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894278"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenariusz: RpcTimeoutException dla serwera oszczędności Apache Spark w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników Platformy Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Aplikacja Spark kończy `org.apache.spark.rpc.RpcTimeoutException` się niepowodzeniem `Futures timed out`z wyjątkiem i komunikatem: , jak w poniższym przykładzie:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`i `overhead limit exceeded` błędy mogą również `sparkthriftdriver.log` pojawić się w następującym przykładzie:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Przyczyna

Błędy te są spowodowane brakiem zasobów pamięci podczas przetwarzania danych. Jeśli rozpocznie się proces wyrzucania elementów bezużytecznych w języku Java, może to prowadzić do zawieszenia aplikacji Platformy Spark. Kwerendy zaczną się limit czasu i zatrzymać przetwarzanie. Błąd `Futures timed out` wskazuje klaster pod silnym naprężeń.

## <a name="resolution"></a>Rozwiązanie

Zwiększ rozmiar klastra, dodając więcej węzłów procesu roboczego lub zwiększając pojemność pamięci istniejących węzłów klastra. Można również dostosować potok danych, aby zmniejszyć ilość przetwarzanych danych jednocześnie.

Steruje `spark.network.timeout` limitem czasu dla wszystkich połączeń sieciowych. Zwiększenie limitu czasu sieci może dać więcej czasu na zakończenie niektórych krytycznych operacji, ale nie rozwiąże to całkowicie problemu.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
