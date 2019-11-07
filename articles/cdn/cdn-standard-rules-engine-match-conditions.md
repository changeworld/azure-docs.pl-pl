---
title: Azure CDN ze standardowych reguł firmy Microsoft dotyczących zgodności aparatu | Microsoft Docs
description: Dokumentacja referencyjna dotycząca platformy Azure Content Delivery Network z poziomu standardowych reguł firmy Microsoft dotyczącej aparatu.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: a72452d37b152a9463a5aee0e199fd42ea852236
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615964"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-match-conditions"></a>Azure CDN z warunków dopasowania aparatu standardowej reguły firmy Microsoft

W tym artykule przedstawiono szczegółowe opisy warunków dopasowania dostępne dla usługi Azure Content Delivery Network (CDN) z [aparatu standardowych reguł](cdn-standard-rules-engine.md)firmy Microsoft.

Pierwsza część reguły jest zestawem warunków dopasowywania. Każda reguła może mieć maksymalnie 4 warunki dopasowania. Warunek dopasowania identyfikuje określone typy żądań, dla których zostaną wykonane akcje zdefiniowane w regule. Jeśli używasz wielu warunków dopasowywania, zostaną zgrupowane razem przy użyciu i logiki.

Można na przykład użyć warunku dopasowywania do:

- Filtruj żądania wygenerowane na podstawie określonego adresu IP lub kraju/regionu.
- Filtruj żądania według informacji nagłówka.
- Filtrowanie żądań z urządzeń przenośnych lub komputerów stacjonarnych.

## <a name="match-conditions"></a>Warunki dopasowania

Poniższe warunki dopasowania są dostępne do użycia. 

### <a name="device-type"></a>Typ urządzenia 

Warunek dopasowania typu urządzenia identyfikuje żądania wysyłane z urządzenia przenośnego lub stacjonarnego na podstawie jego właściwości.  

**Pola wymagane**

Operator | Obsługiwana wartość
---------|----------------
Równa się, nie równa się | Urządzenia przenośne, komputery stacjonarne


### <a name="http-version"></a>Wersja protokołu HTTP

Warunek dopasowania wersji protokołu HTTP identyfikuje żądania na podstawie wersji protokołu HTTP, do której dociera żądanie.

**Pola wymagane**

Operator | Obsługiwana wartość
---------|----------------
Równa się, nie równa się | 2,0, 1,1, 1,0, 0,9, wszystkie


### <a name="request-cookies"></a>Pliki cookie żądania

Warunek dopasowania plików cookie żądania identyfikuje żądania na podstawie informacji o pliku cookie w żądaniu przychodzącym.

**Pola wymagane**

Nazwa pliku cookie | Operator | Wartość cookie | Przekształcanie wielkości liter
------------|----------|--------------|---------------
Ciąg | [Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

Informacje o kluczu
- Wartości symboli wieloznacznych, w tym gwiazdki (*), nie są obsługiwane podczas określania nazwy pliku cookie, tylko dokładne dopasowania nazw plików cookie kwalifikują się do porównania.
- Dla każdego wystąpienia tego warunku dopasowywania można określić tylko jedną nazwę pliku cookie.
- Porównania nazw plików cookie nie uwzględniają wielkości liter.
- Określ wiele wartości plików cookie, ograniczając każdą z nich pojedynczym miejscem. 
- Wartości plików cookie mogą korzystać z symboli wieloznacznych.
- Jeśli nie określono wartości symbolu wieloznacznego, tylko dokładne dopasowanie będzie spełniało ten warunek dopasowania. Na przykład określenie "value" będzie pasować do wartości "value", ale nie "wartość1". 

### <a name="post-argument"></a>Opublikuj argument

**Pola wymagane**

Nazwa argumentu | Operator | Wartość argumentu | Przekształcanie wielkości liter
--------------|----------|----------------|---------------
Ciąg | [Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

### <a name="query-string"></a>Ciąg kwerendy

Warunki dopasowania ciągu zapytania identyfikują żądania zawierające określony parametr ciągu zapytania. Ten parametr jest ustawiony na wartość zgodną z określonym wzorcem. Parametry ciągu zapytania (na przykład parametr = wartość) w adresie URL żądania określają, czy ten warunek jest spełniony. Ten warunek dopasowania identyfikuje parametr ciągu zapytania według jego nazwy i akceptuje co najmniej jedną wartość wartości parametru.

**Pola wymagane**

Operator | Ciąg kwerendy | Przekształcanie wielkości liter
---------|--------------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

### <a name="remote-address"></a>Adres zdalny

Warunek zgodności adresów zdalnych zidentyfikuje żądania na podstawie lokalizacji lub adresu IP osoby żądającej.

**Pola wymagane**

Operator | Obsługiwane wartości
---------|-----------------
Dowolne | Nie dotyczy
Dopasowanie geograficzne | Kody krajów
Dopasowanie adresu IP | Adresy IP (rozdzielone miejsce)
Nie wszystkie | Nie dotyczy
Brak dopasowania geograficznego | Kody krajów
Niezgodność adresów IP | Adresy IP (rozdzielone miejsce)

Informacje o kluczu:

- Użyj notacji CIDR.
- Określ wiele adresów IP i/lub bloków adresów IP, ograniczając każdą z nich pojedynczym miejscem. Na przykład:
  - **Przykład IPv4**: 1.2.3.4 10.20.30.40 dopasowuje wszystkie żądania, które dotarły do adresu 1.2.3.4 lub 10.20.30.40.
  - **Przykład IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 dopasowuje wszystkie żądania, które nadeszły z adresu 1:2:3:4:5:6:7:8 lub 10:20:30:40:50:60:70:80.
- Składnia bloku adresów IP to podstawowy adres IP, po którym następuje ukośnik i rozmiar prefiksu. Na przykład:
  - **Przykład IPv4**: 5.5.5.64/26 dopasowuje wszystkie żądania odbierane z adresów 5.5.5.64 przez 5.5.5.127.
  - **Przykład IPv6**: 1:2:3:/48 dopasowuje wszystkie żądania odbierane z adresów 1:2:3:0:0:0:0:0 do 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF.

### <a name="request-body"></a>Treść żądania

**Pola wymagane**

Operator | Treść żądania | Przekształcanie wielkości liter
---------|--------------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

### <a name="request-header"></a>Nagłówek żądania

**Pola wymagane**
Nazwa nagłówka | Operator | Wartość nagłówka | Przekształcanie wielkości liter
------------|----------|--------------|---------------
Ciąg | [Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

### <a name="request-method"></a>Metoda żądania

**Pola wymagane**

Operator | Obsługiwana wartość
---------|----------------
Równa się, nie równa się | GET, POST, PUT, DELETE, GŁOWY, OPTIONS, TRACE

Informacje o kluczu:

- Tylko Metoda GET Request może generować zawartość z pamięci podręcznej w usłudze CDN. Wszystkie pozostałe metody żądań są przekazywane za pomocą sieci. 

### <a name="request-protocol"></a>Protokół żądania

**Pola wymagane**

Operator | Obsługiwana wartość
---------|----------------
Równa się, nie równa się | HTTP, HTTPS

### <a name="request-url"></a>Adres URL żądania

**Pola wymagane**

Operator | Adres URL żądania | Przekształcanie wielkości liter
---------|-------------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

Informacje o kluczu:

- Po wprowadzeniu adresu URL żądania upewnij się, że dołączasz informacje o protokole. Na przykład "https://www. [twojadomena]. com "

### <a name="url-file-extension"></a>Rozszerzenie pliku adresu URL

**Pola wymagane**

Operator | Wewnętrzny | Przekształcanie wielkości liter
---------|-----------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

Informacje o kluczu:

- W przypadku rozszerzenia nie należy umieszczać wiodących kropek; na przykład użyj HTML zamiast. html.

### <a name="url-file-name"></a>Nazwa pliku adresu URL

**Pola wymagane**

Operator | Nazwa pliku | Przekształcanie wielkości liter
---------|-----------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

Informacje o kluczu:

- Aby określić wiele nazw plików, oddziel poszczególne nazwy plików pojedynczą spacją. 

### <a name="url-path"></a>Ścieżka adresu URL

**Pola wymagane**

Operator | Wartość | Przekształcanie wielkości liter
---------|-------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

Informacje o kluczu:

- Wartość nazwy pliku może korzystać z symboli wieloznacznych. Na przykład każdy wzorzec nazwy pliku może składać się z co najmniej jednej gwiazdki (*), gdzie każda Gwiazdka dopasowuje sekwencję składającą się z co najmniej jednego znaku.

## <a name="reference-for-rules-engine-match-conditions"></a>Dokumentacja warunków dopasowania aparatu reguł

### <a name="standard-operator-list"></a>Standardowa lista operatorów

W przypadku reguł zawierających listę standardowych operatorów następujące operatory są prawidłowe:

- Dowolne
- Równa się 
- Contains 
- Zaczyna się od 
- Kończący się na 
- Mniej niż
- Mniejsze niż lub równe
- Większe niż
- Większe niż lub równe
- Nie wszystkie
- Nie zawiera
- Nie zaczyna się od 
- Nie kończący się na 
- Nie mniejsze niż
- Nie mniejsze niż lub równe
- Nie większe niż
- Nie większe niż lub równe

W przypadku operatorów liczbowych, takich jak "mniejsze niż" lub "większe niż lub równe", użyte porównanie będzie oparte na długości. W takim przypadku wartość w warunku dopasowywania powinna być liczbą całkowitą równą długości, którą chcesz porównać. 

---

[Powrót do początku](#match-conditions)

</br>

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Content Delivery Network](cdn-overview.md)
- [Dokumentacja aparatu reguł](cdn-standard-rules-engine-reference.md)
- [Akcje aparatu reguł](cdn-standard-rules-engine-actions.md)
- [Wymuszanie protokołu HTTPS przy użyciu standardowego aparatu reguł](cdn-standard-rules-engine.md)
