---
title: Dopasuj warunki w silniku reguł standardowych dla usługi Azure CDN | Dokumenty firmy Microsoft
description: Dokumentacja referencyjna dotycząca warunków dopasowania w silniku reguł standardowych dla usługi Azure Content Delivery Network (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 425266e2a7ca42bb17ca598ddfc2f2b86591f32e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900191"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Dopasuj warunki w silniku reguł standardowych dla usługi Azure CDN

W [silniku reguł standardowych](cdn-standard-rules-engine.md) dla usługi Azure Content Delivery Network (Azure CDN) reguła składa się z co najmniej jednego dopasowania i akcji. Ten artykuł zawiera szczegółowe opisy warunków dopasowania, których można użyć w silniku reguł standardowych dla usługi Azure CDN.

Pierwsza część reguły to warunek dopasowania lub zestaw warunków dopasowania. W silniku reguł standardowych dla usługi Azure CDN każda reguła może mieć maksymalnie cztery warunki dopasowania. Warunek dopasowania identyfikuje określone typy żądań, dla których wykonywane są zdefiniowane akcje. Jeśli używasz wielu warunków dopasowania, warunki dopasowania są zgrupowane za pomocą logiki AND.

Na przykład można użyć warunku dopasowania do:

- Filtrowanie żądań na podstawie określonego adresu IP, kraju lub regionu.
- Filtrowanie żądań według informacji nagłówka.
- Filtrowanie żądań z urządzeń mobilnych lub urządzeń stacjonarnych.

## <a name="match-conditions"></a>Warunki dopasowania

Następujące warunki dopasowania są dostępne do użycia w silniku reguł standardowych dla usługi Azure CDN. 

### <a name="device-type"></a>Typ urządzenia 

Identyfikuje żądania z urządzenia przenośnego lub urządzenia stacjonarnego.  

#### <a name="required-fields"></a>Wymagane pola

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | Telefon komórkowy, komputery

### <a name="http-version"></a>Wersja HTTP

Identyfikuje żądania na podstawie wersji HTTP żądania.

#### <a name="required-fields"></a>Wymagane pola

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | 2.0, 1.1, 1.0, 0.9, Wszystkie

### <a name="request-cookies"></a>Prośba o pliki cookie

Identyfikuje żądania na podstawie informacji o plikach cookie w żądaniu przychodzącym.

#### <a name="required-fields"></a>Wymagane pola

Nazwa pliku cookie | Operator | Wartość pliku cookie | Transformacja obudowy
------------|----------|--------------|---------------
Ciąg | [Lista operatorów standardowych](#standard-operator-list) | Ciąg, Int | Bez transformacji, na wielkie litery, na małe litery

#### <a name="key-information"></a>Najważniejsze informacje

- Nie można używać wartości symboli wieloznacznych\*(w tym gwiazdek ( )) podczas określania nazwy pliku cookie; musisz użyć dokładnej nazwy pliku cookie.
- Można określić tylko jedną nazwę pliku cookie na wystąpienie tego warunku dopasowania.
- Porównania nazw plików cookie są niewrażliwe na argumenty.
- Aby określić wiele wartości plików cookie, należy użyć pojedynczej spacji między każdą wartością pliku cookie. 
- Wartości plików cookie mogą korzystać z wartości symboli wieloznacznych.
- Jeśli wartość symboli wieloznacznych nie została określona, tylko dokładne dopasowanie spełnia ten warunek dopasowania. Na przykład "Wartość" będzie pasować do "Value", ale nie "Value1". 

### <a name="post-argument"></a>Argument Księguj

Identyfikuje żądania na podstawie argumentów zdefiniowanych dla metody żądania POST, która jest używana w żądaniu. 

#### <a name="required-fields"></a>Wymagane pola

Nazwa argumentu | Operator | Wartość argumentu | Transformacja obudowy
--------------|----------|----------------|---------------
Ciąg | [Lista operatorów standardowych](#standard-operator-list) | Ciąg, Int | Bez transformacji, na wielkie litery, na małe litery

### <a name="query-string"></a>Ciąg zapytania

Identyfikuje żądania, które zawierają określony parametr ciągu kwerendy. Ten parametr jest ustawiony na wartość, która pasuje do określonego wzorca. Parametry ciągu kwerendy (na przykład **parametr=wartość)** w adresie URL żądania określają, czy ten warunek jest spełniony. Ten warunek dopasowania identyfikuje parametr ciągu zapytania według jego nazwy i akceptuje jedną lub więcej wartości dla wartości parametru.

#### <a name="required-fields"></a>Wymagane pola

Operator | Ciąg zapytania | Transformacja przypadku
---------|--------------|---------------
[Lista operatorów standardowych](#standard-operator-list) | Ciąg, Int | Bez transformacji, na wielkie litery, na małe litery

### <a name="remote-address"></a>Adres zdalny

Identyfikuje żądania na podstawie lokalizacji lub adresu IP osoby żądawczej.

#### <a name="required-fields"></a>Wymagane pola

Operator | Obsługiwane wartości
---------|-----------------
Dowolne | Nie dotyczy
Dopasowanie geograficzne | Kod kraju
Dopasowanie ip | Adres IP (oddzielony spacją)
Nie jakieś | Nie dotyczy
Nie dopasowanie geograficzne | Kod kraju
Nie dopasowanie IP | Adres IP (oddzielony spacją)

#### <a name="key-information"></a>Najważniejsze informacje

- Użyj notacji CIDR.
- Aby określić wiele adresów IP i bloków adresów IP, należy użyć pojedynczej spacji między wartościami:
  - **Przykład IPv4:** *1.2.3.4 10.20.30.40* pasuje do wszystkich żądań, które przychodzą z adresu 1.2.3.4 lub 10.20.30.40.
  - **Przykład IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* pasuje do wszelkich żądań 1:2:3:4:5:6:7:8 lub 10:20:30:40:50:60:70:80.
- Składnia bloku adresów IP jest podstawowym adresem IP, po którym następuje ukośnik do przodu i rozmiar prefiksu. Przykład:
  - **Przykład IPv4:** *5.5.5.64/26* pasuje do wszystkich żądań, które przychodzą z adresów 5.5.5.64 do 5.5.5.127.
  - **Przykład IPv6**: *1:2:3:/48* pasuje do wszelkich żądań, które przychodzą z adresów 1:2:3:0:0:0:0 do 1:2:3:ffff:ffff:ffff:ffff:ffff.

### <a name="request-body"></a>Treść żądania

Identyfikuje żądania na podstawie określonego tekstu, który pojawia się w treści żądania.

#### <a name="required-fields"></a>Wymagane pola

Operator | Treść żądania | Transformacja obudowy
---------|--------------|---------------
[Lista operatorów standardowych](#standard-operator-list) | Ciąg, Int | Bez transformacji, na wielkie litery, na małe litery

### <a name="request-header"></a>Nagłówek żądania

Identyfikuje żądania, które używają określonego nagłówka w żądaniu.

#### <a name="required-fields"></a>Wymagane pola

Nazwa nagłówka | Operator | Wartość nagłówka | Transformacja obudowy
------------|----------|--------------|---------------
Ciąg | [Lista operatorów standardowych](#standard-operator-list) | Ciąg, Int | Bez transformacji, na wielkie litery, na małe litery

### <a name="request-method"></a>Metoda żądania

Identyfikuje żądania, które używają określonej metody żądania.

#### <a name="required-fields"></a>Wymagane pola

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | POBIERZ, POST, UMIEŚCIĆ, USUNĄĆ, GŁOWA, OPCJE, ŚLEDZENIE

#### <a name="key-information"></a>Najważniejsze informacje

- Tylko metoda żądania GET może generować zawartość buforowaną w usłudze Azure CDN. Wszystkie inne metody żądania są przesuwają się za pośrednictwem sieci. 

### <a name="request-protocol"></a>Protokół żądania

Identyfikuje żądania, które używają określonego protokołu używanego.

#### <a name="required-fields"></a>Wymagane pola

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | HTTP, HTTPS

### <a name="request-url"></a>Adres URL żądania

Identyfikuje żądania zgodne z określonym adresem URL.

#### <a name="required-fields"></a>Wymagane pola

Operator | Adres URL żądania | Transformacja obudowy
---------|-------------|---------------
[Lista operatorów standardowych](#standard-operator-list) | Ciąg, Int | Bez transformacji, na wielkie litery, na małe litery

#### <a name="key-information"></a>Najważniejsze informacje

- Korzystając z tego warunku reguły, należy dołączyć informacje o protokole. Na przykład: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>Rozszerzenie pliku adresu URL

Identyfikuje żądania, które zawierają określone rozszerzenie pliku w nazwie pliku w żądającym adresie URL.

#### <a name="required-fields"></a>Wymagane pola

Operator | Wewnętrzny | Transformacja obudowy
---------|-----------|---------------
[Lista operatorów standardowych](#standard-operator-list) | Ciąg, Int | Bez transformacji, na wielkie litery, na małe litery

#### <a name="key-information"></a>Najważniejsze informacje

- W przypadku rozszerzenia nie należy uwzględniać okresu wiodącego; na przykład użyj *html* zamiast *.html*.

### <a name="url-file-name"></a>Nazwa pliku adresu URL

Identyfikuje żądania, które zawierają określoną nazwę pliku w żądającym adresie URL.

#### <a name="required-fields"></a>Wymagane pola

Operator | Nazwa pliku | Transformacja obudowy
---------|-----------|---------------
[Lista operatorów standardowych](#standard-operator-list) | Ciąg, Int | Bez transformacji, na wielkie litery, na małe litery

#### <a name="key-information"></a>Najważniejsze informacje

- Aby określić wiele nazw plików, należy oddzielić każdą nazwę pliku pojedynczą spacją. 

### <a name="url-path"></a>Ścieżka adresu URL

Identyfikuje żądania, które zawierają określoną ścieżkę w żądającym adresie URL.

#### <a name="required-fields"></a>Wymagane pola

Operator | Wartość | Transformacja przypadku
---------|-------|---------------
[Lista operatorów standardowych](#standard-operator-list) | Ciąg, Int | Bez transformacji, na wielkie litery, na małe litery

#### <a name="key-information"></a>Najważniejsze informacje

- Wartość nazwy pliku może korzystać z wartości symboli wieloznacznych. Na przykład każdy wzorzec nazwy pliku może składać się z jednej lub więcej gwiazdek (*), gdzie każda gwiazdka pasuje do sekwencji jednego lub więcej znaków.

## <a name="reference-for-rules-engine-match-conditions"></a>Informacje na kątem reguł warunków dopasowania silnika

### <a name="standard-operator-list"></a>Lista operatorów standardowych

W przypadku reguł, które akceptują wartości z listy operatorów standardowych, prawidłowe są następujące operatory:

- Dowolne
- Równa się 
- Contains 
- Zaczyna się od 
- Kończy się na 
- Mniejsze niż
- Mniej niż lub równa
- Większe niż
- Większa lub równa się
- Nie ma żadnych
- Nie zawiera
- Nie zaczyna się od 
- Nie kończy się na 
- Nie mniej niż
- Nie mniej niż lub równa
- Nie większa niż
- Nie większa lub równa się

Dla operatorów liczbowych, takich jak *Mniej niż* i Większa *lub równa*się , używane porównanie opiera się na długości. W takim przypadku wartość w warunku dopasowania powinna być całkowitej, która jest równa długości, którą chcesz porównać. 

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł standardowych](cdn-standard-rules-engine-reference.md)
- [Akcje w silniku reguł standardowych](cdn-standard-rules-engine-actions.md)
- [Wymuszanie protokołu HTTPS za pomocą aparatu reguł standardowych](cdn-standard-rules-engine.md)
