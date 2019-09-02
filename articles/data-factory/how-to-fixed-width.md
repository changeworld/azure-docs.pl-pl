---
title: Przetwarzanie plików tekstowych o stałej długości z mapowaniem przepływów danych w Azure Data Factory
description: Dowiedz się, jak przetwarzać pliki tekstowe o stałej długości w Azure Data Factory przy użyciu mapowania przepływów danych.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210678"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>Przetwarzanie plików tekstowych o stałej długości przy użyciu Data Factory mapowania przepływów danych

Data Factory mapowanie przepływów danych obsługuje dane transformacji z plików tekstowych o stałej szerokości. Zdefiniujesz zestaw danych dla pliku tekstowego bez ogranicznika, a następnie ustawisz podział podciągów na podstawie pozycji porządkowej.

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Przejdź do **+ Nowy potok** , aby rozpocząć nowy potok

2. Dodawanie działania przepływu danych, które będzie używane do przetwarzania plików o stałej szerokości

  ![Potok o stałej szerokości](media/data-flow/fwpipe.png)

3. W działaniu przepływu danych wybierz kolejno pozycje nowy mapowanie przepływu danych.

4. Dodaj transformację źródłową, kolumnę pochodną, wybór i transformację ujścia

  ![Przepływ danych o stałej szerokości](media/data-flow/fw2.png)

5. Skonfiguruj transformację źródłową, aby korzystała z nowego zestawu danych, który będzie tekstem rozdzielanym typem

6. Nie ustawiaj ogranicznika kolumny ani nagłówków

Będziemy po prostu ustawić punkty początkowe i długości dla tej zawartości pliku:

```
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
```

7. Na karcie projekcja transformacji źródłowej powinna zostać wyświetlona jedna kolumna ciągów o nazwie "Column_1".

8. Teraz w kolumnie pochodnej Utwórz nową kolumnę

9. Podajemy proste nazwy kolumn, takie jak Kol1

10. Następnie w Konstruktorze wyrażeń wpisz:

  ```substring(Column_1,1,4)```

  ![kolumna pochodna](media/data-flow/fwderivedcol1.png)

10. Powtórz tę czynność dla wszystkich kolumn, które należy przeanalizować

12. Kliknij kartę Inspekcja, aby wyświetlić nowe kolumny, które zostaną wygenerowane.

  ![skontrol](media/data-flow/fwinspect.png)

13. Użyj opcji Przekształć, aby usunąć wszystkie kolumny, które nie są potrzebne do przekształcenia

  ![Wybierz transformację](media/data-flow/fwselect.png)

14. Na koniec użyj polecenia ujścia, aby wyprowadzić dane do folderu:

  ![zbiornik o stałej szerokości](media/data-flow/fwsink.png)

  Oto dane wyjściowe, które będą wyglądać następująco:

  ![Wyjście o stałej szerokości](media/data-flow/fxdoutput.png)

  Dane o stałej szerokości są teraz podzielone na cztery znaki i przypisane do Kol1, Col2, Col3, Col4,... Na podstawie powyższego przykładu dzielę dane na 4 kolumny

## <a name="next-steps"></a>Następne kroki

* Kompiluj resztę logiki przepływu danych przy użyciu [transformacji](concepts-data-flow-overview.md) przepływu danych mapowania
