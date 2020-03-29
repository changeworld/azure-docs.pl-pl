---
title: azcopy env | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla polecenia env azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033761"
---
# <a name="azcopy-env"></a>azcopy env

Pokazuje zmienne środowiskowe, które można skonfigurować zachowanie AzCopy.Shows the environment variables that can configure AzCopy's behavior.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Jeśli ustawisz zmienną środowiskową przy użyciu wiersza polecenia, ta zmienna będzie czytelna w historii wiersza polecenia. Należy wziąć pod uwagę wyczyszczenie zmiennych, które zawierają poświadczenia z historii wiersza polecenia. Aby zachować zmienne z pojawiających się w historii, można użyć skryptu, aby monitować użytkownika o ich poświadczenia i ustawić zmienną środowiskową.

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](storage-use-azcopy-blobs.md)
- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h, --pomoc|Pokazuje zawartość pomocy dla polecenia env. |
|--show-wrażliwe|Pokazuje zmienne środowiska poufne/tajne.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mbps uint32|Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.|
|Ciąg typu --output|Format danych wyjściowych polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "tekst".|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
